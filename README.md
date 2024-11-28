# News-Portal-system
this is a project based on creating a java program with using swing for a News portal system
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.util.ArrayList;
import java.util.List;

// User class to represent a user
class User {
    private String username;
    private String password;
    private boolean isAdmin;

    public User(String username, String password, boolean isAdmin) {
        this.username = username;
        this.password = password;
        this.isAdmin = isAdmin;
    }

    public String getUsername() {
        return username;
    }

    public String getPassword() {
        return password;
    }

    public boolean isAdmin() {
        return isAdmin;
    }
}

// Article class to represent a news article
class Article {
    private String title;
    private String content;
    private String category;

    public Article(String title, String content, String category) {
        this.title = title;
        this.content = content;
        this.category = category;
    }

    public String getTitle() {
        return title;
    }

    public String getContent() {
        return content;
    }

    public String getCategory() {
        return category;
    }
}

// NewsPortal class to manage users and articles
class NewsPortal {
    private List<User> users;
    private List<Article> articles;

    public NewsPortal() {
        users = new ArrayList<>();
        articles = new ArrayList<>();
        loadPredefinedArticles();
    }

    private void loadPredefinedArticles() {
        articles.add(new Article("Tech Trends 2024", "Exploring the top technology trends for 2024.", "Technology"));
        articles.add(new Article("Healthy Living Tips", "Discover tips to maintain a healthy lifestyle.", "Health"));
        articles.add(new Article("Global Market Updates", "Latest updates on the global stock market.", "Finance"));
    }

    public void registerUser(String username, String password) {
        users.add(new User(username, password, false));
    }

    public void registerAdmin(String username, String password) {
        users.add(new User(username, password, true));
    }

    public User loginUser(String username, String password) {
        for (User user : users) {
            if (user.getUsername().equals(username) && user.getPassword().equals(password)) {
                return user;
            }
        }
        return null;
    }

    public void addArticle(String title, String content, String category) {
        articles.add(new Article(title, content, category));
    }

    public List<Article> getArticles() {
        return articles;
    }

    public List<Article> getArticlesByCategory(String category) {
        List<Article> filteredArticles = new ArrayList<>();
        for (Article article : articles) {
            if (article.getCategory().equalsIgnoreCase(category)) {
                filteredArticles.add(article);
            }
        }
        return filteredArticles;
    }
}

// Main GUI Class
public class NewsPortalGUI {
    private NewsPortal newsPortal;
    private JFrame frame;
    private User loggedInUser;

    public NewsPortalGUI() {
        newsPortal = new NewsPortal();
        newsPortal.registerAdmin("admin", "admin123");
        newsPortal.registerUser("user", "user123");

        initMainFrame();
    }

    private void initMainFrame() {
        frame = new JFrame("News Portal");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(400, 300);

        JPanel panel = new JPanel();
        panel.setLayout(new GridLayout(4, 1, 10, 10));

        JButton btnRegisterUser = new JButton("Register User");
        btnRegisterUser.addActionListener(e -> showRegisterScreen(false));

        JButton btnRegisterAdmin = new JButton("Register Admin");
        btnRegisterAdmin.addActionListener(e -> showRegisterScreen(true));

        JButton btnLogin = new JButton("Login");
        btnLogin.addActionListener(e -> showLoginScreen());

        JButton btnExit = new JButton("Exit");
        btnExit.addActionListener(e -> System.exit(0));

        panel.add(btnRegisterUser);
        panel.add(btnRegisterAdmin);
        panel.add(btnLogin);
        panel.add(btnExit);

        frame.add(panel);
        frame.setVisible(true);
    }

    private void showRegisterScreen(boolean isAdmin) {
        JFrame registerFrame = new JFrame(isAdmin ? "Register Admin" : "Register User");
        registerFrame.setSize(300, 200);
        registerFrame.setLayout(new GridLayout(3, 2, 5, 5));

        JTextField usernameField = new JTextField();
        JPasswordField passwordField = new JPasswordField();
        JButton registerButton = new JButton("Register");

        registerButton.addActionListener(e -> {
            String username = usernameField.getText();
            String password = new String(passwordField.getPassword());
            if (isAdmin) {
                newsPortal.registerAdmin(username, password);
            } else {
                newsPortal.registerUser(username, password);
            }
            JOptionPane.showMessageDialog(registerFrame, (isAdmin ? "Admin" : "User") + " registered successfully!");
            registerFrame.dispose();
        });

        registerFrame.add(new JLabel("Username:"));
        registerFrame.add(usernameField);
        registerFrame.add(new JLabel("Password:"));
        registerFrame.add(passwordField);
        registerFrame.add(new JLabel());
        registerFrame.add(registerButton);

        registerFrame.setVisible(true);
    }

    private void showLoginScreen() {
        JFrame loginFrame = new JFrame("Login");
        loginFrame.setSize(300, 200);
        loginFrame.setLayout(new GridLayout(3, 2, 5, 5));

        JTextField usernameField = new JTextField();
        JPasswordField passwordField = new JPasswordField();
        JButton loginButton = new JButton("Login");

        loginButton.addActionListener(e -> {
            String username = usernameField.getText();
            String password = new String(passwordField.getPassword());
            loggedInUser = newsPortal.loginUser(username, password);
            if (loggedInUser != null) {
                JOptionPane.showMessageDialog(loginFrame, "Login successful!");
                loginFrame.dispose();
                showUserMenu();
            } else {
                JOptionPane.showMessageDialog(loginFrame, "Invalid credentials.");
            }
        });

        loginFrame.add(new JLabel("Username:"));
        loginFrame.add(usernameField);
        loginFrame.add(new JLabel("Password:"));
        loginFrame.add(passwordField);
        loginFrame.add(new JLabel());
        loginFrame.add(loginButton);

        loginFrame.setVisible(true);
    }

    private void showUserMenu() {
        JFrame userFrame = new JFrame("User Menu");
        userFrame.setSize(400, 300);
        userFrame.setLayout(new GridLayout(3, 1, 10, 10));

        JButton btnViewArticles = new JButton("View Articles");
        btnViewArticles.addActionListener(e -> showArticles(false));

        JButton btnAdminPanel = new JButton("Admin Panel");
        btnAdminPanel.setEnabled(loggedInUser.isAdmin());
        btnAdminPanel.addActionListener(e -> showAdminPanel());

        JButton btnLogout = new JButton("Logout");
        btnLogout.addActionListener(e -> {
            loggedInUser = null;
            userFrame.dispose();
            JOptionPane.showMessageDialog(frame, "Logged out successfully!");
        });

        userFrame.add(btnViewArticles);
        if (loggedInUser.isAdmin()) {
            userFrame.add(btnAdminPanel);
        }
        userFrame.add(btnLogout);

        userFrame.setVisible(true);
    }

    private void showArticles(boolean filterByCategory) {
        JFrame articleFrame = new JFrame("Articles");
        articleFrame.setSize(400, 400);
        JTextArea textArea = new JTextArea();
        textArea.setEditable(false);

        List<Article> articles;
        if (filterByCategory) {
            String category = JOptionPane.showInputDialog("Enter category:");
            articles = newsPortal.getArticlesByCategory(category);
        } else {
            articles = newsPortal.getArticles();
        }

        for (Article article : articles) {
            textArea.append("Title: " + article.getTitle() + "\n");
            textArea.append("Content: " + article.getContent() + "\n");
            textArea.append("Category: " + article.getCategory() + "\n\n");
        }

        articleFrame.add(new JScrollPane(textArea));
        articleFrame.setVisible(true);
    }

    private void showAdminPanel() {
        JFrame adminFrame = new JFrame("Admin Panel");
        adminFrame.setSize(400, 300);
        adminFrame.setLayout(new GridLayout(3, 1, 10, 10));

        JButton btnAddArticle = new JButton("Add Article");
        btnAddArticle.addActionListener(e -> {
            String title = JOptionPane.showInputDialog("Enter title:");
            String content = JOptionPane.showInputDialog("Enter content:");
            String category = JOptionPane.showInputDialog("Enter category:");
            newsPortal.addArticle(title, content, category);
            JOptionPane.showMessageDialog(adminFrame, "Article added successfully!");
        });

        JButton btnViewArticles = new JButton("View Articles");
        btnViewArticles.addActionListener(e -> showArticles(false));

        JButton btnLogout = new JButton("Logout");
        btnLogout.addActionListener(e -> adminFrame.dispose());

        adminFrame.add(btnAddArticle);
        adminFrame.add(btnViewArticles);
        adminFrame.add(btnLogout);

        adminFrame.setVisible(true);
    }

    public static void main(String[] args) {
        new NewsPortalGUI();
    }
}

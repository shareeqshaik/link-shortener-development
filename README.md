# link-shortener-development
To accurately outline the scope of work required for a project, it is crucial to first identify itsobjectives. Pinpointing what the project hopes to accomplish will assist in determining itsinclusions and limitations.
Here's a basic implementation of a URL shortener in Java:
Here's a simple implementation of a Link Shortener in Java:


LinkShortener.java

import java.util.HashMap;
import java.util.Map;
import java.util.Random;

public class LinkShortener {
    private Map<String, String> longToShort;
    private Map<String, String> shortToLong;
    private Random random;
    private String domain;

    public LinkShortener(String domain) {
        this.domain = domain;
        longToShort = new HashMap<>();
        shortToLong = new HashMap<>();
        random = new Random();
    }

    // Generate short URL
    public String shorten(String longURL) {
        if (longToShort.containsKey(longURL)) {
            return longToShort.get(longURL);
        }

        String shortURL = generateShortURL();
        while (shortToLong.containsKey(shortURL)) {
            shortURL = generateShortURL();
        }

        longToShort.put(longURL, shortURL);
        shortToLong.put(shortURL, longURL);
        return domain + "/" + shortURL;
    }

    // Expand short URL
    public String expand(String shortURL) {
        if (!shortURL.startsWith(domain + "/")) {
            return "Invalid short URL";
        }

        String shortCode = shortURL.substring(domain.length() + 1);
        if (!shortToLong.containsKey(shortCode)) {
            return "Short URL not found";
        }

        return shortToLong.get(shortCode);
    }

    // Generate random short URL code
    private String generateShortURL() {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < 6; i++) {
            sb.append(getRandomChar());
        }
        return sb.toString();
    }

    // Get random character (a-z, A-Z, 0-9)
    private char getRandomChar() {
        int rand = random.nextInt(62);
        if (rand < 10) {
            return (char) ('0' + rand);
        } else if (rand < 36) {
            return (char) ('A' + rand - 10);
        } else {
            return (char) ('a' + rand - 36);
        }
    }
}



CLI Interface:


LinkShortenerCLI.java

import java.util.Scanner;

public class LinkShortenerCLI {
    public static void main(String[] args) {
        LinkShortener shortener = new LinkShortener("(link unavailable)");

        Scanner scanner = new Scanner(System.in);
        while (true) {
            System.out.println("1. Shorten URL");
            System.out.println("2. Expand URL");
            System.out.println("3. Exit");
            System.out.print("Choose an option: ");
            int option = scanner.nextInt();
            scanner.nextLine(); // Consume newline left-over

            switch (option) {
                case 1:
                    System.out.print("Enter long URL: ");
                    String longURL = scanner.nextLine();
                    String shortURL = shortener.shorten(longURL);
                    System.out.println("Short URL: " + shortURL);
                    break;
                case 2:
                    System.out.print("Enter short URL: ");
                    String shortURLInput = scanner.nextLine();
                    String longURLExpanded = shortener.expand(shortURLInput);
                    System.out.println("Long URL: " + longURLExpanded);
                    break;
                case 3:
                    System.exit(0);
                    break;
                default:
                    System.out.println("Invalid option");
            }
        }
    }
}



Web Interface (using Spring Boot):


LinkShortenerWeb.java

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

@SpringBootApplication
@RestController
public class LinkShortenerWeb {
    private LinkShortener shortener;

    public LinkShortenerWeb() {
        shortener = new LinkShortener("(link unavailable)");
    }

    @GetMapping("/shorten")
    public String shorten(@RequestBody String longURL) {
        return shortener.shorten(longURL);
    }

    @PostMapping("/expand")
    public String expand(@RequestBody String shortURL) {
        return shortener.expand(shortURL);
    }

    public static void main(String[] args) {
        SpringApplication.run(LinkShortenerWeb.class, args);
    }
}






  

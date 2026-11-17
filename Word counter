import java.io.*;
import java.nio.file.*;
import java.nio.charset.StandardCharsets;
import java.util.*;
import java.util.zip.*;
import javax.xml.parsers.*;
import org.w3c.dom.*;

public class Main {

    // MODULE 1: INPUT MODULE
    static String inputModule(Scanner sc) throws Exception {
        System.out.println("========== WORD COUNTER ==========");
        System.out.println("1. Read TXT File\n2. Read DOCX File");
        System.out.println("3. Analyze PDF Text\n4. Enter Text Directly");
        System.out.print("Enter your choice: ");

        int choice = Integer.parseInt(sc.nextLine().trim());

        if (choice == 1 || choice == 2) {
            System.out.print("Enter file path: ");
            String path = sc.nextLine().trim();

            if (path.length() >= 2 && path.startsWith("\"")
                    && path.endsWith("\"")) {
                path = path.substring(1, path.length() - 1);
            }
            return fileReadingModule(path, choice);
        }

        if (choice == 3) {
            System.out.println("\nCopy text from your PDF.");
            System.out.println("Paste it below.");
            System.out.println("Type END on a new line to finish:");

            StringBuilder text = new StringBuilder();
            while (sc.hasNextLine()) {
                String line = sc.nextLine();
                if (line.equals("END")) {
                    break;
                }
                text.append(line).append(" ");
            }
            return text.toString();
        }

        if (choice == 4) {
            System.out.println("Enter your text:");
            return sc.nextLine();
        }

        throw new IllegalArgumentException(
                "Invalid choice. Select 1, 2, 3 or 4.");
    }

    // MODULE 2: FILE READING MODULE
    static String fileReadingModule(String path, int choice)
            throws Exception {
        Path file = Paths.get(path).toAbsolutePath();
        System.out.println("Checking file: " + file);

        if (!Files.exists(file)) {
            throw new FileNotFoundException("File not found: " + file);
        }
        if (!Files.isRegularFile(file)) {
            throw new IOException("The selected path is not a file.");
        }
        if (!Files.isReadable(file)) {
            throw new IOException("The selected file cannot be read.");
        }

        if (choice == 1) {
            if (!path.toLowerCase(Locale.ROOT).endsWith(".txt")) {
                throw new IllegalArgumentException(
                        "Please select a .txt file.");
            }

            StringBuilder text = new StringBuilder();
            try (BufferedReader br = Files.newBufferedReader(
                    file, StandardCharsets.UTF_8)) {
                String line;
                while ((line = br.readLine()) != null) {
                    text.append(line).append(" ");
                }
            }

            System.out.println("TXT file loaded successfully!");
            return text.toString();
        }

        if (choice == 2) {
            if (!path.toLowerCase(Locale.ROOT).endsWith(".docx")) {
                throw new IllegalArgumentException(
                        "Please select a .docx file.");
            }

            StringBuilder text = new StringBuilder();
            try (ZipFile zip = new ZipFile(file.toFile())) {
                ZipEntry entry = zip.getEntry("word/document.xml");
                if (entry == null) {
                    throw new IOException(
                            "Invalid DOCX file: document.xml missing.");
                }

                DocumentBuilderFactory f = DocumentBuilderFactory.newInstance();
                f.setNamespaceAware(true);
                f.setFeature(
                        "http://apache.org/xml/features/disallow-doctype-decl",
                        true);
                f.setFeature(
                        "http://xml.org/sax/features/external-general-entities",
                        false);
                f.setFeature(
                        "http://xml.org/sax/features/external-parameter-entities",
                        false);
                f.setXIncludeAware(false);
                f.setExpandEntityReferences(false);

                try (InputStream in = zip.getInputStream(entry)) {
                    Document doc = f.newDocumentBuilder().parse(in);
                    String ns = "http://schemas.openxmlformats.org/"
                            + "wordprocessingml/2006/main";
                    NodeList paragraphs =
                            doc.getElementsByTagNameNS(ns, "p");

                    for (int i = 0; i < paragraphs.getLength(); i++) {
                        NodeList words = ((Element) paragraphs.item(i))
                                .getElementsByTagNameNS(ns, "t");
                        for (int j = 0; j < words.getLength(); j++) {
                            text.append(words.item(j).getTextContent());
                        }
                        text.append(" ");
                    }
                }
            }

            System.out.println("DOCX file loaded successfully!");
            return text.toString();
        }

        throw new IllegalArgumentException("Unsupported file selection.");
    }

    // MODULE 3: PREPROCESSING MODULE
    static String preprocessingModule(String text) {
        return text.toLowerCase(Locale.ROOT)
                .replaceAll("[^a-z0-9\\s]", " ").trim();
    }

    // MODULE 4: WORD EXTRACTION MODULE
    static String[] wordExtractionModule(String text) {
        return text.isEmpty() ? new String[0] : text.split("\\s+");
    }

    // MODULE 5: FREQUENCY COUNTER
    static HashMap<String, Integer> frequencyCounter(String[] words) {
        HashMap<String, Integer> frequency = new HashMap<>();
        for (String word : words) {
            frequency.put(word, frequency.getOrDefault(word, 0) + 1);
        }
        return frequency;
    }

    // MODULE 6: SORTING MODULE
    static TreeMap<String, Integer> sortingModule(
            HashMap<String, Integer> frequency) {
        return new TreeMap<>(frequency);
    }

    // MODULE 7: REPORT MODULE
    static void reportModule(TreeMap<String, Integer> sorted) {
        System.out.println("\n========== WORD FREQUENCY REPORT ==========");
        System.out.printf("%-25s %s%n", "WORD", "FREQUENCY");
        System.out.println("-------------------------------------------");

        sorted.forEach((word, count) ->
                System.out.printf("%-25s %d%n", word, count));

        System.out.println("-------------------------------------------");
    }

    // MODULE 8: STATISTICS MODULE
    static void statisticsModule(TreeMap<String, Integer> sorted) {
        int total = 0;
        int highest = 0;

        for (int count : sorted.values()) {
            total += count;
            if (count > highest) {
                highest = count;
            }
        }

        System.out.println("Total Words   : " + total);
        System.out.println("Unique Words  : " + sorted.size());
        System.out.print("Most Frequent Word(s): ");

        if (sorted.isEmpty()) {
            System.out.println("None (0 times)");
        } else {
            for (Map.Entry<String, Integer> entry : sorted.entrySet()) {
                if (entry.getValue() == highest) {
                    System.out.print(entry.getKey() + " ");
                }
            }
            System.out.println("(" + highest + " times)");
        }
    }

    // MODULE 9: ERROR HANDLING MODULE
    static void errorHandlingModule(Exception e) {
        if (e instanceof NumberFormatException) {
            System.out.println("ERROR: Please enter a valid number.");
        } else if (e instanceof IOException
                && !(e instanceof FileNotFoundException)) {
            System.out.println("ERROR while reading file: " + e.getMessage());
        } else {
            System.out.println("ERROR: " + e.getMessage());
        }
    }

    // MAIN METHOD
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        try {
            String text = preprocessingModule(inputModule(sc));
            String[] words = wordExtractionModule(text);
            HashMap<String, Integer> frequency = frequencyCounter(words);
            TreeMap<String, Integer> sorted = sortingModule(frequency);

            reportModule(sorted);
            statisticsModule(sorted);
        } catch (Exception e) {
            errorHandlingModule(e);
        } finally {
            sc.close();
        }
    }
}

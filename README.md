import java.io.*;
import java.util.regex.*;

public class PlagiarismChecker {

    // 文本预处理：去除标点符号，转换为小写
    public static String preprocessText(String text) {
        text = text.replaceAll("[^a-zA-Z\\s]", ""); // 去除标点符号
        text = text.toLowerCase(); // 转换为小写
        return text;
    }

    // 计算最长公共子序列（LCS）的长度
    public static int lcsLength(String original, String plagiarized) {
        int m = original.length();
        int n = plagiarized.length();
        int[][] dp = new int[m + 1][n + 1];

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                if (original.charAt(i - 1) == plagiarized.charAt(j - 1)) {
                    dp[i][j] = dp[i - 1][j - 1] + 1;
                } else {
                    dp[i][j] = Math.max(dp[i - 1][j], dp[i][j - 1]);
                }
            }
        }
        return dp[m][n];
    }

    // 计算相似度
    public static float calculateSimilarity(String original, String plagiarized) {
        int lcsLen = lcsLength(original, plagiarized);
        int totalLen = original.length() + plagiarized.length();
        return (2.0f * lcsLen) / totalLen;
    }

    // 读取文件内容
    public static String readFile(String filePath) throws IOException {
        StringBuilder content = new StringBuilder();
        try (BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
            String line;
            while ((line = reader.readLine()) != null) {
                content.append(line).append("\n");
            }
        }
        return content.toString().trim();
    }

    // 写入结果到文件
    public static void writeFile(String filePath, float similarity) throws IOException {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(filePath))) {
            writer.write(String.format("%.2f", similarity));
        }
    }

    public static void main(String[] args) {
        if (args.length != 3) {
            System.out.println("Usage: java PlagiarismChecker <original_file> <plagiarized_file> <output_file>");
            System.exit(1);
        }

        String originalFilePath = args[0];
        String plagiarizedFilePath = args[1];
        String outputFilePath = args[2];

        try {
            // 读取文件内容
            String originalText = readFile(originalFilePath);
            String plagiarizedText = readFile(plagiarizedFilePath);

            // 文本预处理
            originalText = preprocessText(originalText);
            plagiarizedText = preprocessText(plagiarizedText);

            // 计算相似度
            float similarity = calculateSimilarity(originalText, plagiarizedText);

            // 输出结果到文件
            writeFile(outputFilePath, similarity);

            System.out.println("Similarity calculated and saved to " + outputFilePath);
        } catch (IOException e) {
            System.err.println("Error: " + e.getMessage());
        }
    }
}

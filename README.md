# internorbit_task4
Level-2 Task-2


//CURRENCY CONVERTER

<br>
import java.io.IOException;
<br>
import java.net.URI;
<br>
import java.net.http.*;
<br>
import java.util.Scanner;
<br>
import org.json.JSONObject;
<br>

public class CurrencyConverter {
<br>
    private static final String API_KEY = "YOUR_API_KEY_HERE";
    <br>
    private static final String API_URL = "https://api.thecurrencyapi.com/convert";
    <br>

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        System.out.print("Base currency (e.g., USD): ");
        String from = sc.next().toUpperCase();

        System.out.print("Target currency (e.g., EUR): ");
        String to = sc.next().toUpperCase();

        System.out.print("Amount in " + from + ": ");
        double amount = sc.nextDouble();

        try {
            double rate = fetchExchangeRate(from, to);
            double result = Math.round(rate * amount * 100.0) / 100.0;

            System.out.printf("1 %s = %.6f %s%n", from, rate, to);
            System.out.printf("%.2f %s = %.2f %s%n", amount, from, result, to);
        } catch (Exception e) {
            System.err.println("Error during conversion: " + e.getMessage());
        }
    }

    private static double fetchExchangeRate(String from, String to) throws IOException, InterruptedException {
        HttpClient client = HttpClient.newHttpClient();
        String uri = String.format("%s?from=%s&to=%s&amount=1", API_URL, from, to);

        HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(uri))
            .header("apikey", API_KEY)
            .build();

        HttpResponse<String> resp = client.send(request, HttpResponse.BodyHandlers.ofString());
        if (resp.statusCode() != 200) {
            throw new IOException("API response: " + resp.statusCode());
        }

        JSONObject obj = new JSONObject(resp.body());
        if (!obj.getBoolean("success")) {
            throw new IOException("API error: " + obj.optString("message"));
        }

        JSONObject data = obj.getJSONObject("data");
        return data.getDouble("exchange_rate");
    }
}



## 1. Simple Java Proxy Server

```java
import java.io.*;
import java.net.*;

public class SimpleProxy {

    public static void main(String[] args) throws IOException {
        int port = 8888; // Proxy will listen on this port
        ServerSocket serverSocket = new ServerSocket(port);
        System.out.println("Proxy running on port " + port);

        while (true) {
            Socket clientSocket = serverSocket.accept();
            new Thread(new ProxyThread(clientSocket)).start();
        }
    }
}

class ProxyThread implements Runnable {
    private Socket clientSocket;

    public ProxyThread(Socket clientSocket) {
        this.clientSocket = clientSocket;
    }

    @Override
    public void run() {
        try (
            InputStream clientIn = clientSocket.getInputStream();
            OutputStream clientOut = clientSocket.getOutputStream();
            BufferedReader reader = new BufferedReader(new InputStreamReader(clientIn));
        ) {
            // Read the request line
            String requestLine = reader.readLine();
            if (requestLine == null || requestLine.isEmpty()) return;

            System.out.println("Request: " + requestLine);

            String[] tokens = requestLine.split(" ");
            String method = tokens[0];
            String urlStr = tokens[1];

            URL url = new URL(urlStr);
            String host = url.getHost();
            int port = (url.getPort() == -1) ? 80 : url.getPort();

            // Connect to destination server
            Socket serverSocket = new Socket(host, port);
            OutputStream serverOut = serverSocket.getOutputStream();
            InputStream serverIn = serverSocket.getInputStream();

            // Forward the request line + headers
            serverOut.write((requestLine + "\r\n").getBytes());
            String headerLine;
            while (!(headerLine = reader.readLine()).isEmpty()) {
                serverOut.write((headerLine + "\r\n").getBytes());
            }
            serverOut.write("\r\n".getBytes());
            serverOut.flush();

            // Forward response back to client
            byte[] buffer = new byte[4096];
            int bytesRead;
            while ((bytesRead = serverIn.read(buffer)) != -1) {
                clientOut.write(buffer, 0, bytesRead);
            }

            clientOut.flush();
            serverSocket.close();
            clientSocket.close();

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

---

## 2. How It Works

1. Listens on port `8888` for incoming client requests.
2. For each request, spawns a new thread (`ProxyThread`).
3. Reads the client’s HTTP request and parses the URL.
4. Connects to the destination server and forwards the request.
5. Reads the server’s response and sends it back to the client.

---

## 3. How to Test

* Run the `SimpleProxy.java` class.
* Configure your browser or HTTP client to use **`localhost:8888`** as proxy.
* Access a website like `http://example.com`. You’ll see requests printed in the console.

---

Let’s build a **more advanced Java proxy** that combines:

1. **Caching** – saves server responses.
2. **Filtering** – blocks specific websites or content types.
3. **Logging** – records requests in a log file.

This is closer to a mini “production-ready” forward proxy.

---

## 1. Advanced Proxy Implementation

```java
import java.io.*;
import java.net.*;
import java.text.SimpleDateFormat;
import java.util.*;
import java.util.concurrent.ConcurrentHashMap;

public class AdvancedProxy {

    private static ConcurrentHashMap<String, byte[]> cache = new ConcurrentHashMap<>();
    private static Set<String> blockedSites = new HashSet<>(Arrays.asList(
        "www.blocked.com", "www.example-block.com"
    ));
    private static final String LOG_FILE = "proxy.log";

    public static void main(String[] args) throws IOException {
        int port = 8888;
        ServerSocket serverSocket = new ServerSocket(port);
        System.out.println("Advanced proxy running on port " + port);

        while (true) {
            Socket clientSocket = serverSocket.accept();
            new Thread(new ProxyThread(clientSocket)).start();
        }
    }

    static class ProxyThread implements Runnable {
        private Socket clientSocket;

        public ProxyThread(Socket clientSocket) {
            this.clientSocket = clientSocket;
        }

        @Override
        public void run() {
            try (
                InputStream clientIn = clientSocket.getInputStream();
                OutputStream clientOut = clientSocket.getOutputStream();
                BufferedReader reader = new BufferedReader(new InputStreamReader(clientIn));
            ) {
                String requestLine = reader.readLine();
                if (requestLine == null || requestLine.isEmpty()) return;
                System.out.println("Request: " + requestLine);

                logRequest(requestLine);

                String[] tokens = requestLine.split(" ");
                String urlStr = tokens[1];
                URL url = new URL(urlStr);
                String host = url.getHost();

                // Check if site is blocked
                if (blockedSites.contains(host)) {
                    String blockedMsg = "HTTP/1.1 403 Forbidden\r\n\r\nBlocked by Proxy";
                    clientOut.write(blockedMsg.getBytes());
                    clientOut.flush();
                    clientSocket.close();
                    return;
                }

                // Check cache
                if (cache.containsKey(urlStr)) {
                    System.out.println("Cache hit: " + urlStr);
                    clientOut.write(cache.get(urlStr));
                    clientOut.flush();
                    clientSocket.close();
                    return;
                }

                // Forward request to server
                int port = (url.getPort() == -1) ? 80 : url.getPort();
                Socket serverSocket = new Socket(host, port);
                OutputStream serverOut = serverSocket.getOutputStream();
                InputStream serverIn = serverSocket.getInputStream();

                serverOut.write((requestLine + "\r\n").getBytes());
                String headerLine;
                while (!(headerLine = reader.readLine()).isEmpty()) {
                    serverOut.write((headerLine + "\r\n").getBytes());
                }
                serverOut.write("\r\n".getBytes());
                serverOut.flush();

                // Read response
                ByteArrayOutputStream responseBuffer = new ByteArrayOutputStream();
                byte[] buffer = new byte[4096];
                int bytesRead;
                while ((bytesRead = serverIn.read(buffer)) != -1) {
                    responseBuffer.write(buffer, 0, bytesRead);
                }

                byte[] responseBytes = responseBuffer.toByteArray();
                cache.put(urlStr, responseBytes); // store in cache

                // Send response to client
                clientOut.write(responseBytes);
                clientOut.flush();

                serverSocket.close();
                clientSocket.close();

            } catch (Exception e) {
                e.printStackTrace();
            }
        }

        private void logRequest(String requestLine) {
            try (FileWriter fw = new FileWriter(LOG_FILE, true);
                 BufferedWriter bw = new BufferedWriter(fw);
                 PrintWriter out = new PrintWriter(bw)) {
                String timestamp = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss").format(new Date());
                out.println(timestamp + " - " + requestLine);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

---

## 2. Features Explained

| Feature            | How it Works                                                         |
| ------------------ | -------------------------------------------------------------------- |
| **Caching**        | Saves server response in `ConcurrentHashMap` for future requests.    |
| **Filtering**      | Blocks requests to sites listed in `blockedSites`. Returns HTTP 403. |
| **Logging**        | Records timestamped requests in `proxy.log`.                         |
| **Multi-threaded** | Each request handled in a separate thread for concurrency.           |

---

## 3. How to Test

1. Run `AdvancedProxy.java`.
2. Configure browser or `requests` to use **localhost:8888**.
3. Try accessing a normal website → should work and cache the response.
4. Try accessing a blocked site → should return **403 Forbidden**.
5. Check `proxy.log` → contains all requests with timestamps.

---


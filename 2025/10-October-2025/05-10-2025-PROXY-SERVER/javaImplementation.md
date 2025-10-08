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

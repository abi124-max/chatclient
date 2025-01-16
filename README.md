# chatclient
its about network access
**company**:CODTECH IT SOLUTION PVT.LTD
**NAME**:H.Abirami
**INTERN ID**:CT08GNX
**DOMAIN**:JAVA programming
**BATCH DURATION**:25-12-24 to 25-01-25
**MENTOR NAME**:NEELA SANTHOSH
# ENTER THE DESCRIPTION  OF TASK PERFORMED WITHIN 500 WORDS
Server: The ChatServer class creates a server socket listening on port 12345. When a client connects, a new ServerThread is created to handle the client's communication. The ServerThread reads messages from the client and echoes them back.
Client: The ChatClient class creates a socket to connect to the server. It uses PrintWriter to send messages to the server and a separate ReadThread to read and print messages from the server.
**PROGRAM**
import java.io.*;
import java.net.*;
import java.util.*;
public class ChatServer {

   private static Set<ClientHandler> clientHandlers = new HashSet<>();
   private static final int PORT = 12347;

    public static void main(String[] args) {
        try (ServerSocket serverSocket = new ServerSocket(PORT)) {
            System.out.println("Server started on port " + PORT);

            while (true) {
                Socket clientSocket = serverSocket.accept();
                System.out.println("New client connected: " + clientSocket.getInetAddress().getHostAddress());
                
                ClientHandler clientHandler = new ClientHandler(clientSocket);
                clientHandlers.add(clientHandler);
                new Thread(clientHandler).start();
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    static void broadcast(String message, ClientHandler excludeClient) {
        for (ClientHandler client : clientHandlers) {
            if (client != excludeClient) {
                client.sendMessage(message);
            }
        }
    }

    static void removeClient(ClientHandler clientHandler) {
        clientHandlers.remove(clientHandler);
        System.out.println("Client disconnected.");
    }
}

class ClientHandler implements Runnable {
    private Socket socket;
    private PrintWriter out;
    private BufferedReader in;

    public ClientHandler(Socket socket) {
        this.socket = socket;
    }

    @Override
    public void run() {
        try {
            in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
            out = new PrintWriter(socket.getOutputStream(), true);

            String clientMessage;
            while ((clientMessage = in.readLine()) != null) {
                System.out.println("Received: " + clientMessage);
                ChatServer.broadcast(clientMessage, this);
            }
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                socket.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
            ChatServer.removeClient(this);
        }
    }

    void sendMessage(String message) {
        out.println(message);
    }
}
**PROGRAM OUTPUT**    


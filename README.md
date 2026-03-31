
# 10 Study of socket programming and client-server model Code-blocks
STUDY OF SOCKET PROGRAMMING AND CLIENT SERVER MODEL
# Socket Programming: Client-Server Data Transfer

## 🎯 AIM
To write a Socket program to transfer data between Client and Server.

## 🧰 EQUIPMENTS REQUIRED
- PC with Linux operating system

## 🛠️ PROCEDURE
1. Connect two computers in Wired/Wireless LAN.
2. Ensure both computers are on the same network and can ping each other.
3. Open terminal and type `gedit filename.c` to write the program.
4. Compile using `gcc filename.c`.
5. Execute using `./a.out`.
6. Run the program on both server and client machines.
7. Enter the IP address of the server on the client machine.
8. Enter the data to be transferred and verify its size.

## 💻 PROGRAM

### 🔸 SERVER

Server Side:

#include <stdio.h>
#include <winsock2.h>

int main() {
    WSADATA wsa;
    SOCKET server_sock, client_sock;
    struct sockaddr_in server, client;
    int c, recv_size;
    char client_message[2000];

    if (WSAStartup(MAKEWORD(2,2), &wsa) != 0) {
        printf("WSAStartup failed: %d\n", WSAGetLastError());
        return 1;
    }

    server_sock = socket(AF_INET, SOCK_STREAM, 0);
    if (server_sock == INVALID_SOCKET) {
        printf("Could not create socket: %d\n", WSAGetLastError());
        WSACleanup(); return 1;
    }

    server.sin_family = AF_INET;
    server.sin_addr.s_addr = INADDR_ANY;
    server.sin_port = htons(8888);

    if (bind(server_sock, (struct sockaddr *)&server, sizeof(server)) == SOCKET_ERROR) {
        printf("Bind failed: %d\n", WSAGetLastError());
        closesocket(server_sock); WSACleanup(); return 1;
    }
    puts("Bind done. Listening on port 8888...");

    listen(server_sock, 3);

    c = sizeof(struct sockaddr_in);
    client_sock = accept(server_sock, (struct sockaddr *)&client, &c);
    if (client_sock == INVALID_SOCKET) {
        printf("Accept failed: %d\n", WSAGetLastError());
        closesocket(server_sock); WSACleanup(); return 1;
    }
    puts("Client connected.");

    while ((recv_size = recv(client_sock, client_message, sizeof(client_message)-1, 0)) > 0) {
        client_message[recv_size] = '\0';
        send(client_sock, client_message, recv_size, 0);
    }

    if (recv_size == 0) puts("Client disconnected");
    else printf("recv failed: %d\n", WSAGetLastError());

    closesocket(client_sock);
    closesocket(server_sock);
    WSACleanup();
    return 0;
}
Client Side:

#include <stdio.h>
#include <string.h>
#include <winsock2.h>

int main() {
    WSADATA wsa;
    SOCKET sock;
    struct sockaddr_in server;
    char message[1000], server_reply[2000];
    int recv_size;

    if (WSAStartup(MAKEWORD(2,2), &wsa) != 0) {
        printf("WSAStartup failed: %d\n", WSAGetLastError());
        return 1;
    }

    sock = socket(AF_INET, SOCK_STREAM, 0);
    if (sock == INVALID_SOCKET) {
        printf("Socket creation failed: %d\n", WSAGetLastError());
        WSACleanup(); return 1;
    }

    server.sin_addr.s_addr = inet_addr("127.0.0.1");
    server.sin_family = AF_INET;
    server.sin_port = htons(8888);

    if (connect(sock, (struct sockaddr *)&server, sizeof(server)) == SOCKET_ERROR) {
        printf("Connect failed: %d\n", WSAGetLastError());
        closesocket(sock); WSACleanup(); return 1;
    }
    puts("Connected to server.");

    while (1) {
        printf("Enter message: ");
        if (scanf("%999s", message) != 1) break; // reads up to whitespace
        if (send(sock, message, (int)strlen(message), 0) == SOCKET_ERROR) {
            puts("Send failed"); break;
        }
        recv_size = recv(sock, server_reply, sizeof(server_reply)-1, 0);
        if (recv_size == SOCKET_ERROR) { printf("recv failed: %d\n", WSAGetLastError()); break; }
        server_reply[recv_size] = '\0';
        printf("Server reply: %s\n", server_reply);
    }

    closesocket(sock);
    WSACleanup();
    return 0;
}
    
## 🎯 RESULT
Thus, a socket program was successfully written to transfer data between client and server, and its performance was studied.

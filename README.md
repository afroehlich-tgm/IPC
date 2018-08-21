# IPC
IPC-Ausarbeitung sowohl Theorie als auch Praxisbeispiele.  Zur Übung und Hilfe für meine Prüfung.
#IPC-Ausarbeitung

#Einführung Sockets

Sockets setzen auf der vierten Schicht auf und
befinden sich selbst auf Layer 5
Je nach verwendetem Protokoll auf Schicht 4 unterscheiden wir zwischen
TCP-Sockets (”Stream“) (WWW, Webservices, Logins, Datenbankanwednungen)
oder
UDP-Sockets (”Datagram“)(Audio-Streamin, Videos, Online-Gaming, Live-Daten)

Bei UDP-Sockets vereinfacht sich das Verfahren

-Ist ein Kommunikationsendpunkt
-wird über IP und Port eindeutig identifiziert
-nimmt eingehende Verbindungen entgegen
- verbindet sich mit seinem Gegetsück

#Server-Socket:
-Wartet & "horcht" auf eingehende Verbindungen durch Clients
-Passiver Socket
-Bekommt IP und Port
-Typischerweiser Multi-Threaded

#Client-Socket
-Verbindet sich mit dem Server-Socket und initiiert den Verbindungsaufbau
-ist der aktive Socket
-Muss IP und Port des Servers wissen
-Multi oder Single-Threaded



#Von einem Socket lesen

1. Eine Socket-Verbindung zum Server herstellen

```java
Socket chatSocket = new Socket("IP", PORT);

```

2. Einen InputStreamReader erzeugen, 
der mit den Lowlevel-Eingabestrom des Sockets (dem Verbindungsstrom) verkettet ist.

```java
InputStreamReader strom = new InputStreamReader(chatSocket.getInputStream());

```

3. Einen BufferedReader machen und lesen

```java
BufferedReader reader = new BufferedReader(strom);
String message = reader.readLine();

```

#In einen Socket schreiben

1. Eine Socket-Verbindung zum Server herstellen

```java
Socket chatSocket = new Socket("IP", PORT);

```

2. Einen PrintWriter erzeugen, der mit dem Lowlevel-Ausgabestrom des Sockets
(dem Verbindungsstrom) verkettet ist.

1. Eine Socket-Verbindung zum Server herstellen

```java
PrintWriter writer = new PrintWriter(chatSocket.getOutputStream());
```

3. Etwas schreiben
```java
writer.println("Nachricht");
writer.print("Noch eine Nachricht");

```

#Eine einfache Server-Client Anwendung

Server: 
```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.OutputStream;
import java.io.PrintWriter;
import java.net.ServerSocket;
import java.net.Socket;

public class Server {

	public static void main(String[] args) {
		try {
			ServerSocket s = new ServerSocket(7777);
			System.out.println("Server gestartet");

			Socket c = s.accept();
			
			OutputStream out = c.getOutputStream();
			PrintWriter writer = new PrintWriter(out);

			InputStream in = c.getInputStream();
			BufferedReader reader = new BufferedReader(new InputStreamReader(in));

			String msg = null;
			while ((msg = reader.readLine()) != null) {
				writer.write(msg + "/n");
				writer.flush();
				System.out.println("Empfangen vom Client: "+msg);
			}
				writer.close();
				reader.close();
			

		} catch (IOException e) {
			// TODO Auto-generated catch block
			e.printStackTrace();
		}

	}
}
```
Client:
```java

```












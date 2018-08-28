# IPC
IPC-Ausarbeitung sowohl Theorie als auch Praxisbeispiele.  Zur Übung und Hilfe für meine Prüfung.
# IPC-Ausarbeitung

# Einführung Sockets

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

# Server-Socket:
-Wartet & "horcht" auf eingehende Verbindungen durch Clients
-Passiver Socket
-Bekommt IP und Port
-Typischerweiser Multi-Threaded

# Client-Socket
-Verbindet sich mit dem Server-Socket und initiiert den Verbindungsaufbau
-ist der aktive Socket
-Muss IP und Port des Servers wissen
-Multi oder Single-Threaded



# Von einem Socket lesen

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

# In einen Socket schreiben

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



# Shared Memory

Einfachste Art, Prozesse miteinander kommunizieren zu lassen
Prozesse teilen sich einen eigens angelegten geteilten Speicherbereich - Shared Memory
Über ein Mapping wird der geteilte Speicherbereich im eigenen Adressraum der Prozesse abgebildet
Geschieht in Python implizit im Hintergrund!

```python
v = Value(’d’, 0.0)
arr = Array(’i’, range(10))

self.v.value = 1234

for i in range(len(self.arr)):
	self.arr[i] = −self.arr[i]
```
Value und Array sind spezielle Klassen, welche in einer Shared Memory Map fur alle Prozesse gespeichert werden ¨
Sie sind thread- und prozesssicher, d.h. der gleichzeitige Zugriff auf diese Objekte wirft keine Exceptions
Aber Achtung: Deswegen muss manchmal trozdem der Zugriff synchronisiert werden (z.B. Lock) 
’d’ steht fur double und ¨ ’i’ steht fur integer (typecodes) 
Es können auch andere c-Datentypen geteilt werden (über multiprocessing.sharedctypes)

#Pipes
Pipes basieren auf dem Prinzip von Datenstr¨omen (Streams), welche wir schon kennengelernt haben
Streams sind ” Kommunikationskanäle“ und werden verwendet, um Eingabe- und Ausgabekanäle abzubilden
Pipes stammen aus der UNIX-Welt
Ursprünglich hatte jeder Prozess genau einen Eingabe- und Ausgabestream Standardmäßig sind diese mit der Konsole verbunden
Uber Pipes können diese umgeleitet werden, sodass der Ausgabekanal eines Prozesses mit dem Eingabekanal eines anderen
verbunden ist
Mittlerweile von den meisten Betriebssystemen unterstutzt

Pipes übertragen wie Sockets im Hintergrund byteweise
Im Vergleich zu Sockets bringen Pipes am selben Rechner jedoch erhebliche Geschwindigkeitsvorteile
Pipes können (abhängig von OS und Sprache) auch über das Netzwerk verwendet werden, allerdings empfehlen sich hier eher
Sockets, da diese fur eine paketweise  übertragung über das Netzwerk ausgelegt sind

```python
from multiprocessing import Process , Pipe
class SenderProcess (Process):
	def __init__ (self, pipe):
		Process.__init__(self)
		self.pipe = pipe
	def run(self):
		self.pipe.send ((123 ,"Hallo Pipe!"))


class ReceiverProcess (Process):
	def __init__(self, pipe):
		Process.__init__(self)
		self.pipe = pipe
	def run(self):
		print(self.pipe.recv ())
		if __name__ == "__main__":
		parent connection , child connection = Pipe ()
		receiver = ReceiverProcess ( child connection )
		receiver.start ()
		sender = SenderProcess ( parent connection )
		sender.start ()
		receiver.join ()
		sender.join ()
```

# Queues

Die multiprocessing.Queue ist fast eine 1:1 Kopie von queue.Queue
Es gibt einen großen Unterschied: Es gibt kein task_done() und join() – hierfür gibt es JoinableQueue
Queues basieren auf Pipes (und verwenden sie im Hintergrund)
Queues unterstützen aber mehrere Producer und Consumer, während bei Pipes gilt, dass pro Pipe-Endpunkt nur ein Prozessschreiben und lesen darf
Es gibt wieder die put() und get() Methoden

```python
from multiprocessing import Process , Queue
class SenderProcess (Process):
	def __init__ self, queue):
		Process.__init__(self)
		self.queue = queue
		
	def run(self):
	self.queue.put ((123 ,"Hallo Queue!"))

class ReceiverProcess (Process):
	def __init__(self, queue):
		Process.__init__(self)
		self.queue = queue
	
	def run(self):
		print(self.queue.get ())
		

if __name__ == "__main__":
	q = Queue ()
	receiver = ReceiverProcess (q)
	receiver.start ()
	sender = SenderProcess (q)
	sender.start ()
	receiver.join ()
	sender.join ()

```

# Events
Die Klasse multiprocessing.Event ist ebenfalls eine 1:1 Kopie von threading.Event
Auch hier werden Threads über das Eintreten eines Ereignisses benachrichtigt
Es gibt wieder die wait() und set() Methoden

# Bedingungsvariablen
Multiprocessing.Condition funktioniert ebenfalls genauso wie threading.Condition
Auch hier können sich Threads gegenseitig benachrichtigen und es gibt zusätzlich noch eine Lock
Es gibt wieder die wait() und notify() Methoden


# Semaphore
Semaphore ¨ahneln Bedingungsvariablen und Locks
Ein Semaphor besitzt einen internen Zähler, welcher bei der Erstellung mit einem Wert initialisiert wird
Ein Semaphor unterstützt zwei grundlegende Operationen: I acquire: Ist der Zähler größer als 0, wird der Wert um 1 erniedrigt
und fortgefahren. Ansonsten wird gewartet, bis der Zähler größer als 0 ist.
release: Erhöt den Zähler wieder um 1. Gibt es wartende Prozesse, wird einer davon aufgeweckt.
Ein Semaphor stellt sozusagen "die Anzahl an freien Plätzen“ in einem kritischen Abschnitt dar
Anwendungsfälle: Limitierte Ressourcen wie Datenbankzugriffe

Metapher: Parkhaus
Im Parkhaus Franz-Josefs-Kai können nur 130 Autos gleichzeitig parken
acquire:
BesucherIn kann Parkhaus nur betreten,
wenn Zähler größer 0 ist.
Beim Betreten des Parkhauses wird der
Zähler um 1 verringert, ansonsten muss man warten.
release:
Beim Verlassen wird Z¨ahler um 1 erhöht.
Ein wartendes Auto kann nun das Parkhaus betreten.

![alt text](https://github.com/afroehlich-tgm/IPC/blob/master/IPC.PNG)













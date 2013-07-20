======================================
VSS FS13 Repetitionsfragen Antworten
======================================

Dieses Dokument wird vorzu erweitert. Ergänzungen und Antworten sind herzlich willkommen.
Repetitionsfragen: https://github.com/moonline/Vss/blob/master/RepetitionQuestions.Vss.tex


Systemmodelle
=============
1) Architekturmodelle zeigen wie die (verteilten) Komponenten zusammenarbeiten

2)
	Client / Server
		Clients stellen Anfragen an einen zentralen Server / initiieren einen Prozess, der Server Antwortet / liefert den Clients Ergebnisse
	Proxy Server & Cache
		Clients stellen Anfragen, diese laufen über einen Proxyserver mit einem Cache. Teilweise erhalten die Clients die Antwort, teilsweise leitet der Proxy Server die Anfrage and den originalen Server weiter.
	P2P
		Alle Kommunikationsteilnehmer sind gleichberechtigt. Oft gibt es einen Leader, dieser ist jedoch austauschbar
	Applets
		Der Client lädt sich vom Server ein Applet herunter. Anschliessend kommuniziert der Client nur noch mit dem Applet und das Applet kommuniziert im Hintergrund mit dem Server, ohne das der Client dies explizit wahrnimmt.
	Thin Client / Fat Server
		Die Clients besitzen selbst keine Software und Daten oder nur wenige. Der Grossteil der Rechenarbeit und Daten stellen die Server bereit. Je nach Ausführung schwank die Verteilung zwischen "Server stellt nur Daten bereit" und "Server stellt Daten und Applikationen bereit, Client ist nur noch UI".
	MVC Pattern
		Die Daten, Model und Controller liegen auf dem Server, die Präsentation beim Client
	Three Tier
		::
		
			Client     [ Präsentation  ]
			- - - - - - - - - -|- - - - - - 
			Server     [ Businesslogik ]
			                   |
			           [     Daten     ]
	
	
	ISO OSI Layer Model
		Aufteilung in Prozesse (Anwendungen), Transport und Netzwerk
		
3) Allgegenwärtige Systeme, immer verfügbar und überall. Dies zieht Kensequenzen wie stark schwankende Netzqualität, Verfügbarkeit, Energieversorgung, ... mit sich.

4) Interaktionsmodell: beschreibt die Kommunikation zwischen den Komponenten. Es gibt keine globale Uhr, nur Nachrichten.

5) Fehlermodell: Beschreibt, was bei einem Fehler passiert und wie die Komponenten darauf reagieren

6) Sicherheitsmodell: beschreibt Bedrohungen und Sicherheitsmassnahmen zur Sicherung der Kommunikation und der Komponenten

7) RM-ODP: Metamodell für verteilte Systeme. Beschreibt ein verteiltes System von verschiedenen Viewpoints aus um die Komplexität zu verringern. Mögliche View Points:
	* Informations / Datensicht: Datenmodell, Constraints
	* Computational- / Logiksicht: Komponenten und Interfaces
	* Engineeringsicht: Netzarchitektur, Client- / Server Aufteilung
	* Technologysicht: Abbildung der Architektur auf echte Hardware
	
	
Interprozesskommunikation
=========================
8)
	synchron
		Sender sendet Message und wartet, bis Antwort eintrifft. -> blockiert
	asynchron
		Sender sendet Message, legt Buffer für Antwort an und holt sie später ab. -> blockiert nicht
		
9)
	Ports
		Schnittstellen an einem Host
	Sockets
		Endpunkt der IP, TCP/UDP Kommunikation, ist immer an einen Port gebunden, wird durch ein File im Hintergrund repräsentiert.
		
10)
	IP
		Verbindungslose Kommunikation: Best Effort, Keine Übermittlungsbestätigung. Keine Übertragungsgarantie. Pakete können verloren gehen, doppelt oder in falscher Reihenfolge ankommen. Fehler aus tieferen Layern treten auch in IP auf.
	TCP
		Verbindungsbehaftete Kommunikation: Bestätigung und Retransmit bei verlorengegangenen. Reihenfolge garantiert.
	UDP
		Verbindungslose Kommunikation: Übertragung nicht garantiert, keine Bestätigung dafür weniger Overhead.
		
11) Pakete können verloren gehen, verworfen werden, doppelt oder in falscher Reihenfolge ankommen
		
12) UDP besitzt wesentlich weniger Overhead als TCP. Spielt die übertragene Datenmenge eine grössere Rolle als die vollständige Übertragung, so ist UDP geeignet. z.B. Multimedia Streaming, VoIP

13)
	UDP:
		.. code-block:: java
		
			// sender
			try {
				DatagramSocket socket = new DatagramSocket();
				String message = "Something to send...";
				DatagramPacket p = new DatagrammPacket(message.getBytes(), (message.getBytes()).length, InetAddress.getByName(receiverHost), receiverPort);
				try {
					socket.send(p);
				} catch (IOException e) { e.printStackTrace(); }
				socket.close();
			} catch (Exception e) { System.err.print(e); }
			
			// receiverHost
			try {
				DatagramSocket socket = new DatagramSocket(port);
				buffer byte[] new byte[512];
				String message = "";
				String messagePart;
				do {
					DatagramPacket p = new DatagramPacket(buffer, buffer.length);
					socket.receive(p);
					messagePart = new String(buffer).trim();
					message += messagePart;
				} while (!messagePart.equals("."));
				System.out.println(message);
				socket.close();
			} catch (Exception e) { System.err.print(e); }
			
			
	TCP
		.. code-block:: java
		
			// sender
			try (Socket socket = new Socket()) {
				BufferedOutputStream bos = new BufferedOutputStream(socket.getOutputStream());
				socket.connect();
				String message = "Somethin to send...";
				bos.write(message);
			} catch (Exception e) { System.err.print(e); }
			
			// receiver
			try (ServerSocket socket = new ServerSocket(serverPort)) {
				while(true) {
					try (ClientSocket client = socket.accept()) {
						DataInputStream in = client.getInputStream();
						DataOutputStream out = client.getOutputStream();
						
						String message = in.readUTF();
						out.write("Successfull received: "+message);
						
						System.out.println(message);
						client.close();
					} catch (Exception e) { System.err.println(e); }
				}
				socket.close();
			} catch (Exception e) { System.err.print(e); }
			
			
14) Sie führen dazu, dass das Programm auf send() und receive() warten muss und blockiert ist. Besser: asynchrones send() benutzen.

15) Sind Timeout und maximale Anzahl Retransmit ausgeschöpft, bricht TCP ab. Somit können auch bei TCP Pakete verloren gehen.

16) 
	Multicast
		Empfänger melden sich bei einer Gruppe an. Nachrichten an die Gruppe werden automatisch allen Teilnehmern zugestellt. Gruppenkommunikation basiert oft auf Multicasting. Multicast muss von den Netzwerkkomponenten unterstützt werden.
	IP Multicast
		Verwendung von speziellen Adressen (224.0.0.1 bis 239.255.255.254) um Gruppen zu identifizieren. Verteilt wird die Nachricht von speziellen Multicastroutern. Begrenzung möglich durch Angabe der maximal durchlaufbaren Router.
		
17)
	Marshalling
		Daten mit einer internen Struktur werden in eine externe Struktur übersetzt (z.B. für den Versand in einem UDP Paket):
	Un-Marshalling
		Daten einer externen Struktur werden in eine interne übersetzt.
		
18)
	direkte Socketnutzung
		Exakte Steuerung der Sockets, möglicherweise effizienter
	Nutzung durch Middleware
		Abstraktion, keine loq-level Programmierung, universeller

19) Die Objekte werden zerlegt und als Textform repräsentiert. Serialisierung kann genutzt werden, um Objekte über das Netzwerk zu übertragen.
		Nachteile: Private Member sind sichtbar, Objektversionen können Ärger machen, Referenzen Serialisieren ist schwierig (möglicherweise sind die Referenzierten Objekte nicht mehr da bei der reSerialisierung)
		
20)

21) 
	* Serialisierbare Klassen müssen Serializable sein. 
	* Mit transient können nicht zu serialisierbare Attribute gekennzeichnet werden. 
	* Durch Überschreiben der Methode writeObject() und readObject() kann die Serialisierung gesteuert werden.
	* Unterklassen von serialisierbaren Klassen sind ebenfalls serialisierbar!
	
22) Java Versionsnummer von Klassen (beinhaltet class-hash): Wird die Klasse verändert, können keine alten Objekte mehr deserialisiert werden! Mittels einer eigen vergebenenSerialVerUid kann die Versionierung gesteuert werden.

23) Java Objekte als XML exportieren. Vorteil: Standartisiertes Datenformat, das auch andern Applikationen erlaubt, die Objekte weiterzuverwenden.

24) Die Referenzen können ungültig werden und die gleiche Referenz darf nicht wiederverwendet werden.

25) Die Java Non-blocking IO Channels dienen zum nicht blockierenden Datenaustausch anstelle der normalen (blocking) Channels.


Verteilte Objekte und entfernte Aufrufe
=======================================
26) Damit der Benutzer entsprechende Massnahmen treffen kann gegen blockieren (z.B. asynchronen Aufruf).

27)
	* Remote Garbage Collection
	* Remote Procedure Call
		
28) 
	RRA
		* Clients stellen Requests an einen Server (R). 
		* Der Server antwortet falls notwendig (R).
		* Der Client sendet je nach dem ein Acklowledge (A).
	Probleme
		* Nachricht/Reply/Ack kann verloren gehen
		* Message/Request kann mehrfach ankommen
		* Message / Reply kann mehrfach interpretiert/ausgeführt werden mit unterschiedlichen Ergebnissen, falls Message/Reply den Status von Server oder Client verändern.
		* Server/Client kann ausfallen, überlastet sein
		* Kommandreihenfolge kann durcheinander geraten
		
29) siehe 28.Probleme

30) 
	* Remote Call wird 1 mal durchgeführt (Maybe)
	* Remote Call mindestens einmal durchgeführt (at least once) -> mehrmals möglich mit untersch. Resultaten
	* Remote Call wird höchstens einmal durchgeführt (at most once) -> Reply wird gespeichert: immer gleiches Res.
	* Remote Call wird genau einmal durchgeführt
		
31) Es soll entfernt eine Methode aufgerufen werden, genau so wie es lokal geschieht.
		Aufruf
			Unbedingt Call by Value, da Referenzen auf Server und Client unterschiedlich sind
		lokale Memer
			Zugriff auf lokale Member remote ist zu unterlassen, die Referenz könnte sich geändert haben in der Zwischenzeit.
			
32) Interface definition Language. Definiert Signatur der Methoden sowie in und out parameter.
	.. code-block:: corba
	
		interface NodeList {
			void addNode(in Node node);
			void removeNode(in int position, out Node node);
		}
		
		
33) 
	* Remote Procedure Call: Data Structs + die Nummer der aufzurufenden Methode werden übermittelt. 
	* Retourniert werden Structs.
	* Communication Moduls auf beiden Seiten sind für die Übermittlung zuständig
	
34) Procedure ist grösser, beinhaltet u.U. mehrere Methoden, Methode call ist eher simpel	
	
35) 
	* Remote Method Invocation. 
	* Biete Remote method calls, Übertragung von serialisierten Java Objekten und remote Garbage Collection.
	* Der Client kommuniziert mit einem Proxy, der Server mit einem Skeleton. Proxy und Skeleton kommunizieren miteinandern und übernehmen Garbage Collecton, Remote Calls, ...
	
	
RMI
===
36) 
	Interface Server
		Interface zur Verfügung stellen
	Registry
		Registry initieren
	Server
		Server erzeugen
		Serverinterface implementieren
		Server anmelden bei Registry
	Client
		Client erzeugen
		RMI Server loockup in Registry machen
		Entfernte Methode auf lokalem Serverproxy aufrufen
		
37) 
	Client
		* besitzt lokale Objekte
		* besitzt einen Proxy für jedes Remote Objekt
		* besitzt ein Kommunikationsmodule, führt Request/Reply Protokoll aus
		* besitzt ein Remote Reference Module, das lokale und remote Referenzen zueinander übersetzt
		* Der Client spricht nur mit dem Proxy
	Server
		* besitzt lokale Objekte (Dienstobjekte, die der Client aufruft)
		* besitzt ein Kommunkationsmodule
		* besitzt Skeleton & Dispatcher für die lokalen Objekte
		* besitzt ein Remote Reference Module
		
38) 
	Proxy
		repräsentiert das entfernte Objekt, implementiert die Schnittstelle des entferten Objektes. Der Client spricht immer nur mit dem Proxy.
	Skeletton
		repräsentiert den Client, implementiert die Methoden der entfernten Schnittstelle. Der Server spricht immer nur mit dem Skeletton. Nur der Proxy des Clients und der Skelleton des Servers sprechend über das Netz direkt miteinander.
		
39) Der Dispatcher wählt die gewünschte Methode auf dem Skeleton aus.

40) 
	Call by Value
		Objekt Serialisieren, mitgeben
	Call by Reference (pseudo)
		Dem Server den Client als Parameter mitgeben, damit der Server auf dem Client entsprechende Methoden aufrufen kann
		
41) Remote Garbage Collection übernimmt das Entsorgen von nicht mehr benötigten Objekten in einem RMI System. Client und Server wissen nicht, wann auf der andern Seite ein Objekt stirbt und sie es abräumen können. Daher muss der GC regelmässig remote vorbeischauen und überprüfen, welche Objekte nicht mehr benötigt werden.

42) Das Interface wird auf einen Interface Server geladen. Client und Server laden sich das Interface herunter und implementieren es. Gegenüber dem normalen RMI ändert sich für den Client nichts, dem Server muss eine remote Codebase hinterlegt werden.

43) Dem RMI Server wird ein Daemon vorgeschaltet. Der Server geht schlafen sobald seine aktuellen Requests abgehandelt wurden. Der Client spricht den Server an, der Daemon weckt den Server und übergibt den Call.


Messaging
=========
44) 
	* Messaging ist Technologie unabhängig und es können beliebige Formate zum Einsatz kommen (JSON, XML, ...).
	* Sender und Empfänger müssen nicht zwingend zur gleichen Zeit aktiv sein.
	
45) Sender und Empfänger sind entkoppelt. Die Kommunikation läuft über eine Zwischenstelle/Vermittler.

46)	+--------------------+--------------------------------------------+--------------------------------------------+
	|                    | räuml. gekoppelt                           | räumlich nicht gekoppelt                   |
	+--------------------+--------------------------------------------+--------------------------------------------+
	| zeitlich gekoppelt | Sender und Empfänger müssen gleichzeitig   | Sender und Empfänger müssen gleichzeitig   | 
	|                    | aktiv sein, damit eine Nachricht über-     | aktiv sein zur Nachrichtenübertragung. Die |
	|                    | mittelt werden kann. Die Kommunikation ist | Kommunikation ist ungerichtet. z.B. IP     |
	|                    | gerichtet. z.B RMI                         | Multicast                                  |
	+--------------------+--------------------------------------------+--------------------------------------------+
	| zeitlich nicht gek.| Sender und Empfänger müssen nicht gleich-  | Sender und Empfänger brauchen sich nicht   |
	|                    | zeitig aktiv sein. Die Kommunikation ist   | zu kennen und haben eigene Zeitsysteme.    |
	|                    | gerichtet.                                 |                                            |
	+--------------------+--------------------------------------------+--------------------------------------------+

	
Gruppenkommunikation
--------------------
47) Multimediastreaming, Multimediachat mit mehreren Teilnehmern, Nachrichtensystem für mehrere Empfänger

48) 
	offene Gruppe
		Kommunikation von aussen in die Gruppe möglich
	geschlossene Gruppe
		Kommunikation nur innerhalb der Gruppe
	überlappende Gruppe
		Mitglieder können in mehreren Gruppen seine
		
49)
	Anforderungen an Zuverlässigkeit und Ordnung
		P2P
			* Integrität: versendete und empfangene Nachricht sind identisch
			* Validität: Nachricht wird eventuell abgeliefert
		Pub / Sub
			* Integrität
			* Validität
			* Agreement: Wird die Nachricht an jemanden ausgeliefert, wird sie an alle ausgeliefert
	Ordnung
		FIFO
			Nachrichten werden an alle in der gleichen Reihenfolge ausgeliefert
		Kausale Ordnung
			Die Ordnung der Ereignisse beeinflusst die Reihenfolge der Nachrichten
		Totale Ordnung
			Wenn eine Nachricht vor einer anderen einem Mitglied ausgeliefert wird, wird sie das an alle
			
50) JGroups erlaubt das erzeugen von Groups und das Publishen / Subscripen in eine Gruppe.
		* Channels: Gruppen, denen man beitreten kann
		* Building Blocks: Gruppieren Channels
		
		
Publisher / Subscriber
----------------------
51) 
	* Publisher bringen Nachrichten in einen Pool eine
	* Subscriber werden benachrichtigt, sobald neue Nachrichten da sind
	* Rollen:
		* Ereignisdienst: generiert Ereignisse
		* relevantes Objekt
		* Beobachter: Vermittler
	* Mit Filter können Subscriber die Nachrichten filtern
	
52) 
	Topic
		Nachrichten können an ein Thema adressiert werden. Es werden nur die Abonnenten des Themas über die neue Nachricht benachrichtigt
	Typen
		Subscribers werden anhand des Nachrichtentyps benachrichtigt.
		
53) Die Queue muss dies übernehmen. Kennt die Queue die Subscribers nicht, so ist das unmöglich.

54) Der Broker übersetzt die Nachricht vom Format eines Systems für das Format eines andern Systems
			
			
			
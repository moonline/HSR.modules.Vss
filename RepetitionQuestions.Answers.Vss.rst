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
	
	
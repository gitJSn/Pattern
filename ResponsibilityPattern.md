# Responsibility Chain Pattern

Das Responsibility Chain Pattern ist ein Verhaltensmuster in der Softwareentwicklung, das dazu dient, Anfragen oder Anforderungen in einer Kette von Empfängern zu verarbeiten, bis einer in der Lage ist, die Anfrage erfolgreich zu bearbeiten. Es ermöglicht es, dass mehrere Klassen an einer Verarbeitung beteiligt sind, ohne dass es zu einer Verbindung zwischen jeder Klasse und ihrem Anrufer kommt.

So kann man zum Beispiel einschränken wer Zugriff auf welchen Inhalt bzw. welche Funktionen hat.
****
## In diesem Beispiel haben nur bestimmte Nutzer Zugriff auf das Teile Bestellsystems:

![example](./problem1-en.png)
[Quelle](https://refactoring.guru/design-patterns/chain-of-responsibility)
****

Die Authentifizierung kann zum Beispiel aus diesen Schritten bestehen:

* Nutzer müssen angemeldet sein
* Nutzer ist VIP und sieht besondere Angebote
* Nutzer ist Admin und kann Bestellungen bearbeiten

****

Diese Schritte kann man mit Hilfe des **Responsibility Patterns** in sogenannte Handler aufteilen.

## Wie sieht das also im Code aus?

```java
interface Handler {
    void setNext(Handler handler);
    void handleRequest(Request request);
}
```

## Implementierung eines Handlers:
```java
class LoggedInHandler implements Handler {
    private Handler next;

    @Override
    public void setNext(Handler handler) {
        this.next = handler;
    }

    @Override
    public void handleRequest(Request request) {
        if (isLoggedIn(request)) {
            System.out.print("Nutzer ist angemeldet!");

            // Überprüfung ob es einen weiteren Handler gibt!
            if(next != null) {
                next.handleRequest(request);
            }
        } else {
            System.out.print("Nutzer nicht angemeldet!");
        }
    }

    private boolean isLoggedIn(Request request) {
        // Überprüfung mit z.B. Token ob Nutzer angemeldet ist
        return true;
    }
}
```

```java
Handler loggedInHandler = new LoggedInHandler();
Handler isAdminHandler = new AdminHandler();
Handler isVIPHandler = new AdminHandler();

loggedInHandler.setNext(isAdminHandler);
isAdminHandler.setNext(isVIPHandler);
```

![example2](solution1-en.png)
[Quelle](https://refactoring.guru/design-patterns/chain-of-responsibility)
***
## Was genau könnte z.B. konkret passieren?
* Nutzer ist nicht angemeldet --> kein Zugang
* Nutzer ist angemeldet und ist Admin --> Zeige ihm direkt die Bestellübersicht
* Nutzer ist angemeldet aber kein Admin? --> Überprüfen ob er VIP ist und zeige ihm entsprechende Angebote

***
# Aufgabe
* Schau dir den vorhandenen Code an
* Implementiere den LoggedInHandler 
1. Öffne die Klasse "LoggedInHandler"
2. Übernehme die setNext Methode aus dem Beispiel
3. Implementiere die HandleRequest Methode
   - Ein Request beinhaltet bereits die Felder "email" und "password"
   - Mit Hilfe der vorhandenen checkLogin Methode kannst du jetzt herausfinden ob ob du "next.handleRequest(request)" ausführen musst oder ob der User keinen Zugriff bekommt.
   - Teste den LoginHandler in der Main(App.java) -> loggedInHandler.handleRequest(...)
4. Mache das selbe für die VIP Überprüfung
5. Setze den VIP handler als nächsten handler nach dem loggedInHandler und teste erneute.
6. Jetzt kannst du noch den Adminhandler implementieren und ihn in deine "Responsibility Chain" hinzufügen!
7. (Schreibe Tests für deine Handler)
<properties 
	pageTitle="Verwendung des Slack-Connectors in Logik-Apps | Microsoft Azure App Service"
	description="Erstellen und Konfigurieren des Slack-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service"
	authors="rajeshramabathiran" 
	manager="erikre" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2016"
	ms.author="rajram"/>

# Erste Schritte mit dem Slack-Connector und das Hinzufügen zur Logik-App
>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview. Um die Schemaversion 2015-08-01-preview aufzurufen, klicken Sie auf [Slack-API](../connectors/connectors-create-api-slack.md).

Sie können eine Verbindung mit Slack-Kanälen herstellen und Nachrichten an Ihr Team senden. Connectors können in Logik-Apps als Teil eines "Workflows" zum Erledigen verschiedener Aufgaben verwendet werden. Wenn Sie den Slack-Connector im Workflow verwenden, können Sie eine Vielzahl von Szenarien mithilfe anderer Connectors umsetzen. Beispielsweise können Sie den [Facebook-Connector](app-service-logic-connector-facebook.md) in Ihrem Workflow zum Übermitteln einer Nachricht an Ihren Slack-Kanal verwenden.

## Trigger und Aktionen
*Trigger* sind Ereignisse, die stattfinden. Z. B. wenn ein Auftrag aktualisiert oder ein neuer Kunde hinzugefügt wird. Eine *Aktion* ist das Ergebnis des Triggers. Z. B. bei Aktualisierung eines Auftrags eine Warnung an den Verkäufer senden. Oder bei Hinzufügen eines neuen Kunden eine E-Mail zur Begrüßung an den neuen Kunden senden.

Der Slack-Connector kann als ein Trigger oder eine Aktion in einer Logik-App verwendet werden und unterstützt Daten im JSON- und XML-Format. Es gibt derzeit keine Trigger für den Slack-Connector.

Der Slack-Connector verfügt über folgende Trigger und Aktionen:

Trigger | Aktionen
--- | ---
Keine | Nachricht veröffentlichen

## Erstellen des Slack-Connectors
Ein Connector kann innerhalb einer Logik-App oder direkt über den Azure Marketplace erstellt werden. So erstellen Sie einen Connector aus dem Marketplace:

1. Wählen Sie im Azure-Startmenü **Marketplace** aus.
2. Wählen Sie **API-Apps** aus, und suchen Sie nach "Slack-Connector".
3. Geben Sie den Namen, den App Service-Plan und andere Eigenschaften ein: ![][1] 

4. Klicken Sie auf **Erstellen**.

## Verwenden des Connectors als Aktion in Ihrer Logik-App

> [AZURE.IMPORTANT] Die Connectors und Logik-Apps müssen immer in der gleichen Ressourcengruppe erstellt werden.

Nachdem der Slack-Connector erstellt wurde, können Sie ihn als Aktion Ihrer Logik-App hinzufügen:

1.	Öffnen Sie in der Logik-App **Trigger und Aktionen**. [Erstellen einer neuen Logik-App](app-service-logic-create-a-logic-app.md)

2.	Der Slack-Connector wird im Katalog auf der rechten Seite aufgeführt:  
![][2]

3.	Wählen Sie den Slack-Connector aus, den Sie erstellt haben, um ihn automatisch Ihrer Logik-App hinzuzufügen.
4.	Wählen Sie **Autorisieren** aus. Melden Sie sich bei Ihrem Slack-Konto an. Am Ende werden Sie aufgefordert, dem Connector die Zugriffsberechtigung für Ihr Slack-Konto zu erteilen. Wählen Sie **Autorisieren** aus:

![][3]
![][4]
![][5]
![][6]
	
5.	Jetzt können den Slack-Connector im Workflow verwenden. Die Aktion "Nachricht veröffentlichen" ist verfügbar: 
![][7]


Sehen wir uns die Benutzeroberfläche von "Nachricht veröffentlichen" an. Sie können diese Aktion zum Veröffentlichen einer Nachricht über einen beliebigen Slack-Kanal verwenden: 
![][8]

Konfigurieren Sie die Eingabeeigenschaften für die Aktion "Nachricht veröffentlichen" wie folgt:

Eigenschaft | Beschreibung
--- | ---
Text | Geben Sie den Text der zu veröffentlichenden Nachricht ein.
Kanalname | Geben Sie den Slack-Kanal ein, über den diese Nachricht veröffentlicht wird. Wenn der Kanal nicht eingegeben wird, wird die Nachricht für "#general" veröffentlicht.
Erweiterte Eigenschaften | **Bot-Benutzername**: Name des Bots, der für diese Nachricht verwendet wird. Die Nachricht wird als "Bot" veröffentlicht, wenn diese Angabe nicht erfolgt.<p><p>**Symbol-URL** – URL zu einem Bild, das als Symbol für diese Nachricht verwendet werden soll.<p><p>**Emoticon** – Emoticon, das als Symbol für diese Nachricht verwendet werden soll. Diese Eigenschaft überschreibt die "Symbol-URL"-Eigenschaft.


Für den Slack-Connector stehen REST-APIs zur Verfügung, sodass Sie den Connector außerhalb einer Logik-App verwenden können. Öffnen Sie den Slack-Connector, und wählen Sie **API-Definition** aus: 
![][9]


## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [Verwalten und Überwachen integrierter API-Apps und Connectors](app-service-logic-monitor-your-connectors.md).


<!-- Image reference -->
[1]: ./media/app-service-logic-connector-slack/img1.PNG
[2]: ./media/app-service-logic-connector-slack/img2.PNG
[3]: ./media/app-service-logic-connector-slack/img3.PNG
[4]: ./media/app-service-logic-connector-slack/img4.PNG
[5]: ./media/app-service-logic-connector-slack/img5.PNG
[6]: ./media/app-service-logic-connector-slack/img6.PNG
[7]: ./media/app-service-logic-connector-slack/img7.PNG
[8]: ./media/app-service-logic-connector-slack/img8.PNG
[9]: ./media/app-service-logic-connector-slack/img9.PNG

<!---HONumber=AcomDC_0323_2016-->
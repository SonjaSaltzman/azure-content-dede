<properties 
   pageTitle="Verwendung des Warten-Connectors in Logik-Apps | Microsoft Azure App Service" 
   description="Erstellen und Konfigurieren des Warten-Connectors oder einer API-App und Verwenden in einer Logik-App in Azure App Service" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajeshramabathiran" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="02/10/2016"
   ms.author="rajram"/>

# Erste Schritte mit dem Warten-Connector und das Hinzufügen zur Logik-App
>[AZURE.NOTE] Diese Version des Artikels gilt für die Logik-Apps-Schemaversion 2014-12-01-preview.

Der Warten-Connector ermöglicht einer Anwendung, die Ausführung für einen bestimmten Zeitraum oder bis zum Eintreten einer angegebenen Uhrzeit zu verzögern. Sie können den Warten-Connector dem geschäftlichen Workflow hinzufügen und Daten im Rahmen dieses Workflows in einer Logik-App verarbeiten. Bei Verwendung in einer Logik-App kann dieser Connector zum Verzögern der Ausführung verwendet werden.

## Verwenden des Warten-Connectors
Zur Verwendung des Warten-Connectors müssen Sie zunächst eine Instanz der Warten-Connector-API-App erstellen. Dies kann entweder inline beim Erstellen einer Logik-App oder durch Auswählen der Warten-Connector-API-App im Azure Marketplace erfolgen.

## Verwenden des Warten-Connectors auf der Oberfläche des Logik-App-Designers
Der Warten-Connector kann als eine Aktion verwendet werden. Er besitzt keine Trigger.

### Aktion
- Klicken Sie im rechten Bereich auf den Warten-Connector: ![Liste der Aktionen][1]
- Der Warten-Connector unterstützt zwei Aktionen: 
	- Verzögern
	- Verzögern bis
	 
- Wählen Sie *Verzögern* aus: ![Eingabe verzögern][2]
- Nehmen Sie die Eingaben für die Aktion vor, und konfigurieren Sie sie: ![Konfigurierte Aktion][3]

Parameter|Typ|Beschreibung des Parameters
---|---|---
Dauer In Minuten|Ganze Zahl|Dauer der Verzögerung in Minuten


## Mehr mit Ihrem Connector machen
Nachdem der Connector nun erstellt ist, können Sie ihn mit Logik-App in einem Geschäftsworkflow hinzufügen. Informationen finden Sie unter [Was sind Logik-Apps?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Wenn Sie Azure Logik-Apps ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [Logik-App testen](https://tryappservice.azure.com/?appservice=logic) sofort kostenlos eine kurzlebige Starter-Logik-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Anzeigen der Swagger-REST-API-Referenz unter [Referenz zu Connectors und API-Apps](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Sie können auch Leistungsstatistiken überprüfen und die Sicherheit zum Connector steuern. Informationen finden Sie unter [API-Apps und Connector verwalten und überwachen](../app-service-api/app-service-api-manage-in-portal.md).

<!--References -->
[1]: ./media/app-service-logic-wait/ListOfActions.PNG
[2]: ./media/app-service-logic-wait/DelayInput.PNG
[3]: ./media/app-service-logic-wait/ActionConfigured.PNG

<!---HONumber=AcomDC_0224_2016-->
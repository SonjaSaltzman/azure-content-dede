<properties
	pageTitle="Problembehandlung bei Fehlern wie „Die Datenbank auf dem Server ist zurzeit nicht verfügbar“ für Azure SQL-Datenbank"
	description="Schritte für Azure SQL-Datenbank zum Bestimmen und Beheben von Verbindungsfehlern."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/12/2016"
	ms.author="daleche"/>

# Problembehandlung bei Fehlern wie „Die Datenbank auf dem Server ist zurzeit nicht verfügbar. Wiederholen Sie den Verbindungsversuch später“ und anderen Verbindungsfehlern
„Die Datenbank <dbname> auf dem Server <servername> ist zurzeit nicht verfügbar....“ ist der häufigste vorübergehende Verbindungsfehler für Azure SQL-Datenbank. Vorübergehende Verbindungsfehler werden normalerweise durch ein geplantes Ereignis (z. B. ein Softwareupgrade) oder ein ungeplantes Ereignis (z. B. einen Prozessabsturz) verursacht. Diese sind im Allgemeinen kurzlebig und dauern zwischen wenigen Sekunden und höchstens einer Minute. Wenn ein anderer Fehler auftritt, untersuchen Sie die [Fehlermeldung](sql-database-develop-error-messages.md) auf Hinweise zur Ursache. Bestimmen Sie, ob das Problem vorübergehend oder dauerhaft ist, und folgen Sie der Anleitung in diesem Thema.

## Schritte zum Beheben vorübergehender Verbindungsprobleme
1.	Informationen zu bekannten Ausfällen finden Sie im [Microsoft Azure-Service-Dashboard](https://azure.microsoft.com/status).
2.	Stellen Sie sicher, dass Ihre App Wiederholungslogik verwendet. Informationen zu allgemeinen Wiederholungsstrategien finden Sie unter [Verbindungsprobleme](sql-database-connectivity-issues.md) und [Bewährte Methoden und Entwurfsrichtlinien](sql-database-connect-central-recommendations.md). Untersuchen Sie anschließend [Codebeispiele](sql-database-develop-quick-start-client-code-samples.md) auf Details.
3.	Sobald eine Datenbank sich ihren Ressourcenbegrenzungen nähert, kann dies wie ein vorübergehendes Verbindungsproblem aussehen. Siehe [Problembehandlung bei Leistungsproblemen](sql-database-troubleshoot-performance.md).
4.	Wenn die Verbindungsprobleme anhalten, stellen Sie eine Azure-Supportanfrage, indem Sie auf der Website des [Azure-Supports](https://azure.microsoft.com/support/options) die Option **Support erhalten** auswählen.

## Schritte zum Beheben dauerhafter Verbindungsprobleme
Wenn die App überhaupt keine Verbindung herstellen kann, ist der Grund meist die IP- und Firewallkonfiguration. Dies kann die Neukonfiguration des Netzwerks auf Clientseite (z. B. eine neue IP-Adresse oder ein neuer Proxy) einschließen. Falsch geschriebene Verbindungsparameter, z. B. in der Verbindungszeichenfolge, sind auch nicht unüblich.

1.	Richten Sie [Firewallregeln](sql-database-configure-firewall-settings.md) so ein, dass die IP-Adresse des Clients zugelassen wird.
2.	Stellen Sie für alle Firewalls zwischen Client und Internet sicher, dass Port 1433 für ausgehende Verbindungen geöffnet ist.
3.	Überprüfen Sie die Verbindungszeichenfolge und andere Verbindungseinstellungen. Siehe im Thema [Verbindungsprobleme](sql-database-connectivity-issues.md) den Abschnitt „Verbindungszeichenfolge“.
4.	Überprüfen Sie im Dashboard den Dienststatus. Wenn Sie glauben, dass es sich um einen regionalen Ausfall handelt, finden Sie unter [Wiederherstellen nach einem Ausfall](sql-database-disaster-recovery.md) Schritte zum Wiederherstellen in einer neuen Region.
5.	Wenn die Verbindungsprobleme anhalten, stellen Sie eine Azure-Supportanfrage, indem Sie auf der Website des [Azure-Supports](https://azure.microsoft.com/support/options) die Option **Support erhalten** auswählen.

<!---HONumber=AcomDC_0218_2016-->
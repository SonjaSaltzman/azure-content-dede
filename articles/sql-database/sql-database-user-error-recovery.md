<properties 
   pageTitle="Wiederherstellung der SQL-Datenbank nach Benutzerfehler" 
   description="Erfahren Sie, wie Sie die Datenbankwiederherstellung nach einem Benutzerfehler, unbeabsichtigter Datenbeschädigungen oder bei einer gelöschten Datenbank mithilfe der Zeitpunktwiederherstellungsfunktion (PITR) von Azure SQL-Datenbank durchführen." 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="02/09/2016"
   ms.author="elfish"/>

# Wiederherstellen einer Azure-SQL-Datenbank nach einem Benutzerfehler

Eine Azure SQL-Datenbank bietet zwei Kernfunktionen zur Wiederherstellung nach einem Benutzerfehlern oder unbeabsichtigter Datenänderung.

- Zeitpunktwiederherstellung 
- Wiederherstellen einer gelöschten Datenbank

Weitere Informationen zu diesen Funktionen finden Sie in diesem [Blogbeitrag](https://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/).

Eine Azure-SQL-Datenbank wird immer in eine neue Datenbank wiederhergestellt. Diese Wiederherstellungsfunktionen sind für alle Basic-, Standard- und Premium-Datenbanken verfügbar.

##Zeitpunktwiederherstellung

Bei einem Benutzerfehler oder unbeabsichtigter Datenänderung kann die Zeitpunktwiederherstellung verwendet werden, um die Datenbank bis zu einem beliebigen Zeitpunkt innerhalb der Datenbankaufbewahrungsdauer wiederherzustellen.

Basic-Datenbanken haben eine Aufbewahrungsdauer von 7 Tagen, Standard-Datenbanken eine Aufbewahrungsdauer von 14 Tagen, und Premium-Datenbanken eine Aufbewahrungsdauer von 35 Tagen. Weitere Informationen zur Datenbankaufbewahrungsdauer finden Sie unter [Übersicht über die Geschäftskontinuität](sql-database-business-continuity.md).

> [AZURE.NOTE] Beim Wiederherstellen einer Datenbank wird eine neue Datenbank erstellt. Es muss sichergestellt werden, dass der Server, auf dem die Wiederherstellung erfolgt, über ausreichend DTU-Kapazität für die neue Datenbank verfügt. [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/), um dieses Kontingent zu erhöhen.

###Azure-Portal
> [AZURE.NOTE] Für Datenbanken in Pools für elastische Datenbanken unterstützt das Azure-Portal nur die Point-in-Time-Wiederherstellung im selben Pool. Wenn Sie die Point-in-Time-Wiederherstellung für eine Datenbank als eigenständige Datenbank durchführen möchten, verwenden Sie die REST-API.

Führen Sie die folgenden Schritte aus, um die Point-in-Time-Wiederherstellung im Azure-Portal zu verwenden.

1. Melden Sie sich beim [Azure-Portal](https://portal.Azure.com) an.
2. Wählen Sie auf der linken Bildschirmseite **DURCHSUCHEN** und dann **SQL-Datenbanken** aus.
3. Navigieren Sie zu Ihrer Datenbank, und wählen Sie sie aus.
4. Wählen Sie oben im Blatt der Datenbank **Wiederherstellen** aus.
5. Geben Sie einen Datenbanknamen und einen Zeitpunkt an, und klicken Sie dann auf **Erstellen**.
6. Der Datenbank-Wiederherstellungsvorgang beginnt und kann mithilfe von **BENACHRICHTIGUNGEN** auf der linken Bildschirmseite überwacht werden.

###PowerShell
Verwenden Sie PowerShell für die programmgesteuerte Ausführung einer Point-in-Time-Wiederherstellung mit dem Cmdlet [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Ausführliche Anleitungen [finden Sie im Video zu diesem Verfahren](https://azure.microsoft.com/documentation/videos/restore-a-sql-database-using-point-in-time-restore-with-microsoft-azure-powershell/).

> [AZURE.IMPORTANT] Dieser Artikel enthält Befehle für Azure PowerShell-Versionen *vor* Version 1.0. Sie können Ihre Version von Azure PowerShell mit dem Befehl **Get-Module azure | format-table version** überprüfen.

		$Database = Get-AzureSqlDatabase -ServerName "YourServerName" –DatabaseName “YourDatabaseName”
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceDatabase $Database –TargetDatabaseName “NewDatabaseName” –PointInTime “2015-01-01 06:00:00”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 

###REST-API 
Verwenden Sie REST für eine programmatische Durchführung der Datenbankwiederherstellung. Erstellen Sie dazu die Wiederherstellungsanforderung über den Vorgang [Erstellen einer Datenbank](https://msdn.microsoft.com/library/azure/mt163685.aspx), und geben Sie als **Erstellungsmodus** **PointInTimeRestore** an.

##Wiederherstellen einer gelöschten Datenbank
Falls eine Datenbank gelöscht wird, bietet Ihnen die Azure-SQL-Datenbank die Möglichkeit, die gelöschte Datenbank zum Zeitpunkt des Löschens wiederherzustellen. Die Azure-SQL-Datenbank speichert die gelöschte Datenbanksicherung während der Aufbewahrungsdauer der Datenbank.

Die Aufbewahrungsdauer einer gelöschten Datenbank wird anhand der Dienstebene der Datenbank bestimmt, während sie vorhanden war, oder der Anzahl der Tage, während denen die Datenbank vorhanden ist, je nachdem, welcher Wert kleiner ist. Weitere Informationen zur Datenbankaufbewahrungsdauer finden Sie unter [Geschäftskontinuität – Überblick](sql-database-business-continuity.md).

> [AZURE.NOTE] Beim Wiederherstellen einer Datenbank wird eine neue Datenbank erstellt. Es muss sichergestellt werden, dass der Server, auf dem die Wiederherstellung erfolgt, über ausreichend DTU-Kapazität für die neue Datenbank verfügt. [Wenden Sie sich an den Support](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests/), um dieses Kontingent zu erhöhen.

###Azure-Portal
Führen Sie die folgenden Schritte aus, um eine gelöschte Datenbank über das Azure-Portal wiederherzustellen.

1. Melden Sie sich beim [Azure-Portal](https://portal.Azure.com) an.
2. Wählen Sie auf der linken Bildschirmseite **DURCHSUCHEN** und dann **SQL Server** aus.
3. Navigieren Sie zu Ihrem Server, und wählen Sie ihn aus.
4. Scrollen Sie auf dem Blatt für Ihren Server nach unten zu **Vorgänge**, und klicken Sie auf die Kachel **Gelöschte Datenbanken**.
5. Klicken Sie auf die Datenbank, die Sie wiederherstellen möchten.
6. Geben Sie einen Datenbanknamen an, und klicken Sie auf **Erstellen**.
7. Der Datenbank-Wiederherstellungsvorgang beginnt und kann mithilfe von **BENACHRICHTIGUNGEN** auf der linken Bildschirmseite überwacht werden.

###PowerShell
Zur Wiederherstellung einer gelöschten Datenbank mithilfe von PowerShell verwenden Sie das Cmdlet [Start-AzureSqlDatabaseRestore](https://msdn.microsoft.com/library/dn720218.aspx?f=255&MSPPError=-2147217396). Ausführliche Anleitungen [finden Sie im Video zu diesem Verfahren](https://azure.microsoft.com/documentation/videos/restore-a-deleted-sql-database-with-microsoft-azure-powershell/).

1. Suchen Sie in der Liste der gelöschten Datenbanken die gelöschte Datenbank und das zugehörige Löschdatum.
		
		Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName"

2. Rufen Sie die gelöschte Datenbank ab, und starten Sie die Wiederherstellung.

		$Database = Get-AzureSqlDatabase -RestorableDropped -ServerName "YourServerName" –DatabaseName “YourDatabaseName” -DeletionDate "1/01/2015 12:00:00 AM""
		$RestoreRequest = Start-AzureSqlDatabaseRestore -SourceRestorableDroppedDatabase $Database –TargetDatabaseName “NewDatabaseName”
		Get-AzureSqlDatabaseOperation –ServerName "YourServerName" –OperationGuid $RestoreRequest.RequestID
		 

###REST-API 
Verwenden Sie REST für eine programmatische Durchführung der Datenbankwiederherstellung.

1.	Listen Sie alle gelöschten wiederherstellbaren Datenbanken mithilfe des Vorgangs [Wiederherstellbare gelöschte Datenbanken auflisten](http://msdn.microsoft.com/library/azure/dn509562.aspx) auf.
	
2.	Rufen Sie die Details der gelöschten Datenbank, die wiederhergestellt werden soll, mithilfe des Vorgangs [Wiederherstellbare gelöschte Datenbank abrufen](http://msdn.microsoft.com/library/azure/dn509574.aspx) ab.

3.	Beginnen Sie die Wiederherstellung mit dem Vorgang [Datenbankwiederherstellungsanforderung erstellen](http://msdn.microsoft.com/library/azure/dn509571.aspx).
	
4.	Verfolgen Sie den Status der Wiederherstellung mithilfe des Vorgangs [Datenbank-Betriebsstatus](http://msdn.microsoft.com/library/azure/dn720371.aspx) nach.

<!---HONumber=AcomDC_0224_2016-->
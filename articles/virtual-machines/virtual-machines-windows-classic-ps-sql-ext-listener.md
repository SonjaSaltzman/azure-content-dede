<properties
	pageTitle="Konfigurieren eines externen Listeners für AlwaysOn-Verfügbarkeitsgruppen | Microsoft Azure"
	description="Dieses Tutorial führt Sie durch die Schritte zum Erstellen eines AlwaysOn-Verfügbarkeitsgruppenlisteners in Azure, der über die öffentliche virtuelle IP-Adresse des zugeordneten Clouddiensts extern zugänglich ist."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="02/03/2016"
	ms.author="jroth" />

# Konfigurieren eines externen Listeners für AlwaysOn-Verfügbarkeitsgruppen in Azure

> [AZURE.SELECTOR]
- [Interner Listener](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Externer Listener](virtual-machines-windows-classic-ps-sql-ext-listener.md)

In diesem Thema erfahren Sie, wie Sie einen Listener für eine AlwaysOn-Verfügbarkeitsgruppe konfigurieren, die extern über das Internet zugänglich ist. Dies wird durch das Zuordnen der **öffentlichen virtuellen IP-Adresse (VIP)** des Clouddiensts zum Listener ermöglicht.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


Ihre Verfügbarkeitsgruppe kann Replikate enthalten, die ausschließlich lokal, ausschließlich in Azure oder sowohl lokal als auch in Azure verfügbar sind (Hybridkonfigurationen). Azure-Replikate können sich innerhalb derselben Region oder in mehreren Regionen befinden, wobei mehrere virtuelle Netzwerke (VNets) verwendet werden. Bei den nachfolgenden Schritten wird davon ausgegangen, dass bereits eine [Verfügbarkeitsgruppe konfiguriert wurde](virtual-machines-windows-classic-portal-sql-availability.md), Sie jedoch noch keinen Listener konfiguriert haben.

## Richtlinien und Einschränkungen für externe Listener

Beachten Sie die folgenden Richtlinien für den Verfügbarkeitsgruppenlistener in Azure, wenn Sie die Bereitstellung mit der öffentlichen VIP-Adresse des Clouddiensts vornehmen:

- Der Verfügbarkeitsgruppenlistener wird unter Windows Server 2008 R2, Windows Server 2012 und Windows Server 2012 R2 unterstützt.

- Die Clientanwendung darf sich nicht in demselben Clouddienst befinden wie Ihre Verfügbarkeitsgruppen-VMs. Azure bietet keine Unterstützung für Direct Server Return, wenn sich Client und Server im selben Clouddienst befinden.

- Standardmäßig zeigen die Schritte in diesem Artikel, wie Sie einen Listener konfigurieren, der die virtuelle IP-Adresse (VIP) des Clouddiensts verwendet. Allerdings ist es möglich, mehrere VIP-Adressen für den Clouddienst zu reservieren und zu erstellen. Daher können Sie anhand der Schritte in diesem Artikel mehrere Listener erstellen, die jeweils einer anderen VIP-Adresse zugeordnet sind. Informationen zum Erstellen von mehreren VIP-Adressen finden Sie unter [Mehrere VIPs pro Clouddienst](load-balancer-multivip.md).

- Wenn Sie einen Listener für eine Hybridumgebung erstellen, muss das lokale Netzwerk zusätzlich zum Standort-zu-Standort-VPN mit dem virtuellen Azure-Netzwerk auch mit dem öffentlichen Internet verbunden sein. Im Azure-Subnetz ist der Verfügbarkeitsgruppenlistener nur über die öffentliche IP-Adresse des jeweiligen Clouddiensts erreichbar.

- Nicht unterstützt wird die Erstellung eines externen Listeners in einem Clouddienst, in dem auch ein interner Listener vorhanden ist, der den internen Load Balancer (ILB) verwendet.

## Festlegen des Zugriffs auf den Listener

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Der Schwerpunkt dieses Artikels liegt auf dem Erstellen eines Listeners, der den **externen Lastenausgleich** verwendet. Einen Listener, der privat für das virtuelle Netzwerk eingesetzt wird, finden Sie in der Version dieses Artikels, in der Schritte zum Einrichten eines [Listeners mit ILB](virtual-machines-windows-classic-ps-sql-int-listener.md) bereitgestellt werden.

## Erstellen von VM-Endpunkten mit Lastenausgleich und Direct Server Return

Der externe Lastenausgleich verwendet die öffentliche virtuelle IP-Adresse des Clouddiensts, der Ihre virtuellen Computer hostet. In diesem Fall müssen Sie den Lastenausgleich also nicht erstellen oder konfigurieren.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Kopieren Sie das unten stehende PowerShell-Skript in einen Text-Editor, und legen Sie die Variablenwerte gemäß Ihrer Umgebung fest (für einige Parameter wurden die Standardwerte angegeben). Wenn Ihre Verfügbarkeitsgruppe sich über mehrere Azure-Regionen erstreckt, müssen Sie das Skript zudem in jedem Datencenter für den Clouddienst und die Knoten ausführen, die sich in diesem Datencenter befinden.

		# Define variables
		$ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
		$AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

		# Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
		ForEach ($node in $AGNodes)
		{
		    Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
		}

1. Nachdem Sie die Variablen festgelegt haben, kopieren Sie das Skript zur Ausführung aus dem Text-Editor in Ihre Azure PowerShell-Sitzung. Wenn die Aufforderung weiterhin >> anzeigt, geben Sie erneut ENTER ein, um die Skriptausführung zu starten.

## Sicherstellen der Installation von KB2854082, falls erforderlich

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## Öffnen der Firewallports in Verfügbarkeitsgruppenknoten

[AZURE.INCLUDE [Firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## Erstellen des Verfügbarkeitsgruppenlisteners

[AZURE.INCLUDE [Firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Für den externen Lastenausgleich müssen Sie die öffentliche virtuelle IP-Adresse des Clouddiensts abrufen, der Ihre Replikate enthält. Melden Sie sich beim klassischen Azure-Portal an. Navigieren Sie zu dem Clouddienst, der Ihren virtuellen Verfügbarkeitsgruppencomputer enthält. Öffnen Sie die Ansicht **Dashboard**.

3. Beachten Sie die Adresse unter **Öffentliche virtuelle IP-Adresse (VIP)**. Wenn Ihre Lösung VNets umfasst, wiederholen Sie diesen Schritt für jeden Clouddienst mit einem virtuellen Computer, der ein Replikat hostet.

4. Kopieren Sie auf einem der virtuellen Computer das unten stehende PowerShell-Skript in einen Text-Editor, und legen Sie die Variablen auf die zuvor notierten Werte fest.

		# Define variables
		$ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
		$IPResourceName = "<IPResourceName>" # the IP Address resource name
		$CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

		Import-Module FailoverClusters

		# If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

		# Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
		# cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Nachdem Sie die Variablen festgelegt haben, öffnen Sie ein Windows PowerShell-Fenster mit erhöhten Rechten, und kopieren Sie das Skript aus dem Text-Editor zur Ausführung in Ihre Azure PowerShell-Sitzung. Wenn die Aufforderung weiterhin >> anzeigt, geben Sie erneut ENTER ein, um die Skriptausführung zu starten.

1. Wiederholen Sie diesen Schritt für jeden virtuellen Computer. Mit diesem Skript wird die IP-Adressressource mit der IP-Adresse des Clouddienst konfiguriert. Außerdem werden weitere Parameter wie z. B. der Testport festgelegt. Wenn die IP-Adressressource online geschaltet wird, kann sie auf Abrufvorgänge am Testport reagieren, die vom Endpunkt mit Lastenausgleich ausgehen, der zuvor in diesem Tutorial erstellt wurde.

## Onlineschalten des Listeners

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## Aufgaben nach der Erstellung

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## Testen des Verfügbarkeitsgruppenlisteners (innerhalb desselben VNets)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## Testen des Verfügbarkeitsgruppenlisteners (über das Internet)

Für den Zugriff auf den Listener von außerhalb des virtuellen Netzwerks müssen Sie einen externen/öffentlichen Lastenausgleich (in diesem Thema beschrieben) statt ILB verwenden. ILB ist nur innerhalb desselben VNets zugänglich. In der Verbindungszeichenfolge geben Sie den Namen des Clouddiensts an. Bei einem Clouddienst mit dem Namen *mycloudservice* lautet die sqlcmd-Anweisung beispielsweise wie folgt:

	sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Im Gegensatz zum vorherigen Beispiel muss die SQL-Authentifizierung verwendet werden, da der Aufrufer die Windows-Authentifizierung nicht über das Internet verwenden kann. Weitere Informationen finden Sie unter [AlwaysOn-Verfügbarkeitsgruppe auf einem virtuellen Azure-Computer: Clientkonnektivitätsszenarien](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Stellen Sie bei Verwendung der SQL-Authentifizierung sicher, dass Sie für beide Replikate dieselbe Anmeldung erstellen. Weitere Informationen zur Problembehandlung von Anmeldungen bei Verfügbarkeitsgruppen finden Sie unter [How to map logins or use contained SQL database user to connect to other replicas and map to availability databases](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx) (in englischer Sprache).

Wenn sich die AlwaysOn-Replikate in unterschiedlichen Subnetzen befinden, müssen Clients **MultisubnetFailover=True** in der Verbindungszeichenfolge angeben. Dies führt zu parallelen Verbindungsversuchen mit Replikaten in unterschiedlichen Subnetzen. Beachten Sie, dass zu diesem Szenario eine regionsübergreifende AlwaysOn-Verfügbarkeitsgruppenbereitstellung gehört.

## Nächste Schritte

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]

<!---HONumber=AcomDC_0323_2016-->
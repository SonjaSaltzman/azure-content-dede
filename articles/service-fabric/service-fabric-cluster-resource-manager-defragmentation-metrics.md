<properties
   pageTitle="Proaktives Packen von Metriken | Microsoft Azure"
   description="Eine Übersicht über die Verwendung des proaktiven Packens von Metriken (Defragmentierung) in Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/10/2016"
   ms.author="masnider"/>

# Defragmentierung von Metriken und Lasten in Service Fabric
Der Clusterressourcen-Manager von Service Fabric wird hauptsächlich für den Lastenausgleich in Bezug auf die Verteilung der Last eingesetzt und stellt sicher, dass alle Knoten in gleichem Umfang genutzt werden. Dies ist in der Regel das sicherste und intelligenteste Konzept im Hinblick auf das Überstehen von Ausfällen, da es sicherstellt, dass bei einem Ausfall nicht die Mehrheit einer Workload verloren geht. Der Ressourcen-Manager von Service Fabric unterstützt auch eine andere Strategie, die Defragmentierung genannt wird. Defragmentierung bedeutet im Allgemeinen, dass anstatt zu versuchen, die Nutzung einer Metrik auf den Cluster zu verteilen, wir tatsächlich versuchen sollten, sie zu konsolidieren. Dies konnten wir ohne große Schwierigkeiten implementieren. Anstatt die Bewertung einer Lösung basierend der Minimierung der durchschnittlichen Standardabweichung der Metriklast für eine angegebene Metrik zu erhöhen, senken wir sie (was heißen soll, dass wir mit der Optimierung durch Erhöhung der Abweichung beginnen, anstatt sie zu minimieren). Doch was spricht für eine solche Strategie?

Wenn Sie die Last gleichmäßig auf die Knoten im Cluster verteilen, haben Sie einige der Ressourcen belegt, die die Knoten zu bieten haben. Normalerweise ist das kein Problem, aber mitunter erstellen einige Workloads Dienste, die überaus groß sind und den Großteil der Kapazität in Anspruch nehmen, sodass ca. 75 % bis 95 % der Ressourcen eines Knotens einem einzelnen Dienst fest zugeordnet sind. Das ist kein Problem. Der Ressourcen-Manager erkennt bei der Erstellung, dass der Cluster reorganisiert werden muss, um Platz für diesen großen Workload zu schaffen, doch in der Zwischenzeit muss dieser Workload warten.

Da bei der Planung neuer Workloads meist ein wenig Wartezeit anfällt, können wir, wenn wir nichts anders machen, mitunter die dazugehörigen SLAs unterschreiten, sobald viele Dienste und Zustandsdaten verschoben werden müssen. Nachdem wir aber die Erstellungszeiten in auf tatsächlichen Clusterdaten basierenden Simulationen gemessen hatten, erkannten wir, dass wenn Dienste groß genug waren und der Cluster ziemlich ausgelastet war, sich die Erstellung dieser großen Dienste verlangsamte, und wir dies verbessern konnten, indem wir Defragmentierungsmetriken hinzufügten.

Ebenso wie die Erstellung von und der Zugriff auf Dateien sich verlangsamen kann, wenn die Festplatte eines Computers fragmentiert ist und durch eine Defragmentierung wieder beschleunigt werden kann, können Sie Defragmentierungsmetriken so konfigurieren, dass der Ressourcen-Manager proaktiv versucht, die Last der Dienste auf weniger Knoten zu komprimieren, damit (stets) Platz für große Dienste ist, und diese rasch erstellt werden können. Die meisten Benutzer brauchen dies nicht, da Dienste üblicherweise klein sind (von wegen Microservices!) und es deshalb nicht schwer ist, sie unterzubringen. Doch wenn Sie über große Dienste verfügen und diese schnell erstellen müssen (und willens sind, die anderen Kompromisse in Kauf zu nehmen), dann sind Defragmentierungsmetriken das Richtige für Sie!

## Vor- und Nachteile der Defragmentierung
Was sind die anderen grundlegenden Kompromisse? Es wird empfohlen, vor dem Aktivieren von Defragmentierungsmetriken Ihre Workloads gründlich zu messen. Es folgt eine Kurzübersicht der zu beachtenden Aspekte:

| Vorteile der Defragmentierung | Nachteile der Defragmentierung |
|----------------------|----------------------|
|Ermöglicht eine schnellere Erstellung großer Dienste |	Konzentriert die Last auf weniger Knoten, wodurch sich Konflikte vermehren
|Ermöglicht eine langsamere Datenverschiebung während der Erstellung | Ausfälle können sich auf mehr Dienste auswirken und mehr Wechsel verursachen
|Ermöglicht eine umfassende Beschreibung der Anforderungen und Freigabe von Speicherplatz |	Komplexere allgemeine Konfiguration der Ressourcenverwaltung

Sie können defragmentierte und normale Metriken im selben Cluster kombinieren. Der Ressourcen-Manager versucht sein Möglichstes, Ihnen ein Layout zu bieten, dass die Defragmentierungsmetriken umfassend konsolidiert, während versucht wird, den Rest zu verteilen. Die genauen Ergebnisse, die Sie erzielen, hängen von der Anzahl der Lastenausgleichsmetriken im Vergleich mit der Anzahl der Defragmentierungsmetriken, ihrer Gewichtung usw. ab.

## Konfigurieren von Defragmentierungsmetriken
Das Konfigurieren von Defragmentierungsmetriken ist eine globale Entscheidung im Cluster, und einzelne Metriken können für die Defragmentierung ausgewählt werden:

ClusterManifest.xml

```xml
<Section Name="DefragmentationMetrics">
    <Parameter Name="Disk" Value="true" />
    <Parameter Name="CPU" Value="false" />
</Section>
```

## Nächste Schritte
- Der Clusterressourcen-Manager bietet viele Optionen für die Beschreibung des Clusters. Weitere Informationen hierzu finden Sie in diesem Artikel zum [Beschreiben eines Service Fabric-Clusters](service-fabric-cluster-resource-manager-cluster-description.md).
- Metriken bestimmen, wie der Clusterressourcen-Manager von Service Fabric den Ressourcenverbrauch und die Kapazität im Cluster verwaltet. Weitere Informationen zu Metriken und deren Konfiguration finden Sie in [diesem Artikel](service-fabric-cluster-resource-manager-metrics.md).

[Image1]: ./media/service-fabric-resource-balancer-proactive-metric-packing/PMP.png

<!---HONumber=AcomDC_0316_2016-->
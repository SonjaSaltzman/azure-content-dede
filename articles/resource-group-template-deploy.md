<properties
   pageTitle="Bereitstellen von Ressourcen mit Ressourcen-Manager-Vorlagen | Microsoft Azure"
   services="azure-resource-manager"
   description="Verwenden Sie den Azure-Ressourcen-Manager, um Ressourcen in Azure bereitzustellen. Eine Vorlage ist eine JSON-Datei und kann über das Portal, PowerShell, die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows oder REST verwendet werden."
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# Bereitstellen von Ressourcen mit einer Azure Resource Manager-Vorlage

In diesem Thema wird erläutert, wie Ihre Anwendung mit Azure Resource Manager-Vorlagen in Azure bereitgestellt wird. Es wird gezeigt, wie Sie Ihre Ressourcen mithilfe von Azure PowerShell, der Azure-Befehlszeilenschnittstelle, der REST-API oder des Azure-Portals bereitstellen können.

Wenn Sie eine Anwendungsdefinition mit einer Vorlage bereitstellen, können Sie Parameterwerte angeben, um anzupassen, wie die Ressourcen erstellt werden. Sie geben Werte für diese Parameter entweder inline oder in einer Parameterdatei an.

## Inkrementelle und vollständige Bereitstellungen

Standardmäßig behandelt Ressourcen-Manager Bereitstellungen als inkrementelle Updates für die Ressourcengruppe. Bei der inkrementellen Bereitstellung kann Ressourcen-Manager Folgendes:

- Ressourcen **unverändert lassen**, die in der Ressourcengruppe vorhanden, aber nicht in der Vorlage angegeben sind.
- Ressourcen **hinzufügen**, die in der Vorlage angegeben, jedoch nicht in der Ressourcengruppe vorhanden sind. 
- **das erneute Bereitstellen von Ressourcen unterlassen**, die in der Ressourcengruppe gemäß der in der Vorlage definierten Bedingung vorhanden sind.

Bei der vollständigen Bereitstellung kann Ressourcen-Manager Folgendes:

- Ressourcen **löschen**, die in der Ressourcengruppe vorhanden, aber nicht in der Vorlage angegeben sind.
- Ressourcen **hinzufügen**, die in der Vorlage angegeben, jedoch nicht in der Ressourcengruppe vorhanden sind. 
- **das erneute Bereitstellen von Ressourcen unterlassen**, die in der Ressourcengruppe gemäß der in der Vorlage definierten Bedingung vorhanden sind.
 
Sie geben den Bereitstellungstyp über die Eigenschaft **Mode** an, wie in den Beispielen unten gezeigt.

## Bereitstellen mit PowerShell

1. Melden Sie sich bei Ihrem Azure-Konto an. Nach der Eingabe Ihrer Anmeldeinformationen gibt der Befehl die Informationen zu Ihrem Konto zurück.

        PS C:\> Login-AzureRmAccount

        Evironment : AzureCloud
        Account    : someone@example.com
        ...


2. Wenn Sie über mehrere Abonnements verfügen, geben Sie mit dem Befehl **Select-AzureRmSubscription** die Abonnement-ID ein, die Sie für die Bereitstellung verwenden möchten.

        PS C:\> Select-AzureRmSubscription -SubscriptionID <YourSubscriptionId>

3. Wenn noch keine Ressourcengruppe vorhanden ist, erstellen Sie eine neue Ressourcengruppe mit dem Befehl **New-AzureRmResourceGroup**. Geben Sie den Namen der Ressourcengruppe und des gewünschten Speicherorts ein.

        PS C:\> New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "West US"
   
     Es wird eine Zusammenfassung der neuen Ressourcengruppe zurückgegeben.
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

4. Überprüfen Sie die Bereitstellung vor der Ausführung durch Ausführen des Cmdlets **Test-AzureRmResourceGroupDeployment**. Geben Sie die Parameter beim Testen der Bereitstellung genau so an wie beim Ausführen der Bereitstellung (wie im nächsten Schritt zu sehen).

        PS C:\> Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

5. Führen Sie zum Erstellen einer neuen Bereitstellung für die Ressourcengruppe den Befehl **New-AzureRmResourceGroupDeployment** aus, und geben Sie die erforderlichen Parameter ein. Die Parameter enthalten den Namen der Bereitstellung, den Namen der Ressourcengruppe, den Pfad oder die URL der erstellten Vorlage und alle anderen für Ihr Szenario erforderlichen Parameter. Ohne Angabe des Parameters **Mode** wird der Standardwert **Incremental** verwendet.
   
     Sie haben die folgenden Möglichkeiten zum Angeben der Parameterwerte:
   
     - Verwenden Sie Inlineparameter.

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - Verwenden Sie ein Parameterobjekt.

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - Verwenden Sie eine Parameterdatei. Informationen über die Vorlagendatei finden Sie unter [Parameterdatei](./#parameter-file).

            PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     Wenn die Ressourcengruppe bereitgestellt wurde, wird eine Zusammenfassung der Bereitstellung angezeigt.

        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleResourceGroup
        ProvisioningState : Succeeded
        Timestamp         : 4/14/2015 7:00:27 PM
        Mode              : Incremental
        ...

     Legen Sie zum Ausführen einer vollständigen Bereitstellung **Mode** auf **Complete** fest. Beachten Sie, dass Sie dazu aufgefordert werden, zu bestätigen, dass Sie den „Complete“-Modus verwenden möchten, was das Löschen von Ressourcen umfassen kann.

        PS C:\> New-AzureRmResourceGroupDeployment -Name ExampleDeployment -Mode Complete -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> 
        Confirm
        Are you sure you want to use the complete deployment mode? Resources in the resource group 'ExampleResourceGroup' which are not
        included in the template will be deleted.
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

     Enthält die Vorlage einen Parameter mit einem Namen, der einem der Parameter im Befehl zum Bereitstellen der Vorlage entspricht (z. B. einen Parameter namens **ResourceGroupName** in der Vorlage, der mit dem Parameter **ResourceGroupName** im Cmdlet [New-AzureRmResourceGroupDeployment](https://msdn.microsoft.com/library/azure/mt679003.aspx) identisch ist), werden Sie aufgefordert, einen Wert für einen Parameter mit dem Postfix **FromTemplate** anzugeben (z. B. **ResourceGroupNameFromTemplate**). Im Allgemeinen sollten Sie diese Verwirrung vermeiden, indem Sie Parametern nicht dieselben Namen wie Parametern für Bereitstellungsvorgänge geben.

6. Abrufen von Informationen über Fehler bei der Bereitstellung.

        PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup -Name ExampleDeployment
        
        
### Video

Hierbei handelt es sich um ein Video, in dem die Verwendung von Ressourcen-Manager-Vorlagen mit PowerShell veranschaulicht wird.

[AZURE.VIDEO deploy-an-application-with-azure-resource-manager-template]


## Bereitstellen über die Azure-Befehlszeilenschnittstelle

Wenn Sie die Azure-Befehlszeilenschnittstelle noch nicht mit dem Ressourcen-Manager verwendet haben, finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](xplat-cli-azure-resource-manager.md) weitere Informationen.

1. Melden Sie sich bei Ihrem Azure-Konto an. Nach der Eingabe Ihrer Anmeldeinformationen gibt der Befehl das Ergebnis der Anmeldung zurück.

        azure login
  
        ...
        info:    login command OK

2. Wenn Sie über mehrere Abonnements verfügen, geben Sie die Abonnement-ID ein, die Sie für die Bereitstellung verwenden möchten.

        azure account set <YourSubscriptionNameOrId>

3. Wechseln Sie zum Azure-Ressourcen-Manager-Modul. Der neue Modus wird bestätigt.

        azure config mode arm
   
        info:     New mode is arm

4. Erstellen Sie eine neue Ressourcengruppe, wenn noch keine vorhanden ist. Geben Sie den Namen der Ressourcengruppe und des gewünschten Speicherorts ein. Es wird eine Zusammenfassung der neuen Ressourcengruppe zurückgegeben.

        azure group create -n ExampleResourceGroup -l "West US"
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Überprüfen Sie die Bereitstellung vor der Ausführung durch Ausführen des Befehls **azure group template validate**. Geben Sie die Parameter beim Testen der Bereitstellung genau so an wie beim Ausführen der Bereitstellung (wie im nächsten Schritt zu sehen).

        azure group template vaildate -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup

5. Führen Sie zum Erstellen einer neuen Bereitstellung für die Ressourcengruppe den folgenden Befehl aus, und geben Sie die erforderlichen Parameter ein. Die Parameter enthalten den Namen der Bereitstellung, den Namen der Ressourcengruppe, den Pfad oder die URL der erstellten Vorlage und alle anderen für Ihr Szenario erforderlichen Parameter.
   
     Sie haben die folgenden Möglichkeiten zum Angeben der Parameterwerte:

     - Verwenden Sie Inlineparameter und eine lokale Vorlage. Jeder Parameter weist das Format `"ParameterName": { "value": "ParameterValue" }` auf. Das folgende Beispiel zeigt die Parameter mit Escapezeichen.

            azure group deployment create -f <PathToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - Verwenden Sie Inlineparameter und einen Link zu einer Vorlage.

            azure group deployment create --template-uri <LinkToTemplate> -p "{"ParameterName":{"value":"ParameterValue"}}" -g ExampleResourceGroup -n ExampleDeployment

     - Verwenden Sie eine Parameterdatei. Informationen über die Vorlagendatei finden Sie unter [Parameterdatei](./#parameter-file).
    
            azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Wenn die Ressourcengruppe bereitgestellt wurde, wird eine Zusammenfassung der Bereitstellung angezeigt.
  
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        ...
        info:    group deployment create command OK

     Legen Sie zum Ausführen einer vollständigen Bereitstellung **Mode** auf **Complete** fest.

        azure group deployment create --mode Complete -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

6. Abrufen von Informationen über die aktuelle Bereitstellung.

        azure group log show -l ExampleResourceGroup

7. Abrufen detaillierter Informationen über Fehler bei der Bereitstellung.
      
        azure group log show -l -v ExampleResourceGroup

## Bereitstellen mit der REST-API
1. Legen Sie [allgemeine Parameter und Header](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common) fest, einschließlich Authentifizierungstoken.
2. Erstellen Sie eine neue Ressourcengruppe, wenn noch keine vorhanden ist. Geben Sie Ihre Abonnement-ID, den Namen der neuen Ressourcengruppe und den Speicherort für Ihre Lösung an. Weitere Informationen finden Sie unter [Erstellen einer Ressourcengruppe](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Überprüfen Sie die Bereitstellung vor der Ausführung durch Ausführen des Vorgangs unter [Überprüfen einer Vorlagenbereitstellung](https://msdn.microsoft.com/library/azure/dn790547.aspx). Geben Sie die Parameter beim Testen der Bereitstellung genau so an wie beim Ausführen der Bereitstellung (wie im nächsten Schritt zu sehen).

3. Erstellen einer neuen Ressourcengruppenbereitstellung Geben Sie Ihre Abonnement-ID, den Namen der bereitzustellenden Ressourcengruppe, den Namen der Bereitstellung und den Speicherort der Vorlage an. Informationen über die Vorlagendatei finden Sie unter [Parameterdatei](./#parameter-file). Weitere Informationen über die REST-API zum Erstellen einer Ressourcengruppe finden Sie unter [Erstellen einer Vorlagenbereitstellung](https://msdn.microsoft.com/library/azure/dn790564.aspx). Beachten Sie, dass **Mode** auf **Incremental** festgelegt ist. Legen Sie zum Ausführen einer vollständigen Bereitstellung **Mode** auf **Complete** fest.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",
              }
            }
          }
   
4. Rufen Sie den Status der Vorlagenbereitstellung ab. Weitere Informationen finden Sie unter [Abrufen von Informationen zu einer Vorlagenbereitstellung](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## Bereitstellen mit Visual Studio 2013

Mit Visual Studio können Sie über die Benutzeroberfläche ein Ressourcengruppenprojekt erstellen und in Azure bereitstellen. Wählen Sie den Typ der Ressourcen aus, die in das Projekt aufgenommen werden sollen, und diese Ressourcen werden der Ressourcen-Manager-Vorlage automatisch hinzugefügt. Das Projekt enthält auch ein PowerShell-Skript zum Bereitstellen der Vorlage.

Eine Einführung in die Verwendung von Visual Studio mit Ressourcengruppen finden Sie unter [Erstellen und Bereitstellen von Azure-Ressourcengruppen über Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Bereitstellen mit dem Portal

Wissen Sie was? Jede Anwendung, die Sie über das [Portal](https://portal.azure.com/) erstellt haben, basiert auf einer Azure-Ressourcen-Manager-Vorlage. Erstellen Sie einfach einen virtuellen Computer, ein virtuelles Netzwerk, ein Speicherkonto, einen App Service der eine Datenbank über das Portal, und Sie können die Vorteile des Azure-Ressourcen-Managers ohne zusätzlichen Aufwand nutzen. Klicken Sie einfach auf das Symbol **Neu**, und Sie können direkt mit der Bereitstellung einer Anwendung über den Azure-Ressourcen-Manager beginnen.

![Neu](./media/resource-group-template-deploy/new.png)

Alle Bereitstellungen über das Portal werden vor der Ausführung automatisch überprüft. Weitere Informationen zum Verwenden des Portals mit dem Azure-Ressourcen-Manager finden Sie unter [Verwenden des Azure-Portals zum Verwalten Ihrer Azure-Ressourcen](azure-portal/resource-group-portal.md).


## Parameterdatei

Bei der Verwendung einer Parameterdatei zum Übergeben von Parameterwerten an Ihre Vorlage während der Bereitstellung müssen Sie eine JSON-Datei in einem Format wie im folgenden Beispiel erstellen.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            },
            "adminPassword": {
                "reference": {
                   "keyVault": {
                      "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                   }, 
                   "secretName": "sqlAdminPassword" 
                }   
            }
       }
    }

Die Parameterdatei darf nicht größer als 64 KB sein.

Informationen zum Definieren von Parametern in der Vorlage finden Sie unter [Erstellen von Vorlagen](../resource-group-authoring-templates/#parameters). Ausführliche Informationen zum KeyVault-Verweis zum Übergeben sicherer Werte finden Sie unter [Übergeben sicherer Werte während der Bereitstellung](resource-manager-keyvault-parameter.md).

## Nächste Schritte
- Ein Beispiel für die Bereitstellung von Ressourcen über die .NET-Clientbibliothek finden Sie unter [Bereitstellen von Ressourcen mithilfe von .NET-Bibliotheken und einer Vorlage](virtual-machines/virtual-machines-windows-csharp-template.md).
- Ein ausführliches Beispiel für die Bereitstellung einer Anwendung finden Sie unter [Vorhersagbares Bereitstellen von Microservices in Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Informationen zum Bereitstellen der Lösung in andere Umgebungen finden Sie unter [Entwicklungs- und Testumgebungen in Microsoft Azure](solution-dev-test-environments.md).
- Informationen zu den Abschnitten der Azure-Ressourcen-Manager-Vorlage finden Sie unter [Erstellen von Vorlagen](resource-group-authoring-templates.md).
- Unter [Vorlagenfunktionen](resource-group-template-functions.md) finden Sie eine Liste der Funktionen, die Sie in einer Azure-Ressourcen-Manager-Vorlage verwenden können.

 

<!---HONumber=AcomDC_0323_2016-->
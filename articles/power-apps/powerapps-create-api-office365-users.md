<properties
	pageTitle="Hinzufügen der Office 365-Benutzer-API zu PowerApps Enterprise | Microsoft Azure"
	description="Erstellen oder Konfigurieren einer neuen Office 365-Benutzer-API in der App Service-Umgebung Ihrer Organisation"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="rajeshramabathiran"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="03/03/2016"
   ms.author="litran"/>

# Erstellen einer neuen Office 365-Benutzer-API in PowerApps Enterprise

> [AZURE.SELECTOR]
- [Logik-Apps](../articles/connectors/create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

Fügen Sie die Office 365-Benutzer-API in der App Service-Umgebung Ihrer Organisation (Mandant) hinzu.

## Erstellen der API im Azure-Portal

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) mit Ihrem Geschäftskonto an. Melden Sie sich beispielsweise mit *IhrBenutzername*@*IhrUnternehmen*.com an. Sie werden dann automatisch mit Ihrem Unternehmensabonnement angemeldet.
 
2. Wählen Sie in der Taskleiste die Option **Durchsuchen** aus:  
![][14]

3. Um PowerApps zu finden, können Sie in der Liste scrollen oder *powerapps* eingeben:  
![][15]

4. Wählen Sie in **PowerApps** die Option **Manage APIs** aus:    
![Zu registrierten APIs navigieren][1]

5. Wählen Sie in **Manage APIs** die Option **Add** aus, um die neue API hinzufügen:  
![API hinzufügen][2]

6. Geben Sie einen beschreibenden **Namen** für Ihre API ein.  
	
7. Wählen Sie in **Source** die Option **Available APIs**, um die vorgefertigten APIs auszuwählen, und wählen Sie dann **Office 365-Benutzer** aus:  
![Office 365-Benutzer-API auswählen][3]

8. Wählen Sie **Einstellungen – Erforderliche Einstellungen konfigurieren** aus:  
![Einstellungen für die Office 365-Benutzer-API konfigurieren][4]

9. Geben Sie die *Client-ID* und den *geheimen App-Schlüssel* Ihrer Azure Active Directory-Anwendung (AAD) für Office 365 ein. Wenn Sie nicht über diese Daten verfügen, finden Sie weiter unten im Abschnitt „Registrieren einer AAD-App zur Verwendung mit PowerApps“ Informationen zum Erstellen der benötigten Werte für die ID und den geheimen Schlüssel.  

	> [AZURE.IMPORTANT] Speichern Sie die **Umleitungs-URL**. Möglicherweise benötigen Sie diesen Wert an späterer Stelle in diesem Thema.

10. Wählen Sie **OK** aus, um die Schritte abzuschließen.

Ihrer App Service-Umgebung wird dann eine neue Office 365-Benutzer-API hinzugefügt.

## Optional: Registrieren einer AAD-App zur Verwendung mit der Office 365-Benutzer-API in PowerApps

Wenn Sie über keine vorhandene AAD-App mit den Werten für den Schlüssel und den geheimen Schlüssel verfügen, führen Sie die folgenden Schritte zum Erstellen der Anwendung aus, um so die benötigten Werte zu erhalten.

1. Öffnen Sie das [Azure-Portal][5].

2. Wählen Sie **Durchsuchen** und dann **Active Directory** aus:

	> [AZURE.NOTE] Damit wird Active Directory im klassischen Azure-Portal geöffnet.

3. Wählen Sie den Mandantennamen Ihrer Organisation aus:  
![Azure Active Directory starten][6]

4. Klicken Sie auf die Registerkarte **Anwendungen**, und wählen Sie **Hinzufügen** aus:  
![AAD-Mandanten-Anwendungen][7]

5. Auf der Seite **Anwendung hinzufügen**:  

	1. Geben Sie einen **Namen** für Ihre Anwendung ein.  
	2. Lassen Sie als Anwendungstyp **Web** ausgewählt.  
	3. Wählen Sie **Weiter**.  

	![AAD-Anwendung hinzufügen – App-Info][8]

6. Unter **App-Eigenschaften**:  

	1. Geben Sie unter **ANMELDE-URL** die Anmelde-URL Ihrer Anwendung ein. Da Sie die Authentifizierung mit AAD für PowerApps durchführen, legen Sie die Anmelde-URL auf \__https://login.windows.net_ fest.  
	2. Geben Sie einen gültigen **APP-ID-URI** für Ihre App ein.  
	3. Klicken Sie auf **OK**.  

	![AAD-Anwendung hinzufügen – App-Eigenschaften][9]

7. Nach erfolgreichem Abschluss werden Sie zu der neuen AAD-App weitergeleitet. Wählen Sie **Konfigurieren** aus:  
![Contoso-AAD-App][10]

8. Legen Sie die **Antwort-URL** im Abschnitt _OAuth 2_ auf die Umleitungs-URL fest, die Sie beim Hinzufügen der neuen Office 365-Benutzer-API im Azure-Portal erhalten haben (weiter oben in diesem Thema). Wählen Sie **Anwendung hinzufügen** aus:  
![Contoso-AAD-App konfigurieren][11]

9. Wählen Sie im Fenster **Berechtigungen für andere Anwendungen** die Option **Office 365 Unified API (Vorschau)** und dann **OK**.

10. Auf der Konfigurationsseite können Sie nun sehen, dass _Office 365 Unified API (Vorschau)_ der Liste _Berechtigungen für andere Anwendungen_ hinzugefügt wurde.

11. Wählen Sie **Delegierte Berechtigungen** für _Office 365 Unified API (Vorschau)_, und wählen Sie dann die Berechtigung **Alle grundlegenden Benutzerprofile lesen**.

Eine neue Azure Active Directory-App wird erstellt. Diese App können Sie in der Konfiguration Ihrer Office 365-Benutzer-API im Azure-Portal verwenden.

Einige hilfreiche Informationen zu AAD-Anwendungen finden Sie unter [Wie und warum werden Anwendungen zu Azure AD hinzugefügt?](../active-directory/active-directory-how-applications-are-added.md).

## Informationen zu REST-APIs

Referenz zur [Office 365-Benutzer-REST-API](../connectors/create-api-office365-users.md).

## Zusammenfassung und nächste Schritte
In diesem Thema haben Sie PowerApps Enterprise die Office 365-Benutzer-API hinzugefügt. Als Nächstes können Sie den Zugriff für Benutzer auf die API einrichten, damit sie den Apps der Benutzer hinzugefügt werden kann:

[Hinzufügen einer Verbindung und Einrichten des Zugriffs für Benutzer](powerapps-manage-api-connection-user-access.md)


<!--References-->
[1]: ./media/powerapps-create-api-office365-users/browse-to-registered-apis.PNG
[2]: ./media/powerapps-create-api-office365-users/add-api.PNG
[3]: ./media/powerapps-create-api-office365-users/select-office365-users-api.PNG
[4]: ./media/powerapps-create-api-office365-users/configure-office365-users-api.PNG
[5]: https://portal.azure.com
[6]: ./media/powerapps-create-api-office365-users/launch-aad.PNG
[7]: ./media/powerapps-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/powerapps-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/powerapps-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/powerapps-create-api-office365-users/contoso-aad-app-configure.PNG

<!-----HONumber=AcomDC_0309_2016-->
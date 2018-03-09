---
title: "Přehled nastavení podmíněného přístupu služby Azure Active Directory | Microsoft Docs"
description: "Získáte přehled o podporovaná nastavení v zásadách podmíněného přístupu Azure Active Directory."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2018
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 66e3207898d6ad8ca9af2b969942e38321c191a5
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Odkaz nastavení podmíněného přístupu Azure Active Directory

Můžete použít [podmíněného přístupu Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) k řízení způsobu oprávněným uživatelům můžete přístup k prostředkům.   

Tento článek obsahuje informace o podporu pro následující možnosti konfigurace v zásadách podmíněného přístupu: 

- Přiřazení cloudové aplikace

- Podmínka platformy zařízení 

- Stav aplikace klienta

- Požadavek schválené klientské aplikace


Pokud to není informace, které hledáte, uveďte poznámky na konci tohoto článku.

## <a name="cloud-apps-assignments"></a>Přiřazení cloudové aplikace

Pomocí zásady podmíněného přístupu můžete řídit přístup uživatelům vaší [cloudových aplikací](active-directory-conditional-access-conditions.md#cloud-apps). Pokud budete konfigurovat zásadu podmíněného přístupu, je nutné vybrat alespoň jeden cloudové aplikace. 

![Vyberte zásady pro cloudové aplikace](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Microsoft cloudové aplikace

Zásady podmíněného přístupu můžete přiřadit následující cloudových aplikací od společnosti Microsoft:

- Azure Information Protection – [Další informace](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- Azure RemoteApp

- Microsoft Dynamics 365

- Microsoft Office 365 Yammer

- Microsoft Office 365 Exchange Online

- Aplikace Microsoft Office 365 SharePoint Online (zahrnuje OneDrive pro firmy a Projectu Online)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Microsoft Teams


### <a name="other-applications"></a>Ostatní aplikace 

Kromě cloudových aplikacích Microsoft můžete přiřadit zásady podmíněného přístupu pro následující typy cloudových aplikací:

- Aplikace Azure AD připojení

- Předem integrovaných federované aplikace software jako služba (SaaS)

- Aplikace, které používají heslo jednotné přihlašování (SSO)

- Obchodní aplikace

- Aplikace, které používají Azure AD Application Proxy


## <a name="device-platform-condition"></a>Podmínka platformy zařízení

V zásadách podmíněného přístupu můžete nakonfigurovat podmínky platformy zařízení ke svázání zásady tak, aby operační systém na klientovi. Podmíněný přístup pro Azure AD podporuje tyto platformy zařízení:

- Android

- iOS

- telefon se systémem Windows

- Windows

- macOS


![Tie – zásady přístupu do klientského operačního systému](./media/active-directory-conditional-access-technical-reference/41.png)





## <a name="client-apps-condition"></a>Stav aplikace klienta 

V zásadách podmíněného přístupu můžete nakonfigurovat [klientské aplikace](active-directory-conditional-access-conditions.md#client-apps) podmínku ke svázání zásady tak, aby klientskou aplikaci, která iniciovala pokus o přístup. Nastavení klienta aplikace podmínku, která má udělit nebo blokovat přístup, když je proveden pokus o přístup z následujících typů klientských aplikací:

- Prohlížeč
- Mobilní aplikace a aplikace klasické pracovní plochy

![Řízení přístupu pro klientské aplikace](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Podporované prohlížeče 

V zásadách podmíněného přístupu můžete vybrat **prohlížeče** jako klientskou aplikaci.

![Řízení přístupu pro podporované prohlížeče](./media/active-directory-conditional-access-technical-reference/05.png)

Toto nastavení funguje u všech prohlížečů. Splňovat zásady pro zařízení, jako je zařízení kompatibilní s požadavek, následující operační systémy a prohlížeče jsou však podporovány:


| Operační systém                     | Prohlížeče                            | Podpora     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, okraji a pro Chrome     | ![Zaškrtnout][1] |
| Windows 8 / 8.1        | Internet Explorer, Chrome           | ![Zaškrtnout][1] |
| Windows 7              | Internet Explorer, Chrome           | ![Zaškrtnout][1] |
| iOS                    | Safari, Intune Managed Browser      | ![Zaškrtnout][1] |
| Android                | Chrome, Intune Managed Browser      | ![Zaškrtnout][1] |
| telefon se systémem Windows          | Internet Explorer, Edge             | ![Zaškrtnout][1] |
| Windows Server 2016    | Internet Explorer, Edge             | ![Zaškrtnout][1] |
| Windows Server 2016    | Chrome                              | Již brzy |
| Windows Server 2012 R2 | Internet Explorer, Chrome           | ![Zaškrtnout][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome           | ![Zaškrtnout][1] |
| macOS                  | Chrome, Safari                      | ![Zaškrtnout][1] |


> [!NOTE]
> Pro podporu Chrome ve Windows 10 Creators Update (verze 1703) nebo novější, nainstalovat [toto rozšíření](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).<br>
> Pro podporu Chrome ve Windows 8.1 a 7, vytvořte následující klíč registru v **HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls** <br>
> Název: 1<br>
> Typ: REG_SZ (String)<br>
> Data: {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}}

Ověřování zařízení umožňuje zařízení identifikovat a ověřovat s zásadu podporují tyto prohlížeče. Kontrola zařízení selže, pokud je v prohlížeči spuštění v privátním režimu. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Podporovaná mobilní aplikace a klienti vzdálené plochy

V zásadách podmíněného přístupu můžete vybrat **mobilní aplikace a klienti vzdálené plochy** jako klientskou aplikaci.


![Řízení přístupu pro podporovaných mobilních aplikací nebo klienti vzdálené plochy](./media/active-directory-conditional-access-technical-reference/06.png)


Toto nastavení má vliv na pokusů o přístup provedených z následující mobilní aplikace a klienti vzdálené plochy: 


|Klientské aplikace|Cílová služba|Platforma|
|---|---|---|
|Vzdálené aplikace Azure|Služba vzdálené aplikace Azure|Windows 10, Windows 8.1, Windows 7, iOS, Android a Mac OS X|
|Dynamics CRM app|Dynamics CRM|Windows 10, Windows 8.1, Windows 7, iOS a Android|
|E-mailu, kalendáři nebo osoby aplikace Outlook 2016, aplikace Outlook 2013 (s moderní ověřování)|Office 365 Exchange Online|Windows 10|
|MFA a umístění zásady pro aplikace. Zásady zařízení na základě nejsou podporovány. |Všechny služby app service pro Moje aplikace|Android a iOS|
|Microsoft týmy služby – tato volba určuje všechny služby, které podporují Teams společnosti Microsoft a všechny jeho klientských aplikací – Windows Desktop, iOS, Android, webové části a webového klienta|Microsoft Teams|Windows 7, Windows 10, Windows 8.1, iOS, Android a systému macOS |
|Aplikace Office 2016, Office 2013 (s moderní ověřování), Onedrivu synchronizovat klienta (viz [poznámky](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|Office 365 Sharepointu Online|Windows 8.1, Windows 7|
|Aplikace Office 2016, Office univerzální aplikace, Office 2013 (s moderní ověřování), OneDrive synchronizace klienta (v tématu [poznámky k](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), podporu skupiny Office je plánované v budoucnosti, podpora aplikací služby SharePoint je plánované v budoucnosti|Office 365 Sharepointu Online|Windows 10|
|Office 2016 pro systému macOS (Word, Excel, PowerPoint, OneNote pouze). OneDrive pro firmy podporu plánované v budoucnosti|Office 365 Sharepointu Online|Mac OS X|
|Mobilní aplikace Office|Office 365 Sharepointu Online|Android, iOS|
|Aplikace Yammer Office|Office 365 Yammer|Windows 10, iOS, Android|
|Outlook 2016 (Office pro systému macOS)|Office 365 Exchange Online|Mac OS X|
|Outlook 2016, aplikace Outlook 2013 (s moderní ověřování), Skype pro firmy (s moderní ověřování)|Office 365 Exchange Online|Windows 8.1, Windows 7|
|Mobilní aplikace Outlook|Office 365 Exchange Online|Android, iOS|
|Aplikaci PowerBI|Služba PowerBI|Windows 10, Windows 8.1, Windows 7 a iOS|
|Skype pro firmy|Office 365 Exchange Online|Android, IOS |
|Visual Studio Team Services aplikace|Visual Studio Team Services|Windows 10, Windows 8.1, Windows 7, iOS a Android|


## <a name="approved-client-app-requirement"></a>Požadavek schválené klientské aplikace 

V zásadách podmíněného přístupu můžete vyžadovat, aby přístup pokusí vybrané cloudové aplikace musí být vytvořen ze schválených klienta aplikace. 

![Řízení přístupu pro schválené klientské aplikace](./media/active-directory-conditional-access-technical-reference/21.png)

Toto nastavení platí pro následující aplikace klienta:


- Microsoft Azure Information Protection
- Microsoft Excel
- Microsoft Kaizala 
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype pro firmy
- Microsoft StaffHub
- Microsoft Teams
- Microsoft Visio
- Microsoft Word



**Poznámky**

- Schválené klientské aplikace podporují funkce správy mobilních aplikací Intune.

- **Vyžadují schválené klientskou aplikaci** požadavek:

    - Podporuje jenom iOS a Android pro [podmínku platformy zařízení](#device-platforms-condition).

    - Nepodporuje **prohlížeče** možnost [klienta aplikace podmínku](#supported-browsers).
    
    - Nahrazuje **mobilní aplikace a klienti vzdálené plochy** možnost [klienta aplikace podmínku](#supported-mobile-apps-and-desktop-clients) když je tato možnost vybraná.


## <a name="next-steps"></a>Další postup

- Přehled podmíněného přístupu najdete v tématu [podmíněného přístupu v Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu v prostředí, najdete v článku [doporučené postupy pro podmíněný přístup v Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



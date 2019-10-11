---
title: Odkaz na nastavení Azure Active Directory podmíněného přístupu | Microsoft Docs
description: Získejte přehled o podporovaných nastaveních ve Azure Active Directory zásadách podmíněného přístupu.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 834b13c512bca1b7c43c3c8e93a72383a82db198
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274185"
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Odkaz na nastavení Azure Active Directory podmíněného přístupu

Pomocí [podmíněného přístupu Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md) můžete řídit, jak můžou autorizovaní uživatelé přistupovat k vašim prostředkům.

V tomto článku najdete informace o podpoře pro následující možnosti konfigurace v zásadách podmíněného přístupu:

- Přiřazení cloudových aplikací
- Podmínka platformy zařízení
- Podmínka klientských aplikací
- Požadavek na schválenou aplikaci klienta

Pokud se nejedná o informace, které hledáte, ponechte prosím na konci tohoto článku komentář.

## <a name="cloud-apps-assignments"></a>Přiřazení cloudových aplikací

Pomocí zásad podmíněného přístupu můžete řídit, jak uživatelé přistupují k vašim [cloudovým aplikacím](conditions.md#cloud-apps-and-actions). Když konfigurujete zásady podmíněného přístupu, musíte vybrat aspoň jednu cloudovou aplikaci. 

![Vyberte cloudové aplikace pro vaše zásady.](./media/technical-reference/09.png)

### <a name="microsoft-cloud-applications"></a>Cloudové aplikace Microsoftu

Zásady podmíněného přístupu můžete přiřadit k těmto cloudovým aplikacím od Microsoftu:

- Azure Analysis Services
- Azure DevOps
- Azure SQL Database a datový sklad – další [informace](https://docs.microsoft.com/azure/sql-database/sql-database-conditional-access)
- Dynamics CRM Online
- Analýzy Microsoft Application Insights
- Microsoft Azure Information Protection – další [informace](https://docs.microsoft.com/azure/information-protection/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)
- Správa Microsoft Azure – další [informace](https://docs.microsoft.com/azure/role-based-access-control/conditional-access-azure-management)
- Správa předplatného Microsoft Azure
- Microsoft Cloud App Security
- Portál Microsoft Commerce Tools Access Control Portal
- Služba Microsoft Commerce Tools Authentication Service
- Microsoft Flow
- Formuláře Microsoft
- Microsoft Intune
- Registrace Microsoft Intune
- Microsoft Planner
- Microsoft PowerApps
- Hledání ve službě Bing
- Microsoft směn staffhubu
- Microsoft Stream
- Microsoft Teams
- Office 365 Exchange Online
- Office 365 SharePoint Online
- Office 365 Yammer
- Office – Delvu
- Sway pro Office
- Skupiny Outlooku
- Služba Power BI
- Projekt online
- Online Skype pro firmy
- Virtuální privátní síť (VPN)
- Ochrana ATP v programu Windows Defender

### <a name="other-applications"></a>Další aplikace

Kromě cloudových aplikací Microsoftu můžete přiřadit zásady podmíněného přístupu k těmto typům cloudových aplikací:

- Aplikace připojené k Azure AD
- Předem integrovaná aplikace federovaného softwaru jako služby (SaaS)
- Aplikace, které používají jednotné přihlašování pomocí hesla (SSO)
- Obchodní aplikace
- Aplikace, které používají Azure Proxy aplikací služby AD

## <a name="device-platform-condition"></a>Podmínka platformy zařízení

V zásadách podmíněného přístupu můžete nakonfigurovat podmínku pro platformu zařízení, aby zásady provázána s operačním systémem klienta. Podmíněný přístup Azure AD podporuje tyto platformy zařízení:

- Android
- iOS
- Windows Phone
- Windows
- macOS

![Propojení zásad přístupu k operačnímu systému klienta](./media/technical-reference/41.png)

Pokud zablokujete starší ověřování pomocí podmínky **ostatních klientů** , můžete také nastavit podmínku pro platformu zařízení.

## <a name="client-apps-condition"></a>Podmínky klientských aplikací

V rámci zásad podmíněného přístupu můžete nakonfigurovat podmínku pro [klientské aplikace](conditions.md#client-apps) tak, aby zásady provedla pro klientskou aplikaci, která iniciovala pokus o přístup. Nastavte podmínku pro klientské aplikace tak, aby při pokusu o přístup z následujících typů klientských aplikací udělily nebo blokovaly přístup:

- Prohlížeee
- Mobilní aplikace a aplikace klasické pracovní plochy

![Řízení přístupu pro klientské aplikace](./media/technical-reference/03.png)

### <a name="supported-browsers"></a>Podporované prohlížeče

V zásadách podmíněného přístupu můžete jako klientskou aplikaci vybrat možnost **prohlížeče** .

![Řízení přístupu pro podporované prohlížeče](./media/technical-reference/05.png)

Toto nastavení funguje ve všech prohlížečích. Pokud ale chcete splnit zásadu zařízení, třeba požadavky na vyhovující zařízení, podporují se tyto operační systémy a prohlížeče:

| JINÉHO                     | Nimi                                        |
| :--                    | :--                                             |
| Windows 10             | Microsoft Edge, Internet Explorer, Chrome       |
| Windows 8/8,1        | Internet Explorer, Chrome                       |
| Windows 7              | Internet Explorer, Chrome                       |
| iOS                    | Microsoft Edge, Intune Managed Browser, Safari  |
| Android                | Microsoft Edge, Intune Managed Browser, Chrome  |
| Windows Phone          | Microsoft Edge, Internet Explorer               |
| Windows Server 2019    | Microsoft Edge, Internet Explorer, Chrome       |
| Windows Server 2016    | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer                       |
| Windows Server 2008 R2 | Internet Explorer                       |
| macOS                  | Chrome, Safari                                  |


#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Proč se v prohlížeči zobrazí výzva k zadání certifikátu

V systému Windows 7, iOS, Android a macOS Azure AD identifikuje zařízení pomocí klientského certifikátu, který se zřídí při registraci zařízení ve službě Azure AD.  Když se uživatel poprvé přihlásí prostřednictvím prohlížeče, zobrazí se uživateli výzva k výběru certifikátu. Uživatel musí před použitím prohlížeče tento certifikát vybrat.

#### <a name="chrome-support"></a>Podpora Chrome

V případě podpory pro Chrome ve **Windows 10 Creators Update (verze 1703)** nebo novější nainstalujte [rozšíření účtů Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Toto rozšíření se vyžaduje, když zásada podmíněného přístupu vyžaduje podrobnosti konkrétního zařízení.

Pokud chcete toto rozšíření automaticky nasadit do prohlížečů Chrome, vytvořte následující klíč registru:

|    |    |
| --- | --- |
| Cesta | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Name | první |
| Typ | REG_SZ (řetězec) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji; https @ no__t-0//clients2. Google. com/Service/-datum2/CRX |

V případě podpory Chrome v **Windows 8.1 a 7**vytvořte následující klíč registru:

|    |    |
| --- | --- |
| Cesta | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Name | první |
| Typ | REG_SZ (řetězec) |
| Data | {"Pattern": "https://device.login.microsoftonline.com"; "Filter": {"ISSUER": {"CN": "MS-Organization-Access"}}} |

Tyto prohlížeče podporují ověřování zařízení, což umožňuje, aby se zařízení identifikovalo a ověřilo na základě zásad. Pokud je prohlížeč spuštěný v privátním režimu, neproběhne jeho ověření.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Podporované mobilní aplikace a desktopové klienty

V zásadách podmíněného přístupu můžete jako klientskou aplikaci vybrat **mobilní aplikace a klienty klasické pracovní plochy** .

![Řízení přístupu pro podporované mobilní aplikace nebo desktopové klienty](./media/technical-reference/06.png)

Toto nastavení má vliv na pokusy o přístup uskutečněné z následujících mobilních aplikací a klientů klasické pracovní plochy:

| Klientské aplikace | Cílová služba | Platforma |
| --- | --- | --- |
| Aplikace Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS a Android |
| Aplikace Pošta/kalendář/lidé, Outlook 2016, Outlook 2013 (s moderním ověřováním)| Office 365 Exchange Online | Windows 10 |
| Zásady vícefaktorového ověřování a umístění pro aplikace Zásady založené na zařízeních se nepodporují.| Všechny moje aplikace App Service| Android a iOS |
| Microsoft Team Services – řídí všechny služby, které podporují Microsoft teams a všechny jeho klientské aplikace – desktopové aplikace pro Windows, iOS, Android, WP a webový klient. | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android a macOS |
| Aplikace Office 2016, Office 2013 (s moderním ověřováním), klient synchronizace OneDrive (viz [poznámky](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplikace Office 2016, univerzální aplikace Office, Office 2013 (s moderním ověřováním), synchronizační klient OneDrivu (viz [poznámky](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), podpora skupin Office je pro budoucnost plánována. Podpora aplikací SharePoint je pro budoucnost budoucí. | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (jenom Word, Excel, PowerPoint, OneNote) Podpora OneDrivu pro firmy plánovaná pro budoucnost| Office 365 SharePoint Online| macOS|
| Sada Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Mobilní aplikace Office | Office 365 SharePoint Online | Android, iOS |
| Aplikace Office Yammer | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office pro macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (s moderním ověřováním), Skype pro firmy (s moderním ověřováním) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Mobilní aplikace Outlook | Office 365 Exchange Online | Android, iOS |
| Aplikace Power BI | služba Power BI | Windows 10, Windows 8.1, Windows 7, Android a iOS |
| Skype pro firmy | Office 365 Exchange Online| Android, IOS |
| Aplikace Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS a Android |

## <a name="support-for-legacy-authentication"></a>Podpora pro starší verze ověřování

Výběrem **jiných klientů**můžete určit podmínku, která bude mít vliv na aplikace, které používají základní ověřování, s poštovními protokoly, jako jsou IMAP, MAPI, pop, SMTP a starší aplikace Office, které nepoužívají moderní ověřování.  

![Ostatní klienti](./media/technical-reference/11.png)

Další informace najdete v tématu [klientské aplikace](conditions.md#client-apps).

## <a name="approved-client-app-requirement"></a>Požadavek na schválenou aplikaci klienta

V zásadách podmíněného přístupu můžete vyžadovat, aby byl pokus o přístup k vybraným cloudovým aplikacím vytvořen ze schválené klientské aplikace. 

![Řízení přístupu ke schváleným klientským aplikacím](./media/technical-reference/21.png)

Toto nastavení platí pro následující klientské aplikace:

- Microsoft Azure Information Protection
- Rezervace Microsoftu
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Fakturace Microsoftu
- Microsoft Kaizala
- Spouštěč Microsoftu
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Power BI Microsoftu
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype pro firmy
- Microsoft směn staffhubu
- Microsoft Stream
- Microsoft Teams
- Microsoft to-do
- Aplikace Microsoft Visio
- Microsoft Word
- Yammer Microsoftu

**Mark**

- Schválené klientské aplikace podporují funkci správy mobilních aplikací Intune.
- Požadavek na **vyžadování schválené aplikace klienta** :
   - Podporuje jenom [podmínku platformy](#device-platform-condition)iOS a Android pro zařízení.

## <a name="app-protection-policy-requirement"></a>Požadavek na zásady ochrany aplikací 

V rámci zásad podmíněného přístupu můžete vyžadovat, aby se v klientské aplikaci nacházely zásady ochrany aplikací, než bude přístup k vybraným cloudovým aplikacím k dispozici. 

![Řízení přístupu pomocí zásad ochrany aplikací](./media/technical-reference/22.png)

Toto nastavení platí pro následující klientské aplikace:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Mark**

- Aplikace pro zásady ochrany aplikací podporují funkci správy mobilních aplikací Intune s ochranou zásad.
- Požadavky na **zásady pro vyžadování zásad ochrany aplikací** :
    - Podporuje jenom [podmínku platformy](#device-platform-condition)iOS a Android pro zařízení.

## <a name="next-steps"></a>Další kroky

- Přehled podmíněného přístupu najdete [v tématu Co je podmíněný přístup v Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)
- Pokud jste připraveni ke konfiguraci zásad podmíněného přístupu ve vašem prostředí, přečtěte si [Doporučené postupy pro podmíněný přístup v Azure Active Directory](best-practices.md).

<!--Image references-->
[1]: ./media/technical-reference/01.png

---
title: Podmínky v zásadách podmíněného přístupu – Azure Active Directory
description: Jaké jsou podmínky v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d9fe24e4a2b25b1ef3f0da2b1a5e1c0f29251df1
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192230"
---
# <a name="conditional-access-conditions"></a>Podmíněný přístup: podmínky

V rámci zásad podmíněného přístupu může správce využít signály z podmínek, jako je riziko, platforma zařízení nebo umístění, a zvýšit tak jejich rozhodování o zásadách. 

![Definování zásad podmíněného přístupu a určení podmínek](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

K vytvoření podrobných a specifických zásad podmíněného přístupu lze kombinovat více podmínek.

Například při přístupu k citlivé aplikaci může správce kromě dalších ovládacích prvků, jako je Multi-Factor Authentication, přihlašovat informace o riziku z ochrany identity a umístění do jejich rozhodnutí o přístupu.

## <a name="sign-in-risk"></a>Riziko přihlášení

Pro zákazníky s přístupem k [Identity Protection](../identity-protection/overview-identity-protection.md)je možné vyhodnotit riziko přihlášení v rámci zásad podmíněného přístupu. Riziko přihlášení představuje pravděpodobnost, že daný požadavek na ověření není autorizovaný vlastníkem identity. Další informace o riziku při přihlašování najdete v článcích, [co je to riziko](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) a [Postupy: konfigurace a povolení zásad rizik](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Platformy zařízení

Platforma zařízení je charakterizována operačním systémem, který běží na zařízení. Azure AD identifikuje platformu pomocí informací poskytovaných zařízením, jako jsou řetězce uživatelských agentů. Vzhledem k tomu, že je možné upravit řetězce uživatelského agenta, tyto informace jsou neověřené. Platforma zařízení by měla být používána společně s Microsoft Intune zásadami dodržování předpisů zařízením nebo jako součást příkazu Block. Ve výchozím nastavení platí pro všechny platformy zařízení.

Podmíněný přístup Azure AD podporuje tyto platformy zařízení:

- Android
- iOS
- telefon se systémem Windows
- Windows
- macOS

Pokud zablokujete starší ověřování pomocí podmínky **ostatních klientů** , můžete také nastavit podmínku pro platformu zařízení.

## <a name="locations"></a>Umístění

Při konfiguraci umístění jako podmínky se můžou organizace rozhodnout zahrnout nebo vyloučit umístění. Tato pojmenovaná umístění můžou zahrnovat informace o veřejné síti IPv4, zemi nebo oblasti nebo dokonce neznámé oblasti, které nejsou namapované na konkrétní země nebo oblasti. Pouze rozsahy IP adres lze označit jako důvěryhodné umístění.

Při zahrnutí **libovolného umístění**Tato možnost zahrnuje jakoukoli IP adresu na internetu, která není právě nakonfigurovaná pojmenovaná umístění. Když vyberete **libovolné umístění**, správci se můžou rozhodnout vyloučit **všechna důvěryhodná** nebo **Vybraná umístění**.

Některé organizace například mohou zvolit, že nevyžadují vícefaktorové ověřování, když jsou jejich uživatelé připojeni k síti v důvěryhodném umístění, jako je jejich fyzické ústředí. Správci můžou vytvořit zásadu, která zahrnuje libovolné umístění, ale nevylučuje vybraná umístění pro své sítě centrály.

## <a name="client-apps-preview"></a>Klientské aplikace (Preview)

Zásady podmíněného přístupu se standardně používají pro aplikace založené na prohlížeči a aplikace, které využívají moderní ověřovací protokoly. Kromě těchto aplikací můžou správci zvolit zahrnutí klientů Exchange ActiveSync a dalších klientů využívajících starší protokoly.

- Prohlížeč
   - Mezi ně patří webové aplikace, které používají protokoly jako SAML, WS-Federation, OpenID Connect nebo služby registrované jako důvěrného klienta OAuth.
- Mobilní aplikace a desktopoví klienti
   - Klienti moderních ověřování
      - Tato možnost zahrnuje aplikace jako desktopové a telefonní aplikace Office.
   - Klienti Exchange ActiveSync
      - Když zásada zablokuje použití protokolu Exchange ActiveSync, bude se ovlivněný uživatel zobrazovat v jednom e-mailu o karanténě. Tento e-mail s informacemi o tom, proč jsou blokované, a obsahuje pokyny k nápravě, pokud je to možné.
   - Ostatní klienti
      - Tato možnost zahrnuje klienty používající základní a starší protokoly ověřování, včetně protokolů IMAP, MAPI, POP, SMTP a starších aplikací Office, které nepodporují moderní ověřování.

Tyto podmínky se běžně používají při vyžadování spravovaného zařízení, blokování starších verzí ověřování a blokování webových aplikací, ale umožňuje mobilní nebo desktopové aplikace.

### <a name="supported-browsers"></a>Podporované prohlížeče

Toto nastavení funguje ve všech prohlížečích. Pokud ale chcete splnit zásadu zařízení, třeba požadavky na vyhovující zařízení, podporují se tyto operační systémy a prohlížeče:

| Operační systém | Prohlížeče |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8/8,1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| telefon se systémem Windows | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Proč se v prohlížeči zobrazí výzva k zadání certifikátu

V systému Windows 7, iOS, Android a macOS Azure AD identifikuje zařízení pomocí klientského certifikátu, který se zřídí při registraci zařízení ve službě Azure AD.  Když se uživatel poprvé přihlásí prostřednictvím prohlížeče, zobrazí se uživateli výzva k výběru certifikátu. Uživatel musí před použitím prohlížeče tento certifikát vybrat.

#### <a name="chrome-support"></a>Podpora Chrome

V případě podpory pro Chrome ve **Windows 10 Creators Update (verze 1703)** nebo novější nainstalujte [rozšíření účtů Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Toto rozšíření se vyžaduje, když zásada podmíněného přístupu vyžaduje podrobnosti konkrétního zařízení.

Pokud chcete toto rozšíření automaticky nasadit do prohlížečů Chrome, vytvořte následující klíč registru:

|    |    |
| --- | --- |
| Cesta | HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist |
| Název | 1 |
| Typ | REG_SZ (String) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji; https\://clients2.google.com/service/update2/crx |

V případě podpory Chrome v **Windows 8.1 a 7**vytvořte následující klíč registru:

|    |    |
| --- | --- |
| Cesta | HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls |
| Název | 1 |
| Typ | REG_SZ (String) |
| Data | {"Pattern": "https://device.login.microsoftonline.com"; "Filter": {"ISSUER": {"CN": "MS-Organization-Access"}}} |

Tyto prohlížeče podporují ověřování zařízení, což umožňuje, aby se zařízení identifikovalo a ověřilo na základě zásad. Pokud je prohlížeč spuštěný v privátním režimu, neproběhne jeho ověření.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Podporované mobilní aplikace a desktopové klienty

Organizace můžou jako klientskou aplikaci vybrat **mobilní aplikace a klienty klasické pracovní plochy** .

Toto nastavení má vliv na pokusy o přístup uskutečněné z následujících mobilních aplikací a klientů klasické pracovní plochy:

| Klientské aplikace | Cílová služba | Platforma |
| --- | --- | --- |
| Aplikace Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS a Android |
| Aplikace Pošta/kalendář/lidé, Outlook 2016, Outlook 2013 (s moderním ověřováním)| Office 365 Exchange Online | Windows 10 |
| Zásady vícefaktorového ověřování a umístění pro aplikace Zásady založené na zařízeních se nepodporují.| Všechny moje aplikace App Service | Android a iOS |
| Microsoft Team Services – řídí všechny služby, které podporují Microsoft teams a všechny jeho klientské aplikace – desktopové aplikace pro Windows, iOS, Android, WP a webový klient. | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android a macOS |
| Aplikace Office 2016, Office 2013 (s moderním ověřováním), [synchronizační klient OneDrivu](https://docs.microsoft.com/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplikace Office 2016, univerzální aplikace Office, sada Office 2013 (s moderním ověřováním), [synchronizační klient OneDrivu](https://docs.microsoft.com/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (jenom Word, Excel, PowerPoint, OneNote) | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Mobilní aplikace Office | Office 365 SharePoint Online | Android, iOS |
| Aplikace Office Yammer | Office 365 Yammer | Windows 10, iOS, Android |
| Outlook 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office pro macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (s moderním ověřováním), Skype pro firmy (s moderním ověřováním) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Mobilní aplikace Outlook | Office 365 Exchange Online | Android, iOS |
| Aplikace Power BI | služba Power BI | Windows 10, Windows 8.1, Windows 7, Android a iOS |
| Skype pro firmy | Office 365 Exchange Online| Android, IOS |
| Aplikace Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS a Android |

### <a name="exchange-activesync-clients"></a>Klienti Exchange ActiveSync

- Organizace můžou při přiřazování zásad uživatelům nebo skupinám vybrat jenom klienty Exchange ActiveSync. Výběr **všech uživatelů**, **Všichni host a externí uživatelé**nebo **role adresáře** způsobí, že všichni uživatelé budou zablokovaný.
- Při vytváření zásad přiřazených klientům Exchange ActiveSync by měla být **Sada Office 365 Exchange Online** jedinou cloudovou aplikací přiřazenou zásadám. 
- Organizace můžou zúžit rozsah těchto zásad na konkrétní platformy pomocí podmínky pro **platformy zařízení** .

Pokud řízení přístupu přiřazené k zásadám **vyžaduje schválení klientské aplikace**, uživatel se přesměruje na instalaci a používání mobilního klienta Outlooku. V případě, že je vyžadováno **ověřování Multi-Factor Authentication** , jsou ovlivněni uživatelé zablokováni, protože základní ověřování nepodporuje službu Multi-Factor Authentication.

Další informace najdete v následujících článcích:

- [Zablokovat starší ověřování pomocí podmíněného přístupu](block-legacy-authentication.md)
- [Vyžadování schválených klientských aplikací s podmíněným přístupem](app-based-conditional-access.md)

### <a name="other-clients"></a>Ostatní klienti

Výběrem **jiných klientů**můžete určit podmínku, která bude mít vliv na aplikace, které používají základní ověřování, s poštovními protokoly, jako jsou IMAP, MAPI, pop, SMTP a starší aplikace Office, které nepoužívají moderní ověřování.

## <a name="device-state-preview"></a>Stav zařízení (Preview)

Stav zařízení se dá použít k vyloučení zařízení, která jsou připojená k hybridní službě Azure AD nebo zařízení označená jako vyhovující zásadám dodržování předpisů pro zásady podmíněného přístupu organizace v Microsoft Intune.

Například *Všichni uživatelé, kteří* přistupují ke cloudové aplikaci *pro správu Microsoft Azure* , včetně **všech stavů zařízení** , včetně **zařízení připojených k hybridní službě Azure AD** a **zařízení označená jako kompatibilní** a pro *řízení přístupu*, **blokuje**. 
   - Tento příklad vytvoří zásadu, která umožňuje přístup ke správě Microsoft Azure jenom ze zařízení, která jsou připojená k hybridní službě Azure AD nebo zařízení označená jako vyhovující předpisům.

## <a name="next-steps"></a>Další kroky

- [Podmíněný přístup: udělení](concept-conditional-access-grant.md)

- [Společné zásady podmíněného přístupu](concept-conditional-access-policy-common.md)

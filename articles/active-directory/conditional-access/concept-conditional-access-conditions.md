---
title: Podmínky v zásadách podmíněného přístupu – Služba Azure Active Directory
description: Jaké jsou podmínky v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb14369275a111476867f2263766e1bb87b7c87d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295330"
---
# <a name="conditional-access-conditions"></a>Podmíněný přístup: Podmínky

V rámci zásad podmíněného přístupu může správce využívat signály z podmínek, jako je riziko, platforma zařízení nebo umístění, k vylepšení svých zásadových rozhodnutí. 

![Definování zásad podmíněného přístupu a určení podmínek](./media/concept-conditional-access-conditions/conditional-access-conditions.png)

Více podmínek lze kombinovat k vytvoření jemně odstupňované a specifické zásady podmíněného přístupu.

Například při přístupu k citlivé aplikaci správce může faktor přihlašovací informace o riziku z identity ochrany a umístění do jejich rozhodnutí o přístupu kromě jiných ovládacích prvků, jako je vícefaktorové ověřování.

## <a name="sign-in-risk"></a>Riziko přihlášení

Pro zákazníky s přístupem k [ochraně identity](../identity-protection/overview-identity-protection.md), riziko přihlášení lze vyhodnotit jako součást zásadpodmíněného přístupu. Riziko přihlášení představuje pravděpodobnost, že daný požadavek na ověření není autorizován vlastníkem identity. Další informace o riziku přihlášení naleznete v článcích [Co je riziko](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) a [jak: Konfigurace a povolení zásad rizik](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Platformy zařízení

Platforma zařízení se vyznačuje operačním systémem, který běží na zařízení. Azure AD identifikuje platformu pomocí informací poskytovaných zařízením, jako jsou řetězce uživatelského agenta. Vzhledem k tomu, že řetězce uživatelského agenta lze upravit, tyto informace jsou neověřené. Platforma zařízení by měla být používána ve shodě se zásadami dodržování předpisů pro zařízení Microsoft Intune nebo jako součást příkazu bloku. Ve výchozím nastavení platí pro všechny platformy zařízení.

Podmíněný přístup Azure AD podporuje následující platformy zařízení:

- Android
- iOS
- Windows Phone
- Windows
- macOS

Pokud zablokujete starší verze ověřování pomocí podmínky **Ostatní klienti,** můžete také nastavit podmínku platformy zařízení.

## <a name="locations"></a>Umístění

Při konfiguraci umístění jako podmínky mohou organizace zvolit zahrnutí nebo vyloučení umístění. Tato uvedená umístění mohou zahrnovat veřejné informace o síti IPv4, zemi nebo oblasti nebo dokonce neznámé oblasti, které nejsou mapovány na konkrétní země nebo oblasti. Jako důvěryhodné umístění lze označit pouze rozsahy IP adres.

Při **zahrnutí libovolného umístění**tato možnost zahrnuje libovolnou IP adresu na internetu, která není pouze nakonfigurovaná pojmenovaná místa. Při výběru **libovolného umístění**mohou správci vyloučit **všechna důvěryhodná** nebo **vybraná umístění**.

Některé organizace se například mohou rozhodnout, že nebudou vyžadovat vícefaktorové ověřování, pokud jsou jejich uživatelé připojeni k síti v důvěryhodném umístění, například v jejich fyzickém ústředí. Správci mohou vytvořit zásadu, která zahrnuje libovolné umístění, ale vyloučí vybraná umístění pro sítě ústředí.

Další informace o umístění chod v článku [Co je podmínka umístění v Azure Active Directory podmíněného přístupu](location-condition.md).

## <a name="client-apps-preview"></a>Klientské aplikace (náhled)

Zásady podmíněného přístupu ve výchozím nastavení platí pro aplikace a aplikace založené na prohlížeči, které využívají moderní ověřovací protokoly. Kromě těchto aplikací mohou správci zahrnout klienty Exchange ActiveSync a další klienty, kteří využívají starší protokoly.

- Prohlížeč
   - Patří mezi ně webové aplikace, které používají protokoly jako SAML, WS-Federation, OpenID Connect nebo služby registrované jako důvěrný klient OAuth.
- Mobilní aplikace a klienti pro stolní počítače
   - Moderní autentizační klienti
      - Tato možnost zahrnuje aplikace, jako jsou desktopové a telefonní aplikace Office.
   - Klienti Exchange ActiveSync
      - Ve výchozím nastavení to zahrnuje veškeré použití protokolu Exchange ActiveSync (EAS). Pokud **zvolíte Použít zásady jenom na podporované platformy,** omezíte se na podporované platformy, jako jsou iOS, Android a Windows.
      - Pokud zásada blokuje použití Exchange ActiveSync, postižený uživatel obdrží jeden e-mail v karanténě. Tento e-mail s informacemi o tom, proč jsou blokovány, obsahuje pokyny k nápravě, pokud je to možné.
   - Ostatní klienti
      - Tato možnost zahrnuje klienty, kteří používají základní nebo starší ověřovací protokoly, které nepodporují moderní ověřování.
         - Ověřený protokol SMTP – používá klient POP a IMAP k odesílání e-mailových zpráv.
         - Automatická konfigurace – klienti Outlooku a EAS používají k vyhledání poštovních schránek exchange online a připojení k ní.
         - Exchange Online PowerShell – používá se k připojení k Exchange Online pomocí vzdáleného Prostředí PowerShell. Pokud zablokujete základní ověřování pro Exchange Online PowerShell, budete muset použít Exchange Online PowerShell modul pro připojení. Pokyny najdete v [tématu Připojení k Exchange Online PowerShellpomocí vícefaktorového ověřování](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Exchange Web Services (EWS) – programovací rozhraní, které používá Outlook, Outlook pro Mac a aplikace třetích stran.
         - IMAP4 - Používá e-mailové klienty IMAP.
         - MAPI přes HTTP (MAPI/HTTP) – používá aplikace Outlook 2010 a novější.
         - Offline adresář (OAB) – kopie kolekcí seznamů adres, které jsou staženy a používány aplikací Outlook.
         - Outlook Anywhere (RPC přes HTTP) – používá outlook 2016 a starší.
         - Outlook Service – používá aplikace Pošta a Kalendář pro Windows 10.
         - POP3 - Používá se e-mailovými klienty POP.
         - Reporting Web Services – používá se k načtení dat sestavy v Exchange Online.

Tyto podmínky se běžně používají, když vyžadují spravované zařízení, blokují starší verze ověřování a blokují webové aplikace, ale umožňují mobilní nebo desktopové aplikace.

### <a name="supported-browsers"></a>Podporované prohlížeče

Toto nastavení funguje se všemi prohlížeči. Aby však byly splněny zásady zařízení, jako je požadavek kompatibilního zařízení, jsou podporovány následující operační systémy a prohlížeče:

| Operační systém | Browsers |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8 / 8.1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Spravovaný prohlížeč, Safari |
| Android | Microsoft Edge, Intune Spravovaný prohlížeč, Chrome |
| Windows Phone | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Proč se v prohlížeči zobrazí výzva k certifikátu

Ve Windows 7 iOS, Android a macOS Azure AD identifikuje zařízení pomocí klientského certifikátu, který se zřídí, když je zařízení registrované ve službě Azure AD.  Při prvním přihlášení uživatele prostřednictvím prohlížeče je uživatel vyzván k výběru certifikátu. Uživatel musí vybrat tento certifikát před použitím prohlížeče.

#### <a name="chrome-support"></a>Podpora pro Chrome

Pro podporu Chromu v **aktualizaci Windows 10 Creators Update (verze 1703)** nebo novější nainstalujte [rozšíření O účty s Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Toto rozšíření je vyžadováno, pokud zásady podmíněného přístupu vyžadují podrobnosti specifické pro zařízení.

Chcete-li toto rozšíření automaticky nasadit do prohlížečů Chrome, vytvořte následující klíč registru:

|    |    |
| --- | --- |
| Cesta | HKEY_LOCAL_MACHINE\Software\Zásady\Google\Chrome\ExtensionInstallForcelist |
| Name (Název) | 1 |
| Typ | REG_SZ (řetězec) |
| Data | ppnbnpeolgkicgegkbkbjmhlideopiji;https\://clients2.google.com/service/update2/crx |

Pro podporu Chromu ve **Windows 8.1 a 7**vytvořte následující klíč registru:

|    |    |
| --- | --- |
| Cesta | HKEY_LOCAL_MACHINE\SOFTWARE\Zásady\Google\Chrome\AutoSelectCertificateForUrls |
| Name (Název) | 1 |
| Typ | REG_SZ (řetězec) |
| Data | {"pattern":"https://device.login.microsoftonline.com","filter":{"ISSUER":{"CN":"MS-Organization-Access"}}} |

Tyto prohlížeče podporují ověřování zařízení, což umožňuje identifikovat a ověřit zařízení proti zásadám. Kontrola zařízení se nezdaří, pokud je prohlížeč spuštěn v soukromém režimu.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Podporované mobilní aplikace a desktopoví klienti

Organizace mohou jako **klientskou aplikaci vybrat Mobilní aplikace a desktopové klienty.**

Toto nastavení má vliv na pokusy o přístup z následujících mobilních aplikací a desktopových klientů:

| Klientské aplikace | Cílová služba | Platforma |
| --- | --- | --- |
| Aplikace Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS a Android |
| Aplikace Pošta/Kalendář/Lidé, Outlook 2016, Outlook 2013 (s moderním ověřováním)| Office 365 Exchange Online | Windows 10 |
| Vícefaktorové informace a zásady umístění aplikací. Zásady založené na zařízení nejsou podporovány.| Jakákoli služba aplikace Moje aplikace | Android a iOS |
| Microsoft Teams Services – to řídí všechny služby, které podporují Microsoft Teams a všechny jeho klientské aplikace – Windows Desktop, iOS, Android, WP a webový klient | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android a macOS |
| Aplikace Office 2016, Office 2013 (s moderním ověřováním), [klient synchronizace OneDrivu](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 8.1, Windows 7 |
| Aplikace Office 2016, univerzální aplikace Office, Office 2013 (s moderním ověřováním), [klient synchronizace OneDrivu](/onedrive/enable-conditional-access) | Office 365 SharePoint Online | Windows 10 |
| Office 2016 (Word, Excel, PowerPoint, jenom OneNote). | Office 365 SharePoint Online | macOS |
| Office 2019| Office 365 SharePoint Online | Windows 10, macOS |
| Mobilní aplikace Office | Office 365 SharePoint Online | Android, iOS |
| Aplikace Office Yammer | Office 365 Yammer | Windows 10, iOS, Android |
| Výhled na rok 2019 | Office 365 SharePoint Online | Windows 10, macOS |
| Outlook 2016 (Office pro macOS) | Office 365 Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (s moderním ověřováním), Skype pro firmy (s moderním ověřováním) | Office 365 Exchange Online | Windows 8.1, Windows 7 |
| Mobilní aplikace Outlook | Office 365 Exchange Online | Android, iOS |
| Aplikace Power BI | službě Power BI | Windows 10, Windows 8.1, Windows 7, Android a iOS |
| Skype pro firmy | Office 365 Exchange Online| Android, iOS |
| Aplikace Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS a Android |

### <a name="exchange-activesync-clients"></a>Klienti Exchange ActiveSync

- Organizace mohou vybrat klienty Exchange ActiveSync pouze při přiřazování zásad uživatelům nebo skupinám. Výběr **všech uživatelů**, **Všech rolí hosta a externích uživatelů**nebo rolí **adresáře** způsobí, že budou všichni uživatelé blokováni.
- Při vytváření zásad přiřazených klientům Exchange ActiveSync by měl být **Office 365 Exchange Online** jedinou cloudovou aplikací přiřazenou k této zásadě. 
- Organizace můžete zúžit rozsah těchto zásad na konkrétní platformy pomocí **platformy zařízení** podmínku.

Pokud ovládací prvek přístupu přiřazený k zásadám používá **vyžadovat schválenou klientskou aplikaci**, je uživatel vyzván k instalaci a použití mobilního klienta Outlooku. V případě, že je vyžadováno **vícefaktorové ověřování,** jsou ovlivnění uživatelé blokováni, protože základní ověřování nepodporuje vícefaktorové ověřování.

Další informace najdete v těchto článcích:

- [Blokování staršího ověřování pomocí podmíněného přístupu](block-legacy-authentication.md)
- [Vyžadování schválených klientských aplikací s podmíněným přístupem](app-based-conditional-access.md)

### <a name="other-clients"></a>Ostatní klienti

Výběrem **možnosti Ostatní klienti**můžete určit podmínku, která ovlivňuje aplikace, které používají základní ověřování s poštovními protokoly, jako jsou IMAP, MAPI, POP, SMTP a starší aplikace Office, které nepoužívají moderní ověřování.

## <a name="device-state-preview"></a>Stav zařízení (náhled)

Podmínku stavu zařízení lze použít k vyloučení zařízení, která jsou hybridní Azure AD připojena a nebo zařízení označeny jako kompatibilní se zásadami dodržování předpisů Microsoft Intune ze zásad y organizace podmíněného přístupu.

Například *všichni uživatelé, kteří* přistupují ke cloudové aplikaci *Microsoft Azure Management,* včetně **stavu všech zařízení** s výjimkou **připojeného zařízení Hybrid Azure AD** a **zařízení označeného jako kompatibilní** a pro ovládací *prvky Access*, **Block**. 
   - Tento příklad by vytvořit zásadu, která umožňuje přístup k Microsoft Azure Management ze zařízení, která jsou hybridní Azure AD připojen a nebo zařízení označena jako kompatibilní.

## <a name="next-steps"></a>Další kroky

- [Podmíněný přístup: Udělení](concept-conditional-access-grant.md)

- [Podmíněné přístupové běžné zásady](concept-conditional-access-policy-common.md)

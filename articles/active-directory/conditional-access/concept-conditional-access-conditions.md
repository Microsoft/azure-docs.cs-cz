---
title: Podmínky v zásadách podmíněného přístupu – Azure Active Directory
description: Jaké jsou podmínky v zásadách podmíněného přístupu Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2dddad5692ab2eebb6ef9b427c091be449791d7c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91818343"
---
# <a name="conditional-access-conditions"></a>Podmíněný přístup: podmínky

V rámci zásad podmíněného přístupu může správce využít signály z podmínek, jako je riziko, platforma zařízení nebo umístění, a zvýšit tak jejich rozhodování o zásadách. 

[![Definování zásad podmíněného přístupu a určení podmínek](./media/concept-conditional-access-conditions/conditional-access-conditions.png)](./media/concept-conditional-access-conditions/conditional-access-conditions.png#lightbox)

K vytvoření podrobných a specifických zásad podmíněného přístupu lze kombinovat více podmínek.

Například při přístupu k citlivé aplikaci může správce kromě dalších ovládacích prvků, jako je Multi-Factor Authentication, přihlašovat informace o riziku z ochrany identity a umístění do jejich rozhodnutí o přístupu.

## <a name="sign-in-risk"></a>Riziko přihlášení

Pro zákazníky s přístupem k [Identity Protection](../identity-protection/overview-identity-protection.md)je možné vyhodnotit riziko přihlášení v rámci zásad podmíněného přístupu. Riziko přihlášení představuje pravděpodobnost, že daný požadavek na ověření není autorizovaný vlastníkem identity. Další informace o riziku při přihlašování najdete v článcích, [co je to riziko](../identity-protection/concept-identity-protection-risks.md#sign-in-risk) a [Postupy: konfigurace a povolení zásad rizik](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="user-risk"></a>Riziko uživatele 

Pro zákazníky, kteří mají přístup k [Identity Protection](../identity-protection/overview-identity-protection.md), se dá vyhodnotit riziko uživatele v rámci zásad podmíněného přístupu. Riziko uživatele představuje pravděpodobnost, že dojde k ohrožení zabezpečení dané identity nebo účtu. Další informace o rizikech uživatelů najdete v článcích, [co je to riziko](../identity-protection/concept-identity-protection-risks.md#user-risk) a [Postupy: konfigurace a povolení zásad rizik](../identity-protection/howto-identity-protection-configure-risk-policies.md).

## <a name="device-platforms"></a>Platformy zařízení

Platforma zařízení je charakterizována operačním systémem, který běží na zařízení. Azure AD identifikuje platformu pomocí informací poskytovaných zařízením, jako jsou řetězce uživatelských agentů. Vzhledem k tomu, že je možné upravit řetězce uživatelského agenta, tyto informace jsou neověřené. Platforma zařízení by měla být používána společně s Microsoft Intune zásadami dodržování předpisů zařízením nebo jako součást příkazu Block. Ve výchozím nastavení platí pro všechny platformy zařízení.

Podmíněný přístup Azure AD podporuje tyto platformy zařízení:

- Android
- iOS
- telefon se systémem Windows
- Windows
- macOS

Pokud zablokujete starší ověřování pomocí podmínky **ostatních klientů** , můžete také nastavit podmínku pro platformu zařízení.

> [!IMPORTANT]
> Microsoft doporučuje, abyste měli zásady podmíněného přístupu pro nepodporované platformy zařízení. Pokud například chcete blokovat přístup k podnikovým prostředkům ze systému Linux nebo jiných nepodporovaných klientů, měli byste nakonfigurovat zásadu s podmínkou pro platformy zařízení, která zahrnuje jakékoli zařízení a vyloučí podporované platformy zařízení a udělí sadě řízení přístup k blokování přístupu.

## <a name="locations"></a>Umístění

Při konfiguraci umístění jako podmínky se můžou organizace rozhodnout zahrnout nebo vyloučit umístění. Tato pojmenovaná umístění můžou zahrnovat informace o veřejné síti IPv4, zemi nebo oblasti nebo dokonce neznámé oblasti, které nejsou namapované na konkrétní země nebo oblasti. Pouze rozsahy IP adres lze označit jako důvěryhodné umístění.

Při zahrnutí **libovolného umístění**Tato možnost zahrnuje jakoukoli IP adresu na internetu, která není právě nakonfigurovaná pojmenovaná umístění. Když vyberete **libovolné umístění**, správci se můžou rozhodnout vyloučit **všechna důvěryhodná** nebo **Vybraná umístění**.

Některé organizace například mohou zvolit, že nevyžadují vícefaktorové ověřování, když jsou jejich uživatelé připojeni k síti v důvěryhodném umístění, jako je jejich fyzické ústředí. Správci mohou vytvořit zásadu, která zahrnuje libovolné umístění, ale vyloučí vybraná umístění pro své sítě centrály.

Další informace o umístěních najdete v článku [co je to podmínka umístění v Azure Active Directory podmíněný přístup](location-condition.md).

## <a name="client-apps"></a>Klientské aplikace

Ve výchozím nastavení se všechny nově vytvořené zásady podmíněného přístupu použijí na všechny typy klientských aplikací, i když není podmínka pro klientské aplikace nakonfigurovaná. 

> [!NOTE]
> Chování podmínky klientských aplikací bylo aktualizováno v srpnu 2020. Pokud máte existující zásady podmíněného přístupu, zůstanou beze změny. Pokud ale kliknete na existující zásadu, odebere se přepínač konfigurace a budou vybrány klientské aplikace, pro které zásada platí.

> [!IMPORTANT]
> Přihlášení od starších klientů ověřování nepodporují MFA a nedávají informace o stavu zařízení službě Azure AD, takže se zablokují prostřednictvím ovládacích prvků pro udělení podmíněného přístupu, jako je třeba vyžadování zařízení s MFA nebo dodržování předpisů. Pokud máte účty, které musí používat starší verze ověřování, musíte tyto účty buď vyloučit ze zásady, nebo nakonfigurovat zásady tak, aby se použily jenom pro klienty moderních ověřování.

Přepínač **Konfigurovat** , pokud je nastaveno na **hodnotu Ano** , se vztahuje na kontrolované položky, pokud je nastavena hodnota **ne** pro všechny klientské aplikace, včetně moderních a starších ověřovacích klientů. Tento přepínač se nezobrazuje v zásadách vytvořených před srpna 2020.

- Klienti moderních ověřování
   - Prohlížeč
      - Mezi ně patří webové aplikace, které používají protokoly jako SAML, WS-Federation, OpenID Connect nebo služby registrované jako důvěrného klienta OAuth.
   - Mobilní aplikace a klienti klasické pracovní plochy
      -  Tato možnost zahrnuje aplikace jako desktopové a telefonní aplikace Office.
- Klienti starší verze ověřování
   - Klienti Exchange ActiveSync
      - To zahrnuje veškeré použití protokolu Exchange ActiveSync (EAS).
      - Když zásada zablokuje použití protokolu Exchange ActiveSync, bude se ovlivněný uživatel zobrazovat v jednom e-mailu o karanténě. Tento e-mail s informacemi o tom, proč jsou blokované, a obsahuje pokyny k nápravě, pokud je to možné.
      - Správci můžou zásady použít jenom pro podporované platformy (například iOS, Android a Windows) prostřednictvím podmíněného přístupu Graph API.
   - Ostatní klienti
      - Tato možnost zahrnuje klienty, kteří používají základní a starší ověřovací protokoly, které nepodporují moderní ověřování.
         - Ověřený protokol SMTP, pomocí kterého klient POP a IMAP odesílá e-mailové zprávy.
         - Automatická konfigurace – používá klienti Outlooku a EAS k vyhledání a připojení k poštovním schránkám v Exchangi Online.
         - Exchange Online PowerShell – slouží k připojení k Exchangi Online pomocí vzdáleného prostředí PowerShell. Pokud zablokujete základní ověřování pro prostředí Exchange Online PowerShell, musíte k připojení použít modul prostředí Exchange Online PowerShell. Pokyny najdete v tématu [připojení k prostředí PowerShell pro Exchange Online pomocí služby Multi-Factor Authentication](/powershell/exchange/exchange-online/connect-to-exchange-online-powershell/mfa-connect-to-exchange-online-powershell).
         - Webové služby Exchange (EWS) – programovací rozhraní, které používá Outlook, Outlook pro Mac a aplikace třetích stran.
         - IMAP4 – používá e-mailové klienty IMAP.
         - Rozhraní MAPI přes protokol HTTP (MAPI/HTTP) – používá Outlook 2010 a novější.
         - Adresář v režimu offline (OAB) – kopii kolekcí seznamu adres, které jsou staženy a používány aplikací Outlook.
         - Outlook odkudkoli (RPC over HTTP) – používá Outlook 2016 a starší.
         - Služba Outlook – používaná aplikací pro poštu a kalendář pro Windows 10
         - POP3 – používá e-mailové klienty POP.
         - Webové služby vytváření sestav – slouží k načtení dat sestavy v Exchangi Online.

Tyto podmínky se běžně používají při vyžadování spravovaného zařízení, blokování starších verzí ověřování a blokování webových aplikací, ale umožňuje mobilní nebo desktopové aplikace.

### <a name="supported-browsers"></a>Podporované prohlížeče

Toto nastavení funguje ve všech prohlížečích. Pokud ale chcete splnit zásadu zařízení, třeba požadavky na vyhovující zařízení, podporují se tyto operační systémy a prohlížeče:

| Operační systém | Browsers |
| :-- | :-- |
| Windows 10 | Microsoft Edge, Internet Explorer, Chrome |
| Windows 8/8,1 | Internet Explorer, Chrome |
| Windows 7 | Internet Explorer, Chrome |
| iOS | Microsoft Edge, Intune Managed Browser, Safari |
| Android | Microsoft Edge, Intune Managed Browser, Chrome |
| telefon se systémem Windows | Microsoft Edge, Internet Explorer |
| Windows Server 2019 | Microsoft Edge, Internet Explorer, Chrome |
| Windows Server 2016 | Internet Explorer |
| Windows Server 2012 R2 | Internet Explorer |
| Windows Server 2008 R2 | Internet Explorer |
| macOS | Chrome, Safari |

> [!NOTE]
> Edge 85 + vyžaduje, aby byl uživatel přihlášený k prohlížeči, aby správně předával identitu zařízení. V opačném případě se chová jako Chrome bez rozšíření účtů. Toto přihlášení se nemusí automaticky vyskytnout v rámci scénáře připojení k hybridní službě Azure AD. 

#### <a name="why-do-i-see-a-certificate-prompt-in-the-browser"></a>Proč se v prohlížeči zobrazí výzva k zadání certifikátu

V systému Windows 7, iOS, Android a macOS Azure AD identifikuje zařízení pomocí klientského certifikátu, který se zřídí při registraci zařízení ve službě Azure AD.  Když se uživatel poprvé přihlásí prostřednictvím prohlížeče, zobrazí se uživateli výzva k výběru certifikátu. Uživatel musí před použitím prohlížeče tento certifikát vybrat.

#### <a name="chrome-support"></a>Podpora Chrome

V případě podpory pro Chrome ve **Windows 10 Creators Update (verze 1703)** nebo novější nainstalujte [rozšíření účtů Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). Toto rozšíření se vyžaduje, když zásada podmíněného přístupu vyžaduje podrobnosti konkrétního zařízení.

Pokud chcete toto rozšíření automaticky nasadit do prohlížečů Chrome, vytvořte následující klíč registru:

- HKEY_LOCAL_MACHINE\Software\Policies\Google\Chrome\ExtensionInstallForcelist cesty
- Název 1
- Typ REG_SZ (String)
- Data ppnbnpeolgkicgegkbkbjmhlideopiji; https \: //clients2.Google.com/Service/Update2/CRX

V případě podpory Chrome v **Windows 8.1 a 7**vytvořte následující klíč registru:

- HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Google\Chrome\AutoSelectCertificateForUrls cesty
- Název 1
- Typ REG_SZ (String)
- Data {"vzor": " https://device.login.microsoftonline.com ", "Filter": {"Issuer": {"CN": "MS-Organization-Access"}}}

Tyto prohlížeče podporují ověřování zařízení, což umožňuje, aby se zařízení identifikovalo a ověřilo na základě zásad. Pokud je prohlížeč spuštěný v privátním režimu, neproběhne jeho ověření.

### <a name="supported-mobile-applications-and-desktop-clients"></a>Podporované mobilní aplikace a desktopové klienty

Organizace můžou jako klientskou aplikaci vybrat **mobilní aplikace a klienty klasické pracovní plochy** .

Toto nastavení má vliv na pokusy o přístup uskutečněné z následujících mobilních aplikací a klientů klasické pracovní plochy:

| Klientské aplikace | Cílová služba | Platforma |
| --- | --- | --- |
| Aplikace Dynamics CRM | Dynamics CRM | Windows 10, Windows 8.1, iOS a Android |
| Aplikace Pošta/kalendář/lidé, Outlook 2016, Outlook 2013 (s moderním ověřováním)| Exchange Online | Windows 10 |
| Zásady vícefaktorového ověřování a umístění pro aplikace Zásady založené na zařízení se nepodporují.| Všechny moje aplikace App Service | Android a iOS |
| Microsoft Team Services – řídí všechny služby, které podporují Microsoft teams a všechny jeho klientské aplikace – desktopové aplikace pro Windows, iOS, Android, WP a webový klient. | Microsoft Teams | Windows 10, Windows 8.1, Windows 7, iOS, Android a macOS |
| Aplikace Office 2016, Office 2013 (s moderním ověřováním), [synchronizační klient OneDrivu](/onedrive/enable-conditional-access) | SharePoint | Windows 8.1, Windows 7 |
| Aplikace Office 2016, univerzální aplikace Office, sada Office 2013 (s moderním ověřováním), [synchronizační klient OneDrivu](/onedrive/enable-conditional-access) | SharePoint Online | Windows 10 |
| Office 2016 (jenom Word, Excel, PowerPoint, OneNote) | SharePoint | macOS |
| Sada Office 2019| SharePoint | Windows 10, macOS |
| Mobilní aplikace Office | SharePoint | Android, iOS |
| Aplikace Office Yammer | Yammer | Windows 10, iOS, Android |
| Outlook 2019 | SharePoint | Windows 10, macOS |
| Outlook 2016 (Office pro macOS) | Exchange Online | macOS |
| Outlook 2016, Outlook 2013 (s moderním ověřováním), Skype pro firmy (s moderním ověřováním) | Exchange Online | Windows 8.1, Windows 7 |
| Mobilní aplikace Outlook | Exchange Online | Android, iOS |
| Aplikace Power BI | Služba Power BI | Windows 10, Windows 8.1, Windows 7, Android a iOS |
| Skype pro firmy | Exchange Online| Android, iOS |
| Aplikace Visual Studio Team Services | Visual Studio Team Services | Windows 10, Windows 8.1, Windows 7, iOS a Android |

### <a name="exchange-activesync-clients"></a>Klienti Exchange ActiveSync

- Organizace můžou při přiřazování zásad uživatelům nebo skupinám vybrat jenom klienty Exchange ActiveSync. Výběr **všech uživatelů**, **Všichni host a externí uživatelé**nebo **role adresáře** způsobí, že všichni uživatelé budou zablokovaný.
- Při vytváření zásad přiřazených klientům Exchange ActiveSync by měla být **Exchange Online** jedinou cloudovou aplikací přiřazenou zásadám. 
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

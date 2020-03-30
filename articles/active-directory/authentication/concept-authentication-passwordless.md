---
title: Přihlášení bez hesla služby Azure Active Directory (preview)
description: Informace o možnostech pro přihlášení bez hesla do služby Azure Active Directory pomocí klíčů zabezpečení FIDO2 nebo aplikace Microsoft Authenticator
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07ba38a5d7e8e8a89ba122efb1734c1f13a94d48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332187"
---
# <a name="passwordless-authentication-options-for-azure-active-directory"></a>Možnosti ověřování bez hesla pro Službu Azure Active Directory

Vícefaktorové ověřování (MFA) je skvělý způsob, jak zabezpečit vaši organizaci, ale uživatelé často jsou frustrováni další vrstvou zabezpečení, která si musí pamatovat svá hesla. Metody ověřování bez hesla jsou pohodlnější, protože heslo je odebráno a nahrazeno něčím, co máte, plus něco, co jste nebo něco, co znáte.

|   | Něco, co máte | Něco, co jste nebo víte |
| --- | --- | --- |
| Bez hesla | Klíč zařízení, telefonu nebo zabezpečení windows 10 | Biometrické nebo PIN |

Každá organizace má jiné potřeby, pokud jde o ověřování. Microsoft nabízí následující tři možnosti ověřování bez hesla, které se integrují s Azure Active Directory (Azure AD):

- Windows Hello pro firmy
- Aplikace Microsoft Authenticator
- Bezpečnostní klíče FIDO2

![Ověřování: Zabezpečení versus pohodlí](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

## <a name="windows-hello-for-business"></a>Windows Hello pro firmy

Windows Hello pro firmy je ideální pro pracovníky s informacemi, kteří mají svůj vlastní určený počítač se systémem Windows. Biometrické údaje a kód PIN jsou přímo spojeny s počítačem uživatele, což zabraňuje přístupu od jiných uživatelů než od vlastníka. Díky integraci infrastruktury veřejných klíčů (PKI) a integrované podpoře jednotného přihlašování (SSO) poskytuje Windows Hello for Business pohodlný způsob bezproblémového přístupu k podnikovým prostředkům v místním prostředí i v cloudu.

![Příklad přihlášení uživatele pomocí Windows Hello pro firmy](./media/concept-authentication-passwordless/windows-hellow-sign-in.jpeg)

Následující kroky ukazují, jak funguje proces přihlášení pomocí služby Azure Active Directory.

![Diagram, který popisuje kroky související s přihlášením uživatele pomocí Windows Hello pro firmy](./media/concept-authentication-passwordless/windows-hello-flow.png)

1. Uživatel se přihlásí do systému Windows pomocí biometrického nebo PIN gesta. Gesto odemkne soukromý klíč Windows Hello for Business a odešle se poskytovateli podpory zabezpečení cloudového ověřování, označovanéjako *poskytovatel cloudového přístupového práva*.
1. Poskytovatel cloudového přístupového prohlášení požaduje nonce z Azure AD.
1. Azure AD vrátí nonce, který je platný po dobu 5 minut.
1. Poskytovatel Cloud AP podepíše nonce pomocí soukromého klíče uživatele a vrátí podepsané nonce do Azure AD.
1. Azure AD ověří podepsané nonce pomocí bezpečně registrovaného veřejného klíče uživatele proti podpisu nonce. Po ověření podpisu Azure AD pak ověří vrácené podepsané nonce. Když je ověřena nonce, Azure AD vytvoří primární obnovovací token (PRT) s klíčem relace, který je zašifrován do klíče přenosu zařízení a vrátí ji poskytovateli Cloud AP.
1. Poskytovatel cloudového přístupového přístupu obdrží šifrovaný PRT s klíčem relace. Pomocí privátního klíče přenosu zařízení poskytovatel cloudového přístupového bodu dešifruje klíč relace a chrání klíč relace pomocí modulu TPM (Trusted Platform Module) zařízení.
1. Poskytovatel cloudového přístupového prohlášení vrátí systému Windows úspěšnou odpověď na ověření. Uživatel pak je schopen přistupovat k systému Windows, stejně jako cloud a místní aplikace bez nutnosti znovu ověřit (SSO).

[Průvodce plánováním](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-planning-guide) Windows Hello pro firmy vám pomůže při rozhodování o typu nasazení Windows Hello pro firmy a možnostech, které budete muset zvážit.

## <a name="microsoft-authenticator-app"></a>Microsoft Authenticator App

Umožněte, aby se telefon vašeho zaměstnance stal metodou ověřování bez hesla. Je možné, že již používáte Microsoft Authenticator App jako pohodlné vícefaktorové ověřování možnost kromě hesla. Aplikaci Authenticator můžete také použít jako možnost bez hesla.

![Přihlášení k Microsoft Edge pomocí aplikace Microsoft Authenticator](./media/concept-authentication-passwordless/concept-web-sign-in-microsoft-authenticator-app.png)

Aplikace Authenticator změní jakýkoli telefon se systémem iOS nebo Android na silné přihlašovací údaje bez hesla. Uživatelé se mohou přihlásit k libovolné platformě nebo prohlížeči tak, že dostanou oznámení do svého telefonu, přiřazují číslo zobrazené na obrazovce číslu v telefonu a poté k potvrzení pomocí biometrických (dotykového nebo dotykového ovládání) nebo KÓDU PIN. Podrobnosti o instalaci naleznete [v článku Stažení a instalace aplikace Microsoft Authenticator.](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-download-install)

Ověřování bez hesla pomocí aplikace Authenticator se řídí stejným základním vzorem jako Windows Hello pro firmy. Je to trochu složitější, protože uživatel musí být identifikován, aby Azure AD mohl najít microsoft authenticator app verze se používá:

![Diagram, který popisuje kroky související s přihlášením uživatele pomocí aplikace Microsoft Authenticator App](./media/concept-authentication-passwordless/authenticator-app-flow.png)

1. Uživatel zadá své uživatelské jméno.
1. Azure AD zjistí, že uživatel má silné přihlašovací údaje a spustí tok silné pověření.
1. Oznámení se do aplikace odesílá prostřednictvím služby Apple Push Notification Service (APNS) na zařízeních se systémem iOS nebo prostřednictvím služby Firebase Cloud Messaging (FCM) na zařízeních s Androidem.
1. Uživatel obdrží nabízené oznámení a otevře aplikaci.
1. Aplikace volá Azure AD a obdrží výzvu proof-of-presence a nonce.
1. Uživatel dokončí výzvu zadáním biometrických nebo PIN kódů pro odemknutí soukromého klíče.
1. Nonce je podepsána pomocí soukromého klíče a odeslána zpět do Služby Azure AD.
1. Azure AD provádí ověření veřejného a soukromého klíče a vrátí token.

## <a name="fido2-security-keys"></a>Bezpečnostní klíče FIDO2

Bezpečnostní klíče FIDO2 jsou nefalšovatelná metoda ověřování bez hesla založená na standardech, která může přijít v libovolném provedení. Fast Identity Online (FIDO) je otevřený standard pro ověřování bez hesla. FIDO umožňuje uživatelům a organizacím využít standard k přihlášení ke svým prostředkům bez uživatelského jména nebo hesla pomocí externího bezpečnostního klíče nebo klíče platformy integrovaného do zařízení.

Ve verzi Public Preview můžou zaměstnanci používat klíče zabezpečení k přihlášení ke svému Azure AD nebo hybridnímu Azure AD, který se připojil k zařízením s Windows 10, a získat jednotné přihlášení ke svým cloudovým a místním prostředkům. Uživatelé se také mohou přihlásit k podporovaným prohlížečům. Bezpečnostní klíče FIDO2 jsou skvělou volbou pro podniky, které jsou velmi citlivé na zabezpečení nebo mají scénáře nebo zaměstnance, kteří nejsou ochotni nebo schopni používat svůj telefon jako druhý faktor.

![Přihlášení k Microsoft Edge pomocí bezpečnostního klíče](./media/concept-authentication-passwordless/concept-web-sign-in-security-key.png)

Následující proces se používá, když se uživatel přihlásí pomocí bezpečnostního klíče FIDO2:

![Diagram, který popisuje kroky pro přihlášení uživatele pomocí klíče zabezpečení FIDO2](./media/concept-authentication-passwordless/fido2-security-key-flow.png)

1. Uživatel připojí bezpečnostní klíč FIDO2 ke svému počítači.
2. Systém Windows zjistí klíč zabezpečení FIDO2.
3. Systém Windows odešle požadavek na ověření.
4. Azure AD odešle zpět nonce.
5. Uživatel dokončí své gesto k odemknutí soukromého klíče uloženého v zabezpečené enklávě zabezpečovacího klíče FIDO2.
6. Klíč zabezpečení FIDO2 podepisuje nonce pomocí soukromého klíče.
7. Požadavek tokenu primárního obnovovacího tokenu (PRT) s podepsaným nonce se odesílá do služby Azure AD.
8. Azure AD ověří podepsané nonce pomocí veřejného klíče FIDO2.
9. Azure AD vrátí PRT povolit přístup k místním prostředkům.

I když existuje mnoho klíčů, které jsou FIDO2 certifikované FIDO Alliance, Microsoft vyžaduje některé volitelné rozšíření fido2 Client-to-Authenticator Protocol (CTAP) specifikace, které mají být implementovány dodavatelem k zajištění maximální bezpečnosti a nejlepší Zkušenosti.

Bezpečnostní klíč **MUSÍ** implementovat následující funkce a rozšíření z protokolu FIDO2 CTAP, aby byl kompatibilní se společností Microsoft:

| # | Důvěryhodnost funkce / rozšíření | Proč je tato funkce nebo rozšíření vyžadováno? |
| --- | --- | --- |
| 1 | Rezidentní klíč | Tato funkce umožňuje přenosný klíč zabezpečení, kde jsou pověření uložena v klíči zabezpečení. |
| 2 | Klientský kolík | Tato funkce umožňuje chránit vaše přihlašovací údaje s druhým faktorem a platí pro klíče zabezpečení, které nemají uživatelské rozhraní. |
| 3 | hmac-tajné | Toto rozšíření zajišťuje, že se můžete k zařízení přihlásit, když je off-line nebo v režimu v letadle. |
| 4 | Více účtů za RP | Tato funkce zajišťuje, že můžete používat stejný klíč zabezpečení ve více službách, jako je účet Microsoft a služba Azure Active Directory. |

Následující zprostředkovatelé nabízejí klíče zabezpečení FIDO2 různých tvarových faktorů, o kterých je známo, že jsou kompatibilní se zkušenostmi bez hesla. Doporučujeme vám vyhodnotit vlastnosti zabezpečení těchto klíčů kontaktováním dodavatele a fido aliance.

| Poskytovatel | Kontakt |
| --- | --- |
| Yubico | [https://www.yubico.com/support/contact/](https://www.yubico.com/support/contact/) |
| Feitian | [https://www.ftsafe.com/about/Contact_Us](https://www.ftsafe.com/about/Contact_Us) |
| Hid | [https://www.hidglobal.com/contact-us](https://www.hidglobal.com/contact-us) |
| Zajištění | [https://www.ensurity.com/contact](https://www.ensurity.com/contact) |
| eWBM | [https://www.ewbm.com/support](https://www.ewbm.com/support) |
| AuthenTrend | [https://authentrend.com/about-us/#pg-35-3](https://authentrend.com/about-us/#pg-35-3) |
| Gemalto (Skupina Thales) | [https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/](https://safenet.gemalto.com/multi-factor-authentication/authenticators/passwordless-authentication/) |
| OneSpan Inc. | [https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf](https://www.onespan.com/sites/default/files/2019-08/Digipass-SecureClick_datasheet.pdf) |
| IDmelon Technologies Inc. | [https://www.idmelon.com/#idmelon](https://www.idmelon.com/#idmelon) | 

> [!NOTE]
> Pokud si zakoupíte a plánujete používat bezpečnostní klíče založené na technologii NFC, potřebujete pro klíč zabezpečení podporovanou čtečku NFC. Čtečka NFC není požadavek nebo omezení Azure. Seznam podporovaných čteček NFC si můžete s dodavatelem pro zdroj zabezpečení na základě technologie NFC připravit.

Pokud jste dodavatel a chcete zařízení dostat do tohoto seznamu [Fido2Request@Microsoft.com](mailto:Fido2Request@Microsoft.com)podporovaných zařízení, kontaktujte .

## <a name="what-scenarios-work-with-the-preview"></a>Jaké scénáře fungují s náhledem?

- Správci mohou povolit metody ověřování bez hesla pro svého klienta
- Správci mohou pro každou metodu cílit na všechny uživatele nebo vybrat uživatele/skupiny v rámci svého tenanta.
- Koncoví uživatelé mohou zaregistrovat a spravovat tyto metody ověřování bez hesla na svém portálu účtu
- Koncoví uživatelé se mohou přihlásit pomocí těchto metod ověřování bez hesla.
   - Microsoft Authenticator App: Funguje ve scénářích, kde se používá ověřování Azure AD, včetně všech prohlížečů, během instalace Windows 10 Out Of Box (OOBE) a s integrovanými mobilními aplikacemi v libovolném operačním systému.
   - Bezpečnostní klávesy: Pracujte na zamykací obrazovce pro Windows 10 a na webu v podporovaných prohlížečích, jako je Microsoft Edge (starší i nový Edge).

## <a name="choose-a-passwordless-method"></a>Zvolte metodu bez hesla

Volba mezi těmito třemi možnostmi bez hesla závisí na požadavcích vaší společnosti na zabezpečení, platformu a aplikaci.

Při výběru technologie bez hesla společnosti Microsoft je třeba zvážit následující skutečnosti:

||**Windows Hello pro firmy**|**Přihlášení bez hesla pomocí aplikace Microsoft Authenticator**|**Bezpečnostní klíče FIDO2**|
|:-|:-|:-|:-|
|**Předpokladem je**| Windows 10, verze 1809 nebo novější<br>Azure Active Directory| Aplikace Microsoft Authenticator<br>Telefon (zařízení se systémem iOS a Android se systémem Android 6.0 nebo vyšším.)|Windows 10, verze 1809 nebo novější<br>Azure Active Directory|
|**Mode**|Platforma|Software|Hardware|
|**Systémy a zařízení**|POČÍTAČ s vestavěným modulem důvěryhodné platformy (TPM)<br>Rozpoznávání PIN a biometrie |Rozpoznávání PIN a biometrie na telefonu|Zabezpečovací zařízení FIDO2 kompatibilní se společností Microsoft|
|**Uživatelské prostředí**|Přihlaste se pomocí kódu PIN nebo biometrického rozpoznávání (obličeje, duhovky nebo otisku prstu) pomocí zařízení s Windows.<br>Ověřování Windows Hello je vázáno na zařízení. uživatel potřebuje zařízení i přihlašovací součást, jako je kód PIN nebo biometrický faktor, pro přístup k podnikovým prostředkům.|Přihlaste se pomocí mobilního telefonu se skenováním otisků prstů, rozpoznáváním obličeje nebo duhovky nebo KÓDEM PIN.<br>Uživatelé se přihlašují k pracovnímu nebo osobnímu účtu ze svého počítače nebo mobilního telefonu.|Přihlaste se pomocí zabezpečovacího zařízení FIDO2 (biometrie, PIN a NFC)<br>Uživatel může přistupovat k zařízením založeným na ovládacích prvcích organizace a ověřování na základě kódu PIN, biometrii pomocí zařízení, jako jsou bezpečnostní klíče USB a čipové karty, klíče nebo nositelná zařízení s podporou technologie NFC.|
|**Povolené scénáře**| Zkušenosti se systémem Windows bez hesla.<br>Platí pro vyhrazené pracovní pc se schopností pro jednotné přihlašování k zařízení a aplikacím.|Bez hesla kdekoli řešení pomocí mobilního telefonu.<br>Použitelné pro přístup k pracovním nebo osobním aplikacím na webu z libovolného zařízení.|Prostředí bez hesla pro pracovníky, kteří používají biometrické údaje, kód YPin a nfc.<br>Platí pro sdílené počítače a v případě, že mobilní telefon není schůdnou možností (například pro pracovníky technické podpory, veřejný kiosek nebo nemocniční tým)|

V následující tabulce můžete zvolit, která metoda bude podporovat vaše požadavky a uživatele.

|Persona|Scénář|Prostředí|Technologie bez hesla|
|:-|:-|:-|:-|
|**Správce**|Zabezpečený přístup k zařízení pro úlohy správy|Přiřazené zařízení s Windows 10|Windows Hello pro firmy a/nebo klíč zabezpečení FIDO2|
|**Správce**|Úlohy správy na zařízeních, která nejsou s Windows| Mobilní zařízení nebo zařízení mimo systém Windows|Přihlášení bez hesla pomocí aplikace Microsoft Authenticator|
|**Informační pracovník**|Produktivita práce|Přiřazené zařízení s Windows 10|Windows Hello pro firmy a/nebo klíč zabezpečení FIDO2|
|**Informační pracovník**|Produktivita práce| Mobilní zařízení nebo zařízení mimo systém Windows|Přihlášení bez hesla pomocí aplikace Microsoft Authenticator|
|**Pracovník v první linii**|Veřejné terminály v továrně, závodě, maloobchodu nebo zadávání dat|Sdílená zařízení s Windows 10|Bezpečnostní klíče FIDO2|

## <a name="next-steps"></a>Další kroky

[Povolte možnosti bezhesla pomocí bezpečnostního klíče FIDO2 ve vaší organizaci](howto-authentication-passwordless-security-key.md)

[Povolení možností bez hesla založených na telefonu ve vaší organizaci](howto-authentication-passwordless-phone.md)

### <a name="external-links"></a>Externí odkazy

[Aliance FIDO](https://fidoalliance.org/)

[Fido2 CTAP specifikace](https://fidoalliance.org/specs/fido-v2.0-id-20180227/fido-client-to-authenticator-protocol-v2.0-id-20180227.html)

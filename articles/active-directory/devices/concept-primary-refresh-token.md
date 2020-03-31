---
title: Primární obnovovací token (PRT) a Azure AD – Azure Active Directory
description: Jaká je role a jak spravujeme primární obnovovací token (PRT) ve službě Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 05/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9a237ad35d9d5d8abee784926563d972d0ee95f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672636"
---
# <a name="what-is-a-primary-refresh-token"></a>Co je primární obnovovací token?

Primární obnovovací token (PRT) je klíčovým artefaktem ověřování Azure AD na zařízeních s Windows 10, iOS a Android. Jedná se o JSON Web Token (JWT) speciálně vydané microsoft první strany token zprostředkovatelů povolit jednotné přihlašování (SSO) napříč aplikacemi používanými na těchto zařízeních. V tomto článku poskytneme podrobnosti o tom, jak je PRT vydáván, používán a chráněn na zařízeních s Windows 10.

Tento článek předpokládá, že už rozumíte různým stavům zařízení dostupným ve službě Azure AD a tomu, jak funguje jednotné přihlašování ve Windows 10. Další informace o zařízeních ve službě Azure AD najdete v článku [Co je správa zařízení ve službě Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Klíčová terminologie a komponenty

Následující součásti systému Windows hrají klíčovou roli při požadování a používání PRT:

* **Poskytovatel cloudového ověřování** (CloudAP): CloudAP je moderní poskytovatel ověřování pro přihlášení k Systému Windows, který ověřuje přihlášení uživatelů k zařízení s Windows 10. CloudAP poskytuje rozhraní pro moduly plug-in, na které mohou poskytovatelé identit y stavět a povolit ověřování v systému Windows pomocí přihlašovacích údajů tohoto zprostředkovatele identity.
* **Správce webových účtů** (WAM): WAM je výchozí zprostředkovatel tokenů na zařízeních s Windows 10. WAM také poskytuje rozhraní pro moduly plug-in, na kterých mohou poskytovatelé identit stavět a povolit služby SSO svým aplikacím, které se spoléhají na tohoto zprostředkovatele identity.
* **Modul plug-in Azure AD CloudAP**: Modul plug-in specifický pro Azure AD založený na rozhraní CloudAP, který ověřuje přihlašovací údaje uživatelů pomocí služby Azure AD během přihlašování k Windows.
* **Azure AD WAM plugin:** Modul plug-in specifický pro Azure AD založený na rámci WAM, který umožňuje přihlašování k aplikacím, které jsou závislé na Azure AD pro ověřování.
* **Dsreg**: Azure AD specifické součásti ve Windows 10, který zpracovává proces registrace zařízení pro všechny stavy zařízení.
* **Trusted Platform Module** (TPM): Čip TPM je hardwarová součást integrovaná do zařízení, která poskytuje hardwarové funkce zabezpečení pro tajné klíče uživatelů a zařízení. Více informací naleznete v článku [Přehled technologie trusted platformmodule technology](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Co PRT obsahuje?

PRT obsahuje deklarace identity obecně obsažené v libovolném tokenu aktualizace Azure AD. Kromě toho jsou v PRT zahrnuty některé deklarace specifické pro zařízení. Jsou to tyto:

* **ID zařízení**: PRT je vydán uživateli na konkrétním zařízení. Deklarace `deviceID` ID zařízení určuje zařízení, na kterém bylo PRT vydáno uživateli. Toto tvrzení je později vydáno na žetony získané prostřednictvím PRT. Deklarace ID zařízení se používá k určení autorizace podmíněného přístupu na základě stavu zařízení nebo dodržování předpisů.
* **Klíč relace**: Klíč relace je šifrovaný symetrický klíč generovaný ověřovací službou Azure AD, vydaný jako součást PRT. Klíč relace funguje jako důkaz vlastnictví při použití PRT k získání tokenů pro jiné aplikace.

### <a name="can-i-see-whats-in-a-prt"></a>Můžu vidět, co je v PRT?

PRT je neprůhledný objekt blob odeslaný z Azure AD, jehož obsah není znám žádné součásti klienta. Nevidíte, co je uvnitř PRT.

## <a name="how-is-a-prt-issued"></a>Jak se PRT vydává?

Registrace zařízení je předpokladem pro ověřování na základě zařízení ve službě Azure AD. PRT se vydává uživatelům pouze na registrovaných zařízeních. Podrobnější informace o registraci zařízení najdete v článku [Windows Hello for Business and Device Registration](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Během registrace zařízení generuje komponenta dsreg dvě sady párů kryptografických klíčů:

* Klíč zařízení (dkpub/dkpriv)
* Klíč pro přepravu (tkpub/tkpriv)

Soukromé klíče jsou vázány na čip TPM zařízení, pokud má zařízení platný a funkční čip TPM, zatímco veřejné klíče jsou odesílány do Služby Azure AD během procesu registrace zařízení. Tyto klíče se používají k ověření stavu zařízení během požadavků PRT.

PRT se vydává během ověřování uživatelů na zařízení s Windows 10 ve dvou scénářích:

* **Azure AD připojen** nebo **hybridní Azure AD připojen**: PRT se vydává během přihlášení k Systému Windows, když se uživatel přihlásí pomocí jejich pověření organizace. PRT je vydáván se všemi windows 10 podporované přihlašovací údaje, například heslo a Windows Hello pro firmy. V tomto scénáři je primární autoritou pro PRT modul plug-in Azure AD CloudAP.
* **Zařízení registrované pro Azure AD**: PRT se vydává, když uživatel přidá sekundární pracovní účet do svého zařízení s Windows 10. Uživatelé mohou přidat účet do systému Windows 10 dvěma různými způsoby -  
   * Přidání účtu přes **výzvu Použít tento účet všude na tomto zařízení** po přihlášení k aplikaci (například Outlooku)
   * Přidání účtu z **nastavení** > **účtů** > **Přístup k práci nebo školní** > **připojení**

Ve scénářích zařízení registrovaných na Azure AD je plugin Azure AD WAM primární autoritou pro PRT, protože přihlášení k Systému Windows se neděje s tímto účtem Azure AD.

> [!NOTE]
> Poskytovatelé identit třetích stran musí podporovat protokol WS-Trust, aby bylo možné vystavovat PRT na zařízeních se systémem Windows 10. Bez WS-Trust, PRT nelze vydat uživatelům na hybridní Azure AD připojen nebo Zařízení připojená k Azure AD

## <a name="what-is-the-lifetime-of-a-prt"></a>Jaká je životnost PRT?

Po vydání je PRT platný po dobu 14 dnů a je průběžně obnovován, pokud uživatel aktivně používá zařízení.  

## <a name="how-is-a-prt-used"></a>Jak se PRT používá?

PRT používají dvě klíčové součásti v systému Windows:

* **Modul plug-in Azure AD CloudAP**: Během přihlášení k Systému Windows požaduje modul plug-in Azure AD CloudAP prt z Azure AD pomocí přihlašovacích údajů poskytnutých uživatelem. Také ukládá do mezipaměti PRT povolit přihlášení do mezipaměti, když uživatel nemá přístup k připojení k internetu.
* **Modul plug-in Azure AD WAM**: Když se uživatelé pokusí o přístup k aplikacím, modul plug-in Azure AD WAM použije prt k povolení přihlašování k řízení osobních údajů ve Windows 10. Modul plug-in Azure AD WAM používá PRT k vyžádání obnovovacích tokenů a přístupu k tokenům pro aplikace, které spoléhají na WAM pro požadavky na tokeny. Umožňuje také sso v prohlížečích vložením PRT do požadavků prohlížeče. Připojování k prohlížeči ve Windows 10 je podporované na Microsoft Edge (nativně) a Chrome (přes Účty Windows 10 nebo Rozšíření Office Online).

## <a name="how-is-a-prt-renewed"></a>Jak se PRT obnovuje?

PRT se obnovuje dvěma různými způsoby:

* **Azure AD CloudAP plugin každé 4 hodiny**: CloudAP plugin obnovuje PRT každé 4 hodiny během přihlášení k Systému Windows. Pokud uživatel nemá během této doby připojení k internetu, plugin CloudAP obnoví PRT po připojení zařízení k internetu.
* **Azure AD WAM plugin během žádosti o token aplikace**: WAM plugin umožňuje přihlašování k systému Microsoft 10 zařízení povolením tiché požadavky na tokeny pro aplikace. Plugin WAM může obnovit PRT během těchto požadavků na tokeny dvěma různými způsoby:
   * Aplikace požaduje WAM pro přístupový token tiše, ale pro tuto aplikaci není k dispozici žádný obnovovací token. V tomto případě WAM používá PRT požádat o token pro aplikaci a získá zpět nový PRT v odpovědi.
   * Aplikace požaduje WAM pro přístupový token, ale PRT je neplatný nebo Azure AD vyžaduje další autorizaci (například Azure Multi-Factor Authentication). V tomto scénáři WAM iniciuje interaktivní přihlášení, které vyžaduje, aby uživatel znovu ověřit nebo poskytnout další ověření a nový PRT je vydán a úspěšné ověření.

### <a name="key-considerations"></a>Klíčové aspekty

* PRT se vydává a obnovuje pouze během ověřování nativní aplikace. PRT není obnovena nebo vydána během relace prohlížeče.
* Ve službě Azure AD připojená a hybridní zařízení Azure AD připojená k zařízení, modul plug-in CloudAP je primární autoritou pro PRT. Pokud PRT je obnovena během žádosti o token na základě WAM, PRT je odeslána zpět do cloudap plugin, který ověří platnost PRT s Azure AD před přijetím.

## <a name="how-is-the-prt-protected"></a>Jak je PRT chráněn?

PRT je chráněn vazbou na zařízení, ke kterým se uživatel přihlásil. Azure AD a Windows 10 umožňují ochranu PRT pomocí následujících metod:

* **Během prvního přihlášení**: Při prvním přihlášení se PRT vydává podpisem požadavků pomocí klíče zařízení kryptograficky generovaný během registrace zařízení. Na zařízení s platným a funkčním čipem TPM je klíč zařízení zabezpečen čipem TPM, který zabraňuje škodlivému přístupu. PRT není vydána, pokud odpovídající podpis klíče zařízení nelze ověřit.
* **Během požadavků na tokeny a obnovení**: Při vydání PRT Azure AD také vydá šifrovaný klíč relace k zařízení. Je zašifrována s klíčem veřejné dopravy (tkpub) generované a odeslané do Azure AD jako součást registrace zařízení. Tento klíč relace lze dešifrovat pouze soukromým klíčem přenosu (tkpriv) zabezpečeným čipem TPM. Klíč relace je klíč Proof-of-Possession (POP) pro všechny požadavky odeslané do služby Azure AD.  Klíč relace je také chráněn čipem TPM a žádná jiná součást operačního systému k němu nemá přístup. Požadavky na tokeny nebo požadavky na obnovení PRT jsou bezpečně podepsány tímto klíčem relace prostřednictvím čipu TPM, a proto s nimi nelze manipulovat. Azure AD zruší platnost všech požadavků ze zařízení, které nejsou podepsány odpovídající klíč relace.

Zabezpečením těchto klíčů pomocí čipu TPM nemohou škodlivé objekty ukrást klíče ani přehrát PRT jinde, protože čip TPM je nepřístupný i v případě, že útočník fyzicky vlastní zařízení.  Použití čipu TPM tedy výrazně zvyšuje zabezpečení azure ad připojil, hybridní Azure AD připojen a Azure AD registrovaných zařízení proti krádeži přihlašovacích údajů. Pro výkon a spolehlivost je čip TPM 2.0 doporučenou verzí pro všechny scénáře registrace zařízení Azure AD ve Windows 10.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Jak jsou tokeny aplikací a soubory cookie prohlížeče chráněny?

**Tokeny aplikací**: Když aplikace požádá o token prostřednictvím WAM, Azure AD vydá obnovovací token a přístupový token. WAM však vrátí pouze přístupový token do aplikace a zabezpečí obnovovací token ve své mezipaměti šifrováním pomocí uživatelského klíče pro programování aplikace pro ochranu dat (DPAPI). WAM bezpečně používá obnovovací token podpisem požadavků pomocí klíče relace k vydání dalších přístupových tokenů. Klíč DPAPI je zabezpečený symetrickým klíčem založeným na Azure AD ve službě Azure AD samotné. Když zařízení potřebuje dešifrovat profil uživatele pomocí klíče DPAPI, Služba Azure AD poskytuje klíč DPAPI zašifrovaný klíčem relace, který modul plug-in CloudAP požaduje čip TPM k dešifrování. Tato funkce zajišťuje konzistenci při zabezpečení obnovovacích tokenů a zabraňuje aplikacím implementujícím vlastní mechanismy ochrany.  

**Soubory cookie prohlížeče**: Ve Windows 10 podporuje Azure AD webové zabezpečení prohlížeče v Aplikaci Internet Explorer a Microsoft Edge nativně nebo v Prohlížeči Google Chrome prostřednictvím rozšíření účtů Windows 10. Zabezpečení je vytvořeno nejen pro ochranu cookies, ale také pro koncové body, do kterých jsou soubory cookie odesílány. Soubory cookie prohlížeče jsou chráněny stejným způsobem jako PRT, a to využitím klíče relace k podepsání a ochraně souborů cookie.

Když uživatel zahájí interakci prohlížeče, prohlížeč (nebo rozšíření) vyvolá nativního klienta COM. Nativní hostitel klienta zajišťuje, že stránka pochází z jedné z povolených domén. Prohlížeč může odeslat další parametry nativnímu hostiteli klienta, včetně nonce, ale nativní hostitel klienta zaručuje ověření názvu hostitele. Nativní hostitel klienta požaduje prt-cookie z modulu plug-in CloudAP, který jej vytvoří a podepíše pomocí klíče relace chráněného čipem TPM. Vzhledem k tomu, že soubor cookie PRT je podepsán klíčem relace, nelze s ním manipulovat. Tento PRT-cookie je součástí hlavičky požadavku pro Azure AD k ověření zařízení, ze kterého pochází. Pokud používáte prohlížeč Chrome, pouze rozšíření explicitně definované v manifestu nativního hostitele klienta jej může vyvolat a zabránit tomu, aby tato rozšíření byla vaše rozšíření požadována. Jakmile Azure AD ověří soubor cookie PRT, vydá soubor cookie relace do prohlížeče. Tento soubor cookie relace také obsahuje stejný klíč relace vydaný s PRT. Během následných požadavků je klíč relace ověřen efektivně vazby cookie do zařízení a brání přehánět záznamy odjinud.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Kdy prt získat nárok MFA?

PRT můžete získat deklarace vícefaktorového ověřování (MFA) v konkrétních scénářích. Při použití PRT založené na MFA k vyžádání tokenů pro aplikace, deklarace mfa se přenese do těchto tokenů aplikace. Tato funkce poskytuje uživatelům bezproblémové prostředí tím, že zabraňuje výzvám vícefaktorovém přístupu pro každou aplikaci, která to vyžaduje. PRT můžete získat deklaraci mfa následujícími způsoby:

* **Přihlaste se pomocí Windows Hello pro firmy**: Windows Hello pro firmy nahrazuje hesla a používá kryptografické klíče k zajištění silného dvoufaktorového ověřování. Windows Hello pro firmy je specifické pro uživatele na zařízení a sama o sobě vyžaduje vícefaktorové ověřování k zajištění. Když se uživatel přihlásí pomocí Windows Hello pro firmy, prt uživatele získá deklaraci MFA. Tento scénář platí také pro uživatele, kteří se přihlašují pomocí čipových karet, pokud ověřování pomocí čipových karet vytváří deklaraci MFA ze služby ADFS.
   * Jako Windows Hello pro firmy je považován za vícefaktorové ověřování, deklarace MFA je aktualizována při aktualizaci samotného PRT, takže doba trvání MFA se bude neustále prodlužovat, když se uživatelé přihlásí pomocí WIndows Hello for Business
* **MFA během interaktivního přihlášení WAM**: Během žádosti o token prostřednictvím WAM, pokud je uživatel povinen provést vícefaktorové ověřování pro přístup k aplikaci, PRT, který je obnoven během této interakce je potištěn deklarací mfa.
   * V tomto případě deklarace MFA není aktualizována průběžně, takže doba trvání vícefaktorové žádosti je založena na životnosti nastavené v adresáři.
   * Pokud se pro přístup k aplikaci používají předchozí existující PRT a RT, budou PRT a RT považovány za první doklad o ověření. Bude vyžadován nový AT s druhým důkazem a vtiskem mfa tvrzení. To bude také vydávat nové PRT a RT.
* **Vícefaktorové ověřování během registrace zařízení**: Pokud správce nakonfiguroval nastavení zařízení ve službě Azure AD tak, aby [vyžadovalo vícefaktorové ověřování k registraci zařízení](device-management-azure-portal.md#configure-device-settings), uživatel musí provést vícefaktorové ověřování k dokončení registrace. Během tohoto procesu PRT, který je vydán uživateli má deklarace mfa získané během registrace. Tato možnost platí pouze pro uživatele, který provedl operaci připojení, nikoli pro ostatní uživatele, kteří se k tomuto zařízení přihlašují.
   * Podobně jako interaktivní přihlášení WAM není deklarace mfa průběžně aktualizována, takže doba trvání vícefaktorového přístupu je založena na době životnosti nastavené v adresáři.

Windows 10 udržuje rozdělený seznam PRT pro každé pověření. Takže je tu PRT pro každý Z Windows Hello pro podnikání, heslo nebo čipovou kartu. Toto dělení zajišťuje, že deklarace MFA jsou izolované na základě použitého pověření a nejsou smíchány během požadavků tokenů.

## <a name="how-is-a-prt-invalidated"></a>Jak je PRT zneplatněn?

PRT je zrušena v následujících scénářích:

* **Neplatný uživatel**: Pokud je uživatel odstraněn nebo zakázán ve službě Azure AD, jejich PRT je zrušena a nelze použít k získání tokenů pro aplikace. Pokud se odstraněný nebo zakázaný uživatel již přihlásil k zařízení dříve, přihlášení v mezipaměti by je přihlašovalo, dokud cloudap neví o jejich neplatném stavu. Jakmile CloudAP zjistí, že uživatel je neplatný, blokuje následné přihlášení. Neplatnému uživateli je automaticky zablokováno přihlášení k novým zařízením, která nemají svá pověření uložena v mezipaměti.
* **Neplatné zařízení**: Pokud je zařízení odstraněno nebo zakázáno ve službě Azure AD, PRT získané na tomto zařízení je neplatné a nelze použít k získání tokenů pro jiné aplikace. Pokud je uživatel již přihlášen k neplatnému zařízení, může v tom pokračovat. Ale všechny tokeny v zařízení jsou zneplatněny a uživatel nemá zabezpečení zabezpečení žádné prostředky z tohoto zařízení.
* **Změna hesla**: Po změně hesla uživatele matná platnost pomocí předchozího hesla je zrušena službou Azure AD. Změna hesla má za následek, že uživatel získá nový PRT. K tomuto zneplatnění může dojít dvěma různými způsoby:
   * Pokud se uživatel přihlásí k Systému Windows pomocí nového hesla, CloudAP zahodí staré PRT a požádá Azure AD vydat nový PRT s jejich nové heslo. Pokud uživatel nemá připojení k internetu, nové heslo nelze ověřit, systém Windows může vyžadovat, aby uživatel zadali své staré heslo.
   * Pokud se uživatel přihlásil pomocí svého starého hesla nebo změnil své heslo po přihlášení k systému Windows, starý PRT se používá pro všechny požadavky na tokeny založené na WAM. V tomto scénáři je uživatel vyzván k opětovnému ověření během požadavku na token WAM a je vydán nový PRT.
* **Problémy s čipem TPM**: Čip TPM zařízení může někdy zakolísat nebo selhat, což vede k nedostupnosti klíčů zabezpečených čipem TPM. V tomto případě zařízení není schopno získat PRT nebo požadující tokeny pomocí existujícího PRT, protože nemůže prokázat vlastnictví kryptografických klíčů. V důsledku toho všechny existující PRT je zrušena Azure AD. Když systém Windows 10 zjistí chybu, zahájí tok obnovení pro opětovnou registraci zařízení pomocí nových kryptografických klíčů. S hybridní mandory Azure Ad připojit, stejně jako počáteční registrace, obnovení se děje tiše bez vstupu uživatele. Pro zařízení Azure AD připojen nebo Azure AD registrovaná zařízení, obnovení musí provést uživatel, který má oprávnění správce na zařízení. V tomto scénáři je tok obnovení iniciován výzvou systému Windows, která vede uživatele k úspěšnému obnovení zařízení.

## <a name="detailed-flows"></a>Podrobné toky

Následující diagramy znázorňují základní podrobnosti při vydávání, obnovování a použití PRT k vyžádání přístupového tokenu pro aplikaci. Kromě toho tyto kroky také popisují, jak jsou výše uvedené mechanismy zabezpečení použity během těchto interakcí.

### <a name="prt-issuance-during-first-sign-in"></a>Vystavení PRT při prvním přihlášení

![Vydání PRT při prvním přihlášení v podrobném toku](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Ve službě Azure AD připojená zařízení, tato výměna se stane synchronně vydat PRT před uživatelem můžete přihlásit k Systému Windows. V hybridních zařízeních spojených s Azure AD je primární autoritou místní služba Active Directory. Takže uživatel čeká pouze na získání TGT pro přihlášení, zatímco vydání PRT se děje asynchronně. Tento scénář se nevztahuje na zařízení registrovaná službou Azure AD, protože přihlášení nepoužívá přihlašovací údaje Azure AD.

| Krok | Popis |
| :---: | --- |
| A | Uživatel zadá své heslo do znaménku v uživatelském rozhraní. LogonUI předá pověření v ověřovací vyrovnávací paměti lsa, který zase předá interně CloudAP. CloudAP předá tento požadavek modulu plug-in CloudAP. |
| B | Modul plug-inituje požadavek na zjišťování sféry k identifikaci zprostředkovatele identity pro uživatele. Pokud má klient uživatele nastavení zprostředkovatele federace, Azure AD vrátí koncový bod zprostředkovatele metadat federace Exchange (MEX). Pokud ne, Azure AD vrátí, že uživatel je spravován označující, že uživatel můžete ověřit pomocí Služby Azure AD. |
| C | Pokud je uživatel spravován, CloudAP získá nonce z Azure AD. Pokud je uživatel federovaný, modul plug-in CloudAP požaduje token SAML od poskytovatele federace s pověřeními uživatele. Jakmile obdrží token SAML, požaduje nonce z Azure AD. |
| D | Modul plug-in CloudAP vytvoří požadavek na ověření s pověřeními uživatele, nonce a oborem brokera, podepíše požadavek pomocí klíče Device (dkpriv) a odešle jej do Služby Azure AD. Ve federovaném prostředí používá modul plug-in CloudAP token SAML vrácený poskytovatelem federace namísto přihlašovacích údajů uživatele. |
| E | Azure AD ověří pověření uživatele, nonce a podpis zařízení, ověří, že zařízení je platné v tenantovi a vydává šifrované PRT. Spolu s PRT Azure AD také vydává symetrický klíč, nazvaný session klíč zašifrovaný Azure AD pomocí klíče přenosu (tkpub). Kromě toho je klíč relace také vložen do PRT. Tento klíč relace funguje jako klíč proof-of-possession (PoP) pro následné požadavky s PRT. |
| F | CloudAP plugin předá šifrovaný PRT a session klíč CloudAP. CloudAP požaduje čip TPM k dešifrování klíče relace pomocí klíče přenosu (tkpriv) a k jeho opětovnému šifrování pomocí vlastního klíče čipu TPM. CloudAP ukládá šifrovaný klíč relace ve své mezipaměti spolu s PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Obnovení PRT v následných přihlášeních

![Obnovení PRT v následných přihlášeních](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Krok | Popis |
| :---: | --- |
| A | Uživatel zadá své heslo do znaménku v uživatelském rozhraní. LogonUI předá pověření v ověřovací vyrovnávací paměti lsa, který zase předá interně CloudAP. CloudAP předá tento požadavek modulu plug-in CloudAP. |
| B | Pokud se uživatel k uživateli již dříve přihlásil, systém Windows zahájí přihlášení v mezipaměti a ověří pověření pro přihlášení uživatele. Každé 4 hodiny plugin CloudAP inicializuje obnovení PRT asynchronně. |
| C | Modul plug-inituje požadavek na zjišťování sféry k identifikaci zprostředkovatele identity pro uživatele. Pokud má klient uživatele nastavení zprostředkovatele federace, Azure AD vrátí koncový bod zprostředkovatele metadat federace Exchange (MEX). Pokud ne, Azure AD vrátí, že uživatel je spravován označující, že uživatel můžete ověřit pomocí Služby Azure AD. |
| D | Pokud je uživatel federovaný, modul plug-in CloudAP požaduje token SAML od poskytovatele federace s pověřeními uživatele. Jakmile obdrží token SAML, požaduje nonce z Azure AD. Pokud je uživatel spravován, CloudAP přímo získat nonce z Azure AD. |
| E | Modul plug-in CloudAP vytvoří požadavek na ověření s pověřeními uživatele, nonce a existující prt, podepíše požadavek pomocí klíče relace a odešle jej do Azure AD. Ve federovaném prostředí používá modul plug-in CloudAP token SAML vrácený poskytovatelem federace namísto přihlašovacích údajů uživatele. |
| F | Azure AD ověří podpis klíče relace porovnáním s klíčem relace vloženým v PRT, ověří nonce a ověří, zda je zařízení platné v tenantovi a vydá nový PRT. Jak již bylo vidět dříve, PRT je opět doprovázen a session klíč zašifrován pomocí klíče přenosu (tkpub). |
| G | CloudAP plugin předá šifrovaný PRT a session klíč CloudAP. CloudAP požaduje čip TPM k dešifrování klíče relace pomocí klíče přenosu (tkpriv) a k jeho opětovnému šifrování pomocí vlastního klíče čipu TPM. CloudAP ukládá šifrovaný klíč relace ve své mezipaměti spolu s PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Využití PRT během požadavků na token aplikace

![Využití PRT během požadavků na token aplikace](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Krok | Popis |
| :---: | --- |
| A | Aplikace (například Outlook, OneNote atd.) iniciuje požadavek na token pro WAM. WAM zase požádá plugin Azure AD WAM, aby obsluhoval požadavek na token. |
| B | Pokud je token aktualizace pro aplikaci již k dispozici, plugin Azure AD WAM ho používá k vyžádání přístupového tokenu. Chcete-li poskytnout doklad o vazby zařízení, wam plugin podepíše požadavek s session klíč. Azure AD ověří klíč relace a vydá přístupový token a nový obnovovací token pro aplikaci, zašifrovaný klíčem relace. WAM plugin požaduje Cloud AP plugin pro dešifrování tokenů, které zase požadují, aby čip TPM dešifroval pomocí klíče relace, což vede k tomu, že plugin WAM získá oba tokeny. Dále plugin WAM poskytuje pouze přístupový token k aplikaci, zatímco znovu šifruje obnovovací token s DPAPI a ukládá jej do vlastní mezipaměti  |
| C |  Pokud obnovovací token pro aplikaci není k dispozici, modul plug-in Azure AD WAM používá PRT k vyžádání přístupového tokenu. Chcete-li poskytnout doklad o vlastnictví, WAM plugin podepíše žádost obsahující PRT s session klíč. Azure AD ověří podpis klíče relace porovnáním s klíčem relace vloženým do PRT, ověří, zda je zařízení platné, a vydá přístupový token a obnovovací token pro aplikaci. Kromě toho Azure AD můžete vydat nový PRT (na základě cyklu aktualizace), všechny z nich zašifrované klíčem relace. |
| D | WAM plugin požaduje Cloud AP plugin pro dešifrování tokenů, které zase požadují, aby čip TPM dešifroval pomocí klíče relace, což vede k tomu, že plugin WAM získá oba tokeny. Dále wam plugin poskytuje pouze přístupový token k aplikaci, zatímco znovu šifruje obnovovací token s DPAPI a ukládá jej do vlastní mezipaměti. WAM plugin bude používat obnovovací token do budoucna pro tuto aplikaci. WAM plugin také vrací nový modul plug-in PRT cloudovému přístupu, který ověřuje PRT pomocí Azure AD před jeho aktualizací ve vlastní mezipaměti. Cloud AP plugin bude používat nový PRT do budoucna. |
| E | WAM poskytuje nově vydaný přístupový token pro WAM, který jej zase poskytuje zpět do volající aplikace|

### <a name="browser-sso-using-prt"></a>SSO prohlížeče pomocí PRT

![SSO prohlížeče pomocí PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Krok | Popis |
| :---: | --- |
| A | Uživatel se přihlásí k systému Windows se svými přihlašovacími údaji, aby získal PRT. Jakmile uživatel otevře prohlížeč, prohlížeč (nebo rozšíření) načte adresy URL z registru. |
| B | Když uživatel otevře přihlašovací adresu URL Azure AD, prohlížeč nebo rozšíření ověří adresu URL s těmi, které získal z registru. Pokud se shodují, prohlížeč vyvolá nativního hostitele klienta pro získání tokenu. |
| C | Nativní hostitel klienta ověří, že adresy URL patří poskytovatelům identit y Microsoft (účet Microsoft nebo Azure AD), extrahuje nonce odeslané z adresy URL a provede volání cloudap plugin získat soubor cookie PRT. |
| D | Modul plug-in CloudAP vytvoří soubor cookie PRT, přihlásí se pomocí klíče relace vázaného na čip TPM a odešle jej zpět nativnímu hostiteli klienta. Vzhledem k tomu, že soubor cookie je podepsán klíčem relace, nelze s ním manipulovat. |
| E | Nativní hostitel klienta vrátí tento soubor cookie PRT do prohlížeče, který jej bude obsahovat jako součást hlavičky požadavku s názvem x-ms-RefreshTokenCredential a tokeny požadavků z Azure AD. |
| F | Azure AD ověří podpis klíče relace na soubor cookie PRT, ověří nonce, ověří, zda je zařízení platné v tenantovi, a vydá token ID pro webovou stránku a šifrovanou relaci cookie pro prohlížeč. |

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží souvisejících s PRT najdete v článku [Řešení potíží s hybridní mandatní službou Azure Active Directory, která se připojila k zařízením s Windows 10 a Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).

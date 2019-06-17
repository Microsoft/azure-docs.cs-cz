---
title: Primární obnovovací Token (PRT) a Azure AD – Azure Active Directory
description: Co je role a jak se spravuje primární aktualizovat Token (PRT) v Azure Active Directory?
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
ms.openlocfilehash: 563ec5969a77dd01506270c3e864e00639a56eb1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110724"
---
# <a name="what-is-a-primary-refresh-token"></a>Co je primární aktualizovat Token?

Primární aktualizovat Token (PRT) je klíče artefakt ověřování Azure AD na Windows 10, iOS a androidem. Jedná JSON Web Token (JWT) speciálně vydat Microsoft první strany token zprostředkovatelů povolit jednotné přihlašování (SSO) v aplikacích používat na těchto zařízeních. V tomto článku budeme poskytovat podrobnosti ohledně fungování PRT vydané, používá a chránit na zařízení s Windows 10.

Tento článek předpokládá, že už chápete stavy jiné zařízení k dispozici ve službě Azure AD a jak jednotné přihlašování funguje ve Windows 10. Další informace o zařízení ve službě Azure AD, najdete v článku [co je Správa zařízení ve službě Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Klíčová terminologie a součásti

Následující součásti Windows sami hrát klíčovou roli při požadování a používání PRT:

* **Cloud poskytovatele ověřování** (CloudAP): CloudAP je poskytovatel moderní ověřování pro Windows přihlášení, které ověřuje uživatele k zařízení s Windows 10. CloudAP poskytuje modul plug-in rozhraní tuto identitu, kterou poskytovatelé můžete vytvořit na povolení ověřování pro Windows pomocí přihlašovacích údajů tohoto zprostředkovatele identity.
* **Správce účtu webové** (WAM): WAM je výchozí zprostředkovatele tokenu na zařízeních s Windows 10. Architektura modulu plug-in WAM také poskytuje tuto identitu poskytovatelé můžete sestavit a povolení jednotného přihlašování k aplikacím na tohoto zprostředkovatele identity.
* **Modul plug-in Azure AD CloudAP**: Plug-in konkrétní služby Azure AD postaveno na rozhraní framework CloudAP, které ověřuje přihlašovací údaje uživatele s Azure AD během přihlášení k Windows.
* **Modul plug-in Azure AD WAM**: Konkrétní modulu plug-in Azure AD postaveno na rozhraní framework WAM, která umožňuje jednotné přihlašování k aplikacím, které jsou závislé na službě Azure AD pro ověřování.
* **Dsreg**: Azure AD určité součásti na Windows 10, která zpracovává proces registrace zařízení pro všechny stavy zařízení.
* **Trusted Platform Module** (TPM): Čip TPM je hardwarová komponenta integrované do zařízení, který poskytuje funkce zabezpečení na základě hardwaru pro tajné klíče uživatelů a zařízení. Další podrobnosti najdete v článku [důvěryhodné Přehled technologie platformy modulu](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Co obsahuje PRT?

PRT obsahuje deklarace, obecně obsažené v libovolné obnovovací token Azure AD. Kromě toho jsou některé deklarací specifická pro zařízení součástí PRT. Jsou následující:

* **ID zařízení**: Objeví se PRT pro uživatele na konkrétní zařízení. Deklarace identity ID zařízení `deviceID` Určuje, PRT byl vydán pro uživatele na zařízení. Tato deklarace identity se později vydává tokeny získat přes PRT. Deklarace identity ID zařízení se používá k určení autorizace pro podmíněný přístup na základě stavu zařízení nebo dodržování předpisů.
* **Klíč relace**: Klíč relace je šifrovaný symetrický klíč, vygenerované službou Azure AD authentication vydané jako součást PRT. Klíč relace slouží jako důkaz vlastnictví při PRT slouží k získání tokenů pro jiné aplikace.

### <a name="can-i-see-whats-in-a-prt"></a>Můžete zobrazit, co je v PRT?

PRT je s neprůhledným objektem blob odeslané ze služby Azure AD, jejichž obsah nejsou známá, mají všechny součásti klienta. Nejde zobrazit, co se nachází uvnitř PRT.

## <a name="how-is-a-prt-issued"></a>Jak je vydána PRT?

Registrace zařízení je předpokladem pro ověřování na základě zařízení ve službě Azure AD. Objeví se PRT uživatelům jenom na registrovaná zařízení. Najdete podrobnější informace o registraci zařízení, najdete v článku [Windows Hello pro firmy a registrace zařízení](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Při registraci zařízení vygeneruje komponentu dsreg dvě sady párům kryptografických klíčů:

* Klíč zařízení (dkpub/dkpriv)
* Přenos klíče (tkpub/tkpriv)

Soukromé klíče jsou vázány na zařízení TPM, pokud má zařízení TPM platné a funkční, zatímco veřejné klíče se odesílají do služby Azure AD během procesu registrace zařízení. Tyto klíče se používají pro ověření stavu zařízení během PRT požadavky.

PRT je vydané během ověřování uživatele na zařízení s Windows 10 ve dvou scénářích:

* **Připojeno k Azure AD** nebo **připojená k hybridní službě Azure AD**: Když se uživatel přihlásí pomocí svých firemních přihlašovacích údajů, objeví se PRT během přihlašování Windows. PRT vydává se všechny přihlašovací údaje systému Windows 10 nepodporuje, například heslo a Windows Hello pro firmy. V tomto scénáři je modul plug-in Azure AD CloudAP primární autority pro PRT.
* **Registraci zařízení ve službě Azure AD**: Když uživatel přidá sekundární pracovní účet do jejich zařízení s Windows 10, objeví se PRT. Uživatele můžete přidat účet do Windows 10, dvěma různými způsoby –  
   * Přidání účtu prostřednictvím **používat tento účet všude na tomto zařízení** příkazový řádek po přihlášení k aplikaci (například Outlook)
   * Přidání účtu z **nastavení** > **účty** > **přístup do práce nebo do školy** > **Connect**

V těchto scénářích je modul plug-in Azure AD WAM primární autority pro PRT, protože Windows přihlašování nefunguje s tímto účtem služby Azure AD.

## <a name="what-is-the-lifetime-of-a-prt"></a>Co je životnost PRT?

Po vydání PRT je platný po dobu 14 dnů a průběžně se obnovuje tak dlouho, dokud uživatel aktivně používá zařízení.  

## <a name="how-is-a-prt-used"></a>Jak se používá PRT?

PRT používá dvě klíčové komponenty ve Windows:

* **Modul plug-in Azure AD CloudAP**: Modul plug-in Azure AD CloudAP během Windows přihlášení, požadavků PRT ze služby Azure AD pomocí pověření poskytnutých uživatelem. Také ukládá PRT povolit v mezipaměti přihlášení, když uživatel nemá přístup k připojení k Internetu.
* **Modul plug-in Azure AD WAM**: Když se uživatelé pokusí o přístup k aplikacím, používá modul plug-in Azure AD WAM PRT Pokud chcete povolit jednotné přihlašování ve Windows 10. Modul plug-in Azure AD WAM používá PRT požádat o aktualizace a přístupové tokeny pro aplikace, které využívají WAM pro žádosti o tokeny. Umožňuje také jednotné přihlašování v prohlížečích vložením PRT do požadavků prohlížeče. Je podporován prohlížeči jednotné přihlašování ve Windows 10 v Microsoft Edge (nativní) a Chrome (prostřednictvím rozšíření Office Online a účtů systému Windows 10).

## <a name="how-is-a-prt-renewed"></a>Jak PRT prodloužení?

PRT se obnovuje v dvěma způsoby:

* **Modul plug-in Azure AD CloudAP každé 4 hodiny**: Modul plug-in CloudAP obnoví PRT každé 4 hodiny během svého přihlašování Windows. Pokud uživatel nemá připojení k Internetu během této doby, modul plug-in CloudAP obnoví PRT po připojení zařízení k Internetu.
* **Modul plug-in Azure AD WAM během žádosti o tokeny aplikace**: Modul plug-in WAM umožňuje jednotné přihlašování na zařízení s Windows 10 tím, že umožňuje bezobslužné žádostí o token pro aplikace. Modul plug-in WAM obnovení můžete provést PRT během těchto žádostí o token dvěma různými způsoby:
   * Aplikace požádá WAM pro přístupový token tiše, ale není pro tuto aplikaci k dispozici žádný obnovovací token. V takovém případě WAM používá PRT k vyžádání tokenu pro aplikaci a získá zpět nové PRT v odpovědi.
   * Aplikace požádá WAM pro přístupový token, ale PRT je neplatný nebo Azure AD vyžaduje další ověření (třeba ověřování Azure Multi-Factor Authentication). V tomto scénáři WAM inicializuje interaktivního přihlášení by uživatel musel donutit nebo dodatečného ověření a nové PRT je vydané pro úspěšné ověření.

### <a name="key-considerations"></a>Klíčové aspekty

* PRT je pouze vydané a obnovit během ověřování nativní aplikací. PRT není obnoven nebo vydané během relace prohlížeče.
* Ve službě Azure AD připojený a hybridní zařízení připojená k Azure AD, modul plug-in CloudAP není primární autority pro PRT. Pokud obnovení PRT během na základě WAM žádosti o token PRT budou odeslána zpět do modulu plug-in CloudAP, kterým se ověří platnost PRT s Azure AD před přijetím.

## <a name="how-is-the-prt-protected"></a>Tom, jak je chráněný PRT?

PRT chráněn s vazbou na zařízení uživatele se přihlásil k. Azure AD a Windows 10 povolte ochranu PRT pomocí následujících metod:

* **Při prvním přihlášení**: Při prvním přihlášení PRT vydává klíče zařízení kryptograficky vygeneruje při registraci zařízení pomocí žádosti o podepsání. Klíč zařízení je na zařízení s čipem TPM platné a funkční, zabezpečené pomocí čipu TPM brání škodlivým přístup. PRT není vystaven, pokud nelze ověřit podpis odpovídající klíče zařízení.
* **Během žádosti o tokeny a obnovení**: Při vydání PRT Azure AD také problémy šifrovaný klíč relace do zařízení. Je zašifrovaný pomocí klíče dopravu (tkpub) generovaného a odeslané do služby Azure AD jako součást registrace zařízení. Tento klíč relace mohou ho dešifrovat jenom přenos privátní klíč (tkpriv) zabezpečené pomocí čipu TPM. Klíč relace je klíč důkaz vlastnictví (POP) pro všechny požadavky odeslané do služby Azure AD.  Klíč relace je také chráněný čipem TPM a žádné jiné součásti operačního systému k němu máte přístup. Token požadavky nebo požadavky na obnovení certifikátu PRT bezpečně podepsány tento klíč relace prostřednictvím čipu TPM a proto nemohlo být manipulováno s. Azure AD, skončí platnost všech žádostí ze zařízení, které nejsou podepsané společností odpovídajícím klíčem relace.

Díky zabezpečení tyto klíče s čipem TPM, nelze útočníky ukrást klíče ani znovu přehrát PRT jinde jako čip TPM není přístupný, i v případě, že útočník má fyzicky zařízení.  Proto pomocí čipu TPM výrazně zvyšuje zabezpečení Azure AD připojeno, připojená k Azure AD, hybridní a Azure AD registrované zařízení proti zcizení přihlašovacích údajů. Doporučená verze pro všechny scénáře registrace zařízení služby Azure AD na Windows 10 je pro výkon a spolehlivost, čip TPM 2.0.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Jak se aplikace tokeny a soubory cookie v prohlížeči chráněná?

**Tokeny aplikací**: Pokud aplikace požaduje token prostřednictvím WAM, problémy s aktualizační token a přístupového tokenu Azure AD. WAM ale pouze vrátí přístupový token do aplikace a zabezpečuje obnovovací token v mezipaměti tím, že šifruje klíčem uživatele dat ochrany aplikace programovací rozhraní (DPAPI). WAM bezpečně používá token obnovení žádosti o podepsání s klíčem relace vydávat další přístupové tokeny. Klíč rozhraní DPAPI je zabezpečena pomocí Azure AD na základě symetrický klíč v samotné služby Azure AD. Pokud zařízení potřebuje k dešifrování uživatelský profil s klíčem rozhraní DPAPI, Azure AD poskytuje klíč rozhraní DPAPI zašifrovaný klíčem relace, které modul plug-in CloudAP požadavků TPM k dešifrování. Tato funkce zajišťuje konzistenci při zabezpečení obnovovací tokeny a implementovat vlastní mechanismus ochrany aplikací se vyhnete.  

**Soubory cookie v prohlížeči**: Ve Windows 10, Azure AD podporuje prohlížeče jednotné přihlašování v aplikaci Internet Explorer a Microsoft Edgem nativně, nebo v prohlížeči Google Chrome prostřednictvím rozšíření účtů pro Windows 10. Zabezpečení je integrované nejen k ochraně souborů cookie, ale také koncové body, na které se odesílají soubory cookie. Soubory cookie prohlížeče jsou chráněny stejným způsobem jako PRT je s využitím klíče relace přihlášení a ochraně souborů cookie.

Když uživatel zahájí interakce prohlížeče, prohlížeč (nebo rozšíření) vyvolá COM nativního klienta systému hostitele. Hostitel nativního klienta zajistí, že na stránce bude z jedné z povolených domén. V prohlížeči může poslat že další parametry nativního klienta hostovat, včetně hodnotu nonce, ale hostitel nativního klienta zaručuje ověření názvu hostitele. Hostitel nativního klienta požadavků souboru cookie PRT CloudAP modulu plug-in, který vytvoří a zaregistruje ho s klíčem relace ochranou čipem TPM. Protože soubor cookie PRT je podepsán klíčem relace, nemohlo být manipulováno s. Tento PRT soubor cookie je zahrnuta v hlavičce žádosti pro službu Azure AD k ověření zařízení, které je pocházející z. Pokud používáte prohlížeč Chrome, můžete vyvolat pouze rozšíření explicitně definované v manifestu Nativní klient systému hostitele to brání rozšíření libovolného z těchto požadavků. Jakmile se Azure AD ověří PRT souboru cookie, vydá souboru cookie relace prohlížeče. Tento soubor cookie relace obsahuje také stejný klíč relace vydávané PRT. Během následujících požadavků se ověří klíč relace efektivně vazby soubor cookie do zařízení a brání riziko odjinud.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Když PRT získat deklaraci identity MFA?

PRT můžete získat deklarace identity služby Multi-Factor authentication (MFA) v konkrétních scénářích. Když PRT založený na vícefaktorovém ověřování umožňuje požádat o tokeny pro aplikace, deklarace identity MFA se přenesou do těchto tokenů aplikace. Tato funkce poskytuje bezproblémové prostředí pro uživatele tím, že zabráníte ověřovacím testem MFA pro každou aplikaci, která vyžaduje. PRT můžete získat deklaraci identity MFA následujícími způsoby:

* **Přihlaste se pomocí Windows Hello pro firmy**: Windows Hello pro firmy nahradí hesla a používá kryptografické klíče pro zajištění silného dvojúrovňového ověřování. Windows Hello pro firmy je specifické pro uživatele na zařízení a samotného poskytování vyžadují vícefaktorové ověřování. Když se uživatel přihlásí pomocí Windows Hello pro firmy, získá uživatele PRT deklaraci identity MFA. Tento scénář platí také pro uživatele přihlášení pomocí čipové karty v případě ověřování pomocí čipové karty vytvoří deklaraci identity MFA ze služby AD FS.
   * Jako Windows Hello pro firmy se považuje za ověřování službou Multi-Factor Authentication, deklarace identity MFA je aktualizována při aktualizaci PRT samotný tak dobu trvání MFA bude průběžně rozšířit, když uživatelé přihlásit pomocí WIndows Hello pro firmy
* **Vícefaktorové ověřování při interaktivním přihlášení k WAM**: Během žádosti o token prostřednictvím WAM Pokud je uživatel vyžadovat vícefaktorové ověřování pro přístup k aplikaci, PRT, který je obnoven během tato interakce je vytištěný s deklaraci identity MFA.
   * Deklarace identity MFA. v takovém případě není průběžně aktualizován, takže doba trvání MFA je založen na dobu života nastavenu na adresář.
* **Vícefaktorové ověřování při registraci zařízení**: Pokud správce nakonfiguroval nastavení jejich zařízení ve službě Azure AD [vyžadovat vícefaktorové ověřování k registraci zařízení](device-management-azure-portal.md#configure-device-settings), uživatel musí udělat k dokončení registrace MFA. Během tohoto procesu PRT, který se vydá uživateli má deklaraci identity MFA při registraci. Tato funkce platí jenom pro uživatele, který nebyl operace spojení, ne na jiných uživatelů, kteří se přihlaste se na toto zařízení.
   * Podobně jako u WAM interaktivní přihlášení, deklarace identity MFA není průběžně aktualizovány, tak dobu trvání MFA je založen na dobu života nastavenu na adresář.

Windows 10 udržuje seznam oddílů PRTs pro každý přihlašovací údaje. Proto je PRT pro každou z Windows Hello pro firmy, heslo nebo čipová karta. Toto rozdělení zajišťuje, že deklarace identity MFA jsou izolované založené na přihlašovacích údajích používá a ne promíchají při žádosti o tokeny.

## <a name="how-is-a-prt-invalidated"></a>Jak PRT zneplatněna?

PRT zneplatněna v následujících scénářích:

* **Neplatný uživatel**: Pokud uživatel je odstraněn nebo zakázaný ve službě Azure AD, jejich PRT zneplatněna a nelze použít k získání tokenů pro aplikace. Pokud odstraněný nebo zakázaný uživatel se již přihlášení k zařízení, než, uložená v mezipaměti přihlášení typicky zaznamenávají je se změnami, dokud CloudAP si je vědoma jejich neplatném stavu. Jakmile CloudAP zjistí, že uživatel je neplatný, blokuje následná přihlášení. Neplatný uživatel je zablokována automaticky, z přihlášení na nová zařízení, které nemají své přihlašovací údaje uložené v mezipaměti.
* **Neplatné zařízení**: Pokud zařízení se odstraní nebo je zakázaný ve službě Azure AD, PRT získat na tomto zařízení je zrušena a nelze použít k získání tokenů pro jiné aplikace. Pokud uživatel je již přihlášení k neplatné zařízení, může i nadále Uděláte to tak. Ale nejsou zneplatněny všech tokenů na zařízení a uživatele nemá jednotného přihlašování k žádným prostředkům z těchto zařízení.
* **Změna hesla**: Po změně hesla uživatelem, PRT získali díky předchozí hesla je neplatná ve službě Azure AD. Výsledky v uživatelů, získávání nových PRT změny hesla. Toto zrušení může dojít, dvěma různými způsoby:
   * Pokud se uživatel přihlásí do Windows pomocí nového hesla, CloudAP odstraní staré PRT a vyžaduje Azure AD, aby vydání nové PRT pomocí nového hesla. Pokud uživatel nemá připojení k Internetu, nové heslo nelze ověřit, Windows může vyžadovat, aby uživatel zadal svoje staré heslo.
   * Pokud uživatel má přihlášení svoje staré heslo nebo po přihlášení k Windows změnit své heslo, staré PRT se používá pro všechny založené na WAM žádostí o token. V tomto scénáři bude uživatel vyzván při žádosti o token WAM donutit k a vydává nové PRT.
* **Problémy s TPM**: V některých případech můžete zařízení TPM falter nebo selhání, což vede k inaccessibility klíče, které jsou zabezpečené pomocí čipu TPM. Zařízení je v tomto případě nepodporující získávání PRT nebo žádosti o tokeny pomocí existující PRT, jak ho nemůže prokázat získáním kryptografických klíčů. V důsledku toho všechny existující PRT zneplatněna službou Azure AD. Když Windows 10 zjistí chybu, spustí tok obnovení znovu zaregistrovat zařízení pomocí nových kryptografických klíčů. S hybridní služby Azure Ad join, stejně jako počáteční registraci obnovení se stane bezobslužném režimu bez zásahu uživatele. Pro zařízení registrovaných v Azure připojené k AD nebo Azure AD, obnovení je třeba provést uživatel, který má oprávnění správce v zařízení. V tomto scénáři obnovení toku inicializuje řádku Windows, který k úspěšnému obnovení zařízení uživatele.

## <a name="detailed-flows"></a>Podrobné toků

Následující obrázky znázorňují základní podrobnosti o vydání, prodlužuje se platnost a pomocí PRT požádat o přístupový token pro aplikaci. Kromě toho tyto kroky také popisují, jak výše uvedené bezpečnostní mechanismy se používají během těchto interakcí.

### <a name="prt-issuance-during-first-sign-in"></a>Vystavení PRT při prvním přihlášení

![Vystavení PRT při prvním přihlášení v podrobné toku](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> Ve službě Azure AD zařízení připojená k, tato výměna se stane synchronně na problém PRT předtím, než uživatel může přihlásit k Windows. V hybridním připojená k Azure AD zařízení, v místním Active Directory je primární autoritu. Dokud při vystavení PRT probíhá asynchronně, můžete získat lístek TGT se můžete přihlásit, tedy pouze čekání uživatele. Tento scénář se nevztahují na zařízení registrováno v Azure AD jako přihlášení nepoužívá přihlašovacích údajů Azure AD.

| Krok | Popis |
| :---: | --- |
| A | Uživatel zadá své heslo v uživatelské rozhraní pro přihlášení. LogonUI předává do LSA, což na oplátku předává interně CloudAP přihlašovací údaje ve vyrovnávací paměti ověřování. CloudAP předá tento požadavek CloudAP modulu plug-in. |
| B | Modul plug-in CloudAP zahájí požadavek na zjišťování sféry k identifikaci zprostředkovatele identity pro uživatele. Pokud tenanta uživatele má instalační program zprostředkovatele federace, Azure AD vrací výměny metadat federační zprostředkovatel koncový bod (MEX). Pokud ne, Azure AD vrátí, zda jde o spravovaného uživatele oznamující, že tento uživatel může ověřit pomocí služby Azure AD. |
| C | Pokud je uživatel spravován, dostane CloudAP hodnotu nonce ze služby Azure AD. Pokud uživatele je Federovaná, modul plug-in CloudAP vyžádá tokenu SAML zprostředkovatele federace s přihlašovacími údaji uživatele. Jakmile přijme, tokenu SAML, vyžaduje hodnotu nonce z Azure AD. |
| D | Modul plug-in CloudAP vytvoří požadavek na ověření pomocí přihlašovacích údajů uživatele, hodnota nonce a obor zprostředkovatele, podepíše žádost s klíčem zařízení (dkpriv) a odesílá je do služby Azure AD. Ve federovaném prostředí, používá modul plug-in CloudAP SAML token vrácený federační zprostředkovatel místo na uživatele, se přihlašovací údaje. |
| E | Azure AD ověří přihlašovací údaje uživatele, hodnota nonce, a zařízení podpis, ověřuje, že zařízení je platný v tenantovi a šifrované PRT. Spolu s PRT Azure AD také vydává symetrický klíč, s názvem šifrované službou Azure AD pomocí přenosu klíče (tkpub) klíč relace. Kromě toho klíč relace se také vloží do PRT. Tento klíč relace slouží jako klíč důkaz vlastnictví (PoP) pro následné požadavky se PRT. |
| F | Modul plug-in CloudAP předá CloudAP šifrovaný klíč PRT a relace. CloudAP požádat o čipu TPM k dešifrování pomocí klíče přenosu (tkpriv) klíč relace a znovu zašifrovat pomocí čipu TPM vlastní klíč. CloudAP ukládá šifrovaný klíč relace v mezipaměti spolu s PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>Obnovení PRT v následná přihlášení

![Obnovení PRT v následná přihlášení](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Krok | Popis |
| :---: | --- |
| A | Uživatel zadá své heslo v uživatelské rozhraní pro přihlášení. LogonUI předává do LSA, což na oplátku předává interně CloudAP přihlašovací údaje ve vyrovnávací paměti ověřování. CloudAP předá tento požadavek CloudAP modulu plug-in. |
| B | Pokud uživatel má dříve přihlášení pro uživatele, inicializuje Windows do mezipaměti, přihlaste se a ověří přihlašovací údaje pro přihlášení uživatele. Každé 4 hodiny, modul plug-in CloudAP PRT obnovení spouští asynchronně. |
| C | Modul plug-in CloudAP zahájí požadavek na zjišťování sféry k identifikaci zprostředkovatele identity pro uživatele. Pokud tenanta uživatele má instalační program zprostředkovatele federace, Azure AD vrací výměny metadat federační zprostředkovatel koncový bod (MEX). Pokud ne, Azure AD vrátí, zda jde o spravovaného uživatele oznamující, že tento uživatel může ověřit pomocí služby Azure AD. |
| D | Pokud uživatele je Federovaná, modul plug-in CloudAP vyžádá tokenu SAML zprostředkovatele federace s přihlašovacími údaji uživatele. Jakmile přijme, tokenu SAML, vyžaduje hodnotu nonce z Azure AD. Pokud je uživatel spravován, CloudAP přímo získat hodnotu nonce ze služby Azure AD. |
| E | Modul plug-in CloudAP vytvoří požadavek na ověření pomocí přihlašovacích údajů uživatele, hodnota nonce a existující PRT, podepíše žádost s klíčem relace a odesílá je do služby Azure AD. Ve federovaném prostředí, používá modul plug-in CloudAP SAML token vrácený federační zprostředkovatel místo na uživatele, se přihlašovací údaje. |
| F | Azure AD ověří podpis klíče relace porovnáním s klíč relace, které jsou součástí PRT, ověří hodnotu nonce a ověřuje, že zařízení je platný v tenantovi a vydá novou PRT. Jak je vidět před PRT znovu doplněná zašifrovaný klíčem přenosu (tkpub) klíč relace. |
| G | Modul plug-in CloudAP předá CloudAP šifrovaný klíč PRT a relace. CloudAP požadavky TPM k dešifrování pomocí klíče přenosu (tkpriv) klíč relace a znovu zašifrovat pomocí čipu TPM vlastní klíč. CloudAP ukládá šifrovaný klíč relace v mezipaměti spolu s PRT. |

### <a name="prt-usage-during-app-token-requests"></a>Využití PRT během žádosti o tokeny aplikací

![Využití PRT během žádosti o tokeny aplikací](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Krok | Popis |
| :---: | --- |
| A | Aplikace (například Outlook, OneNote atd.) zahájí žádost o token do WAM. WAM, pak požádá modul plug-in Azure AD WAM ke zpracování požadavku na token. |
| B | Pokud obnovovací token aplikace je již k dispozici, modul plug-in Azure AD WAM se použije k vyžádání tokenu přístupu. Předložit doklad o zařízení vazby modulu plug-in WAM podepíše žádost s klíčem relace. Azure AD ověří klíč relace a vydá přístupový token a nového tokenu obnovení pro aplikace, které jsou šifrované pomocí klíče relace. Modul plug-in WAM požadavků modulu plug-in Asie a Tichomoří cloudu k dešifrování tokenů, který zase vyžaduje TPM k dešifrování pomocí klíče relace, což vede k modulu plug-in WAM získávání oba tokeny. V dalším kroku modulu plug-in WAM poskytuje přístupový token do aplikace, zatímco obnovovací token pomocí rozhraní DPAPI znovu zašifruje a ukládá ho do své vlastní mezipaměti  |
| C |  Pokud obnovovací token pro aplikaci není k dispozici, používá modul plug-in Azure AD WAM PRT k vyžádání tokenu přístupu. Modul plug-in WAM předložit doklad o vlastnictví podepíše žádost obsahující PRT s klíčem relace. Azure AD ověří podpis klíče relace porovnáním s klíč relace, které jsou součástí PRT, ověří, jestli zařízení je platný, vystaví přístupový token a aktualizační token pro aplikaci. Kromě toho Azure AD můžete vydat nové PRT (podle cyklu aktualizace), všechny z nich šifrované pomocí klíče relace. |
| D | Modul plug-in WAM požadavků modulu plug-in Asie a Tichomoří cloudu k dešifrování tokenů, který zase vyžaduje TPM k dešifrování pomocí klíče relace, což vede k modulu plug-in WAM získávání oba tokeny. V dalším kroku WAM modulu plug-in poskytuje přístupový token do aplikace, zatímco obnovovací token pomocí rozhraní DPAPI znovu zašifruje a ukládá ho do své vlastní mezipaměti. Modul plug-in WAM použije obnovovací token do budoucna pro tuto aplikaci. Modul plug-in WAM taky poskytuje zpět nové PRT k plug-in přístupový bod v cloudu, který ověřuje PRT s Azure AD před aktualizací ve své vlastní mezipaměti. Modul plug-in přístupový bod cloudu pomocí nové PRT do budoucna. |
| E | WAM poskytuje nově vydaný přístupový token k WAM, který zase obsahuje zpět na volající aplikace|

### <a name="browser-sso-using-prt"></a>Prohlížeč jednotné přihlašování pomocí PRT

![Prohlížeč jednotné přihlašování pomocí PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Krok | Popis |
| :---: | --- |
| A | Uživatel se přihlásí do Windows pomocí svých přihlašovacích údajů k získání PRT. Když uživatel otevře prohlížeč, prohlížeč (nebo rozšíření) načte z registru adresy URL. |
| B | Když uživatel otevře přihlašovací adresa URL služby Azure AD, prohlížeče nebo rozšíření ověří adresu URL na základě těch získal z registru. Pokud se shodují, prohlížeč vyvolá Nativní klient systému hostitele pro získání tokenu. |
| C | Hostitel nativního klienta ověří, že adresy URL patří ke zprostředkovatelům identity společnosti Microsoft (účet Microsoft nebo Azure AD), extrahuje hodnotu nonce odeslané z adresy URL a provede volání modulu plug-in CloudAP zobrazíte PRT souboru cookie. |
| D | Modul plug-in CloudAP vytvoření souboru cookie PRT, přihlaste se pomocí klíče relace vázané na čipu TPM a pak zasílají zpět Nativní klient systému hostitele. Protože soubor cookie je podepsán klíčem relace, nemohlo být manipulováno s. |
| E | Hostitel nativního klienta vrátí tento soubor cookie PRT do prohlížeče, která bude obsahovat ho jako součást hlavičky x-ms-RefreshTokenCredential a žádosti o tokeny volat z Azure AD. |
| F | Azure AD ověří podpis klíče relace v souboru cookie PRT, ověří hodnotu nonce, ověří, že zařízení je platný v tenantovi a vydá token ID pro webovou stránku a soubor cookie šifrované relace prohlížeče. |

## <a name="next-steps"></a>Další postup

Další informace o řešení problémů souvisejících se PRT, najdete v článku [zařízení s Windows 10 a Windows serveru 2016 připojená k hybridní Poradce při potížích s Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).

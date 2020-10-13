---
title: Primární obnovovací token (PRT) a Azure AD – Azure Active Directory
description: Jaká je role a jak spravujeme primární obnovovací token (PRT) v Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 000bc150b1a4addb4b68bd86b8d72524ec1015fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91450419"
---
# <a name="what-is-a-primary-refresh-token"></a>Co je primární obnovovací token?

Primární obnovovací token (PRT) je klíčový artefakt ověřování Azure AD na zařízeních s Windows 10, iOS a Androidem. Jedná se o JSON Web Token (JWT) vydaný zprostředkovatelům tokenů Microsoft First stran, který umožňuje jednotné přihlašování (SSO) v aplikacích používaných na těchto zařízeních. V tomto článku poskytneme podrobné informace o tom, jak se na zařízeních s Windows 10 vydává, používá a chrání PRT.

V tomto článku se předpokládá, že už rozumíte různým stavům zařízení, které jsou dostupné v Azure AD, a jak jednotné přihlašování funguje ve Windows 10. Další informace o zařízeních v Azure AD najdete v článku [co je Správa zařízení v Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Klíčová terminologie a komponenty

Následující součásti systému Windows hrají klíčovou roli při vyžádání a používání PRT:

* **Zprostředkovatel ověřování v cloudu** (CloudAP): CloudAP je moderní zprostředkovatel ověřování pro přihlášení k systému Windows, který ověřuje uživatele přihlašování k zařízení s Windows 10. CloudAP poskytuje architekturu modulu plug-in, kterou můžou poskytovatelé identity vytvořit, aby mohli používat ověřování v systému Windows pomocí přihlašovacích údajů daného zprostředkovatele identity.
* **Správce webového účtu** (WAM): na zařízeních s Windows 10 je výchozí zprostředkovatel tokenů služby WAM. WAM také poskytuje rozhraní modulu plug-in, které mohou poskytovatelé identity sestavit a povolit jednotné přihlašování k aplikacím, které se spoléhají na tohoto zprostředkovatele identity.
* **Modul plug-in Azure AD CloudAP**: modul plug-in specifický pro Azure AD založený na rozhraní CloudAP, který ověřuje přihlašovací údaje uživatelů pomocí Azure AD během přihlašování Windows.
* **Modul plug-in Azure AD WAM**: modul plug-in Azure AD založený na rozhraní WAM, který umožňuje jednotné přihlašování k aplikacím, které se spoléhají na Azure AD pro ověřování.
* **Dsreg**: specifická součást služby Azure AD ve Windows 10, která zpracovává proces registrace zařízení pro všechny stavy zařízení.
* Čip TPM ( **Trusted Platform Module** ): čipem TPM je hardwarová komponenta integrovaná do zařízení, která poskytuje hardwarové funkce zabezpečení pro tajné klíče uživatelů a zařízení. Další podrobnosti najdete v článku [Přehled technologie Trusted Platform Module](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Co PRT obsahuje?

PRT obsahuje deklarace, které se obecně nacházejí v jakémkoli obnovovacím tokenu Azure AD. Kromě toho jsou součástí PRT některé deklarace identity specifické pro zařízení. Jsou to tyto:

* **ID zařízení**: PRT se vydá uživateli na konkrétním zařízení. Deklarace ID zařízení `deviceID` určuje zařízení, na kterém se PRT uživateli vystavil. Tato deklarace identity se později vydává tokeny získanými prostřednictvím PRT. Deklarace ID zařízení se používá k určení autorizace podmíněného přístupu na základě stavu zařízení nebo dodržování předpisů.
* **Klíč relace**: klíč relace je šifrovaný symetrický klíč generovaný službou ověřování Azure AD vydaný jako součást PRT. Klíč relace funguje jako důkaz o vlastnictví, když se PRT používá k získání tokenů pro ostatní aplikace.

### <a name="can-i-see-whats-in-a-prt"></a>Můžu se podívat, co je v PRT?

PRT je neprůhledný objekt BLOB odeslaný z Azure AD, jehož obsah není známý pro žádné součásti klienta. Nemůžete vidět, co se nachází uvnitř PRT.

## <a name="how-is-a-prt-issued"></a>Jak se vystavuje PRT?

Registrace zařízení je předpokladem pro ověřování na základě zařízení ve službě Azure AD. PRT se vydává uživatelům jenom na registrovaných zařízeních. Další podrobné informace o registraci zařízení najdete v článku [Windows Hello pro firmy a registrace zařízení](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Během registrace zařízení generuje komponenta dsreg dvě sady párů kryptografických klíčů:

* Klíč zařízení (dkpub/dkpriv)
* Transportní klíč (tkpub/tkpriv)

Privátní klíče jsou svázané s čipem TPM zařízení, pokud má zařízení platný a funkční čip TPM, zatímco veřejné klíče se během procesu registrace zařízení odesílají do služby Azure AD. Tyto klíče se používají k ověření stavu zařízení během PRT požadavků.

PRT se vydává během ověřování uživatelů na zařízení s Windows 10 ve dvou scénářích:

* Připojené ke službě **Azure AD** nebo k **hybridní službě Azure AD připojené**: během přihlašování uživatele pomocí přihlašovacích údajů organizace se vydává PRT při přihlášení k systému Windows. PRT se vydá se všemi podporovanými přihlašovacími údaji Windows 10, třeba heslem a Windows Hello pro firmy. V tomto scénáři je modulem plug-in Azure AD CloudAP primární autoritou pro PRT.
* **Zařízení registrovaná v Azure AD**: PRT se vydá, když uživatel přidá sekundární pracovní účet do svého zařízení s Windows 10. Uživatelé můžou přidat účet do Windows 10 dvěma různými způsoby –  
   * Přidání účtu prostřednictvím tohoto účtu po přihlášení k aplikaci (například Outlook) prostřednictvím výzvy **použít tento účet všude v tomto zařízení**
   * Přidání účtu z **Nastavení**  >  **účty**  >  **přístup do práce nebo do školy**  >  **připojit**

Ve scénářích zařízení registrovaných v Azure AD je modul plug-in Azure AD WAM primární autoritou pro PRT, protože k tomuto účtu Azure AD neprobíhá přihlášení k Windows.

> [!NOTE]
> Zprostředkovatelé identity od jiných výrobců potřebují podporovat protokol WS-Trust, aby bylo možné povolit vystavování PRT na zařízeních s Windows 10. Bez WS-Trust se PRT nedá vystavit uživatelům na zařízeních připojených k hybridní službě Azure AD nebo na zařízeních připojených k Azure AD. V AD FS se vyžadují jenom koncové body usernamemixed. AD FS/Services/Trust/2005/windowstransport a AD FS/Services/Trust/13/windowstransport by měly být povolené jenom jako intranetové koncové body a **nesmí být zveřejněné** jako extranetové koncové body prostřednictvím služby Proxy webových aplikací.

## <a name="what-is-the-lifetime-of-a-prt"></a>Jaká je životnost PRT?

Po vydání je PRT platný po dobu 14 dnů a průběžně se prodlouží, dokud uživatel zařízení aktivně používá.  

## <a name="how-is-a-prt-used"></a>Jak se používá PRT?

PRT používá dvě klíčové komponenty v systému Windows:

* **Modul plug-in Azure AD CloudAP**: během přihlašování Windows vyžádá modul plug-in Azure AD CloudAP PRT z Azure AD s použitím přihlašovacích údajů poskytnutých uživatelem. Také ukládá do mezipaměti PRT, aby povoloval přihlášení do mezipaměti, když uživatel nemá přístup k Internetu.
* **Modul plug-in Azure AD WAM**: když se uživatelé pokusí získat přístup k aplikacím, modul plug-in Azure AD služby WAM použije PRT k povolení jednotného přihlašování ve Windows 10. Modul plug-in Azure AD WAM používá PRT k vyžádání aktualizací a přístupových tokenů pro aplikace, které využívají WAM pro žádosti o tokeny. Umožňuje také jednotné přihlašování v prohlížečích vložením PRT do požadavků prohlížeče. Jednotné přihlašování prohlížeče ve Windows 10 je podporované v Microsoft Edge (nativně) a Chrome (prostřednictvím [účtů Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en) nebo rozšíření [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en) ).

## <a name="how-is-a-prt-renewed"></a>Jak se PRT obnoví?

PRT se obnovuje dvěma různými způsoby:

* **Modul plug-in Azure AD CloudAP každé 4 hodiny**: modul plug-in CloudAP obnovuje PRT každé 4 hodiny během přihlašování Windows. Pokud uživatel nemá během této doby připojení k Internetu, modul plug-in CloudAP obnoví PRT po připojení zařízení k Internetu.
* **Modul plug-in Azure AD WAM během žádosti o tokeny aplikace**: modul plug-in WAM umožňuje jednotné přihlašování na zařízeních s Windows 10 povolením požadavků na tiché tokeny Modul plug-in WAM může obnovit PRT během těchto požadavků na tokeny dvěma různými způsoby:
   * Aplikace požaduje u přístupového tokenu služby WAM v tichém režimu, ale pro tuto aplikaci není k dispozici žádný obnovovací token. V tomto případě WAM používá PRT k vyžádání tokenu pro aplikaci a vrátí nový PRT v odpovědi.
   * Aplikace požaduje službu WAM pro přístupový token, ale PRT je neplatná nebo služba Azure AD vyžaduje další autorizaci (například Azure Multi-Factor Authentication). V tomto scénáři vytvoří WAM interaktivní přihlášení, které vyžaduje opětovné ověření uživatele nebo poskytnutí dalšího ověřování a nové PRT se vydá po úspěšném ověření.

V prostředí AD FS není pro obnovení PRT nutná přímá čára pohledu na řadič domény. Obnovení PRT vyžaduje, aby byl na proxy serveru povolen pouze koncový bod/ADFS/Services/Trust/2005/usernamemixed a/ADFS/Services/Trust/13/usernamemixed pomocí protokolu WS-Trust.

Koncové body přenosů systému Windows se vyžadují pro ověřování hesla, jenom když se změní heslo, ne pro obnovení PRT.

### <a name="key-considerations"></a>Klíčové aspekty

* PRT se vydává a obnovuje se jenom během nativního ověřování aplikace. PRT se během relace prohlížeče neobnovuje ani nevydá.
* Modul plug-in CloudAP je pro PRT primární autoritou připojená k Azure AD a zařízením připojeným k hybridní službě Azure AD. Pokud se během žádosti o token založený na WAM obnoví PRT, PRT se pošle zpátky do modulu plug-in CloudAP, který před přijetím ověří platnost PRT ve službě Azure AD.

## <a name="how-is-the-prt-protected"></a>Jak je ochrana PRT chráněná?

PRT je chráněn pomocí vazby na zařízení, ke kterému se uživatel přihlásil. Azure AD a Windows 10 umožňují ochranu PRT pomocí následujících metod:

* **Při prvním přihlášení**: při prvním přihlášení je PRT vydaný pomocí podepisování požadavků pomocí klíče zařízení kryptograficky generované při registraci zařízení. V zařízení s platným a fungujícím čipem TPM je klíč zařízení zabezpečený čipem TPM, který brání jakémukoli škodlivému přístupu. PRT se nevydá, pokud se odpovídající podpis klíče zařízení nedá ověřit.
* **Během žádosti a obnovení tokenu**: když se vydá PRT, Azure AD taky vystaví šifrovaný klíč relace do zařízení. Je zašifrovaný pomocí veřejného přenosového klíče (tkpub) vygenerovaného a odeslaného do Azure AD jako součást registrace zařízení. Tento klíč relace lze dešifrovat pouze pomocí privátního transportního klíče (tkpriv) zabezpečeného čipem TPM. Klíč relace je klíč kontrolního bodu (POP) pro všechny požadavky odeslané do služby Azure AD.  Klíč relace je také chráněn čipem TPM a žádná jiná součást operačního systému k ní nemá přístup. Požadavky na tokeny nebo požadavky na obnovení PRT jsou zabezpečeny podepsány tímto klíčem relace prostřednictvím čipu TPM, a proto nemohou být úmyslně poškozeny. Služba Azure AD zruší platnost všech požadavků ze zařízení, které nejsou podepsané odpovídajícím klíčem relace.

Díky zabezpečení těchto klíčů s čipem TPM nemohou škodlivé aktéri ukrást klíče ani přehrávat PRT jinde, protože čip TPM je nepřístupný, i když má útočník fyzické vlastnictví zařízení.  Proto použití čipu TPM značně vylepšuje zabezpečení připojených k Azure AD, připojené k hybridní službě Azure AD a zařízením registrovaným v Azure AD proti krádeži přihlašovacích údajů. Pro výkon a spolehlivost je pro všechny scénáře registrace zařízení Azure AD ve Windows 10 doporučená verze TPM 2,0.

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Jak jsou tokeny aplikace a soubory cookie prohlížeče chráněné?

**Tokeny aplikace**: když aplikace požaduje token prostřednictvím služby WAM, služba Azure AD vydá obnovovací token a přístupový token. Služba WAM ale vrátí přístupový token do aplikace a zabezpečí token aktualizace ve své mezipaměti tím, že ho zašifruje pomocí klíče rozhraní aplikačního programovacího rozhraní (DPAPI) pro ochranu dat uživatele. Služba WAM bezpečně používá aktualizační token tím, že podepisuje žádosti s klíčem relace k vydávání dalších přístupových tokenů. Klíč rozhraní DPAPI je zabezpečený pomocí symetrického klíče založeného na službě Azure AD ve službě Azure AD. Když zařízení potřebuje dešifrovat profil uživatele s klíčem DPAPI, Azure AD poskytne klíč rozhraní DPAPI zašifrovaný klíčem relace, který CloudAP plugin požaduje dešifrování čipem TPM. Tato funkce zaručuje konzistenci v zabezpečení aktualizačních tokenů a zabraňuje aplikacím implementací vlastních mechanismů ochrany.  

**Soubory cookie v prohlížeči**: ve Windows 10 podporuje Azure AD jednotné přihlašování prohlížeče v Internet Exploreru a Microsoft Edge nativně nebo v Google Chrome prostřednictvím rozšíření účtů Windows 10. Zabezpečení je postavené nejen k ochraně souborů cookie, ale také k koncovým bodům, na které se soubory cookie odesílají. Soubory cookie prohlížeče jsou chráněny stejným způsobem jako PRT, protože využívají klíč relace k podepisování a ochraně souborů cookie.

Když uživatel zahájí interakci s prohlížečem, prohlížeč (nebo rozšíření) vyvolá hostitele nativního klienta modelu COM. Hostitel nativního klienta zajišťuje, aby se stránka nastavila z jedné z povolených domén. Prohlížeč může odeslat jiné parametry do nativního hostitele klienta, včetně hodnoty nonce, ale nativní hostitel klienta garantuje ověření názvu hostitele. Nativní klient klienta požaduje PRT-cookie z modulu plug-in CloudAP, který vytvoří a podepíše ho pomocí klíče relace chráněného čipem TPM. Vzhledem k tomu, že PRT-cookie je podepsán klíčem relace, nemůže být poškozen. Tento PRT soubor cookie je obsažen v hlavičce žádosti pro Azure AD za účelem ověření zařízení, ze kterého pochází. Pokud používáte prohlížeč Chrome, může to vyvolat pouze rozšíření explicitně definované v manifestu nativního klientského hostitele, které brání libovolným rozšířením v provádění těchto požadavků. Jakmile Azure AD ověří soubor cookie PRT, vydá do prohlížeče soubor cookie relace. Tento soubor cookie relace obsahuje také stejný klíč relace vydaný pomocí PRT. Během následujících požadavků se klíč relace efektivně ověří a naváže se soubor cookie k zařízení a zabrání se dalšímu hraní z jiných míst.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Kdy PRT získá deklaraci MFA?

PRT může v určitých scénářích získat deklaraci MFA (Multi-Factor Authentication). Pokud se pro žádosti o tokeny pro aplikace používá PRT založené na MFA, přenese se deklarace MFA na tyto tokeny aplikace. Tato funkce poskytuje uživatelům bezproblémové prostředí tím, že pro každou aplikaci, která ji vyžaduje, zabrání v ověřování MFA. PRT může získat deklaraci MFA následujícími způsoby:

* **Přihlášení pomocí Windows Hello pro firmy**: Windows Hello pro firmy nahrazuje hesla a používá kryptografické klíče k zajištění silného dvojúrovňového ověřování. Windows Hello pro firmy je specifické pro uživatele na zařízení a samo od něj vyžaduje vícefaktorové ověřování. Když se uživatel přihlásí pomocí Windows Hello pro firmy, PRT uživatele získá deklaraci MFA. Tento scénář platí taky pro uživatele přihlášené pomocí čipových karet, pokud ověřování pomocí čipové karty vytvoří z AD FS deklaraci MFA.
   * Vzhledem k tomu, že Windows Hello pro firmy se považuje za vícefaktorové ověřování, se deklarace MFA aktualizuje, když se PRT sám aktualizuje, takže doba platnosti MFA se průběžně rozšiřuje, když se uživatelé přihlásí pomocí WIndows Hello pro firmy.
* **Vícefaktorové ověřování během interaktivního přihlašování k WAM**: během žádosti o token prostřednictvím služby WAM je potřeba, aby při přístupu uživatele k aplikaci MFA PRT, která se v průběhu této interakce obnovila, byla netištěna s deklarací MFA.
   * V takovém případě se deklarace MFA neaktualizuje nepřetržitě, takže doba platnosti MFA je založena na nastavení životnosti v adresáři.
   * Když se pro přístup k aplikaci použijí předchozí existující PRT a RT, PRT a RT se považují za první ověření ověřování. V případě potřeby bude vyžadován druhý důkaz a netištěná deklarace MFA. Tím se taky vydáte nové PRT a RT.
* **Vícefaktorové ověřování během registrace zařízení**: Pokud správce nakonfiguroval nastavení zařízení v Azure AD tak, aby při [registraci zařízení vyžadoval MFA](device-management-azure-portal.md#configure-device-settings), musí uživatel provést MFA, aby se registrace dokončila. Během tohoto procesu má PRT, který byl vydán uživateli, nárok na MFA získanou během registrace. Tato možnost se vztahuje jenom na uživatele, který provedl operaci spojení, a ne na jiné uživatele, kteří se k tomuto zařízení přihlásí.
   * Podobně jako interaktivní přihlášení k adresáři WAM se deklarace MFA neaktualizuje nepřetržitě, takže doba platnosti MFA je založena na nastavení životnosti v adresáři.

Windows 10 udržuje rozdělený seznam PRTs pro každé přihlašovací údaje. K dispozici je tedy PRT pro každé z Windows Hello pro firmy, heslo nebo čipové karty. Tento oddíl zajišťuje, že deklarace MFA jsou izolované na základě použitých přihlašovacích údajů a nejsou smíšené v rámci požadavků na tokeny.

## <a name="how-is-a-prt-invalidated"></a>Jak je PRT zrušení platnosti?

V následujících scénářích je zrušena platnost PRT:

* **Neplatný uživatel**: Pokud je uživatel ve službě Azure AD odstraněný nebo zakázaný, jejich PRT se neověřuje a nedá se použít k získání tokenů pro aplikace. Pokud se odstraněný nebo zakázaný uživatel už předtím přihlásil k zařízení, přihlásí se do mezipaměti v, dokud CloudAP nevědí o jejich neplatném stavu. Jakmile CloudAP zjistí, že je uživatel neplatný, blokuje další přihlášení. Neplatnému uživateli se automaticky zablokuje přihlášení na nová zařízení, která nemají svoje přihlašovací údaje v mezipaměti.
* **Neplatné zařízení**: Pokud se v Azure AD odstraní nebo zakáže zařízení, PRT získaný v tomto zařízení se zruší a nedá se použít k získání tokenů pro ostatní aplikace. Pokud je už uživatel přihlášený k neplatnému zařízení, může to pokračovat. Ale všechny tokeny v zařízení jsou neověřené a uživatel k žádným prostředkům z tohoto zařízení nemá jednotné přihlašování.
* **Změna hesla**: když uživatel změní heslo, PRT získaná pomocí předchozího hesla zruší platnost v Azure AD. Změna hesla způsobí, že uživatel získá nový PRT. K této neplatnosti může dojít dvěma různými způsoby:
   * Pokud se uživatel přihlásí k Windows pomocí nového hesla, CloudAP zahodí starou PRT a vyžádá si Azure AD, aby vydával nový PRT pomocí nového hesla. Pokud uživatel nemá připojení k Internetu, nové heslo nelze ověřit, systém Windows může vyžadovat, aby uživatel zadal staré heslo.
   * Pokud se uživatel přihlásil pomocí původního hesla nebo změnil heslo po přihlášení k systému Windows, bude se stará PRT používat pro všechny žádosti o tokeny založené na WAM. V tomto scénáři se uživateli zobrazí výzva k opětovnému ověření během žádosti o token WAM a vystavuje se nový PRT.
* **Problémy s čipem TPM**: někdy může čip TPM zařízení Falter nebo selhat, což vede k nedostupnosti klíčů ZABEZPEČENÝCH čipem TPM. V takovém případě zařízení není schopné získat PRT nebo požádat o tokeny pomocí existujícího PRT, protože nemůže prokázat, že kryptografické klíče nejsou k dispozici. V důsledku toho služba Azure AD zruší platnost všech stávajících PRT. Když Windows 10 zjistí selhání, zahájí tok obnovení, který znovu zaregistruje zařízení s novými kryptografickými klíči. Při připojení k hybridní službě Azure AD, stejně jako při prvotní registraci, se obnovení stane bez zásahu uživatele. Pro připojené služby Azure AD nebo zařízení registrovaná v Azure AD musí být obnovení provedeno uživatelem, který má na zařízení oprávnění správce. V tomto scénáři je tok obnovení iniciováný výzvou Windows, která uživatele provede k úspěšnému obnovení zařízení.

## <a name="detailed-flows"></a>Podrobné toky

Následující diagramy znázorňují základní podrobnosti o vydávání, obnovování a používání PRT k vyžádání přístupového tokenu pro aplikaci. Kromě toho tento postup popisuje, jak jsou výše uvedené mechanismy zabezpečení aplikovány během těchto interakcí.

### <a name="prt-issuance-during-first-sign-in"></a>Vystavení PRT při prvním přihlášení

![PRT vystavování během prvního přihlašování – podrobný postup](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> V zařízeních připojených k Azure AD se tato výměna provádí synchronně, aby vydávala PRT, než se uživatel může přihlásit k Windows. V zařízeních připojených k hybridní službě Azure AD je místní služba Active Directory primární autoritou. Proto uživatel čeká jenom na to, dokud nezíská lístek TGT k přihlášení, zatímco vystavení PRT proběhne asynchronně. Tento scénář se nevztahuje na zařízení registrovaná v Azure AD, protože přihlášení nepoužívá přihlašovací údaje Azure AD.

| Krok | Description |
| :---: | --- |
| A | Uživatel zadá heslo do uživatelského rozhraní pro přihlášení. LogonUI předá přihlašovací údaje ve vyrovnávací paměti ověření pro LSA, které v nástroji předává internímu CloudAP. CloudAP přepošle tento požadavek do modulu plug-in CloudAP. |
| B | Modul plug-in CloudAP inicializuje požadavek na zjištění sféry k identifikaci poskytovatele identity pro daného uživatele. Pokud má tenant uživatele nastavení zprostředkovatele federace, Azure AD vrátí koncový bod výměny metadat poskytovatele federačního koncového bodu (MEX). Pokud ne, Azure AD vrátí, že uživatel je spravovaný, což znamená, že se uživatel může ověřit pomocí Azure AD. |
| C | Pokud je uživatel spravován, CloudAP Získá hodnotu NONCE z Azure AD. Pokud je uživatel federovaný, vyžádá modul plug-in CloudAP token SAML od poskytovatele federace s přihlašovacími údaji uživatele. Po přijetí obdrží token SAML požadavek na hodnotu NONCE z Azure AD. |
| D | Modul plug-in CloudAP vytvoří požadavek na ověření s přihlašovacími údaji uživatele, hodnotu nonce a oborem zprostředkovatele, podepíše požadavek pomocí klíče zařízení (dkpriv) a pošle ho do služby Azure AD. Ve federovaném prostředí používá modul plug-in CloudAP token SAML vrácený zprostředkovatelem federace místo přihlašovacích údajů uživatele. |
| E | Azure AD ověří přihlašovací údaje uživatele, hodnotu nonce a podpis zařízení, ověří, že je zařízení v tenantovi platné, a vydá šifrované PRT. Společně s PRT Azure AD taky vydává symetrický klíč, který se nazývá klíč relace zašifrovaný pomocí Azure AD pomocí přenosového klíče (tkpub). Kromě toho je klíč relace také vložen do PRT. Tento klíč relace slouží jako klíč kontrolního bodu (PoP) pro následné žádosti s PRT. |
| F | Modul plug-in CloudAP předá šifrovaný PRT a klíč relace do CloudAP. CloudAP požádat čip TPM, aby dešifroval klíč relace pomocí přenosového klíče (tkpriv) a znovu ho zašifroval pomocí vlastního klíče TPM. CloudAP ukládá šifrovaný klíč relace ve své mezipaměti spolu s PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>PRT obnovení v následných přihlášeních

![PRT obnovení v následných přihlášeních](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Krok | Description |
| :---: | --- |
| A | Uživatel zadá heslo do uživatelského rozhraní pro přihlášení. LogonUI předá přihlašovací údaje ve vyrovnávací paměti ověření pro LSA, které v nástroji předává internímu CloudAP. CloudAP přepošle tento požadavek do modulu plug-in CloudAP. |
| B | Pokud se uživatel dřív přihlásil k uživateli, Windows iniciuje přihlášení do mezipaměti a ověří přihlašovací údaje pro přihlášení uživatele v. Každé 4 hodiny modul plug-in CloudAP inicializuje asynchronní obnovení PRT. |
| C | Modul plug-in CloudAP inicializuje požadavek na zjištění sféry k identifikaci poskytovatele identity pro daného uživatele. Pokud má tenant uživatele nastavení zprostředkovatele federace, Azure AD vrátí koncový bod výměny metadat poskytovatele federačního koncového bodu (MEX). Pokud ne, Azure AD vrátí, že uživatel je spravovaný, což znamená, že se uživatel může ověřit pomocí Azure AD. |
| D | Pokud je uživatel federovaný, vyžádá modul plug-in CloudAP token SAML od poskytovatele federace s přihlašovacími údaji uživatele. Po přijetí obdrží token SAML požadavek na hodnotu NONCE z Azure AD. Pokud je uživatel spravován, CloudAP získá přímo ze služby Azure AD hodnotu nonce. |
| E | Modul plug-in CloudAP vytvoří požadavek na ověření pomocí přihlašovacích údajů uživatele, hodnoty nonce a stávajícího PRT, podepíše požadavek s klíčem relace a odešle ho do služby Azure AD. Ve federovaném prostředí používá modul plug-in CloudAP token SAML vrácený zprostředkovatelem federace místo přihlašovacích údajů uživatele. |
| F | Azure AD ověřuje signaturu klíče relace porovnáním s klíčem relace vloženým v PRT, ověří hodnotu nonce a ověří, jestli je zařízení v tenantovi platné, a vydá nové PRT. Jak je uvedeno dříve, PRT je znovu spojen s klíčem relace šifrovaným pomocí přenosového klíče (tkpub). |
| G | Modul plug-in CloudAP předá šifrovaný PRT a klíč relace do CloudAP. CloudAP vyžádá čip TPM, aby dešifroval klíč relace pomocí přenosového klíče (tkpriv) a znovu ho zašifroval pomocí vlastního klíče TPM. CloudAP ukládá šifrovaný klíč relace ve své mezipaměti spolu s PRT. |

> [!NOTE]
> PRT se dá obnovit externě bez nutnosti připojení VPN, když jsou koncové body usernamemixed povolené externě.

### <a name="prt-usage-during-app-token-requests"></a>PRT využití během žádostí o tokeny aplikace

![PRT využití během žádostí o tokeny aplikace](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Krok | Description |
| :---: | --- |
| A | Aplikace (například Outlook, OneNote atd.) inicializuje požadavek na token pro WAM. Služba WAM pak požádá modul plug-in Azure AD WAM, aby vyžádala požadavek na token. |
| B | Pokud je aktualizační token pro aplikaci již k dispozici, modul plug-in Azure AD použije ho k vyžádání přístupového tokenu. K zajištění ověření vazby zařízení modul plug-in WAM podepíše požadavek pomocí klíče relace. Azure AD ověří klíč relace a vydá přístupový token a nový obnovovací token pro aplikaci, který je zašifrovaný klíčem relace. Modul plug-in WAM požaduje modul plug-in cloudového bodu k dešifrování tokenů, což zase požaduje čip TPM k dešifrování pomocí klíče relace. Výsledkem je, že modul plug-in WAM získá tokeny. V dalším kroku modul plug-in WAM poskytuje aplikaci přístup jenom k přístupovému tokenu, zatímco znovu šifruje obnovovací token pomocí DPAPI a ukládá ho do vlastní mezipaměti.  |
| C |  Pokud obnovovací token pro aplikaci není k dispozici, modul plug-in Azure AD služby WAM použije PRT k vyžádání přístupového tokenu. Aby bylo možné poskytnout důkaz o vlastnictví, modul plug-in WAM podepíše požadavek obsahující PRT s klíčem relace. Azure AD ověří signaturu klíče relace porovnáním s klíčem relace vloženým v PRT, ověří, že zařízení je platné a vydá přístupový token a obnovovací token pro aplikaci. Kromě toho může Azure AD vydávat nové PRT (na základě aktualizačního cyklu), které jsou zašifrované klíčem relace. |
| D | Modul plug-in WAM požaduje modul plug-in cloudového bodu k dešifrování tokenů, což zase požaduje čip TPM k dešifrování pomocí klíče relace. Výsledkem je, že modul plug-in WAM získá tokeny. V dalším kroku modul plug-in WAM poskytuje aplikaci přístup jenom k přístupovému tokenu, zatímco znovu šifruje obnovovací token pomocí DPAPI a ukládá ho do vlastní mezipaměti. Modul plug-in WAM použije obnovovací token pro tuto aplikaci. Modul plug-in WAM také vrací nový modul plug-in PRT do cloudového přístupového bodu, který ověřuje PRT ve službě Azure AD před jejich aktualizací ve vlastní mezipaměti. Modul plug-in cloudového přístupového bodu bude používat nové PRT s dál. |
| E | WAM poskytuje novému vydanému přístupovému tokenu WAM, který zase poskytuje volající aplikaci.|

### <a name="browser-sso-using-prt"></a>Procházení jednotného přihlašování pomocí PRT

![Procházení jednotného přihlašování pomocí PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Krok | Description |
| :---: | --- |
| A | Uživatel se do Windows přihlásí pomocí svých přihlašovacích údajů, aby mohl získat PRT. Jakmile uživatel otevře prohlížeč, prohlížeč (nebo rozšíření) načte adresy URL z registru. |
| B | Když uživatel otevře přihlašovací adresu URL služby Azure AD, prohlížeč nebo rozšíření ověří adresu URL pomocí těch, které jsou získány z registru. Pokud se shodují, prohlížeč vyvolá nativního klientského hostitele pro získání tokenu. |
| C | Nativní klient klienta ověří, jestli adresy URL patří poskytovatelům identity Microsoftu (účet Microsoft nebo Azure AD), extrahuje hodnotu NONCE odeslanou z adresy URL a vyvolá volání modulu plug-in CloudAP pro získání souboru cookie PRT. |
| D | Modul plug-in CloudAP vytvoří soubor cookie PRT, přihlaste se pomocí klíče relace vázané na čip TPM a odešlete ho zpátky do nativního klientského hostitele. V případě, že je soubor cookie podepsán klíčem relace, nemůže být poškozen. |
| E | Nativní klient klienta vrátí tento soubor cookie PRT do prohlížeče, který bude obsahovat jako součást hlavičky požadavku s názvem x-MS-RefreshTokenCredential a žádosti o tokeny od Azure AD. |
| F | Azure AD ověří signaturu klíče relace v souboru cookie PRT, ověří hodnotu nonce, ověří, že je zařízení v tenantovi platné, a vydá token ID pro webovou stránku a šifrovaný soubor cookie relace pro prohlížeč. |

> [!NOTE]
> Tok jednotného přihlašování prohlížeče popsaný v předchozích krocích se nevztahuje na relace v privátních režimech, jako je InPrivate ve službě Microsoft Edge nebo anonymním v Google Chrome (při použití rozšíření účtů Microsoft).

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží souvisejících s PRT najdete v článku [řešení potíží se zařízeními s Windows 10 a Windows serverem 2016, která se připojila k hybridním Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).

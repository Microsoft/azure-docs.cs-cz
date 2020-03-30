---
title: Vývoj koncového bodu SCIM pro zřizování uživatelů do aplikací z Azure AD
description: Systém pro správu identit napříč doménami (SCIM) standardizuje automatické zřizování uživatelů. Naučte se vyvíjet koncový bod SCIM, integrovat rozhraní SCIM API s Azure Active Directory a začít automatizovat zřizování uživatelů a skupin do cloudových aplikací.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/07/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0507989ec25db595a85b89f15d8ff7d056a970f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297676"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů pomocí služby Azure Active Directory (Azure AD)

Jako vývojář aplikace můžete použít rozhraní API pro správu identit mezi doménami (SCIM), které umožňuje automatické zřizování uživatelů a skupin mezi vaší aplikací a službou Azure AD. Tento článek popisuje, jak vytvořit koncový bod SCIM a integrovat se službou zřizování Azure AD. Specifikace SCIM poskytuje společné uživatelské schéma pro zřizování. Při použití ve spojení s federačními standardy, jako je SAML nebo OpenID Connect, scim poskytuje správcům komplexní řešení založené na standardech pro správu přístupu.

SCIM je standardizovaná definice dvou koncových bodů: koncového bodu /Users a koncového bodu /Groups. Používá běžné REST slovesa k vytvoření, aktualizaci a odstranění objektů a předdefinované schéma pro běžné atributy, jako je název skupiny, uživatelské jméno, jméno, příjmení a e-mail. Aplikace, které nabízejí rozhraní API REST SCIM 2.0, mohou snížit nebo eliminovat bolest při práci s proprietárním rozhraním API pro správu uživatelů. Například všechny kompatibilní SCIM klient ví, jak vytvořit HTTP POST objektu JSON na /Users koncový bod vytvořit novou položku uživatele. Namísto nutnosti mírně odlišné rozhraní API pro stejné základní akce, aplikace, které odpovídají standardu SCIM můžete okamžitě využít již existující klienty, nástroje a kód. 

![Zřizování z Azure AD do aplikace s SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Standardní uživatelské schéma objektu a rozhraní API pro správu definované v SCIM 2.0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) umožňují poskytovatelům identit a aplikacím snadněji integrovat mezi sebou. Vývojáři aplikací, kteří vytvářejí koncový bod SCIM, se mohou integrovat s libovolným klientem kompatibilním s ROZHRANÍM SCIM, aniž by museli pracovat na vlastní chod.

Automatizace zřizování do aplikace vyžaduje sestavení a integraci koncového bodu SCIM s klientem Azure AD SCIM. Proveďte následující kroky a spusťte zřizování uživatelů a skupin do vaší aplikace. 
    
  * **[Krok 1: Navrhněte uživatelské a skupinové schéma.](#step-1-design-your-user-and-group-schema)** Identifikujte objekty a atributy, které vaše aplikace potřebuje, a určete, jak se mapují na schéma uživatele a skupiny podporované implementací Scim azure ad.

  * **[Krok 2: Pochopit implementaci Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)** Zjistěte, jak je implementován klient Scim Azure AD, a modelujte zpracování požadavků protokolu SCIM a odpovědi.

  * **[Krok 3: Vytvoření koncového bodu SCIM.](#step-3-build-a-scim-endpoint)** Koncový bod musí být kompatibilní scim 2.0 integrovat se službou zřizování Azure AD. Jako možnost můžete použít knihovny Microsoft Common Language Infrastructure (CLI) a ukázky kódu k vytvoření koncového bodu. Tyto vzorky jsou určeny pouze pro referenční a zkušební; doporučujeme, abyste nekódovali produkční aplikaci, abyste na nich byli závislýi.

  * **[Krok 4: Integrujte koncový bod SCIM s klientem Azure AD SCIM.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Pokud vaše organizace používá aplikaci jiného výrobce, která implementuje profil SCIM 2.0, který podporuje Azure AD, můžete začít automatizovat zřizování a zrušení zřizování uživatelů a skupin ihned.

  * **[Krok 5: Publikování aplikace do galerie aplikací Azure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Uspokojte zákazníkům zjišťování vaší aplikace a snadno konfigurujte zřizování. 

![Kroky pro integraci koncového bodu SCIM s Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Krok 1: Návrh uživatelského a skupinového schématu

Každá aplikace vyžaduje různé atributy k vytvoření uživatele nebo skupiny. Začněte integraci identifikací objektů (uživatelů, skupin) a atributů (jméno, manažer, funkce atd.), které vaše aplikace vyžaduje. Standard SCIM definuje schéma pro správu uživatelů a skupin. Schéma základního uživatele vyžaduje pouze tři atributy: **id** (identifikátor definované poskytovatelem služeb), **externalId** (identifikátor definovaný klientem) a **meta** (metadata jen pro čtení spravovaná poskytovatelem služeb). Všechny ostatní atributy jsou volitelné. Kromě základního uživatelského schématu definuje standard SCIM rozšíření podnikového uživatele a model pro rozšíření uživatelského schématu tak, aby vyhovovalo potřebám vaší aplikace. Pokud například vaše aplikace vyžaduje správce uživatele, můžete použít schéma podnikového uživatele ke shromažďování správce uživatele a základní schéma pro shromažďování e-mailu uživatele. Chcete-li schéma navrhnout, postupujte takto:
  1. Seznam atributů, které vaše aplikace vyžaduje. Může být užitečné rozdělit vaše požadavky na atributy potřebné pro ověřování (např. loginName a e-mail), atributy potřebné ke správě životního cyklu uživatele (např. stav / aktivní) a další atributy potřebné pro vaši konkrétní aplikaci (např. manažer, značka).
  2. Zkontrolujte, zda jsou tyto atributy již definovány v základním uživatelském schématu nebo schématu podnikového uživatele. Pokud všechny atributy, které potřebujete a nejsou zahrnuty ve schématech jádra nebo podnikového uživatele, budete muset definovat rozšíření schématu uživatele, které pokrývá atributy, které potřebujete. V níže uvedeném příkladu jsme přidali rozšíření pro uživatele povolit zřizování "značky" na uživatele. Nejlepší je začít pouze s základní a podnikové uživatelské schémata a rozbalit na další vlastní schémata později.  
  3. Namapujte atributy SCIM na atributy uživatele ve službě Azure AD. Pokud jeden z atributů, které jste definovali v koncovém bodě SCIM nemá jasný protějšek na uživatelské schéma Azure AD, je velká pravděpodobnost, že data nejsou uloženy na objekt uživatele vůbec na většině klientů. Zvažte, zda tento atribut může být volitelné pro vytvoření uživatele. Pokud je atribut důležitý pro vaši aplikaci, průvodce správce klienta rozšířit své schéma nebo použít atribut rozšíření, jak je znázorněno níže pro "značky" vlastnost.

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabulka 1: Osnova atributů, které potřebujete 
| Krok 1: Určení atributů, které vaše aplikace vyžaduje| Krok 2: Mapování požadavků na aplikace na standard SCIM| Krok 3: Mapování atributů SCIM na atributy Azure AD|
|--|--|--|
|Loginname|userName|userPrincipalName (Hlavní název uživatele)|
|firstName|name.givenName|givenName|
|lastName|name.lastName|lastName|
|workMail|E-maily[zadejte eq "práce"].hodnota|Pošta|
|manager|manager|manager|
|značka|urn:ietf:params:scim:schémata:rozšíření:2.0:CustomExtension:tag|extensionAttribute1|
|status|Aktivní|isSoftDeleted (vypočítaná hodnota není uložena u uživatele)|

Schéma definované výše by být reprezentovány pomocí datové části Json níže. Všimněte si, že kromě atributy požadované pro aplikaci, reprezentace JSON obsahuje požadované "id", "externalId" a "meta" atributy.

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen",
     "externalId":"bjensen",
     "name":{
       "familyName":"Jensen",
       "givenName":"Barbara"
     },
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User": {
     "Manager": "123456"
   },
     "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:CustomAttribute:User": {
     "tag": "701984",
   },
   "meta": {
     "resourceType": "User",
     "created": "2010-01-23T04:56:22Z",
     "lastModified": "2011-05-13T04:42:34Z",
     "version": "W\/\"3694e05e9dff591\"",
     "location":
 "https://example.com/v2/Users/2819c223-7f76-453a-919d-413861904646"
   }
 ```

### <a name="table-2-default-user-attribute-mapping"></a>Tabulka 2: Výchozí mapování atributů uživatele
Potom můžete použít níže uvedenou tabulku pochopit, jak atributy, které vaše aplikace vyžaduje může mapovat na atribut v Azure AD a SCIM RFC. Můžete [přizpůsobit,](customize-application-attributes.md) jak jsou namapovány atributy mezi Azure AD a koncový bod SCIM. Upozorňujeme, že nemusíte podporovat uživatele i skupiny ani všechny níže uvedené atributy. Jsou odkazem na to, jak jsou atributy ve službě Azure AD často mapovány na vlastnosti v protokolu SCIM. 

| Uživatel služby Azure Active Directory | "urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:User" |
| --- | --- |
| Issoftdeleted |Aktivní |
|Oddělení|urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:oddělení|
| displayName |displayName |
|Employeeid|urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:employeeNumber|
| Fax-Telefonní číslo |phoneNumbers[zadejte eq "fax"].value |
| givenName |name.givenName |
| název úlohy |title |
| pošta |e-maily[zadejte eq "práce"].hodnota |
| přezdívka |externalId |
| manager |urn:ietf:params:scim:schémata:rozšíření:enterprise:2.0:Uživatel:manažer |
| mobil |phoneNumbers[zadejte eq "mobile"].value |
| Postalcode |adresy[zadejte eq "práce"].PSČ |
| proxy-adresy |e-maily[zadejte eq "jiné"]. Hodnotu |
| physical-Delivery-OfficeName |adresy[zadejte eq "other"]. Formátovaný |
| Streetaddress |addresses[zadejte eq "work"].streetAddress |
| surname |name.familyName |
| telefonní číslo |phoneNumbers[zadejte eq "work"].value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabulka 3: Výchozí mapování atributů skupiny

| Skupina Azure Active Directory | urn:ietf:params:scim:schémata:core:2.0:Skupina |
| --- | --- |
| displayName |displayName |
| pošta |e-maily[zadejte eq "práce"].hodnota |
| přezdívka |displayName |
| členy |členy |
| Objectid |externalId |
| proxyAddresses |e-maily[zadejte eq "jiné"]. Hodnotu |

Existuje několik koncových bodů definovaných v RFC SCIM. Můžete začít s koncovým bodem /User a potom rozbalit. Koncový bod /Schemas je užitečný při použití vlastních atributů nebo při častém změny schématu. Umožňuje klientovi automaticky načíst nejaktuálnější schéma. Koncový bod /Bulk je zvláště užitečné při podpoře skupin. Níže uvedená tabulka popisuje různé koncové body definované ve standardu SCIM. Koncový bod /Schemas je užitečný při použití vlastních atributů nebo při častém změny schématu. Umožňuje klientovi automaticky načíst nejaktuálnější schéma. Koncový bod /Bulk je zvláště užitečné při podpoře skupin. Níže uvedená tabulka popisuje různé koncové body definované ve standardu SCIM. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabulka 4: Určete koncové body, které chcete vyvinout
|Koncový bod|POPIS|
|--|--|
|/Uživatel|Provádějte operace CRUD s objektem uživatele.|
|/Skupina|Proveďte operace CRUD na objektu skupiny.|
|/ServiceProviderConfig|Obsahuje podrobnosti o funkcích standardu SCIM, které jsou podporovány, například prostředky, které jsou podporovány a metodu ověřování.|
|/ResourceTypes|Určuje metadata o jednotlivých prostředcích.|
|/Schémata|Sada atributů podporovaných jednotlivými klienty a poskytovateli služeb se může lišit. Zatímco jeden poskytovatel služeb může obsahovat "jméno", "název" a "e-maily", zatímco jiný poskytovatel služeb používá "jméno", "název" a "telefonní čísla". Koncový bod schématu umožňuje zjišťování podporovaných atributů.|
|/Hromadné|Hromadné operace umožňují provádět operace s velkou kolekcí objektů prostředků v jedné operaci (např. aktualizace členství pro velkou skupinu).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Krok 2: Principy implementace Azure AD SCIM
> [!IMPORTANT]
> Chování implementace Azure AD SCIM byla naposledy aktualizována na 18 prosince 2018. Informace o tom, co se změnilo, najdete v [tématu dodržování protokolu SCIM 2.0 služby Azure AD User Provisioning](application-provisioning-config-problem-scim-compatibility.md).

Pokud vytváříte aplikaci, která podporuje rozhraní API pro správu uživatelů SCIM 2.0, tato část podrobně popisuje, jak je implementován klient Azure AD SCIM. Také ukazuje, jak modelovat zpracování požadavků protokolu SCIM a odpovědi. Po implementaci koncového bodu SCIM jej můžete otestovat podle postupu popsaného v předchozí části.

Ve [specifikaci protokolu SCIM 2.0](http://www.simplecloud.info/#Specification)musí aplikace splňovat tyto požadavky:

* Podporuje vytváření uživatelů a volitelně také skupiny podle části [3.3 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Podporuje úpravy uživatelů nebo skupin s požadavky PATCH podle [oddílu 3.5.2 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Podporuje načítání známého prostředku pro uživatele nebo skupinu vytvořenou dříve podle [oddílu 3.4.1 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Podporuje dotazování uživatelů nebo skupin podle části [3.4.2 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Ve výchozím nastavení jsou uživatelé načteni `id` `username` a `externalid`dotazováni jejich a `displayName`, a skupiny jsou dotazovány .  
* Podporuje dotazování uživatele podle ID a správce podle oddílu 3.4.2 protokolu SCIM.  
* Podporuje dotazování skupin podle ID a člen, podle oddílu 3.4.2 protokolu SCIM.  
* Přijme jeden nosný token pro ověřování a autorizaci Azure AD do vaší aplikace.

Při implementaci koncového bodu SCIM postupujte podle těchto obecných pokynů, abyste zajistili kompatibilitu s Azure AD:

* `id`je požadovaná vlastnost pro všechny prostředky. Každá odpověď, která vrátí prostředek by měla zajistit, že každý prostředek má tuto vlastnost, s výjimkou `ListResponse` s nulou členů.
* Odpověď na požadavek na dotaz/filtr by měla být vždy . `ListResponse`
* Skupiny jsou volitelné, ale podporovány pouze v případě, že implementace SCIM podporuje požadavky PATCH.
* Není nutné zahrnout celý prostředek v odpovědi PATCH.
* Microsoft Azure AD používá jenom následující operátory:  
    - `eq`
    - `and`
* Nevyžadují shodu rozlišující malá a velká `op` písmena na konstrukčních prvcích v SCIM, zejména hodnoty operace PATCH, jak je definováno v . https://tools.ietf.org/html/rfc7644#section-3.5.2 Azure AD vydává hodnoty 'op' `Add` `Replace`jako `Remove`, a .
* Microsoft Azure AD provádí požadavky na načtení náhodného uživatele a skupiny, aby bylo zajištěno, že koncový bod a přihlašovací údaje jsou platné. Taky se to dělá jako součást toku **testovacího připojení** na [webu Azure Portal](https://portal.azure.com). 
* Atribut, na který lze dotazovány prostředky, by měl být nastaven jako odpovídající atribut v aplikaci na [webu Azure Portal](https://portal.azure.com). Další informace naleznete [v tématu Přizpůsobení mapování atributů zřizování uživatelů](customize-application-attributes.md)

### <a name="user-provisioning-and-deprovisioning"></a>Zřizování a zrušení zřizování uživatelů

Následující obrázek znázorňuje zprávy, které služba Azure Active Directory odesílá službě SCIM za účelem správy životního cyklu uživatele v úložišti identit vaší aplikace.  

![Zobrazuje pořadí zřizování a zrušení zřízení uživatele.](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Pořadí zřizování a zrušení zřízení uživatele*

### <a name="group-provisioning-and-deprovisioning"></a>Zřizování a zrušení zřizování skupin

Zřizování a zrušení zřízení skupiny jsou volitelné. Při implementaci a povolení, na následujícím obrázku znázorňuje zprávy, které Azure AD odešle do služby SCIM ke správě životního cyklu skupiny v úložišti identit vaší aplikace.  Tyto zprávy se liší od zpráv o uživatelích dvěma způsoby:

* Požadavky na načtení skupin určují, že atribut členů má být vyloučen z jakéhokoli prostředku poskytnutého v reakci na požadavek.  
* Požadavky k určení, zda referenční atribut má určitou hodnotu jsou požadavky o atribut members.  

![Zobrazuje pořadí zřizování a zrušení zřizování skupin.](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Pořadí zřizování a zrušení zřizování skupiny*

### <a name="scim-protocol-requests-and-responses"></a>Požadavky a odpovědi protokolu SCIM
Tato část obsahuje příklad požadavků SCIM vyzařovaných klientem Azure AD SCIM a příklad očekávané odpovědi. Chcete-li dosáhnout nejlepších výsledků, měli byste aplikaci zakódovat, aby tyto požadavky zpracovávala v tomto formátu, a vyzařovat očekávané odpovědi.

> [!IMPORTANT]
> Informace o tom, jak a kdy služba zřizování uživatelů Azure AD vyzařuje operace popsané níže, naleznete v [části Zřizování cykly: Počáteční a přírůstkové](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) v [jak funguje zřizování](how-provisioning-works.md).

[Uživatelské operace](#user-operations)
  - [Vytvořit uživatele](#create-user) [(požadavek](#request) / [na odpověď)](#response)
  - [Získat uživatele](#get-user) [(požadavek](#request-1) / [na odpověď)](#response-1)
  - [Získat uživatele podle dotazu](#get-user-by-query) [(Požadavek](#request-2) / [na odpověď)](#response-2)
  - [Získat uživatele podle dotazu - Nulové výsledky](#get-user-by-query---zero-results) [(Odpověď na žádost)](#request-3)
/ [Response](#response-3)
  - [Aktualizovat uživatele [Vlastnosti s více hodnotami]](#update-user-multi-valued-properties) ([Odpověď na požadavek](#request-4) /  [Response](#response-4))
  - [Aktualizovat uživatele [Vlastnosti s jednou hodnotou]](#update-user-single-valued-properties) ([Odpověď na požadavek](#request-5)
/ [Response](#response-5)) 
  - [Zakázat uživatele](#disable-user) [(požadavek](#request-14) / 
[na odpověď)](#response-14)
  - [Odstranit uživatele](#delete-user) [(požadavek](#request-6) / 
[na odpověď)](#response-6)


[Skupinové operace](#group-operations)
  - [Vytvořit skupinu](#create-group)  / [(Požadavek na odpověď)](#response-7) [Request](#request-7)
  - [Získat skupinu](#get-group) [(Žádost](#request-8) / [o odpověď)](#response-8)
  - [Získat skupinu podle displayName](#get-group-by-displayname) [(Požadavek](#request-9) / [na odpověď)](#response-9)
  - [Aktualizovat skupinu [Atributy nečlenů]](#update-group-non-member-attributes) ([Odpověď na požadavek](#request-10) /
  [Response](#response-10))
  - [Aktualizovat skupinu [Přidat členy]](#update-group-add-members) ( [Žádost](#request-11) /
[o odpověď](#response-11))
  - [Aktualizovat skupinu [Odebrat členy]](#update-group-remove-members) ( [Žádost](#request-12) /
[o odpověď](#response-12))
  - [Odstranit skupinu](#delete-group) [(žádost o](#request-13) /
[odpověď)](#response-13)

### <a name="user-operations"></a>Uživatelské operace

* Uživatelé mohou být `userName` `email[type eq "work"]` dotazováni nebo atributy.  

#### <a name="create-user"></a>Vytvořit uživatele

###### <a name="request"></a>Žádost

*POST /Uživatelé*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Odpověď

*HTTP/1.1 201 Vytvořeno*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Získání uživatele

###### <a name="request"></a><a name="request-1"></a>Požadavek
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Odpověď (Uživatel nalezen)
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

###### <a name="request"></a>Žádost
*GET /Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Odpověď (Uživatel nebyl nalezen. Všimněte si, že detail není vyžadován, pouze stav.)

```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:Error"
    ],
    "status": "404",
    "detail": "Resource 23B51B0E5D7AE9110A49411D@7cca31655d49f3640a494224 not found"
}
```

#### <a name="get-user-by-query"></a>Získat uživatele podle dotazu

##### <a name="request"></a><a name="request-2"></a>Požadavek

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Reakce

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Získat uživatele podle dotazů – nulové výsledky

##### <a name="request"></a><a name="request-3"></a>Požadavek

*GET /Users?filter=userName eq "neexistující uživatel"*

##### <a name="response"></a><a name="response-3"></a>Reakce

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Aktualizovat uživatele [Vlastnosti s více hodnotami]

##### <a name="request"></a><a name="request-4"></a>Požadavek

*PATCH /Uživatelé/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response"></a><a name="response-4"></a>Reakce

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Aktualizovat uživatele [Vlastnosti s jednou hodnotou]

##### <a name="request"></a><a name="request-5"></a>Požadavek

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response"></a><a name="response-5"></a>Reakce

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

### <a name="disable-user"></a>Zakázání uživatele

##### <a name="request"></a><a name="request-14"></a>Požadavek

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "Operations": [
        {
            "op": "Replace",
            "path": "active",
            "value": false
        }
    ],
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"
    ]
}
```

##### <a name="response"></a><a name="response-14"></a>Reakce

```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User"
    ],
    "id": "CEC50F275D83C4530A495FCF@834d0e1e5d8235f90a495fda",
    "userName": "deanruiz@testuser.com",
    "name": {
        "familyName": "Harris",
        "givenName": "Larry"
    },
    "active": false,
    "emails": [
        {
            "value": "gloversuzanne@testuser.com",
            "type": "work",
            "primary": true
        }
    ],
    "addresses": [
        {
            "country": "ML",
            "type": "work",
            "primary": true
        }
    ],
    "meta": {
        "resourceType": "Users",
        "location": "/scim/5171a35d82074e068ce2/Users/CEC50F265D83B4530B495FCF@5171a35d82074e068ce2"
    }
}
```
#### <a name="delete-user"></a>Odstranění uživatele

##### <a name="request"></a><a name="request-6"></a>Požadavek

*ODSTRANIT /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Reakce

*HTTP/1.1 204 Žádný obsah*

### <a name="group-operations"></a>Skupinové operace

* Skupiny musí být vždy vytvořeny s prázdným seznamem členů.
* Skupiny mohou být `displayName` dotazovány atributem.
* Aktualizace skupiny PATCH požadavek by měl přinést *HTTP 204 žádný obsah* v odpovědi. Vrátit tělo se seznamem všech členů není vhodné.
* Není nutné podporovat vrácení všech členů skupiny.

#### <a name="create-group"></a>Vytvoření skupiny

##### <a name="request"></a><a name="request-7"></a>Požadavek

*POST /Skupiny HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a><a name="response-7"></a>Reakce

*HTTP/1.1 201 Vytvořeno*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Získání skupiny

##### <a name="request"></a><a name="request-8"></a>Požadavek

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Reakce
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Získat skupinu podle displayName

##### <a name="request"></a><a name="request-9"></a>Požadavek
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Reakce

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Aktualizovat skupinu [Atributy nečlenů]

##### <a name="request"></a><a name="request-10"></a>Požadavek

*PATCH /Skupiny/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response"></a><a name="response-10"></a>Reakce

*HTTP/1.1 204 Žádný obsah*

### <a name="update-group-add-members"></a>Aktualizovat skupinu [Přidat členy]

##### <a name="request"></a><a name="request-11"></a>Požadavek

*PATCH /Skupiny/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-11"></a>Reakce

*HTTP/1.1 204 Žádný obsah*

#### <a name="update-group-remove-members"></a>Aktualizovat skupinu [Odebrat členy]

##### <a name="request"></a><a name="request-12"></a>Požadavek

*PATCH /Skupiny/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response"></a><a name="response-12"></a>Reakce

*HTTP/1.1 204 Žádný obsah*

#### <a name="delete-group"></a>Odstranění skupiny

##### <a name="request"></a><a name="request-13"></a>Požadavek

*ODSTRANIT /Skupiny/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Reakce

*HTTP/1.1 204 Žádný obsah*

### <a name="security-requirements"></a>Požadavky na zabezpečení
**Verze protokolu TLS**

Jediné přijatelné verze protokolu TLS jsou TLS 1.2 a TLS 1.3. Nejsou povoleny žádné jiné verze TLS. Není povolena žádná verze ssl. 
- Rsa klíče musí být alespoň 2 048 bitů.
- Klávesy ECC musí mít alespoň 256 bitů generovaných pomocí schválené eliptické křivky.


**Délky klíčů**

Všechny služby musí používat certifikáty X.509 generované pomocí šifrovacích klíčů dostatečné délky, což znamená:

**Šifrovací sady**

Všechny služby musí být nakonfigurovány tak, aby používaly následující šifrovací sady v přesném pořadí uvedeném níže. Všimněte si, že pokud máte pouze certifikát RSA, nainstalované šifrovací sady ECDSA nemají žádný vliv. </br>

Minimální lišta TLS 1.2 Cipher Suites:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384


## <a name="step-3-build-a-scim-endpoint"></a>Krok 3: Vytvoření koncového bodu SCIM

Teď, když jste navrhli schéma a pochopili implementaci Scim Azure AD, můžete začít vyvíjet koncový bod SCIM. Spíše než začít od nuly a budování implementace zcela na vlastní pěst, můžete se spolehnout na řadu open source SCIM knihoven publikovaných komunitou SCIM.

Open source .NET Core [referenční kód](https://aka.ms/SCIMReferenceCode) publikovaný týmem zřizování Azure AD je jeden takový prostředek, který můžete přeskočit spuštění vývoje. Jakmile jste vytvořili koncový bod SCIM, budete chtít vyzkoušet. Můžete použít sběr [pošťáktesty](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) poskytované jako součást referenčního kódu nebo spustit prostřednictvím ukázkové požadavky / odpovědi uvedené [výše](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations).  

   > [!Note]
   > Referenční kód je určen k vám pomůže začít vytvářet koncový bod SCIM a je k dispozici "TAK, JAK JE." Příspěvky od komunity jsou vítány, aby pomohly vytvořit a udržovat kód.

Řešení se skládá ze dvou projektů, _Microsoft.SCIM_ a _Microsoft.SCIM.WebHostSample_.

Projekt _Microsoft.SCIM_ je knihovna, která definuje součásti webové služby, které odpovídají specifikaci SCIM. Deklaruje rozhraní _Microsoft.SCIM.IProvider_, požadavky jsou přeloženy do volání metody zprostředkovatele, který by byl naprogramován tak, aby pracovat v úložišti identit.

![Členění: Požadavek přeložený do volání metod poskytovatele](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Projekt _Microsoft.SCIM.WebHostSample_ je visual studio ASP.NET základní webovou aplikaci založenou na šabloně _Empty._ To umožňuje ukázkový kód, který má být nasazen jako samostatný, hostovaný v kontejnerech nebo v rámci Internetové informační služby. Také implementuje rozhraní _Microsoft.SCIM.IProvider_ uchování tříd v paměti jako ukázkové úložiště identit.

```csharp
    public class Startup
    {
        ...
        public IMonitor MonitoringBehavior { get; set; }
        public IProvider ProviderBehavior { get; set; }

        public Startup(IWebHostEnvironment env, IConfiguration configuration)
        {
            ...
            this.MonitoringBehavior = new ConsoleMonitor();
            this.ProviderBehavior = new InMemoryProvider();
        }
        ...
```

### <a name="building-a-custom-scim-endpoint"></a>Vytváření vlastního koncového bodu SCIM

Služba SCIM musí mít certifikát http adresy a ověřování serveru, jehož kořenovým certifikačním úřadem je jeden z následujících názvů:

* Cnnic
* Comodo
* CyberTrust
* DigiCert
* Geotrust
* Globální znak
* Jdi Tati
* Verisign
* WoSign

Sada .NET Core SDK obsahuje vývojový certifikát HTTPS, který lze použít během vývoje, certifikát je nainstalován jako součást prvního spuštění. V závislosti na tom, jak spustíte ASP.NET základní webovou aplikaci, bude poslouchat jiný port:

* Microsoft.SCIM.WebHostUkázka:https://localhost:5001
* IIS Express:https://localhost:44359/

Další informace o protokolu HTTPS v ASP.NET Core použijte následující odkaz: [Vynucení protokolu HTTPS v ASP.NET Core](https://docs.microsoft.com/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Zpracování ověřování koncového bodu

Požadavky ze služby Azure Active Directory zahrnují token nosiče OAuth 2.0. Každá služba přijímající požadavek by měla ověřit vystavittele jako službu Azure Active Directory pro očekávaného klienta Azure Active Directory.

V tokenu je vystavittel identifikován deklarací iss, například `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"`. V tomto příkladu základní adresa hodnoty `https://sts.windows.net`deklarace identity , identifikuje službu Azure Active Directory jako vystavittele, zatímco segment relativní adresy _cbb1a5ac-f33b-45fa-9bf5-f37db0fed422_, je jedinečný identifikátor klienta služby Azure Directory, pro kterého byl token vydán.

Cílovou skupinou tokenu bude ID šablony aplikace pro aplikaci v galerii, každá aplikace `iss` registrovaná v jednom tenantovi může obdržet stejnou deklaraci s požadavky SCIM. ID šablony aplikace pro každou aplikaci v [ProvisioningFeedback@microsoft.com](mailto:ProvisioningFeedback@microsoft.com) galerii se liší, prosím, kontaktujte dotazy týkající se ID šablony aplikace pro aplikaci galerie. ID šablony aplikace pro všechny vlastní aplikace je _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_.

V ukázkovém kódu jsou požadavky ověřeny pomocí balíčku Microsoft.AspNetCore.Authentication.JwtBearer. Následující kód vynucuje, že požadavky na některý z koncových bodů služby jsou ověřeny pomocí tokenu nosiče vydaného službou Azure Active Directory pro určeného klienta:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                ...
            }
            else
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.Authority = " https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/";
                        options.Audience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621";
                        ...
                    });
            }
            ...
        }

        public void Configure(IApplicationBuilder app)
        {
            ...
            app.UseAuthentication();
            app.UseAuthorization();
            ...
       }
```

Nosný token je také nutné použít za [předpokladu, pošťák testy](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) a provádět místní ladění pomocí localhost. Ukázkový kód používá ASP.NET prostředí core změnit možnosti ověřování během fáze vývoje a povolit použití tokenu podepsaného svým držitelem.

Další informace o více prostředích v ASP.NET Core použijte následující odkaz: [Použijte více prostředí v ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

Následující kód vynucuje, že požadavky na některý z koncových bodů služby jsou ověřeny pomocí nosného tokenu podepsaného vlastním klíčem:

```csharp
        public void ConfigureServices(IServiceCollection services)
        {
            if (_env.IsDevelopment())
            {
                services.AddAuthentication(options =>
                {
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
                    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
                })
                    .AddJwtBearer(options =>
                    {
                        options.TokenValidationParameters =
                            new TokenValidationParameters
                            {
                                ValidateIssuer = false,
                                ValidateAudience = false,
                                ValidateLifetime = false,
                                ValidateIssuerSigningKey = false,
                                ValidIssuer = "Microsoft.Security.Bearer",
                                ValidAudience = "Microsoft.Security.Bearer",
                                IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"))
                            };
                    });
            }
        ...
```

Odeslat požadavek GET na řadič tokenu získat platný nosný token, metoda _GenerateJSONWebToken_ je zodpovědný za vytvoření tokenu odpovídající parametry nakonfigurované pro vývoj:

```csharp
        private string GenerateJSONWebToken()
        {
            // Create token key
            SymmetricSecurityKey securityKey =
                new SymmetricSecurityKey(Encoding.UTF8.GetBytes("A1B2C3D4E5F6A1B2C3D4E5F6"));
            SigningCredentials credentials =
                new SigningCredentials(securityKey, SecurityAlgorithms.HmacSha256);

            // Set token expiration
            DateTime startTime = DateTime.UtcNow;
            DateTime expiryTime = startTime.AddMinutes(120);

            // Generate the token
            JwtSecurityToken token =
                new JwtSecurityToken(
                    "Microsoft.Security.Bearer",
                    "Microsoft.Security.Bearer",
                    null,
                    notBefore: startTime,
                    expires: expiryTime,
                    signingCredentials: credentials);

            string result = new JwtSecurityTokenHandler().WriteToken(token);
            return result;
        }
```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Zpracování zřizování a zrušení zřizování uživatelů

***Příklad 1. Dotaz na službu pro odpovídajícího uživatele***

Azure Active Directory dotazuje službu pro uživatele s hodnotou atributu externalId odpovídající hodnotě atributu mailNickname uživatele ve službě Azure AD. Dotaz je vyjádřen jako požadavek HTTP (Hypertext Transfer Protocol), jako je například tento příklad, kde jyoung je ukázka mailNickname uživatele ve službě Azure Active Directory.

>[!NOTE]
> Toto je pouze příklad. Ne všichni uživatelé budou mít atribut mailNickname a hodnota, kterou má uživatel, nemusí být v adresáři jedinečná. Atribut použitý pro porovnávání (což je v tomto případě externalId) je konfigurovatelný v [mapování atributů Azure AD](customize-application-attributes.md).

```
GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
 Authorization: Bearer ...
```

V ukázkovém kódu je požadavek přeložen do volání metody QueryAsync poskytovatele služby. Zde je podpis této metody: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  
 // Microsoft.SCIM.IQueryParameters is defined in 
 // Microsoft.SCIM.Protocol.  

 Task<Resource[]> QueryAsync(IRequest<IQueryParameters> request);
```

V ukázkovém dotazu jsou pro uživatele s danou hodnotou pro atribut externalId hodnoty argumentů předaných metodě QueryAsync:

* Parametry. AlternativníFiltry.Počet: 1
* Parametry. alternatefilters.elementat(0). AttributePath: "externalId"
* Parametry. alternatefilters.elementat(0). ComparisonOperator: ComparisonOperator.Equals
* Parametry. AlternateFilter.Elementat(0). Hodnota porovnání: "jyoung"

***Příklad 2. Zřízení uživatele***

Pokud odpověď na dotaz na webovou službu pro uživatele s hodnotou atributu externalId, která odpovídá hodnotě atributu mailNickname uživatele, nevrátí žádné uživatele, pak služba Azure Active Directory požádá, aby služba zřizovat uživatele odpovídající jednomu ve službě Azure Active Directory.  Zde je příklad takového požadavku: 

```
 POST https://.../scim/Users HTTP/1.1
 Authorization: Bearer ...
 Content-type: application/scim+json
 {
   "schemas":
   [
     "urn:ietf:params:scim:schemas:core:2.0:User",
     "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
   "externalId":"jyoung",
   "userName":"jyoung",
   "active":true,
   "addresses":null,
   "displayName":"Joy Young",
   "emails": [
     {
       "type":"work",
       "value":"jyoung@Contoso.com",
       "primary":true}],
   "meta": {
     "resourceType":"User"},
    "name":{
     "familyName":"Young",
     "givenName":"Joy"},
   "phoneNumbers":null,
   "preferredLanguage":null,
   "title":null,
   "department":null,
   "manager":null}
```

V ukázkovém kódu je požadavek přeložen do volání metody CreateAsync poskytovatele služby. Zde je podpis této metody: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource is defined in 
 // Microsoft.SCIM.Schemas.  

 Task<Resource> CreateAsync(IRequest<Resource> request);
```

V požadavku na zřízení uživatele je hodnota argumentu prostředku instancí třídy Microsoft.SCIM.Core2EnterpriseUser definované v knihovně Microsoft.SCIM.Schemas.  Pokud je požadavek na zřízení uživatele úspěšný, očekává se, že implementace metody vrátí instanci třídy Microsoft.SCIM.Core2EnterpriseUser s hodnotou VlastnostI Identifier nastavenou na jedinečný identifikátor nově zřízeného Uživatele.  

***Příklad 3. Dotaz na aktuální stav uživatele*** 

Chcete-li aktualizovat uživatele, o kterých je známo, že existuje v úložišti identit, které je vedeno objektem SCIM, služba Azure Active Directory pokračuje tak, že požaduje aktuální stav tohoto uživatele ze služby s požadavkem, například: 

```
 GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
 Authorization: Bearer ...
```

V ukázkovém kódu je požadavek přeložen do volání metody RetrieveAsync poskytovatele služby. Zde je podpis této metody: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.Resource and 
 // Microsoft.SCIM.IResourceRetrievalParameters 
 // are defined in Microsoft.SCIM.Schemas 

 Task<Resource> RetrieveAsync(IRequest<IResourceRetrievalParameters> request);
```

V příkladu požadavku na načtení aktuálního stavu uživatele jsou hodnoty vlastností objektu poskytnuté jako hodnota argumentu parameters následující: 
  
* Identifikátor: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

***Příklad 4. Dotaz na hodnotu atributu reference, který má být aktualizován*** 

Pokud má být atribut odkazu aktualizován, azure active directory dotazy služby k určení, zda aktuální hodnota atributu reference v úložišti identity fronted službou již odpovídá hodnotu tohoto atributu v Azure Active Adresář. Pro uživatele je jediným atributem, jehož aktuální hodnota je dotazován tímto způsobem atribut správce. Zde je příklad požadavku k určení, zda atribut správce objektu uživatele má aktuálně určitou hodnotu: V ukázkovém kódu je požadavek přeložen do volání metody QueryAsync poskytovatele služby. Hodnota vlastností objektu, který je k dispozici jako hodnota argumentu parameters, je následující: 
  
* Parametry. AlternativníFiltry.Počet: 2
* Parametry. AlternateFilters.Elementat(x). AttributePath: "ID"
* Parametry. AlternateFilters.Elementat(x). ComparisonOperator: ComparisonOperator.Equals
* Parametry. AlternateFilter.Elementat(x). Srovnávací hodnota: "54D382A4-2050-4C03-94D1-E769F1D15682"
* Parametry. AlternateFilters.ElementAt(y). AttributePath: "manažer"
* Parametry. AlternateFilters.ElementAt(y). ComparisonOperator: ComparisonOperator.Equals
* Parametry. AlternateFilter.ElementAt(y). PorovnáníHodnota: "2819c223-7f76-453a-919d-4138619046466"
* Parametry. RequestedAttributePaths.ElementAt(0): "ID"
* Parametry. SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

Zde hodnota indexu x může být 0 a hodnota indexu y může být 1 nebo hodnota x může být 1 a hodnota y může být 0, v závislosti na pořadí výrazů parametru dotazu filtru.   

***Příklad 5. Žádost z Azure AD na službu SCIM k aktualizaci uživatele*** 

Tady je příklad požadavku služby Azure Active Directory na službu SCIM o aktualizaci uživatele: 

```
  PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
  Content-type: application/scim+json
  {
    "schemas": 
    [
      "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations":
    [
      {
        "op":"Add",
        "path":"manager",
        "value":
          [
            {
              "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
              "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
```

V ukázkovém kódu je požadavek přeložen do volání metody UpdateAsync poskytovatele služby. Zde je podpis této metody: 

```csharp
 // System.Threading.Tasks.Tasks and 
 // System.Collections.Generic.IReadOnlyCollection<T>  // are defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in
 // Microsoft.SCIM.Service.
 // Microsoft.SCIM.IPatch, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task UpdateAsync(IRequest<IPatch> request);
```

V příkladu požadavku na aktualizaci uživatele má objekt poskytnutý jako hodnota argumentu patch tyto hodnoty vlastností: 
  
* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schémata:rozšíření:organizace:2.0:Uživatel"
* (PatchRequest jako PatchRequest2). Počet operací:1
* (PatchRequest jako PatchRequest2). Operations.Elementat(0). OperationName: OperationName.Add
* (PatchRequest jako PatchRequest2). Operations.Elementat(0). Path.AttributePath: "manažer"
* (PatchRequest jako PatchRequest2). Operations.Elementat(0). Hodnota.Počet: 1
* (PatchRequest jako PatchRequest2). Operations.Elementat(0). Value.Elementat(0). Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest jako PatchRequest2). Operations.Elementat(0). Value.Elementat(0). Hodnota: 2819c223-7f76-453a-919d-4138619046466

***Příklad 6. Zrušení zřízení uživatele***

Chcete-li zrušení zřízení uživatele z úložiště identit v čele se službou SCIM, Azure AD odešle požadavek, jako jsou:

```
  DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
  Authorization: Bearer ...
```

V ukázkovém kódu je požadavek přeložen do volání metody DeleteAsync poskytovatele služby. Zde je podpis této metody: 

```csharp
 // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
 // Microsoft.SCIM.IRequest is defined in 
 // Microsoft.SCIM.Service.  
 // Microsoft.SCIM.IResourceIdentifier, 
 // is defined in Microsoft.SCIM.Protocol. 

 Task DeleteAsync(IRequest<IResourceIdentifier> request);
```

Objekt zadaný jako hodnota argumentu resourceIdentifier má tyto hodnoty vlastností v příkladu požadavku na zrušení zřízení uživatele: 

* ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schémata:rozšíření:organizace:2.0:Uživatel"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Krok 4: Integrace koncového bodu SCIM s klientem Azure AD SCIM

Azure AD lze nakonfigurovat tak, aby automaticky zřizování přiřazených uživatelů a skupin aplikací, které implementují konkrétní profil [protokolu SCIM 2.0](https://tools.ietf.org/html/rfc7644). Specifika profilu jsou popsány v [kroku 2: Pochopit implementaci Scim Azure AD](#step-2-understand-the-azure-ad-scim-implementation).

Ověřte u svého poskytovatele aplikace nebo dokumentace poskytovatele aplikace prohlášení o kompatibilitě s těmito požadavky.

> [!IMPORTANT]
> Implementace Scim Azure AD je postavená na službě zřizování uživatelů Azure AD, která je navržena tak, aby neustále udržovala uživatele synchronizované mezi Azure AD a cílovou aplikací a implementuje velmi specifickou sadu standardních operací. Je důležité pochopit, toto chování pochopit chování klienta Azure AD SCIM. Další informace naleznete v části [Zřizování cykly: Počáteční a přírůstkové](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) v [Jak zřizování funguje](how-provisioning-works.md).

### <a name="getting-started"></a>Začínáme

Aplikace, které podporují profil SCIM popsaný v tomto článku, lze připojit ke službě Azure Active Directory pomocí funkce "aplikace bez galerie" v galerii aplikací Azure AD. Po připojení Azure AD spustí proces synchronizace každých 40 minut, kde se dotazuje aplikace SCIM koncový bod pro přiřazené uživatele a skupiny a vytvoří nebo upraví je podle podrobností přiřazení.

**Připojení aplikace, která podporuje SCIM:**

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com). Všimněte si, že můžete získat přístup k bezplatné zkušební verzi pro Azure Active Directory s licencemi P2 po přihlášení do [vývojářského programu](https://developer.microsoft.com/office/dev-program)
2. V levém podokně vyberte **podnikové aplikace.** Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací, které byly přidány z galerie.
3. Vyberte **+ Nová aplikace** > **Všechny** > **aplikace bez galerie**.
4. Zadejte název aplikace a vyberte **Přidat,** chcete-li vytvořit objekt aplikace. Nová aplikace se přidá do seznamu podnikových aplikací a otevře se na obrazovce správy aplikací.

   ![Snímek obrazovky zobrazuje galerii aplikací Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Galerie aplikací Azure AD*

5. Na obrazovce správy aplikací vyberte **Zřizování** v levém panelu.
6. V nabídce **Režim zřizování** vyberte **Možnost Automaticky**.

   ![Příklad: Stránka zřizování aplikace na webu Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurace zřizování na webu Azure Portal*

7. Do pole **Adresa URL klienta** zadejte adresu URL koncového bodu SCIM aplikace. Příklad: `https://api.contoso.com/scim/`
8. Pokud koncový bod SCIM vyžaduje token nosiče OAuth od jiného vystavitele než Azure AD, zkopírujte požadovaný token oauth do pole **volitelného tajného tokenu.** Pokud toto pole zůstane prázdné, Azure AD obsahuje token nosiče OAuth vydaný z Azure AD s každým požadavkem. Aplikace, které používají Azure AD jako zprostředkovatele identity můžete ověřit tento token vydaný Azure AD. 
   > [!NOTE]
   > Toto pole se ***nedoporučuje*** ponechat prázdné a spoléhat se na token generovaný službou Azure AD. Tato možnost je k dispozici především pro účely testování.
9. Vyberte **Testovat připojení,** chcete-li, aby se služba Azure Active Directory pokusila připojit ke koncovému bodu SCIM. Pokud se pokus nezdaří, zobrazí se informace o chybě.  

    > [!NOTE]
    > **Test Connection** dotazuje koncový bod SCIM pro uživatele, který neexistuje, pomocí náhodného identifikátoru GUID jako odpovídající vlastnost vybrané v konfiguraci Azure AD. Očekávaná správná odpověď je HTTP 200 OK s prázdnou zprávou SCIM ListResponse.

10. Pokud se pokusy o připojení k aplikaci podaří úspěšně, pak vyberte **Uložit,** chcete-li uložit pověření správce.
11. V části **Mapování** existují dvě volitelné sady [mapování atributů](customize-application-attributes.md): jedna pro objekty uživatele a jedna pro objekty skupiny. Vyberte každou z nich a zkontrolujte atributy, které jsou synchronizovány z Azure Active Directory do vaší aplikace. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatelům a skupinám ve vaší aplikaci pro operace aktualizace. Chcete-li potvrdit všechny změny, vyberte **uložit.**

    > [!NOTE]
    > Volitelně můžete zakázat synchronizaci objektů skupiny zakázáním mapování "skupiny".

12. V části **Nastavení**pole **Obor** definuje, kteří uživatelé a skupiny jsou synchronizováni. Vyberte **Synchronizovat pouze přiřazené uživatele a skupiny** (doporučeno), chcete-li synchronizovat pouze uživatele a skupiny přiřazené na kartě **Uživatelé a skupiny.**
13. Po dokončení konfigurace nastavte **stav zřizování** **na Zapnuto**.
14. Vyberte **Uložit** a spusťte zřizovací službu Azure AD.
15. Pokud synchronizujete pouze přiřazené uživatele a skupiny (doporučeno), nezapomeňte vybrat kartu **Uživatelé a skupiny a** přiřadit uživatelům nebo skupinám, které chcete synchronizovat.

Po spuštění počátečního cyklu můžete vybrat **Zřizování protokolů** v levém panelu pro sledování průběhu, který zobrazuje všechny akce provedené službou zřizování ve vaší aplikaci. Další informace o tom, jak číst protokoly zřizování Azure AD, naleznete [v tématu Vytváření sestav na automatické zřizování uživatelských účtů](check-status-user-account-provisioning.md).

> [!NOTE]
> Počáteční cyklus trvá déle než pozdější synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěna.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Krok 5: Publikování aplikace do galerie aplikací Azure AD

Pokud vytváříte aplikaci, která bude používat více než jeden tenant, můžete ji zpřístupnit v galerii aplikací Azure AD. To usnadní organizacím zjistit aplikaci a nakonfigurovat zřizování. Publikování aplikace v galerii Azure AD a zpřístupnění zřizování ostatním je snadné. Podívejte se na kroky [zde](../develop/howto-app-gallery-listing.md). Společnost Microsoft s vámi bude spolupracovat na integraci vaší aplikace do naší galerie, testování koncového bodu a vydání [dokumentace](../saas-apps/tutorial-list.md) k připojení pro zákazníky. 

### <a name="gallery-onboarding-checklist"></a>Kontrolní seznam pro registraci galerie
Postupujte podle níže uvedeného kontrolního seznamu a ujistěte se, že vaše aplikace je na palubě quicky a zákazníci mají bezproblémové nasazení. Informace budou získány od vás při nástupu do galerie. 
> [!div class="checklist"]
> * Podpora koncového bodu uživatele a skupiny [SCIM 2.0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) (je vyžadován pouze jeden, ale oba jsou doporučeny)
> * Podpora alespoň 25 požadavků za sekundu na klienta (povinné)
> * Navázat technické a podpůrné kontakty, které zákazníkům po zavádějí informace o registraci v galerii (povinné)
> * 3 Nekončící testovací pověření pro vaši aplikaci (povinné)
> * Podporujte udělení autorizačního kódu OAuth nebo dlouhodobý token, jak je popsáno níže (povinné)
> * Vytvoření technického a podpůrného kontaktního místa pro podporu zákazníků po registraci v galerii (povinné)
> * Podpora aktualizace více členství ve skupinách pomocí jednoho PATCH (doporučeno) 
> * Zdokumentujte koncový bod SCIM veřejně (doporučeno) 
> * [Zjišťování schématu podpory](https://tools.ietf.org/html/rfc7643#section-6) (doporučeno)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorizace zřizovacích konektorů v galerii aplikací
Spec SCIM nedefinuje schéma specifické pro SCIM pro ověřování a autorizaci. Spoléhá se na používání stávajících průmyslových norem. Klient zřizování Azure AD podporuje dvě metody autorizace pro aplikace v galerii. 

|Autorizační metoda|Výhody|Nevýhody|Podpora|
|--|--|--|--|
|Uživatelské jméno a heslo (není doporučeno nebo podporované službou Azure AD)|Snadná implementace|Nejistá - [Váš Pa $ $word nezáleží](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Podporováno případ od případu pro aplikace galerie. Není podporováno pro aplikace bez galerie.|
|Dlouhověký žeton nosiče|Dlouhodobé tokeny nevyžadují, aby byl přítomen uživatel. Jsou snadno pro správce použít při nastavování zřizování.|Dlouhodobé tokeny může být obtížné sdílet s správcem bez použití nezabezpečené metody, jako je například e-mail. |Podporováno pro aplikace galerie a aplikace, které nejsou galerie. |
|Udělení autorizačního kódu OAuth|Přístupové tokeny jsou mnohem kratší než hesla a mají automatizovaný mechanismus aktualizace, který dlouhodobé nosné tokeny nemají.  Skutečný uživatel musí být přítomen během počáteční autorizace a přidat úroveň odpovědnosti. |Vyžaduje, aby byl uživatel přítomen. Pokud uživatel opustí organizaci, token je neplatný a autorizace bude nutné znovu dokončit.|Podporováno pro aplikace galerie. Podpora aplikací, které nejsou galerie, probíhá.|
|Udělení pověření klienta OAuth|Přístupové tokeny jsou mnohem kratší než hesla a mají automatizovaný mechanismus aktualizace, který dlouhodobé nosné tokeny nemají. Udělení autorizačního kódu a pověření klienta udělit vytvořit stejný typ přístupového tokenu, takže přechod mezi těmito metodami je transparentní rozhraní API.  Zřizování může být zcela automatizované a nové tokeny lze bezvýhradně požadovat bez interakce s uživatelem. ||Není podporováno pro aplikace galerie a aplikace, které nejsou galerie. Podpora je v našich nevyřízených položkách.|

[!NOTE] V unovém unovém unovém ujetí konfigurace Azure AD se nedoporučuje ponechat pole tokenu prázdné. Generovaný token je primárně k dispozici pro účely testování.

**Tok udělení autorizačního kódu OAuth:** Zřizovací služba podporuje [udělení autorizačního kódu](https://tools.ietf.org/html/rfc6749#page-24). Po odeslání žádosti o publikování aplikace v galerii s vámi náš tým bude spolupracovat na shromažďování následujících informací:
*  Adresa URL autorizace: Adresa URL klienta pro získání autorizace od vlastníka prostředku prostřednictvím přesměrování uživatelského agenta. Uživatel je přesměrován na tuto adresu URL, aby povolil přístup. 
*  Adresa URL výměny tokenů: Adresa URL klienta pro výměnu autorizačního grantu pro přístupový token, obvykle s ověřováním klienta.
*  ID klienta: Autorizační server vydá registrovanému klientovi identifikátor klienta, což je jedinečný řetězec představující informace o registraci poskytnuté klientem.  Identifikátor klienta není tajný klíč. je vystaven a vlastníka prostředku a **nesmí** být použit samostatně pro ověřování klienta.  
*  Tajný klíč klienta: Tajný klíč klienta je tajný klíč generovaný autorizačním serverem. Měla by se jedná o jedinečnou hodnotu známou pouze autorizačnímu serveru. 

Všimněte si, že OAuth v1 není podporována z důvodu expozice tajného klíče klienta. OAuth v2 je podporován.  

Doporučené postupy (doporučené, ale nepovinné):
* Podpora více adres URL přesměrování. Správci mohou konfigurovat zřizování z "portal.azure.com" a "aad.portal.azure.com". Podpora více adres URL přesměrování zajistí, že uživatelé mohou autorizovat přístup z obou portálů.
* Podpořte více tajných kódů, abyste zajistili hladkou tajnou obnovu bez prostojů. 

**Dlouho žil OAuth nosič žetony:** Pokud vaše aplikace nepodporuje tok udělení autorizačního kódu OAuth, můžete také vygenerovat dlouho trvající token nosiče OAuth, než který může správce použít k nastavení integrace zřizování. Token by měl být trvalý, jinak úloha zřizování bude po vypršení platnosti tokenu [v karanténě.](application-provisioning-quarantine-status.md) Tento token musí být menší než 1 KB ve velikosti.  

Další metody ověřování a autorizace nám dejte vědět na [UserVoice](https://aka.ms/appprovisioningfeaturerequest).

### <a name="gallery-go-to-market-launch-check-list"></a>Galerie go-to-market spuštění kontrolní seznam
Chcete-li zvýšit povědomí a poptávku po naší společné integraci, doporučujeme aktualizovat stávající dokumentaci a zesílit integraci do marketingových kanálů.  Níže je sada kontrolních seznamů, které doporučujeme dokončit, abyste podpořili spuštění

* **Připravenost na prodej a zákaznickou podporu.** Ujistěte se, že vaše prodejní a podpůrné týmy jsou si vědomi a mohou mluvit s možnostmi integrace. Informujte svůj prodejní a podpůrný tým, poskytněte mu časté dotazy a zahrňte integraci do prodejních materiálů. 
* **Blog post a / nebo tisková zpráva.** Vytvořte blogový příspěvek nebo tiskovou zprávu, která popisuje společnou integraci, výhody a způsob, jak začít. [Příklad: Tisková zpráva Imprivata a Azure Active Directory](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Sociální média.** Využijte své sociální média, jako je Twitter, Facebook nebo LinkedIn, k propagaci integrace se svými zákazníky. Ujistěte se, že patří, @AzureAD takže můžeme retweet svůj příspěvek. [Příklad: Imprivata Twitter Post](https://twitter.com/azuread/status/1123964502909779968)
* **Marketingové webové stránky.** Vytvořte nebo aktualizujte marketingové stránky (např. stránku integrace, stránku partnera, stránku s cenami atd.), aby zahrnovaly dostupnost společné integrace. [Příklad: Stránka integrace pingboardu](https://pingboard.com/org-chart-for), [stránka integrace smartsheetu](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Monday.com cenovou stránku](https://monday.com/pricing/) 
* **Technická dokumentace.** Vytvořte článek centra nápovědy nebo technickou dokumentaci o tom, jak mohou zákazníci začít. [Příklad: Integrace vyslance + Microsoft Azure Active Directory.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Komunikace se zákazníky.** Upozorněte zákazníky na novou integraci prostřednictvím komunikace se zákazníky (měsíční bulletiny, e-mailové kampaně, poznámky k verzi produktu). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Povolit IP adresy používané zřizovací službou Azure AD k provádění požadavků SCIM

Některé aplikace umožňují příchozí provoz do své aplikace. Aby zřizovací služba Azure AD fungovala podle očekávání, musí být povoleny použité IP adresy. Seznam IP adres pro každou značku služby nebo oblast najdete v souboru JSON – [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519). Tyto IP adresy si můžete podle potřeby stáhnout a naprogramovat do brány firewall. Vyhrazené rozsahy IP adres pro zřizování Azure AD najdete v části "AzureActiveDirectoryDomainServices."

## <a name="related-articles"></a>Související články

* [Automatizace zřizování uživatelů a deprovisioning u aplikací SaaS](user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](customize-application-attributes.md)
* [Psaní výrazů pro mapování atributů](functions-for-customizing-application-data.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Oznámení o zřizování účtů](user-provisioning.md)
* [Seznam výukových programů o tom, jak integrovat aplikace SaaS](../saas-apps/tutorial-list.md)

---
title: Kurz – vývoj koncového bodu SCIM pro zřizování uživatelů pro aplikace ze služby Azure AD
description: Systém pro správu identit mezi doménami (SCIM) standardizace Automatické zřizování uživatelů. V tomto kurzu se naučíte vyvíjet SCIM koncový bod, integrovat rozhraní SCIM API pomocí Azure Active Directory a začít automatizovat zřizování uživatelů a skupin do cloudových aplikací.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 04/12/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 4130ed4bb690edb3c0c5d72d7d158262ed6ff39d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305595"
---
# <a name="tutorial-develop-and-plan-provisioning-for-a-scim-endpoint"></a>Kurz: vývoj a plánování zřizování pro koncový bod SCIM

Jako vývojář aplikace můžete použít systém pro rozhraní API pro správu uživatelů mezi doménami (SCIM) a povolit tak Automatické zřizování uživatelů a skupin mezi vaší aplikací a službou Azure AD (AAD). Tento článek popisuje, jak vytvořit koncový bod SCIM a jak ho integrovat se službou AAD Provisioning. Specifikace SCIM poskytuje společné uživatelské schéma pro zřizování. Při použití ve spojení s federačními standardy, jako je SAML nebo OpenID Connect, SCIM poskytuje správcům ucelené řešení založené na standardech pro správu přístupu.

![Zřizování z Azure AD do aplikace pomocí SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

SCIM je standardizovaná definice dvou koncových bodů: `/Users` koncový bod a `/Groups` koncový bod. Používá běžné operace REST k vytváření, aktualizaci a odstraňování objektů a předdefinované schéma pro běžné atributy, jako je název skupiny, uživatelské jméno, křestní jméno, příjmení a e-mail. Aplikace, které nabízejí SCIM 2,0 REST API můžou snížit nebo eliminovat přehlednost práce s rozhraním API pro správu uživatelů. Například libovolný kompatibilní klient SCIM ví, jak vytvořit novou položku uživatele pomocí HTTP POST objektu JSON pro `/Users` koncový bod. Místo toho, aby pro stejné základní akce bylo nutné trochu odlišné rozhraní API, můžou aplikace, které odpovídají standardu SCIM, okamžitě využít výhod existujících klientů, nástrojů a kódu. 

Standardní schéma uživatelského objektu a rozhraní REST API pro správu definovaná v SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) umožňují snazší integraci zprostředkovatelů identity a aplikací. Vývojáři aplikací, kteří vytvářejí koncový bod SCIM, mohou být integrováni s jakýmkoli klientem kompatibilním s SCIM, aniž by museli provádět vlastní práci.

K automatizaci zřizování pro aplikaci budete potřebovat sestavování a integraci koncového bodu SCIM s klientem Azure AD SCIM. Pomocí následujících kroků spustíte zřizování uživatelů a skupin do vaší aplikace. 
    
1. Návrh schématu uživatelů a skupin

   Identifikujte objekty a atributy aplikace a určete tak, jak se mapují na schéma uživatele a skupiny podporované implementací AAD SCIM.

1. Princip implementace AAD SCIM

   Seznamte se s tím, jak je klient služby AAD SCIM implementovaný pro modelování požadavků a odpovědí na protokoly SCIM.

1. Vytvoření koncového bodu SCIM

   Koncový bod musí být SCIM 2,0, který je kompatibilní pro integraci se službou pro zřizování AAD. Jako možnost můžete k sestavení koncového bodu použít knihovny a ukázky kódu Microsoft Common Language Infrastructure (CLI). Tyto ukázky jsou pouze pro referenci a testování; Doporučujeme, abyste je používali jako závislosti ve vaší produkční aplikaci.

1. Integrace koncového bodu SCIM s klientem AAD SCIM 

   Pokud vaše organizace používá aplikaci třetí strany k implementaci profilu SCIM 2,0, který AAD podporuje, můžete rychle automatizovat zřizování a rušení zřizování uživatelů a skupin.

1. Publikování aplikace v galerii aplikací AAD 

   Usnadňují zákazníkům zjišťování vaší aplikace a snadné konfiguraci zřizování. 

![Postup integrace koncového bodu SCIM s Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="design-your-user-and-group-schema"></a>Návrh schématu uživatelů a skupin

Každá aplikace vyžaduje pro vytvoření uživatele nebo skupiny jiné atributy. Zahajte integraci určením požadovaných objektů (uživatelů, skupin) a atributů (název, manažer, pracovní pozice atd.), které vaše aplikace potřebuje. 

Standard SCIM definuje schéma pro správu uživatelů a skupin. 

**Základní** schéma uživatele vyžaduje pouze tři atributy (všechny ostatní atributy jsou nepovinné):

- `id`, definovaný identifikátor poskytovatele služby
- `userName`Jedinečný identifikátor pro uživatele (obvykle se mapuje na hlavní název uživatele Azure AD)
- `meta`, metadata *jen pro čtení* udržovaná poskytovatelem služeb

Kromě **základního** uživatelského schématu definuje SCIM Standard uživatelské rozšíření **organizace** s modelem pro rozšíření schématu uživatele, aby splňovalo potřeby vaší aplikace. 

Pokud například vaše aplikace vyžaduje e-maily uživatele i správce uživatele, použijte **základní** schéma ke shromáždění e-mailu uživatele a schématu uživatele **podniku** ke shromáždění manažera daného uživatele.

Pro návrh schématu použijte následující postup:

1. Seznam atributů, které vaše aplikace vyžaduje, a jejich kategorizace jako atributů potřebných pro ověřování (například loginName a e-mail), atributů potřebných ke správě životního cyklu uživatele (například stav/aktivní) a všech dalších atributů, které aplikace potřebuje k fungování (například správce, značka).

1. Ověřte, zda jsou atributy již definovány ve schématu **základního** uživatele nebo schématu uživatele **organizace** . V takovém případě je nutné definovat rozšíření schématu uživatele, které pokrývá chybějící atributy. Pokud chcete, aby uživatel mohl zřídit uživatele, zobrazte si níže uvedený příklad pro rozšíření uživatele `tag` .

1. Namapujte atributy SCIM na atributy uživatelů v Azure AD. Pokud některý z atributů, které jste definovali ve vašem koncovém bodu SCIM, nemá ve schématu uživatele Azure AD jasný protějšek, požádejte správce tenanta, aby rozšířil své schéma, nebo použijte atribut rozšíření, jak je uvedeno níže pro `tags` vlastnost.

|Požadovaný atribut aplikace|Mapovaný atribut SCIM|Atribut mapované služby Azure AD|
|--|--|--|
|loginName|userName|userPrincipalName (Hlavní název uživatele)|
|firstName|name.givenName|givenName|
|lastName|name.familyName|Příjmení|
|workMail|e-maily [typ EQ "Work"]. Value|Poštovní|
|manager|manager|manager|
|značka|urn: IETF: params: SCIM: schémata: rozšíření: 2.0: CustomExtension: tag|extensionAttribute1|
|status|active|isSoftDeleted (vypočtená hodnota neuložená na uživateli)|

**Příklad seznamu požadovaných atributů**

```json
{
     "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User",
      "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
      "urn:ietf:params:scim:schemas:extension:CustomExtensionName:2.0:User"],
     "userName":"bjensen@testuser.com",
     "id": "48af03ac28ad4fb88478",
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
}   
```
**Příklad schématu definovaného datovou částí JSON**

> [!NOTE]
> Kromě atributů vyžadovaných pro aplikaci obsahuje reprezentace JSON také požadované `id` `externalId` atributy, a `meta` .

Pomáhá kategorizovat mezi `/User` a `/Group` namapovat všechny výchozí atributy uživatele v Azure AD na SCIM RFC, najdete v tématu [jak přizpůsobit atributy mezi Azure AD a vaším koncovým bodem SCIM](customize-application-attributes.md).


| Azure Active Directory uživatel | "urn: IETF: param: SCIM: schemas: rozšíření: Enterprise: 2.0: User" |
| --- | --- |
| IsSoftDeleted |active |
|Oddělení|urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|
| displayName |displayName |
|Zaměstnance|urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|
| Facsimile-TelephoneNumber |phoneNumbers [typ EQ "fax"]. hodnota |
| givenName |name.givenName |
| jobTitle |title |
| pošta |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager |
| mobil |phoneNumbers[type eq "mobile"].value |
| postalCode |adresy [typ EQ "Work"]. postalCode |
| proxy – adresy |e-maily [Type EQ "other"]. Osa |
| fyzické doručování – Office |adresy [Type EQ "other"]. Formátu |
| streetAddress |adresy [Type EQ "]. streetAddress |
| surname |name.familyName |
| telefonní číslo |phoneNumbers[type eq "work"].value |
| uživatel – hlavní |userName |

**Příklad seznamu atributů uživatelů a skupin**

| Skupina Azure Active Directory | urn: IETF: parametry: SCIM: schemas: Core: 2.0: Group |
| --- | --- |
| displayName |displayName |
| pošta |emails[type eq "work"].value |
| mailNickname |displayName |
| členy |členy |
| objectId |externalId |
| proxyAddresses |e-maily [Type EQ "other"]. Osa |

**Příklad seznamu atributů skupin**

> [!NOTE]
> Nemusíte podporovat uživatele i skupiny nebo všechny zobrazené atributy, je to jenom odkaz na to, jak se atributy ve službě Azure AD často mapují na vlastnosti v protokolu SCIM. 

V dokumentu RFC SCIM je definováno několik koncových bodů. Můžete začít s `/User` koncovým bodem a pak ho odtud rozbalit. 

|Koncový bod|Popis|
|--|--|
|/User|Provádění operací CRUD u objektu uživatele.|
|/Group|Proveďte operace CRUD u objektu skupiny.|
|/Schemas|Sada atributů podporovaná jednotlivými klienty a poskytovatelem služeb se může lišit. Jeden poskytovatel služeb může zahrnovat `name` , `title` a `emails` , i když jiný poskytovatel služeb používá `name` , `title` a `phoneNumbers` . Koncový bod schémat umožňuje zjišťování podporovaných atributů.|
|/Bulk|Hromadné operace umožňují provádět operace s velkou kolekcí objektů prostředků v rámci jedné operace (například členství v aktualizacích pro velkou skupinu).|
|/ServiceProviderConfig|Obsahuje podrobné informace o funkcích podporovaného standardu SCIM, například o podporovaných zdrojích a metodě ověřování.|
|/ResourceTypes|Určuje metadata o jednotlivých prostředcích.|

**Příklad seznamu koncových bodů**

> [!NOTE]
> Použijte `/Schemas` koncový bod k podpoře vlastních atributů nebo pokud se schéma často mění, protože umožňuje klientovi, aby získal nejaktuálnější schéma automaticky. Použijte `/Bulk` koncový bod pro podporu skupin.

## <a name="understand-the-aad-scim-implementation"></a>Princip implementace AAD SCIM

V rámci podpory rozhraní API pro správu uživatelů SCIM 2,0 Tato část popisuje, jak je implementován klient služby AAD SCIM a ukazuje, jak modelovat zpracování požadavků protokolu SCIM a odpovědi.

> [!IMPORTANT]
> Chování implementace Azure AD SCIM se naposledy aktualizovalo 18. prosince 2018. Informace o tom, co se změnilo, najdete v článku [dodržování předpisů protokolu SCIM 2,0 ve službě Azure AD pro zřizování uživatelů](application-provisioning-config-problem-scim-compatibility.md).

V rámci [specifikace protokolu SCIM 2,0](http://www.simplecloud.info/#Specification)musí vaše aplikace podporovat tyto požadavky:

|Požadavek|Referenční poznámky (protokol SCIM)|
|-|-|
|Vytváření uživatelů a volitelně také skupin|[oddíl 3,3](https://tools.ietf.org/html/rfc7644#section-3.3)|
|Úprava uživatelů nebo skupin s požadavky na opravu|[oddíl 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2) Podpora zajišťuje, že skupiny a uživatelé jsou zřízeny způsobem.|
|Načtěte známý prostředek pro uživatele nebo skupinu, které jste vytvořili dříve.|[oddíl 3.4.1](https://tools.ietf.org/html/rfc7644#section-3.4.1)|
|Dotazování uživatelů nebo skupin|[oddíl 3.4.2](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Ve výchozím nastavení jsou uživatelé načítáni pomocí `id` a dotazováni jejich `username` a a `externalId` skupiny jsou dotazovány nástrojem `displayName` .|
|Dotazování uživatele podle ID a podle manažera|oddíl 3.4.2|
|Skupiny dotazů podle ID a podle členů|oddíl 3.4.2|
|Filter [excludedAttributes = Members](#get-group) při dotazování na prostředek skupiny|oddíl 3.4.2.5|
|Přijměte jeden nosný token pro ověřování a autorizaci AAD pro vaši aplikaci.||
|Obnovitelné odstranění uživatele `active=false` a obnovení uživatele `active=true`|Objekt uživatele by měl být vrácen v požadavku bez ohledu na to, zda je uživatel aktivní. Jediným okamžikem, kdy by uživatel neměl být vrácen, je v případě, že je pevným smazán z aplikace.|
|Podpora koncového bodu/schemas|[oddíl 7](https://tools.ietf.org/html/rfc7643#page-30) Koncový bod zjišťování schématu se použije ke zjištění dalších atributů.|

Při implementaci koncového bodu SCIM použijte obecné pokyny pro zajištění kompatibility s AAD:

* `id` je požadovaná vlastnost pro všechny prostředky. Každá odpověď, která vrací prostředek, by měla mít jistotu, že každý prostředek má tuto vlastnost, s výjimkou `ListResponse` členů s nulovými členy.
* Odpověď na požadavek na dotaz nebo filtr by měla být vždy typu `ListResponse` .
* Skupiny jsou volitelné, ale podporují se jenom v případě, že implementace SCIM podporuje žádosti o **opravu** .
* V reakci na **opravu** není nutné zahrnout celý prostředek.
* Microsoft AAD používá jenom tyto operátory: `eq` , `and`
* Pro strukturální prvky v SCIM nemusíte rozlišovat velká a malá písmena, zejména  `op` hodnoty operace opravy, jak je definováno v [oddílu 3.5.2](https://tools.ietf.org/html/rfc7644#section-3.5.2). AAD generuje hodnoty `op` jako **Přidat**, **nahradit** a **Odebrat**.
* Služba Microsoft AAD dává žádost o načtení náhodného uživatele a skupiny, aby bylo zajištěno, že koncový bod a přihlašovací údaje jsou platné. Je také proveden jako součást toku **testu připojení** v [Azure Portal](https://portal.azure.com). 
* Atribut, na kterém mohou být prostředky odesílány, by měl být nastaven jako odpovídající atribut v aplikaci v [Azure Portal](https://portal.azure.com), viz [Přizpůsobení mapování atributů zřizování uživatelů](customize-application-attributes.md).
* Podpora HTTPS na koncovém bodu SCIM
* [Zjišťování schématu](#schema-discovery)
  * Zjišťování schématu se v současnosti nepodporuje u vlastní aplikace, ale používá se v některých aplikacích galerie. Po přesměrování se zjišťování schématu použije jako primární metoda pro přidání dalších atributů do konektoru. 
  * Pokud hodnota není k dispozici, neodesílají hodnoty null.
  * Hodnoty vlastností by měly být ve stylu CamelCase použita (např. v/v).
  * Musí vracet odpověď seznamu.
  
### <a name="user-provisioning-and-deprovisioning"></a>Zřizování a rušení uživatelů

Následující ilustrace znázorňuje zprávy, které služba AAD posílá službě SCIM ke správě životního cyklu uživatele v úložišti identit vaší aplikace.  

![Zobrazuje pořadí zřizování a rušení zřizování uživatelů.](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Pořadí zřizování a rušení zřizování uživatelů*

### <a name="group-provisioning-and-deprovisioning"></a>Zřizování skupin a jejich rušení

Zřizování skupin a zrušení zřízení jsou volitelné. Když se implementuje a povolí, na následujícím obrázku se zobrazí zprávy, které služba AAD posílá službě SCIM ke správě životního cyklu skupiny v úložišti identit vaší aplikace. Tyto zprávy se liší od zpráv o uživatelích dvěma způsoby:

* Požadavky na načtení skupin určují, že atribut Members se má vyloučit z libovolného prostředku poskytnutého v reakci na požadavek.  
* Žádosti o zjištění, zda má atribut reference určitou hodnotu, jsou požadavky na atribut members.  

![Zobrazuje pořadí zřizování a rušení zřizování skupin.](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Pořadí zřizování a rušení zřizování skupin*

### <a name="scim-protocol-requests-and-responses"></a>Požadavky a odpovědi protokolu SCIM
V této části najdete příklady požadavků SCIM vygenerovaných klientem AAD SCIM a příklady očekávaných odpovědí. Pro dosažení nejlepších výsledků byste měli kód aplikace, aby zpracovávala tyto požadavky v tomto formátu, a generovat očekávané odpovědi.

> [!IMPORTANT]
> Pokud chcete zjistit, jak a kdy služba AAD pro zřizování uživatelů vysílá operace popsané níže, přečtěte si část [zřizovací cykly: počáteční a přírůstkové](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) , [jak zřizování funguje](how-provisioning-works.md).

[Uživatelské operace](#user-operations)
  - [Vytvořit uživatele](#create-user) ([žádost o](#request)  /  [odpověď](#response))
  - [Získat uživatele](#get-user) ([žádost o](#request-1)  /  [odpověď](#response-1))
  - [Získat uživatele dotazem](#get-user-by-query) ([žádost o](#request-2)  /  [odpověď](#response-2))
  - [Získat uživatele podle dotazů – žádné výsledky](#get-user-by-query---zero-results) ([](#request-3)  /  [odpověď](#response-3)na žádost)
  - [Aktualizace uživatele [vlastnosti s více hodnotami]](#update-user-multi-valued-properties) ([](#request-4)  /  [odpověď](#response-4)na žádost)
  - [Aktualizace uživatele [vlastnosti s jednou hodnotou]](#update-user-single-valued-properties) ([](#request-5)  /  [odpověď](#response-5)na žádost) 
  - [Zakázat uživatele](#disable-user) ([žádost o](#request-14)  /  [odpověď](#response-14))
  - [Odstranit uživatele](#delete-user) ([žádost o](#request-6)  /  [odpověď](#response-6))

[Operace skupiny](#group-operations)
  - [Vytvořit skupinu](#create-group) ([](#request-7)  /  [odpověď](#response-7)na žádost)
  - [Získat skupinu](#get-group) ([](#request-8)  /  [odpověď](#response-8)na žádost)
  - [Získat skupinu podle DisplayName](#get-group-by-displayname) (odpověď na[žádost](#request-9)  /  [](#response-9))
  - [Update Group [atributy nečlenu]](#update-group-non-member-attributes) ([](#request-10)  /  [odpověď](#response-10)na žádost)
  - [Aktualizace skupiny [přidat členy]](#update-group-add-members) ([](#request-11)  /  [odpověď](#response-11)na žádost)
  - [Aktualizace skupiny [odebrat členy]](#update-group-remove-members) ([](#request-12)  /  [odpověď](#response-12)na žádost)
  - [Odstranit skupinu](#delete-group) ([](#request-13)  /  [odpověď](#response-13)na žádost)

[Zjišťování schématu](#schema-discovery)
  - [Zjistit schéma](#discover-schema) ([](#request-15)  /  [odpověď](#response-15)na žádost)

### <a name="user-operations"></a>Uživatelské operace

* Na uživatele lze zadat dotaz pomocí `userName` `email[type eq "work"]` atributů nebo.  

#### <a name="create-user"></a>Vytvořit uživatele

##### <a name="request"></a>Žádost

*PO/Users*
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

*HTTP/1.1 201 vytvořeno*
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

###### <a name="request"></a><a name="request-1"></a>Žádost
*ZÍSKAT/Users/5d48a0a8e9f04aa38008* 

###### <a name="response-user-found"></a><a name="response-1"></a>Odpověď (uživatel se našla)
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
*ZÍSKAT/Users/5171a35d82074e068ce2* 

###### <a name="response-user-not-found-note-that-the-detail-is-not-required-only-status"></a>Odpověď (uživatel nebyl nalezen. Všimněte si, že podrobnosti nejsou požadovány, pouze stav.)

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

##### <a name="request"></a><a name="request-2"></a>Žádost

*ZÍSKAT/Users? Filter = userName EQ "Test_User_dfeef4c5-5681 -4387-B016-bdf221e82081"*

##### <a name="response"></a><a name="response-2"></a>Odpověď

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

#### <a name="get-user-by-query---zero-results"></a>Získat uživatele podle dotazů – žádné výsledky

##### <a name="request"></a><a name="request-3"></a>Žádost

*ZÍSKAT/Users? Filter = userName EQ "neexistující uživatel"*

##### <a name="response"></a><a name="response-3"></a>Odpověď

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

#### <a name="update-user-multi-valued-properties"></a>Aktualizace uživatele [vlastnosti s více hodnotami]

##### <a name="request"></a><a name="request-4"></a>Žádost

*Oprava/Users/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response"></a><a name="response-4"></a>Odpověď

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

#### <a name="update-user-single-valued-properties"></a>Aktualizace uživatele [vlastnosti s jednou hodnotou]

##### <a name="request"></a><a name="request-5"></a>Žádost

*Oprava/Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-5"></a>Odpověď

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

##### <a name="request"></a><a name="request-14"></a>Žádost

*Oprava/Users/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a><a name="response-14"></a>Odpověď

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

##### <a name="request"></a><a name="request-6"></a>Žádost

*Odstranit/Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a><a name="response-6"></a>Odpověď

*HTTP/1.1 204 bez obsahu*

### <a name="group-operations"></a>Operace skupiny

* Skupiny se vždy vytvoří s prázdným seznamem členů.
* Do skupin lze zadat dotaz pomocí `displayName` atributu.
* Aktualizace žádosti o opravu skupiny by měla v odpovědi vracet *HTTP 204 bez obsahu* . Vrácení textu se seznamem všech členů není vhodné.
* Není nutné podporovat vrácení všech členů skupiny.

#### <a name="create-group"></a>Vytvoření skupiny

##### <a name="request"></a><a name="request-7"></a>Žádost

*POST/Groups HTTP/1.1*
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

##### <a name="response"></a><a name="response-7"></a>Odpověď

*HTTP/1.1 201 vytvořeno*
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

##### <a name="request"></a><a name="request-8"></a>Žádost

*ZÍSKAT/Groups/40734ae655284ad3abcc? excludedAttributes = Members HTTP/1.1*

##### <a name="response"></a><a name="response-8"></a>Odpověď
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

#### <a name="get-group-by-displayname"></a>Získat Group by DisplayName

##### <a name="request"></a><a name="request-9"></a>Žádost
*GET/Groups? excludedAttributes = Members&Filter = DisplayName EQ "DisplayName" HTTP/1.1*

##### <a name="response"></a><a name="response-9"></a>Odpověď

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

#### <a name="update-group-non-member-attributes"></a>Aktualizovat skupinu [atributy nečlenské]

##### <a name="request"></a><a name="request-10"></a>Žádost

*Oprava/Groups/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response"></a><a name="response-10"></a>Odpověď

*HTTP/1.1 204 bez obsahu*

### <a name="update-group-add-members"></a>Aktualizace skupiny [přidat členy]

##### <a name="request"></a><a name="request-11"></a>Žádost

*Oprava/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-11"></a>Odpověď

*HTTP/1.1 204 bez obsahu*

#### <a name="update-group-remove-members"></a>Skupina aktualizací [odebrat členy]

##### <a name="request"></a><a name="request-12"></a>Žádost

*Oprava/Groups/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a><a name="response-12"></a>Odpověď

*HTTP/1.1 204 bez obsahu*

#### <a name="delete-group"></a>Odstranění skupiny

##### <a name="request"></a><a name="request-13"></a>Žádost

*Odstranit/Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a><a name="response-13"></a>Odpověď

*HTTP/1.1 204 bez obsahu*

### <a name="schema-discovery"></a>Zjišťování schématu
#### <a name="discover-schema"></a>Zjistit schéma

##### <a name="request"></a><a name="request-15"></a>Žádost
*ZÍSKAT/schemas* 
##### <a name="response"></a><a name="response-15"></a>Odpověď
*HTTP/1.1 200 OK*
```json
{
    "schemas": [
        "urn:ietf:params:scim:api:messages:2.0:ListResponse"
    ],
    "itemsPerPage": 50,
    "startIndex": 1,
    "totalResults": 3,
    "Resources": [
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:User",
    "name" : "User",
    "description" : "User Account",
    "attributes" : [
      {
        "name" : "userName",
        "type" : "string",
        "multiValued" : false,
        "description" : "Unique identifier for the User, typically
used by the user to directly authenticate to the service provider.
Each User MUST include a non-empty userName value.  This identifier
MUST be unique across the service provider's entire set of Users.
REQUIRED.",
        "required" : true,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "server"
      },                
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:User"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:core:2.0:Group",
    "name" : "Group",
    "description" : "Group",
    "attributes" : [
      {
        "name" : "displayName",
        "type" : "string",
        "multiValued" : false,
        "description" : "A human-readable name for the Group.
REQUIRED.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
        "/v2/Schemas/urn:ietf:params:scim:schemas:core:2.0:Group"
    }
  },
  {
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Schema"],
    "id" : "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User",
    "name" : "EnterpriseUser",
    "description" : "Enterprise User",
    "attributes" : [
      {
        "name" : "employeeNumber",
        "type" : "string",
        "multiValued" : false,
        "description" : "Numeric or alphanumeric identifier assigned
to a person, typically based on order of hire or association with an
organization.",
        "required" : false,
        "caseExact" : false,
        "mutability" : "readWrite",
        "returned" : "default",
        "uniqueness" : "none"
      },
    ],
    "meta" : {
      "resourceType" : "Schema",
      "location" :
"/v2/Schemas/urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
    }
  }
]
}
```

### <a name="security-requirements"></a>Požadavky na zabezpečení
**Verze protokolu TLS**

Jedinými přijatelnými verzemi protokolu TLS jsou TLS 1,2 a TLS 1,3. Nejsou povoleny žádné jiné verze TLS. Není povolená žádná verze SSL. 
- Klíče RSA musí mít minimálně 2 048 bitů.
- Klíče ECC musí být alespoň 256 bitů vygenerované pomocí schválené eliptické křivky.

**Délky klíčů**

Všechny služby musí používat certifikáty X. 509 generované pomocí kryptografických klíčů s dostatečnou délkou, což znamená:

**Šifrovací sady**

Všechny služby musí být nakonfigurovány tak, aby používaly následující šifrovací sady, v přesném pořadí uvedeném níže. Všimněte si, že pokud máte jenom certifikát RSA, nainstalovaná šifrovací sada ECDSA nemá žádný vliv. </br>

Minimální pruh šifrovacích sad TLS 1,2:

- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384

### <a name="ip-ranges"></a>Rozsahy IP adres
Služba zřizování Azure AD aktuálně funguje pod rozsahy IP adres pro Azureactivedirectory selhala, jak je uvedeno [tady](https://www.microsoft.com/download/details.aspx?id=56519&WT.mc_id=rss_alldownloads_all). Rozsahy IP adres uvedené pod značkou Azureactivedirectory selhala můžete přidat, pokud chcete, aby byl do vaší aplikace povolený provoz ze služby zřizování Azure AD. Všimněte si, že budete muset pečlivě zkontrolovat seznam rozsahů IP adres pro vypočítané adresy. Adresa, jako je například ' 40.126.25.32 ', může být reprezentována v seznamu rozsahů IP adres jako ' 40.126.0.0/18 '. Seznam rozsahů IP adres můžete také programově načíst pomocí následujícího [rozhraní API](/rest/api/virtualnetwork/servicetags/list).

## <a name="build-a-scim-endpoint"></a>Vytvoření koncového bodu SCIM

Teď, když jste navrhli schéma a rozumíte implementaci Azure AD SCIM, můžete začít s vývojem koncového bodu SCIM. Místo začátku od nuly a sestavení implementace zcela na vlastní, můžete spoléhat na řadu open source knihoven SCIM, které publikovala komunita SCIM.

Pokyny k vytvoření SCIM koncového bodu, včetně příkladů, najdete v tématu [vývoj ukázkového koncového bodu SCIM](use-scim-to-build-users-and-groups-endpoints.md).

[Vzorový ukázkový kód](https://aka.ms/SCIMReferenceCode) .NET Core, který je publikovaný týmem zřizování Azure AD, je jedním z takových prostředků, které můžou začít s vývojem. Po vytvoření SCIM koncového bodu ho budete chtít otestovat. Můžete použít kolekci předávacích [testů](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) , které jsou součástí referenčního kódu, nebo spustit prostřednictvím vzorových požadavků nebo odpovědí uvedených [výše](#user-operations).  

   > [!Note]
   > Referenční kód vám umožňuje začít vytvářet SCIM koncový bod a poskytuje "tak, jak je". Příspěvky z komunity jsou Vítá vás při sestavování a údržbě kódu.

Řešení se skládá ze dvou projektů, _Microsoft. SCIM_ a _Microsoft. SCIM. WebHostSample_.

Projekt _Microsoft. SCIM_ je knihovna, která definuje komponenty webové služby, které jsou v souladu se specifikací SCIM. Deklaruje rozhraní _Microsoft. SCIM. IProvider_, požadavky jsou přeloženy do volání metod poskytovatele, která by byla naprogramována pro provoz v úložišti identit.

![Rozpis: požadavek přeložený do volání metod poskytovatele](media/use-scim-to-provision-users-and-groups/scim-figure-3.png)

Projekt _Microsoft. SCIM. WebHostSample_ je webová aplikace sady Visual Studio ASP.NET Core založená na _prázdné_ šabloně. To umožňuje, aby byl vzorový kód nasazen jako samostatný, hostovaný v kontejnerech nebo v rámci Internetová informační služba. Implementuje taky rozhraní _Microsoft. SCIM. IProvider_ , které udržuje třídy v paměti jako ukázkové úložiště identit.

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

### <a name="building-a-custom-scim-endpoint"></a>Vytvoření vlastního koncového bodu SCIM

Služba SCIM musí mít adresu HTTP a certifikát ověřování serveru, u kterého je kořenová certifikační autorita jedním z následujících názvů:

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* Nedůvěra
* GlobalSign
* Přejít na Daddy
* Společnost
* WoSign
* Kořenová certifikační autorita v čase X3

.NET Core SDK obsahuje certifikát pro vývoj HTTPS, který se dá použít během vývoje, certifikát se nainstaluje jako součást prvního spuštění prostředí. V závislosti na tom, jak spouštíte ASP.NET Core webové aplikace, bude naslouchat na jiném portu:

* Microsoft. SCIM. WebHostSample: https://localhost:5001
* IIS Express: https://localhost:44359/

Další informace o protokolu HTTPS v ASP.NET Core použijte následující odkaz: [vysazení protokolu HTTPS v ASP.NET Core](/aspnet/core/security/enforcing-ssl)

### <a name="handling-endpoint-authentication"></a>Zpracování ověřování koncového bodu

Žádosti od Azure Active Directory zahrnují nosný token OAuth 2,0. Každá služba, která požadavek obdrží, by měla ověřit vystavitele jako Azure Active Directory pro očekávaného tenanta Azure Active Directory.

V tokenu je Vystavitel identifikovaný deklarací ISS, jako je `"iss":"https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/"` . V tomto příkladu základní adresa hodnoty deklarace identity `https://sts.windows.net` identifikuje Azure Active Directory jako Vystavitel, zatímco relativní segment adres _cbb1a5ac-f33b-45fa-9BF5-f37db0fed422_ jedinečný identifikátor Azure Active Directory tenanta, pro který byl token vydán.

Cílovou skupinou pro token bude ID šablony aplikace pro aplikaci v galerii, takže každá z aplikací zaregistrovaných v jednom tenantovi může obdržet stejnou `iss` deklaraci požadavků SCIM. ID šablony aplikace pro všechny vlastní aplikace je _8adf8e6e-67b2-4cf2-a259-e3dc5476c621_. Token vygenerovaný službou Azure AD Provisioning by se měl použít jenom pro testování. Neměl by se používat v produkčním prostředí.

V ukázkovém kódu se požadavky ověřují pomocí balíčku Microsoft. AspNetCore. Authentication. JwtBearer. Následující kód vynutil, že požadavky na koncové body služby jsou ověřovány pomocí tokenu nosiče vydaného Azure Active Directory pro zadaného tenanta:

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

Nosný token je také vyžadován pro použití poskytovaných [testů post](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) a provádí místní ladění pomocí místního hostitele. Vzorový kód používá ASP.NET Core prostředí pro změnu možností ověřování během fáze vývoje a povolení použití tokenu podepsaného svým držitelem.

Další informace o více prostředích v ASP.NET Core najdete v tématu [použití více prostředí v ASP.NET Core](/aspnet/core/fundamentals/environments).

Následující kód vynutil, že požadavky na některý z koncových bodů služby jsou ověřovány pomocí nosných tokenů podepsaných vlastním klíčem:

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

Odeslání požadavku GET kontroleru tokenu k získání platného tokenu nosiče je _GenerateJSONWebToken_ metody vytvořit token odpovídající parametrům nakonfigurovaným pro vývoj:

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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Řízení zřizování a rušení zřizování uživatelů

***Příklad 1. Dotazování služby pro odpovídajícího uživatele***

Azure Active Directory (AAD) dotazuje službu pro uživatele s `externalId` hodnotou atributu, která odpovídá hodnotě atributu mailNickname uživatele v AAD. Dotaz se vyjadřuje jako požadavek HTTP (Hypertext Transfer Protocol), jako je například tento příklad, kde jyoung je ukázka mailNickname uživatele v Azure Active Directory.

>[!NOTE]
> Toto je pouze příklad. Ne všichni uživatelé budou mít atribut mailNickname a hodnota, kterou uživatel nemusí být v adresáři jedinečná. Také atribut použitý pro spárování (v tomto případě je v tomto případě `externalId` ) lze konfigurovat v [mapování atributů AAD](customize-application-attributes.md).

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

V ukázkovém dotazu pro uživatele s danou hodnotou pro `externalId` atribut jsou hodnoty argumentů předaných metodě QueryAsync:

* ukazatelů. AlternateFilters. Count: 1
* ukazatelů. AlternateFilters. ElementAt (0). AttributePath: "externalId"
* ukazatelů. AlternateFilters. ElementAt (0). ComparisonOperator: ComparisonOperator. Equals
* ukazatelů. AlternateFilter. ElementAt (0). ComparisonValue: "jyoung"

***Příklad 2. Zřídit uživatele***

Pokud odpověď na dotaz na webovou službu pro uživatele s `externalId` hodnotou atributu, která odpovídá hodnotě atributu mailNickname uživatele, nevrátí žádné uživatele, požádá AAD, aby služba zřídila uživatele, který odpovídá vašemu adresáři AAD.  Tady je příklad takového požadavku: 

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
   "userName":"jyoung@testuser.com",
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

V žádosti o zřízení uživatele je hodnota argumentu prostředku instancí třídy Microsoft. SCIM. Core2EnterpriseUser definované v knihovně Microsoft. SCIM. schemas.  Pokud požadavek na zřízení uživatele uspěje, pak implementace metody očekává, že se vrátí instance třídy Microsoft. SCIM. Core2EnterpriseUser s hodnotou vlastnosti identifikátor nastavenou na jedinečný identifikátor nově zřízeného uživatele.  

***Příklad 3. Dotaz na aktuální stav uživatele*** 

Chcete-li aktualizovat uživatele, který je známý jako v úložišti identit, front-SCIM, Azure Active Directory pokračuje tím, že požádá o aktuální stav tohoto uživatele ze služby s požadavkem, jako například: 

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

V příkladu žádosti o načtení aktuálního stavu uživatele jsou hodnoty vlastností objektu, které jsou k dispozici jako hodnota argumentu Parameters, následující: 
  
* Identifikátor: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: IETF: param: SCIM: schemas: Extension: Enterprise: 2.0: User"

***Příklad 4. Dotaz na hodnotu referenčního atributu, který se má aktualizovat*** 

Pokud má být aktualizován atribut reference, pak Azure Active Directory dotazování služby, aby zjistila, zda aktuální hodnota atributu reference v úložišti identit, na kterou služba zachází, se již shoduje s hodnotou tohoto atributu v Azure Active Directory. Pro uživatele je jediným atributem, pro který je aktuální hodnota dotazována tímto způsobem atribut správce. Tady je příklad žádosti o zjištění, zda má atribut Manager objektu User Object aktuálně určitou hodnotu: v ukázkovém kódu je požadavek přeložen do volání metody QueryAsync poskytovatele služby. Hodnota vlastností objektu poskytnutého jako hodnota argumentu parameters je následující: 
  
* ukazatelů. AlternateFilters. Count: 2
* ukazatelů. AlternateFilters. ElementAt (x). AttributePath: "ID"
* ukazatelů. AlternateFilters. ElementAt (x). ComparisonOperator: ComparisonOperator. Equals
* ukazatelů. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ukazatelů. AlternateFilters. ElementAt (y). AttributePath: "správce"
* ukazatelů. AlternateFilters. ElementAt (y). ComparisonOperator: ComparisonOperator. Equals
* ukazatelů. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453A-919d-413861904646"
* ukazatelů. RequestedAttributePaths. ElementAt (0): "ID"
* ukazatelů. SchemaIdentifier: "urn: IETF: param: SCIM: schemas: Extension: Enterprise: 2.0: User"

V tomto případě může být hodnota indexu x rovna 0 a hodnota indexu y může být 1 nebo hodnota x může být 1 a hodnota y může být 0 v závislosti na pořadí výrazů parametru dotazu filtru.   

***Příklad 5. Požadavek od Azure AD do služby SCIM k aktualizaci uživatele*** 

Tady je příklad požadavku z Azure Active Directory na službu SCIM k aktualizaci uživatele: 

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

V příkladu žádosti o aktualizaci uživatele má objekt zadaný jako hodnota argumentu patch tyto hodnoty vlastností: 

|Argument|Hodnota|
|-|-|
|ResourceIdentifier. Identifier|"54D382A4-2050-4C03-94D1-E769F1D15682"|
|ResourceIdentifier.SchemaIdentifier|"urn: IETF: param: SCIM: schemas: rozšíření: Enterprise: 2.0: User"|
|(PatchRequest as PatchRequest2). Operations. Count|1|
|(PatchRequest as PatchRequest2). Operations. ElementAt (0). OperationName|OperationName. Add|
|(PatchRequest as PatchRequest2). Operations. ElementAt (0). Cesta. AttributePath|programu|
|(PatchRequest as PatchRequest2). Operations. ElementAt (0). Value. Count|1|
|(PatchRequest as PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Odkaz|http://.../scim/Users/2819c223-7f76-453a-919d-413861904646|
|(PatchRequest as PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Osa| 2819c223-7f76-453A-919d-413861904646|

***Příklad 6. Zrušení zřízení uživatele***

Chcete-li zrušit zřízení uživatele z úložiště identity, které je frontou služby SCIM, služba AAD odešle požadavek, například:

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

Objekt poskytnutý jako hodnota argumentu resourceIdentifier má tyto hodnoty vlastností v příkladu požadavku na zrušení zřízení uživatele: 

* ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier. SchemaIdentifier: "urn: IETF: Paras: SCIM: schemas: Enterprise: 2.0: User"

## <a name="integrate-your-scim-endpoint-with-the-aad-scim-client"></a>Integrace koncového bodu SCIM s klientem AAD SCIM

Služba Azure AD se dá nakonfigurovat tak, aby automaticky zřídila přiřazené uživatele a skupiny aplikacím, které implementují konkrétní profil [protokolu SCIM 2,0](https://tools.ietf.org/html/rfc7644). Konkrétní informace o profilu najdete v dokumentaci [k implementaci Azure AD SCIM](#understand-the-aad-scim-implementation).

Obraťte se na poskytovatele vaší aplikace nebo na dokumentaci poskytovatele vaší aplikace, kde najdete prohlášení o kompatibilitě s těmito požadavky.

> [!IMPORTANT]
> Implementace Azure AD SCIM je postavená na službě Azure AD Provisioning Service, která je navržená tak, aby neustále udržovala synchronizaci mezi službou Azure AD a cílovou aplikací a implementovala velmi specifickou sadu standardních operací. Je důležité porozumět tomuto chování, abyste pochopili chování klienta Azure AD SCIM. Další informace najdete v části [cykly zřizování: počáteční a přírůstkové](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) , [jak zřizování funguje](how-provisioning-works.md).

### <a name="getting-started"></a>Začínáme

Aplikace, které podporují profil SCIM popsané v tomto článku, se dají připojit k Azure Active Directory pomocí funkce aplikace mimo galerii v galerii aplikací Azure AD. Po připojení Azure AD spustí proces synchronizace každých 40 minut, kde se dotazuje koncovému bodu aplikace SCIM pro přiřazené uživatele a skupiny a vytvoří je nebo upraví podle údajů o přiřazení.

**Připojení aplikace, která podporuje SCIM:**

1. Přihlaste se k [portálu AAD](https://aad.portal.azure.com). Všimněte si, že můžete získat přístup k bezplatné zkušební verzi pro Azure Active Directory s licencemi P2, a to tak, že si zaregistrujete [program pro vývojáře](https://developer.microsoft.com/office/dev-program) .
1. V levém podokně vyberte **podnikové aplikace** . Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací přidaných z galerie.
1. Vyberte **+ Nová aplikace**  >  **+ vytvořit vlastní aplikaci**.
1. Zadejte název vaší aplikace, zvolte možnost *integrace jakékoli jiné aplikace, kterou v galerii nenajdete*, a vyberte **Přidat** k vytvoření objektu aplikace. Nová aplikace se přidá do seznamu podnikových aplikací a otevře se na obrazovce správy aplikací.

   ![Snímek obrazovky se zobrazí v galerii aplikací ](media/use-scim-to-provision-users-and-groups/scim-figure-2b-1.png)
    *Azure AD* Galerie aplikací Azure AD.

   > [!NOTE]
   > Pokud používáte staré prostředí Galerie aplikací, postupujte podle pokynů na obrazovce níže.
   
   ![Snímek obrazovky se službou Azure AD stará Experience Galerie aplikací Azure ve starém prostředí ](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)
    *Galerie*

1. Na obrazovce Správa aplikací vyberte na levém panelu možnost **zřizování** .
1. V nabídce **režim zřizování** vyberte **automaticky**.

   ![Příklad: stránka zřizování aplikace v Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurace zřizování v Azure Portal*

1. Do pole **Adresa URL tenanta** zadejte adresu URL koncového bodu SCIM aplikace. Příklad: `https://api.contoso.com/scim/`
1. Pokud koncový bod SCIM vyžaduje token nosiče OAuth od jiného vydavatele než Azure AD, Zkopírujte požadovaný token Bearer OAuth do pole volitelného **tajného tokenu** . Pokud toto pole zůstane prázdné, Azure AD zahrnuje token nosiče OAuth vydaný z Azure AD s každým požadavkem. Aplikace, které používají Azure AD jako zprostředkovatel identity, můžou ověřit tento token vydaný službou Azure AD. 
   > [!NOTE]
   > ***Nedoporučujeme*** toto pole nechat prázdné a spoléhat se na token generovaný službou Azure AD. Tato možnost je primárně k dispozici pro účely testování.
1. Vyberte **Test připojení** , aby se Azure Active Directory pokus o připojení ke koncovému bodu SCIM. Pokud se pokus nezdaří, zobrazí se informace o chybě.  

    > [!NOTE]
    > **Test Connection** se dotazuje na koncový bod SCIM pro uživatele, který neexistuje, pomocí NÁHODNÉho identifikátoru GUID jako odpovídající vlastnosti vybrané v konfiguraci služby Azure AD. Očekávaná správná odpověď je HTTP 200 OK s prázdnou SCIM zprávou ListResponse.

1. Pokud se pokusí připojit k aplikaci úspěšně, vyberte **Uložit** a uložte přihlašovací údaje správce.
1. V oddílu **mapování** existují dvě sady [mapování atributů](customize-application-attributes.md), které lze vybrat: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každou z nich a zkontrolujte atributy, které jsou synchronizované z Azure Active Directory do vaší aplikace. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování s uživateli a skupinami v aplikaci pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    > [!NOTE]
    > Volitelně můžete zakázat synchronizaci objektů skupiny zakázáním mapování "skupiny".

1. V části **Nastavení** pole **obor** určuje, kteří uživatelé a skupiny budou synchronizováni. Vyberte možnost **synchronizovat pouze přiřazené uživatele a skupiny** (doporučeno), chcete-li synchronizovat pouze uživatele a skupiny přiřazené na kartě **Uživatelé a skupiny** .
1. Po dokončení konfigurace nastavte **stav zřizování** **na zapnuto**.
1. Výběrem **Uložit** spusťte službu Azure AD Provisioning.
1. Pokud synchronizujete pouze přiřazené uživatele a skupiny (doporučeno), nezapomeňte vybrat kartu **Uživatelé a skupiny** a přiřadit uživatele nebo skupiny, které chcete synchronizovat.

Po zahájení počátečního cyklu můžete na levém panelu vybrat **protokoly zřizování** a monitorovat průběh, který zobrazuje všechny akce prováděné službou zřizování ve vaší aplikaci. Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](check-status-user-account-provisioning.md).

> [!NOTE]
> Počáteční cyklus trvá déle než pozdější synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná.

## <a name="publish-your-application-to-the-aad-application-gallery"></a>Publikování aplikace v galerii aplikací AAD

Pokud vytváříte aplikaci, kterou bude používat víc než jeden tenant, můžete je zpřístupnit v galerii aplikací Azure AD. Díky tomu budou moci organizace snadno vyhledat aplikaci a nakonfigurovat zřizování. Publikování aplikace v galerii Azure AD a zpřístupnění pro ostatní je snadné. Podívejte se na tento [postup.](../develop/v2-howto-app-gallery-listing.md) Microsoft bude s vámi spolupracovat na integraci vaší aplikace do naší galerie, testování koncového bodu a [dokumentaci](../saas-apps/tutorial-list.md) k registraci pro zákazníky, kteří budou používat.

### <a name="gallery-onboarding-checklist"></a>Kontrolní seznam pro registraci galerie
Pomocí kontrolního seznamu můžete rychle připojit vaši aplikaci a zákazníci mají hladké prostředí pro nasazení. Informace budou shromažďovány od vás při připojování k galerii. 
> [!div class="checklist"]
> * Podpora koncového bodu uživatelů a skupin [SCIM 2,0](#understand-the-aad-scim-implementation) (je vyžadována pouze jedna, ale doporučuje se obě)
> * Podpora aspoň 25 požadavků za sekundu na tenanta, aby se zajistilo zřízení a zrušení zřízení uživatelů a skupin bez prodlení (povinné)
> * Navázání technologických kontaktů a vedení a podpory pro zákazníky po registraci galerie (povinné)
> * 3 přihlašovací údaje pro vaši aplikaci bez vypršení platnosti (povinné)
> * Podpora udělení autorizačního kódu OAuth nebo dlouhého nedlouhodobého tokenu, jak je popsáno níže (povinné)
> * Zřízení technického a podpůrného kontaktního bodu pro podporu zákazníků po registraci galerie (povinné)
> * [Podpora zjišťování schématu (povinné)](https://tools.ietf.org/html/rfc7643#section-6)
> * Podpora aktualizace více členství ve skupině s jednou OPRAVou
> * Veřejně zdokumentujte svůj koncový bod SCIM.

### <a name="authorization-to-provisioning-connectors-in-the-application-gallery"></a>Autorizace při zřizování konektorů v galerii aplikací
Specifikace SCIM nedefinuje schéma specifické pro SCIM pro ověřování a autorizaci a spoléhá na použití stávajících oborových standardů.

|Metoda autorizace|Výhody|Nevýhody|Podpora|
|--|--|--|--|
|Uživatelské jméno a heslo (nedoporučuje se ani nepodporuje služba Azure AD)|Snadná implementace|Nezabezpečené – [vaše PA $ $Word nezáleží](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Podporuje se pro aplikace v galerii případ od případu. Nepodporuje se pro aplikace mimo galerii.|
|Dlouhý nosný token|Dlouhodobé tokeny nevyžadují, aby uživatel byl přítomen. Správci se můžou při nastavování zřizování snadno použít.|Dlouhotrvající tokeny může být obtížné sdílet se správcem bez použití nezabezpečených metod, jako je e-mailová adresa. |Podporováno pro galerie a aplikace mimo galerii. |
|Udělení autorizačního kódu OAuth|Přístupové tokeny jsou mnohem kratší než hesla a mají mechanismus automatizovaného obnovení, který nemá dlouhodobé tokeny pro nosiče.  Skutečný uživatel musí být přítomen při počáteční autorizaci a přidává úroveň zodpovědnosti. |Vyžaduje, aby byl uživatel přítomen. Pokud uživatel odejde z organizace, token je neplatný a autorizaci bude nutné dokončit znovu.|Podporováno pro aplikace v galerii, ale ne aplikace bez galerie. Pro účely krátkodobého testování ale můžete v uživatelském rozhraní zadat přístupový token jako tajný token. Podpora pro udělení kódu OAuth v jiné než galerii je v našich nevyřízených položkách, kromě podpory pro konfigurovatelné adresy URL ověření a tokenu v aplikaci Galerie.|
|Udělení přihlašovacích údajů klienta OAuth|Přístupové tokeny jsou mnohem kratší než hesla a mají mechanismus automatizovaného obnovení, který nemá dlouhodobé tokeny pro nosiče. Udělení autorizačního kódu i udělení přihlašovacích údajů klienta vytvoří stejný typ přístupového tokenu, takže přesun mezi těmito metodami je pro rozhraní API transparentní.  Zřizování se dá kompletně automatizovat a nové tokeny se můžou bez zásahu uživatele považovat za tichou. ||Nepodporuje se pro galerie a aplikace mimo galerii. Podpora je v našich nevyřízených položkách.|

> [!NOTE]
> V uživatelském rozhraní vlastní aplikace pro konfiguraci zřizování AAD se nedoporučuje ponechat pole token prázdné. Vygenerovaný token je primárně k dispozici pro účely testování.

### <a name="oauth-code-grant-flow"></a>Tok udělení kódu OAuth

Služba zřizování podporuje [udělení autorizačního kódu](https://tools.ietf.org/html/rfc6749#page-24) a po odeslání žádosti o publikování vaší aplikace v galerii bude náš tým spolupracovat na shromažďování těchto informací:

- **Autorizační adresa URL**, adresa URL klienta, který získá autorizaci od vlastníka prostředku prostřednictvím přesměrování uživatelského agenta. Uživatel se přesměruje na tuto adresu URL a autorizuje přístup. 

- **Adresa URL pro výměnu tokenu**, adresa URL klienta k výměně udělení autorizace přístupového tokenu, obvykle s ověřením klienta.

- **ID klienta**, autorizační server vydá registrovanému klientovi identifikátor klienta, což je jedinečný řetězec, který představuje registrační informace poskytované klientem.  Identifikátor klienta není tajný. je vystavena vlastníkovi prostředku a **nesmí** se používat samostatně pro ověřování klientů.  

- **Tajný kód klienta**, tajný klíč generovaný autorizačním serverem, který by měl mít jedinečnou hodnotu známou jenom pro autorizační Server. 

> [!NOTE]
> Adresa **URL pro autorizaci** a **adresu URL pro výměnu tokenů** se v tuto chvíli nedají konfigurovat na tenanta.

> [!NOTE]
> OAuth v1 není podporovaný kvůli expozici tajného klíče klienta. Je podporován protokol OAuth v2.  

Osvědčené postupy (Doporučené, ale nepovinné):
* Podporuje více adres URL pro přesměrování. Správci mohou nakonfigurovat zřizování z obou "portal.azure.com" i "aad.portal.azure.com". Podpora více adres URL pro přesměrování zajistí, že uživatelé budou moct autorizovat přístup z obou portálu.
* Podpora více tajných kódů pro snadné obnovení bez výpadků. 

#### <a name="how-to-setup-oauth-code-grant-flow"></a>Postup nastavení toku udělení kódu OAuth

1. Přihlaste se k Azure Portal, ve **firemních aplikacích**  >    >  **zřizování** aplikací a vyberte **autorizovat**.

   1. Azure Portal přesměruje uživatele na adresu URL autorizace (přihlašovací stránka pro aplikaci třetí strany).

   1. Správce poskytuje přihlašovací údaje k aplikaci třetí strany. 

   1. Aplikace třetí strany přesměruje uživatele zpět na Azure Portal a poskytuje kód pro udělení. 

   1. Služba zřizování Azure AD volá adresu URL tokenu a poskytuje kód pro udělení. Aplikace třetí strany reaguje na přístupový token, obnovovací token a datum vypršení platnosti.

1. Po zahájení cyklu zřizování služba zkontroluje, jestli je aktuální přístupový token platný, a v případě potřeby ho vymění pro nový token. Přístupový token je k dispozici v každém požadavku na aplikaci a platnost žádosti je kontrolována před každou žádostí.

> [!NOTE]
> I když není možné nastavovat OAuth na aplikacích mimo galerii, můžete ručně vygenerovat přístupový token z autorizačního serveru a zadat ho jako tajný token do aplikace mimo galerii. To vám umožní ověřit kompatibilitu serveru SCIM s klientem služby AAD SCIM před zprovozněním do Galerie aplikací, která podporuje udělení kódu OAuth.  

**Dlouhodobé tokeny Bearer OAuth:** Pokud vaše aplikace nepodporuje tok udělení autorizačního kódu OAuth, místo toho vygeneruje dlouhodobé tokeny Bearer OAuth, které správce může použít k nastavení integrace zřizování. Token by měl být trvalý nebo jinak bude úloha zřizování v [karanténě](application-provisioning-quarantine-status.md) , až vyprší platnost tokenu.

V případě dalších metod ověřování a autorizace dejte nám na [UserVoice](https://aka.ms/appprovisioningfeaturerequest)informace.

### <a name="gallery-go-to-market-launch-check-list"></a>Seznam kontrol spuštění v galerii pro uvedení na trh
Abychom vám pomohli při zvyšování povědomí a vyžádání naší společné integrace, doporučujeme aktualizovat svou stávající dokumentaci a rozšířit integraci do marketingových kanálů.  Níže je uveden seznam aktivit kontrolního seznamu, které doporučujeme dokončit pro podporu spuštění.

> [!div class="checklist"]
> * Ujistěte se, že týmy pro prodej a zákazníky podporují, jsou připravené a můžou mluvit s možnostmi integrace. Stručně vaše týmy poskytují časté otázky a zahrnují integraci do vašich prodejních materiálů. 
> * Vytvořte Blogový příspěvek nebo stiskněte vydanou verzi, která popisuje společnou integraci, výhody a postupy, jak začít. [Příklad: Imprivata and Azure Active Directory Press vydaná verze](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
> * Využijte své sociální média, jako je Twitter, Facebook nebo LinkedIn, a Propagujte integraci s vašimi zákazníky. Nezapomeňte přidat @AzureAD svůj příspěvek. [Příklad: Imprivata Twitter post](https://twitter.com/azuread/status/1123964502909779968)
> * Umožňuje vytvořit nebo aktualizovat marketingové stránky/Web (například stránku integrace, stránku pro partnery, ceny atd.), aby zahrnovaly dostupnost společné integrace. [Příklad: stránka integrace Pingboard](https://pingboard.com/org-chart-for), stránka [integrace Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Stránka s cenami Monday.com](https://monday.com/pricing/) 
> * Vytvořte článek centra pro nápovědu nebo technickou dokumentaci, jak mohou zákazníci začít. [Příklad: integrace zástupné + Microsoft Azure Active Directory.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
> * Upozorní zákazníky na novou integraci prostřednictvím zákaznické komunikace (měsíční bulletiny, e-mailové kampaně, poznámky k verzi produktu). 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vývoj ukázkového koncového bodu SCIM](use-scim-to-build-users-and-groups-endpoints.md) 
>  [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS](user-provisioning.md) 
>  [Přizpůsobení mapování atributů pro zřizování uživatelů](customize-application-attributes.md) 
>  [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md) 
>  [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md) 
>  [Oznámení](user-provisioning.md) 
>  zřizování účtů [Seznam kurzů, jak integrovat aplikace SaaS](../saas-apps/tutorial-list.md)

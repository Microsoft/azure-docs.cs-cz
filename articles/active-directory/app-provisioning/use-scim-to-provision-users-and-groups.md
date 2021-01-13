---
title: Kurz – vývoj koncového bodu SCIM pro zřizování uživatelů pro aplikace ze služby Azure AD
description: Systém pro správu identit mezi doménami (SCIM) standardizace Automatické zřizování uživatelů. V tomto kurzu se naučíte vyvíjet SCIM koncový bod, integrovat rozhraní SCIM API pomocí Azure Active Directory a začít automatizovat zřizování uživatelů a skupin do cloudových aplikací.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.custom: contperf-fy21q2
ms.openlocfilehash: 2cdd02ef6ff67f14dbf99e40a254f2c214e23afb
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165412"
---
# <a name="tutorial---build-a-scim-endpoint-and-configure-user-provisioning-with-azure-ad"></a>Kurz – sestavení koncového bodu SCIM a konfigurace zřizování uživatelů pomocí Azure AD

Jako vývojář aplikace můžete použít systém pro rozhraní API pro správu uživatelů mezi doménami (SCIM) a povolit tak Automatické zřizování uživatelů a skupin mezi vaší aplikací a službou Azure AD. Tento článek popisuje, jak vytvořit koncový bod SCIM a jak ho integrovat se službou zřizování Azure AD. Specifikace SCIM poskytuje společné uživatelské schéma pro zřizování. Při použití ve spojení s federačními standardy, jako je SAML nebo OpenID Connect, SCIM poskytuje správcům ucelené řešení založené na standardech pro správu přístupu.

SCIM je standardizovaná definice dvou koncových bodů: bod/Users a koncový bod/groups. Používá běžné operace REST k vytváření, aktualizaci a odstraňování objektů a předdefinované schéma pro běžné atributy, jako je název skupiny, uživatelské jméno, křestní jméno, příjmení a e-mail. Aplikace, které nabízejí SCIM 2,0 REST API můžou snížit nebo eliminovat přehlednost práce s rozhraním API pro správu uživatelů. Například kterýkoli kompatibilní klient SCIM ví, jak vytvořit příspěvek HTTP objektu JSON do koncového bodu/Users a vytvořit tak novou položku uživatele. Místo toho, aby pro stejné základní akce bylo nutné trochu odlišné rozhraní API, můžou aplikace, které odpovídají standardu SCIM, okamžitě využít výhod existujících klientů, nástrojů a kódu. 

![Zřizování z Azure AD do aplikace pomocí SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Standardní schéma uživatelského objektu a rozhraní REST API pro správu definovaná v SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) umožňují snazší integraci zprostředkovatelů identity a aplikací. Vývojáři aplikací, kteří vytvářejí koncový bod SCIM, mohou být integrováni s jakýmkoli klientem kompatibilním s SCIM, aniž by museli provádět vlastní práci.

Automatizace zřizování pro aplikaci vyžaduje sestavení a integraci koncového bodu SCIM s klientem Azure AD SCIM. Provedením následujících kroků spustíte zřizování uživatelů a skupin do vaší aplikace. 
    
  * **[Krok 1: návrh schématu uživatelů a skupin](#step-1-design-your-user-and-group-schema)** Identifikujte objekty a atributy, které vaše aplikace potřebuje, a určete, jak se mají mapovat na schéma uživatelů a skupin podporované implementací Azure AD SCIM.

  * **[Krok 2: pochopení implementace Azure AD SCIM.](#step-2-understand-the-azure-ad-scim-implementation)** Seznamte se s tím, jak je klient Azure AD SCIM implementovaný, a modelovat žádosti a odpovědi protokolů SCIM.

  * **[Krok 3: Vytvoření koncového bodu SCIM](#step-3-build-a-scim-endpoint)** Koncový bod musí být SCIM 2,0, který je kompatibilní pro integraci se službou zřizování Azure AD. Jako možnost můžete k sestavení koncového bodu použít knihovny a ukázky kódu Microsoft Common Language Infrastructure (CLI). Tyto ukázky jsou pouze pro referenci a testování; Doporučujeme, abyste před kódováním produkční aplikace mohli na nich pořizovat závislosti.

  * **[Krok 4: Integrujte svůj koncový bod SCIM s klientem Azure AD SCIM.](#step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client)** Pokud vaše organizace používá aplikaci třetí strany, která implementuje profil SCIM 2,0, který Azure AD podporuje, můžete hned začít automatizovat zřizování a rušení zřizování uživatelů a skupin.

  * **[Krok 5: publikování aplikace v galerii aplikací Azure AD.](#step-5-publish-your-application-to-the-azure-ad-application-gallery)** Usnadňují zákazníkům zjišťování vaší aplikace a snadné konfiguraci zřizování. 

![Postup integrace koncového bodu SCIM s Azure AD](media/use-scim-to-provision-users-and-groups/process.png)

## <a name="step-1-design-your-user-and-group-schema"></a>Krok 1: návrh schématu uživatelů a skupin

Každá aplikace vyžaduje pro vytvoření uživatele nebo skupiny jiné atributy. Zahajte integraci určením objektů (uživatelů, skupin) a atributů (název, manažer, pracovní zařazení atd.), které vaše aplikace vyžaduje. Standard SCIM definuje schéma pro správu uživatelů a skupin. Základní schéma uživatele vyžaduje jenom tři atributy: **ID** (definovaný identifikátor poskytovatele služby), **externalId** (definovaný identifikátor klienta) a **meta** (metadata jen pro čtení udržovaná poskytovatelem služeb). Všechny ostatní atributy jsou volitelné. Kromě základního schématu uživatele definuje standard SCIM i rozšíření podnikového uživatele a model pro rozšíření schématu uživatele tak, aby splňoval potřeby vaší aplikace. Pokud například vaše aplikace vyžaduje správce uživatele, můžete ke shromáždění e-mailu uživatele a ke shromáždění jeho základního schématu použít schéma uživatele organizace. Pro návrh schématu použijte následující postup:
  1. Seznam atributů, které vaše aplikace vyžaduje. To může být užitečné pro rozdělení vašich požadavků do atributů potřebných pro ověřování (například loginName a e-mail), atributů potřebných ke správě životního cyklu uživatele (např. stav/aktivní) a dalších atributů potřebných pro fungování konkrétní aplikace (např. správce, značka).
  2. Ověřte, zda tyto atributy jsou již definovány ve schématu základního uživatele nebo schématu uživatele organizace. Pokud jsou nějaké atributy, které potřebujete a nejsou zahrnuté v základních nebo podnikových schématech uživatelů, budete muset definovat rozšíření pro uživatelské schéma, které pokrývá atributy, které potřebujete. V následujícím příkladu jsme přidali rozšíření pro uživatele, aby bylo možné na uživateli zřídit značku. Nejlepší je začít jenom se základními a podnikovými schématy uživatelů a později je rozšířit na další vlastní schémata.  
  3. Namapujte atributy SCIM na atributy uživatele v Azure AD. Pokud některý z atributů, které jste definovali ve vašem koncovém bodu SCIM, nemá u schématu uživatele Azure AD jasný protějšek, je velmi pravděpodobné, že data nejsou uložená v objektu uživatele ve většině tenantů. Zvažte, zda tento atribut může být volitelný pro vytvoření uživatele. Pokud je atribut pro fungování vaší aplikace kritický, požádejte správce tenanta, aby rozšířil své schéma, nebo použijte atribut rozšíření, jak je znázorněno níže pro vlastnost Tags.

### <a name="table-1-outline-the-attributes-that-you-need"></a>Tabulka 1: Osnova potřebných atributů 
| Krok 1: určení atributů, které vaše aplikace vyžaduje| Krok 2: mapování požadavků aplikace na SCIM Standard| Krok 3: mapování atributů SCIM na atributy služby Azure AD|
|--|--|--|
|loginName|userName|userPrincipalName (Hlavní název uživatele)|
|firstName|name.givenName|givenName|
|lastName|Name. lastName|lastName|
|workMail|E-maily [typ EQ "Work"]. Value|Poštovní|
|manager|manager|manager|
|značka|urn: IETF: params: SCIM: schémata: rozšíření: 2.0: CustomExtension: tag|extensionAttribute1|
|status|active|isSoftDeleted (vypočtená hodnota neuložená na uživateli)|

Výše definované schéma by představovalo použití datové části JSON níže. Všimněte si, že kromě atributů vyžadovaných pro aplikaci, reprezentace JSON zahrnuje povinné `id` `externalId` atributy, a `meta` .

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
}   
```

### <a name="table-2-default-user-attribute-mapping"></a>Tabulka 2: mapování výchozích atributů uživatele
Pak můžete pomocí následující tabulky porozumět tomu, jak atributy, které vaše aplikace vyžaduje, se mapují na atribut v Azure AD a v SCIM RFC. Můžete [přizpůsobit](customize-application-attributes.md) způsob mapování atributů mezi Azure AD a koncovým bodem SCIM. Všimněte si, že nemusíte podporovat uživatele i skupiny ani všechny níže uvedené atributy. Jsou to odkazy na to, jak se atributy ve službě Azure AD často mapují na vlastnosti v protokolu SCIM. 

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


### <a name="table-3-default-group-attribute-mapping"></a>Tabulka 3: mapování atributů výchozí skupiny

| Skupina Azure Active Directory | urn: IETF: parametry: SCIM: schemas: Core: 2.0: Group |
| --- | --- |
| displayName |displayName |
| pošta |emails[type eq "work"].value |
| mailNickname |displayName |
| členy |členy |
| objectId |externalId |
| proxyAddresses |e-maily [Type EQ "other"]. Osa |

V dokumentu RFC SCIM je definováno několik koncových bodů. Můžete začít s koncovým bodem/User a pak od něj rozbalit. Koncový bod/schemas je užitečný při použití vlastních atributů nebo v případě, že se schéma často mění. Umožňuje klientovi, aby získal nejaktuálnější schéma automaticky. Koncový bod/Bulk je zvláště užitečný při podpoře skupin. Následující tabulka popisuje různé koncové body definované ve standardu SCIM.
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabulka 4: určení koncových bodů, které byste chtěli vyvíjet
|Služba|POPIS|
|--|--|
|/User|Provádění operací CRUD u objektu uživatele.|
|/Group|Proveďte operace CRUD u objektu skupiny.|
|/ServiceProviderConfig|Obsahuje podrobné informace o funkcích podporovaného standardu SCIM, například o podporovaných zdrojích a metodě ověřování.|
|/ResourceTypes|Určuje metadata o jednotlivých prostředcích.|
|/Schemas|Sada atributů podporovaná jednotlivými klienty a poskytovatelem služeb se může lišit. Jeden poskytovatel služeb může zahrnovat `name` , `title` a `emails` , i když jiný poskytovatel služeb používá `name` , `title` a `phoneNumbers` . Koncový bod schémat umožňuje zjišťování podporovaných atributů.|
|/Bulk|Hromadné operace umožňují provádět operace s velkou kolekcí objektů prostředků v rámci jedné operace (například členství v aktualizacích pro velkou skupinu).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Krok 2: pochopení implementace Azure AD SCIM
> [!IMPORTANT]
> Chování implementace Azure AD SCIM se naposledy aktualizovalo 18. prosince 2018. Informace o tom, co se změnilo, najdete v článku [dodržování předpisů protokolu SCIM 2,0 ve službě Azure AD pro zřizování uživatelů](application-provisioning-config-problem-scim-compatibility.md).

Pokud vytváříte aplikaci, která podporuje rozhraní API pro správu uživatelů SCIM 2,0, v této části najdete podrobné informace o implementaci klienta Azure AD SCIM. Také ukazuje, jak modelovat zpracování požadavků protokolu SCIM a jejich odpovědi. Po implementaci koncového bodu SCIM ho můžete otestovat pomocí postupu popsaného v předchozí části.

V rámci [specifikace protokolu SCIM 2,0](http://www.simplecloud.info/#Specification)musí vaše aplikace splňovat tyto požadavky:

* Podporuje vytváření uživatelů a volitelně také skupiny podle oddílu [3,3 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Podporuje úpravu uživatelů nebo skupin s požadavky na opravy podle [oddílu 3.5.2 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2). Podpora zajišťuje, že skupiny a uživatelé jsou zřízeny způsobem. 
* Podporuje načítání známého prostředku pro uživatele nebo skupinu vytvořené dříve, podle [oddílu 3.4.1 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Podporuje dotazování uživatelů nebo skupin podle části [3.4.2 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Ve výchozím nastavení jsou uživatelé načítáni pomocí `id` a dotazováni jejich `username` a a `externalId` skupiny jsou dotazovány nástrojem `displayName` .  
* Podporuje dotazování uživatele podle ID a podle manažera podle části 3.4.2 protokolu SCIM.  
* Podporuje dotazování skupin podle ID a členu podle části 3.4.2 protokolu SCIM.  
* Podporuje filtr [excludedAttributes = Members](#get-group) při dotazování na prostředek skupiny, podle části 3.4.2.5 protokolu SCIM.
* Přijme jeden nosný token pro ověřování a autorizaci služby Azure AD pro vaši aplikaci.
* Podporuje obnovitelné odstranění uživatele `active=false` a obnovení uživatele `active=true` (objekt uživatele by měl být vrácen v požadavku bez ohledu na to, zda je uživatel aktivní). Jediným okamžikem, kdy by uživatel neměl být vrácen, je v případě, že je pevným smazán z aplikace. 

Při implementaci SCIM koncového bodu, který zajistí kompatibilitu s Azure AD, postupujte podle těchto obecných pokynů:

* `id` je požadovaná vlastnost pro všechny prostředky. Každá odpověď, která vrací prostředek, by měla mít jistotu, že každý prostředek má tuto vlastnost, s výjimkou `ListResponse` členů s nulovými členy.
* Odpověď na požadavek na dotaz nebo filtr by měla být vždy typu `ListResponse` .
* Skupiny jsou volitelné, ale podporují se jenom v případě, že implementace SCIM podporuje žádosti o opravu.
* V reakci na opravu není nutné zahrnout celý prostředek.
* Microsoft Azure AD používá pouze následující operátory:  
    - `eq`
    - `and`
* Pro strukturální prvky v SCIM nemusíte rozlišovat velká a malá písmena, zejména `op` hodnoty operace opravy, jak je definováno v https://tools.ietf.org/html/rfc7644#section-3.5.2 . Azure AD vygeneruje hodnoty "op" jako `Add` , `Replace` a `Remove` .
* Microsoft Azure AD vytváří požadavky na načtení náhodného uživatele a skupiny, aby bylo zajištěno, že koncový bod a přihlašovací údaje jsou platné. Také se provádí jako součást toku **testu připojení** v [Azure Portal](https://portal.azure.com). 
* Atribut, na kterém mohou být prostředky odesílány, by měl být nastaven jako odpovídající atribut v aplikaci v [Azure Portal](https://portal.azure.com). Další informace najdete v tématu [Přizpůsobení mapování atributů zřizování uživatelů](customize-application-attributes.md) .
* Podpora HTTPS na koncovém bodu SCIM

### <a name="user-provisioning-and-deprovisioning"></a>Zřizování a rušení uživatelů

Následující ilustrace znázorňuje zprávy, které Azure Active Directory posílá službě SCIM ke správě životního cyklu uživatele v úložišti identit vaší aplikace.  

![Zobrazuje pořadí zřizování a rušení zřizování uživatelů.](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Pořadí zřizování a rušení zřizování uživatelů*

### <a name="group-provisioning-and-deprovisioning"></a>Zřizování skupin a jejich rušení

Zřizování skupin a zrušení zřízení jsou volitelné. Když se implementuje a povolí, na následujícím obrázku se zobrazí zprávy, které služba Azure AD pošle službě SCIM ke správě životního cyklu skupiny v úložišti identit vaší aplikace.  Tyto zprávy se liší od zpráv o uživatelích dvěma způsoby:

* Požadavky na načtení skupin určují, že atribut Members se má vyloučit z libovolného prostředku poskytnutého v reakci na požadavek.  
* Žádosti o zjištění, zda má atribut reference určitou hodnotu, jsou požadavky na atribut members.  

![Zobrazuje pořadí zřizování a rušení zřizování skupin.](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Pořadí zřizování a rušení zřizování skupin*

### <a name="scim-protocol-requests-and-responses"></a>Požadavky a odpovědi protokolu SCIM
V této části najdete příklady požadavků SCIM vygenerovaných klientem Azure AD SCIM a příklady očekávaných odpovědí. Pro dosažení nejlepších výsledků byste měli kód aplikace, aby zpracovávala tyto požadavky v tomto formátu, a generovat očekávané odpovědi.

> [!IMPORTANT]
> Pokud chcete zjistit, jak a kdy služba zřizování uživatelů Azure AD vysílá níže popsané operace, přečtěte si část [zřizovací cykly: počáteční a přírůstkové](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) , [jak zřizování funguje](how-provisioning-works.md).

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

### <a name="user-operations"></a>Uživatelské operace

* Na uživatele lze zadat dotaz pomocí `userName` `email[type eq "work"]` atributů nebo.  

#### <a name="create-user"></a>Vytvořit uživatele

###### <a name="request"></a>Žádost

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

## <a name="step-3-build-a-scim-endpoint"></a>Krok 3: Vytvoření koncového bodu SCIM

Teď, když jste navrhli schéma a rozumíte implementaci Azure AD SCIM, můžete začít s vývojem koncového bodu SCIM. Místo začátku od nuly a sestavení implementace zcela na vlastní, můžete spoléhat na řadu open source knihoven SCIM, které publikovala komunita SCIM.

Open source [referenční kód](https://aka.ms/SCIMReferenceCode) .NET Core publikovaný týmem zřizování Azure AD je jeden takový prostředek, který může přejít k zahájení vývoje. Po vytvoření SCIM koncového bodu ho budete chtít otestovat. Můžete použít kolekci předávacích [testů](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) , které jsou součástí referenčního kódu, nebo spustit prostřednictvím vzorových požadavků nebo odpovědí uvedených [výše](#user-operations).  

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

Další informace o více prostředích v ASP.NET Core použijte následující odkaz: [použití více prostředí v ASP.NET Core](
https://docs.microsoft.com/aspnet/core/fundamentals/environments)

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

***Příklad 1. Dotazování služby pro odpovídajícího uživatele** _

Azure Active Directory dotazuje službu pro uživatele s `externalId` hodnotou atributu, která odpovídá hodnotě atributu mailNickname uživatele v Azure AD. Dotaz se vyjadřuje jako požadavek HTTP (Hypertext Transfer Protocol), jako je například tento příklad, kde jyoung je ukázka mailNickname uživatele v Azure Active Directory.

>[!NOTE]
> Toto je pouze příklad. Ne všichni uživatelé budou mít atribut mailNickname a hodnota, kterou uživatel nemusí být v adresáři jedinečná. Atribut použitý pro porovnání (který v tomto případě je v tomto případě `externalId` ) se taky dá nakonfigurovat v [mapováních atributů Azure AD](customize-application-attributes.md).

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

ukazatelů. AlternateFilters. Count: 1
* ukazatelů. AlternateFilters. ElementAt (0). AttributePath: "externalId"
* ukazatelů. AlternateFilters. ElementAt (0). ComparisonOperator: ComparisonOperator. Equals
* ukazatelů. AlternateFilter. ElementAt (0). ComparisonValue: "jyoung"

***Příklad 2. Zřídit uživatele**

Pokud odpověď na dotaz na webovou službu pro uživatele s `externalId` hodnotou atributu mailNickname uživatele nevrátí žádné uživatele, Azure Active Directory požadavky, které zajišťují, že služba zřídí uživatele, který odpovídá vašemu Azure Active Directory.  Tady je příklad takového požadavku: 

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

V žádosti o zřízení uživatele je hodnota argumentu prostředku instancí třídy Microsoft. SCIM. Core2EnterpriseUser definované v knihovně Microsoft. SCIM. schemas.  Pokud požadavek na zřízení uživatele uspěje, pak implementace metody očekává, že se vrátí instance třídy Microsoft. SCIM. Core2EnterpriseUser s hodnotou vlastnosti identifikátor nastavenou na jedinečný identifikátor nově zřízeného uživatele.  

_*_Příklad 3. Dotaz na aktuální stav uživatele_*_ 

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
  
_ Identifikátor: "54D382A4-2050-4C03-94D1-E769F1D15682"
* SchemaIdentifier: "urn: IETF: param: SCIM: schemas: Extension: Enterprise: 2.0: User"

***Příklad 4. Dotaz na hodnotu referenčního atributu, který** se má aktualizovat _ 

Pokud má být aktualizován atribut reference, pak Azure Active Directory dotazování služby, aby zjistila, zda aktuální hodnota atributu reference v úložišti identit, na kterou služba zachází, se již shoduje s hodnotou tohoto atributu v Azure Active Directory. Pro uživatele je jediným atributem, pro který je aktuální hodnota dotazována tímto způsobem atribut správce. Tady je příklad žádosti o zjištění, zda má atribut Manager objektu User Object aktuálně určitou hodnotu: v ukázkovém kódu je požadavek přeložen do volání metody QueryAsync poskytovatele služby. Hodnota vlastností objektu poskytnutého jako hodnota argumentu parameters je následující: 
  
ukazatelů. AlternateFilters. Count: 2
* ukazatelů. AlternateFilters. ElementAt (x). AttributePath: "ID"
* ukazatelů. AlternateFilters. ElementAt (x). ComparisonOperator: ComparisonOperator. Equals
* ukazatelů. AlternateFilter. ElementAt (x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ukazatelů. AlternateFilters. ElementAt (y). AttributePath: "správce"
* ukazatelů. AlternateFilters. ElementAt (y). ComparisonOperator: ComparisonOperator. Equals
* ukazatelů. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453A-919d-413861904646"
* ukazatelů. RequestedAttributePaths. ElementAt (0): "ID"
* ukazatelů. SchemaIdentifier: "urn: IETF: param: SCIM: schemas: Extension: Enterprise: 2.0: User"

V tomto případě může být hodnota indexu x rovna 0 a hodnota indexu y může být 1 nebo hodnota x může být 1 a hodnota y může být 0 v závislosti na pořadí výrazů parametru dotazu filtru.   

***Příklad 5. Požadavek od Azure AD do služby SCIM k aktualizaci uživatele** _ 

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
  
_ ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier. SchemaIdentifier: "urn: IETF: Paras: SCIM: schemas: Enterprise: 2.0: User"
* (PatchRequest as PatchRequest2). Operace. Count: 1
* (PatchRequest as PatchRequest2). Operations. ElementAt (0). OperationName: OperationName. Add
* (PatchRequest as PatchRequest2). Operations. ElementAt (0). Cesta. AttributePath: "správce"
* (PatchRequest as PatchRequest2). Operations. ElementAt (0). Hodnota. Count: 1
* (PatchRequest as PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Referenční informace: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
* (PatchRequest as PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Hodnota: 2819c223-7f76-453A-919d-413861904646

***Příklad 6. Zrušení zřízení uživatele** _

Pokud chcete zrušit zřízení uživatele z úložiště identit, které přednáší služba SCIM, Azure AD pošle žádost, například:

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

_ ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
* ResourceIdentifier. SchemaIdentifier: "urn: IETF: Paras: SCIM: schemas: Enterprise: 2.0: User"

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Krok 4: integrace koncového bodu SCIM s klientem Azure AD SCIM

Služba Azure AD se dá nakonfigurovat tak, aby automaticky zřídila přiřazené uživatele a skupiny aplikacím, které implementují konkrétní profil [protokolu SCIM 2,0](https://tools.ietf.org/html/rfc7644). Konkrétní informace o profilu jsou zdokumentovány v [kroku 2: pochopení implementace Azure AD SCIM](#step-2-understand-the-azure-ad-scim-implementation).

Obraťte se na poskytovatele vaší aplikace nebo na dokumentaci poskytovatele vaší aplikace, kde najdete prohlášení o kompatibilitě s těmito požadavky.

> [!IMPORTANT]
> Implementace Azure AD SCIM je postavená na službě Azure AD Provisioning Service, která je navržená tak, aby neustále udržovala synchronizaci mezi službou Azure AD a cílovou aplikací a implementovala velmi specifickou sadu standardních operací. Je důležité porozumět tomuto chování, abyste pochopili chování klienta Azure AD SCIM. Další informace najdete v části [cykly zřizování: počáteční a přírůstkové](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) , [jak zřizování funguje](how-provisioning-works.md).

### <a name="getting-started"></a>Začínáme

Aplikace, které podporují profil SCIM popsané v tomto článku, se dají připojit k Azure Active Directory pomocí funkce aplikace mimo galerii v galerii aplikací Azure AD. Po připojení Azure AD spustí proces synchronizace každých 40 minut, kde se dotazuje koncovému bodu aplikace SCIM pro přiřazené uživatele a skupiny a vytvoří je nebo upraví podle údajů o přiřazení.

**Připojení aplikace, která podporuje SCIM:**

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com). Všimněte si, že můžete získat přístup k bezplatné zkušební verzi pro Azure Active Directory s licencemi P2, a to tak, že si zaregistrujete [program pro vývojáře](https://developer.microsoft.com/office/dev-program) .
2. V levém podokně vyberte **podnikové aplikace** . Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací přidaných z galerie.
3. Vyberte **+ Nová aplikace**  >  **všechny**  >  **aplikace mimo galerii**.
4. Zadejte název vaší aplikace a vyberte **Přidat** a vytvořte objekt aplikace. Nová aplikace se přidá do seznamu podnikových aplikací a otevře se na obrazovce správy aplikací.

   ![Snímek obrazovky se zobrazením Galerie aplikací Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Galerie aplikací Azure AD*

5. Na obrazovce Správa aplikací vyberte na levém panelu možnost **zřizování** .
6. V nabídce **režim zřizování** vyberte **automaticky**.

   ![Příklad: stránka zřizování aplikace v Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurace zřizování v Azure Portal*

7. Do pole **Adresa URL tenanta** zadejte adresu URL koncového bodu SCIM aplikace. Příklad: `https://api.contoso.com/scim/`
8. Pokud koncový bod SCIM vyžaduje token nosiče OAuth od jiného vydavatele než Azure AD, Zkopírujte požadovaný token Bearer OAuth do pole volitelného **tajného tokenu** . Pokud toto pole zůstane prázdné, Azure AD zahrnuje token nosiče OAuth vydaný z Azure AD s každým požadavkem. Aplikace, které používají Azure AD jako zprostředkovatel identity, můžou ověřit tento token vydaný službou Azure AD. 
   > [!NOTE]
   > *_* Nedoporučujeme_* toto pole nechat prázdné a spoléhat se na token generovaný službou Azure AD. Tato možnost je primárně k dispozici pro účely testování.
9. Vyberte _ *test Connection**, aby se Azure Active Directory pokus o připojení ke koncovému bodu SCIM. Pokud se pokus nezdaří, zobrazí se informace o chybě.  

    > [!NOTE]
    > **Test Connection** se dotazuje na koncový bod SCIM pro uživatele, který neexistuje, pomocí NÁHODNÉho identifikátoru GUID jako odpovídající vlastnosti vybrané v konfiguraci služby Azure AD. Očekávaná správná odpověď je HTTP 200 OK s prázdnou SCIM zprávou ListResponse.

10. Pokud se pokusí připojit k aplikaci úspěšně, vyberte **Uložit** a uložte přihlašovací údaje správce.
11. V oddílu **mapování** existují dvě sady [mapování atributů](customize-application-attributes.md), které lze vybrat: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každou z nich a zkontrolujte atributy, které jsou synchronizované z Azure Active Directory do vaší aplikace. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování s uživateli a skupinami v aplikaci pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    > [!NOTE]
    > Volitelně můžete zakázat synchronizaci objektů skupiny zakázáním mapování "skupiny".

12. V části **Nastavení** pole **obor** určuje, kteří uživatelé a skupiny budou synchronizováni. Vyberte možnost **synchronizovat pouze přiřazené uživatele a skupiny** (doporučeno), chcete-li synchronizovat pouze uživatele a skupiny přiřazené na kartě **Uživatelé a skupiny** .
13. Po dokončení konfigurace nastavte **stav zřizování** **na zapnuto**.
14. Výběrem **Uložit** spusťte službu Azure AD Provisioning.
15. Pokud synchronizujete pouze přiřazené uživatele a skupiny (doporučeno), nezapomeňte vybrat kartu **Uživatelé a skupiny** a přiřadit uživatele nebo skupiny, které chcete synchronizovat.

Po zahájení počátečního cyklu můžete na levém panelu vybrat **protokoly zřizování** a monitorovat průběh, který zobrazuje všechny akce prováděné službou zřizování ve vaší aplikaci. Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](check-status-user-account-provisioning.md).

> [!NOTE]
> Počáteční cyklus trvá déle než pozdější synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Krok 5: publikování aplikace v galerii aplikací Azure AD

Pokud vytváříte aplikaci, kterou bude používat víc než jeden tenant, můžete je zpřístupnit v galerii aplikací Azure AD. Díky tomu budou moci organizace snadno vyhledat aplikaci a nakonfigurovat zřizování. Publikování aplikace v galerii Azure AD a zpřístupnění pro ostatní je snadné. Podívejte se na tento [postup.](../develop/v2-howto-app-gallery-listing.md) Microsoft bude s vámi spolupracovat na integraci vaší aplikace do naší galerie, testování koncového bodu a [dokumentaci](../saas-apps/tutorial-list.md) k registraci pro zákazníky, kteří budou používat.

### <a name="gallery-onboarding-checklist"></a>Kontrolní seznam pro registraci galerie
Postupujte podle níže uvedeného kontrolního seznamu, abyste měli jistotu, že je vaše aplikace rychle připojená a že zákazníci mají hladké prostředí pro nasazení. Informace budou shromažďovány od vás při připojování k galerii. 
> [!div class="checklist"]
> * Podpora koncového bodu uživatelů a skupin [SCIM 2,0](#step-2-understand-the-azure-ad-scim-implementation) (je vyžadována pouze jedna, ale doporučuje se obě)
> * Podpora aspoň 25 požadavků za sekundu na tenanta, aby se zajistilo zřízení a zrušení zřízení uživatelů a skupin bez prodlení (povinné)
> * Navázání technologických kontaktů a vedení a podpory pro zákazníky po registraci galerie (povinné)
> * 3 přihlašovací údaje pro vaši aplikaci bez vypršení platnosti (povinné)
> * Podpora udělení autorizačního kódu OAuth nebo dlouhého nedlouhodobého tokenu, jak je popsáno níže (povinné)
> * Zřízení technického a podpůrného kontaktního bodu pro podporu zákazníků po registraci galerie (povinné)
> * Podpora aktualizace více členství ve skupině s jednou OPRAVou (doporučeno) 
> * Veřejně zdokumentujte svůj koncový bod SCIM (doporučeno) 
> * [Podpora zjišťování schématu](https://tools.ietf.org/html/rfc7643#section-6) (doporučeno)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorizace pro zřizovací konektory v galerii aplikací
Specifikace SCIM nedefinuje schéma specifické pro SCIM pro ověřování a autorizaci. Spoléhá se na použití stávajících oborových standardů. Klient zřizování Azure AD podporuje dvě autorizační metody pro aplikace v galerii. 

|Metoda autorizace|Výhody|Nevýhody|Podpora|
|--|--|--|--|
|Uživatelské jméno a heslo (nedoporučuje se ani nepodporuje služba Azure AD)|Snadná implementace|Nezabezpečené – [vaše PA $ $Word nezáleží](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Podporuje se pro aplikace v galerii případ od případu. Nepodporuje se pro aplikace mimo galerii.|
|Dlouhý nosný token|Dlouhodobé tokeny nevyžadují, aby uživatel byl přítomen. Správci se můžou při nastavování zřizování snadno použít.|Dlouhotrvající tokeny může být obtížné sdílet se správcem bez použití nezabezpečených metod, jako je e-mailová adresa. |Podporováno pro galerie a aplikace mimo galerii. |
|Udělení autorizačního kódu OAuth|Přístupové tokeny jsou mnohem kratší než hesla a mají mechanismus automatizovaného obnovení, který nemá dlouhodobé tokeny pro nosiče.  Skutečný uživatel musí být přítomen při počáteční autorizaci a přidává úroveň zodpovědnosti. |Vyžaduje, aby byl uživatel přítomen. Pokud uživatel odejde z organizace, token je neplatný a autorizaci bude nutné dokončit znovu.|Podporováno pro aplikace v galerii, ale ne aplikace bez galerie. Pro účely krátkodobého testování ale můžete v uživatelském rozhraní zadat přístupový token jako tajný token. Podpora pro udělení kódu OAuth v jiné než galerii je v našich nevyřízených položkách.|
|Udělení přihlašovacích údajů klienta OAuth|Přístupové tokeny jsou mnohem kratší než hesla a mají mechanismus automatizovaného obnovení, který nemá dlouhodobé tokeny pro nosiče. Udělení autorizačního kódu i udělení přihlašovacích údajů klienta vytvoří stejný typ přístupového tokenu, takže přesun mezi těmito metodami je pro rozhraní API transparentní.  Zřizování se dá kompletně automatizovat a nové tokeny se můžou bez zásahu uživatele považovat za tichou. ||Nepodporuje se pro galerie a aplikace mimo galerii. Podpora je v našich nevyřízených položkách.|

> [!NOTE]
> V uživatelském rozhraní vlastní aplikace pro konfiguraci zřizování Azure AD se nedoporučuje ponechat pole token prázdné. Vygenerovaný token je primárně k dispozici pro účely testování.

**Tok udělení autorizačního kódu OAuth:** Služba zřizování podporuje [udělení autorizačního kódu](https://tools.ietf.org/html/rfc6749#page-24). Po odeslání žádosti o publikování vaší aplikace v galerii bude náš tým spolupracovat s vámi a shromažďovat následující informace:
*  Autorizační adresa URL: adresa URL klienta, který získá autorizaci od vlastníka prostředku prostřednictvím přesměrování uživatelského agenta. Uživatel se přesměruje na tuto adresu URL a autorizuje přístup. Všimněte si, že tato adresa URL není v současné době konfigurovatelná pro každého tenanta.
*  Adresa URL pro výměnu tokenu: adresa URL, kterou klient vyměňuje udělení autorizace přístupového tokenu, obvykle s ověřením klienta. Všimněte si, že tato adresa URL není v současné době konfigurovatelná pro každého tenanta.
*  ID klienta: autorizační server vydá registrovanému klientovi identifikátor klienta, což je jedinečný řetězec, který představuje registrační informace poskytované klientem.  Identifikátor klienta není tajný. je vystavena vlastníkovi prostředku a **nesmí** se používat samostatně pro ověřování klientů.  
*  Tajný kód klienta: tajný klíč klienta je tajný kód vygenerovaný autorizačním serverem. Mělo by se jednat o jedinečnou hodnotu známou jenom pro autorizační Server. 

Upozorňujeme, že OAuth v1 není podporován kvůli expozici tajného klíče klienta. Je podporován protokol OAuth v2.  

Osvědčené postupy (doporučeno, ale není nutné):
* Podporuje více adres URL pro přesměrování. Správci mohou nakonfigurovat zřizování z obou "portal.azure.com" i "aad.portal.azure.com". Podpora více adres URL pro přesměrování zajistí, že uživatelé budou moct autorizovat přístup z obou portálu.
* Podpora více tajných kódů, aby bylo zajištěno hladké obnovení tajných kódů bez výpadků. 

Postup v toku udělení kódu OAuth:
1. Uživatel se přihlásí do Azure Portal > podnikové aplikace > vyberte > zřizování aplikace > klikněte na autorizovat.
2. Azure Portal přesměruje uživatele na adresu URL autorizace (přihlašovací stránka pro aplikaci třetí strany).
3. Správce poskytuje přihlašovací údaje k aplikaci třetí strany. 
4. Aplikace třetí strany přesměruje uživatele zpět na Azure Portal a poskytuje kód pro udělení. 
5. Služba zřizování Azure AD volá adresu URL tokenu a poskytuje kód pro udělení. Aplikace třetí strany reaguje na přístupový token, obnovovací token a datum vypršení platnosti.
6. Po zahájení cyklu zřizování služba zkontroluje, jestli je aktuální přístupový token platný, a v případě potřeby ho vymění pro nový token. Přístupový token je k dispozici v každém požadavku na aplikaci a platnost žádosti je kontrolována před každou žádostí.

> [!NOTE]
> I když v současné době není možné nastavovat OAuth na aplikaci mimo galerii, můžete ručně vygenerovat přístupový token z autorizačního serveru a zadat ho do pole tajný token aplikace mimo galerii. To vám umožní ověřit kompatibilitu serveru SCIM s klientem Azure AD SCIM před zprovozněním do Galerie aplikací, která podporuje udělení kódu OAuth.  

**Dlouhodobé tokeny Bearer OAuth:** Pokud vaše aplikace nepodporuje tok udělení autorizačního kódu OAuth, můžete také vygenerovat dlouhodobé tokeny Bearer OAuth, než může správce použít k nastavení integrace zřizování. Token by měl být trvalý nebo jinak bude úloha zřizování v [karanténě](application-provisioning-quarantine-status.md) , až vyprší platnost tokenu.

V případě dalších metod ověřování a autorizace dejte nám na [UserVoice](https://aka.ms/appprovisioningfeaturerequest)informace.

### <a name="gallery-go-to-market-launch-check-list"></a>Seznam kontrol spuštění v galerii pro uvedení na trh
Abychom vám pomohli při zvyšování povědomí a vyžádání naší společné integrace, doporučujeme aktualizovat svou stávající dokumentaci a rozšířit integraci do marketingových kanálů.  Níže je uveden seznam aktivit kontrolního seznamu, které doporučujeme dokončit pro podporu spuštění.

* **Připravenost na prodej a zákaznickou podporu.** Zajistěte, aby si týmy pro prodej a podporu byly vědomy a mohli mluvit s možnostmi integrace. Stručně váš tým pro prodej a podporu jim poskytne Nejčastější dotazy a integruje integraci do vašich prodejních materiálů. 
* **Příspěvek na blogu nebo tiskovou verzi.** Vytvořte Blogový příspěvek nebo stiskněte vydanou verzi, která popisuje společnou integraci, výhody a postupy, jak začít. [Příklad: Imprivata and Azure Active Directory Press vydaná verze](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Sociální média.** Využijte své sociální média, jako je Twitter, Facebook nebo LinkedIn, a Propagujte integraci s vašimi zákazníky. Nezapomeňte přidat @AzureAD svůj příspěvek. [Příklad: Imprivata Twitter post](https://twitter.com/azuread/status/1123964502909779968)
* **Marketingový Web.** Umožňuje vytvořit nebo aktualizovat marketingové stránky (například stránku integrace, stránku pro partnery, ceny atd.), aby zahrnovaly dostupnost společné integrace. [Příklad: stránka integrace Pingboard](https://pingboard.com/org-chart-for), stránka [integrace Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Stránka s cenami Monday.com](https://monday.com/pricing/) 
* **Technickou dokumentaci.** Vytvořte článek centra pro nápovědu nebo technickou dokumentaci, jak mohou zákazníci začít. [Příklad: integrace zástupné + Microsoft Azure Active Directory.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Komunikace se zákazníky.** Upozorní zákazníky na novou integraci prostřednictvím zákaznické komunikace (měsíční bulletiny, e-mailové kampaně, poznámky k verzi produktu). 

## <a name="related-articles"></a>Související články

* [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS](user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](customize-application-attributes.md)
* [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Oznámení zřizování účtů](user-provisioning.md)
* [Seznam kurzů, jak integrovat aplikace SaaS](../saas-apps/tutorial-list.md)


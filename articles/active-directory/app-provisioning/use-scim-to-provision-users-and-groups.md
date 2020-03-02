---
title: Vývoj koncového bodu SCIM pro zřizování uživatelů pro aplikace ze služby Azure AD
description: Systém pro správu identit mezi doménami (SCIM) standardizace Automatické zřizování uživatelů. Naučte se vyvíjet SCIM koncový bod, Integrujte své rozhraní SCIM API pomocí Azure Active Directory a začněte automatizovat zřizování uživatelů a skupin do svých cloudových aplikací.
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
ms.date: 02/18/2020
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11e4768c5cf6df784c8f32aff2f884adfa6b68ab
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/01/2020
ms.locfileid: "78204850"
---
# <a name="build-a-scim-endpoint-and-configure-user-provisioning-with-azure-active-directory-azure-ad"></a>Vytvoření koncového bodu SCIM a konfigurace zřizování uživatelů pomocí Azure Active Directory (Azure AD)

Jako vývojář aplikace můžete použít systém pro rozhraní API pro správu uživatelů mezi doménami (SCIM) a povolit tak Automatické zřizování uživatelů a skupin mezi vaší aplikací a službou Azure AD. Tento článek popisuje, jak vytvořit koncový bod SCIM a jak ho integrovat se službou zřizování Azure AD. Specifikace SCIM poskytuje společné uživatelské schéma pro zřizování. Při použití ve spojení s federačními standardy, jako je SAML nebo OpenID Connect, SCIM poskytuje správcům ucelené řešení založené na standardech pro správu přístupu.

SCIM je standardizovaná definice dvou koncových bodů: bod/Users a koncový bod/groups. Používá běžné operace REST k vytváření, aktualizaci a odstraňování objektů a předdefinované schéma pro běžné atributy, jako je název skupiny, uživatelské jméno, křestní jméno, příjmení a e-mail. Aplikace, které nabízejí SCIM 2,0 REST API můžou snížit nebo eliminovat přehlednost práce s rozhraním API pro správu uživatelů. Například kterýkoli kompatibilní klient SCIM ví, jak vytvořit příspěvek HTTP objektu JSON do koncového bodu/Users a vytvořit tak novou položku uživatele. Místo toho, aby pro stejné základní akce bylo nutné trochu odlišné rozhraní API, můžou aplikace, které odpovídají standardu SCIM, okamžitě využít výhod existujících klientů, nástrojů a kódu. 

![Zřizování z Azure AD do aplikace pomocí SCIM](media/use-scim-to-provision-users-and-groups/scim-provisioning-overview.png)

Standardní schéma uživatelského objektu a rozhraní REST API pro správu definovaná v SCIM 2,0 (RFC [7642](https://tools.ietf.org/html/rfc7642), [7643](https://tools.ietf.org/html/rfc7643), [7644](https://tools.ietf.org/html/rfc7644)) umožňují snazší integraci zprostředkovatelů identity a aplikací. Vývojáři aplikací, kteří vytvářejí koncový bod SCIM, mohou být integrováni s jakýmkoli klientem kompatibilním s SCIM, aniž by museli provádět vlastní práci.

Automatizace zřizování pro aplikaci vyžaduje sestavení a integraci koncového bodu SCIM s odpovídajícím Azure AD SCIM. Provedením následujících kroků spustíte zřizování uživatelů a skupin do vaší aplikace. 
    
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
|workMail|E-maily [typ EQ "Work"]. Value|Pošta|
|Správce|Správce|Správce|
|tag|urn: IETF: params: SCIM: schémata: rozšíření: 2.0: CustomExtension: tag|extensionAttribute1|
|status|aktivní|isSoftDeleted (vypočtená hodnota neuložená na uživateli)|

Výše definované schéma by představovalo použití datové části JSON níže. Všimněte si, že kromě atributů vyžadovaných pro aplikaci obsahuje reprezentace JSON požadované atributy ID, externalId a meta.

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

### <a name="table-2-default-user-attribute-mapping"></a>Tabulka 2: mapování výchozích atributů uživatele
Pak můžete pomocí následující tabulky porozumět tomu, jak atributy, které vaše aplikace vyžaduje, se mapují na atribut v Azure AD a v SCIM RFC. Můžete [přizpůsobit](customize-application-attributes.md) způsob mapování atributů mezi Azure AD a koncovým bodem SCIM. Všimněte si, že nemusíte podporovat uživatele i skupiny ani všechny níže uvedené atributy. Jsou to odkazy na to, jak se atributy ve službě Azure AD často mapují na vlastnosti v protokolu SCIM. 

| Uživatele Azure Active Directory | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktivní |
|Oddělení|urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: oddělení|
| displayName |displayName |
|employeeId|urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: uživatel: employeeNumber|
| Ve TelephoneNumber |phoneNumbers [typ eq "fax"] .value |
| givenName |name.givenName |
| pracovní funkce |Název |
| e-mailu |e-mailů [typ eq "pracovní"] .value |
| mailNickname |externalId |
| Správce |urn: IETF: parametry: SCIM: schémata: rozšíření: Enterprise: 2.0: User: Manager |
| Mobilní zařízení |phoneNumbers [eq typ "mobilní"] .value |
| PSČ |.postalCode adresy [typ eq "pracovní"] |
| Adresy proxy serveru |e-mailů [Zadejte eq "other"]. Hodnota |
| fyzický Delivery-OfficeName |adresy [Zadejte eq "other"]. Ve formátu |
| streetAddress |.streetAddress adresy [typ eq "pracovní"] |
| Příjmení |name.familyName |
| telefonní číslo |phoneNumbers [typ eq "pracovní"] .value |
| user-PrincipalName |userName |


### <a name="table-3-default-group-attribute-mapping"></a>Tabulka 3: mapování atributů výchozí skupiny

| Skupina Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |displayName |
| e-mailu |e-mailů [typ eq "pracovní"] .value |
| mailNickname |displayName |
| členové |členové |
| ID objektu |externalId |
| proxyAddresses |e-mailů [Zadejte eq "other"]. Hodnota |

V dokumentu RFC SCIM je definováno několik koncových bodů. Můžete začít s koncovým bodem/User a pak od něj rozbalit. Koncový bod/schemas je užitečný při použití vlastních atributů nebo v případě, že se schéma často mění. Umožňuje klientovi, aby získal nejaktuálnější schéma automaticky. Koncový bod/Bulk je zvláště užitečný při podpoře skupin. Následující tabulka popisuje různé koncové body definované ve standardu SCIM. Koncový bod/schemas je užitečný při použití vlastních atributů nebo v případě, že se schéma často mění. Umožňuje klientovi načíst nejaktuálnější schéma automaticky. Koncový bod/Bulk je zvláště užitečný při podpoře skupin. Následující tabulka popisuje různé koncové body definované ve standardu SCIM. 
 
### <a name="table-4-determine-the-endpoints-that-you-would-like-to-develop"></a>Tabulka 4: určení koncových bodů, které byste chtěli vyvíjet
|KONCOVÝ BOD|POPIS|
|--|--|
|/User|Provádění operací CRUD u objektu uživatele.|
|/Group|Proveďte operace CRUD u objektu skupiny.|
|/ServiceProviderConfig|Obsahuje podrobné informace o funkcích podporovaného standardu SCIM, například o podporovaných zdrojích a metodě ověřování.|
|/ResourceTypes|Určuje metadata o jednotlivých prostředcích.|
|/Schemas|Sada atributů podporovaná jednotlivými klienty a poskytovatelem služeb se může lišit. Druhý poskytovatel služeb může zahrnovat "název", "title" a "e-maily", zatímco jiný poskytovatel služeb používá "název", "title" a "phoneNumbers". Koncový bod schémat umožňuje zjišťování podporovaných atributů.|
|/Bulk|Hromadné operace umožňují provádět operace s velkou kolekcí objektů prostředků v rámci jedné operace (například členství v aktualizacích pro velkou skupinu).|


## <a name="step-2-understand-the-azure-ad-scim-implementation"></a>Krok 2: pochopení implementace Azure AD SCIM
> [!IMPORTANT]
> Chování implementace Azure AD SCIM se naposledy aktualizovalo 18. prosince 2018. Informace o tom, co se změnilo, najdete v článku [dodržování předpisů protokolu SCIM 2,0 ve službě Azure AD pro zřizování uživatelů](application-provisioning-config-problem-scim-compatibility.md).

Pokud vytváříte aplikaci, která podporuje rozhraní API pro správu uživatelů SCIM 2,0, v této části najdete podrobné informace o implementaci klienta Azure AD SCIM. Také ukazuje, jak modelovat zpracování požadavků protokolu SCIM a jejich odpovědi. Po implementaci koncového bodu SCIM ho můžete otestovat pomocí postupu popsaného v předchozí části.

V rámci [specifikace protokolu SCIM 2,0](http://www.simplecloud.info/#Specification)musí vaše aplikace splňovat tyto požadavky:

* Podporuje vytváření uživatelů a volitelně také skupiny podle oddílu [3,3 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Podporuje úpravu uživatelů nebo skupin s požadavky na opravy podle [oddílu 3.5.2 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Podporuje načítání známého prostředku pro uživatele nebo skupinu vytvořené dříve, podle [oddílu 3.4.1 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Podporuje dotazování uživatelů nebo skupin podle části [3.4.2 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Ve výchozím nastavení jsou uživatelé načítáni pomocí jejich `id` a dotazováni jejich `username` a `externalid`a skupiny jsou dotazovány `displayName`.  
* Podporuje dotazování uživatele podle ID a podle manažera podle části 3.4.2 protokolu SCIM.  
* Podporuje dotazování skupin podle ID a členu podle části 3.4.2 protokolu SCIM.  
* Přijme jeden nosný token pro ověřování a autorizaci služby Azure AD pro vaši aplikaci.

Při implementaci SCIM koncového bodu, který zajistí kompatibilitu s Azure AD, postupujte podle těchto obecných pokynů:

* `id` je požadovaná vlastnost pro všechny prostředky. Každá odpověď, která vrací prostředek, by měla zajistit, aby každý prostředek měl tuto vlastnost, s výjimkou `ListResponse` s nulovými členy.
* Odpověď na požadavek na dotaz nebo filtr by měla být vždy `ListResponse`.
* Skupiny jsou volitelné, ale podporují se jenom v případě, že implementace SCIM podporuje žádosti o opravu.
* V reakci na opravu není nutné zahrnout celý prostředek.
* Microsoft Azure AD používá pouze následující operátory:  
    - `eq`
    - `and`
* Pro strukturální prvky v SCIM nemusíte rozlišovat velká a malá písmena, konkrétně PATCH `op` hodnoty operací, jak je definováno v https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD vygeneruje hodnoty "op" jako `Add`, `Replace`a `Remove`.
* Microsoft Azure AD vytváří požadavky na načtení náhodného uživatele a skupiny, aby bylo zajištěno, že koncový bod a přihlašovací údaje jsou platné. Také se provádí jako součást toku **testu připojení** v [Azure Portal](https://portal.azure.com). 
* Atribut, na kterém mohou být prostředky odesílány, by měl být nastaven jako odpovídající atribut v aplikaci v [Azure Portal](https://portal.azure.com). Další informace najdete v tématu [Přizpůsobení mapování atributů zřizování uživatelů](customize-application-attributes.md) .

### <a name="user-provisioning-and-deprovisioning"></a>Zřizování a rušení uživatelů

Následující ilustrace znázorňuje zprávy, které Azure Active Directory posílá službě SCIM ke správě životního cyklu uživatele v úložišti identit vaší aplikace.  

![zobrazuje pořadí zřizování a rušení zřizování uživatelů](media/use-scim-to-provision-users-and-groups/scim-figure-4.png)<br/>
*Pořadí zřizování a rušení zřizování uživatelů*

### <a name="group-provisioning-and-deprovisioning"></a>Zřizování skupin a jejich rušení

Zřizování skupin a zrušení zřízení jsou volitelné. Když se implementuje a povolí, na následujícím obrázku se zobrazí zprávy, které služba Azure AD pošle službě SCIM ke správě životního cyklu skupiny v úložišti identit vaší aplikace.  Tyto zprávy se liší od zpráv o uživatelích dvěma způsoby:

* Požadavky na načtení skupin určují, že atribut Members se má vyloučit z libovolného prostředku poskytnutého v reakci na požadavek.  
* Žádosti slouží k určení, zda má atribut typu odkaz určitou hodnotu se žádostí o členů atributu.  

![zobrazuje pořadí zřizování a rušení zřizování skupin](media/use-scim-to-provision-users-and-groups/scim-figure-5.png)<br/>
*Pořadí zřizování a rušení zřizování skupin*

### <a name="scim-protocol-requests-and-responses"></a>Požadavky a odpovědi protokolu SCIM
V této části najdete příklady požadavků SCIM vygenerovaných klientem Azure AD SCIM a příklady očekávaných odpovědí. Pro dosažení nejlepších výsledků byste měli kód aplikace, aby zpracovávala tyto požadavky v tomto formátu, a generovat očekávané odpovědi.

> [!IMPORTANT]
> Pokud chcete zjistit, jak a kdy služba zřizování uživatelů Azure AD vysílá níže popsané operace, přečtěte si část [zřizovací cykly: počáteční a přírůstkové](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) , [jak zřizování funguje](how-provisioning-works.md).

[Uživatelské operace](#user-operations)
  - [Vytvořit uživatele](#create-user) ([žádost o](#request) [odpověď](#response) / )
  - [Získat uživatele](#get-user) ([žádost o](#request-1) / [odpověď](#response-1))
  - [Získat uživatele dotazem](#get-user-by-query) ([požadavek](#request-2) / [odpověď](#response-2))
  - [Získat uživatele podle dotazů – žádné výsledky](#get-user-by-query---zero-results) ([požadavek](#request-3)
/ [odpověď](#response-3))
  - [Aktualizace uživatele [vlastnosti s více hodnotami]](#update-user-multi-valued-properties) ([žádost](#request-4) /  [odpověď](#response-4))
  - [Aktualizace uživatele [vlastnosti s jednou hodnotou]](#update-user-single-valued-properties) ([žádost](#request-5)
/ [odpověď](#response-5)) 
  - [Zakázat uživatele](#disable-user) ([požadavek](#request-14) / 
[Response](#response-14))
  - [Odstranit uživatele](#delete-user) ([žádost o](#request-6) [odpověď](#response-6) / 
)


[Operace skupiny](#group-operations)
  - [Vytvořit skupinu](#create-group) ( [žádost o](#request-7) [odpověď](#response-7) / )
  - [Získat skupinu](#get-group) ( [žádost o](#request-8) [odpověď](#response-8) / )
  - [Získat Group by DisplayName](#get-group-by-displayname) ([Request](#request-9) / [Response](#response-9))
  - [Update Group [atributy nečlenu]](#update-group-non-member-attributes) ([žádost](#request-10) /
 [odpověď](#response-10))
  - [Aktualizace skupiny [přidat členy]](#update-group-add-members) ( [žádost o](#request-11) [odezvu](#response-11) /
)
  - [Aktualizace skupiny [odebrat členy]](#update-group-remove-members) ( [žádost o](#request-12) [odezvu](#response-12) /
)
  - [Odstranit skupinu](#delete-group) ([žádost o](#request-13) [odpověď](#response-13) /
)

### <a name="user-operations"></a>Uživatelské operace

* Na uživatele lze zadat dotaz pomocí atributů `userName` nebo `email[type eq "work"]`.  

#### <a name="create-user"></a>Vytvoření uživatele

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

#### <a name="get-user"></a>Načíst uživatele

###### <a name="request-1"></a>Request
*ZÍSKAT/Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Odpověď (uživatel se našla)
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

##### <a name="request-2"></a>Request

*ZÍSKAT/Users? Filter = userName EQ "Test_User_dfeef4c5-5681 -4387-B016-bdf221e82081"*

##### <a name="response-2"></a>Základě

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

##### <a name="request-3"></a>Request

*ZÍSKAT/Users? Filter = userName EQ "neexistující uživatel"*

##### <a name="response-3"></a>Základě

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

##### <a name="request-4"></a>Request

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

##### <a name="response-4"></a>Základě

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

##### <a name="request-5"></a>Request

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

##### <a name="response-5"></a>Základě

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

### <a name="disable-user"></a>Zakázat uživatele

##### <a name="request-14"></a>Request

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

##### <a name="response-14"></a>Základě

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

##### <a name="request-6"></a>Request

*Odstranit/Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Základě

*HTTP/1.1 204 bez obsahu*

### <a name="group-operations"></a>Operace skupiny

* Skupiny se vždy vytvoří s prázdným seznamem členů.
* Do skupin lze zadat dotaz pomocí atributu `displayName`.
* Aktualizace žádosti o opravu skupiny by měla v odpovědi vracet *HTTP 204 bez obsahu* . Vrácení textu se seznamem všech členů není vhodné.
* Není nutné podporovat vrácení všech členů skupiny.

#### <a name="create-group"></a>Vytvoření skupiny

##### <a name="request-7"></a>Request

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

##### <a name="response-7"></a>Základě

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

#### <a name="get-group"></a>Získat skupinu

##### <a name="request-8"></a>Request

*ZÍSKAT/Groups/40734ae655284ad3abcc? excludedAttributes = Members HTTP/1.1*

##### <a name="response-8"></a>Základě
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

##### <a name="request-9"></a>Request
*GET/Groups? excludedAttributes = Members & Filter = DisplayName EQ "DisplayName" HTTP/1.1*

##### <a name="response-9"></a>Základě

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

##### <a name="request-10"></a>Request

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

##### <a name="response-10"></a>Základě

*HTTP/1.1 204 bez obsahu*

### <a name="update-group-add-members"></a>Aktualizace skupiny [přidat členy]

##### <a name="request-11"></a>Request

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

##### <a name="response-11"></a>Základě

*HTTP/1.1 204 bez obsahu*

#### <a name="update-group-remove-members"></a>Skupina aktualizací [odebrat členy]

##### <a name="request-12"></a>Request

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

##### <a name="response-12"></a>Základě

*HTTP/1.1 204 bez obsahu*

#### <a name="delete-group"></a>Odstranění skupiny

##### <a name="request-13"></a>Request

*Odstranit/Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Základě

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


## <a name="step-3-build-a-scim-endpoint"></a>Krok 3: Vytvoření koncového bodu SCIM

Teď, když jste si desidned schéma a pochopili implementaci Azure AD SCIM, můžete začít vyvíjet svůj koncový bod SCIM. Místo začátku od nuly a sestavení implementace zcela na vlastní, můžete spoléhat na počet Open Source knihoven SCIM publikovaných SCIM Commuinty.  
Open source [referenční kód](https://aka.ms/SCIMReferenceCode) .NET Core publikovaný týmem zřizování Azure AD je jeden takový prostředek, který může přejít k zahájení vývoje. Po vytvoření koncového bodu SCIM ho budete chtít otestovat. Můžete použít kolekci předávacích [testů](https://github.com/AzureAD/SCIMReferenceCode/wiki/Test-Your-SCIM-Endpoint) , které jsou součástí referenčního kódu, nebo spustit prostřednictvím vzorových požadavků nebo odpovědí uvedených [výše](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#user-operations).  

Zde je, jak to funguje:

1. Azure AD poskytuje knihovnu rozhraní CLI (Common Language Infrastructure) s názvem Microsoft. SystemForCrossDomainIdentityManagement, která je součástí ukázek kódu, které jsou popsány níže. Systémové integrátory a vývojáři můžou pomocí této knihovny vytvořit a nasadit koncový bod webové služby založené na SCIM, který může propojit službu Azure AD s jakýmkoli úložištěm identit aplikace.
2. Mapování jsou implementovány ve webové službě k mapování schématu standardizované uživatele na schéma uživatele a protokol vyžadovaného aplikací. 
3. Adresa URL koncového bodu je zaregistrovaný ve službě Azure AD jako součást vlastní aplikace v galerii aplikací.
4. Uživatelé a skupiny jsou přiřazeny k této aplikaci ve službě Azure AD. Po přiřazení jsou vloženy do fronty, která bude synchronizována do cílové aplikace. Procesu synchronizace zpracování fronty se spouští každých 40 minut.

### <a name="code-samples"></a>Ukázky kódů

Pro snazší provádění tohoto procesu jsou k dispozici [ukázky kódu](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) , které vytvářejí koncový bod webové služby SCIM a předvádějí Automatické zřizování. Vzorek je poskytovatel, který uchovává soubor s řádky hodnot oddělených čárkou, které představují uživatele a skupiny.

**Požadavky**

* Visual Studio 2013 nebo novější
* [Azure SDK pro .NET](https://azure.microsoft.com/downloads/)
* Windows počítač, který podporuje rozhraní ASP.NET 4.5 se použije jako koncový bod SCIM. Tento počítač musí být přístupný z cloudu.
* [Předplatné Azure se zkušební nebo licencovanou verzí Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Začínáme

Nejjednodušší způsob, jak implementovat SCIM koncový bod, který může přijímat žádosti o zřízení ze služby Azure AD je k sestavení a nasazení vzorového kódu, jejichž výstupem jsou zřízené uživatele do souboru hodnot oddělených čárkami (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Vytvoření ukázkového koncového bodu SCIM

1. Stažení ukázkového balíčku kódu na [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Rozbalte balíček a umístěte ji na svém počítači s Windows do umístění, jako je například C:\AzureAD-BYOA-Provisioning-Samples\.
1. V této složce spusťte FileProvisioning\Host\FileProvisioningService.csproj projektu v sadě Visual Studio.
1. Vyberte **nástroje** > **správce balíčků NuGet** > **konzolu Správce balíčků**a spusťte následující příkazy pro projekt FileProvisioningService, abyste vyřešili odkazy na řešení:

   ```powershell
    Update-Package -Reinstall
   ```

1. FileProvisioningService projekt sestavte.
1. Spusťte aplikaci příkazového řádku v systému Windows (jako správce) a pomocí příkazu **CD** změňte adresář na složku **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug** .
1. Spusťte následující příkaz a nahraďte `<ip-address>` IP adresou nebo názvem domény počítače s Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. V systému Windows v části **nastavení systému windows** >  **& nastavení sítě Internet**vyberte možnost **Brána Windows Firewall** > **Upřesnit nastavení**a vytvořte **příchozí pravidlo** , které povolí příchozí přístup k portu 9000.
1. Pokud je počítač s Windows za směrovačem, je potřeba nakonfigurovat směrovač tak, aby spouštěl překlad přístupu k síti mezi jeho port 9000, který je zpřístupněný pro Internet, a port 9000 na počítači s Windows. Tato konfigurace je vyžadována pro Azure AD pro přístup k tomuto koncovému bodu v cloudu.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Registrace ukázkového koncového bodu SCIM ve službě Azure AD

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com). 
1. V levém podokně vyberte **podnikové aplikace** . Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací přidaných z galerie.
1. Vyberte **+ Nová aplikace** > **všechny** > **aplikací mimo galerii**.
1. Zadejte název vaší aplikace a vyberte **Přidat** a vytvořte objekt aplikace. Objekt aplikace vytvořené slouží k reprezentaci cílové aplikace, můžete by zřizování a implementaci jednotného přihlašování pro a nejen SCIM koncový bod.
1. Na obrazovce Správa aplikací vyberte na levém panelu možnost **zřizování** .
1. V nabídce **režim zřizování** vyberte **automaticky**.    
1. Do pole **Adresa URL tenanta** zadejte adresu URL koncového bodu SCIM aplikace. Příklad: https://api.contoso.com/scim/

1. Pokud koncový bod SCIM vyžaduje token nosiče OAuth od jiného vydavatele než Azure AD, Zkopírujte požadovaný token Bearer OAuth do pole volitelného **tajného tokenu** . Pokud toto pole zůstane prázdné, Azure AD zahrnuje token nosiče OAuth vydaný z Azure AD s každým požadavkem. Aplikace, které používají Azure AD jako zprostředkovatel identity, můžou ověřit tento token vydaný službou Azure AD.
1. Vyberte **Test připojení** , aby se Azure Active Directory pokus o připojení ke koncovému bodu SCIM. Pokud se pokus nezdaří, zobrazí se informace o chybě.  

    > [!NOTE]
    > **Test Connection** se dotazuje na koncový bod SCIM pro uživatele, který neexistuje, pomocí NÁHODNÉho identifikátoru GUID jako odpovídající vlastnosti vybrané v konfiguraci služby Azure AD. Očekávaná správná odpověď je HTTP 200 OK s prázdnou SCIM zprávou ListResponse
1. Pokud se pokusí připojit k aplikaci úspěšně, vyberte **Uložit** a uložte přihlašovací údaje správce.
1. V oddílu **mapování** existují dvě sady mapování atributů, které lze vybrat: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každé z nich ke kontrole atributů, které se synchronizují ze služby Azure Active Directory do vaší aplikace. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování s uživateli a skupinami v aplikaci pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.
1. V části **Nastavení**pole **obor** určuje, kteří uživatelé a skupiny budou synchronizováni. Vyberte možnost **synchronizovat pouze přiřazené uživatele a skupiny** (doporučeno), chcete-li synchronizovat pouze uživatele a skupiny přiřazené na kartě **Uživatelé a skupiny** .
1. Po dokončení konfigurace nastavte **stav zřizování** **na zapnuto**.
1. Výběrem **Uložit** spusťte službu Azure AD Provisioning.
1. Pokud synchronizujete pouze přiřazené uživatele a skupiny (doporučeno), nezapomeňte vybrat kartu **Uživatelé a skupiny** a přiřadit uživatele nebo skupiny, které chcete synchronizovat. Po zahájení počátečního cyklu můžete vybrat **protokoly auditu** na levém panelu a monitorovat průběh, který zobrazuje všechny akce prováděné službou zřizování ve vaší aplikaci. Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](check-status-user-account-provisioning.md).
V posledním kroku ověřování ukázka je k otevření souboru TargetFile.csv \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug složky na svém počítači s Windows. Po spuštění procesu zřizování tento soubor obsahuje podrobné informace o všech přiřazeny a zřízení uživatelů a skupin.

## <a name="step-4-integrate-your-scim-endpoint-with-the-azure-ad-scim-client"></a>Krok 4: integrace koncového bodu SCIM s klientem Azure AD SCIM

Služba Azure AD se dá nakonfigurovat tak, aby automaticky zřídila přiřazené uživatele a skupiny aplikacím, které implementují konkrétní profil [protokolu SCIM 2,0](https://tools.ietf.org/html/rfc7644). Konkrétní informace o profilu jsou zdokumentovány v [kroku 2: pochopení implementace Azure AD SCIM](#step-2-understand-the-azure-ad-scim-implementation).

Obraťte se na svého poskytovatele aplikace nebo dokumentaci poskytovatele aplikace pro příkazy kompatibilitu s těmito požadavky.

> [!IMPORTANT]
> Implementace Azure AD SCIM je postavená na službě Azure AD Provisioning Service, která je navržená tak, aby neustále udržovala synchronizaci mezi službou Azure AD a cílovou aplikací a implementovala velmi specifickou sadu standardních operací. Je důležité porozumět tomuto chování, abyste pochopili chování klienta Azure AD SCIM. Další informace najdete v části [cykly zřizování: počáteční a přírůstkové](how-provisioning-works.md#provisioning-cycles-initial-and-incremental) , [jak zřizování funguje](how-provisioning-works.md).

### <a name="getting-started"></a>Začínáme

Aplikace s podporou SCIM profilu je popsáno v tomto článku můžou být připojen k Azure Active Directory pomocí funkce "aplikaci mimo Galerii" v galerii aplikací Azure AD. Jakmile budete připojeni, Azure AD spustí proces synchronizace každých 40 minut, kde dotazuje aplikace SCIM koncový bod pro přiřazení uživatelé a skupiny a vytvoří nebo upraví podle podrobnosti o přiřazení.

**Připojení aplikace, která podporuje SCIM:**

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com). Všimněte si, že můžete získat přístup k bezplatné zkušební verzi pro Azure Active Directory s licencemi P2, a to tak, že si zaregistrujete [program pro vývojáře](https://developer.microsoft.com/office/dev-program) .
2. V levém podokně vyberte **podnikové aplikace** . Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací přidaných z galerie.
3. Vyberte **+ Nová aplikace** > **všechny** > **aplikací mimo galerii**.
4. Zadejte název vaší aplikace a vyberte **Přidat** a vytvořte objekt aplikace. Nová aplikace se přidá do seznamu podnikových aplikací a otevře se na obrazovce správy aplikací.

   ![snímek obrazovky se zobrazí v galerii aplikací Azure AD](media/use-scim-to-provision-users-and-groups/scim-figure-2a.png)<br/>
   *Galerie aplikací Azure AD*

5. Na obrazovce Správa aplikací vyberte na levém panelu možnost **zřizování** .
6. V nabídce **režim zřizování** vyberte **automaticky**.

   Příklad ![: stránka zřizování aplikace v Azure Portal](media/use-scim-to-provision-users-and-groups/scim-figure-2b.png)<br/>
   *Konfigurace zřizování v Azure Portal*

7. Do pole **Adresa URL tenanta** zadejte adresu URL koncového bodu SCIM aplikace. Příklad: https://api.contoso.com/scim/
8. Pokud koncový bod SCIM vyžaduje token nosiče OAuth od jiného vydavatele než Azure AD, Zkopírujte požadovaný token Bearer OAuth do pole volitelného **tajného tokenu** . Pokud toto pole zůstane prázdné, Azure AD zahrnuje token nosiče OAuth vydaný z Azure AD s každým požadavkem. Aplikace, které používají Azure AD jako zprostředkovatel identity, můžou ověřit tento token vydaný službou Azure AD. 
   > [!NOTE]
   > ***Nedoporučujeme*** toto pole nechat prázdné a spoléhat se na token generovaný službou Azure AD. Tato možnost je primárně k dispozici pro účely testování.
9. Vyberte **Test připojení** , aby se Azure Active Directory pokus o připojení ke koncovému bodu SCIM. Pokud se pokus nezdaří, zobrazí se informace o chybě.  

    > [!NOTE]
    > **Test Connection** se dotazuje na koncový bod SCIM pro uživatele, který neexistuje, pomocí NÁHODNÉho identifikátoru GUID jako odpovídající vlastnosti vybrané v konfiguraci služby Azure AD. Očekávaná správná odpověď je HTTP 200 OK s prázdnou SCIM zprávou ListResponse.

10. Pokud se pokusí připojit k aplikaci úspěšně, vyberte **Uložit** a uložte přihlašovací údaje správce.
11. V oddílu **mapování** existují dvě sady [mapování atributů](customize-application-attributes.md), které lze vybrat: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každé z nich ke kontrole atributů, které se synchronizují ze služby Azure Active Directory do vaší aplikace. Atributy vybrané jako **odpovídající** vlastnosti se používají ke spárování s uživateli a skupinami v aplikaci pro operace aktualizace. Vyberte **Uložit** a potvrďte všechny změny.

    > [!NOTE]
    > Volitelně můžete zakázat synchronizaci objektů skupiny zakázáním "groups" mapování.

12. V části **Nastavení**pole **obor** určuje, kteří uživatelé a skupiny budou synchronizováni. Vyberte možnost **synchronizovat pouze přiřazené uživatele a skupiny** (doporučeno), chcete-li synchronizovat pouze uživatele a skupiny přiřazené na kartě **Uživatelé a skupiny** .
13. Po dokončení konfigurace nastavte **stav zřizování** **na zapnuto**.
14. Výběrem **Uložit** spusťte službu Azure AD Provisioning.
15. Pokud synchronizujete pouze přiřazené uživatele a skupiny (doporučeno), nezapomeňte vybrat kartu **Uživatelé a skupiny** a přiřadit uživatele nebo skupiny, které chcete synchronizovat.

Po zahájení počátečního cyklu můžete na levém panelu vybrat **protokoly zřizování** a monitorovat průběh, který zobrazuje všechny akce prováděné službou zřizování ve vaší aplikaci. Další informace o tom, jak číst protokoly zřizování Azure AD, najdete v tématu [vytváření sestav o automatickém zřizování uživatelských účtů](check-status-user-account-provisioning.md).

> [!NOTE]
> Počáteční cyklus trvá déle než pozdější synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná.

## <a name="step-5-publish-your-application-to-the-azure-ad-application-gallery"></a>Krok 5: publikování aplikace v galerii aplikací Azure AD

Pokud vytváříte aplikaci, kterou bude používat víc než jeden tenant, můžete je zpřístupnit v galerii aplikací Azure AD. Díky tomu budou moci organizace snadno vyhledat aplikaci a nakonfigurovat zřizování. Publikování aplikace v galerii Azure AD a zpřístupnění pro ostatní je snadné. Podívejte se na tento [postup.](../develop/howto-app-gallery-listing.md) Microsoft bude s vámi spolupracovat na integraci vaší aplikace do naší galerie, testování koncového bodu a [dokumentaci](../saas-apps/tutorial-list.md) k registraci pro zákazníky, kteří budou používat. 

### <a name="gallery-onboarding-checklist"></a>Kontrolní seznam pro registraci galerie
Postupujte podle níže uvedeného kontrolního seznamu a ujistěte se, že je vaše aplikace rychlá a zákazníci mají hladké prostředí pro nasazení. Informace budou shromažďovány od vás při připojování k galerii. 
> [!div class="checklist"]
> * Podpora koncového bodu uživatelů a skupin [SCIM 2,0](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#step-2-understand-the-azure-ad-scim-implementation) (je vyžadována pouze jedna, ale doporučuje se obě)
> * Podpora minimálně 25 požadavků za sekundu na tenanta (povinné)
> * Navázání technologických kontaktů a vedení a podpory pro zákazníky po registraci galerie (povinné)
> * 3 přihlašovací údaje pro vaši aplikaci bez vypršení platnosti (povinné)
> * Podpora udělení autorizačního kódu OAuth nebo dlouhého nedlouhodobého tokenu, jak je popsáno níže (povinné)
> * Zřízení technického a podpůrného kontaktního bodu pro podporu zákazníků po registraci galerie (povinné)
> * Podpora aktualizace více členství ve skupině s jednou OPRAVou (doporučeno) 
> * Veřejně zdokumentujte svůj koncový bod SCIM (doporučeno) 
> * [Podpora zjišťování schématu](https://tools.ietf.org/html/rfc7643#section-6) (doporučeno)


### <a name="authorization-for-provisioning-connectors-in-the-application-gallery"></a>Autorizace pro zřizovací konektory v galerii aplikací
Specifikace SCIM nedefinuje schéma specifické pro SCIM pro ověřování a autorizaci. Spoléhá se na použití stávajících oborových standardů. Klient zřizování Azure AD podporuje dvě autorizační metody pro aplikace v galerii. 

|Autorizační metoda|V oblasti IT|Nevýhody|Podpora|
|--|--|--|--|
|Uživatelské jméno a heslo (nedoporučuje se ani nepodporuje služba Azure AD)|Snadná implementace|Nezabezpečené – [vaše PA $ $Word nezáleží](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/your-pa-word-doesn-t-matter/ba-p/731984)|Podporuje se pro aplikace v galerii případ od případu. Nepodporuje se pro aplikace mimo galerii.|
|Dlouhý nosný token|Dlouhodobé tokeny nevyžadují, aby uživatel byl přítomen. Správci se můžou při nastavování zřizování snadno použít.|Dlouhotrvající tokeny může být obtížné sdílet se správcem bez použití nezabezpečených metod, jako je e-mailová adresa. |Podporováno pro galerie a aplikace mimo galerii. |
|Udělení autorizačního kódu OAuth|Přístupové tokeny jsou mnohem kratší než hesla a mají mechanismus automatizovaného obnovení, který nemá dlouhodobé tokeny pro nosiče.  Skutečný uživatel musí být přítomen při počáteční autorizaci a přidává úroveň zodpovědnosti. |Vyžaduje, aby byl uživatel přítomen. Pokud uživatel odejde z organizace, token je neplatný a autorizaci bude nutné dokončit znovu.|Podporováno pro aplikace v galerii. Probíhá podpora pro aplikace mimo galerii.|
|Udělení přihlašovacích údajů klienta OAuth|Přístupové tokeny jsou mnohem kratší než hesla a mají mechanismus automatizovaného obnovení, který nemá dlouhodobé tokeny pro nosiče. Udělení autorizačního kódu i udělení přihlašovacích údajů klienta vytvoří stejný typ přístupového tokenu, takže přesun mezi těmito metodami je pro rozhraní API transparentní.  Zřizování se dá kompletně automatizovat a nové tokeny se můžou bez zásahu uživatele považovat za tichou. ||Nepodporuje se pro galerie a aplikace mimo galerii. Podpora je v našich nevyřízených položkách.|

[!NOTE] V uživatelském rozhraní vlastní aplikace pro konfiguraci zřizování Azure AD se nedoporučuje ponechat pole token prázdné. Vygenerovaný token je primárně k dispozici pro účely testování.

**Tok udělení autorizačního kódu OAuth:** Služba zřizování podporuje [udělení autorizačního kódu](https://tools.ietf.org/html/rfc6749#page-24). Po odeslání žádosti o publikování vaší aplikace v galerii bude náš tým spolupracovat s vámi a shromažďovat následující informace:
*  Autorizační adresa URL: adresa URL klienta, který získá autorizaci od vlastníka prostředku prostřednictvím přesměrování uživatelského agenta. Uživatel se přesměruje na tuto adresu URL a autorizuje přístup. 
*  Adresa URL pro výměnu tokenu: adresa URL, kterou klient vyměňuje udělení autorizace přístupového tokenu, obvykle s ověřením klienta.
*  ID klienta: autorizační server vydá registrovanému klientovi identifikátor klienta, což je jedinečný řetězec, který představuje registrační informace poskytované klientem.  Identifikátor klienta není tajný. je vystavena vlastníkovi prostředku a **nesmí** se používat samostatně pro ověřování klientů.  
*  Tajný kód klienta: tajný klíč klienta je tajný kód vygenerovaný autorizačním serverem. Mělo by se jednat o jedinečnou hodnotu známou jenom pro autorizační Server. 

Upozorňujeme, že OAuth v1 není podporován kvůli expozici tajného klíče klienta. Je podporován protokol OAuth v2.  

Osvědčené postupy (doporučeno, ale není nutné):
* Podporuje více adres URL pro přesměrování. Správci mohou nakonfigurovat zřizování z obou "portal.azure.com" i "aad.portal.azure.com". Podpora více adres URL pro přesměrování zajistí, že uživatelé budou moct autorizovat přístup z obou portálu.
* Podpora více tajných kódů, aby bylo zajištěno hladké obnovení tajných kódů bez výpadků. 

**Dlouhodobé tokeny nosiče OAuth:** Pokud vaše aplikace nepodporuje tok udělení autorizačního kódu OAuth, můžete také vygenerovat dlouhodobé tokeny Bearer OAuth, než může správce použít k nastavení integrace zřizování. Token by měl být trvalý nebo jinak bude úloha zřizování v [karanténě](application-provisioning-quarantine-status.md) , až vyprší platnost tokenu. Hodnota tohoto tokenu musí být nižší než 1 KB.  

V případě dalších metod ověřování a autorizace dejte nám na [UserVoice](https://aka.ms/appprovisioningfeaturerequest)informace.

### <a name="gallery-go-to-market-launch-check-list"></a>Seznam kontrol spuštění v galerii pro uvedení na trh
Abychom vám pomohli při zvyšování povědomí a vyžádání naší společné integrace, doporučujeme aktualizovat svou stávající dokumentaci a rozšířit integraci do marketingových kanálů.  Níže je uveden seznam aktivit kontrolního seznamu, které doporučujeme dokončit pro podporu spuštění.

* **Připravenost na prodej a zákaznickou podporu.** Zajistěte, aby si týmy pro prodej a podporu byly vědomy a mohli mluvit s možnostmi integrace. Stručně váš tým pro prodej a podporu jim poskytne Nejčastější dotazy a integruje integraci do vašich prodejních materiálů. 
* **Příspěvek na blogu nebo tiskovou verzi.** Vytvořte Blogový příspěvek nebo stiskněte vydanou verzi, která popisuje společnou integraci, výhody a postupy, jak začít. [Příklad: Imprivata and Azure Active Directory Press vydaná verze](https://www.imprivata.com/company/press/imprivata-introduces-iam-cloud-platform-healthcare-supported-microsoft) 
* **Sociální média.** Využijte své sociální média, jako je Twitter, Facebook nebo LinkedIn, a Propagujte integraci s vašimi zákazníky. Nezapomeňte zahrnout @AzureAD, abychom mohli vyložit svůj příspěvek. [Příklad: Imprivata Twitter post](https://twitter.com/azuread/status/1123964502909779968)
* **Marketingový Web.** Umožňuje vytvořit nebo aktualizovat marketingové stránky (například stránku integrace, stránku pro partnery, ceny atd.), aby zahrnovaly dostupnost společné integrace. [Příklad: stránka integrace Pingboard](https://pingboard.com/org-chart-for), stránka [integrace Smartsheet](https://www.smartsheet.com/marketplace/apps/microsoft-azure-ad), [Stránka s cenami Monday.com](https://monday.com/pricing/) 
* **Technickou dokumentaci.** Vytvořte článek centra pro nápovědu nebo technickou dokumentaci, jak mohou zákazníci začít. [Příklad: integrace zástupné + Microsoft Azure Active Directory.](https://envoy.help/en/articles/3453335-microsoft-azure-active-directory-integration/
) 
* **Komunikace se zákazníky.** Upozorní zákazníky na novou integraci prostřednictvím zákaznické komunikace (měsíční bulletiny, e-mailové kampaně, poznámky k verzi produktu). 

### <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Povolte IP adresy, které používá služba zřizování Azure AD k provádění požadavků SCIM.

Některé aplikace umožňují příchozí provoz do své aplikace. Aby služba zřizování služby Azure AD fungovala podle očekávání, musí být povolené IP adresy. Seznam IP adres pro jednotlivé značky nebo oblasti služby najdete v souboru JSON – [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519). Tyto IP adresy můžete podle potřeby stáhnout a programovat do brány firewall. Rezervované rozsahy IP adres pro zřizování Azure AD najdete v části "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Související články

* [Automatizace zřizování a rušení zřizování uživatelů pro aplikace SaaS](user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](customize-application-attributes.md)
* [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Oznámení zřizování účtů](user-provisioning.md)
* [Seznam kurzů, jak integrovat aplikace SaaS](../saas-apps/tutorial-list.md)

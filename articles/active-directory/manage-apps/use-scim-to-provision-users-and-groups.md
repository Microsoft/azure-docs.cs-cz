---
title: Automatizace zřizování aplikací pomocí SCIM ve službě Azure Active Directory | Dokumentace Microsoftu
description: Azure Active Directory můžete automaticky zřizovat uživatele a skupiny, které se aplikace nebo identity úložiště, který je přední stěnou webová služba s rozhraním definovaných ve specifikaci protokolu SCIM
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 2/22/2018
ms.author: celested
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 785c02a0187cf02df3bb3cf5d248f97a3edf74c6
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961750"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Automatické zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí systému pro mezi doménami Identity Management (SCIM)

## <a name="overview"></a>Přehled

SCIM je standardizovaná protokol a schéma, které se zaměřuje na disku větší konzistence ve způsobu správy identit napříč systémy. Pokud aplikace podporuje koncový bod SCIM správy uživatelů, služba zřizování uživatelů Azure AD můžete posílat požadavky k vytvoření, úpravě nebo odstranění přiřazení uživatelé a skupiny k tomuto koncovému bodu. 

Aplikace, pro který Azure AD podporuje mnoho [předem integrované automatické zřizování uživatelů](../saas-apps/tutorial-list.md) implementovat SCIM jako způsob, jak uživatel dostávat upozornění na změnu.  Kromě toho zákazníci mohou připojit aplikace, které podporují konkrétní profil [SCIM 2.0 specifikace protokolu](https://tools.ietf.org/html/rfc7644) pomocí možnosti integrace obecný "mimo Galerii" na webu Azure Portal. 

Hlavní téma tohoto dokumentu je v profilu SCIM 2.0, který implementuje Azure AD jako součást její obecný konektor SCIM pro aplikace mimo galerii. Ale úspěšně testování aplikace, která podporuje SCIM pomocí obecného Azure AD connector je krok uvedená v galerii Azure AD jako podpora zřizování uživatelů pro aplikaci. Další informace o zařazení vaší aplikace, uvedená v galerii aplikací Azure AD, najdete v článku [Microsoft Application Network](https://microsoft.sharepoint.com/teams/apponboarding/Apps/SitePages/Default.aspx).
 

>[!IMPORTANT]
>Chování Azure AD SCIM implementace poslední aktualizace 18. prosince 2018. Informace o tom, co se změnilo, naleznete v tématu [SCIM 2.0 protokol dodržování předpisů služby zřizování uživatelů služby Azure AD](application-provisioning-config-problem-scim-compatibility.md).

![][0]
*Obrázek 1: Zřizování z Azure Active Directory do aplikací nebo identity úložiště, který implementuje SCIM*

Tento článek je rozdělený do čtyř oddílů:

* **[Zřizování uživatelů a skupin a aplikací třetích stran, které podporují SCIM 2.0](#provisioning-users-and-groups-to-applications-that-support-scim)**  – Pokud vaše organizace používá aplikaci třetí strany, že implementuje profil SCIM 2.0 této službě Azure AD podporuje, je můžete začít s automatizací obojí zřizování a zrušení zřizování uživatelů a skupin ještě dnes.

* **[Znalost implementace Azure AD SCIM](#understanding-the-azure-ad-scim-implementation)**  – Pokud vytváříte aplikaci, která podporuje správu uživatelů SCIM 2.0 rozhraní API, tato část podrobně popisuje způsob implementace klienta Azure AD SCIM a jak by měl modelů váš protokol SCIM žádosti o zpracování odpovědi.
  
* **[Vytváření koncový bod SCIM pomocí knihovny Microsoft CLI](#building-a-scim-endpoint-using-microsoft-cli-libraries)**  – Pokud chcete pomoci při vývoji koncový bod SCIM, neexistují knihovny společné jazykové infrastruktury (CLI) spolu s ukázek kódu, které ukazují, jak zadat koncový bod SCIM a Převede uzel SCIM zprávy.  

* **[Referenční dokumentace schématu uživatelů a skupin](#user-and-group-schema-reference)**  -popisuje schéma uživatele a skupiny podporováno implementací Azure AD SCIM pro aplikace mimo galerii. 

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Zřizování uživatelů a skupin a aplikace s podporou SCIM
Dá se Azure AD automaticky zřizovat přiřazené uživatele a skupiny, které se aplikace, které implementují konkrétní profil [protokol SCIM 2.0](https://tools.ietf.org/html/rfc7644). Podrobnosti profilu jsou dokumentovány v článku [znalost implementace Azure AD SCIM](#implementing-a-scim-endpoint-that-works-with-azure-ad-user-provisioning).

Obraťte se na svého poskytovatele aplikace nebo dokumentaci poskytovatele aplikace pro příkazy kompatibilitu s těmito požadavky.

>[!IMPORTANT]
>Implementace Azure AD SCIM je postavený na uživatele Azure AD, zřizovací služba, která je navržena pro trvale synchronizaci uživatelů mezi službami Azure AD a cílovou aplikaci a implementuje specifickou sadu standardních operací. je důležité pochopit, chcete-li pochopit chování klienta Azure AD SCIM těchto chování. Další informace najdete v tématu [co se stane během zřizování uživatelů?](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Začínáme
Aplikace s podporou SCIM profilu je popsáno v tomto článku můžou být připojen k Azure Active Directory pomocí funkce "aplikaci mimo Galerii" v galerii aplikací Azure AD. Jakmile budete připojeni, Azure AD spustí proces synchronizace každých 40 minut, kde dotazuje aplikace SCIM koncový bod pro přiřazení uživatelé a skupiny a vytvoří nebo upraví podle podrobnosti o přiřazení.

**Připojení aplikace, která podporuje SCIM:**

1. Přihlaste se k [na webu Azure portal](https://portal.azure.com). 
2. Přejděte do **Azure Active Directory > podnikové aplikace**a vyberte **novou aplikaci > všechny > aplikace mimo galerii**.
3. Zadejte název pro vaši aplikaci a klikněte na tlačítko **přidat** ikonu pro vytvoření objektu aplikace.
    
   ![][1]
   *Obrázek 2: Galerie aplikací Azure AD*
    
4. Zobrazenou obrazovku, vyberte **zřizování** kartu v levém sloupci.
5. V **režim zřizování** nabídce vyberte možnost **automatické**.
    
   ![][2]
   *Obrázek 3: Konfigurace zřizování na webu Azure Portal*
    
6. V **adresy URL Tenanta** zadejte adresu URL koncového bodu SCIM vaší aplikace. Příklad: https://api.contoso.com/scim/v2/
7. Pokud koncový bod SCIM vyžaduje tokenu nosiče OAuth z vystavitele než Azure AD, zkopírujte požadované tokenu nosiče OAuth nepovinný **tajný klíč tokenu** pole. Pokud toto pole necháte prázdné, Azure AD zahrnuje vydané ze služby Azure AD s každou žádostí tokenu nosiče OAuth. Aplikace, které používají Azure AD jako zprostředkovatele identity můžete ověřit tento Azure AD vystavený token.
8. Klikněte na tlačítko **Test připojení** tlačítko s Azure Active Directory, pokus o připojení ke koncovému bodu SCIM. Pokud se nezdaří pokusy, zobrazí se informace o chybě.  

    >[!NOTE]
    >**Test připojení** dotazuje SCIM koncový bod pro uživatele, který neexistuje, pomocí náhodný GUID jako odpovídající vlastnost vybrané v konfiguraci Azure AD. Očekávaný správná odpověď je prázdná zpráva SCIM ListResponse HTTP 200 OK. 

9. Pokud pokusy o připojení k aplikaci uspět, pak klikněte na tlačítko **Uložit** k uložení přihlašovacích údajů správce.
10. V **mapování** části, existují dvě sady vybrat mapování atributů: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každé z nich ke kontrole atributů, které se synchronizují ze služby Azure Active Directory do vaší aplikace. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatele a skupiny ve vaší aplikaci pro operace update. Vyberte tlačítko Uložit potvrďte změny.

    >[!NOTE]
    >Volitelně můžete zakázat synchronizaci objektů skupiny zakázáním "groups" mapování. 

11. V části **nastavení**, **oboru** pole určuje, kteří uživatelé a skupiny synchronizované. Výběr "Synchronizovat jenom přiřazené uživatele a skupiny" (doporučeno) bude synchronizovat jenom uživatelé a skupiny přiřazení v **uživatelů a skupin** kartu.
12. Po dokončení konfigurace se změnit **stavu zřizování** k **na**.
13. Klikněte na tlačítko **Uložit** spustit služba zřizování Azure AD. 
14. Je-li synchronizovat jenom přiřazené uživatele a skupiny (doporučeno), je potřeba vybrat možnost **uživatelů a skupin** kartu a přiřaďte uživatele a/nebo skupiny, kterou chcete synchronizovat.

Jakmile se počáteční synchronizace se spustila, můžete použít **protokoly auditu** kartu sledování průběhu, který zobrazuje všechny akce provedené v zřizovací služba ve vaší aplikaci. Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](check-status-user-account-provisioning.md).

> [!NOTE]
> Počáteční synchronizace trvá déle než při následné synchronizace, ke kterým dochází přibližně každých 40 minut za předpokladu, že služba běží. 


## <a name="understanding-the-azure-ad-scim-implementation"></a>Znalost implementace Azure AD SCIM

Pokud vytváříte aplikaci, která podporuje správu uživatelů SCIM 2.0 rozhraní API, tato část podrobně popisuje způsob implementace klienta Azure AD SCIM a jak by měl modelů váš protokol SCIM žádostí o zpracování a odpovědi. Když naimplementujete SCIM koncový bod, takže ji můžete otestovat pomocí následujícího postupu popsaného v předchozí části.

V rámci [SCIM 2.0 specifikace protokolu](http://www.simplecloud.info/#Specification), vaše aplikace musí splňovat tyto požadavky:

* Podporuje vytváření uživatele a volitelně také skupiny, podle části [3.3 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Podporuje úpravy uživatele a/nebo skupiny s žádostmi o opravu podle [části 3.5.2 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Podporuje načítání známých prostředků pro uživatele nebo skupinu vytvořili dříve, jak je uvedeno [části 3.4.1 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Podporuje dotazování uživatele a/nebo skupiny, podle části [3.4.2 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Ve výchozím nastavení, uživatelé jsou načítána pro jejich `id` Power pivotu a dotazované podle jejich `username` a `externalid`, a jsou skupiny dotazovat `displayName`.  
* Podporuje dotazování uživatele tak, že ID a správce podle části 3.4.2 SCIM protokolu.  
* Podporuje dotazování skupiny tak, že ID a člen podle části 3.4.2 SCIM protokolu.  
* Přijímá jeden nosný token pro ověření a autorizaci služby Azure AD pro vaši aplikaci.

Kromě toho postupujte podle následujících obecných pokynů při implementaci koncový bod SCIM pro zajištění kompatibility s Azure AD:

* `id` je povinná pro všechny zdroje. každé odpovědi, která vrací prostředku by měl zajistit každý prostředek je tato vlastnost, s výjimkou `ListResponse` s nulový počet členů.
* Odpověď na žádost/filtr dotazu by měla být vždy `ListResponse`.
* Skupiny jsou volitelné, ale pouze podporován, když SCIM implementace podporuje žádosti PATCH.
* Není nutné zahrnout do odpovědi oprava celý zdroj.
* Microsoft Azure AD používá pouze následující operátory  
     - `eq`
     - `and`
* Nevyžadují, aby malá a velká písmena se vyhledala shoda podle konstrukční prvky v SCIM v PATCH zejména `op` operace hodnoty, jak jsou definovány v https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD vydá hodnoty "op" jako `Add`, `Replace`, a `Remove`.
* Microsoft Azure AD díky požadavky na načtení náhodné uživatele a skupiny k zajištění, že koncový bod a přihlašovací údaje jsou platné. Také se provádí jako součást **Test připojení** tok v [webu Azure portal](https://portal.azure.com). 
* Atribut prostředky je možné zadávat dotazy na musí být nastavená jako odpovídající atribut v aplikaci v [webu Azure portal](https://portal.azure.com). Další informace najdete v tématu [přizpůsobení mapování atributů služby uživatele zřizování](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Zřizování uživatelů a jeho rušení
Následující ilustrace ukazuje zprávy, že Azure Active Directory odesílá do služby SCIM ke správě životního cyklu uživatele v úložišti identitu vaší aplikace.  

![][4]
*Obrázek 4: Zřizování uživatelů a jeho rušení pořadí*

### <a name="group-provisioning-and-de-provisioning"></a>Skupinové zřizování a zrušení zřizování.
Skupina zřizování a jeho rušení jsou volitelné. Když implementována a povolena, na následujícím obrázku zobrazuje zprávy, že Azure AD, které se odešle službě SCIM ke správě životního cyklu skupiny ve vaší aplikace úložiště identit.  Tyto zprávy se liší od zprávy týkající se uživatelů dvěma způsoby: 

* Požadavky k načtení skupin stanovit, že atribut členy mají být vyloučeny z libovolného zdroje k dispozici v odpovědi na požadavek.  
* Žádosti slouží k určení, zda má atribut typu odkaz určitou hodnotu se žádostí o členů atributu.  

![][5]
*Obrázek 5: Skupinové zřizování a jeho rušení pořadí*

### <a name="scim-protocol-requests-and-responses"></a>SCIM protokol požadavky a odpovědi
Tato část poskytuje příklad SCIM žádosti, protože ho vygeneroval klienta Azure AD SCIM, jakož i očekává ukázkové odpovědi. Nejlepších výsledků dosáhnete by měl kód vaší aplikace pro zpracování těchto žádostí v následujícím formátu a generovat očekávané odpovědi.

>[!IMPORTANT]
>Vysvětlení, jak a kdy služba zřizování uživatelů Azure AD vydá operací popsaných níže najdete v tématu [co se stane během zřizování uživatelů?](user-provisioning.md#what-happens-during-provisioning).

- [Operace uživatelů](#user-operations)
    - [Vytvoření uživatele](#create-user) - [žádosti](#request) - [odpovědi](#response)
    - [Načíst uživatele](#get-user) - [žádosti](#request-1) - [odpovědi](#response-1)
    - [Načíst uživatele dotazem](#get-user-by-query) - [žádosti](#request-2) - [odpovědi](#response-2)
    - [Načíst uživatele dotazem - nula výsledků](#get-user-by-query---zero-results) - [žádosti](#request-3) - [odpovědi](#response-3)
    - [Aktualizace uživatele [více Vážíme si toho vlastnosti]](#update-user-multi-valued-properties) - [žádosti](#request-4) - [odpovědi](#response-4)
    - [Aktualizace uživatele [jednou hodnotou vlastnosti]](#update-user-single-valued-properties) - [žádosti](#request-5) - [odpovědi](#response-5)
    - [Odstranit uživatele](#delete-user) - [žádosti](#request-6) - [odpovědi](#response-6)
- [Operace skupiny](#group-operations)
    - [Vytvoření skupiny](#create-group) - [žádosti](#request-7) - [odpovědi](#response-7)
    - [Získat skupinu](#get-group) - [žádosti](#request-8) - [odpovědi](#response-8)
    - [Získat skupinu podle displayName](#get-group-by-displayname) - [žádosti](#request-9) - [odpovědi](#response-9)
    - [Aktualizace skupiny [třetí atributy]](#update-group-non-member-attributes) - [žádosti](#request-10) - [odpovědi](#response-10)
    - [Skupina aktualizací [přidat členy]](#update-group-add-members) - [žádosti](#request-11) - [odpovědi](#response-11)
    - [Aktualizace skupiny [odebrat členy]](#update-group-remove-members) - [žádosti](#request-12) - [odpovědi](#response-12)
    - [Odstranit skupinu](#delete-group) - [žádosti](#request-13) - [odpovědi](#response-13)

### <a name="user-operations"></a>Operace uživatelů

* Uživatelé nemůžou dotazovat `userName` nebo `email[type eq "work"]` atributy.  

#### <a name="create-user"></a>Vytvořit uživatele

###### <a name="request"></a>Žádost
*PŘÍSPĚVEK/uživatelé*
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
*HTTP/1.1 201 vytvořili*
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

###### <a name="request"></a>Žádost
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response"></a>Odpověď
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
#### <a name="get-user-by-query"></a>Načíst uživatele podle dotazu

##### <a name="request"></a>Žádost
*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response"></a>Odpověď
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

#### <a name="get-user-by-query---zero-results"></a>Načíst uživatele dotazem - nula výsledků

##### <a name="request"></a>Žádost
*/ GET uživatelé? filter = eq uživatelské jméno "neexistující uživatel"*

##### <a name="response"></a>Odpověď
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

#### <a name="update-user-multi-valued-properties"></a>Aktualizace uživatele [více Vážíme si toho vlastnosti]

##### <a name="request"></a>Žádost
*Oprava/uživatele/6764549bef60420686bc HTTP/1.1*
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

##### <a name="response"></a>Odpověď
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

#### <a name="update-user-single-valued-properties"></a>Aktualizace uživatele [jednou hodnotou vlastnosti]

##### <a name="request"></a>Žádost
*Oprava/uživatele/5171a35d82074e068ce2 HTTP/1.1*
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

##### <a name="response"></a>Odpověď
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

#### <a name="delete-user"></a>Odstranění uživatele

##### <a name="request"></a>Žádost
*Odstranit /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response"></a>Odpověď
*HTTP/1.1 204 žádný obsah.*

### <a name="group-operations"></a>Operace skupiny

* Skupiny se vytvoří vždy se seznamem prázdné členy.
* Skupiny nemůžou dotazovat `displayName` atribut.
* Aktualizace skupiny požadavku PATCH by měl yield *HTTP 204 žádný obsah* v odpovědi. Vrátí text se seznamem všech členů není vhodné.
* Není nutné pro podporu vrací všechny členy skupiny.

#### <a name="create-group"></a>Vytvoření skupiny

##### <a name="request"></a>Žádost
*/ Groups POST protokolu HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "id": "c4d56c3c-bf3b-4e96-9b64-837018d6060e",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response"></a>Odpověď
*HTTP/1.1 201 vytvořili*
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

##### <a name="request"></a>Žádost
*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response"></a>Odpověď
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

##### <a name="request"></a>Žádost
*/ GET groups? excludedAttributes = členy & filtr = eq displayName "displayName" HTTP/1.1*

##### <a name="response"></a>Odpověď
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
#### <a name="update-group-non-member-attributes"></a>Aktualizovat skupinu [třetí atributy]

##### <a name="request"></a>Žádost
*Oprava/skupin/fa2ce26709934589afc5 HTTP/1.1*
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

##### <a name="response"></a>Odpověď
*HTTP/1.1 204 žádný obsah.*

### <a name="update-group-add-members"></a>Skupina aktualizací [přidat členy]

##### <a name="request"></a>Žádost
*Oprava/skupin/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a>Odpověď
*HTTP/1.1 204 žádný obsah.*

#### <a name="update-group-remove-members"></a>Aktualizovat skupinu [odebrat členy]

##### <a name="request"></a>Žádost
*Oprava/skupin/a99962b9f99d4c4fac67 HTTP/1.1*
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

##### <a name="response"></a>Odpověď
*HTTP/1.1 204 žádný obsah.*

#### <a name="delete-group"></a>Odstranění skupiny

##### <a name="request"></a>Žádost
*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response"></a>Odpověď
*HTTP/1.1 204 žádný obsah.*


## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Vytváření koncový bod SCIM pomocí knihovny Microsoft CLI
Vytvořením SCIM webové služby, které sdílí rozhraní se službou Azure Active Directory, můžete povolit automatické zřizování uživatelů pro téměř jakoukoli aplikaci nebo identity úložiště.

Zde je, jak to funguje:

1. Azure AD poskytuje společné knihovny jazykové infrastruktury (CLI) s názvem Microsoft.SystemForCrossDomainIdentityManagement součástí kódu, které popisují ukázky níže. Systémoví integrátoři a vývojáře, můžete použít tuto knihovnu k vytvoření a nasazení koncového bodu SCIM webové služby lze připojit k libovolné aplikace úložiště identit Azure AD.
2. Mapování jsou implementovány ve webové službě k mapování schématu standardizované uživatele na schéma uživatele a protokol vyžadovaného aplikací. 
3. Adresa URL koncového bodu je zaregistrovaný ve službě Azure AD jako součást vlastní aplikace v galerii aplikací.
4. Uživatelé a skupiny jsou přiřazeny k této aplikaci ve službě Azure AD. Po přiřazení jsou vloženy do fronty se dá provést synchronizace k cílové aplikaci. Procesu synchronizace zpracování fronty se spouští každých 40 minut.

### <a name="code-samples"></a>Ukázky kódů
Pro zjednodušení tohoto procesu [ukázky kódu](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) jsou k dispozici, vytvořte koncový bod webové služby SCIM a ukažte, automatické zřizování. Vzorek je poskytovatele, který udržuje soubor s řádky reprezentující uživatele a skupiny hodnot oddělených čárkami.    

**Požadavky**

* Visual Studio 2013 nebo novější
* [Azure SDK pro .NET](https://azure.microsoft.com/downloads/)
* Windows počítač, který podporuje rozhraní ASP.NET 4.5 se použije jako koncový bod SCIM. Tento počítač musí být přístupné z cloudu
* [Předplatné Azure s verzí zkušební verzi nebo s licencí Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Začínáme
Nejjednodušší způsob, jak implementovat SCIM koncový bod, který může přijímat žádosti o zřízení ze služby Azure AD je k sestavení a nasazení vzorového kódu, jejichž výstupem jsou zřízené uživatele do souboru hodnot oddělených čárkami (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Chcete-li vytvořit koncový bod ukázka SCIM

1. Stáhněte si balíček ukázky kódu v [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Rozbalte balíček a umístěte ji na svém počítači s Windows do umístění, jako je například C:\AzureAD-BYOA-Provisioning-Samples\.
3. V této složce spusťte FileProvisioning\Host\FileProvisioningService.csproj projektu v sadě Visual Studio.
4. Vyberte **nástroje > Správce balíčků NuGet > Konzola správce balíčků**a spusťte následující příkazy pro projekt FileProvisioningService k vyřešení odkazů řešení:

   ```
    Update-Package -Reinstall
   ```

5. FileProvisioningService projekt sestavte.
6. Spuštění aplikace příkazového řádku ve Windows (jako správce) a použít **cd** příkazu změňte adresář na vaše **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**složky.
7. Spusťte následující příkaz, < adresa > nahraďte IP adresy nebo domény název počítače Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

8. Ve Windows v rámci **nastavení Windows > síť a Internet nastavení**, vyberte **brány Windows Firewall > Upřesnit nastavení**a vytvoření **příchozí pravidlo** , který umožňuje příchozí přístup k portu 9000.
9. Pokud počítač Windows je za směrovač a směrovač je potřeba nakonfigurovat provádět překlad přístupu mezi jeho port 9000, který je přístupný z Internetu a port 9000 na počítači s Windows. Tato konfigurace je nutná pro službu Azure AD bude mít přístup k tomuto koncovému bodu v cloudu.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>K registraci koncového bodu SCIM ukázky v Azure AD

1. Přihlaste se k [na webu Azure portal](https://portal.azure.com). 
2. Přejděte do **Azure Active Directory > podnikové aplikace**a vyberte **novou aplikaci > všechny > aplikace mimo galerii**.
3. Zadejte název pro vaši aplikaci a klikněte na tlačítko **přidat** ikonu pro vytvoření objektu aplikace. Objekt aplikace vytvořené slouží k reprezentaci cílové aplikace, můžete by zřizování a implementaci jednotného přihlašování pro a nejen SCIM koncový bod.
4. Zobrazenou obrazovku, vyberte **zřizování** kartu v levém sloupci.
5. V **režim zřizování** nabídce vyberte možnost **automatické**.
    
  ![][2]
  *Obrázek 6: Konfigurace zřizování na webu Azure Portal*
    
6. V **adresy URL Tenanta** pole, zadejte adresu URL a port koncového bodu SCIM vystavený Internetu. Položka je něco jako http://testmachine.contoso.com:9000 nebo http://\<ip-address >: 9000 /, kde \<ip-address > je vystavený Internetu IP adresu.  
7. Pokud koncový bod SCIM vyžaduje tokenu nosiče OAuth z vystavitele než Azure AD, zkopírujte požadované tokenu nosiče OAuth nepovinný **tajný klíč tokenu** pole. Pokud toto pole necháte prázdné, bude obsahovat Azure AD z Azure AD s každou žádostí vydány tokenu nosiče OAuth. Aplikace, které používají Azure AD jako zprostředkovatele identity můžete ověřit této služby Azure AD – vydaný token.
8. Klikněte na tlačítko **Test připojení** tlačítko s Azure Active Directory, pokus o připojení ke koncovému bodu SCIM. Pokud se nezdaří pokusy, zobrazí se informace o chybě.  

    >[!NOTE]
    >**Test připojení** dotazuje SCIM koncový bod pro uživatele, který neexistuje, pomocí náhodný GUID jako odpovídající vlastnost vybrané v konfiguraci Azure AD. Očekávaný správná odpověď HTTP 200 OK je s prázdnou zprávu SCIM ListResponse

9. Pokud pokusy o připojení k aplikaci uspět, pak klikněte na tlačítko **Uložit** k uložení přihlašovacích údajů správce.
10. V **mapování** části, existují dvě sady vybrat mapování atributů: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každé z nich ke kontrole atributů, které se synchronizují ze služby Azure Active Directory do vaší aplikace. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatele a skupiny ve vaší aplikaci pro operace update. Vyberte tlačítko Uložit potvrďte změny.
11. V části **nastavení**, **oboru** pole určuje, kteří uživatelé nebo skupiny synchronizované. Výběr "Synchronizovat jenom přiřazené uživatele a skupiny" (doporučeno) bude synchronizovat jenom uživatelé a skupiny přiřazení v **uživatelů a skupin** kartu.
12. Po dokončení konfigurace se změnit **stavu zřizování** k **na**.
13. Klikněte na tlačítko **Uložit** spustit služba zřizování Azure AD. 
14. Je-li synchronizovat jenom přiřazené uživatele a skupiny (doporučeno), je potřeba vybrat možnost **uživatelů a skupin** kartu a přiřaďte uživatele a/nebo skupiny, kterou chcete synchronizovat.

Jakmile se počáteční synchronizace se spustila, můžete použít **protokoly auditu** kartu sledování průběhu, který zobrazuje všechny akce provedené v zřizovací služba ve vaší aplikaci. Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](check-status-user-account-provisioning.md).

V posledním kroku ověřování ukázka je k otevření souboru TargetFile.csv \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug složky na svém počítači s Windows. Po spuštění procesu zřizování tento soubor obsahuje podrobné informace o všech přiřazeny a zřízení uživatelů a skupin.

### <a name="development-libraries"></a>Knihovny pro vývoj
K vývoji vlastní webové služby, který odpovídá specifikaci SCIM, nejdřív seznámíte s následující knihovny poskytované společností Microsoft vám pomohou zrychlit proces vývoje: 

1. Společné jazykové infrastruktury (CLI) knihovny se nabízejí pro použití s jazyky na základě této infrastruktury, jako je C#. Deklaruje jednu z těchto knihoven Microsoft.SystemForCrossDomainIdentityManagement.Service, rozhraní, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, je znázorněno na následujícím obrázku. Vývojáři používat knihovny by implementovat rozhraní s třídou, která může být uvedené, obecně jako poskytovatel. Tyto knihovny umožňují vývojářům nasazení webové služby, který odpovídá specifikaci SCIM. Webová služba může být buď hostovaný v rámci Internetové informační službě nebo libovolného spustitelného sestavení rozhraní příkazového řádku. Požadavek je přeložit na volání metody zprostředkovatele, které by být naprogramovány vývojářem na provádět některé úložiště identit.
  
   ![][3]
  
2. [Express route obslužné rutiny](https://expressjs.com/guide/routing.html) jsou k dispozici pro analýzu node.js požadavek objektů představujících volání (jak jsou definovány ve specifikaci SCIM) provedené webových služeb node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Vytváření koncových bodů vlastního SCIM
Používání knihoven rozhraní příkazového řádku, vývojáře, kteří používají tyto knihovny hostování svých služeb v rámci jakékoli spustitelného sestavení rozhraní příkazového řádku nebo v rámci Internetové informační služby. Tady je ukázkový kód pro hostování služby v rámci spustitelného sestavení, na adrese http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Tato služba musí mít HTTP adresu a server ověřovací certifikát z nich kořenové certifikační autority je jedním z následujících názvů: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Ověřovací certifikát serverů může být vázaný na port na hostiteli Windows pomocí nástroje network shell: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Hodnota poskytnutá pro certhash argument tady, je kryptografický otisk certifikátu, zatímco hodnota poskytnutá pro appid argument je libovolný globálně jedinečný identifikátor.  

K hostování služby v rámci Internetové informační služby, by vývojář vytvářet sestavení knihovny kódu rozhraní příkazového řádku s třídou s názvem po spuštění ve výchozím oboru názvů sestavení.  Tady je příklad této třídy: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Ověřování koncových bodů zpracování
Požadavky z Azure Active Directory obsahovat nosného tokenu OAuth 2.0.   Jakoukoli službu přijetí požadavku by měl ověřit vystavitele jako Azure Active Directory jménem očekávanému tenantovi Azure Active Directory pro přístup k webové službě Azure Active Directory Graphu.  V tokenu, Vystavitel je identifikován deklaraci identity iss, stejně jako "jednotky ISS – překročené": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  V tomto příkladu základní adresa hodnotu deklarace identity https://sts.windows.net, identifikuje Azure Active Directory jako vystavitel, zatímco relativní adresy segmentu, cbb1a5ac f33b-45fa-9bf5-f37db0fed422, je jedinečný identifikátor tenanta Azure Active Directory na jménem, z nichž byl token vydán.  Pokud byl token vydán pro přístup k webové službě Azure Active Directory Graphu, musí být identifikátor této služby, 00000002-0000-0000-c000-000000000000, v hodnotu deklarace identity aud tokenu.  Všimněte si, že každý z aplikace, které jsou registrovány v jednom tenantovi se může zobrazit stejný `iss` deklarace identity pomocí SCIM žádostí.

Vývojáři, kteří používají knihovny rozhraní příkazového řádku, poskytovaných microsoftem pro vytváření služeb SCIM může ověřit požadavky z Azure Active Directory pomocí balíčku Microsoft.Owin.Security.ActiveDirectory pomocí následujících kroků: 

1. Ve zprostředkovateli implementace Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior vlastnosti tak, že se vrátí metoda má být volána při každém spuštění služby: 

   ```
     public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

2. Přidejte následující kód k této metodě mít každá žádost k některé z koncových bodů služby ověřen jako opatřené u tokenu vydaného službou Azure Active Directory jménem zadaného klienta, pro přístup k webové službě Azure AD Graph: 

   ```
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "00000002-0000-0000-c000-000000000000"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Zpracování zřizování a rušení zřízení uživatelů

1. Azure Active Directory dotáže služby pro uživatele s hodnotou atributu externalId odpovídající hodnota atributu mailNickname uživatele ve službě Azure AD. Dotaz je vyjádřena jako žádost protokolu HTTP (Hypertext Transfer), jako je tento příklad, ve které jyoung je ukázka mailNickname uživatele v Azure Active Directory.

    >[!NOTE]
    > Toto je pouze příklad. Ne všichni uživatelé budou mít atributu mailNickname a hodnota, kterou má uživatel nemusí být jedinečný v adresáři. Kromě toho je možné konfigurovat v atribut použije k porovnání (v tomto případě je to externalId) [mapování atributů Azure AD](customize-application-attributes.md).

  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  Pokud služba je vytvořená pomocí rozhraní příkazového řádku knihovny poskytované společností Microsoft pro implementaci služeb SCIM, požadavek přeložit na volání metody dotazu poskytovatele.  Tady je signatura této metody: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  Tady je definice Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters rozhraní: 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   If the service was built using the Common Language Infrastructure libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider.  Here is the signature of that method: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Here is the definition of the Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters interface: 

   ```
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   In the following sample of a query for a user with a given value for the externalId attribute, values of the arguments passed to the Query method are: 
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

2. If the response to a query to the web service for a user with an externalId attribute value that matches the mailNickname attribute value of a user does not return any users, then Azure Active Directory requests that the service provision a user corresponding to the one in Azure Active Directory.  Here is an example of such a request: 

  ````
    Povolení protokolu HTTP/1.1 https://.../scim/Users příspěvek: Nosiče...  Typ obsahu: application/scim + json {"schémata": ["urn: ietf:params:scim:schemas:core:2.0:User", "urn: ietf:params:scim:schemas:extension:enterprise:2.0User"], "externalId": "jyoung", "userName": "jyoung", "aktivní": true, "adresy": null,    "displayName": "Radosti a velkou Young", "e-mailů": [{"type": "pracovní", "value": "jyoung@Contoso.com", "primární": true}], "meta": {"resourceType": "User"}, "název": {"familyName": "Malé", "jméno": "Radosti a velkou"}, "phoneNumbers": null, "preferredLa nguage": null,"title": null,"oddělení": null,"správce": null}
  ````
  The CLI libraries provided by Microsoft for implementing SCIM services would translate that request into a call to the Create method of the service’s provider.  The Create method has this signature: 
  ````
    System.Threading.Tasks.Tasks je definována v knihovně mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.Resource je definována v / / Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task < Microsoft.SystemForCrossDomainIdentityManagement.Resource > vytvořit (Microsoft.SystemForCrossDomainIdentityManagement.Resource prostředků, řetězec correlationIdentifier);
  ````
  In a request to provision a user, the value of the resource argument is an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, defined in the Microsoft.SystemForCrossDomainIdentityManagement.Schemas library.  If the request to provision the user succeeds, then the implementation of the method is expected to return an instance of the Microsoft.SystemForCrossDomainIdentityManagement. Core2EnterpriseUser class, with the value of the Identifier property set to the unique identifier of the newly provisioned user.  

3. To update a user known to exist in an identity store fronted by an SCIM, Azure Active Directory proceeds by requesting the current state of that user from the service with a request such as: 
  ````
    ZÍSKÁTE autorizační ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1: Nosiče...
  ````
  In a service built using the CLI libraries provided by Microsoft for implementing SCIM services, the request is translated into a call to the Retrieve method of the service’s provider.  Here is the signature of the Retrieve method: 
  ````
    System.Threading.Tasks.Tasks je definována v knihovně mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.Resource a / nebo Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters / / jsou definovány v Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task < Microsoft.SystemForCrossDomainIdentityManagement.Resource > načtení (Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters parametry, řetězec correlationIdentifier);

    veřejné rozhraní Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters {Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier ResourceIdentifier {get;}} veřejného rozhraní Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier {řetězec identifikátoru {get; nastavte;} řetězec Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier {get; nastavte;}}
  ````
  In the example of a request to retrieve the current state of a user, the values of the properties of the object provided as the value of the parameters argument are as follows: 
  
   * Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. If a reference attribute is to be updated, then Azure Active Directory queries the service to determine whether or not the current value of the reference attribute in the identity store fronted by the service already matches the value of that attribute in Azure Active Directory. For users, the only attribute of which the current value is queried in this way is the manager attribute. Here is an example of a request to determine whether the manager attribute of a particular user object currently has a certain value: 

  If the service was built using the CLI libraries provided by Microsoft for implementing SCIM services, then the request is translated into a call to the Query method of the service’s provider. The value of the properties of the object provided as the value of the parameters argument are as follows: 
  
  * parameters.AlternateFilters.Count: 2
  * parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
  * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
  * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
  * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * parameters.RequestedAttributePaths.ElementAt(0): "ID"
  * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Here, the value of the index x may be 0 and the value of the index y may be 1, or the value of x may be 1 and the value of y may be 0, depending on the order of the expressions of the filter query parameter.   

5. Here is an example of a request from Azure Active Directory to an SCIM service to update a user: 
  ````
    Povolení protokolu HTTP/1.1 ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 opravy: Nosiče...  Typ obsahu: application/scim + json {"schémata": ["urn: ietf:params:scim:api:messages:2.0:PatchOp"], "Operace": [{"op": "Přidání", "cesty": "správce", "value": [{"$ref": "http://.../scim/Users/2819c223-7f76-453a-919d-413861904646", "value": "2819c223-7f76-453a-919d-413861904646"}]}]}
  ````
  The Microsoft CLI libraries for implementing SCIM services would translate the request into a call to the Update method of the service’s provider. Here is the signature of the Update method: 
  ````
    System.Threading.Tasks.Tasks a / nebo System.Collections.Generic.IReadOnlyCollection<T> / / jsou definovány v knihovně mscorlib.dll.  
    Microsoft.SystemForCrossDomainIdentityManagement.IPatch, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, / / Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, / / Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, / / Microsoft.SystemForCrossDomainIdentityManagement.OperationName, / / Microsoft.SystemForCrossDomainIdentityManagement.IPath a / nebo Microsoft.SystemForCrossDomainIdentityManagement.OperationValue / / jsou všechny definované v Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task aktualizace (Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, řetězec correlationIdentifier);

    veřejné rozhraní Microsoft.SystemForCrossDomainIdentityManagement.IPatch {Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase PatchRequest {get; nastavte;} Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier ResourceIdentifier {get; nastavte;}        
    }

    Veřejná třída PatchRequest2:    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase {public System.Collections.Generic.IReadOnlyCollection < Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation > operace {get;}


   Pokud služba je vytvořená pomocí knihovny Common Language Infrastructure pro implementaci SCIM služeb poskytovaných microsoftem, požadavek přeložit na volání metody dotazu poskytovatele. Hodnota vlastnosti objektu, který je uvedený jako hodnota argumentu parametry jsou následující: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: ID
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
   * Parametry. AlternateFilters.ElementAt(y). AttributePath: "správce"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): ID
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   Tady hodnota indexu x může být 0 a hodnota y indexu může být 1, nebo hodnota x může být 1 a hodnota y může být 0, v závislosti na pořadí výrazy parametr dotazu filter.   

5. Tady je příklad požadavku z Azure Active Directory na službu SCIM provést aktualizaci uživatele: 

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

   Knihovny Microsoft Common Language Infrastructure pro implementaci služeb SCIM převedla žádost do volání metody aktualizace poskytovatele služby. Následuje podpis metody Update: 

   ```
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    V tomto příkladu požadavek na aktualizaci uživatele má zadaný objekt jako hodnota argumentu oprava hodnoty těchto vlastností: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest jako PatchRequest2). Operations.Count: 1
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Název operace: OperationName.Add
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "správce"
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.Count: 1
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referenční dokumentace: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Hodnota: 2819c223-7f76-453a-919d-413861904646

6. Na zrušení zřízení uživatele z identity úložiště přední stěnou služba SCIM, Azure AD, jako odešle žádost: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Pokud služba je vytvořená pomocí knihovny Common Language Infrastructure pro implementaci SCIM služeb poskytovaných microsoftem, je požadavek přeložit na volání metody Delete poskytovatele služby.   Tato metoda má tento podpis: 

   ```
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   Objekt zadaný jako hodnota argumentu resourceIdentifier má tyto hodnoty vlastností v příkladu požadavek na zrušení zřízení uživatele: 

6. Na zrušení zřízení uživatele z identity úložiště přední stěnou služba SCIM, Azure AD, jako odešle žádost: 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Pokud služba je vytvořená pomocí rozhraní příkazového řádku knihovny pro implementaci SCIM služeb poskytovaných microsoftem, je požadavek přeložit na volání metody Delete poskytovatele služby.   Tato metoda má tento podpis: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  Objekt zadaný jako hodnota argumentu resourceIdentifier má tyto hodnoty vlastností v příkladu požadavek na zrušení zřízení uživatele: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Referenční dokumentace schématu uživatelů a skupin
Azure Active Directory můžete zřídit dva typy prostředků, aby SCIM webové služby.  Tyto typy prostředků jsou uživatelé a skupiny.  

Prostředky uživatelů jsou označeny identifikátor schématu `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, která je zahrnutá v téhle specifikaci protokolu: https://tools.ietf.org/html/rfc7643.  Výchozí mapování atributů uživatelů ve službě Azure Active Directory pro atributy prostředky uživatelů najdete v následující tabulce 1.  

Prostředky skupiny prostředků jsou označeny identifikátor schématu `urn:ietf:params:scim:schemas:core:2.0:Group`.  Tabulka 2 níže ukazuje výchozí mapování atributů skupin v Azure Active Directory na atributy skupiny prostředků.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabulka 1: Výchozí mapování atributů uživatele
| Uživatele Azure Active Directory | "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |aktivní |
| displayName |displayName |
| Ve TelephoneNumber |phoneNumbers [typ eq "fax"] .value |
| givenName |name.givenName |
| pracovní funkce |název |
| e-mailu |e-mailů [typ eq "pracovní"] .value |
| mailNickname |externalId |
| manažer |manažer |
| Mobilní zařízení |phoneNumbers [eq typ "mobilní"] .value |
| ID objektu |ID |
| PSČ |.postalCode adresy [typ eq "pracovní"] |
| Adresy proxy serveru |e-mailů [Zadejte eq "other"]. Hodnota |
| fyzický Delivery-OfficeName |adresy [Zadejte eq "other"]. Ve formátu |
| streetAddress |.streetAddress adresy [typ eq "pracovní"] |
| Příjmení |name.familyName |
| telefonní číslo |phoneNumbers [typ eq "pracovní"] .value |
| user-PrincipalName |uživatelské jméno |

### <a name="table-2-default-group-attribute-mapping"></a>Tabulka 2: Výchozí skupiny atributů mapování
| Skupina Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| e-mailu |e-mailů [typ eq "pracovní"] .value |
| mailNickname |displayName |
| členové |členové |
| ID objektu |ID |
| proxyAddresses |e-mailů [Zadejte eq "other"]. Hodnota |


## <a name="related-articles"></a>Související články
* [Automatizace uživatele zřizování a jeho rušení pro aplikace SaaS](user-provisioning.md)
* [Přizpůsobení mapování atributů pro zřizování uživatelů](customize-application-attributes.md)
* [Zápis výrazů pro mapování atributů](functions-for-customizing-application-data.md)
* [Filtry oborů pro zřizování uživatelů](define-conditional-rules-for-provisioning-user-accounts.md)
* [Oznámení o zřizování účtů](user-provisioning.md)
* [Seznam kurzů o integraci aplikací SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png

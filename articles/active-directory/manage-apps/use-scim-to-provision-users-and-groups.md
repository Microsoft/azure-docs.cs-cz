---
title: Automatizace zřizování aplikací pomocí SCIM ve službě Azure Active Directory | Dokumentace Microsoftu
description: Azure Active Directory můžete automaticky zřizovat uživatele a skupiny, které se aplikace nebo identity úložiště, který je přední stěnou webová služba s rozhraním definovaných ve specifikaci protokolu SCIM
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: b135838558a493cff0e28a8429d31f5a03a69857
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033458"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Automatické zřizování uživatelů a skupin ze služby Azure Active Directory do aplikací pomocí systému pro mezi doménami Identity Management (SCIM)

SCIM je standardizovaný protokol a schéma, které se zaměřuje na zajištění větší konzistence v tom, jak jsou identity spravované napříč systémy. Když aplikace podporuje koncový bod SCIM pro správu uživatelů, služba zřizování uživatelů Azure AD může posílat žádosti o vytvoření, úpravu nebo odstranění přiřazených uživatelů a skupin do tohoto koncového bodu.

Mnohé z aplikací, pro které Azure AD podporuje [předem integrované Automatické zřizování uživatelů](../saas-apps/tutorial-list.md) , implementují SCIM jako způsob přijímání oznámení o změnách uživatelů.  Kromě toho můžou zákazníci připojit aplikace, které podporují konkrétní profil [specifikace protokolu SCIM 2,0](https://tools.ietf.org/html/rfc7644) , pomocí Obecné možnosti integrace "mimo Galerie" v Azure Portal.

Hlavní fokus tohoto článku se nachází v profilu SCIM 2,0, který Azure AD implementuje jako součást svého generického SCIM konektoru pro aplikace mimo galerii. Úspěšné testování aplikace, která podporuje SCIM s obecným konektorem Azure AD, je ale krok k získání aplikace uvedené v galerii Azure AD jako podpora zřizování uživatelů. Další informace o tom, jak se vaše aplikace zobrazuje v galerii aplikací Azure AD, [najdete v tématu Postup: Seznamte aplikaci v galerii](../develop/howto-app-gallery-listing.md)aplikací Azure AD.

> [!IMPORTANT]
> Chování implementace Azure AD SCIM se naposledy aktualizovalo 18. prosince 2018. Informace o tom, co se změnilo, najdete v článku [dodržování předpisů protokolu SCIM 2,0 ve službě Azure AD pro zřizování uživatelů](application-provisioning-config-problem-scim-compatibility.md).

![Ukazuje zřizování z Azure AD do úložiště aplikace nebo identity.][0]<br/>
*Obrázek 1: Zřizování z Azure Active Directory do úložiště aplikací nebo identit, které implementuje SCIM*

Tento článek je rozdělen do čtyř oddílů:

* **[Zřizování uživatelů a skupin pro aplikace třetích stran, které podporují SCIM 2,0](#provisioning-users-and-groups-to-applications-that-support-scim)** – Pokud vaše organizace používá aplikaci třetí strany, která implementuje profil SCIM 2,0, který Azure AD podporuje, můžete začít automatizovat zřizování i zrušení zřizování uživatelů a skupin dnes.
* **[Porozumění implementaci Azure AD SCIM](#understanding-the-azure-ad-scim-implementation)** – Pokud vytváříte aplikaci, která podporuje rozhraní API pro správu uživatelů SCIM 2,0, Tato část podrobně popisuje, jak se implementuje klient Azure AD SCIM a jak byste měli model SCIMovat. zpracování požadavků a odpovědí.
* **[Vytvoření koncového bodu SCIM pomocí knihoven Microsoft CLI](#building-a-scim-endpoint-using-microsoft-cli-libraries)** library – Common Language Infrastructure (CLI) spolu s ukázkami kódu ukazují, jak vyvíjet koncový bod SCIM a překládat zprávy SCIM.  
* **[Odkaz na schéma uživatelů a skupin](#user-and-group-schema-reference)** – popisuje schéma uživatelů a skupin podporované implementací Azure AD SCIM pro aplikace mimo galerii.

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Zřizování uživatelů a skupin a aplikace s podporou SCIM

Služba Azure AD se dá nakonfigurovat tak, aby automaticky zřídila přiřazené uživatele a skupiny aplikacím, které implementují konkrétní profil [protokolu SCIM 2,0](https://tools.ietf.org/html/rfc7644). Konkrétní informace o profilu najdete v dokumentaci [implementace Azure AD SCIM](#understanding-the-azure-ad-scim-implementation).

Obraťte se na svého poskytovatele aplikace nebo dokumentaci poskytovatele aplikace pro příkazy kompatibilitu s těmito požadavky.

> [!IMPORTANT]
> Implementace Azure AD SCIM je postavená na službě Azure AD Provisioning Service, která je navržená tak, aby neustále udržovala synchronizaci mezi službou Azure AD a cílovou aplikací a implementovala velmi specifickou sadu standardních operací. Je důležité porozumět tomuto chování, abyste pochopili chování klienta Azure AD SCIM. Další informace najdete v tématu [co se stane při zřizování uživatelů](user-provisioning.md#what-happens-during-provisioning).

### <a name="getting-started"></a>Začínáme

Aplikace s podporou SCIM profilu je popsáno v tomto článku můžou být připojen k Azure Active Directory pomocí funkce "aplikaci mimo Galerii" v galerii aplikací Azure AD. Jakmile budete připojeni, Azure AD spustí proces synchronizace každých 40 minut, kde dotazuje aplikace SCIM koncový bod pro přiřazení uživatelé a skupiny a vytvoří nebo upraví podle podrobnosti o přiřazení.

**Připojení aplikace, která podporuje SCIM:**

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com). 
1. V levém podokně vyberte **podnikové aplikace** . Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací přidaných z galerie.
1. Vyberte **+ Nová aplikace** > **všechny** > **aplikace mimo galerii**.
1. Zadejte název vaší aplikace a vyberte **Přidat** a vytvořte objekt aplikace. Nová aplikace se přidá do seznamu podnikových aplikací a otevře se na obrazovce správy aplikací.

   ![Snímek obrazovky se zobrazením Galerie aplikací Azure AD][1]<br/>
   *Obrázek 2: Galerie aplikací Azure AD*

1. Na obrazovce Správa aplikací vyberte na levém panelu možnost **zřizování** .
1. V **režim zřizování** nabídce vyberte možnost **automatické**.

   ![Příklad: Stránka zřizování aplikace v Azure Portal][2]<br/>
   *Obrázek 3: Konfigurace zřizování v Azure Portal*

1. V **adresy URL Tenanta** zadejte adresu URL koncového bodu SCIM vaší aplikace. Příklad: https://api.contoso.com/scim/
1. Pokud koncový bod SCIM vyžaduje tokenu nosiče OAuth z vystavitele než Azure AD, zkopírujte požadované tokenu nosiče OAuth nepovinný **tajný klíč tokenu** pole. Pokud toto pole zůstane prázdné, Azure AD zahrnuje token nosiče OAuth vydaný z Azure AD s každým požadavkem. Aplikace, které používají Azure AD jako zprostředkovatel identity, můžou ověřit tento token vydaný službou Azure AD.
1. Vyberte **Test připojení** , aby se Azure Active Directory pokus o připojení ke koncovému bodu SCIM. Pokud se pokus nezdaří, zobrazí se informace o chybě.  

    > [!NOTE]
    > **Test Connection** se dotazuje na koncový bod SCIM pro uživatele, který neexistuje, pomocí NÁHODNÉho identifikátoru GUID jako odpovídající vlastnosti vybrané v konfiguraci služby Azure AD. Očekávaná správná odpověď je HTTP 200 OK s prázdnou SCIM zprávou ListResponse.

1. Pokud se pokusí připojit k aplikaci úspěšně, vyberte **Uložit** a uložte přihlašovací údaje správce.
1. V **mapování** části, existují dvě sady vybrat mapování atributů: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každé z nich ke kontrole atributů, které se synchronizují ze služby Azure Active Directory do vaší aplikace. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatele a skupiny ve vaší aplikaci pro operace update. Vyberte **Uložit** a potvrďte všechny změny.

    > [!NOTE]
    > Volitelně můžete zakázat synchronizaci objektů skupiny zakázáním "groups" mapování.

1. V části **Nastavení**pole **obor** určuje, kteří uživatelé a skupiny budou synchronizováni. Vyberte možnost **synchronizovat pouze přiřazené uživatele a skupiny** (doporučeno), chcete-li synchronizovat pouze uživatele a skupiny přiřazené na kartě **Uživatelé a skupiny** .
1. Po dokončení konfigurace nastavte **stav zřizování** **na zapnuto**.
1. Výběrem **Uložit** spusťte službu Azure AD Provisioning.
1. Pokud synchronizujete pouze přiřazené uživatele a skupiny (doporučeno), nezapomeňte vybrat kartu **Uživatelé a skupiny** a přiřadit uživatele nebo skupiny, které chcete synchronizovat.

Po zahájení počátečního cyklu můžete vybrat **protokoly auditu** na levém panelu a monitorovat průběh, který zobrazuje všechny akce prováděné službou zřizování ve vaší aplikaci. Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](check-status-user-account-provisioning.md).

> [!NOTE]
> Počáteční cyklus trvá déle než pozdější synchronizace, ke kterým dochází přibližně každých 40 minut, pokud je služba spuštěná.

## <a name="understanding-the-azure-ad-scim-implementation"></a>Principy implementace Azure AD SCIM

Pokud vytváříte aplikaci, která podporuje rozhraní API pro správu uživatelů SCIM 2,0, v této části najdete podrobný popis způsobu implementace klienta Azure AD SCIM a způsobu modelování požadavků na zpracování a odpovědi protokolu SCIM. Po implementaci koncového bodu SCIM ho můžete otestovat pomocí postupu popsaného v předchozí části.

V rámci [specifikace protokolu SCIM 2,0](http://www.simplecloud.info/#Specification)musí vaše aplikace splňovat tyto požadavky:

* Podporuje vytváření uživatelů a volitelně také skupiny podle oddílu [3,3 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Podporuje úpravu uživatelů nebo skupin s požadavky na opravy podle [oddílu 3.5.2 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Podporuje načítání známého prostředku pro uživatele nebo skupinu vytvořené dříve, podle [oddílu 3.4.1 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Podporuje dotazování uživatelů nebo skupin podle části [3.4.2 protokolu SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  Ve výchozím nastavení `id` jsou uživatelé načítáni pomocí a dotazováni jejich `username` a `externalid`a skupiny jsou dotazovány nástrojem `displayName`.  
* Podporuje dotazování uživatele podle ID a podle manažera podle části 3.4.2 protokolu SCIM.  
* Podporuje dotazování skupin podle ID a členu podle části 3.4.2 protokolu SCIM.  
* Přijme jeden nosný token pro ověřování a autorizaci služby Azure AD pro vaši aplikaci.

Při implementaci SCIM koncového bodu, který zajistí kompatibilitu s Azure AD, postupujte podle těchto obecných pokynů:

* `id`je požadovaná vlastnost pro všechny prostředky. Každá odpověď, která vrací prostředek, by měla mít jistotu, že každý prostředek má `ListResponse` tuto vlastnost, s výjimkou členů s nulovými členy.
* Odpověď na požadavek na dotaz nebo filtr by měla být vždy `ListResponse`typu.
* Skupiny jsou volitelné, ale podporují se jenom v případě, že implementace SCIM podporuje žádosti o opravu.
* V reakci na opravu není nutné zahrnout celý prostředek.
* Microsoft Azure AD používá pouze následující operátory:  
     - `eq`
     - `and`
* Pro strukturální prvky v SCIM nemusíte rozlišovat velká a malá písmena, zejména hodnoty `op` operace opravy, jak je definováno https://tools.ietf.org/html/rfc7644#section-3.5.2 v. Azure AD vygeneruje hodnoty "op" jako `Add`, `Replace`a `Remove`.
* Microsoft Azure AD vytváří požadavky na načtení náhodného uživatele a skupiny, aby bylo zajištěno, že koncový bod a přihlašovací údaje jsou platné. Také se provádí jako součást toku **testu připojení** v [Azure Portal](https://portal.azure.com). 
* Atribut, na kterém mohou být prostředky odesílány, by měl být nastaven jako odpovídající atribut v aplikaci v [Azure Portal](https://portal.azure.com). Další informace najdete v tématu [Přizpůsobení mapování atributů zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings) .

### <a name="user-provisioning-and-de-provisioning"></a>Zřizování uživatelů a jeho rušení

Následující ilustrace znázorňuje zprávy, které Azure Active Directory posílá službě SCIM ke správě životního cyklu uživatele v úložišti identit vaší aplikace.  

![Zobrazuje pořadí zřizování a zrušení zřizování uživatelů.][4]<br/>
*Obrázek 4: Pořadí zřizování a zrušení zřizování uživatelů*

### <a name="group-provisioning-and-de-provisioning"></a>Skupinové zřizování a zrušení zřizování.

Zřizování skupin a zrušení zřizování jsou volitelné. Když se implementuje a povolí, na následujícím obrázku se zobrazí zprávy, které služba Azure AD pošle službě SCIM ke správě životního cyklu skupiny v úložišti identit vaší aplikace.  Tyto zprávy se liší od zpráv o uživatelích dvěma způsoby:

* Požadavky na načtení skupin určují, že atribut Members se má vyloučit z libovolného prostředku poskytnutého v reakci na požadavek.  
* Žádosti slouží k určení, zda má atribut typu odkaz určitou hodnotu se žádostí o členů atributu.  

![Zobrazuje pořadí zřizování a rušení skupin.][5]<br/>
*Obrázek 5: Pořadí zřizování skupin a zrušení zřizování*

### <a name="scim-protocol-requests-and-responses"></a>Požadavky a odpovědi protokolu SCIM
V této části najdete příklady požadavků SCIM vygenerovaných klientem Azure AD SCIM a příklady očekávaných odpovědí. Pro dosažení nejlepších výsledků byste měli kód aplikace, aby zpracovávala tyto požadavky v tomto formátu, a generovat očekávané odpovědi.

> [!IMPORTANT]
> Pokud chcete zjistit, jak a kdy služba zřizování uživatelů Azure AD vysílá níže popsané operace, Projděte si téma [co se stane při zřizování uživatelů?](user-provisioning.md#what-happens-during-provisioning).

- [Uživatelské operace](#user-operations)
  - [Vytvoření uživatele](#create-user)
    - [Požadavek](#request)
    - [Odpověď](#response)
  - [Získání uživatele](#get-user)
    - [Požadavek](#request-1)
    - [Odpověď](#response-1)
  - [Získat uživatele podle dotazu](#get-user-by-query)
    - [Požadavek](#request-2)
    - [Odpověď](#response-2)
  - [Získat uživatele podle dotazů – žádné výsledky](#get-user-by-query---zero-results)
    - [Požadavek](#request-3)
    - [Odpověď](#response-3)
  - [Aktualizace uživatele [vlastnosti s více hodnotami]](#update-user-multi-valued-properties)
    - [Požadavek](#request-4)
    - [Odpověď](#response-4)
  - [Aktualizace uživatele [vlastnosti s jednou hodnotou]](#update-user-single-valued-properties)
    - [Požadavek](#request-5)
    - [Odpověď](#response-5)
  - [Odstranění uživatele](#delete-user)
    - [Požadavek](#request-6)
    - [Odpověď](#response-6)
- [Operace skupiny](#group-operations)
  - [Vytvoření skupiny](#create-group)
    - [Požadavek](#request-7)
    - [Odpověď](#response-7)
  - [Získání skupiny](#get-group)
    - [Požadavek](#request-8)
    - [Odpověď](#response-8)
  - [Získat Group by DisplayName](#get-group-by-displayname)
    - [Požadavek](#request-9)
    - [Odpověď](#response-9)
  - [Aktualizovat skupinu [atributy nečlenské]](#update-group-non-member-attributes)
    - [Požadavek](#request-10)
    - [Odpověď](#response-10)
  - [Aktualizace skupiny [přidat členy]](#update-group-add-members)
    - [Požadavek](#request-11)
    - [Odpověď](#response-11)
  - [Skupina aktualizací [odebrat členy]](#update-group-remove-members)
    - [Požadavek](#request-12)
    - [Odpověď](#response-12)
  - [Odstranění skupiny](#delete-group)
    - [Požadavek](#request-13)
    - [Odpověď](#response-13)

### <a name="user-operations"></a>Uživatelské operace

* Na uživatele lze zadat dotaz pomocí `userName` atributů `email[type eq "work"]` nebo.  

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
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Základě
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

#### <a name="get-user-by-query"></a>Získat uživatele podle dotazu

##### <a name="request-2"></a>Request

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

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

#### <a name="delete-user"></a>Odstranit uživatele

##### <a name="request-6"></a>Request

*Odstranit/Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Základě

*HTTP/1.1 204 bez obsahu*

### <a name="group-operations"></a>Operace skupiny

* Skupiny se vždy vytvoří s prázdným seznamem členů.
* Do `displayName` skupin lze zadat dotaz pomocí atributu.
* Aktualizace žádosti o opravu skupiny by měla v odpovědi vracet *HTTP 204 bez obsahu* . Vrácení textu se seznamem všech členů není vhodné.
* Není nutné podporovat vrácení všech členů skupiny.

#### <a name="create-group"></a>Vytvořit skupinu

##### <a name="request-7"></a>Request

*POST/Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "members": [],
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

#### <a name="delete-group"></a>Odstranit skupinu

##### <a name="request-13"></a>Request

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Základě

*HTTP/1.1 204 bez obsahu*

## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Vytvoření koncového bodu SCIM pomocí knihoven Microsoft CLI

Vytvořením webové služby SCIM, která používá rozhraní s Azure Active Directory, můžete povolit Automatické zřizování uživatelů pro prakticky jakékoli aplikace nebo úložiště identit.

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
* [Předplatné Azure s verzí zkušební verzi nebo s licencí Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Začínáme

Nejjednodušší způsob, jak implementovat SCIM koncový bod, který může přijímat žádosti o zřízení ze služby Azure AD je k sestavení a nasazení vzorového kódu, jejichž výstupem jsou zřízené uživatele do souboru hodnot oddělených čárkami (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Vytvoření ukázkového koncového bodu SCIM

1. Stáhněte si balíček ukázky kódu v [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Rozbalte balíček a umístěte ji na svém počítači s Windows do umístění, jako je například C:\AzureAD-BYOA-Provisioning-Samples\.
1. V této složce spusťte FileProvisioning\Host\FileProvisioningService.csproj projektu v sadě Visual Studio.
1. Vyberte **nástroje** > **správce**balíčků NuGet konzola správce balíčků a spusťte následující příkazy pro projekt FileProvisioningService, abyste vyřešili odkazy na řešení: > 

   ```powershell
    Update-Package -Reinstall
   ```

1. FileProvisioningService projekt sestavte.
1. Spuštění aplikace příkazového řádku ve Windows (jako správce) a použít **cd** příkazu změňte adresář na vaše **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**složky.
1. Spusťte následující příkaz, kterým nahraďte `<ip-address>` IP adresu nebo název domény počítače s Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. V systému Windows v části >  **nastavení systému Windows** **Síťová & nastavení Internetu**vyberte**Upřesnit nastavení** **brány Windows Firewall** > a vytvořte **příchozí pravidlo** , které povolí příchozí přístup k portu. 9000.
1. Pokud je počítač s Windows za směrovačem, je potřeba nakonfigurovat směrovač tak, aby spouštěl překlad přístupu k síti mezi jeho port 9000, který je zpřístupněný pro Internet, a port 9000 na počítači s Windows. Tato konfigurace je vyžadována pro Azure AD pro přístup k tomuto koncovému bodu v cloudu.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Registrace ukázkového koncového bodu SCIM ve službě Azure AD

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com). 
1. V levém podokně vyberte **podnikové aplikace** . Zobrazí se seznam všech nakonfigurovaných aplikací, včetně aplikací přidaných z galerie.
1. Vyberte **+ Nová aplikace** > **všechny** > **aplikace mimo galerii**.
1. Zadejte název vaší aplikace a vyberte **Přidat** a vytvořte objekt aplikace. Objekt aplikace vytvořené slouží k reprezentaci cílové aplikace, můžete by zřizování a implementaci jednotného přihlašování pro a nejen SCIM koncový bod.
1. Na obrazovce Správa aplikací vyberte na levém panelu možnost **zřizování** .
1. V **režim zřizování** nabídce vyberte možnost **automatické**.    
1. V **adresy URL Tenanta** zadejte adresu URL koncového bodu SCIM vaší aplikace. Příklad: https://api.contoso.com/scim/

1. Pokud koncový bod SCIM vyžaduje tokenu nosiče OAuth z vystavitele než Azure AD, zkopírujte požadované tokenu nosiče OAuth nepovinný **tajný klíč tokenu** pole. Pokud toto pole zůstane prázdné, Azure AD zahrnuje token nosiče OAuth vydaný z Azure AD s každým požadavkem. Aplikace, které používají Azure AD jako zprostředkovatel identity, můžou ověřit tento token vydaný službou Azure AD.
1. Vyberte **Test připojení** , aby se Azure Active Directory pokus o připojení ke koncovému bodu SCIM. Pokud se pokus nezdaří, zobrazí se informace o chybě.  

    > [!NOTE]
    > **Test Connection** se dotazuje na koncový bod SCIM pro uživatele, který neexistuje, pomocí NÁHODNÉho identifikátoru GUID jako odpovídající vlastnosti vybrané v konfiguraci služby Azure AD. Očekávaná správná odpověď je HTTP 200 OK s prázdnou SCIM zprávou ListResponse

1. Pokud se pokusí připojit k aplikaci úspěšně, vyberte **Uložit** a uložte přihlašovací údaje správce.
1. V **mapování** části, existují dvě sady vybrat mapování atributů: jeden pro uživatelské objekty a jeden pro objekty skupiny. Vyberte každé z nich ke kontrole atributů, které se synchronizují ze služby Azure Active Directory do vaší aplikace. Atributy vybrané jako **odpovídající** vlastnosti se používají tak, aby odpovídaly uživatele a skupiny ve vaší aplikaci pro operace update. Vyberte **Uložit** a potvrďte všechny změny.
1. V části **nastavení**, **oboru** pole určuje, kteří uživatelé nebo skupiny synchronizované. Vyberte možnost **synchronizovat pouze přiřazené uživatele a skupiny** (doporučeno), chcete-li synchronizovat pouze uživatele a skupiny přiřazené na kartě **Uživatelé a skupiny** .
1. Po dokončení konfigurace nastavte **stav zřizování** **na zapnuto**.
1. Výběrem **Uložit** spusťte službu Azure AD Provisioning.
1. Pokud synchronizujete pouze přiřazené uživatele a skupiny (doporučeno), nezapomeňte vybrat kartu **Uživatelé a skupiny** a přiřadit uživatele nebo skupiny, které chcete synchronizovat.

Po zahájení počátečního cyklu můžete vybrat **protokoly auditu** na levém panelu a monitorovat průběh, který zobrazuje všechny akce prováděné službou zřizování ve vaší aplikaci. Další informace o tom, jak číst zřizování protokoly Azure AD najdete v tématu [hlášení o zřizování automatické uživatelských účtů](check-status-user-account-provisioning.md).

V posledním kroku ověřování ukázka je k otevření souboru TargetFile.csv \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug složky na svém počítači s Windows. Po spuštění procesu zřizování tento soubor obsahuje podrobné informace o všech přiřazeny a zřízení uživatelů a skupin.

### <a name="development-libraries"></a>Knihovny pro vývoj

K vývoji vlastní webové služby, který odpovídá specifikaci SCIM, nejdřív seznámíte s následující knihovny poskytované společností Microsoft vám pomohou zrychlit proces vývoje:

* Společné jazykové infrastruktury (CLI) knihovny se nabízejí pro použití s jazyky na základě této infrastruktury, jako je C#. Jedna z těchto knihoven, Microsoft. SystemForCrossDomainIdentityManagement. Service, deklaruje rozhraní, Microsoft. SystemForCrossDomainIdentityManagement. IProvider, jak je znázorněno na následujícím obrázku. Vývojář používající knihovny by implementoval toto rozhraní se třídou, která může být obecně označována jako poskytovatel. Knihovny umožňují vývojáři nasadit webovou službu, která odpovídá specifikaci SCIM. Webová služba může být buď hostovaná v rámci Internetová informační služba, nebo jakékoli spustitelné sestavení CLI. Požadavek je přeložit na volání metody zprostředkovatele, které by být naprogramovány vývojářem na provádět některé úložiště identit.
  
   ![Členěn Požadavek přeložený do volání metod poskytovatele][3]
  
* [Express route obslužné rutiny](https://expressjs.com/guide/routing.html) jsou k dispozici pro analýzu node.js požadavek objektů představujících volání (jak jsou definovány ve specifikaci SCIM) provedené webových služeb node.js.

### <a name="building-a-custom-scim-endpoint"></a>Vytvoření vlastního koncového bodu SCIM

Vývojáři, kteří používají knihovny CLI, mohou hostovat své služby v jakémkoli spustitelném sestavení CLI nebo v rámci Internetová informační služba. Tady je ukázkový kód pro hostování služby v rámci spustitelného sestavení, na adrese http://localhost:9000: 

   ```csharp
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
   ```

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

Pro hostování služby v rámci Internetová informační služba by vývojář vytvořil sestavení knihovny kódu CLI s třídou s názvem Startuping ve výchozím oboru názvů sestavení.  Tady je příklad této třídy: 

   ```csharp
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
   ```

### <a name="handling-endpoint-authentication"></a>Ověřování koncových bodů zpracování

Požadavky z Azure Active Directory obsahovat nosného tokenu OAuth 2.0.   Každá služba, která požadavek obdrží, by měla ověřit vystavitele, který je Azure Active Directory pro očekávaného Azure Active Directoryho tenanta, pro přístup k webové službě Azure Active Directory Graph.  V tokenu je Vystavitel identifikovaný deklarací ISS, například "ISS": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/ ".  V tomto příkladu základní adresa hodnoty https://sts.windows.net deklarace identity identifikuje Azure Active Directory jako Vystavitel, zatímco relativní segment adres cbb1a5ac-f33b-45fa-9BF5-f37db0fed422 je jedinečným identifikátorem Azure Active Directory tenanta pro. který token byl vydán. Cílovou skupinou pro token bude ID šablony aplikace pro aplikaci v galerii. ID šablony aplikace pro všechny vlastní aplikace je 8adf8e6e-67b2-4cf2-a259-e3dc5476c621. ID šablony aplikace pro každou aplikaci v galerii se liší. Kontaktujte ProvisioningFeedback@microsoft.com prosím otázky pro ID šablony aplikace pro aplikaci Galerie. Každá aplikace registrovaná v jednom tenantovi může obdržet stejnou `iss` deklaraci identity s požadavky SCIM.

Vývojáři, kteří pomocí knihoven CLI poskytovaných Microsoftem vytvářejí službu SCIM, můžou ověřit požadavky z Azure Active Directory pomocí balíčku Microsoft. Owin. Security. Active Directory pomocí následujících kroků: 

1. Ve zprostředkovateli implementace Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior vlastnosti tak, že se vrátí metoda má být volána při každém spuštění služby: 

   ```csharp
     public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
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

1. Do této metody přidejte následující kód, který bude mít všechny požadavky na všechny koncové body služby, které jsou ověřené s tokenem vydaným Azure Active Directory pro zadaného tenanta, pro přístup k webové službě Azure AD Graph: 

   ```csharp
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
           ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
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

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Řízení zřizování a rušení zřizování uživatelů

1. Azure Active Directory dotáže služby pro uživatele s hodnotou atributu externalId odpovídající hodnota atributu mailNickname uživatele ve službě Azure AD. Dotaz se vyjadřuje jako požadavek HTTP (Hypertext Transfer Protocol), jako je například tento příklad, kde jyoung je ukázka mailNickname uživatele v Azure Active Directory.

    >[!NOTE]
    > Toto je pouze příklad. Ne všichni uživatelé budou mít atribut mailNickname a hodnota, kterou uživatel nemusí být v adresáři jedinečná. Také atribut použitý pro spárování (který v tomto případě je externalId) se dá nakonfigurovat v [mapováních atributů Azure AD](customize-application-attributes.md).

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   Pokud byla služba sestavena pomocí knihoven CLI poskytovaných společností Microsoft pro implementaci služeb SCIM, pak se žádost převede na volání metody dotazu poskytovatele služby.  Tady je signatura této metody: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ```
   Tady je definice Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters rozhraní: 
   ```csharp
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

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   Pokud služba je vytvořená pomocí knihovny Common Language Infrastructure pro implementaci SCIM služeb poskytovaných microsoftem, požadavek přeložit na volání metody dotazu poskytovatele.  Tady je signatura této metody: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Tady je definice Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters rozhraní: 

   ```csharp
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

   V následujícím ukázkovém dotazu pro uživatele s danou hodnotou pro atribut externalId jsou hodnoty argumentů předaných metodě dotazu: 
   * ukazatelů. AlternateFilters. Count: 1
   * Parametry. AlternateFilters.ElementAt(0). AttributePath: externalId""
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator. Equals
   * Parametry. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

1. Pokud odpověď na dotaz na webovou službu pro uživatele s hodnotou atributu externalId, která se shoduje s hodnotou atributu mailNickname uživatele, nevrátí žádné uživatele, Azure Active Directory žádosti, že služba zřídí uživatele, který odpovídá tomuto typu. v Azure Active Directory.  Tady je příklad takové žádosti: 

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
   Knihovny CLI poskytované společností Microsoft pro implementaci služeb SCIM převádějí tento požadavek na volání metody Create poskytovatele služby.  Metoda vytvoření má tento podpis: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   V požadavku na zřízení uživatele je hodnota argumentu prostředků instance Microsoft.SystemForCrossDomainIdentityManagement. Třída Core2EnterpriseUser definovaný v knihovně Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Pokud je žádost o zřízení uživatele úspěšné, implementaci metody se má vracet instanci Microsoft.SystemForCrossDomainIdentityManagement. Třída Core2EnterpriseUser s hodnotou nastavenou na jedinečný identifikátor nově zřízeného uživatele vlastnost identifikátor.  

1. Provést aktualizaci uživatele ví, že existují v úložišti identity přední stěnou podle SCIM, Azure Active Directory pokračuje vyžádáním aktuální stav daného uživatele ze služby s žádostí, jako například: 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   V rámci služby vytvořené pomocí knihoven CLI poskytovaných společností Microsoft pro implementaci služeb SCIM jsou požadavky přeloženy do volání metody načtení poskytovatele služby.  Následuje podpis metody načtení: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
   ```
   V příkladu požadavek pro načtení aktuálního stavu uživatele hodnoty vlastnosti objektu, který je uvedený jako hodnota argumentu parametry jsou následující: 
  
   * RID "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

1. Pokud je referenční atribut aktualizovaný, Azure Active Directory se dotazuje na službu a určí, jestli aktuální hodnota atributu reference v úložišti identit, na které se služba nachází, se už shoduje s hodnotou tohoto atributu v Azure Active. Službě. Pro uživatele je jediným atributem, z nichž aktuální hodnota je dotazován tímto způsobem atribut správce. Tady je příklad požadavku k určení, zda atribut manager objektu konkrétní uživatel aktuálně má určitou hodnotu: 

   Pokud byla služba sestavena pomocí knihoven CLI poskytovaných společností Microsoft pro implementaci služeb SCIM, pak se žádost převede na volání metody dotazu poskytovatele služby. Hodnota vlastnosti objektu, který je uvedený jako hodnota argumentu parametry jsou následující: 
  
   * ukazatelů. AlternateFilters. Count: 2
   * ukazatelů. AlternateFilters. ElementAt (x). AttributePath: ID
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator. Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * Parametry. AlternateFilters.ElementAt(y). AttributePath: "správce"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator. Equals
   * ukazatelů. AlternateFilter. ElementAt (y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * ukazatelů. RequestedAttributePaths. ElementAt (0): ID
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   V tomto případě může být hodnota indexu x rovna 0 a hodnota indexu y může být 1 nebo hodnota x může být 1 a hodnota y může být 0 v závislosti na pořadí výrazů parametru dotazu filtru.   

1. Tady je příklad požadavku z Azure Active Directory na službu SCIM provést aktualizaci uživatele: 
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
   Knihovny Microsoft CLI pro implementaci SCIM Services přeloží požadavek na volání metody Update poskytovatele služby. Následuje podpis metody Update: 
   ```csharp
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
   ```

   Pokud služba je vytvořená pomocí knihovny Common Language Infrastructure pro implementaci SCIM služeb poskytovaných microsoftem, požadavek přeložit na volání metody dotazu poskytovatele. Hodnota vlastnosti objektu, který je uvedený jako hodnota argumentu parametry jsou následující: 
  
* ukazatelů. AlternateFilters. Count: 2
* ukazatelů. AlternateFilters. ElementAt (x). AttributePath: ID
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator. Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* Parametry. AlternateFilters.ElementAt(y). AttributePath: "správce"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator. Equals
* ukazatelů. AlternateFilter. ElementAt (y). ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* ukazatelů. RequestedAttributePaths. ElementAt (0): ID
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  V tomto případě může být hodnota indexu x rovna 0 a hodnota indexu y může být 1 nebo hodnota x může být 1 a hodnota y může být 0 v závislosti na pořadí výrazů parametru dotazu filtru.   

1. Tady je příklad požadavku z Azure Active Directory na službu SCIM provést aktualizaci uživatele: 

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

   ```csharp
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
  
   * ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest as PatchRequest2). Operations. Count: 1
   * (PatchRequest as PatchRequest2). Operations. ElementAt (0). OperationName OperationName.Add
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "správce"
   * (PatchRequest as PatchRequest2). Operations. ElementAt (0). Hodnota. Count: 1
   * (PatchRequest jako PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referenční dokumentace: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest as PatchRequest2). Operations. ElementAt (0). Value. ElementAt (0). Osa 2819c223-7f76-453a-919d-413861904646

1. Na zrušení zřízení uživatele z identity úložiště přední stěnou služba SCIM, Azure AD, jako odešle žádost: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Pokud služba je vytvořená pomocí knihovny Common Language Infrastructure pro implementaci SCIM služeb poskytovaných microsoftem, je požadavek přeložit na volání metody Delete poskytovatele služby.   Tato metoda má tento podpis: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   Objekt zadaný jako hodnota argumentu resourceIdentifier má tyto hodnoty vlastností v příkladu požadavek na zrušení zřízení uživatele: 

1. Na zrušení zřízení uživatele z identity úložiště přední stěnou služba SCIM, Azure AD, jako odešle žádost: 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Pokud byla služba sestavena pomocí knihoven CLI poskytovaných společností Microsoft pro implementaci služeb SCIM, pak je požadavek přeložen do volání metody Delete poskytovatele služby.   Tato metoda má tento podpis: 
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
  
   * ResourceIdentifier. Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Odkaz na schéma uživatelů a skupin

Azure Active Directory můžete zřídit dva typy prostředků, aby SCIM webové služby.  Tyto typy prostředků jsou uživatelé a skupiny.  

Prostředky uživatele jsou označeny identifikátorem `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`schématu, který je zahrnutý v této specifikaci protokolu:. https://tools.ietf.org/html/rfc7643  Výchozí mapování atributů uživatelů v Azure Active Directory k atributům uživatelských prostředků je uvedeno v tabulce 1.  

Prostředky skupiny prostředků jsou označeny identifikátor schématu `urn:ietf:params:scim:schemas:core:2.0:Group`. Tabulka 2 zobrazuje výchozí mapování atributů skupin v Azure Active Directory na atributy prostředků skupiny.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabulka 1: Mapování výchozích atributů uživatele

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
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabulka 2: Mapování atributů výchozí skupiny

| Skupina Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| displayName |externalId |
| e-mailu |e-mailů [typ eq "pracovní"] .value |
| mailNickname |displayName |
| členové |členové |
| ID objektu |ID |
| proxyAddresses |e-mailů [Zadejte eq "other"]. Hodnota |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Povolte IP adresy, které používá služba zřizování Azure AD k provádění požadavků SCIM.

Některé aplikace umožňují příchozí provoz do své aplikace. Aby služba zřizování služby Azure AD fungovala podle očekávání, musí být povolené IP adresy. Seznam IP adres pro jednotlivé značky nebo oblasti služby najdete v souboru JSON – [rozsahy IP adres Azure a značky služeb – veřejný cloud](https://www.microsoft.com/download/details.aspx?id=56519). Tyto IP adresy můžete podle potřeby stáhnout a programovat do brány firewall. Rezervované rozsahy IP adres pro zřizování Azure AD najdete v části "AzureActiveDirectoryDomainServices".

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

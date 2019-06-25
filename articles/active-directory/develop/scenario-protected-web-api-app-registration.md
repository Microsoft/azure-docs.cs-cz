---
title: Chráněné webové rozhraní API – registrace aplikace | Azure
description: Zjistěte, jak vytvářet chráněné webové rozhraní API a informace, které potřebujete k registraci aplikace.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 22fe71c38678ae789a93ecbc956f24f0b0ebeb01
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67111131"
---
# <a name="protected-web-api---app-registration"></a>Chráněné webové rozhraní API – registrace aplikace

Tento článek vysvětluje podrobnosti registrace aplikace pro chráněné webové rozhraní API.

Zobrazit [rychlý start: Registrace aplikace s platformou identity Microsoft](quickstart-register-app.md) běžné pokyny o tom, jak zaregistrovat aplikaci.

## <a name="accepted-token-version"></a>Přijetí tokenu verze

Microsoft identity platform endpoint může vydat dva typy tokenů: tokeny v1.0 a v2.0 tokeny. Další informace o těchto tokenů v [přístupové tokeny](access-tokens.md). Přijatý token verze závisí **podporovaných typů účtu** jste zvolili při vytváření vaší aplikace:

- Pokud hodnota **podporovaných typů účtu** je **účty v jakékoli organizaci adresáři a osobní účty Microsoft (třeba Skype, Xbox, Outlook.com)** , přijatý token verze bude verze 2.0.
- Přijatý token verze v opačném případě bude v1.0.

Po vytvoření aplikace, přijatý token verzi můžete změnit pomocí následujících kroků:

1. Na webu Azure Portal, vyberte svou aplikaci a pak vyberte **Manifest** pro vaši aplikaci.
2. V manifestu, vyhledejte **"accessTokenAcceptedVersion"** a, že její hodnota je **2**. Tato vlastnost umožňuje vědět, že webové rozhraní API přijímá v2.0 tokeny služby Azure AD. Pokud je **null**, přijatý token verze bude v1.0.
3. Vyberte **Uložit**.

> [!NOTE]
> Záleží jen na webové rozhraní API se rozhodnout, která token verze (verze 1.0 nebo 2.0) přijímá. Když klient vyžádá token pro vaše webového rozhraní API s využitím koncového bodu v2.0 platforma identit Microsoft, získají první token, který označuje, která verze je přijat ve webovém rozhraní API.

## <a name="no-redirect-uri"></a>Žádný identifikátor URI pro přesměrování

Webová rozhraní API nebudou muset registrovat na identifikátor URI přesměrování je žádný uživatel přihlášený interaktivní.

## <a name="expose-an-api"></a>Vystavit rozhraní API

Další nastavení specifická pro webová rozhraní API je zveřejněné rozhraní API a vystavené obory.

### <a name="resource-uri-and-scopes"></a>Identifikátor URI prostředku a obory

Obory jsou obvykle ve formátu `resourceURI/scopeName`. Pro Microsoft Graph obory má zkratky jako `User.Read`, ale tento řetězec je zkratka pro `https://graph.microsoft.com/user.read`.

Při registraci aplikace budete muset definovat následující parametry:

- Identifikátor URI – ve výchozím nastavení portál pro registraci aplikací doporučuje, abyste použili `api://{clientId}`. Tento identifikátor URI prostředku je jedinečný, ale není lidské čitelné. Můžete ho změnit, ale ujistěte se, že se jedná o jedinečný.
- Jeden nebo více **obory** (pro klientské aplikace, zobrazí se jako **delegovaná oprávnění** vaše webové rozhraní API)
- Jeden nebo více **role aplikace** (pro klientské aplikace, zobrazí se jako **oprávnění aplikace** vaše webové rozhraní API)

Obory jsou také zobrazeny v obrazovkami pro vyjádření souhlasu, která se zobrazí koncovým uživatelům, kteří používají vaše aplikace. Proto budete muset poskytnout odpovídající řetězce, které popisují oboru:

- Jak je vidět koncovým uživatelem
- Jak je vidět správcem tenanta, který vám může udělit souhlas správce

### <a name="how-to-expose-delegated-permissions-scopes"></a>Jak vystavit delegovaná oprávnění (obory)

1. Vyberte **vystavit rozhraní API** kapitoly registrace aplikace a:
   1. Vyberte **Přidat obor**.
   1. Pokud o to požádá přijměte navrhovanou identifikátor URI ID aplikace (api :// {clientId}) tak, že vyberete **uložit a pokračovat**.
   1. Zadejte následující parametry:
      - Pro **název oboru**, použijte `access_as_user`.
      - Pro **kdo může odsouhlasit**, ujistěte se, že **správci a uživatelé** je vybraná možnost.
      - V **zobrazovaný název souhlasu správce**, typ `Access TodoListService as a user`.
      - V **popis souhlasu správce**, typ `Accesses the TodoListService Web API as a user`.
      - V **zobrazovaný název souhlasu uživatele**, typ `Access TodoListService as a user`.
      - V **popis souhlasu uživatele**, typ `Accesses the TodoListService Web API as a user`.
      - Zachovat **stavu** nastavena na **povoleno**.
      - Vyberte **přidat obor**.

### <a name="case-where-your-web-api-is-called-by-daemon-application"></a>Případ, kde proces démon aplikace volá webové rozhraní API

V tomto odstavci se dozvíte, jak zaregistrovat chráněné webové rozhraní API tak, že je možné volat bezpečně v démon aplikace:

- je potřeba zveřejnit **oprávnění aplikace**. Oprávnění aplikace se deklarovat jenom jako aplikace démon není interakci s uživateli a proto by nedávalo smysl delegovaná oprávnění.
- Správci tenanta můžou vyžadovat Azure AD, aby problém tokeny pro vaši webovou aplikaci do pouze aplikace, které jste zaregistrovali, že chtějí přístup k některé z oprávnění aplikace webového rozhraní API.

#### <a name="how-to-expose-application-permissions-app-roles"></a>Jak publikovat aplikaci oprávnění (role aplikace)

Zveřejnit aplikaci oprávnění, bude nutné upravit manifest.

1. V registraci aplikace pro vaši aplikaci, klikněte na tlačítko **Manifest**.
1. Upravit manifest vyhledáním `appRoles` nastavení a přidání jednoho nebo několika aplikačních rolí. Definice role je k dispozici v bloku JSON ukázka níže.  Nechte `allowedMemberTypes` "Aplikace" pouze. Ujistěte se prosím, která **id** je jedinečný identifikátor guid a **displayName** a **hodnotu** neobsahují žádné mezery.
1. Uložte manifest.

Obsah `appRoles` by měl být následující ( `id` může být libovolný jedinečný identifikátor GUID)

```JSon
"appRoles": [
    {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
    }
],
```

#### <a name="how-to-ensure-that-azure-ad-issues-tokens-for-your-web-api-only-to-allowed-clients"></a>Jak zajistit, že Azure AD vystavuje tokeny pro vaše webové rozhraní API pouze povolené klientů

Webové rozhraní API vyhledá role aplikace (to znamená způsob pro vývojáře dělat to). Ale můžete také konfigurovat Azure Active Directory k vydání tokenu pouze pro aplikace, které nebyly schválené správcem tenanta pro přístup k rozhraní API pro webové rozhraní API. Pokud chcete přidat toto dodatečné zabezpečení:

1. V aplikaci **přehled** stránce pro registraci vaší aplikace, vyberte hypertextový odkaz s názvem vaší aplikace v **spravované aplikace v místním adresáři**. Název pro toto pole můžete byla zkrácena. Můžete je například čtení: `Managed application in ...`

   > [!NOTE]
   >
   > Při výběru tohoto odkazu přejdete **podnikových aplikací – přehled** stránky, které jsou přidružené k objektu služby pro vaši aplikaci v tenantovi, ve které jste vytvořili. Můžete přejít zpět na stránce registrace aplikace s použitím tlačítka Zpět v prohlížeči.

1. Vyberte **vlastnosti** stránku **spravovat** část stránky podnikových aplikací
1. Pokud chcete AAD pro vynucení přístupu pro vaše webové rozhraní API z pouze určitá klienty, nastavte **přiřazení uživatelů povinné?** k **Ano**.

   > [!IMPORTANT]
   >
   > Nastavením **přiřazení uživatelů povinné?** k **Ano**, AAD zkontroluje přiřazení role aplikace z klientů, když zadají žádost o přístupový token pro webové rozhraní API. Pokud klient není přiřazen k žádné AppRoles, AAD vracel jenom následující chybu: `invalid_client: AADSTS501051: Application xxxx is not assigned to a role for the xxxx`
   >
   > Pokud uchováváte **přiřazení uživatelů povinné?** k **ne**, <span style='background-color:yellow; display:inline'>Azure AD nebude kontrolovat přiřazení rolí aplikace, když klient požádá o přístupový token pro vaše webové rozhraní API</span>. Proto libovolného démon klienta (tj. jakýkoli klient pomocí toku přihlašovacích údajů klienta) by stále moct získání přístupového tokenu pro rozhraní API, stejně tak, že zadáte jeho cílová skupina. Všechny aplikace, bude mít přístup k rozhraní API, aniž byste museli požádat o oprávnění k němu. Nyní, to není pak by, protože vaše webové rozhraní API můžete vždy, jak je vysvětleno v další části, ověřte, zda aplikace má tu správnou roli (které bylo povoleno správcem tenanta), tak, že přístupový token má `roles` deklarace identity a správné hodnoty pro t jeho deklarace identity (v našem případě `access_as_application`).

1. Vyberte **uložit**

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-protected-web-api-app-configuration.md)

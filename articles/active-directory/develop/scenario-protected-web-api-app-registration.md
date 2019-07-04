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
ms.openlocfilehash: e4622cffedc159ce85166eafe571ccb26c2c1b4d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536860"
---
# <a name="protected-web-api-app-registration"></a>Chráněné webové rozhraní API: Registrace aplikací

Tento článek vysvětluje, jaké jsou specifikace registrace aplikace pro chráněné webové rozhraní API.

Zobrazit [rychlý start: Registrace aplikace s platformou identity Microsoft](quickstart-register-app.md) pro běžné kroky pro registraci aplikace.

## <a name="accepted-token-version"></a>Přijetí tokenu verze

Microsoft identity platform endpoint může vydat dva typy tokenů: tokeny v1.0 a v2.0 tokeny. Další informace o těchto tokenů najdete v tématu [přístupové tokeny](access-tokens.md). Přijatý token verze závisí **podporovaných typů účtu** jste zvolili při vytváření vaší aplikace:

- Pokud hodnota **podporovaných typů účtu** je **účty v jakékoli organizaci adresáři a osobní účty Microsoft (třeba Skype, Xbox, Outlook.com)** , přijatý token verze bude verze 2.0.
- Přijatý token verze v opačném případě bude v1.0.

Po vytvoření aplikace, můžete zjistit nebo změnit přijatá token verze pomocí následujících kroků:

1. Na webu Azure Portal, vyberte svou aplikaci a pak vyberte **Manifest** pro vaši aplikaci.
2. V manifestu, vyhledejte **"accessTokenAcceptedVersion"** . Všimněte si, že její hodnota je **2**. Tato vlastnost určuje do služby Azure Active Directory (Azure AD), webové rozhraní API přijímá tokeny verze 2.0. Pokud je hodnota **null**, přijatý token verze je verze 1.0.
3. Pokud jste změnili verzi tokenu, vyberte **Uložit**.

> [!NOTE]
> Webové rozhraní API určuje přijímá token verze (verze 1.0 nebo 2.0). Když klient vyžádá token pro vaše webové rozhraní API z koncového bodu v2.0 platforma identit Microsoft, získají první token, který označuje, která verze je přijat ve webovém rozhraní API.

## <a name="no-redirect-uri"></a>Žádný identifikátor URI pro přesměrování

Webová rozhraní API nebudou muset registrovat na identifikátor URI přesměrování, protože není přihlášený žádný uživatel interaktivně.

## <a name="expose-an-api"></a>Vystavit rozhraní API

Další nastavení specifická pro webová rozhraní API je zveřejněné rozhraní API a vystavené obory.

### <a name="resource-uri-and-scopes"></a>Identifikátor URI prostředku a obory

Obory jsou obvykle ve formě `resourceURI/scopeName`. Pro Microsoft Graph obory má zkratky jako `User.Read`. Tento řetězec je zkratka pro `https://graph.microsoft.com/user.read`.

Při registraci aplikace budete muset definovat tyto parametry:

- Identifikátor URI prostředku. Ve výchozím nastavení, portál pro registraci aplikací doporučuje, abyste použili `api://{clientId}`. Tento identifikátor URI prostředku je jedinečný, ale není lidské čitelné. Můžete ho změnit, ale ujistěte se, že nová hodnota je jedinečný.
- Jeden nebo více *obory*. (Pro klientské aplikace, zobrazí se jako *delegovaná oprávnění* pro vaše webové rozhraní API.)
- Jeden nebo více *role aplikace*. (Pro klientské aplikace, zobrazí se jako *oprávnění aplikace* pro vaše webové rozhraní API.)

Obory jsou také zobrazeny v obrazovkami pro vyjádření souhlasu, která se zobrazí koncovým uživatelům vaší aplikace. Proto budete muset poskytnout odpovídající řetězce, které popisují oboru:

- Jak je vidět koncovým uživatelem.
- Jak je vidět správcem tenanta, který vám může udělit souhlas správce.

### <a name="exposing-delegated-permissions-scopes"></a>Vystavení delegovaná oprávnění (obory)

1. Vyberte **vystavit rozhraní API** části v registraci aplikace.
1. Vyberte **Přidat obor**.
1. Po výzvě přijměte navrhovanou identifikátor URI ID aplikace (`api://{clientId}`) tak, že vyberete **uložit a pokračovat**.
1. Zadejte tyto parametry:
      - Pro **název oboru**, použijte **access_as_user**.
      - Pro **kdo může odsouhlasit**, ujistěte se, že **správci a uživatelé** zaškrtnuto.
      - V **zobrazovaný název souhlasu správce**, zadejte **TodoListService přístup jako uživatel**.
      - V **popis souhlasu správce**, zadejte **přistupuje k webové rozhraní API TodoListService jako uživatel**.
      - V **zobrazovaný název souhlasu uživatele**, zadejte **TodoListService přístup jako uživatel**.
      - V **popis souhlasu uživatele**, zadejte **přistupuje k webové rozhraní API TodoListService jako uživatel**.
      - Zachovat **stavu** nastavena na **povoleno**.
      - Vyberte **přidat obor**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Pokud vaše webové rozhraní API je volán démon aplikace

V této části se naučíte zaregistrovat chráněné webové rozhraní API je možné bezpečně volat v aplikace démonů.

- je potřeba zveřejnit *oprávnění aplikace*. Budete deklarovat pouze oprávnění aplikace, protože aplikace démonů nespolupracují s uživateli, takže by nedávalo smysl delegovaná oprávnění.
- Správci tenanta může vyžadovat služby Azure Active Directory (Azure AD) k vydání tokenů pro vaše webové rozhraní API pouze pro aplikace, které jste se zaregistrovali pro přístup k jedné oprávnění aplikace webového rozhraní API.

#### <a name="exposing-application-permissions-app-roles"></a>Vystavení oprávnění aplikace (role aplikace)

Zveřejnit aplikaci oprávnění, bude nutné upravit manifest.

1. Registrace aplikace pro vaši aplikaci, vyberte **Manifest**.
1. Upravit manifest vyhledáním `appRoles` nastavení a přidání jednoho nebo více aplikačních rolí. Definice role je podle následující ukázky JSON blok. Nechte `allowedMemberTypes` nastavena na `"Application"` pouze. Ujistěte se, `id` jedinečný identifikátor GUID a že `displayName` a `value` neobsahují mezery.
1. Uložte manifest.

Následující příklad ukazuje obsah `appRoles`. ( `id` Může být libovolný jedinečný identifikátor GUID.)

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Zajištění, že Azure AD vystavuje tokeny pro vaše webového rozhraní API povoleno pouze klienti

Webové rozhraní API vyhledá role aplikace. (To je způsob pro vývojáře k vystavení oprávnění aplikace.) Ale můžete také konfigurovat Azure AD k vydání tokenu pro vaše webové rozhraní API jen pro aplikace, které jsou schválené správcem tenanta pro přístup k rozhraní API. Pokud chcete přidat toto zvýšené zabezpečení:

1. V aplikaci **přehled** stránce pro registraci vaší aplikace, vyberte odkaz s názvem vaší aplikace v rámci **spravované aplikace v místním adresáři**. Název pro toto pole může být zkrácena. Může se zobrazit třeba **spravovaná aplikace v...**

   > [!NOTE]
   >
   > Při výběru tohoto odkazu přejdete na **podnikových aplikací – přehled** stránky, které jsou přidružené k objektu služby pro vaši aplikaci v tenantovi, ve které jste vytvořili. Můžete přejít zpět na stránce registrace aplikace s použitím tlačítka Zpět v prohlížeči.

1. Vyberte **vlastnosti** stránku **spravovat** část stránky podnikových aplikací.
1. Pokud chcete povolit přístup k webové rozhraní API z pouze určitá klienty Azure AD, nastavte **přiřazení uživatelů povinné?** k **Ano**.

   > [!IMPORTANT]
   >
   > Pokud nastavíte **přiřazení uživatelů povinné?** k **Ano**, Azure AD bude zjišťovat přiřazení rolí aplikace klientů, když zadají žádost o přístupový token pro webové rozhraní API. Pokud klient není přiřazen k žádné roli aplikace, služby Azure AD se vrátí chybu `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Pokud uchováváte **přiřazení uživatelů povinné?** nastavena na **ne**, *Azure AD nebude kontrolovat přiřazení rolí aplikace, když klient požádá o přístupový token pro vaše webové rozhraní API*. Získání přístupového tokenu pro rozhraní API jenom tak, že zadáte jeho cílová skupina uvidí libovolného klienta démon (to znamená, libovolného klienta pomocí toku přihlašovacích údajů klienta). Všechny aplikace bude mít přístup k rozhraní API, aniž byste museli požádat o oprávnění k němu. Ale vaše webové rozhraní API můžete vždy, jak je popsáno v předchozí části, ověřte, že aplikace má tu správnou roli (který má oprávnění správce tenanta). Rozhraní API tak, že přístupový token má role provádí toto ověřování deklarací identity a správnost hodnotu pro tuto deklaraci. (V našem případě je hodnota `access_as_application`.)

1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-protected-web-api-app-configuration.md)

---
title: Registrace aplikace chráněného webového rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak vytvořit chráněné webové rozhraní API a informace, které potřebujete k registraci aplikace.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 997dd98d35b74effe5d195f9a781fa0935286ee9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537215"
---
# <a name="protected-web-api-app-registration"></a>Chráněné webové rozhraní API: Registrace aplikací

Tento článek vysvětluje specifika registrace aplikací pro chráněné webové rozhraní API.

Běžné kroky k registraci aplikace najdete v [tématu Úvodní příručka: Registrace aplikace na platformě identit microsoftu](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Přijatá verze tokenu

Koncový bod platformy identit y Microsoft může vydávat tokeny v1.0 a tokeny v2.0. Další informace o těchto tokenech naleznete v [tématu Access tokeny](access-tokens.md).

Verze přijatého tokenu závisí na hodnotě **podporovaných typů účtů,** kterou zvolíte při vytváření aplikace.

- Pokud je hodnota **podporovaných typů účtů** **Účty v libovolném organizačním adresáři a osobníúčty Microsoft (např. Skype, Xbox, Outlook.com)**, je přijatá verze tokenu verze v2.0.
- V opačném případě je přijatá verze tokenu v1.0.

Po vytvoření aplikace můžete určit nebo změnit verzi přijatého tokenu následujícím postupem:

1. Na webu Azure Portal vyberte aplikaci a pak vyberte **Manifest**.
1. Najít **vlastnost accessTokenAcceptedVersion** v manifestu. Výchozí hodnota vlastnosti je 2.
1. Tato hodnota určuje azure active directory (Azure AD), které verze tokenu webové rozhraní API přijímá.
    - Pokud je hodnota 2, webové rozhraní API přijímá tokeny v2.0.
    - Pokud je hodnota **null**, webové rozhraní API přijímá tokeny v1.0.
1. Pokud jste změnili verzi tokenu, vyberte **Uložit**.

> [!NOTE]
> Webové rozhraní API určuje, kterou verzi tokenu přijímá. Když klient požaduje token pro vaše webové rozhraní API z koncového bodu platformy microsoftidentity (v2.0), klient získá token, který označuje, kterou verzi tokenu webové rozhraní API přijímá.

## <a name="no-redirect-uri"></a>Žádný identifikátor URI přesměrování

Webová rozhraní API nemusí registrovat identifikátor URI přesměrování, protože není interaktivně přihlášen žádný uživatel.

## <a name="exposed-api"></a>Vystavené rozhraní API

Další nastavení specifická pro webová rozhraní API jsou vystavené rozhraní API a vystavené obory.

### <a name="application-id-uri-and-scopes"></a>Identifikátor URI id aplikace a obory

Obory mají obvykle `resourceURI/scopeName`formulář . V aplikaci Microsoft Graph mají obory zástupce. Například `User.Read` je zkratka `https://graph.microsoft.com/user.read`pro .

Během registrace aplikace je třeba definovat tyto parametry:

- Identifikátor URI prostředku
- Jeden nebo více oborů
- Jedna nebo více rolí aplikace

Ve výchozím nastavení portál pro registraci aplikací `api://{clientId}`doporučuje použít identifikátor URI prostředku . Tento identifikátor URI je jedinečný, ale není čitelný pro člověka. Pokud změníte identifikátor URI, ujistěte se, že nová hodnota je jedinečná.

Pro klientské aplikace se obory zobrazují jako *delegovaná oprávnění* a role aplikací se zobrazují jako *oprávnění aplikací* pro webové rozhraní API.

Obory se také zobrazují v okně souhlasu, které se zobrazí uživatelům vaší aplikace. Takže je třeba zadat odpovídající řetězce, které popisují obor:

- Jak je vidět uživatelem.
- Jak vidí správce tenanta, který může udělit souhlas správce.

### <a name="exposing-delegated-permissions-scopes"></a>Odhalení delegovaných oprávnění (oborů)

1. V registraci aplikace vyberte **vystavit rozhraní API.**
1. Vyberte **Přidat obor**.
1. Pokud se zobrazí výzva, přijměte navrhované identifikátory URI aplikace (`api://{clientId}`) výběrem **možnosti Uložit a pokračovat**.
1. Zadejte tyto hodnoty:
    - Vyberte **název oboru** a zadejte **access_as_user**.
    - Vyberte **Kdo může souhlasit** a ujistěte se, že jsou **vybraní správci a uživatelé.**
    - Vyberte **zobrazovaný název souhlasu správce** a **zadejte přístup TodoListService jako uživatel**.
    - Vyberte **popis souhlasu správce** a zadejte **přístupy webového rozhraní API TodoListService jako uživatele**.
    - Vyberte **zobrazovaný název souhlasu uživatele** a **zadejte přístup K todoListService jako uživatel**.
    - Vyberte **popis souhlasu uživatele** a zadejte **přístupy webového rozhraní API TodoListService jako uživatele**.
    - Ponechte hodnotu **Stavu** nastavenou na **Povoleno**.
 1. Vyberte **přidat obor**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Pokud je vaše webové rozhraní API voláno aplikací pro daemony

V této části se dozvíte, jak zaregistrovat chráněné webové rozhraní API, aby ho aplikace pro daemon mohly bezpečně volat.

- Deklarujete a zveřejňujete pouze *oprávnění aplikací,* protože aplikace daemonu nekomunikují s uživateli. Delegovaná oprávnění by nedávala smysl.
- Správci tenanta můžou vyžadovat, aby Azure AD vydávala tokeny webového rozhraní API jenom aplikacím, které se zaregistrovaly pro přístup k jednomu z oprávnění aplikace rozhraní API.

#### <a name="exposing-application-permissions-app-roles"></a>Vystavení oprávnění aplikací (role aplikace)

Chcete-li zpřístupnit oprávnění aplikací, je třeba upravit manifest.

1. V registraci přihlášky pro vaši přihlášku vyberte **Manifest**.
1. Chcete-li manifest upravit, vyhledejte `appRoles` nastavení a přidejte role aplikace. Definice rolí jsou uvedeny v následujícím ukázkovém bloku JSON.
1. Nechte `allowedMemberTypes` nastaveno pouze. `"Application"`
1. Ujistěte `id` se, že je jedinečný identifikátor GUID.
1. Ujistěte `displayName` `value` se, že neobsahuje mezery.
1. Uložte seznam.

Následující ukázka ukazuje `appRoles`obsah , kde `id` hodnota může být libovolný jedinečný identifikátor GUID.

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Zajištění, že Azure AD vydává tokeny pro vaše webové rozhraní API jenom povoleným klientům

Webové rozhraní API zkontroluje roli aplikace. Tato role je způsob, jak vývojáře softwaru vystavit oprávnění aplikací. Azure AD můžete také nakonfigurovat tak, aby vydávala tokeny rozhraní API jenom pro aplikace, které správce tenanta schvaluje pro přístup k rozhraní API.

Chcete-li přidat toto zvýšené zabezpečení:

1. Přejděte na stránku **Přehled** aplikace pro registraci aplikace.
1. V části **Spravovaná aplikace v místním adresáři**vyberte odkaz s názvem aplikace. Popisek pro tento výběr může být zkrácen. Můžete například zobrazit **spravovanou aplikaci v ...**

   > [!NOTE]
   >
   > Když vyberete tento odkaz, přejdete na stránku **Přehled podnikových aplikací.** Tato stránka je přidružena k instančnímu objektu pro vaši aplikaci v tenantovi, kde jste ji vytvořili. Na stránku registrace aplikace můžete přejít pomocí tlačítka Zpět v prohlížeči.

1. Vyberte stránku **Vlastnosti** v části **Spravovat** na stránkách aplikace Enterprise.
1. Pokud chcete, aby Azure AD povolit přístup k webové rozhraní API **Yes**pouze z určitých klientů, nastavte **přiřazení uživatele povinné?**

   > [!IMPORTANT]
   >
   > Pokud nastavíte **přiřazení uživatele povinné?** na **Ano**, Azure AD zkontroluje přiřazení rolí aplikace klienta, když požaduje přístupový token webového rozhraní API. Pokud klient není přiřazen k žádné role aplikace, Azure AD vrátí chybovou zprávu "invalid_client: \<AADSTS501051: Název\> aplikace aplikace není přiřazen a role pro \<webové rozhraní API\>".
   >
   > Pokud **ponesete přiřazení uživatele požadované?** nastaveno na **ne**, Azure AD nebude kontrolovat přiřazení rolí aplikace, když klient požaduje přístupový token pro vaše webové rozhraní API. Každý klient daemon, což znamená, že každý klient pomocí toku pověření klienta, můžete získat přístupový token pro rozhraní API pouze zadáním jeho cílové skupiny. Každá aplikace může přistupovat k rozhraní API, aniž by pro něj musela vyžadovat oprávnění.
   >
   > Ale jak je vysvětleno v předchozí části, vaše webové rozhraní API můžete vždy ověřit, že aplikace má správnou roli, která je autorizována správcem klienta. Rozhraní API provádí toto ověření ověřením, že přístupový token má deklaraci rolí a že hodnota pro tuto deklaraci je správná. V předchozí ukázce JSON je `access_as_application`hodnota .

1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-protected-web-api-app-configuration.md)

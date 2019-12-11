---
title: Registrace aplikace chráněného webového rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet chráněné webové rozhraní API a informace, které potřebujete k registraci aplikace.
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
ms.openlocfilehash: 86c36661dd8b5d43417ca1d845eef5cdb146f1e9
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962521"
---
# <a name="protected-web-api-app-registration"></a>Chráněné webové rozhraní API: registrace aplikace

Tento článek vysvětluje konkrétní registraci aplikací pro chráněné webové rozhraní API.

Obecné kroky pro registraci aplikace najdete v tématu [rychlý Start: registrace aplikace na platformě Microsoft Identity](quickstart-register-app.md) .

## <a name="accepted-token-version"></a>Verze přijatého tokenu

Koncový bod platformy Microsoft Identity může vydávat dva typy tokenů: v tokenech 1.0 a tokenech v 2.0. Další informace o těchto tokenech najdete v tématu [přístupové tokeny](access-tokens.md). Přijatá verze tokenu závisí na **podporovaných typech účtů** , které jste si zvolili při vytváření aplikace:

- Pokud je hodnota **podporovaných typů účtů** **účty v jakémkoli adresáři organizace a na osobních účtech Microsoft (např. Skype, Xbox, Outlook.com)** , verze přijatého tokenu bude v 2.0.
- V opačném případě bude verze přijatého tokenu v 1.0.

Po vytvoření aplikace můžete zjistit nebo změnit verzi přijatého tokenu pomocí následujících kroků:

1. V Azure Portal vyberte svou aplikaci a pak vyberte **manifest** pro vaši aplikaci.
2. V manifestu vyhledejte **"accessTokenAcceptedVersion"** . Všimněte si, že jeho hodnota je **2**. Tato vlastnost určuje, jestli se má Azure Active Directory (Azure AD) tokeny webového rozhraní API přijmout verze 2.0. Pokud je hodnota **null**, verze přijatého tokenu je v 1.0.
3. Pokud jste změnili verzi tokenu, vyberte **Uložit**.

> [!NOTE]
> Webové rozhraní API určuje, která verze tokenu (v 1.0 nebo v 2.0) přijímá. Když klienti požadují token pro vaše webové rozhraní API z koncového bodu Microsoft Identity Platform (v 2.0), získají token, který indikuje, kterou verzi webové rozhraní API akceptuje.

## <a name="no-redirect-uri"></a>Žádný identifikátor URI pro přesměrování

Webová rozhraní API nepotřebují registrovat identifikátor URI přesměrování, protože k interaktivnímu přihlášení není žádný uživatel.

## <a name="expose-an-api"></a>Zpřístupnit rozhraní API

Další nastavení specifické pro webová rozhraní API je vystavené rozhraní API a exponované obory.

### <a name="resource-uri-and-scopes"></a>Identifikátor URI a rozsahy prostředku

Obory jsou obvykle ve formě `resourceURI/scopeName`. Pro Microsoft Graph mají obory zkratky jako `User.Read`. Tento řetězec je zástupcem pro `https://graph.microsoft.com/user.read`.

Při registraci aplikace budete muset definovat tyto parametry:

- Identifikátor URI prostředku Portál pro registraci aplikací standardně doporučuje použít `api://{clientId}`. Identifikátor URI tohoto prostředku je jedinečný, ale není čitelný pro lidskou čitelnost. Můžete ho změnit, ale zajistěte, aby byla nová hodnota jedinečná.
- Jeden nebo více *oborů*. (Klientským aplikacím se zobrazí jako *delegovaná oprávnění* pro vaše webové rozhraní API.)
- Jedna nebo více *aplikačních rolí*. (Pro klientské aplikace se zobrazí jako *oprávnění aplikace* pro vaše webové rozhraní API.)

Obory se také zobrazují na obrazovce pro vyjádření souhlasu, která se zobrazí koncovým uživatelům vaší aplikace. Proto budete muset zadat odpovídající řetězce, které popisují rozsah:

- Jak je vidět koncový uživatel.
- Jak je vidět správce tenanta, který může udělit souhlas správce.

### <a name="exposing-delegated-permissions-scopes"></a>Vystavení delegovaných oprávnění (rozsahy)

1. V registraci aplikace vyberte část **vystavení rozhraní API** .
1. Vyberte **Přidat obor**.
1. Pokud se zobrazí výzva, přijměte navržený identifikátor URI (`api://{clientId}`) ID aplikace **a vyberte Uložit a pokračovat**.
1. Zadejte tyto parametry:
      - Jako **název oboru**použijte **access_as_user**.
      - Pro **uživatele, kteří můžou vyjádřit souhlas**, se ujistěte, že je vybraná možnost **Správci a uživatelé** .
      - V **zobrazení název souhlasu správce**zadejte **přístup TodoListService jako uživatel**.
      - V **popisu souhlasu správce**zadejte **přístup k webovému rozhraní API TodoListService jako uživatel**.
      - V **zobrazení název souhlasu uživatele**zadejte **přístup TodoListService jako uživatel**.
      - V **popisu souhlasu uživatele**zadejte **přístup k webovému rozhraní API TodoListService jako uživatel**.
      - Ponechat **stav** nastavený na **povoleno**.
      - Vyberte **Přidat obor**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Pokud je vaše webové rozhraní API voláno aplikací démona

V této části se dozvíte, jak zaregistrovat chráněné webové rozhraní API, aby je bylo možné bezpečně volat pomocí aplikací démona.

- Budete muset zveřejnit *oprávnění aplikace*. Deklarujete jenom oprávnění aplikace, protože aplikace typu démon nepracují s uživateli, takže delegovaná oprávnění by nevedla smysl.
- Správci klientů můžou vyžadovat Azure Active Directory (Azure AD) k vydávání tokenů pro vaše webové rozhraní API pouze aplikacím, které mají zaregistrovaný přístup k jednomu z oprávnění aplikace webového rozhraní API.

#### <a name="exposing-application-permissions-app-roles"></a>Vystavení oprávnění aplikace (aplikační role)

Chcete-li zveřejnit oprávnění aplikace, budete muset manifest upravit.

1. V registraci aplikace pro vaši aplikaci vyberte možnost **manifest**.
1. Upravte manifest tak, že vyhledáte nastavení `appRoles` a přidáte jednu nebo více aplikačních rolí. Definice role je k dispozici v následujícím ukázkovém bloku JSON. Ponechte `allowedMemberTypes` nastavenou na pouze `"Application"`. Ujistěte se, že `id` jedinečný identifikátor GUID a že `displayName` a `value` neobsahuje mezery.
1. Uložte manifest.

Následující příklad ukazuje obsah `appRoles`. (`id` může být libovolný jedinečný identifikátor GUID.)

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

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Zajištění, že služba Azure AD vystavuje tokeny pro vaše webové rozhraní API jenom pro povolené klienty

Webové rozhraní API kontroluje aplikační roli. (To je vývojář způsob, jak zveřejnit oprávnění aplikace.) Můžete ale také nakonfigurovat službu Azure AD tak, aby vydávala token pro webové rozhraní API jenom aplikacím schváleným správcem tenanta pro přístup k vašemu rozhraní API. Přidání tohoto zvýšeného zabezpečení:

1. Na stránce **Přehled** aplikace pro registraci vaší aplikace vyberte odkaz s názvem vaší aplikace v části **spravovaná aplikace v místním adresáři**. Název tohoto pole může být oříznutý. Můžete například zobrazit **spravovanou aplikaci v...**

   > [!NOTE]
   >
   > Když vyberete tento odkaz, přejdete na stránku s **přehledem podnikové aplikace** přidruženou k instančnímu objektu vaší aplikace v tenantovi, kde jste ji vytvořili. Zpět na stránku registrace aplikace můžete přejít pomocí tlačítka zpět v prohlížeči.

1. V části **Správa** na stránkách podnikové aplikace vyberte stránku **vlastností** .
1. Pokud chcete, aby služba Azure AD povolovala přístup k webovému rozhraní API jenom pro některé klienty, nastavte pro **přiřazení uživatele** hodnotu **Ano**.

   > [!IMPORTANT]
   >
   > Pokud jste si nastavili **přiřazení uživatele jako povinné?** Pokud **Ano**, služba Azure AD zkontroluje přiřazení rolí aplikace při vyžádání přístupového TOKENU pro webové rozhraní API. Pokud klient není přiřazený k rolím aplikace, služba Azure AD vrátí chybu `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>`.
   >
   > Pokud si zachováte **přiřazení uživatelů?** nastavit na **ne**, *Azure AD nebude kontrolovat přiřazení role aplikace, když si klient vyžádá přístupový token pro vaše webové rozhraní API*. Jakýkoli Klient démona (to znamená, že každý klient používající tok přihlašovacích údajů klienta) bude moci získat přístupový token pro rozhraní API jenom tak, že určí cílovou skupinu. Každá aplikace bude mít přístup k rozhraní API bez nutnosti požádat o oprávnění. Ale vaše webové rozhraní API může vždy, jak je vysvětleno v předchozí části, ověřit, zda má aplikace správnou roli (která je autorizována správcem tenanta). Rozhraní API provede toto ověření tím, že ověří, že přístupový token má deklaraci identity rolí a že hodnota této deklarace je správná. (V našem případě je hodnota `access_as_application`.)

1. Vyberte **Save** (Uložit).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace kódu aplikace](scenario-protected-web-api-app-configuration.md)

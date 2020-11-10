---
title: Registrace aplikace chráněného webového rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet chráněné webové rozhraní API a informace, které potřebujete k registraci aplikace.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 7a38e2384c5f24bc3a72e1ef8e8f7119b2db0f2f
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443938"
---
# <a name="protected-web-api-app-registration"></a>Chráněné webové rozhraní API: registrace aplikace

Tento článek vysvětluje konkrétní registraci aplikací pro chráněné webové rozhraní API.

Běžné kroky k registraci aplikace najdete v tématu [rychlý Start: registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md).

## <a name="accepted-token-version"></a>Verze přijatého tokenu

Koncový bod platformy Microsoft Identity může vydávat tokeny v 1.0 a tokeny v 2.0. Další informace o těchto tokenech najdete v tématu [přístupové tokeny](access-tokens.md).

Verze tokenu, kterou vaše rozhraní API může přijmout, závisí na **podporovaných typech účtů** při vytváření registrace aplikace webového rozhraní api v Azure Portal.

- Pokud je hodnota **podporovaných typů účtů** **účty v jakémkoli adresáři organizace a na osobních účtech Microsoft (např. Skype, Xbox, Outlook.com)** , musí být přijatý token verze v 2.0.
- V opačném případě může verze přijatého tokenu v 1.0.

Po vytvoření aplikace můžete zjistit nebo změnit verzi přijatého tokenu pomocí následujících kroků:

1. V Azure Portal vyberte svou aplikaci a pak vyberte **manifest**.
1. V manifestu Najděte vlastnost **accessTokenAcceptedVersion** .
1. Hodnota určuje Azure Active Directory (Azure AD), která verze tokenu přijímá webové rozhraní API.
    - Pokud je hodnota 2, webové rozhraní API akceptuje tokeny verze 2.0.
    - Pokud je hodnota **null** , webové rozhraní API akceptuje tokeny v 1.0.
1. Pokud jste změnili verzi tokenu, vyberte **Uložit**.

> [!NOTE]
> Webové rozhraní API určuje, která verze tokenu přijímá. Když klient požádá o token webového rozhraní API z koncového bodu Microsoft Identity Platform (v 2.0), klient získá token, který indikuje, kterou verzi tokenu webové rozhraní API přijímá.

## <a name="no-redirect-uri"></a>Žádný identifikátor URI pro přesměrování

Webová rozhraní API nepotřebují registrovat identifikátor URI přesměrování, protože k němu není interaktivně přihlášený žádný uživatel.

## <a name="exposed-api"></a>Vystavené rozhraní API

Další nastavení specifická pro webová rozhraní API jsou vystavená rozhraní API a exponované obory nebo aplikační role.

### <a name="application-id-uri-and-scopes"></a>Identifikátor URI a obory ID aplikace

Rozsahy mají obvykle formu `resourceURI/scopeName` . Pro Microsoft Graph mají obory zkratky. Například `User.Read` je zástupce pro `https://graph.microsoft.com/user.read` .

Během registrace aplikace je potřeba definovat tyto parametry:

- Identifikátor URI prostředku
- Jeden nebo více oborů
- Jedna nebo více aplikačních rolí

Portál pro registraci aplikací standardně doporučuje použít identifikátor URI prostředku `api://{clientId}` . Tento identifikátor URI je jedinečný, ale není čitelný pro lidské čtení. Pokud identifikátor URI změníte, ujistěte se, že je nová hodnota jedinečná. Portál pro registraci aplikací bude mít jistotu, že používáte [nakonfigurovanou doménu vydavatele](howto-configure-publisher-domain.md).

Pro klientské aplikace se obory zobrazují jako *delegovaná oprávnění* a role aplikace se jako oprávnění aplikace pro vaše webové rozhraní API zobrazují jako *oprávnění aplikace* .

Obory se také zobrazí v okně pro vyjádření souhlasu, které jsou prezentovány uživatelům vaší aplikace. Proto je nutné zadat odpovídající řetězce, které popisují rozsah:

- Jak je vidět uživatel.
- Jak je vidět správce klienta, který může udělit souhlas správce.

Na role aplikace nemůže uživatel zasílat (protože je používá aplikace, která volá webové rozhraní API jménem sebe). Správce tenanta bude muset souhlasit s klientskými aplikacemi vašeho webového rozhraní API, aby vystavoval role aplikací. Podrobnosti najdete v tématu o [souhlasu správce](v2-admin-consent.md) .

### <a name="exposing-delegated-permissions-scopes"></a>Vystavení delegovaných oprávnění (rozsahy)

1. Vyberte **vystavení rozhraní API** v registraci aplikace.
1. Vyberte **Přidat obor**.
1. Pokud se zobrazí výzva, přijměte navržený identifikátor URI () ID aplikace `api://{clientId}` výběrem možnosti **Uložit a pokračovat**.
1. Zadejte tyto hodnoty:
    - Vyberte **název oboru** a zadejte **access_as_user**.
    - Vyberte **, kdo může souhlasit** a zda je vybrána možnost **Správci a uživatelé** .
    - Vyberte možnost **Zobrazovaný název souhlasu správce** a **jako uživatel zadejte přístup TodoListService**.
    - Vyberte **Popis souhlasu správce** a zadejte **přístup k webovému rozhraní API TodoListService jako uživatel**.
    - Vyberte **zobrazované jméno souhlasu uživatele** a **jako uživatel zadejte Access TodoListService**.
    - Vyberte **Popis souhlasu uživatele** a zadejte **přístup k webovému rozhraní API TodoListService jako uživatel**.
    - Ponechte hodnotu **stavu** nastavenou na **povoleno**.
 1. Vyberte **Přidat obor**.

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>Pokud je vaše webové rozhraní API voláno aplikací démona

V této části se dozvíte, jak zaregistrovat chráněné webové rozhraní API, aby ho aplikace typu démon mohli bezpečně volat.

- Deklarujete a zveřejňujete pouze *oprávnění aplikace* , protože aplikace typu démon nekomunikují s uživateli. Delegovaná oprávnění by nevedla smysl.
- Správci klientů můžou vyžadovat, aby služba Azure AD vydávala tokeny webového rozhraní API jenom aplikacím, které mají zaregistrovaný přístup k jednomu z oprávnění k aplikaci rozhraní API.

#### <a name="exposing-application-permissions-app-roles"></a>Vystavení oprávnění aplikace (aplikační role)

Chcete-li zveřejnit oprávnění aplikace, je nutné upravit manifest.

1. V registraci aplikace pro vaši aplikaci vyberte možnost **manifest**.
1. Chcete-li upravit manifest, vyhledejte `appRoles` nastavení a přidejte aplikační role. Definice rolí jsou k dispozici v následujícím ukázkovém bloku JSON.
1. Nechejte `allowedMemberTypes` nastavenou `"Application"` pouze na.
1. Ujistěte `id` se, že je jedinečný identifikátor GUID.
1. Ujistěte `displayName` se, že `value` neobsahují mezery.
1. Uložte manifest.

Následující příklad ukazuje obsah `appRoles` , kde hodnota `id` může být libovolný jedinečný identifikátor GUID.

```json
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

Webové rozhraní API kontroluje aplikační roli. Tato role představuje způsob, jak vystavit oprávnění aplikace pro vývojáře softwaru. Službu Azure AD můžete také nakonfigurovat tak, aby vydávala tokeny API pouze aplikacím, které správce tenanta schválí pro přístup přes rozhraní API.

Přidání tohoto zvýšeného zabezpečení:

1. Přejít na stránku **Přehled** aplikace pro registraci vaší aplikace
1. **V části spravovaná aplikace v místním adresáři** vyberte odkaz s názvem vaší aplikace. Popisek tohoto výběru může být oříznutý. Například se může zobrazit **spravovaná aplikace v...**

   > [!NOTE]
   >
   > Když vyberete tento odkaz, přejdete na stránku **Přehled podnikové aplikace** . Tato stránka je přidružená k instančnímu objektu vaší aplikace v tenantovi, kde jste ji vytvořili. Můžete přejít na stránku registrace aplikace pomocí tlačítka zpět v prohlížeči.

1. V části **Správa** na stránkách podnikové aplikace vyberte stránku **vlastností** .
1. Pokud chcete, aby služba Azure AD povolovala přístup k webovému rozhraní API jenom pro některé klienty, nastavte pro **přiřazení uživatele** hodnotu **Ano**.

   > [!IMPORTANT]
   >
   > Pokud jste nastavili **přiřazení uživatele jako povinné?** Pokud **Ano** , Azure AD zkontroluje přiřazení role aplikace klienta, když požádá o přístupový token webového rozhraní API. Pokud klient není přiřazený k rolím aplikace, Azure AD vrátí chybovou zprávu "invalid_client: AADSTS501051: aplikace není \<application name\> přiřazená k roli pro \<web API\> ".
   >
   > Pokud si zachováte **přiřazení uživatelů?** nastavit na **ne** , Azure AD nebude kontrolovat přiřazení role aplikace, když si klient vyžádá přístupový token pro vaše webové rozhraní API. Každý klient démona, což znamená, že každý klient používající tok přihlašovacích údajů klienta, může získat přístupový token pro rozhraní API jenom tak, že určí cílovou skupinu. Každá aplikace má přístup k rozhraní API bez nutnosti požádat o oprávnění.
   >
   > Jak je vysvětleno v předchozí části, vaše webové rozhraní API může vždy ověřit, zda má aplikace správnou roli, která je autorizována správcem tenanta. Rozhraní API provede toto ověření tím, že ověří, že přístupový token má deklaraci identity rolí a že hodnota této deklarace je správná. V předchozím příkladu JSON je hodnota `access_as_application` .

1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [Konfigurace kódu aplikace](scenario-protected-web-api-app-configuration.md).

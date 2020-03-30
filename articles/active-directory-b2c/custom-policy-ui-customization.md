---
title: Přizpůsobení uživatelského rozhraní aplikace pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Přečtěte si o přizpůsobení uživatelského rozhraní pomocí vlastních zásad ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 87c3a3a904705b9fcb702c4745c4c80a4b447e69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79476718"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Přizpůsobení uživatelského rozhraní aplikace pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Dokončením kroků v tomto článku vytvoříte vlastní zásady registrace a přihlášení se značkou a vzhledem. S Azure Active Directory B2C (Azure AD B2C) získáte téměř úplnou kontrolu nad obsahem HTML a CSS, který se zobrazí uživatelům. Při použití vlastní zásady nakonfigurujete přizpůsobení uživatelského rozhraní ve formátu XML namísto použití ovládacích prvků na webu Azure Portal.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [části Začínáme s vlastními zásadami](custom-policy-get-started.md). Měli byste mít funkční vlastní zásady pro registraci a přihlášení pomocí místních účtů.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

## <a name="4-modify-the-extensions-file"></a>4. Upravte soubor přípon

Chcete-li konfigurovat vlastní nastavení uživatelského rozhraní, zkopírujte **contentdefinition** a jeho podřízené prvky ze základního souboru do souboru přípon.

1. Otevřete základní soubor zásad. Například <em> `SocialAndLocalAccounts/` </em>. Tento základní soubor je jedním ze souborů zásad zahrnutých do počátečního balíčku vlastních zásad, který byste měli získat v předpokladu [Začínáme s vlastními zásadami](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Vyhledejte a zkopírujte celý obsah prvku **ContentDefinitions.**
1. Otevřete soubor přípony. Například *TrustFrameworkExtensions.xml*. Vyhledejte element **BuildingBlocks.** Pokud prvek neexistuje, přidejte jej.
1. Vložte celý obsah **ContentDefinitions** element, který jste zkopírovali jako podřízený **BuildingBlocks** element.
1. Vyhledejte element **ContentDefinition,** který obsahuje `Id="api.signuporsignin"` v xml, který jste zkopírovali.
1. Změňte hodnotu **LoadUri** na adresu URL souboru HTML, který jste nahráli do úložiště. Například, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Vaše vlastní zásady by měly vypadat jako následující fragment kódu:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Uložte soubor přípon.

## <a name="5-upload-and-test-your-updated-custom-policy"></a>5. Nahrání a testování aktualizovaných vlastních zásad

### <a name="51-upload-the-custom-policy"></a>5.1 Nahrání vlastních zásad

1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. Vyhledejte a vyberte **Azure AD B2C**.
1. V části **Zásady**vyberte **rozhraní Identity Experience Framework**.
1. Vyberte **Nahrát vlastní zásady**.
1. Nahrajte soubor přípon, který jste dříve změnili.

### <a name="52-test-the-custom-policy-by-using-run-now"></a>5.2 Testování vlastních zásad pomocí **funkce Spustit nyní**

1. Vyberte zásadu, kterou jste nahráli, a pak vyberte **Spustit nyní**.
1. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Konfigurace identifikátoru URI obsahu dynamické vlastní stránky

Pomocí vlastních zásad Azure AD B2C můžete odeslat parametr v cestě URL nebo řetězec dotazu. Předáním parametru do vašeho koncového bodu HTML můžete dynamicky měnit obsah stránky. Můžete například změnit obrázek pozadí na registrační nebo přihlašovací stránce Azure AD B2C na základě parametru, který předáte z vašeho webu nebo mobilní aplikace. Parametrem může být libovolný [překládání deklarací ,](claim-resolver-overview.md)například ID aplikace, ID jazyka nebo parametr vlastního řetězce dotazu, například `campaignId`.

### <a name="sending-query-string-parameters"></a>Odesílání parametrů řetězce dotazu

Chcete-li odeslat parametry řetězce dotazu, `ContentDefinitionParameters` přidejte do [zásad předávající strany](relyingparty.md)prvek, jak je znázorněno níže.

```XML
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

V definici obsahu změňte `LoadUri` `https://<app_name>.azurewebsites.net/home/unified`hodnotu aplikace . Vaše vlastní `ContentDefinition` zásady by měly vypadat jako následující fragment kódu:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Když Azure AD B2C načte stránku, provede volání koncového bodu webového serveru:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Identifikátor URI obsahu dynamické stránky

Obsah lze stáhnout z různých míst na základě použitých parametrů. V koncovém bodě s podporou CORS nastavte strukturu složek pro hostování obsahu. Obsah můžete například uspořádat do následující struktury. Kořenová *složka/složka podle jazyka/souborů html*. Identifikátor URI vlastní stránky může vypadat například takto:

```XML
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C odešle dvoupísmenný kód `fr` ISO pro jazyk pro francouzštinu:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Další kroky

Další informace o prvcích uživatelského rozhraní, které lze přizpůsobit, naleznete [v referenční příručce pro přizpůsobení uživatelského rozhraní pro toky uživatelů](customize-ui-overview.md).

---
title: Přizpůsobení uživatelského rozhraní aplikace pomocí vlastní zásady
titleSuffix: Azure AD B2C
description: Přečtěte si o přizpůsobení uživatelského rozhraní pomocí vlastních zásad v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 340c9629af89bfacb85b37503743fc5770070ae3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95990872"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Přizpůsobení uživatelského rozhraní aplikace pomocí vlastní zásady v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Provedením kroků v tomto článku vytvoříte vlastní zásadu registrace a přihlašování s vaší značkou a zobrazením. Pomocí Azure Active Directory B2C (Azure AD B2C) získáte téměř úplné řízení obsahu HTML a CSS, který je prezentován uživatelům. Když použijete vlastní zásadu, nakonfigurujete přizpůsobení uživatelského rozhraní v XML namísto použití ovládacích prvků v Azure Portal.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v části Začínáme [s vlastními zásadami](custom-policy-get-started.md). Měli byste mít pracovní vlastní zásady pro registraci a přihlašování pomocí místních účtů.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. Upravte soubor rozšíření.

Chcete-li nakonfigurovat přizpůsobení uživatelského rozhraní, zkopírujte **ContentDefinition** a jeho podřízené prvky ze základního souboru do souboru rozšíření.

1. Otevřete základní soubor zásad. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Tento základní soubor je jedním ze souborů zásad, které jsou součástí počáteční sady Custom Policy Pack, které byste měli mít k dispozici v rámci svých požadavků. Začněte [s vlastními zásadami](./custom-policy-get-started.md).
1. Vyhledejte a zkopírujte celý obsah elementu **ContentDefinitions** .
1. Otevřete soubor rozšíření. Například *TrustFrameworkExtensions.xml*. Vyhledejte element **BuildingBlocks** . Pokud element neexistuje, přidejte jej.
1. Vložte celý obsah elementu **ContentDefinitions** , který jste zkopírovali jako podřízený prvek **BuildingBlocks** elementu.
1. Vyhledejte element **ContentDefinition** , který obsahuje `Id="api.signuporsignin"` soubor ve formátu XML, který jste zkopírovali.
1. Změňte hodnotu **LoadUri** na adresu URL souboru HTML, který jste nahráli do úložiště. Například, `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Vaše vlastní zásada by měla vypadat jako následující fragment kódu:

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

1. Uložte soubor rozšíření.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. nahrávání a testování aktualizovaných vlastních zásad

#### <a name="51-upload-the-custom-policy"></a>5,1 nahrajte vlastní zásadu.

1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **Architektura prostředí identity**.
1. Vyberte **Odeslat vlastní zásadu**.
1. Nahrajte soubor rozšíření, který jste předtím změnili.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 otestujte vlastní zásady pomocí rutiny **spustit hned**

1. Vyberte zásady, které jste nahráli, a pak vyberte **Spustit nyní**.
1. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Konfigurovat dynamický identifikátor URI obsahu vlastní stránky

Pomocí Azure AD B2C vlastních zásad můžete odeslat parametr v cestě URL nebo v řetězci dotazu. Předáním parametru do vašeho koncového bodu HTML můžete dynamicky měnit obsah stránky. Můžete například změnit obrázek pozadí na registrační nebo přihlašovací stránce Azure AD B2C na základě parametru, který předáte z vašeho webu nebo mobilní aplikace. Parametr může být jakýkoli [překladač deklarací identity](claim-resolver-overview.md), jako je ID aplikace, ID jazyka nebo vlastní parametr řetězce dotazu, jako je například `campaignId` .

### <a name="sending-query-string-parameters"></a>Odesílají se parametry řetězce dotazu.

K odeslání parametrů řetězce dotazu v [zásadách předávající strany](relyingparty.md)přidejte `ContentDefinitionParameters` element, jak je znázorněno níže.

```xml
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

V definici obsahu změňte hodnotu `LoadUri` na `https://<app_name>.azurewebsites.net/home/unified` . Vaše vlastní zásada `ContentDefinition` by měla vypadat jako následující fragment kódu:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Když Azure AD B2C načte stránku, provede volání na koncový bod webového serveru:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Identifikátor URI dynamického obsahu stránky

Obsah lze z různých míst načíst na základě použitých parametrů. V rámci koncového bodu s povoleným CORS nastavte strukturu složek pro hostování obsahu. Můžete například uspořádat obsah v následující struktuře. Kořenová *Složka/složka pro jednotlivé jazyky/soubory HTML* Identifikátor URI vlastní stránky může vypadat například takto:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C pošle 2. kód ISO pro jazyk, `fr` pro francouzštinu:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Další kroky

Další informace o prvcích uživatelského rozhraní, které je možné přizpůsobit, najdete v [Referenční příručce pro přizpůsobení uživatelského rozhraní pro toky uživatelů](customize-ui-overview.md).
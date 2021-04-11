---
title: Řešení potíží s vlastními zásadami v Azure Active Directory B2C
description: Přečtěte si o přístupech k řešení chyb při práci s vlastními zásadami v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbb3bc0e34ad596c39aebb49124bb72d0b3efe6f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103905"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Řešení potíží s Azure AD B2C vlastními zásadami

Pokud používáte [vlastní zásady](custom-policy-overview.md)Azure Active Directory B2C (Azure AD B2C), může docházet k problémům s formátováním jazyka XML v zásadách nebo při běhových potížích. Tento článek popisuje některé nástroje a tipy, které vám pomůžou zjistit a vyřešit problémy. 

Tento článek se zaměřuje na řešení potíží s konfigurací vlastní zásady Azure AD B2C. Neřeší aplikaci předávající strany ani její knihovnu identit.

## <a name="azure-ad-b2c-correlation-id-overview"></a>Přehled ID korelace Azure AD B2C

ID korelace Azure AD B2C je jedinečný identifikátor, který je připojený k žádostem o autorizaci. Projde všemi kroky orchestrace, které uživatel provedl. S ID korelace můžete:

- Identifikujte přihlašovací aktivitu v aplikaci a sledujte výkon vašich zásad.
- Najděte protokoly Application Insights pro žádosti o přihlášení.
- Předejte své REST API korelační ID a použijte ho k identifikaci přihlašovacího toku. 

ID korelace se změní pokaždé, když se naváže nová relace. Při ladění zásad se ujistěte, že jste zavřeli existující karty prohlížeče. Nebo otevřete nový prohlížeč v privátním režimu.

### <a name="get-the-azure-ad-b2c-correlation-id"></a>Získat ID korelace Azure AD B2C

ID korelace najdete na stránce Azure AD B2C registrace nebo přihlášení. V prohlížeči vyberte možnost **Zobrazit zdroj**. Korelace se zobrazí jako komentář v horní části stránky.

![Snímek obrazovky Azure AD B2C zdroj zobrazení stránky pro přihlášení](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

Zkopírujte ID korelace a potom pokračujte v toku přihlášení. Pomocí ID korelace můžete sledovat chování přihlášení. Další informace najdete v tématu [řešení potíží s Application Insights](#troubleshooting-with-application-insights).

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>Echo ID korelace Azure AD B2C

Do tokenů Azure AD B2C můžete zahrnout ID korelace. Zahrnutí ID korelace:

1. Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud element neexistuje, přidejte jej.
1. Vyhledejte element [ClaimsSchema](claimsschema.md) . Pokud element neexistuje, přidejte jej.
1. Přidejte deklaraci identity města do elementu **ClaimsSchema** .  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. Otevřete soubor předávající strany vašich zásad. Například <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> soubor. Po úspěšné cestě uživatele a odeslání do aplikace se do tokenu přidá výstupní deklarace identity. Upravte prvek Technical Profile v části předávající strany a přidejte město jako výstupní deklaraci identity.
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Řešení potíží s Application Insights

K diagnostice problémů s vlastními zásadami použijte [Application Insights](troubleshoot-with-application-insights.md). Application Insights sleduje aktivitu uživatelské cesty pro vlastní zásady. Poskytuje způsob, jak diagnostikovat výjimky a sledovat výměnu deklarací identity mezi Azure AD B2C a různými poskytovateli deklarací identity, které jsou definovány technickými profily, jako jsou zprostředkovatelé identit, služby založené na rozhraní API, Azure AD B2C adresář uživatelů a další služby.  

Pro [vs Code](https://code.visualstudio.com/)doporučujeme nainstalovat [rozšíření Azure AD B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) . S rozšířením Azure AD B2C jsou protokoly uspořádány podle názvu zásad, ID korelace (Application Insights prezentuje první číslici ID korelace) a časové razítko protokolu. Tato funkce vám pomůže najít relevantní protokol založený na místním časovém razítku a sledovat cestu uživatele spouštěnou pomocí Azure AD B2C. 

> [!NOTE]
> - Předtím, než budete moci zobrazit nové protokoly v Application Insights, existuje krátké zpoždění, obvykle méně než pět minut.
> - Komunita vyvinula rozšíření Visual Studio Code pro Azure AD B2C, která vývojářům identity pomůžou. Rozšíření není podporováno společností Microsoft a je zpřístupněno výhradně tak, jak je.

Tok jednotného přihlašování může vydávat více než jedno trasování Azure Application Insights. Na následujícím snímku obrazovky má zásada *B2C_1A_signup_signin* tři protokoly. Každý protokol představuje část toku přihlášení.

Následující snímek obrazovky ukazuje rozšíření Azure AD B2C pro VS Code s Průzkumníkem trasování Azure Application Insights.

![Snímek obrazovky s rozšířením Azure AD B2C pro VS Code se službou Azure Application Insights Trace.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>Filtrování protokolu trasování

Když se zaměříte na Azure AD B2C Průzkumníku trasování, začněte psát první číslici ID korelace nebo čas, který chcete najít. Zobrazí se pole filtru v pravém horním rohu okna Azure AD B2C trasování, které ukazuje, co jste nastavili, a zvýrazní se shodné protokoly trasování.  

![Snímek obrazovky s rozšířením pro Azure AD B2C Azure AD B2C Průzkumník trasování pro zvýraznění.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

Když najedete myší na pole filtru a vyberete **Povolit filtr u typu** , zobrazí se jenom vyhovující protokoly trasování. K vymazání filtru použijte **tlačítko "X" Clear** .

![Snímek obrazovky s rozšířením Azure AD B2C Azure AD B2C filtru Průzkumníka trasování.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Podrobnosti protokolu trasování Application Insights

Když vyberete trasování Azure Application Insights, rozšíření otevře okno **podrobnosti Application Insights** s následujícími informacemi:

- **Application Insights** – Obecné informace o protokolu trasování, včetně názvu zásady, ID korelace, ID trasování Azure Application Insights a časového razítka trasování.
- **Technické profily** – seznam technických profilů, které se zobrazí v protokolu trasování.
- **Deklarace identity** – Abecední seznam deklarací identity, které se zobrazují v protokolu trasování a jejich hodnoty. Pokud se deklarace identity objeví v protokolu trasování víckrát s různými hodnotami, `=>` označí podpis nejnovější hodnotu. Tyto deklarace si můžete prohlédnout a zjistit, jestli jsou správně nastavené očekávané hodnoty deklarace identity. Pokud máte například předběžnou podmínku, která kontroluje hodnotu deklarace identity, část deklarace identity vám může pomoct určit, proč se očekávaný tok chová jinak.
- **Transformuje deklarace** – seznam transformací deklarací identity, které se zobrazují v protokolu trasování. Každá transformace deklarací obsahuje vstupní deklarace identity, vstupní parametry a výstupní deklarace identity. Část transformace deklarací identity poskytuje přehled o datech odesílaných a výsledku transformace deklarací identity.
- **Tokeny** – seznam tokenů, které se zobrazí v protokolu trasování. Tokeny zahrnují základní tokeny federovaného OAuth a OpenId Connect identity Provider. Token federovaného zprostředkovatele identity poskytuje podrobné informace o tom, jak poskytovatel identity vrací deklarace identity Azure AD B2C, takže můžete namapovat výstupní deklarace identity technického profilu zprostředkovatele identity. 
- **Výjimky** – seznam výjimek nebo závažných chyb, které se zobrazí v protokolu trasování.
- **Application Insights JSON** – nezpracovaná data se vrátí z Application Insights.

Následující snímek obrazovky ukazuje příklad okna podrobnosti protokolu trasování Application Insights.  

![Snímek obrazovky Azure AD B2C rozšíření Azure AD B2Cho trasování.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>Řešení potíží s tokeny JWT

Pro účely ověření tokenu JWT a jejich ladění může dekódovat JWTs pomocí webu, jako je [https://jwt.ms](https://jwt.ms) . Vytvořte testovací aplikaci, která se může přesměrovat na `https://jwt.ms` pro kontrolu tokenu. Pokud jste to ještě neudělali, [Zaregistrujte webovou aplikaci](tutorial-register-applications.md)a [Povolte implicitní udělení tokenu ID](tutorial-register-applications.md#enable-id-token-implicit-grant). 

![Snímek obrazovky verze Preview tokenu JWT](./media/troubleshoot-custom-policies/jwt-token-preview.png)

Pomocí rutiny **Spustit nyní** a `https://jwt.ms` otestujte zásady nezávisle na vaší webové nebo mobilní aplikaci. Tento web funguje jako aplikace předávající strany. Zobrazuje obsah tokenu JWT (JSON web token), který vygenerovala vaše zásada Azure AD B2C.

## <a name="troubleshoot-saml-protocol"></a>Řešení potíží s protokolem SAML

K usnadnění konfigurace a ladění integrace s vaším poskytovatelem služeb můžete použít rozšíření prohlížeče pro protokol SAML, například [rozšíření SAML devtools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) pro Chrome, [trasování SAML](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) pro Firefox nebo [vývojové nástroje pro vývojáře v IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Následující snímek obrazovky ukazuje, jak DevTools rozšíření SAML prezentuje požadavek SAML Azure AD B2C odesílá poskytovateli identity a odpověď SAML.

![Snímek obrazovky protokolu trasování protokolu SAML.](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

Pomocí těchto nástrojů můžete kontrolovat integraci mezi aplikací a Azure AD B2C. Například:

- Zkontrolujte, zda požadavek SAML obsahuje signaturu a určíte, jaký algoritmus se má použít k přihlášení k žádosti o autorizaci.
- Ověřte, zda Azure AD B2C vrátí chybovou zprávu.
- Ověřte, zda je oddíl kontrolního výrazu zašifrovaný.
- Získat název deklarací identity vrátí poskytovatele identity.

Pomocí [Fiddler](https://www.telerik.com/fiddler)můžete také sledovat výměnu zpráv mezi prohlížečem klienta a Azure AD B2C. Může vám získat informace o tom, kde se v krocích orchestrace vaše uživatelská cesta nedaří.

## <a name="troubleshoot-policy-validity"></a>Řešení potíží s platností zásad

Po dokončení vývoje zásady nahrajete zásadu pro Azure AD B2C. u vaší zásady můžou nastat nějaké problémy. K zajištění integrity a platnosti zásad použijte následující metody.

Nejběžnější Chyba při nastavování vlastních zásad je nesprávně naformátované XML. Dobrý editor XML je skoro nezbytný. Zobrazuje XML nativně, obsah barevných kódů, předem vyplní běžné výrazy, uchovává elementy XML indexované a může ověřit proti schématu XML.

Doporučujeme použít [Visual Studio Code](https://code.visualstudio.com/). Pak nainstalujte rozšíření XML, jako je například [Podpora jazyka XML, Red Hat](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml). Přípona XML umožňuje ověřit schéma XML před nahráním souboru XML pomocí definice schématu [XSD](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) vlastní zásady.

Pomocí strategie přidružení souborů XML můžete vytvořit vazbu souboru XML, který bude XSD, přidáním následujících nastavení do `settings.json` souboru vs Code. Postupujte následovně:

1. V VS Code klikněte na **Nastavení**. Další informace najdete v tématu [nastavení pro uživatele a pracovní prostor](https://code.visualstudio.com/docs/getstarted/settings).
1. Vyhledejte **přidružení** souborů a potom v části **rozšíření** vyberte **XML**.
1. Vyberte **Upravit v settings.jszapnuto**.

    ![Snímek obrazovky VS Code ověřování schématu XML.](./media/troubleshoot-custom-policies/xml-validation.png)
1. V settings.jsna přidejte následující kód JSON:

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

Následující příklad ukazuje chybu ověřování XML. Když přesunete ukazatel myši na název elementu, rozšíření vypíše očekávané prvky.

![Snímek obrazovky s indikátorem chyby ověřování schématu XML VS Code.](./media/troubleshoot-custom-policies/xml-validation-error.png)

V následujícím případě `DisplayName` je prvek správný. Ale v nesprávném pořadí. `DisplayName`By měl být před `Protocol` elementem. Chcete-li problém vyřešit, přesuňte ukazatel myši nad `DisplayName` prvek do správného pořadí prvků.

![Snímek obrazovky VS Code chyba pořadí ověřování schématu XML.](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>Nahrávání zásad a ověřování zásad

Ověření souboru zásad XML se provádí automaticky při nahrání. Většina chyb způsobí selhání nahrávání. Ověření zahrnuje soubor zásad, který nahráváte. Zahrnuje také řetězec souborů, na které se nahrát soubor odkazuje (soubor zásad předávající strany, soubor rozšíření a základní soubor).

> [!TIP]
> Azure AD B2C spustí další ověřování pro zásady předávající strany. Při potížích se zásadami, a to i v případě, že upravíte jenom zásady rozšíření, je dobrým zvykem nahrát taky zásady předávající strany. 

Tato část obsahuje běžné chyby ověřování a pravděpodobná řešení.

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>Zjistila se chyba ověřování schématu... má neplatný podřízený element {Name}.

Vaše zásada obsahuje neplatný element XML, nebo je element XML platný, ale zdá se, že je v nesprávném pořadí. Pokud chcete tento typ chyby opravit, podívejte se do části [řešení potíží s zásadami platnosti](#troubleshoot-policy-validity) .

### <a name="there-is-a-duplicate-key-sequence-number"></a>Existuje duplicitní sekvence klíčů {Number}. 

[Cesta](userjourneys.md) uživatele nebo [Podřízená cesta](subjourneys.md) se skládá z uspořádaného seznamu kroků orchestrace, které se spustí v posloupnosti. Po změně cesty přepište kroky postupně, aniž by bylo vynecháno celé číslo od 1 do N.

> [!TIP]
> [](https://code.visualstudio.com/) K přečíslování všech [](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) `(Shift+Ctrl+r)` kroků orchestrace uživatelů a dílčích cest v zásadách můžete použít Azure AD B2C rozšíření pro vs Code příkaz.

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>... očekávalo se, že má krok s pořadím {Number}, ale nenašel se...

Podívejte se na předchozí chybu.

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>Pořadí kroků orchestrace {Number} v cestě uživatele {Name}... následuje krok výběru zprostředkovatele deklarací identity a musí se jednat o výměnu deklarací identity, ale je typu...

Typ kroků orchestrace `ClaimsProviderSelection` a `CombinedSignInAndSignUp` obsahuje seznam možností, ze kterých si uživatel může vybrat. Musí následovat po typu `ClaimsExchange` s jedním nebo více výměnou deklarací identity.

Následující kroky orchestrace způsobují tento typ nebo chybu. Druhý krok orchestrace musí být typu `ClaimsExchange` , ne `ClaimsProviderSelection` .

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>... Krok {Number} se 2 výměnou deklarací identity. Před tím, než se dá určit, kterou výměnu deklarací identity je možné použít, musí předcházet výběr poskytovatele deklarací identity.

Typ kroku orchestrace `ClaimsExchange` musí mít jeden `ClaimsExchange` , pokud předchozí krok není typu `ClaimsProviderSelection` nebo `CombinedSignInAndSignUp` . Následující kroky orchestrace způsobují tento typ chyby. Šestý krok obsahuje dvě výměny deklarací identity. 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Pokud chcete tento typ chyby opravit, použijte dva kroky orchestrace. Každý krok orchestrace s jedním výměnou deklarací identity.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>Existuje duplicitní sekvence klíčů {Name}.

Cesta má více `ClaimsExchange` stejných `Id` . Následující kroky způsobují tento typ chyby. ID *AADUserWrite* se v cestě uživatele zobrazuje dvakrát.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Chcete-li tento typ chyby vyřešit, změňte osmý postup orchestrace deklarací identity na jedinečný název, jako je například *volání rozhraní REST API*.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>... vytvoří odkaz na třídu ClaimType s ID {název deklarace identity}, ale ani zásady, ani žádná z jeho základních zásad tyto prvky neobsahují.

Tento typ chyby se stane, když vaše zásada vytvoří odkaz na deklaraci identity, která není deklarovaná ve [schématu deklarací identity](claimsschema.md). Deklarace identity musí být definované aspoň v jednom ze souborů v zásadě. 

Například technický profil s *schoolId* výstupní deklarací identity. Ale výstupní *schoolId* deklarace identity se nikdy nedeklaruje v zásadě ani v zásadách nadřazených členů.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

Chcete-li tento typ chyby opravit, ověřte `ClaimTypeReferenceId` , zda je hodnota chybně napsaná nebo ve schématu neexistuje. Pokud je deklarace identity definovaná v zásadách rozšíření, ale používá se taky v základní zásadě. Ujistěte se, že je deklarace identity definovaná v zásadě, kterou používá, nebo v zásadě nejvyšší úrovně.

Přidání deklarace identity do schématu deklarací vyřeší tento typ chyby.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>... vytvoří odkaz na ClaimsTransformation s ID...

Příčinou této chyby je podobná chyba deklarace identity. Podívejte se na předchozí chybu.

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>Uživatel je aktuálně přihlášen jako uživatel tenanta ' yourtenant.onmicrosoft.com '...

Přihlásíte se pomocí účtu z klienta, který se liší od zásad, které se pokoušíte nahrát. Například se přihlašujete pomocí admin@contoso.onmicrosoft.com , zatímco vaše zásada `TenantId` je nastavena na `fabrikam.onmicrosoft.com` .

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- Ověřte, zda je `TenantId` hodnota v `<TrustFrameworkPolicy\>` `<BasePolicy\>` elementech a shodná s cílovým Azure AD B2C tenant.

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>Typ deklarace identity {Name} je výstupní deklarací technického profilu předávající strany, ale není to výstupní deklarace v žádném z kroků cesty pro uživatele...

V zásadách předávající strany jste přidali výstupní deklaraci identity, ale výstupní deklarace identity není výstupní deklarací v žádném z kroků cesty uživatelů. Azure AD B2C nemůže přečíst hodnotu deklarace identity z kontejneru deklarací identity.

V následujícím příkladu je deklarace identity *schoolId* výstupní deklarací technického profilu předávající strany, ale nejedná se o deklaraci identity v žádném z kroků cesty uživatele *SignUpOrSignIn* .

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

Pokud chcete tento typ chyby opravit, ujistěte se, že se výstupní deklarace identity zobrazovaly minimálně v jednom kroku orchestrace Technical Profile výstupní kolekce deklarací. Pokud vaše cesta uživatele nemůže zastavovat deklaraci identity, nastavte v technickém profilu předávající strany výchozí hodnotu, jako je například prázdný řetězec.  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>Vstupní řetězec nemá správný formát.

Nastavíte nesprávný typ hodnoty na deklaraci identity z jiného typu. Můžete například definovat celočíselnou deklaraci identity.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

Pak se pokusíte nastavit řetězcovou hodnotu:

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

Chcete-li tento typ chyby opravit, ujistěte se, že jste nastavili správnou hodnotu, například `DefaultValue="0"` .


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>Tenant {Name} už má zásadu s ID {Name}. Nelze uložit jinou zásadu se stejným ID.

Pokusíte se o nahrání zásady pro vašeho tenanta, ale do vašeho tenanta se už nahrála zásada se stejným názvem. 

Pokud chcete tento typ chyby vyřešit, vyberte při nahrávání zásady možnost **Přepsat vlastní zásadu, pokud už existuje** .

![Snímek obrazovky, který ukazuje, jak přepsat vlastní zásady, pokud už existuje.](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>Další kroky

- Naučte [se shromažďovat protokoly Azure Active Directory B2C pomocí Application Insights](troubleshoot-with-application-insights.md).


---
title: Řešení potíží s vlastními zásadami pomocí Application Insights
titleSuffix: Azure AD B2C
description: Jak nastavit Application Insights pro trasování provádění vašich vlastních zásad.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4a68b492bad4ac091b4600c9ec81ac0de27cc05
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572899"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Shromažďovat protokoly Azure Active Directory B2C pomocí Application Insights

Tento článek popisuje kroky pro shromažďování protokolů z Active Directory B2C (Azure AD B2C), abyste mohli diagnostikovat problémy s vlastními zásadami. Application Insights poskytuje způsob, jak diagnostikovat výjimky a vizualizovat problémy s výkonem aplikací. Azure AD B2C obsahuje funkci pro posílání dat Application Insights.

Podrobné protokoly aktivit popsané tady by měly být povolené **jenom** během vývoje vlastních zásad.

> [!WARNING]
> `DeploymentMode` `Development` V produkčních prostředích nenastavujte na. Protokoly shromažďují všechny deklarace identity odeslané do a od zprostředkovatelů identity. Jako vývojář se předpokládá zodpovědnost za jakékoli osobní údaje shromážděné v protokolech Application Insights. Tyto podrobné protokoly se shromažďují jenom v případě, že zásady jsou umístěné v **režimu pro vývojáře**.

## <a name="set-up-application-insights"></a>Nastavit Application Insights

Pokud ho ještě nemáte, vytvořte v předplatném instanci Application Insights.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vaše předplatné Azure (ne váš Azure AD B2C adresář).
1. V navigační nabídce vlevo vyberte **vytvořit prostředek** .
1. Vyhledejte a vyberte **Application Insights** a pak vyberte **vytvořit**.
1. Vyplňte formulář, vyberte **zkontrolovat + vytvořit** a pak vyberte **vytvořit**.
1. Po dokončení nasazení vyberte **Přejít k prostředku**.
1. V nabídce **Konfigurovat** v Application Insights vyberte **vlastnosti**.
1. Poznamenejte si **klíč instrumentace** pro použití v pozdějším kroku.

## <a name="configure-the-custom-policy"></a>Konfigurace vlastních zásad

1. Otevřete soubor předávající strany (RP), například *SignUpOrSignin.xml*.
1. Do elementu přidejte následující atributy `<TrustFrameworkPolicy>` :

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Pokud ještě neexistuje, přidejte `<UserJourneyBehaviors>` do uzlu podřízený uzel `<RelyingParty>` . Musí se nacházet hned po `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Přidejte následující uzel jako podřízený `<UserJourneyBehaviors>` prvek elementu. Nezapomeňte nahradit `{Your Application Insights Key}` **klíč instrumentace** Application Insights, který jste si poznamenali dříve.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` dává ApplicationInsights pokyn k urychlení telemetrie prostřednictvím kanálu zpracování. Vhodné pro vývoj, ale omezení na vysoké objemy. V produkčním prostředí nastavte `DeveloperMode` na `false` .
    * `ClientEnabled="true"` pošle skript ApplicationInsights na straně klienta pro sledování zobrazení stránky a chyby na straně klienta. Můžete je zobrazit v tabulce **browserTimings** na portálu Application Insights. Nastavením `ClientEnabled= "true"` přidáte Application Insights do skriptu stránky a získáte časování načtení stránky a volání AJAX, počty, podrobnosti výjimek prohlížeče a selhání AJAX a počty uživatelů a relací. Toto pole je **volitelné** a je nastavené na `false` výchozí hodnotu.
    * `ServerEnabled="true"` odešle existující UserJourneyRecorder JSON jako vlastní událost pro Application Insights.

    Příklad:

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Nahrajte zásadu.

## <a name="see-the-logs-in-application-insights"></a>Podívejte se na protokoly v Application Insights

Předtím, než budete moci zobrazit nové protokoly v Application Insights, existuje krátké zpoždění, obvykle méně než pět minut.

1. Otevřete prostředek Application Insights, který jste vytvořili v [Azure Portal](https://portal.azure.com).
1. Na stránce **Přehled** vyberte **protokoly**.
1. Otevřete novou kartu v Application Insights.

Tady je seznam dotazů, které můžete použít k zobrazení protokolů:

| Dotaz | Description |
|---------------------|--------------------|
`traces` | Zobrazit všechny protokoly vygenerované Azure AD B2C |
`traces | where timestamp > ago(1d)` | Zobrazit všechny protokoly vygenerované Azure AD B2C za poslední den

Položky mohou být dlouhé. Pro lepší vzhled exportujte do sdíleného svazku clusteru.

Další informace o dotazování najdete v tématu [Přehled dotazů protokolu v Azure monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="configure-application-insights-in-production"></a>Konfigurace Application Insights v produkčním prostředí

Pro zlepšení výkonu produkčního prostředí a lepší zkušenosti uživatelů je důležité nakonfigurovat zásady tak, aby ignorovaly neimportované zprávy. Pomocí následující konfigurace pošlete do Application Insights jenom kritické chybové zprávy. 

1. Nastavte `DeploymentMode` atribut [TrustFrameworkPolicy](trustframeworkpolicy.md) na `Production` . 

   ```xml
   <TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_signup_signin"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin"
   DeploymentMode="Production"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights">
   ```

1. Nastavte `DeveloperMode` [JourneyInsights](relyingparty.md#journeyinsights) na `false` .

   ```xml
   <UserJourneyBehaviors>
     <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   </UserJourneyBehaviors>
   ```
   
1. Nahrajte a otestujte vaše zásady.

## <a name="next-steps"></a>Další kroky

Komunita vyvinula uživatele prohlížeče cest, aby usnadnila vývojářům identity. Čte z vaší instance Application Insights a poskytuje dobře strukturovaný pohled na události cest uživatelů. Získáte zdrojový kód a nasadíte ho ve svém vlastním řešení.

Microsoft nepodporuje přehrávač pro cestu uživatelů a je k dispozici výhradně tak, jak je.

Verzi prohlížeče, která čte události z Application Insights na GitHubu, najdete tady:

[Azure-Samples/Active-Directory-B2C-Advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

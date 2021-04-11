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
ms.date: 04/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074bffb8614be1f71ba1956fd5a238bc19354c58
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028728"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Shromažďovat protokoly Azure Active Directory B2C pomocí Application Insights

Tento článek popisuje kroky pro shromažďování protokolů z Active Directory B2C (Azure AD B2C), abyste mohli diagnostikovat problémy s vlastními zásadami. Application Insights nabízí způsob, jak diagnostikovat výjimky a vizualizovat problémy s výkonem aplikací. Azure AD B2C obsahuje funkci umožňující odesílat data do Application Insights.

Podrobné protokoly aktivit popsané tady by měly být povolené **jenom** během vývoje vlastních zásad.

> [!WARNING]
> `DeploymentMode` `Development` V produkčních prostředích nenastavujte na. Protokoly shromažďují všechny deklarace identity odeslané do a od zprostředkovatelů identity. Jako vývojář se předpokládá zodpovědnost za jakékoli osobní údaje shromážděné v protokolech Application Insights. Tyto podrobné protokoly se shromažďují jenom v případě, že zásady jsou umístěné v **režimu pro vývojáře**.

## <a name="set-up-application-insights"></a>Nastavit Application Insights

Pokud ho ještě nemáte, vytvořte v předplatném instanci Application Insights.

> [!TIP]
> Jednu instanci Application Insights lze použít pro více klientů Azure AD B2C. Potom můžete v dotazu filtrovat podle tenanta nebo podle názvu zásady. Další informace najdete v části [protokoly v Application Insights](#see-the-logs-in-application-insights) Samples.

Pokud chcete ve svém předplatném použít ukončení instance Application Insights, postupujte takto:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vaše předplatné Azure (ne váš Azure AD B2C adresář).
1. Otevřete prostředek Application Insights, který jste vytvořili dříve.
1. Na stránce **Přehled** a zaznamenejte **klíč instrumentace** .

Pokud chcete ve svém předplatném vytvořit instanci Application Insights, postupujte podle těchto kroků:

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

1. Pokud ještě neexistuje, přidejte `<UserJourneyBehaviors>` do uzlu podřízený uzel `<RelyingParty>` . Musí být umístěn po `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Přidejte následující uzel jako podřízený `<UserJourneyBehaviors>` prvek elementu. Nezapomeňte nahradit `{Your Application Insights Key}` **klíč instrumentace** Application Insights, který jste si poznamenali dříve.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` dává ApplicationInsights pokyn k urychlení telemetrie prostřednictvím kanálu zpracování. Vhodné pro vývoj, ale omezení na vysoké objemy. V produkčním prostředí nastavte `DeveloperMode` na `false` .
    * `ClientEnabled="true"` pošle skript ApplicationInsights na straně klienta pro sledování zobrazení stránky a chyby na straně klienta. Můžete je zobrazit v tabulce **browserTimings** na portálu Application Insights. Nastavením `ClientEnabled= "true"` přidáte Application Insights do skriptu stránky a získáte časování načtení stránky a volání AJAX, počty, podrobnosti výjimek prohlížeče a selhání AJAX a počty uživatelů a relací. Toto pole je **volitelné** a je nastavené na `false` výchozí hodnotu.
    * `ServerEnabled="true"` odešle existující UserJourneyRecorder JSON jako vlastní událost pro Application Insights.

    Například:

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
| `traces` | Získat všechny protokoly vygenerované Azure AD B2C |
| `traces | where timestamp > ago(1d)` | Načte všechny protokoly vygenerované Azure AD B2C za poslední den.|
| `traces | where message contains "exception" | where timestamp > ago(2h)`|  Načte všechny protokoly s chybami za poslední dvě hodiny.|
| `traces | where customDimensions.Tenant == "contoso.onmicrosoft.com" and customDimensions.UserJourney  == "b2c_1a_signinandup"` | Získejte všechny protokoly generované Azure AD B2C tenant *contoso.onmicrosoft.com* a cestu uživatele *b2c_1a_signinandup*. |
| `traces | where customDimensions.CorrelationId == "00000000-0000-0000-0000-000000000000"`| Získá všechny protokoly generované Azure AD B2C pro ID korelace. Nahraďte ID korelace vaším ID korelace. | 

Položky mohou být dlouhé. Pro lepší vzhled exportujte do sdíleného svazku clusteru.

Další informace o dotazování najdete v tématu [Přehled dotazů protokolu v Azure monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-logs-in-vs-code-extension"></a>Zobrazit VS Code rozšíření protokolů

Doporučujeme, abyste nainstalovali [rozšíření Azure AD B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) pro [vs Code](https://code.visualstudio.com/). S rozšířením Azure AD B2C jsou protokoly seřazené podle názvu zásady, ID korelace (Application Insights prezentuje první číslici ID korelace) a časové razítko protokolu. Tato funkce vám pomůže najít relevantní protokol založený na místním časovém razítku a zobrazit cestu uživatele spouštěnou Azure AD B2C.

> [!NOTE]
> Komunita vyvinula rozšíření vs Code, které Azure AD B2C pomáhat vývojářům identity. Rozšíření není podporováno společností Microsoft a je zpřístupněno výhradně tak, jak je.

### <a name="set-application-insights-api-access"></a>Nastavení přístupu k rozhraní API Application Insights

Až nastavíte Application Insights a nakonfigurujete vlastní zásady, budete potřebovat získat **ID rozhraní api** Application Insights a vytvořit **klíč rozhraní API**. ID rozhraní API a klíč rozhraní API používá rozšíření Azure AD B2C k načtení Application Insightsch událostí (telemetrií). Klíče rozhraní API by se měly spravovat jako hesla. Udržujte si tajný klíč.

> [!NOTE]
> Application Insights klíč instrumentace, který jste vytvořili dříve, používá Azure AD B2C k posílání telemetrií do Application Insights. Klíč instrumentace použijete pouze v zásadách Azure AD B2C, nikoli v rozšíření vs Code.

Získání ID a klíče Application Insights:

1. V Azure Portal otevřete prostředek Application Insights pro vaši aplikaci.
1. Vyberte **Nastavení** a pak vyberte **přístup přes rozhraní API**.
1. Kopírovat **ID aplikace**
1. Vyberte **vytvořit klíč rozhraní API** .
1. Zaškrtněte políčko **pro čtení telemetrie** .
1. Před zavřením okna klíče pro vytvoření rozhraní API zkopírujte **klíč** a uložte ho někam do zabezpečení. Pokud klíč ztratíte, budete muset vytvořit další.

    ![Snímek obrazovky, který ukazuje, jak vytvořit přístupový klíč rozhraní API](./media/troubleshoot-with-application-insights/application-insights-api-access.png)

### <a name="set-up-azure-ad-b2c-vs-code-extension"></a>Nastavení rozšíření Azure AD B2C VS Code

Teď máte ID a klíč rozhraní API služby Azure Application Insights, můžete nakonfigurovat rozšíření vs Code, abyste mohli protokoly číst. Rozšíření Azure AD B2C VS Code poskytuje dva obory pro nastavení:

- **Globální nastavení uživatele** – nastavení, která se použijí globálně pro jakoukoli instanci vs Code, kterou otevřete.
- **Nastavení pracovního prostoru** – nastavení uložená v pracovním prostoru a platí jenom v případě, že je pracovní prostor otevřený (pomocí vs Code **otevřené složky**).

1. V Průzkumníku **trasování Azure AD B2C** klikněte na ikonu **Nastavení** .

    ![Snímek obrazovky, který ukazuje výběr nastavení Application Insights](./media/troubleshoot-with-application-insights/app-insights-settings.png)

1. Zadejte **ID** a **klíč** Azure Application Insights.
1. Klikněte na **Uložit**.

Po uložení nastavení se protokoly Application Insights zobrazí v okně **Azure AD B2C trasování (App Insights)** .

![Snímek obrazovky s rozšířením Azure AD B2C pro VSCode, který prezentuje trasování Azure Application Insights.](./media/troubleshoot-with-application-insights/vscode-extension-application-insights-trace.png)


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

- Naučte se [řešit potíže s Azure AD B2C vlastními zásadami](troubleshoot-custom-policies.md)

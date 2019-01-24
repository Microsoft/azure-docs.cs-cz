---
title: Application Insights a řešení potíží se zásadami vlastní v Azure Active Directory B2C | Dokumentace Microsoftu
description: jak k nastavení Application Insights pro sledování spuštění vlastních zásad.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 814ae29d4b50ecb44db45534c5d2a6bfb5b97a59
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54846502"
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: Shromažďování protokolů

Tento článek popisuje kroky pro shromažďování protokolů z Azure AD B2C, takže můžete diagnostikovat problémy s vlastními zásadami.

>[!NOTE]
>V současné době jsou navrženy protokoly podrobné aktivit je zde popsáno, **pouze** na podporu při vývoji vlastních zásad. Nepoužívejte režimu pro vývoj v produkčním prostředí.  Protokoly shromažďovat všechny deklarace identity posílané do a z zprostředkovatelé identity během vývoje.  Je-li použít v produkčním prostředí, vývojář zodpovědnost za identifikovatelné osobní údaje (soukromě údaje) shromážděné v protokolu App Insights, které vlastní.  Tyto podrobné protokoly jsou shromážděna, pouze když zásady se přikládá **VÝVOJOVÝ režim**.


## <a name="use-application-insights"></a>Pomocí služby Application Insights

Azure AD B2C podporuje funkce pro odesílání dat do služby Application Insights.  Application Insights umožňuje diagnostikovat výjimky a vizualizovat problémy s výkonem aplikací.

### <a name="setup-application-insights"></a>Nainstalovat Application Insights

1. Přejděte na [Azure Portal](https://portal.azure.com). Ujistěte se, že máte v tenantovi vašeho předplatného Azure (ne vašeho tenanta Azure AD B2C).
1. Klikněte na tlačítko **+ nová** v navigační nabídce vlevo.
1. Vyhledejte a vyberte **Application Insights**, pak klikněte na tlačítko **vytvořit**.
1. Vyplňte formulář a klikněte na tlačítko **vytvořit**. Vyberte **Obecné** pro **typ aplikace**.
1. Po vytvoření prostředku, otevřete prostředek Application Insights.
1. Najít **vlastnosti** v levé nabídce a klepněte na něj.
1. Kopírovat **Instrumentační klíč** a uložte jej pro další části.

### <a name="set-up-the-custom-policy"></a>Nastavení vlastních zásad

1. Otevřete soubor RP (například SignUpOrSignin.xml).
1. Přidání následujících atributů pro `<TrustFrameworkPolicy>` element:

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Pokud již neexistuje, přidejte podřízený uzel `<UserJourneyBehaviors>` k `<RelyingParty>` uzlu. Musí být umístěné bezprostředně po `<DefaultUserJourney ReferenceId="UserJourney Id from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`
2. Přidejte následující uzel jako podřízený objekt `<UserJourneyBehaviors>` elementu. Nezapomeňte nahradit `{Your Application Insights Key}` s **Instrumentační klíč** , který jste získali z Application Insights v předchozí části.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` říká ApplicationInsights ohledně urychlení jejich zpracování telemetrická data prostřednictvím kanálu zpracování kvalitní pro vývoj, ale omezené na velké objemy.
  * `ClientEnabled="true"` odešle skript na straně klienta ApplicationInsights pro sledování chyb zobrazení a na straně klienta stránky (nejsou potřeba).
  * `ServerEnabled="true"` odešle existující UserJourneyRecorder JSON jako vlastní událost do Application Insights.
Ukázka:

  ```XML
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

3. Nahrajte zásady.

### <a name="see-the-logs-in-application-insights"></a>Zobrazit protokoly ve službě Application Insights

>[!NOTE]
> Zde je krátké zpoždění (méně než pět minut), než uvidíte nové protokoly ve službě Application Insights.

1. Otevřete prostředek Application Insights, které jste vytvořili [webu Azure portal](https://portal.azure.com).
1. V **přehled** nabídky, klikněte na **Analytics**.
1. Otevření nové záložky ve službě Application Insights.
1. Tady je seznam dotazů, můžete si zobrazit protokoly

| Dotaz | Popis |
|---------------------|--------------------|
trasování | Zobrazit všechny protokoly vytvořené službou Azure AD B2C |
trasování \| kde časové razítko > ago(1d) | Zobrazit všechny protokoly vytvořené službou Azure AD B2C pro poslední den

Položky mohou být dlouhé.  Export do souboru CSV pro podívat podrobněji.

Další informace o nástroji pro analýzu [tady](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>Společenství vyvinula prohlížeč cesty uživatele, což vývojářům umožňuje identity.  Není podporovaný společností Microsoft a k dispozici výhradně jako-je.  Načte z vaší instance služby Application Insights a poskytuje cestu události strukturované zobrazení uživatele.  Získat zdrojový kód a nasaďte ji ve své vlastní řešení.

Verze prohlížeče, který načítá události z Application Insights je umístěná [zde](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

>[!NOTE]
>V současné době jsou navrženy protokoly podrobné aktivit je zde popsáno, **pouze** na podporu při vývoji vlastních zásad. Nepoužívejte režimu pro vývoj v produkčním prostředí.  Protokoly shromažďovat všechny deklarace identity posílané do a z zprostředkovatelé identity během vývoje.  Je-li použít v produkčním prostředí, vývojář zodpovědnost za identifikovatelné osobní údaje (soukromě údaje) shromážděné v protokolu App Insights, které vlastní.  Tyto podrobné protokoly jsou shromážděna, pouze když zásady se přikládá **VÝVOJOVÝ režim**.

[Úložiště GitHub pro nepodporované vlastní zásady ukázky a související nástroje](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>Další kroky

Zkoumání dat ve službě Application Insights vám pomohou pochopit, jak vyskytne architekturu rozhraní identit funguje základní B2C k zajištění svou vlastní identitu.

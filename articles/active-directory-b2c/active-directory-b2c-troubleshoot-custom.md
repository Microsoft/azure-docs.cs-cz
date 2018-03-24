---
title: Application Insights k řešení potíží se zásadami vlastní – Azure AD B2C | Microsoft Docs
description: Postup instalace Application Insights pro sledování spuštění vlastní zásady
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: 3e27976b02ee75083b07ca2e4f0c06e19502d1f5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-collecting-logs"></a>Azure Active Directory B2C: Shromažďování protokolů.

Tento článek popisuje kroky pro shromažďování protokolů z Azure AD B2C, takže lze diagnostikovat problémy s vlastní zásady.

>[!NOTE]
>V současné době jsou navrženy protokoly podrobné aktivity, které jsou zde popsané **pouze** a usnadňuje vývoj vlastních zásad. Nepoužívejte režimu pro vývoj v produkčním prostředí.  Protokoly shromažďovat všechny deklarace identity posílané do a z poskytovatelů identit během vývoje.  Pokud se používá v produkčním prostředí, vývojář odpovědnost pro PII (soukromě osobní informace) shromážděné v protokolu statistiky aplikace, kterou vlastní.  Tyto podrobné protokoly jsou shromažďovány pouze, pokud zásady je umístěn na **režimu pro vývoj**.


## <a name="use-application-insights"></a>Pomocí Application Insights

Azure AD B2C podporuje funkci pro odesílání dat do služby Application Insights.  Application Insights umožňuje diagnostikovat výjimky a vizualizovat problémy s výkonem aplikace.

### <a name="setup-application-insights"></a>Instalační program Application Insights

1. Přejděte na [portál Azure](https://portal.azure.com). Zkontrolujte, zda že jste v klientovi s předplatným Azure (ne vašeho klienta Azure AD B2C).
1. Klikněte na tlačítko **+ nový** v levé navigační nabídce.
1. Vyhledejte a vyberte **Application Insights**, pak klikněte na tlačítko **vytvořit**.
1. Vyplňte formulář a klikněte na tlačítko **vytvořit**. Vyberte **Obecné** pro **typ aplikace**.
1. Po vytvoření prostředku, otevřete prostředek Application Insights.
1. Najít **vlastnosti** v levé nabídce a klepněte na něj.
1. Kopírování **klíč instrumentace** a uložit pro další části.

### <a name="set-up-the-custom-policy"></a>Nastavení vlastních zásad

1. Otevřete soubor RP (například SignUpOrSignin.xml).
1. Přidejte následující atributy, které se `<TrustFrameworkPolicy>` element:

  ```XML
  DeploymentMode="Development"
  UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
  ```

1. Pokud již neexistuje, přidejte podřízený uzel `<UserJourneyBehaviors>` k `<RelyingParty>` uzlu. Musí být umístěna bezprostředně po `<DefaultUserJourney ReferenceId="UserJourney Id from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`
2. Přidejte následující uzel jako podřízený `<UserJourneyBehaviors>` elementu. Nezapomeňte nahradit `{Your Application Insights Key}` s **klíč instrumentace** získaný ze služby Application Insights v předchozí části.

  ```XML
  <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
  ```

  * `DeveloperMode="true"` informuje ApplicationInsights urychlit telemetrie skrz kanál zpracování, vhodné pro vývoj, ale omezené na vysokou svazky.
  * `ClientEnabled="true"` odešle klientský skript ApplicationInsights pro sledování zobrazení a na straně klienta chyby stránky (není potřeba).
  * `ServerEnabled="true"` odešle existující JSON UserJourneyRecorder jako vlastní události Application insights.
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

### <a name="see-the-logs-in-application-insights"></a>V protokolech ve službě Application Insights

>[!NOTE]
> Je malou zpoždění (méně než pět minut), než budete moct vidět nové protokoly ve službě Application Insights.

1. Otevřete prostředek Application Insights, který jste vytvořili v [portál Azure](https://portal.azure.com).
1. V **přehled** nabídky, klikněte na **Analytics**.
1. Otevřete novou kartu ve službě Application Insights.
1. Tady je seznam dotazů, které můžete použít k najdete v souborech protokolů

| Dotaz | Popis |
|---------------------|--------------------|
Trasování | Zobrazit všechny protokoly služby Azure AD B2C |
trasování \| kterých časové razítko > ago(1d) | Zobrazit všechny protokoly služby Azure AD B2C pro poslední den

Položky může trvat dlouho.  Obrázek otevřete exportovat do souboru CSV.

Další informace o nástroj pro analýzu [zde](https://docs.microsoft.com/azure/application-insights/app-insights-analytics).

>[!NOTE]
>Komunita vyvinula prohlížeč cesty uživatele, což vývojářům identity.  Není podporovaný společností Microsoft a k dispozici výhradně jako-je.  Přečte z vaší instance služby Application Insights a poskytne strukturované zobrazení uživatele cesty události.  Získat zdrojový kód a nasaďte ho ve vlastní řešení.

Verze prohlížeče, který čte události z Application Insights se nachází [sem](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

>[!NOTE]
>V současné době jsou navrženy protokoly podrobné aktivity, které jsou zde popsané **pouze** a usnadňuje vývoj vlastních zásad. Nepoužívejte režimu pro vývoj v produkčním prostředí.  Protokoly shromažďovat všechny deklarace identity posílané do a z poskytovatelů identit během vývoje.  Pokud se používá v produkčním prostředí, vývojář odpovědnost pro PII (soukromě osobní informace) shromážděné v protokolu statistiky aplikace, kterou vlastní.  Tyto podrobné protokoly jsou shromažďovány pouze, pokud zásady je umístěn na **režimu pro vývoj**.

[Úložiště Github pro nepodporovaný – ukázky vlastních zásad a související nástroje](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies)



## <a name="next-steps"></a>Další kroky

Prozkoumejte data ve službě Application Insights vám pomohou pochopit, jak se vyskytne rozhraní prostředí Identity základní B2C funguje k poskytování vlastní identity.

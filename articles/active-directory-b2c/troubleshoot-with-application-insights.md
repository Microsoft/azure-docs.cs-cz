---
title: Poradce při potížích s vlastními zásadami pomocí přehledů aplikací
titleSuffix: Azure AD B2C
description: Jak nastavit Application Insights sledovat provádění vlastních zásad.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403dbe6106cb7a1d277ba672112d2bc45dbc2987
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186263"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Shromažďování protokolů Služby Azure Active Directory B2C pomocí přehledů aplikací

Tento článek obsahuje postup pro shromažďování protokolů ze služby Active Directory B2C (Azure AD B2C), takže můžete diagnostikovat problémy s vlastní zásady. Application Insights poskytuje způsob diagnostiky výjimek a vizualizaci problémů s výkonem aplikací. Azure AD B2C obsahuje funkci pro odesílání dat do Application Insights.

Podrobné protokoly aktivit popsané zde by měly být povoleny **pouze** během vývoje vlastních zásad.

> [!WARNING]
> Nepovolujte režim vývoje v produkčním prostředí. Protokoly shromažďují všechny deklarace odeslané poskytovatelům identity a od nich. Vy jako vývojář přebíráte odpovědnost za veškeré osobní údaje shromážděné v protokolech Application Insights. Tyto podrobné protokoly jsou shromažďovány pouze v případě, že je zásada umístěna v **režimu vývojáře**.

## <a name="set-up-application-insights"></a>Nastavení přehledů aplikací

Pokud ještě nemáte, vytvořte instanci Application Insights ve vašem předplatném.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vaše předplatné Azure (ne váš adresář Azure AD B2C).
1. V levé navigační nabídce vyberte **Vytvořit zdroj.**
1. Vyhledejte a vyberte **Přehledy aplikací**a vyberte **možnost Vytvořit**.
1. Vyplňte formulář, vyberte **Zkontrolovat + vytvořit**a pak vyberte **Vytvořit**.
1. Po dokončení nasazení vyberte **Přejít na prostředek**.
1. V části **Konfigurovat** v nabídce Application Insights vyberte **Vlastnosti**.
1. **Zaznamenejte klíč instrumentace** pro pozdější krok.

## <a name="configure-the-custom-policy"></a>Konfigurace vlastních zásad

1. Otevřete soubor předávající strany (RP), například *SignUpOrSignin.xml*.
1. Do prvku přidejte `<TrustFrameworkPolicy>` následující atributy:

   ```XML
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Pokud ještě neexistuje, přidejte `<UserJourneyBehaviors>` do `<RelyingParty>` uzlu podřízený uzel. Musí být umístěn `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />`ihned po .
1. Přidejte následující uzel jako podřízený `<UserJourneyBehaviors>` prvek. Nezapomeňte nahradit `{Your Application Insights Key}` **klíč instrumentace** Application Insights, který jste zaznamenali dříve.

    ```XML
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`říká ApplicationInsights urychlit telemetrická data prostřednictvím kanálu zpracování. Dobré pro vývoj, ale omezené ve vysokých objemech.
    * `ClientEnabled="true"`odešle skript ApplicationInsights na straně klienta pro sledování zobrazení stránky a chyby na straně klienta. Můžete je zobrazit v tabulce **browserTimings** na portálu Application Insights. Nastavením `ClientEnabled= "true"`přidáte Application Insights do stránkového skriptu a získáte časování načítání stránky a volání AJAX, počty, podrobnosti o výjimkách prohlížeče a selhání AJAX a počty uživatelů a relací. Toto pole je **volitelné** `false` a je ve výchozím nastavení nastaveno na hodnotu .
    * `ServerEnabled="true"`odešle existující UserJourneyRecorder JSON jako vlastní událost application insights.

    Například:

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

1. Nahrajte zásady.

## <a name="see-the-logs-in-application-insights"></a>Zobrazení protokolů v application insights

Je krátké zpoždění, obvykle méně než pět minut, než uvidíte nové protokoly v Application Insights.

1. Otevřete prostředek Application Insights, který jste vytvořili na [webu Azure Portal](https://portal.azure.com).
1. V nabídce **Přehled** vyberte **Analytics**.
1. Otevřete novou kartu v Application Insights.

Zde je seznam dotazů, které můžete použít k zobrazení protokolů:

| Dotaz | Popis |
|---------------------|--------------------|
`traces` | Zobrazit všechny protokoly generované Azure AD B2C |
`traces | where timestamp > ago(1d)` | Zobrazit všechny protokoly generované Azure AD B2C za poslední den

Záznamy mohou být dlouhé. Exportujte do CSV pro bližší pohled.

Další informace o dotazování najdete [v tématu Přehled dotazů protokolu v Azure Monitoru](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Další kroky

Komunita vyvinula prohlížeč cest uživatelů, který pomáhá vývojářům identity. Čte se z instance Application Insights a poskytuje dobře strukturované zobrazení událostí cesty uživatele. Získáte zdrojový kód a nasadit ve vlastním řešení.

Uživatel cesta hráč není podporován společností Microsoft, a je k dispozici výhradně tak, jak je.

Verzi prohlížeče, která čte události z Application Insights na GitHubu, najdete tady:

[Azure-Samples/active-directory-b2c-advanced-policies Azure-Samples/active-directory-b2c-advanced-policies Azure-Samples/active-directory-b2c-advanced-policies Azure-](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)

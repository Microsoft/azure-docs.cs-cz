---
title: Monitorování výkonu Azure App Services | Microsoft Docs
description: Sledování výkonu aplikací pro Azure App Services. Zatížení grafu a doba odezvy, informace o závislostech a nastavení výstrah pro výkon.
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 55682215a990643111d5813db437b1f1e4612868
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547226"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorování výkonu služby Azure App Service

Povolení monitorování webových aplikací založených na ASP.NET a ASP.NET Core běžících na [Azure App Services](../../app-service/index.yml) je teď jednodušší než kdy dřív. Vzhledem k tomu, že jste předtím museli ručně nainstalovat rozšíření lokality, je ve výchozím nastavení do image služby App Service standardně integrováno nejnovější rozšíření nebo agent. Tento článek vás provede povolením Application Insights monitorování a poskytuje předběžné pokyny pro automatizaci procesu pro rozsáhlá nasazení.

> [!NOTE]
> Ruční přidání rozšíření Application Insights webu prostřednictvím rozšíření **nástrojů pro vývoj**  >   je zastaralé. Tato metoda instalace rozšíření byla závislá na ruční aktualizaci pro každou novou verzi. Nejnovější stabilní verze rozšíření je teď  [předinstalována](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) jako součást image App Service. Soubory jsou umístěny v `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` a jsou automaticky aktualizovány s každou stabilní verzí. Pokud budete postupovat podle pokynů na základě agentů a zapnout monitorování níže, automaticky se odebere zastaralé rozšíření za vás.

## <a name="enable-application-insights"></a>Povolení Application Insights

Existují dva způsoby, jak povolit monitorování aplikací pro hostované aplikace Azure App Services:

* **Monitorování aplikací založené na agentech** (ApplicationInsightsAgent).  
    * Tato metoda je nejjednodušší pro povolení a není nutná žádná pokročilá konfigurace. Často se označuje jako monitorování za běhu. V případě Azure App Services doporučujeme, abyste aspoň povolili tuto úroveň monitorování a pak na základě vašeho konkrétního scénáře mohli vyhodnotit, jestli je potřeba rozšířené monitorování prostřednictvím ruční instrumentace.

* **Ruční instrumentace aplikace pomocí kódu** instalací sady Application Insights SDK.

    * Tento přístup je mnohem přizpůsobitelnější, ale vyžaduje [Přidání závislosti na balíčky NuGet sady Application Insights SDK](./asp-net.md). Tato metoda také znamená, že je nutné spravovat aktualizace na nejnovější verzi balíčků sami.

    * Pokud potřebujete vlastní volání rozhraní API ke sledování událostí nebo závislostí, které se ve výchozím nastavení nezachycují pomocí monitorování založeného na agentech, musíte použít tuto metodu. Další informace najdete v [článku rozhraní API pro vlastní události a metriky](./api-custom-events-metrics.md) . Tato možnost je také momentálně jedinou podporovanou možností pro úlohy založené na systému Linux.

> [!NOTE]
> Pokud je detekováno monitorování založené na agentech i ruční instrumentaci založené na sadě SDK, bude dodrženo pouze nastavení ručního instrumentace. K tomu je potřeba zabránit odesílání duplicitních dat. Další informace najdete v [části řešení potíží](#troubleshooting) níže.

## <a name="enable-agent-based-monitoring"></a>Povolit monitorování na základě agentů

# <a name="aspnet"></a>[ASP.NET](#tab/net)

> [!NOTE]
> Kombinace APPINSIGHTS_JAVASCRIPT_ENABLED a urlCompression není podporována. Další informace najdete v tématu Vysvětlení v [části Poradce při potížích](#troubleshooting).


1. **Vyberte Application Insights** v Ovládacích panelech Azure pro vaši službu App Service.

    ![V části nastavení vyberte Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Pokud jste ještě nevytvořili prostředek Application Insights pro tuto aplikaci, vyberte možnost vytvořit nový prostředek. 

     > [!NOTE]
     > Když kliknete na **OK** , vytvoří se nový prostředek, zobrazí se výzva k **použití nastavení monitorování**. Když vyberete **pokračovat** , připojí se k vaší službě App service nový Application Insights prostředek. tím se taky **aktivuje restartování služby App Service**. 

     ![Používejte webovou aplikaci.](./media/azure-web-apps/create-resource-01.png)

2. Jakmile určíte, který prostředek se má použít, můžete zvolit, jak má Application Insights shromažďovat data na platformu pro vaši aplikaci. Monitorování aplikací ASP.NET se používá ve výchozím nastavení se dvěma různými úrovněmi shromažďování.

    ![Snímek obrazovky se zobrazí stránka Application Insights rozšíření webu s vybraným možnost vytvořit nový prostředek.](./media/azure-web-apps/choose-options-new.png)
 
 Níže je uveden souhrn dat shromažďovaných pro každou trasu:
        
| Data | Kolekce ASP.NET úrovně Basic | ASP.NET Doporučené shromažďování |
| --- | --- | --- |
| Přidání trendů využití procesoru, paměti a vstupně-výstupních operací |Ano |Ano |
| Shromažďování trendů využití a povolení korelace mezi výsledky dostupnosti a transakcemi | Ano |Ano |
| Shromažďování výjimek nezpracovaných hostitelským procesem | Ano |Ano |
| Zlepšení přesnosti metrik APM v případě zatížení při použití vzorkování | Ano |Ano |
| Korelace mikroslužeb napříč požadavky a závislostmi | Ne (jenom možnosti APM s jednou instancí) |Ano |

3. Pokud chcete nakonfigurovat nastavení, jako je vzorkování, které byste mohli dříve řídit prostřednictvím souboru applicationinsights.config, můžete teď s těmito nastaveními pracovat pomocí nastavení aplikace s odpovídající předponou. 

    * Chcete-li například změnit procento počátečního vzorkování, můžete vytvořit nastavení aplikace pro: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` a hodnotu `100` .

    * Seznam podporovaných nastavení procesoru telemetrie pro adaptivní vzorkování můžete zobrazit v [kódu](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs) a v [související dokumentaci](./sampling.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

> [!IMPORTANT]
> Podporovány jsou následující verze ASP.NET Core: ASP.NET Core 2,1 a 3,1. Verze 2,0, 2,2 a 3,0 byly vyřazeny a již nejsou podporovány. Pokud chcete, aby byla Automatická instrumentace fungovat, upgradujte prosím na [podporovanou verzi](https://dotnet.microsoft.com/platform/support/policy/dotnet-core) .NET Core.

Cílení na úplné rozhraní z ASP.NET Core, samostatného nasazení a aplikací založených na systému Linux se v současnosti **nepodporují** pomocí monitorování založeného na agentech nebo rozšíření. ([Ruční instrumentace](./asp-net-core.md) přes kód bude fungovat ve všech předchozích scénářích.)

1. **Vyberte Application Insights** v Ovládacích panelech Azure pro vaši službu App Service.

    ![V části nastavení vyberte Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Pokud jste ještě nevytvořili prostředek Application Insights pro tuto aplikaci, vyberte možnost vytvořit nový prostředek. 

     > [!NOTE]
     > Když kliknete na **OK** , vytvoří se nový prostředek, zobrazí se výzva k **použití nastavení monitorování**. Když vyberete **pokračovat** , připojí se k vaší službě App service nový Application Insights prostředek. tím se taky **aktivuje restartování služby App Service**. 

     ![Používejte webovou aplikaci.](./media/azure-web-apps/create-resource-01.png)

2. Jakmile určíte, který prostředek se má použít, můžete zvolit způsob, jakým má Application Insights shromažďovat data na platformu pro vaši aplikaci. ASP.NET Core nabízí **doporučenou kolekci** nebo **zakázanou** pro ASP.NET Core 2,1 a 3,1.

    ![Zvolit možnosti na platformu](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

V App Service webové aplikace v části **Nastavení**  >  **Vyberte Application Insights**  >  **Povolit**. Monitorování založené na agentech Node.js je aktuálně ve verzi Preview.

# <a name="java"></a>[Java](#tab/java)

Postupujte podle pokynů pro [Application Insights agenta java 3,0](./java-in-process-agent.md) a povolte tak automatickou instrumentaci pro aplikace v jazyce Java, aniž byste museli měnit kód.
Automatická integrace ještě není k dispozici pro App Service.

# <a name="python"></a>[Python](#tab/python)

Webové aplikace založené na Pythonu App Service v současné době nepodporují monitorování na základě automatického agenta nebo rozšíření. Pokud chcete povolit monitorování pro aplikaci v Pythonu, musíte [aplikaci ručně instrumentovat](./opencensus-python.md).

---

## <a name="enable-client-side-monitoring"></a>Povolit monitorování na straně klienta

# <a name="aspnet"></a>[ASP.NET](#tab/net)

Monitorování na straně klienta je výslovný souhlas pro ASP.NET. Postup při povolování monitorování na straně klienta:

* **Nastavení** **>** **Konfigurace**
   * V části nastavení aplikace vytvořte **nové nastavení aplikace**:

     Název: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Hodnota: `true`

   * Kliknutím na **Uložit** uložte nastavení a kliknutím na **Restartovat** restartujte aplikaci.

Chcete-li zakázat monitorování na straně klienta, buď z nastavení aplikace odeberte dvojici hodnoty klíče, nebo hodnotu nastavte na hodnotu NEPRAVDA.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

Monitorování na straně klienta je **ve výchozím nastavení povolené** pro ASP.NET Core aplikace se **doporučenou kolekcí** bez ohledu na to, jestli je přítomné nastavení aplikace APPINSIGHTS_JAVASCRIPT_ENABLED.

Pokud z nějakého důvodu chcete vypnout monitorování na straně klienta:

* **Nastavení** **>** **Konfigurace**
   * V části nastavení aplikace vytvořte **nové nastavení aplikace**:

     Jméno: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Hodnota: `false`

   * Kliknutím na **Uložit** uložte nastavení a kliknutím na **Restartovat** restartujte aplikaci.

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Chcete-li povolit monitorování na straně klienta pro aplikaci Node.js, je nutné [do aplikace ručně přidat sadu JavaScript SDK na straně klienta](./javascript.md).

# <a name="java"></a>[Java](#tab/java)

Chcete-li povolit monitorování na straně klienta pro aplikaci Java, je nutné [do aplikace ručně přidat sadu JavaScript SDK na straně klienta](./javascript.md).

# <a name="python"></a>[Python](#tab/python)

Pokud chcete povolit monitorování na straně klienta pro vaši aplikaci v Pythonu, musíte [do své aplikace přidat ručně sadu JavaScript SDK na straně klienta](./javascript.md).

---

## <a name="automate-monitoring"></a>Automatizace monitorování

Aby bylo možné povolit shromažďování telemetrie s Application Insights, je třeba nastavit pouze nastavení aplikace:

   ![App Service nastavení aplikace s dostupnými nastaveními Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definice nastavení aplikace

|Název nastavení aplikace |  Definice | Hodnota |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Hlavní rozšíření, které řídí monitorování za běhu. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Jenom ve výchozím režimu jsou k dispozici základní funkce, aby se zajistil optimální výkon. | `default` nebo `recommended`: |
|InstrumentationEngine_EXTENSION_VERSION | Určuje, zda bude modul binárního zápisu `InstrumentationEngine` zapnutý. Toto nastavení má vliv na výkon a má vliv na čas spuštění a spuštění. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Ovládací prvky, pokud se v SQL & text tabulky Azure bude zachytávat spolu s voláními závislostí. Upozornění na výkon: bude to mít vliv na počáteční čas spuštění aplikace. Toto nastavení vyžaduje `InstrumentationEngine` . | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>App Service nastavení aplikace s Azure Resource Manager

Nastavení aplikace pro App Services lze spravovat a konfigurovat pomocí [šablon Azure Resource Manager](../../azure-resource-manager/templates/template-syntax.md). Tuto metodu lze použít při nasazování nových App Servicech prostředků pomocí Automatizace Azure Resource Manager nebo pro úpravu nastavení existujících prostředků.

Základní struktura JSON nastavení aplikace pro službu App Service je následující:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Příklad šablony Azure Resource Manager s nastavením aplikace nakonfigurovaným pro Application Insights může být tato [Šablona](https://github.com/Andrew-MSFT/BasicImageGallery) užitečná, konkrétně část od [řádku 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizujte vytváření prostředků Application Insights a odkaz na nově vytvořenou App Service.

Pokud chcete vytvořit šablonu Azure Resource Manager se všemi nakonfigurovanými výchozími nastaveními Application Insights, spusťte proces jako kdybyste vytvořili novou webovou aplikaci s povoleným Application Insights.

Vybrat **Možnosti automatizace**

   ![Nabídka pro vytvoření webové aplikace v App Service](./media/azure-web-apps/create-web-app.png)

Tato možnost generuje nejnovější šablonu Azure Resource Manager se všemi požadovanými nakonfigurovanými nastaveními.

  ![Šablona webové aplikace App Service](./media/azure-web-apps/arm-template.png)

Níže je ukázka, nahraďte všechny instance  `AppMonitoredSite` s názvem vašeho webu:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>Povolení přes PowerShell

Aby bylo možné povolit monitorování aplikací prostřednictvím prostředí PowerShell, je nutné změnit pouze nastavení základní aplikace. Níže je ukázka, která umožňuje monitorování aplikací pro web s názvem "AppMonitoredSite" ve skupině prostředků "AppMonitoredRG" a konfiguruje data, která se mají odeslat do klíče instrumentace "012345678-abcd-ef01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Upgradovat rozšíření nebo agenta monitorování

### <a name="upgrading-from-versions-289-and-up"></a>Upgrade z verzí 2.8.9 a up

Upgrade z verze 2.8.9 proběhne automaticky bez dalších akcí. Nové bity monitorování se doručí na pozadí cílové službě App Service a při restartování aplikace se budou vyzvednout.

Pokud chcete zjistit, kterou verzi rozšíření používáte, přejděte na `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Snímek obrazovky s cestou URL http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Upgrade z verzí 1.0.0-2.6.5

Počínaje verzí 2.8.9 se používá předem nainstalovaná rozšíření webu. Pokud používáte starší verzi, můžete aktualizovat prostřednictvím jednoho ze dvou způsobů:

* [Upgrade povolíte prostřednictvím portálu](#enable-application-insights). (I v případě, že máte rozšíření Application Insights pro Azure App Service nainstalované, uživatelské rozhraní zobrazí jenom tlačítko **Povolit** . Po pozadí bude staré rozšíření privátního webu odebráno.)

* [Upgrade prostřednictvím PowerShellu](#enabling-through-powershell):

    1. Nastavte nastavení aplikace tak, aby povolovalo předinstalované rozšíření webu ApplicationInsightsAgent. Viz [povolení prostřednictvím PowerShellu](#enabling-through-powershell).
    2. Ručně odeberte rozšíření privátního webu s názvem Application Insights příponu pro Azure App Service.

Pokud je upgrade proveden z verze před aplikací 2.5.1, zkontrolujte, zda jsou knihovny DLL ApplicationInsigths odebrány ze složky bin aplikace, [v tématu Postup řešení potíží](#troubleshooting).

## <a name="troubleshooting"></a>Řešení potíží

Níže najdete naše podrobné pokyny k odstraňování potíží pro monitorování rozšíření/na základě agentů pro aplikace založené na ASP.NET a ASP.NET Core, které běží na Azure App Services.

> [!NOTE]
> Doporučený postup pro monitorování aplikací Java je použití automatické instrumentace beze změny kódu. Postupujte prosím podle pokynů pro [Application Insights agenta Java 3,0](./java-in-process-agent.md).


1. Ověřte, že je aplikace monitorována prostřednictvím `ApplicationInsightsAgent` .
    * Ověřte, že `ApplicationInsightsAgent_EXTENSION_VERSION` nastavení aplikace je nastavené na hodnotu ~ 2.
2. Ujistěte se, že aplikace splňuje požadavky, které se mají monitorovat.
    * Přejděte na `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.

    ![Snímek https://yoursitename.scm.azurewebsites/applicationinsights stránky výsledků](./media/azure-web-apps/app-insights-sdk-status.png)

    * Potvrďte, že `Application Insights Extension Status` je `Pre-Installed Site Extension, version 2.8.12.1527, is running.` 
    * Pokud není spuštěný, postupujte podle [pokynů pro monitorování povolení Application Insights](#enable-application-insights) .

    * Potvrďte, že zdroj stavu existuje a vypadá takto: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Pokud není k dispozici podobná hodnota, znamená to, že aplikace momentálně není spuštěná nebo není podporovaná. Chcete-li zajistit, aby aplikace běžela, zkuste ručně navštívit koncové body adresy URL nebo aplikace, čímž umožníte zpřístupnění běhových informací.

    * Potvrďte, že `IKeyExists` je `true`
        * Pokud je `false` , přidejte `APPINSIGHTS_INSTRUMENTATIONKEY` a `APPLICATIONINSIGHTS_CONNECTION_STRING` s identifikátorem GUID ikey do nastavení aplikace.

    * Potvrďte, že nejsou k dispozici žádné položky pro `AppAlreadyInstrumented` , `AppContainsDiagnosticSourceAssembly` a `AppContainsAspNetTelemetryCorrelationAssembly` .
        * Pokud existuje kterákoli z těchto položek, odeberte z aplikace následující balíčky:, a `Microsoft.ApplicationInsights` `System.Diagnostics.DiagnosticSource` `Microsoft.AspNet.TelemetryCorrelation` .
        * Pouze pro ASP.NET Core aplikace: v případě, že vaše aplikace odkazuje na jakékoli Application Insights balíčky, například pokud jste předtím instrumentoval (nebo se pokusili instrumentovat) aplikaci pomocí [sady ASP.NET Core SDK](./asp-net-core.md), povolení integrace App Service se nemusí projevit a data se nemusí zobrazit v Application Insights. Pokud chcete problém vyřešit, na portálu zapněte "spolupráci s Application Insights SDK" a začnete zobrazovat data v Application Insights 
        > [!IMPORTANT]
        > Tato funkce je ve verzi Preview. 

        ![Povolit nastavení existující aplikace](./media/azure-web-apps/netcore-sdk-interop.png)

        Data se teď budou posílat pomocí přístupu bez kódu, i když se původně použila Application Insights SDK nebo se pokusila o použití.

        > [!IMPORTANT]
        > Pokud aplikace použila k odeslání jakékoli telemetrie Application Insights SDK, bude taková telemetrie zakázaná – jinými slovy, vlastní telemetrie – Pokud nějaká existuje, například metody Track * () a jakékoli vlastní nastavení, jako je vzorkování, bude zakázané. 


### <a name="php-and-wordpress-are-not-supported"></a>PHP a WordPress nejsou podporované.

Weby PHP a WordPress nejsou podporovány. V současnosti není k dispozici žádná oficiálně podporovaná sada SDK/Agent pro monitorování těchto úloh na straně serveru. Ruční instrumentování transakcí na straně klienta na webu PHP nebo WordPress je však možné provést přidáním JavaScriptu na straně klienta na webové stránky pomocí [sady JavaScript SDK](./javascript.md).

Následující tabulka obsahuje podrobnější vysvětlení toho, co tyto hodnoty znamenají, jejich základní příčiny a Doporučené opravy:

|Hodnota problému|Vysvětlení|Oprava
|---- |----|---|
| `AppAlreadyInstrumented:true` | Tato hodnota označuje, že rozšíření zjistilo, že některé aspekty sady SDK už v aplikaci existují a že se bude zálohovat. Důvodem může být odkaz na `System.Diagnostics.DiagnosticSource` ,  `Microsoft.AspNet.TelemetryCorrelation` , nebo `Microsoft.ApplicationInsights`  | Odeberte odkazy. Některé z těchto odkazů jsou ve výchozím nastavení přidány z určitých šablon sady Visual Studio a starší verze sady Visual Studio mohou přidat odkazy na `Microsoft.ApplicationInsights` .
|`AppAlreadyInstrumented:true` | Pokud je aplikace cílena na ASP.NET Core 2,1 nebo 2,2, tato hodnota označuje, že rozšíření zjistilo, že některé aspekty sady SDK již v aplikaci existují a že se bude zálohovat. | Pro zákazníky s .NET Core 2.1, 2.2 se místo toho [doporučuje](https://github.com/aspnet/Announcements/issues/287) použít meta-package Microsoft. AspNetCore. app. Navíc na portálu zapněte "interoperabilitu s Application Insights SDK" (viz výše uvedené pokyny).|
|`AppAlreadyInstrumented:true` | Tato hodnota může být také způsobena přítomností výše uvedených knihoven DLL ve složce aplikace z předchozího nasazení. | Vyčistěte složku aplikace, abyste měli jistotu, že se odeberou tyto knihovny DLL. Ověřte adresář Bin místní aplikace a adresář wwwroot na App Service. (Pokud chcete kontrolovat adresář wwwroot vaší App Service webové aplikace: Rozšířené nástroje (Kudu) > ladit konzolu > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Tato hodnota označuje, že rozšíření zjistilo odkazy na `Microsoft.AspNet.TelemetryCorrelation` v aplikaci a bude se přepínat. | Odeberte odkaz.
|`AppContainsDiagnosticSourceAssembly**:true`|Tato hodnota označuje, že rozšíření zjistilo odkazy na `System.Diagnostics.DiagnosticSource` v aplikaci a bude se přepínat.| Pro ASP.NET odeberte odkaz. 
|`IKeyExists:false`|Tato hodnota označuje, že klíč instrumentace není přítomen v AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY` . Možné příčiny: tyto hodnoty se možná omylem odeberou, zapomněli jste nastavit hodnoty v automatizačním skriptu atd. | Ujistěte se, že se nastavení nachází v nastavení aplikace App Service.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED a urlCompression se nepodporují.

Pokud používáte APPINSIGHTS_JAVASCRIPT_ENABLED = true v případech, kdy je obsah kódovaný, může dojít k chybám jako: 

- 500 Chyba při zápisu adresy URL
- Chyba modulu pro přepis adresy URL 500,53 s pravidly pro přepsání odchozího zápisu zprávy nelze použít, je-li obsah odpovědi HTTP kódován (' gzip '). 

Důvodem je, že nastavení aplikace APPINSIGHTS_JAVASCRIPT_ENABLED nastavené na hodnotu true a kódování obsahu je současně přítomno. Tento scénář se ještě nepodporuje. Alternativním řešením je odebrat APPINSIGHTS_JAVASCRIPT_ENABLED z nastavení aplikace. To bohužel znamená, že pokud se pořád vyžaduje instrumentace JavaScriptu na straně klienta nebo prohlížeče, pro vaše webové stránky jsou nutné ruční odkazy na sadu SDK. Postupujte prosím podle [pokynů](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) pro ruční instrumentaci pomocí sady JavaScript SDK.

Nejnovější informace o Application Insights agenta nebo rozšíření najdete v [poznámkách k verzi](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>Výchozí web nasazený pomocí Web Apps nepodporuje automatické monitorování na straně klienta.

Při vytváření webové aplikace s `ASP.NET` `ASP.NET Core` modulem runtime nebo v Azure App Services nasadí jednu STATICKOU stránku HTML jako úvodní web. Statická webová stránka také načte spravovanou webovou část ASP.NET ve službě IIS. To umožňuje testovat monitorování na straně serveru bez kódu, ale nepodporuje automatické monitorování na straně klienta.

Pokud chcete testovat server bez kódu a monitorování na straně klienta pro ASP.NET nebo ASP.NET Core ve webové aplikaci Azure App Services, doporučujeme, abyste si vyzkoušeli oficiální Příručky k [vytvoření ASP.NET Core webové aplikace](../../app-service/quickstart-dotnetcore.md) a [Vytvoření webové aplikace v ASP.NET Framework](../../app-service/quickstart-dotnet-framework.md) , a pak pomocí pokynů v aktuálním článku povolíte monitorování.

### <a name="connection-string-and-instrumentation-key"></a>Připojovací řetězec a klíč instrumentace

Pokud je používáno monitorování bez kódu, je vyžadován pouze připojovací řetězec. Přesto však doporučujeme nastavit klíč instrumentace, aby se zajistila zpětná kompatibilita se staršími verzemi sady SDK, když se provádí ruční instrumentace.

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Rozdíl mezi standardními metrikami z Application Insights vs Azure App Service metriky?

Application Insights shromažďuje telemetrii pro ty žádosti, které ji udělaly v aplikaci. Pokud k selhání došlo v WebApps/IIS a požadavek nedorazil na uživatelskou aplikaci, nebude mít Application Insights žádné telemetrie.

Doba trvání `serverresponsetime` vypočítaná pomocí Application Insights není nutně shodná s dobou odezvy serveru zjištěným Web Apps. Důvodem je to, že Application Insights počítá jenom dobu trvání, kdy požadavek skutečně dosáhne uživatelské aplikace. Pokud je požadavek zablokovaný nebo zařazený do fronty ve službě IIS, bude tato čekací doba obsažená v metrikách webové aplikace, ale ne v Application Insightsch metrikách.

## <a name="release-notes"></a>Poznámky k verzi

Nejnovější aktualizace a opravy chyb [najdete v poznámkách k verzi](./web-app-extension-release-notes.md).

## <a name="next-steps"></a>Další kroky
* [Spusťte profiler v živé aplikaci](./profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorujte službu Azure Functions pomocí Application Insights.
* [Povolte odesílání diagnostiky Azure](../platform/diagnostics-extension-to-application-insights.md) do Application Insights.
* [Monitorujte metriky stavu služby](../platform/data-platform.md), abyste zajistili dostupnost služby a její schopnost dobře reagovat.
* [Přijímejte oznámení o výstrahách](../platform/alerts-overview.md) vždy, když nastanou provozní události nebo když metriky překročí prahovou hodnotu.
* Použitím [Application Insights pro aplikace JavaScript a webové stránky](javascript.md) získávejte telemetrické údaje klienta z prohlížečů, které webovou stránky navštíví.
* [Nastavte testy dostupnosti webu](monitor-web-app-availability.md) tak, aby se aktivovaly výstrahy, pokud je webový server mimo provoz.

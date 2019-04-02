---
title: Monitorování výkonu Azure app services | Dokumentace Microsoftu
description: Sledování výkonu aplikací pro Azure app Service. Graf zatížení a doby odezvy, informací o závislostech a nastavit upozornění na výkon.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: mbullwin
ms.openlocfilehash: 7386f6bd92143cf3fb7b37725900425f99371cd0
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804988"
---
# <a name="monitor-azure-app-service-performance"></a>Sledování výkonu služby Azure App Service

Povolení sledování v rozhraní .NET a .NET Core na základě webové aplikace běžící v Azure App Service je teď snadnější než kdy dřív. Zatímco dříve jste museli ručně nainstalovat rozšíření webu, nejnovější agent/rozšíření je teď součástí app service image ve výchozím nastavení. Tento článek se vás provede povolením monitorování pomocí Application Insights a také předběžnou pokyny pro automatizaci tohoto procesu pro rozsáhlá nasazení.

> [!NOTE]
> Ruční přidání rozšíření webu Application Insights prostřednictvím **nástroje pro vývoj** > **rozšíření** je zastaralý. Nejnovější stabilní verze tohoto rozšíření je teď [předinstalovaným](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) jako součást image služby App Service. Soubory jsou umístěny v `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` a aktualizují automaticky s každou stabilní verzi. Pokud budete postupovat podle pokynů na základě agenta monitorování povolit níže, automaticky odebere nepoužívané rozšíření za vás.

## <a name="enable-application-insights"></a>Povolení Application Insights

Existují dva způsoby, jak povolit funkci application monitoring pro aplikace hostované služby Azure App Services:

* **Monitorování aplikací založené na agentovi** (ApplicationInsightsAgent).  
    * Tato metoda je nejjednodušší povolit a není nutná žádná pokročilé konfigurace. To se často označuje jako "runtime" monitorování. Pro Azure App Services doporučujeme přinejmenším povolení sledování této úrovně a pak podle váš konkrétní scénář, kterou můžete vyhodnotit, jestli je potřeba rozšířené monitorování prostřednictvím ručního instrumentace.

* **Ručně nakonfigurujete aplikace prostřednictvím kódu** nainstalováním sady Application Insights SDK.

    * Tento přístup je mnohem lépe přizpůsobitelnou, ale vyžaduje [Přidat závislost na balíčky NuGet sady SDK pro Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Tato metoda také znamená, že budete muset spravovat aktualizace na nejnovější verzi balíčků sami.

    * Pokud je potřeba volat vlastní rozhraní API není zachycena ve výchozím nastavení se monitorování na základě agenta sledování událostí a závislostí, musíte použít tuto metodu. Podívejte se [rozhraní API pro vlastní události a metriky článku](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) Další informace.

> [!NOTE]
> Pokud na základě agenta monitorování a ruční SDK na základě instrumentace se zjistí, že pouze nastavení ruční instrumentace se neuplatňují. Toto je zabránit duplicitní data ze odesílat. Další informace o této rezervaci [řešení potíží s části](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) níže.

## <a name="enable-agent-based-monitoring-net"></a>Povolit na základě agenta monitorování .NET

1. **Vyberte Application Insights** Azure ovládacího panelu pro službu app service.

    ![V části Nastavení zvolte Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Můžete vytvořit nový prostředek, pokud jste už nastavili prostředek Application Insights pro tuto aplikaci. 

     > [!NOTE]
     > Po kliknutí na **OK** k vytvoření nového prostředku, zobrazí se výzva k **použít nastavení monitorování**. Výběr **pokračovat** propojí nový prostředek Application Insights do vaší služby app service, to může také **aktivujte restartování služby app service**. 

     ![Používejte webovou aplikaci.](./media/azure-web-apps/create-resource-01.png)

2. Po určení, který prostředek se má použít, můžete nastavit, jak chcete application insights shromažďovat data pro jednotlivé platformy pro vaši aplikaci. Monitorování aplikace ASP.NET je ve výchozím nastavení se dvou různých úrovních kolekce.

    ![Vyberte možnosti jednotlivé platformy](./media/azure-web-apps/choose-options-new.png)

   * .NET **základní kolekce** úroveň nabízí základní možnosti APM jednou instancí.

   * .NET **doporučuje kolekce** úroveň:
       * Přidá trendy využití procesoru, paměti a vstupně-výstupních operací.
       * Koreluje mikroslužby napříč hranicemi požadavku nebo závislost.
       * Shromažďuje trendy využití a umožňuje spojitost s míněním z výsledky dostupnosti transakcí.
       * Shromažďuje výjimky ošetřena hostitelský proces.
       * Při vzorkování se používá, zvyšují přesnost metriky APM v zatížení.

3. Ke konfiguraci nastavení, jako je odběr vzorků, které mohl dříve řídit pomocí souboru applicationinsights.config můžete nyní pracovat s tyto stejné nastavení prostřednictvím nastavení aplikace s odpovídající předpona. 

    * Například, chcete-li změnit procento počáteční vzorkování, můžete vytvořit nastavení aplikace z: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` a hodnotu `100`.

    * Seznam podporovaných adaptivního vzorkování nastavení procesoru telemetrická data, můžete konzultovat [kód](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) a [přidružená dokumentace](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-net-core"></a>Povolit na základě agenta monitorování .NET Core

Jsou podporovány následující verze sady .NET Core: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2

Cílí na úplné rozhraní .NET Core, samostatná nasazení a ASP.NET Core 3.0 jsou v tuto chvíli **nepodporuje** s monitorováním/rozšíření agenta na základě. ([Ruční instrumentace](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) prostřednictvím kódu bude fungovat ve všech předchozích scénářích.)

1. **Vyberte Application Insights** Azure ovládacího panelu pro službu app service.

    ![V části Nastavení zvolte Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Můžete vytvořit nový prostředek, pokud jste už nastavili prostředek Application Insights pro tuto aplikaci. 

     > [!NOTE]
     > Po kliknutí na **OK** k vytvoření nového prostředku, zobrazí se výzva k **použít nastavení monitorování**. Výběr **pokračovat** propojí nový prostředek Application Insights do vaší služby app service, to může také **aktivujte restartování služby app service**. 

     ![Používejte webovou aplikaci.](./media/azure-web-apps/create-resource-01.png)

2. Po určení, který prostředek se má použít, můžete nastavit, jak chcete Application Insights shromažďovat data pro jednotlivé platformy pro vaši aplikaci. .NET core nabízí **doporučuje kolekce** nebo **zakázané** pro .NET Core 2.0, 2.1 a 2.2.

    ![Vyberte možnosti jednotlivé platformy](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-net"></a>Povolit monitorování .NET na straně klienta

Monitorování na straně klienta, je vyjádřit výslovný souhlas pro technologii ASP.NET. Povolení monitorování na straně klienta:

* Vyberte **nastavení** > ** ** aplikace nastavení ***
   * V části Nastavení aplikace přidejte novou **název nastavení aplikace** a **hodnotu**:

     Jméno: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Hodnota: `true`

   * Kliknutím na **Uložit** uložte nastavení a kliknutím na **Restartovat** restartujte aplikaci.

![Snímek obrazovky aplikace nastavení uživatelského rozhraní](./media/azure-web-apps/appinsights-javascript-enabled.png)

Zakázat – monitorování na straně klienta buď Odebereme pár přiřazené hodnotě klíče z nastavení aplikace, nebo nastavte hodnotu na hodnotu false.

## <a name="enable-client-side-monitoring-net-core"></a>Povolit monitorování .NET Core na straně klienta

Monitorování na straně klienta je **ve výchozím nastavení povolené** pro aplikace .NET Core s **doporučuje kolekce**, bez ohledu na to, zda je k dispozici nastavení "APPINSIGHTS_JAVASCRIPT_ENABLED" aplikace.

Pokud z nějakého důvodu chcete vypnout monitorování na straně klienta:

* Vyberte **nastavení** > **nastavení aplikace**
   * V části Nastavení aplikace přidejte novou **název nastavení aplikace** a **hodnotu**:

     Jméno: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Hodnota: `false`

   * Kliknutím na **Uložit** uložte nastavení a kliknutím na **Restartovat** restartujte aplikaci.

![Snímek obrazovky aplikace nastavení uživatelského rozhraní](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatizace monitorování

Chcete-li povolit shromažďování telemetrie s Application Insights, třeba nastavit pouze nastavení aplikace:

   ![Nastavení aplikace app Service s dostupnými nastaveními Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definice nastavení aplikace

|Název nastavení aplikace |  Definice | Hodnota |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Hlavní rozšíření, které řídí monitorování modulu runtime. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Ve výchozím režimu pouze základní funkce jsou povolené s cílem zajistit optimální výkon. | `default` nebo `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Řídí, jestli modul přepisování binární `InstrumentationEngine` zapne. Toto nastavení má vliv na výkon a má vliv na čas úplné spuštění nebo spuštění. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Určuje, zda text tabulky SQL a Azure budou zachyceny spolu s volání závislostí. Upozornění ohledně výkonu: Toto nastavení vyžaduje `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Aplikaci služby nastavení aplikace pomocí Azure Resource Manageru

Nastavení aplikace pro App Services můžete spravovat a nakonfigurovanou [šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Tuto metodu lze použít při nasazování nové prostředky App Service pomocí automatizace Azure Resource Manageru, nebo pro úpravu nastavení stávající prostředky.

Základní struktura JSON nastavení aplikace pro app service je nižší než:

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

Příklad šablony Azure Resource Manageru pomocí nastavení aplikace, které jsou nakonfigurované pro službu Application Insights to [šablony](https://github.com/Andrew-MSFT/BasicImageGallery) může být užitečné, konkrétně část od [řádek 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizace vytvoření prostředku Application Insights a odkaz na nově vytvořené služby App Service.

Pokud chcete vytvořit šablonu Azure Resource Manageru se všemi nastaveními výchozí Application Insights, která je nakonfigurovaná, zahájíte proces jako, pokud se chystáte vytvořit novou webovou aplikaci s Application Insights aktivovaných.

Vyberte **možnosti služby Automation**

   ![Nabídce vytvoření webové aplikace služby App Service](./media/azure-web-apps/create-web-app.png)

Tato možnost vygeneruje nejnovější šablony Azure Resource Manageru s všechna požadovaná nastavení, které jsou nakonfigurované.

  ![Šablony webové aplikace služby App Service](./media/azure-web-apps/arm-template.png)

Zde je příklad, nahraďte všechny výskyty `AppMonitoredSite` názvem vašeho serveru:

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
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> Šablona se generování nastavení aplikace v režimu "Výchozí". Tento režim je optimalizované pro výkon, i když můžete upravit šablonu, kterou chcete aktivovat podle toho, která funkce dáváte přednost.

### <a name="enabling-through-powershell"></a>Povolení přes PowerShell

Chcete-li povolit monitorování prostřednictvím prostředí PowerShell aplikace, musí změnit základní nastavení aplikace. Níže je příklad, který umožňuje monitorování aplikací pro web nazývá "AppMonitoredSite" ve skupině prostředků "AppMonitoredRG", a nakonfiguruje dat k odeslání do "012345678-abcd-ef01-2345-6789abcd" Instrumentační klíč.

```powershell
$app = Get-AzureRmWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzureRmWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Upgrade rozšíření/agenta monitorování

### <a name="upgrading-from-versions-289-and-up"></a>Upgrade z verze 2.8.9 a nahoru

Upgrade z verze 2.8.9 probíhá automaticky, bez jakékoli další akce. Nové monitorování bity jsou doručovány na pozadí do služby app service cíl a při restartování aplikace, neexistoval, použije.

Zjištění verze rozšíření spustíte navštivte `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Snímek obrazovky se cesty url http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Upgrade z verze 1.0.0 - 2.6.5

Počínaje verzí 2.8.9 rozšíření předem nainstalované lokality se používá. Pokud máte starší verzi, můžete aktualizovat prostřednictvím jedním ze dvou způsobů:

* [Upgrade tím, že prostřednictvím portálu](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (I v případě, že máte rozšíření Application Insights pro službu Azure App Service, uživatelské rozhraní zobrazí pouze **povolit** tlačítko. Na pozadí stará rozšíření privátní webu se odeberou.)

* [Upgrade pomocí Powershellu](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Nastavte nastavení aplikace, povolení rozšíření pro předem nainstalované lokality ApplicationInsightsAgent. Zobrazit [povolení prostřednictvím prostředí powershell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Ručně odeberte rozšíření privátní webu s názvem rozšíření Application Insights pro službu Azure App Service.

Pokud provedete upgrade z verze před jazykem 2.5.1, zkontrolujte, že jsou knihovny DLL ApplicationInsigths odstraněn ze složky koše aplikace [najdete v článku řešení potíží](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Řešení potíží

Tady je náš podrobný Průvodce odstraňováním potíží pro rozšíření/agenta na základě sledování pro .NET a .NET Core na základě aplikace běžící v Azure App Service.

> [!NOTE]
> Aplikací Java a Node.js jsou podporovány pouze v Azure App Service prostřednictvím ručního SDK na základě instrumentace a proto se nedá použít následující postup u těchto scénářů.

1. Zkontrolujte, zda je aplikace monitorována prostřednictvím `ApplicationInsightsAgent`.
    * Zkontrolujte, že "ApplicationInsightsAgent_EXTENSION_AGENT aplikace nastavená na hodnotu"~ 2".
2. Ujistěte se, že aplikace splňuje požadavky, které se mají monitorovat.
    * Přejděte na `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Snímek obrazovky https://yoursitename.scm.azurewebsites/applicationinsights stránka s výsledky](./media/azure-web-apps/app-insights-sdk-status.png)

    * Ujistěte se, že `Application Insights Extension Status` je `Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Pokud není spuštěný, postupujte [povolit sledování pokyny Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Zkontrolujte, jestli zdrojový stav existuje a bude vypadat takto: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Pokud podobně jako hodnota není k dispozici, to znamená, že aplikace není spuštěna nebo není podporována. Aby bylo zajištěno, že je aplikace spuštěná, zkuste ručně navštívit adresu url/aplikačními koncovými body aplikace, které vám umožní modulu runtime informace k dispozici.

    * Ujistěte se, že `IKeyExists` je `true`
        * Pokud je false, přidejte ' APPINSIGHTS_INSTRUMENTATIONKEY s identifikátorem guid váš Instrumentační klíč nastavení aplikace.

    * Potvrďte, že neexistují žádné položky pro `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly`, a `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Pokud některé z těchto položek, odeberte z vaší aplikace následující balíčky: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, a `Microsoft.AspNet.TelemetryCorrelation`.

Následující tabulka obsahuje podrobnější vysvětlení významu těchto hodnot, jejich podkladové způsobí, že a doporučené opravy:

|Hodnota problému|Vysvětlení|Napravit
|---- |----|---|
| `AppAlreadyInstrumented:true` | Tato hodnota označuje, že rozšíření zjistil, že některé aspekty sady SDK je již k dispozici v aplikaci a bude regrese. Může být způsobeno odkaz na `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`, nebo `Microsoft.ApplicationInsights`  | Odeberte odkazy. Ve výchozím nastavení některé z těchto odkazů se přidávají z určitých šablon sady Visual Studio a starších verzích sady Visual Studio může přidávat odkazy na `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Tato hodnota může být také způsobeno přítomnost výše uvedené knihovny DLL ve složce aplikace z předchozího nasazení. | Odstraňte složky aplikace k zajištění, že se odeberou tyto knihovny DLL.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Tato hodnota označuje, že rozšíření zjistil odkazy na `Microsoft.AspNet.TelemetryCorrelation` v aplikaci a bude regrese. | Odeberte odkaz.
|`AppContainsDiagnosticSourceAssembly**:true`|Tato hodnota označuje, že rozšíření zjistil odkazy na `System.Diagnostics.DiagnosticSource` v aplikaci a bude regrese.| Odeberte odkaz.
|`IKeyExists:false`|Tato hodnota označuje, že Instrumentační klíč není k dispozici v nastavení aplikace, `APPINSIGHTS_INSTRUMENTATIONKEY`. Možné příčiny: Hodnoty mohou být omylem odstraněna, si vzpomenout na nastavit hodnoty ve skriptu pro automatizaci, atd. | Zajistěte, aby že toto nastavení je k dispozici v nastavení aplikace služby App Service.

Nejnovější informace o agent/rozšíření Application Insights, podívejte se [poznámky k verzi](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Další postup

* [Spusťte profiler v živé aplikaci](../../azure-monitor/app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorujte službu Azure Functions pomocí Application Insights.
* [Povolte odesílání diagnostiky Azure](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md) do Application Insights.
* [Monitorujte metriky stavu služby](../../azure-monitor/platform/data-collection.md), abyste zajistili dostupnost služby a její schopnost dobře reagovat.
* [Přijímejte oznámení o výstrahách](../../azure-monitor/platform/alerts-overview.md) vždy, když nastanou provozní události nebo když metriky překročí prahovou hodnotu.
* [Nastavte testy dostupnosti webu](../../azure-monitor/app/monitor-web-app-availability.md) tak, aby se aktivovaly výstrahy, pokud je webový server mimo provoz.
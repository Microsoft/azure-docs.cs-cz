---
title: Sledování výkonu služeb azure aplikací | Dokumenty společnosti Microsoft
description: Monitorování výkonu aplikací pro služby aplikací Azure. Doba načítání a odezvy grafu, informace o závislostech a nastavení výstrah na výkon.
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 2ec503829d3e6edd7b2b6f6b36314db8a205a8cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297608"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorování výkonu služby Azure App Service

Povolení monitorování vašich webových aplikací založených na ASP.NET a ASP.NET jádra, které běží ve [službě Azure App Services,](https://docs.microsoft.com/azure/app-service/) je teď jednodušší než kdy dřív. Vzhledem k tomu, že dříve jste potřebovali ručně nainstalovat rozšíření webu, nejnovější rozšíření /agent je nyní integrován do image služby aplikace ve výchozím nastavení. Tento článek vás provede povolením monitorování přehledů aplikací a poskytne předběžné pokyny pro automatizaci procesu pro rozsáhlá nasazení.

> [!NOTE]
> Ruční přidání rozšíření webu Application Insights prostřednictvím**rozšíření** **nástrojů** > pro vývoj se zastaralá. Tato metoda instalace rozšíření byla závislá na ručních aktualizacích pro každou novou verzi. Nejnovější stabilní verze rozšíření je nyní [předinstalována](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) jako součást bitové kopie služby App Service. Soubory jsou umístěny v `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` a jsou automaticky aktualizovány s každou stabilní verzi. Pokud budete postupovat podle pokynů založených na agentovi povolit monitorování níže, automaticky odebere zastaralé rozšíření pro vás.

## <a name="enable-application-insights"></a>Povolení Application Insights

Monitorování aplikací pro hostované aplikace Azure App Services můžete povolit dvěma způsoby:

* **Monitorování aplikací na základě agenta** (ApplicationInsightsAgent).  
    * Tato metoda je nejjednodušší povolit a není vyžadována žádná pokročilá konfigurace. Často se označuje jako "runtime" monitorování. Pro Azure App Services doporučujeme minimálně povolení této úrovně monitorování a pak na základě konkrétního scénáře můžete vyhodnotit, zda je potřeba pokročilejší monitorování prostřednictvím ruční instrumentace.

* **Ruční instrumentace aplikace prostřednictvím kódu** instalací Application Insights SDK.

    * Tento přístup je mnohem více přizpůsobitelné, ale vyžaduje [přidání závislosti na balíčcích Application Insights SDK NuGet](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Tato metoda také znamená, že budete muset spravovat aktualizace nejnovější verze balíčků sami.

    * Pokud potřebujete provést vlastní volání rozhraní API ke sledování událostí nebo závislostí, které nejsou ve výchozím nastavení zachyceny pomocí monitorování založeného na agentovi, budete muset použít tuto metodu. Další informace najdete v [článku api pro vlastní události a metriky.](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics)

> [!NOTE]
> Pokud je detekováno monitorování založené na agentech a ruční instrumentace založené na sadě SDK, bude dodrženo pouze ruční nastavení instrumentace. Tím zabráníte odeslání duplicitních dat. Další informace naleznete v [části řešení potíží](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) níže.

## <a name="enable-agent-based-monitoring"></a>Povolit monitorování založené na agentovi

# <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> Kombinace APPINSIGHTS_JAVASCRIPT_ENABLED a urlCompression není podporována. Další informace naleznete v vysvětlení v [části řešení potíží](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. V ovládacím panelu Azure pro vaši aplikační službu **vyberte Přehledy aplikací.**

    ![V části Nastavení zvolte Application Insights.](./media/azure-web-apps/settings-app-insights-01.png)

   * Zvolte vytvoření nového prostředku, pokud jste pro tuto aplikaci již nenastavili prostředek Application Insights. 

     > [!NOTE]
     > Po klepnutí na tlačítko **OK** vytvoříte nový prostředek, budete vyzváni k **použití nastavení monitorování**. Když vyberete **Pokračovat,** propojíte váš nový prostředek Application Insights se službou aplikace, takže to **také spustí restartování vaší služby aplikace**. 

     ![Používejte webovou aplikaci.](./media/azure-web-apps/create-resource-01.png)

2. Po určení, který prostředek použít, můžete zvolit, jak chcete, aby přehledy aplikací shromažďovaly data podle platformy pro vaši aplikaci. ASP.NET monitorování aplikací je ve výchozím nastavení se dvěma různými úrovněmi kolekce.

    ![Výběr možností podle platformy](./media/azure-web-apps/choose-options-new.png)

   * Úroveň **kolekce** .NET Basic nabízí základní funkce APM s jednou instancí.

   * Doporučená úroveň **kolekce** .NET:
       * Přidá trendy využití procesoru, paměti a vstupně-va.
       * Koreluje mikroslužby přes hranice požadavku a závislosti.
       * Shromažďuje trendy využití a umožňuje korelaci z výsledků dostupnosti na transakce.
       * Shromažďuje výjimky neošetřené hostitelským procesem.
       * Zlepšuje přesnost metrik APM při zatížení při použití vzorkování.

3. Chcete-li nakonfigurovat nastavení, jako je vzorkování, které jste mohli dříve ovládat prostřednictvím souboru applicationinsights.config, můžete nyní pracovat se stejnými nastaveními prostřednictvím nastavení aplikace s odpovídající předponou. 

    * Chcete-li například změnit počáteční procento vzorkování, můžete `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` vytvořit nastavení `100`aplikace: a hodnotu .

    * Seznam podporovaných nastavení procesoru adaptivní vzorkování telemetrie můžete konzultovat [kód](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) a [související dokumentaci](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Podporovány jsou následující verze rozhraní .NET Core: ASP.NET Core 2.0, ASP.NET Core 2.1, ASP.NET Core 2.2, ASP.NET Core 3.0

Cílení na úplnou architekturu z .NET Core, samostatné nasazení a linuxové aplikace nejsou aktuálně **podporovány** pomocí monitorování založeného na agentu nebo rozšíření. ([Ruční instrumentace](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) prostřednictvím kódu bude fungovat ve všech předchozích scénářích.)

1. V ovládacím panelu Azure pro vaši aplikační službu **vyberte Přehledy aplikací.**

    ![V části Nastavení zvolte Application Insights.](./media/azure-web-apps/settings-app-insights-01.png)

   * Zvolte vytvoření nového prostředku, pokud jste pro tuto aplikaci již nenastavili prostředek Application Insights. 

     > [!NOTE]
     > Po klepnutí na tlačítko **OK** vytvoříte nový prostředek, budete vyzváni k **použití nastavení monitorování**. Když vyberete **Pokračovat,** propojíte váš nový prostředek Application Insights se službou aplikace, takže to **také spustí restartování vaší služby aplikace**. 

     ![Používejte webovou aplikaci.](./media/azure-web-apps/create-resource-01.png)

2. Po určení, který prostředek použít, můžete zvolit, jak chcete Application Insights shromažďovat data pro platformu pro vaši aplikaci. .NET Core nabízí **doporučenou kolekci** nebo **zakázáno** pro .NET Core 2.0, 2.1, 2.2 a 3.0.

    ![Výběr možností podle platformy](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Ve webové aplikaci App Service v části **Nastavení** > **vyberte Povolit přehledy** > **Enable**aplikací . Monitorování založené na agentovi Node.js je aktuálně ve verzi preview.

# <a name="java"></a>[Java](#tab/java)

Webové aplikace založené na službě Java App Service v současné době nepodporují automatické monitorování založené na agentech a rozšířeních. Chcete-li povolit monitorování aplikace Java, musíte [ručně instrumentovat aplikaci](https://docs.microsoft.com/azure/azure-monitor/app/java-get-started).

# <a name="python"></a>[Python](#tab/python)

Webové aplikace založené na službě Python App Service v současné době nepodporují automatické monitorování založené na agentech nebo příponech. Chcete-li povolit monitorování aplikace Pythonu, musíte [ručně instrumentovat aplikaci](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

---

## <a name="enable-client-side-monitoring"></a>Povolit monitorování na straně klienta

# <a name="net"></a>[.NET](#tab/net)

Monitorování na straně klienta je opt-in pro ASP.NET. Povolení monitorování na straně klienta:

* Vyberte **nastavení** >** **Nastavení aplikace****
   * V části Nastavení aplikace přidejte nový název a **hodnotu** **nastavení aplikace** :

     Jméno:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Hodnota: `true`

   * Kliknutím na **Uložit** uložte nastavení a kliknutím na **Restartovat** restartujte aplikaci.

![Snímek obrazovky s rozhraním nastavení aplikace](./media/azure-web-apps/appinsights-javascript-enabled.png)

Chcete-li zakázat monitorování na straně klienta, odeberte přidružený pár hodnot klíče z nastavení aplikace nebo nastavte hodnotu na hodnotu false.

# <a name="net-core"></a>[.NET Core](#tab/netcore)

Monitorování na straně klienta je **ve výchozím nastavení povoleno** pro aplikace .NET Core s **doporučenou kolekcí**bez ohledu na to, zda je k dispozici nastavení aplikace APPINSIGHTS_JAVASCRIPT_ENABLED.

Pokud z nějakého důvodu chcete zakázat monitorování na straně klienta:

* Vybrat **nastavení** > **nastavení aplikace**
   * V části Nastavení aplikace přidejte nový název a **hodnotu** **nastavení aplikace** :

     Jméno:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Hodnota: `false`

   * Kliknutím na **Uložit** uložte nastavení a kliknutím na **Restartovat** restartujte aplikaci.

![Snímek obrazovky s rozhraním nastavení aplikace](./media/azure-web-apps/appinsights-javascript-disabled.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Chcete-li povolit monitorování na straně klienta pro aplikaci Node.js, je třeba ručně přidat do [aplikace sadu JavaScript na straně klienta](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="java"></a>[Java](#tab/java)

Chcete-li povolit monitorování na straně klienta pro vaši aplikaci Java, je třeba [ručně přidat do aplikace sadu JavaScript na straně klienta](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

# <a name="python"></a>[Python](#tab/python)

Chcete-li povolit monitorování na straně klienta pro vaši aplikaci Pythonu, musíte ručně [přidat do aplikace sadu JavaScript Na straně klienta](https://docs.microsoft.com/azure/azure-monitor/app/javascript).

---

## <a name="automate-monitoring"></a>Automatizace monitorování

Chcete-li povolit shromažďování telemetrie s Application Insights, je třeba nastavit pouze nastavení aplikace:

   ![Nastavení aplikací služby App Service s dostupným nastavením Přehledů aplikací](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definice nastavení aplikace

|Název nastavení aplikace |  Definice | Hodnota |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Hlavní rozšíření, které řídí monitorování za běhu. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Pouze ve výchozím režimu jsou povoleny základní funkce, které zajistí optimální výkon. | `default` nebo `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Určuje, zda bude modul `InstrumentationEngine` pro přepis binárního přepisu zapnutý. Toto nastavení má vliv na výkon a ovlivňuje čas studeného spuštění/spuštění. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Určuje, pokud sql & azure text tabulky se zachytí spolu s volání závislostí. Upozornění na výkon: doba studeného spuštění aplikace bude ovlivněna. Toto nastavení `InstrumentationEngine`vyžaduje . | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Nastavení aplikací služby App Service pomocí Správce prostředků Azure

Nastavení aplikací pro služby App Services lze spravovat a konfigurovat pomocí [šablon Azure Resource Manager .](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates) Tuto metodu lze použít při nasazování nových prostředků služby App Service s automatizací Azure Resource Manager nebo pro úpravu nastavení stávajících prostředků.

Základní struktura nastavení aplikace JSON pro službu aplikace je níže:

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

Příklad šablony Azure Resource Manager s nastavením aplikací nakonfigurovaným pro Application Insights může být tato [šablona](https://github.com/Andrew-MSFT/BasicImageGallery) užitečná, konkrétně v části začínající na [řádku 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatizujte vytvoření prostředku Application Insights a odkaz na nově vytvořenou službu App Service.

Chcete-li vytvořit šablonu Správce prostředků Azure se všemi nakonfigurovanými výchozími nastaveními application insights, zahajte proces, jako byste chtěli vytvořit novou webovou aplikaci s povolenými přehledy aplikací.

Vybrat **možnosti automatizace**

   ![Nabídka pro vytváření webových aplikací služby App Service](./media/azure-web-apps/create-web-app.png)

Tato možnost generuje nejnovější šablonu Azure Resource Manager se všemi nakonfigurovanými požadovanými nastaveními.

  ![Šablona webové aplikace Služby Aplikace](./media/azure-web-apps/arm-template.png)

Níže je ukázka, nahraďte všechny instance `AppMonitoredSite` s názvem webu:

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

### <a name="enabling-through-powershell"></a>Povolení prostřednictvím PowerShellu

Chcete-li povolit monitorování aplikací prostřednictvím prostředí PowerShell, je třeba změnit pouze základní nastavení aplikace. Níže je ukázka, která umožňuje monitorování aplikací pro webové stránky s názvem "AppMonitoredSite" ve skupině prostředků "AppMonitoredRG", a nakonfiguruje data, která mají být odeslána do klíče instrumentace "012345678-abcd-ef01-2345-6789abcd".

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

## <a name="upgrade-monitoring-extensionagent"></a>Rozšíření/agent monitorování upgradu

### <a name="upgrading-from-versions-289-and-up"></a>Upgrade z verzí 2.8.9 a novějších

Upgrade z verze 2.8.9 probíhá automaticky, bez dalších akcí. Nové monitorovací bity jsou dodávány na pozadí do cílové služby aplikace a při restartování aplikace budou vyzvednuty.

Chcete-li zkontrolovat, kterou verzi rozšíření používáte, navštivte`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Snímek obrazovky s adresou URLhttp://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Upgrade z verzí 1.0.0 - 2.6.5

Počínaje verzí 2.8.9 se používá předinstalované rozšíření lokality. Pokud jste starší verze, můžete aktualizovat jedním ze dvou způsobů:

* [Upgrade povolením prostřednictvím portálu](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (I když máte nainstalované rozšíření Application Insights pro službu Azure App Service, ui zobrazí pouze **povolit** tlačítko. V zákulisí bude odebráno staré rozšíření soukromého webu.)

* [Upgrade přes Prostředí PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Nastavte nastavení aplikace tak, aby bylo možné povolit předinstalované rozšíření lokality ApplicationInsightsAgent. Viz [Povolení prostřednictvím powershellu](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Ručně odeberte privátní rozšíření webu s názvem Rozšíření Application Insights pro Azure App Service.

Pokud je upgrade proveden z verze před verzí 2.5.1, zkontrolujte, zda jsou dll applicationInsigths odebrány ze složky přihrádky [aplikace, viz postup řešení potíží](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Řešení potíží

Níže je náš podrobný průvodce odstraňováním potíží pro monitorování založené na rozšíření/agentovi pro aplikace založené na rozhraní .NET a .NET Core spuštěné ve službě Azure App Services.

> [!NOTE]
> Aplikace java jsou podporované jenom ve službě Azure App Services prostřednictvím ruční instrumentace založené na sadě SDK, a proto se na tyto scénáře nevztahují následující kroky.

1. Zkontrolujte, zda je `ApplicationInsightsAgent`aplikace monitorována prostřednictvím aplikace .
    * Zkontrolujte, zda `ApplicationInsightsAgent_EXTENSION_VERSION` je nastavení aplikace nastaveno na hodnotu "~2".
2. Ujistěte se, že aplikace splňuje požadavky, které mají být sledovány.
    * Přejít na`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Snímek https://yoursitename.scm.azurewebsites/applicationinsights obrazovky se stránkou s výsledky](./media/azure-web-apps/app-insights-sdk-status.png)

    * Zkontrolujte, `Application Insights Extension Status` zda je`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Pokud není spuštěn, postupujte podle [pokynů pro monitorování povolit přehledy aplikací.](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights)

    * Zkontrolujte, zda zdroj stavu existuje a vypadá takto:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Pokud podobná hodnota není k dispozici, znamená to, že aplikace není aktuálně spuštěna nebo není podporována. Chcete-li zajistit, aby aplikace byla spuštěna, zkuste ručně navštívit koncové body adresy URL nebo aplikace aplikace, což umožní, aby byly k dispozici informace o běhu.

    * Potvrďte, že `IKeyExists` je`true`
        * Pokud ano `false`, `APPINSIGHTS_INSTRUMENTATIONKEY` `APPLICATIONINSIGHTS_CONNECTION_STRING` přidejte a s ikey guid do nastavení aplikace.

    * Zkontrolujte, zda neexistují `AppAlreadyInstrumented` `AppContainsDiagnosticSourceAssembly`žádné `AppContainsAspNetTelemetryCorrelationAssembly`položky pro , a .
        * Pokud některá z těchto položek existuje, odeberte z přihlášky následující balíčky: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource`, a `Microsoft.AspNet.TelemetryCorrelation`.

Níže uvedená tabulka obsahuje podrobnější vysvětlení toho, co tyto hodnoty znamenají, jejich základní příčiny a doporučené opravy:

|Hodnota problému|Vysvětlení|Oprava
|---- |----|---|
| `AppAlreadyInstrumented:true` | Tato hodnota označuje, že rozšíření zjištěno, že některé aspekty sady SDK je již k dispozici v aplikaci a bude back-off. Může to být způsobeno `System.Diagnostics.DiagnosticSource`odkazem na , `Microsoft.AspNet.TelemetryCorrelation`nebo`Microsoft.ApplicationInsights`  | Odstraňte odkazy. Některé z těchto odkazů jsou přidány ve výchozím nastavení z některých šablon sady Visual `Microsoft.ApplicationInsights`Studio a starší verze sady Visual Studio mohou přidávat odkazy na .
|`AppAlreadyInstrumented:true` | Pokud aplikace cílí na rozhraní .NET Core 2.1 nebo 2.2 a odkazuje na metabalíček [Microsoft.AspNetCore.All,](https://www.nuget.org/packages/Microsoft.AspNetCore.All) pak přináší application insights a rozšíření bude back-off. | Zákazníkům na rozhraní .NET Core 2.1,2.2 se [doporučuje](https://github.com/aspnet/Announcements/issues/287) použít metabalíček Microsoft.AspNetCore.App.|
|`AppAlreadyInstrumented:true` | Tato hodnota může být také způsobena přítomností výše uvedených dlls ve složce aplikace z předchozího nasazení. | Vyčistěte složku aplikace, abyste zajistili, že budou tyto dll odstraněny. Zkontrolujte adresář bin místní aplikace i adresář wwwroot ve službě App Service. (Chcete-li zkontrolovat adresář wwwroot webové aplikace App Service: Pokročilé nástroje (Kudu) > ladicí konzoly > CMD > home\site\wwwroot).
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Tato hodnota označuje, že `Microsoft.AspNet.TelemetryCorrelation` rozšíření zjištěny odkazy v aplikaci a bude back-off. | Odeberte odkaz.
|`AppContainsDiagnosticSourceAssembly**:true`|Tato hodnota označuje, že `System.Diagnostics.DiagnosticSource` rozšíření zjištěny odkazy v aplikaci a bude back-off.| Odeberte odkaz.
|`IKeyExists:false`|Tato hodnota označuje, že instrumentace klíč není `APPINSIGHTS_INSTRUMENTATIONKEY`k dispozici v AppSetting, . Možné příčiny: Hodnoty mohly být omylem odebrány, zapomněli jste nastavit hodnoty ve skriptu automatizace atd. | Ujistěte se, že nastavení je k dispozici v nastavení aplikace App Service.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED a urlCompression není podporována

Pokud používáte APPINSIGHTS_JAVASCRIPT_ENABLED=true v případech, kdy je obsah kódován, může se stát, že se vám nabudou chyby, jako jsou: 

- Chyba přepisu adresy URL 500
- 500.53 Chyba modulu přepisování adres URL se zprávou Pravidla odchozího přepisu nelze použít, pokud je zakódován obsah odpovědi HTTP ("gzip"). 

To je způsobeno APPINSIGHTS_JAVASCRIPT_ENABLED nastavení aplikace je nastavena na true a kódování obsahu jsou k dispozici ve stejnou dobu. Tento scénář ještě není podporován. Chcete-li odebrat APPINSIGHTS_JAVASCRIPT_ENABLED z nastavení aplikace, je to toto řešení. Bohužel to znamená, že pokud je javascriptová instrumentace na straně klienta/prohlížeče stále vyžadována, jsou pro vaše webové stránky zapotřebí ruční odkazy sady SDK. Postupujte [podle pokynů](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) pro ruční instrumentaci pomocí sady JavaScript SDK.

Nejnovější informace o agentovi/rozšíření Application Insights najdete v [poznámkách k verzi](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

### <a name="php-and-wordpress-are-not-supported"></a>PHP a WordPress nejsou podporovány

PHP a WordPress stránky nejsou podporovány. V současné době neexistuje žádná oficiálně podporovaná sada SDK/agent pro monitorování těchto úloh na straně serveru. Nicméně, ručně instrumentace client-side transakce na PHP nebo WordPress stránky přidáním client-side javascript na vaše webové stránky lze provést pomocí [JavaScript SDK](https://docs.microsoft.com/azure/azure-monitor/app/javascript). 

## <a name="next-steps"></a>Další kroky
* [Spusťte profiler v živé aplikaci](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorujte službu Azure Functions pomocí Application Insights.
* [Povolte odesílání diagnostiky Azure](../platform/diagnostics-extension-to-application-insights.md) do Application Insights.
* [Monitorujte metriky stavu služby](../platform/data-platform.md), abyste zajistili dostupnost služby a její schopnost dobře reagovat.
* [Přijímejte oznámení o výstrahách](../platform/alerts-overview.md) vždy, když nastanou provozní události nebo když metriky překročí prahovou hodnotu.
* Použitím [Application Insights pro aplikace JavaScript a webové stránky](javascript.md) získávejte telemetrické údaje klienta z prohlížečů, které webovou stránky navštíví.
* [Nastavte testy dostupnosti webu](monitor-web-app-availability.md) tak, aby se aktivovaly výstrahy, pokud je webový server mimo provoz.

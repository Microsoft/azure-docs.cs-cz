---
title: Monitorování výkonu Azure App Services | Microsoft Docs
description: Sledování výkonu aplikací pro Azure App Services. Zatížení grafu a doba odezvy, informace o závislostech a nastavení výstrah pro výkon.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mbullwin
ms.openlocfilehash: 4f296aae6c147b0d5209276dbd008a1207837cfd
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875205"
---
# <a name="monitor-azure-app-service-performance"></a>Monitorování výkonu Azure App Service

Povolení monitorování webových aplikací založených na .NET a .NET Core běžících na [Azure App Services](https://docs.microsoft.com/azure/app-service/) je teď jednodušší než kdy dřív. Vzhledem k tomu, že jste předtím museli ručně nainstalovat rozšíření lokality, je ve výchozím nastavení do image služby App Service standardně integrováno nejnovější rozšíření nebo agent. Tento článek vás provede povolením Application Insights monitorování a poskytuje předběžné pokyny pro automatizaci procesu pro rozsáhlá nasazení.

> [!NOTE]
> Ruční přidání rozšíření Application Insights webu prostřednictvím**rozšíření** **nástrojů** > pro vývoj je zastaralé. Tato metoda instalace rozšíření byla závislá na ruční aktualizaci pro každou novou verzi. Nejnovější stabilní verze rozšíření je teď předinstalována [](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) jako součást image App Service. Soubory jsou umístěny v `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` a jsou automaticky aktualizovány s každou stabilní verzí. Pokud budete postupovat podle pokynů na základě agentů a zapnout monitorování níže, automaticky se odebere zastaralé rozšíření za vás.

## <a name="enable-application-insights"></a>Povolení Application Insights

Existují dva způsoby, jak povolit monitorování aplikací pro hostované aplikace Azure App Services:

* **Monitorování aplikací na základě agentů** (ApplicationInsightsAgent).  
    * Tato metoda je nejjednodušší pro povolení a není nutná žádná pokročilá konfigurace. Často se označuje jako monitorování za běhu. V případě Azure App Services doporučujeme, abyste aspoň povolili tuto úroveň monitorování a pak na základě vašeho konkrétního scénáře mohli vyhodnotit, jestli je potřeba rozšířené monitorování prostřednictvím ruční instrumentace.

* **Ruční instrumentace aplikace pomocí kódu** instalací sady Application Insights SDK.

    * Tento přístup je mnohem přizpůsobitelnější, ale vyžaduje [Přidání závislosti na balíčky NuGet sady Application Insights SDK](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Tato metoda také znamená, že je nutné spravovat aktualizace na nejnovější verzi balíčků sami.

    * Pokud potřebujete vlastní volání rozhraní API ke sledování událostí nebo závislostí, které se ve výchozím nastavení nezachycují pomocí monitorování založeného na agentech, musíte použít tuto metodu. Další informace najdete v [článku rozhraní API pro vlastní události a metriky](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) .

> [!NOTE]
> Pokud se zjistí jenom monitorování na základě agentů a ruční instrumentaci založené na sadě SDK, bude se dodržovat jenom ruční nastavení instrumentace. K tomu je potřeba zabránit odeslání duplicitních dat. Další informace o této části najdete v [části řešení potíží](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) níže.

## <a name="enable-agent-based-monitoring-for-net-applications"></a>Povolit monitorování na základě agentů pro aplikace .NET

> [!NOTE]
> Kombinace APPINSIGHTS_JAVASCRIPT_ENABLED a urlCompression se nepodporuje. Další informace najdete v tématu Vysvětlení v [části Poradce při potížích](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Vyberte Application Insights** v Ovládacích panelech Azure pro vaši službu App Service.

    ![V části nastavení vyberte Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Pokud jste ještě nevytvořili prostředek Application Insights pro tuto aplikaci, vyberte možnost vytvořit nový prostředek. 

     > [!NOTE]
     > Když kliknete na **OK** , vytvoří se nový prostředek, zobrazí se výzva k **použití nastavení monitorování**. Když vyberete **pokračovat** , připojí se k vaší službě App service nový Application Insights prostředek. tím se taky **aktivuje restartování služby App Service**. 

     ![Používejte webovou aplikaci.](./media/azure-web-apps/create-resource-01.png)

2. Jakmile určíte, který prostředek se má použít, můžete zvolit, jak má Application Insights shromažďovat data na platformu pro vaši aplikaci. Monitorování aplikací ASP.NET se používá ve výchozím nastavení se dvěma různými úrovněmi shromažďování.

    ![Zvolit možnosti na platformu](./media/azure-web-apps/choose-options-new.png)

   * Úroveň **kolekce .NET Basic** nabízí základní možnosti APM s jednou instancí.

   * Úroveň **kolekce doporučená** pro .NET:
       * Přidá trendy využití procesoru, paměti a vstupně-výstupních operací.
       * Koreluje mikroslužby napříč hranicemi požadavků a závislostí.
       * Shromažďuje trendy využití a umožňuje korelaci z výsledků dostupnosti až po transakce.
       * Shromažďuje výjimky neošetřené hostitelským procesem.
       * Při použití vzorkování vylepšuje přesnost metriky APM při zatížení.

3. Chcete-li nakonfigurovat nastavení jako vzorkování, které byste mohli dříve řídit prostřednictvím souboru ApplicationInsights. config, můžete nyní s těmito nastaveními pracovat pomocí nastavení aplikace s odpovídající předponou. 

    * Chcete-li například změnit procento počátečního vzorkování, můžete vytvořit nastavení aplikace pro: `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` a `100`hodnotu.

    * Seznam podporovaných nastavení procesoru telemetrie pro adaptivní vzorkování můžete zobrazit v [kódu](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) a v [související dokumentaci](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-for-net-core-applications"></a>Povolit monitorování na základě agentů pro aplikace .NET Core

Podporovány jsou následující verze rozhraní .NET Core: ASP.NET Core 2,0, ASP.NET Core 2,1, ASP.NET Core 2,2

Cílení na plnou verzi rozhraní .NET Core, samostatné nasazení a ASP.NET Core 3,0 se v současnosti **nepodporují** pomocí monitorování založeného na agentech nebo rozšíření. ([Ruční instrumentace](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) přes kód bude fungovat ve všech předchozích scénářích.)

1. **Vyberte Application Insights** v Ovládacích panelech Azure pro vaši službu App Service.

    ![V části nastavení vyberte Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Pokud jste ještě nevytvořili prostředek Application Insights pro tuto aplikaci, vyberte možnost vytvořit nový prostředek. 

     > [!NOTE]
     > Když kliknete na **OK** , vytvoří se nový prostředek, zobrazí se výzva k **použití nastavení monitorování**. Když vyberete **pokračovat** , připojí se k vaší službě App service nový Application Insights prostředek. tím se taky **aktivuje restartování služby App Service**. 

     ![Používejte webovou aplikaci.](./media/azure-web-apps/create-resource-01.png)

2. Jakmile určíte, který prostředek se má použít, můžete zvolit způsob, jakým má Application Insights shromažďovat data na platformu pro vaši aplikaci. .NET Core nabízí **doporučenou kolekci** nebo **zakázanou** pro .net Core 2,0, 2,1 a 2,2.

    ![Zvolit možnosti na platformu](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-for-net-applications"></a>Povolit monitorování na straně klienta pro aplikace .NET

Monitorování na straně klienta je výslovný souhlas pro ASP.NET. Postup při povolování monitorování na straně klienta:

* Vyberte **nastavení** > * * * * nastavení aplikace * * * *.
   * V části nastavení aplikace přidejte název a **hodnotu** **Nastavení nové aplikace** :

     Jméno:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Hodnota: `true`

   * Kliknutím na **Uložit** uložte nastavení a kliknutím na **Restartovat** restartujte aplikaci.

![Snímek obrazovky uživatelského rozhraní nastavení aplikace](./media/azure-web-apps/appinsights-javascript-enabled.png)

Chcete-li zakázat monitorování na straně klienta, buď z nastavení aplikace odeberte dvojici hodnoty klíče, nebo hodnotu nastavte na hodnotu NEPRAVDA.

## <a name="enable-client-side-monitoring-for-net-core-applications"></a>Povolit monitorování na straně klienta pro aplikace .NET Core

Monitorování na straně klienta je **ve výchozím nastavení povolené** pro aplikace .NET Core s **doporučenou kolekcí**bez ohledu na to, jestli je přítomné nastavení aplikace APPINSIGHTS_JAVASCRIPT_ENABLED.

Pokud z nějakého důvodu chcete vypnout monitorování na straně klienta:

* Vybrat **Nastavení** > **aplikace**
   * V části nastavení aplikace přidejte název a **hodnotu** **Nastavení nové aplikace** :

     Jméno:`APPINSIGHTS_JAVASCRIPT_ENABLED`

     Hodnota: `false`

   * Kliknutím na **Uložit** uložte nastavení a kliknutím na **Restartovat** restartujte aplikaci.

![Snímek obrazovky uživatelského rozhraní nastavení aplikace](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatizace monitorování

Aby bylo možné povolit shromažďování telemetrie s Application Insights, je třeba nastavit pouze nastavení aplikace:

   ![App Service nastavení aplikace s dostupnými nastaveními Application Insights](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definice nastavení aplikace

|Název nastavení aplikace |  Definice | Hodnota |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Hlavní rozšíření, které řídí monitorování za běhu. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Jenom ve výchozím režimu jsou k dispozici základní funkce, aby se zajistil optimální výkon. | `default`nebo `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Určuje, zda bude modul `InstrumentationEngine` binárního zápisu zapnutý. Toto nastavení má vliv na výkon a má vliv na čas spuštění a spuštění. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Ovládací prvky, pokud se v SQL & text tabulky Azure bude zachytávat spolu s voláními závislostí. Upozornění výkonu: Toto nastavení vyžaduje `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>App Service nastavení aplikace s Azure Resource Manager

Nastavení aplikace pro App Services lze spravovat a konfigurovat pomocí [šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Tuto metodu lze použít při nasazování nových App Servicech prostředků pomocí Automatizace Azure Resource Manager nebo pro úpravu nastavení existujících prostředků.

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

Níže je ukázka, nahraďte všechny instance `AppMonitoredSite` s názvem vašeho webu:

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
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> Šablona vygeneruje nastavení aplikace ve výchozím režimu. Tento režim je optimalizovaný pro výkon, ale můžete upravit šablonu, aby se aktivovaly libovolné funkce, které dáváte přednost.

### <a name="enabling-through-powershell"></a>Povolení přes PowerShell

Aby bylo možné povolit monitorování aplikací prostřednictvím prostředí PowerShell, je nutné změnit pouze nastavení základní aplikace. Níže je ukázka, která umožňuje monitorování aplikací pro web s názvem "AppMonitoredSite" ve skupině prostředků "AppMonitoredRG" a konfiguruje data, která se mají odeslat do klíče instrumentace "012345678-abcd-ef01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Upgradovat rozšíření nebo agenta monitorování

### <a name="upgrading-from-versions-289-and-up"></a>Upgrade z verzí 2.8.9 a up

Upgrade z verze 2.8.9 proběhne automaticky bez dalších akcí. Nové bity monitorování se doručí na pozadí cílové službě App Service a při restartování aplikace se budou vyzvednout.

Pokud chcete zjistit, kterou verzi rozšíření používáte, přejděte na`http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Snímek obrazovky s cestou URL http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Upgrade z verzí 1.0.0-2.6.5

Počínaje verzí 2.8.9 se používá předem nainstalovaná rozšíření webu. Pokud používáte starší verzi, můžete aktualizovat prostřednictvím jednoho ze dvou způsobů:

* [Upgrade povolíte prostřednictvím portálu](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (I v případě, že máte rozšíření Application Insights pro Azure App Service nainstalované, uživatelské rozhraní zobrazí jenom tlačítko **Povolit** . Po pozadí bude staré rozšíření privátního webu odebráno.)

* [Upgrade prostřednictvím PowerShellu](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Nastavte nastavení aplikace tak, aby povolovalo předinstalované rozšíření webu ApplicationInsightsAgent. Viz [povolení prostřednictvím PowerShellu](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Ručně odeberte rozšíření privátního webu s názvem Application Insights příponu pro Azure App Service.

Pokud je upgrade proveden z verze před aplikací 2.5.1, zkontrolujte, zda jsou knihovny DLL ApplicationInsigths odebrány ze složky bin aplikace, [v tématu Postup řešení potíží](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>Řešení potíží

Níže najdete naše podrobné pokyny k odstraňování potíží pro monitorování rozšíření/na základě agentů pro aplikace založené na platformě .NET a .NET Core běžící na Azure App Services.

> [!NOTE]
> Aplikace Java a Node. js se podporují jenom v Azure App Services prostřednictvím ruční instrumentace založené na sadě SDK, a proto se tyto kroky nevztahují na tyto scénáře.

1. Ověřte, že je aplikace monitorována `ApplicationInsightsAgent`prostřednictvím.
    * Ověřte, `ApplicationInsightsAgent_EXTENSION_VERSION` že nastavení aplikace je nastavené na hodnotu ~ 2.
2. Ujistěte se, že aplikace splňuje požadavky, které se mají monitorovat.
    * Přejít na`https://yoursitename.scm.azurewebsites.net/ApplicationInsights`

    ![Snímek stránky https://yoursitename.scm.azurewebsites/applicationinsights výsledků](./media/azure-web-apps/app-insights-sdk-status.png)

    * Potvrďte, `Application Insights Extension Status` že je`Pre-Installed Site Extension, version 2.8.12.1527, is running.`
        * Pokud není spuštěný, postupujte podle [pokynů pro monitorování povolení Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights) .

    * Potvrďte, že zdroj stavu existuje a vypadá takto:`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * Pokud není k dispozici podobná hodnota, znamená to, že aplikace momentálně není spuštěná nebo není podporovaná. Chcete-li zajistit, aby aplikace běžela, zkuste ručně navštívit koncové body adresy URL nebo aplikace, čímž umožníte zpřístupnění běhových informací.

    * Potvrďte `IKeyExists` , že je`true`
        * Pokud má hodnotu false, přidejte do nastavení aplikace APPINSIGHTS_INSTRUMENTATIONKEY s identifikátorem GUID ikey.

    * Potvrďte, že nejsou k dispozici `AppContainsDiagnosticSourceAssembly`žádné položky `AppContainsAspNetTelemetryCorrelationAssembly`pro `AppAlreadyInstrumented`, a.
        * Pokud existuje kterákoli z těchto položek, odeberte z aplikace následující balíčky: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource` `Microsoft.AspNet.TelemetryCorrelation`a.

Následující tabulka obsahuje podrobnější vysvětlení toho, co tyto hodnoty znamenají, jejich základní příčiny a Doporučené opravy:

|Hodnota problému|Vysvětlení|Napravit
|---- |----|---|
| `AppAlreadyInstrumented:true` | Tato hodnota označuje, že rozšíření zjistilo, že některé aspekty sady SDK už v aplikaci existují a že se bude zálohovat. Důvodem může být odkaz na `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation`, nebo`Microsoft.ApplicationInsights`  | Odeberte odkazy. Některé z těchto odkazů jsou ve výchozím nastavení přidány z určitých šablon sady Visual Studio a starší verze sady Visual Studio mohou přidat odkazy `Microsoft.ApplicationInsights`na.
|`AppAlreadyInstrumented:true` | Pokud je aplikace cílena na rozhraní .NET Core 2,1 nebo 2,2 a odkazuje na soubor [Microsoft. AspNetCore. All](https://www.nuget.org/packages/Microsoft.AspNetCore.All) meta-package, pak Application Insights a rozšíření bude zase vypnuto. | Pro zákazníky s .NET Core 2.1, 2.2 se místo toho [doporučuje](https://github.com/aspnet/Announcements/issues/287) použít meta-package Microsoft. AspNetCore. app.|
|`AppAlreadyInstrumented:true` | Tato hodnota může být také způsobena přítomností výše uvedených knihoven DLL ve složce aplikace z předchozího nasazení. | Vyčistěte složku aplikace, abyste měli jistotu, že se odeberou tyto knihovny DLL.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Tato hodnota označuje, že rozšíření zjistilo `Microsoft.AspNet.TelemetryCorrelation` odkazy na v aplikaci a bude se přepínat. | Odeberte odkaz.
|`AppContainsDiagnosticSourceAssembly**:true`|Tato hodnota označuje, že rozšíření zjistilo `System.Diagnostics.DiagnosticSource` odkazy na v aplikaci a bude se přepínat.| Odeberte odkaz.
|`IKeyExists:false`|Tato hodnota označuje, že klíč instrumentace není přítomen v AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Možné příčiny: Hodnoty mohou být omylem odstraněny, zapomněli jste nastavit hodnoty ve skriptu automatizace atd. | Ujistěte se, že se nastavení nachází v nastavení aplikace App Service.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED a urlCompression se nepodporují.

Pokud používáte APPINSIGHTS_JAVASCRIPT_ENABLED = true v případech, kde je obsah kódovaný, může dojít k chybám jako: 

- 500 Chyba při zápisu adresy URL
- Chyba modulu pro přepis adresy URL 500,53 s pravidly pro přepsání odchozího zápisu zprávy nelze použít, je-li obsah odpovědi HTTP kódován (' gzip '). 

Je to proto, že nastavení aplikace APPINSIGHTS_JAVASCRIPT_ENABLED je nastavené na hodnotu true a kódování obsahu je přítomné současně. Tento scénář se ještě nepodporuje. Alternativním řešením je odebrat APPINSIGHTS_JAVASCRIPT_ENABLED z nastavení aplikace. To bohužel znamená, že pokud se pořád vyžaduje instrumentace JavaScriptu na straně klienta nebo prohlížeče, pro vaše webové stránky jsou nutné ruční odkazy na sadu SDK. Postupujte prosím podle [pokynů](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) pro ruční instrumentaci pomocí sady JavaScript SDK.

Nejnovější informace o Application Insights agenta nebo rozšíření najdete v poznámkách k [verzi](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Další kroky
* [Spusťte profiler v živé aplikaci](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) – monitorujte službu Azure Functions pomocí Application Insights.
* [Povolte odesílání diagnostiky Azure](../platform/diagnostics-extension-to-application-insights.md) do Application Insights.
* [Monitorujte metriky stavu služby](../platform/data-platform.md), abyste zajistili dostupnost služby a její schopnost dobře reagovat.
* [Přijímejte oznámení o výstrahách](../platform/alerts-overview.md) vždy, když nastanou provozní události nebo když metriky překročí prahovou hodnotu.
* Použitím [Application Insights pro aplikace JavaScript a webové stránky](javascript.md) získávejte telemetrické údaje klienta z prohlížečů, které webovou stránky navštíví.
* [Nastavte testy dostupnosti webu](monitor-web-app-availability.md) tak, aby se aktivovaly výstrahy, pokud je webový server mimo provoz.

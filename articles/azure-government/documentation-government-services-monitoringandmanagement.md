---
title: Azure Government Monitorování a správa | Microsoft Docs
description: To poskytuje srovnání funkcí a pokynů pro vývoj aplikací pro Azure Government.
services: azure-government
cloud: gov
author: gsacavdm
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.topic: article
ms.workload: azure-government
ms.date: 12/11/2019
ms.author: gsacavdm
ms.openlocfilehash: 38eb44e6355ddcb5ac78f5d8bb2008ea5a0f0cdf
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127939"
---
# <a name="azure-government-monitoring--management"></a>Azure Government Monitorování a správa
Tento článek popisuje varianty a požadavky služby monitorování a správy pro prostředí Azure Government.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="advisor"></a>Advisor
Poradce je všeobecně dostupný v Azure Government.

Další informace najdete v tématu [Veřejná dokumentace služby Advisor](../advisor/advisor-overview.md).

### <a name="variations"></a>Odlišnost
Následující doporučení poradce v současnosti nejsou v Azure Government k dispozici:

* Vysoká dostupnost
  * Konfigurace brány VPN na aktivní – aktivní pro odolnost připojení
  * Vytváření upozornění na Azure Service Health, která se mají upozornit, když se na vás bude týkat problémy Azure
  * Konfigurace koncových bodů Traffic Manager pro odolnost
  * Použití obnovitelného odstranění pro účet Azure Storage
* Výkon
  * Zlepšení výkonu a spolehlivosti App Service
  * Omezte čas DNS na živý v profilu Traffic Manager, aby se převzaly služby při selhání do funkčních koncových bodů rychleji
  * Zvýšení výkonu SQL Data Warehouse
  * Použití služby Premium Storage
  * Migrace účtu úložiště na Azure Resource Manager
* Náklady
  * Koupit rezervované instance virtuálních počítačů, abyste ušetřili peníze nad náklady na průběžné platby
  * Eliminujte nezajištěné okruhy ExpressRoute.
  * Odstranění nebo změna konfigurace nečinných bran virtuálních sítí

Výpočet použitý k doporučení, abyste měli k disAzure Government správné velikosti virtuálních počítačů, a jejich vypnutí je následující:

Advisor monitoruje využití virtuálních počítačů po dobu 7 dnů a identifikuje virtuální počítače s nízkým využitím. Virtuální počítače se považují za nízké využití, pokud jejich využití CPU je 5% nebo méně a jejich využití sítě je méně než 2%, nebo pokud je možné aktuální zatížení přizpůsobit menší velikosti virtuálního počítače. Pokud chcete mít více agresivní při identifikaci nevyužitých virtuálních počítačů, můžete upravit pravidlo využití CPU pro každé předplatné.

## <a name="automation"></a>Automation
Automatizace je všeobecně dostupná v Azure Government.

Další informace najdete v tématu o [veřejné dokumentaci automatizace](../automation/automation-intro.md).

## <a name="azure-migrate"></a>Azure Migrate

Azure Migrate je všeobecně k dispozici v Azure Government.

Další informace najdete v [dokumentaci Azure Migrate](../migrate/migrate-overview.md).

### <a name="variations"></a>Odlišnost
Následující funkce Azure Migrate nejsou v Azure Government v současnosti dostupné:

* Funkce vizualizace závislosti není v Azure Government k dispozici, protože Azure Migrate závisí na Service Map pro vizualizaci závislostí, která v tuto chvíli není v Azure Government dostupná.
* Posuzování pro Azure Government můžete vytvořit pouze jako cílové oblasti a pomocí Azure Government nabídek.

## <a name="backup"></a>Backup
Zálohování je všeobecně dostupné v Azure Government.

Další informace najdete v tématu [Azure Government Backup](documentation-government-services-backup.md).

## <a name="policy"></a>Zásada
Zásady jsou všeobecně dostupné v Azure Government.

Další informace najdete v tématu [Azure Policy](../governance/policy/overview.md).

## <a name="site-recovery"></a>Site Recovery
Azure Site Recovery je všeobecně k dispozici v Azure Government.

Další informace najdete v tématu [Site Recovery komerční dokumentaci](../site-recovery/site-recovery-overview.md).

### <a name="variations"></a>Odlišnost
Následující funkce Site Recovery nejsou v tuto chvíli dostupné v Azure Government:
* E-mailové oznámení

| Site Recovery | Classic | Resource Manager |
| --- | --- | --- |
| VMware/fyzické  | GA | GA |
| Hyper-V | GA | GA |
| Z lokality do lokality | GA | GA |

Následující adresy URL pro Site Recovery se liší v Azure Government:

| Veřejné Azure | Azure Government | Poznámky: |
| --- | --- | --- |
| \*.hypervrecoverymanager.windowsazure.com | \*. hypervrecoverymanager.windowsazure.us | Přístup ke službě Site Recovery |
| \*.backup.windowsazure.com  | \*. backup.windowsazure.us | Přístup ke službě ochrany |
| \*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net | Pro ukládání snímků virtuálních počítačů |
| https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi | Stažení MySQL |

## <a name="monitor"></a>Monitorování
Azure Monitor je všeobecně k dispozici v Azure Government.

Další informace najdete v tématu [monitorování komerční dokumentace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview).

### <a name="variations"></a>Odlišnost
Následující části obsahují podrobnosti o rozdílech a řešeních pro funkce Azure Monitor v Azure Government:

#### <a name="action-groups"></a>Skupiny akcí
Skupiny akcí jsou všeobecně dostupné v Azure Government bez rozdílů od komerčních Azure.   

#### <a name="activity-log-alerts"></a>Upozornění protokolu aktivit
Výstrahy protokolu aktivit jsou všeobecně dostupné v Azure Government bez rozdílů od komerčních Azure.

#### <a name="alerts-experience"></a>Prostředí výstrah
Prostředí uživatelských rozhraní s jednotnými výstrahami je k dispozici pro výstrahy metrik a protokolování v Azure Government.

#### <a name="autoscale"></a>Automatické škálování
Automatické škálování je všeobecně dostupné v Azure Government.

Pokud používáte volání PowerShellu/ARM/REST k zadání nastavení, nastavte "umístění" automatického škálování na "USGov) – Virginia" nebo "USGov Iowa". Prostředek, který cílí na automatické škálování, může existovat v libovolné oblasti. Příklad nastavení je následující:

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:05:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "1"
$rule2 = New-AzAutoscaleRule -MetricName "Requests" -MetricResourceId "/subscriptions/S1/resourceGroups/RG1/providers/Microsoft.Web/sites/WebSite1" -Operator GreaterThan -MetricStatistic Average -Threshold 10 -TimeGrain 00:01:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue "2"
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1, $rule2 -Name "MyProfile"
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri https://example.com?mytoken=mytokenvalue
$notification1= New-AzAutoscaleNotification -CustomEmails myname@company.com -SendEmailToSubscriptionAdministrator -SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
Add-AzAutoscaleSetting -Location "USGov Virginia" -Name "MyScaleVMSSSetting" -ResourceGroup sdubeys-usgv -TargetResourceId /subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Web/serverFarms/ServerFarm1 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Pokud vás zajímá implementace automatického škálování vašich prostředků, určete nastavení pomocí volání PowerShellu/ARM/REST.

Další informace o používání PowerShellu najdete v tématu [Veřejná dokumentace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples#create-and-manage-autoscale-settings).

#### <a name="metrics"></a>Metriky
Metriky jsou všeobecně dostupné v Azure Government. Multidimenzionální metriky jsou však podporovány pouze prostřednictvím REST API. Možnost Zobrazit multidimenzionální [metriky](../azure-monitor/platform/metrics-charts.md) je ve verzi Preview na portálu Azure Government.

#### <a name="metric-alerts"></a>Upozornění metrik
První generace výstrah metrik je všeobecně dostupná jak v Azure Government, tak i v komerčních Azure. První generace se nazývá *výstrahy (Classic)* . Druhá generace upozornění na metriky (označované také jako [Unified Alerts](../azure-monitor/platform/alerts-overview.md)) je teď dostupná i s omezenou sadou poskytovatelů prostředků v [porovnání s veřejným cloudem](../azure-monitor/platform/alerts-metric-near-real-time.md). Další informace budou v průběhu času přidány. 

Prostředky, které jsou aktuálně podporované v průběhu druhé výstrahy generace, jsou:
- Microsoft.ApiManagement/service
- Microsoft.Cache/redis
- Microsoft.Compute/virtualMachines
- Microsoft.DBforMySQL/servers
- Microsoft.DBforPostgreSQL/servers
- Microsoft.DBforMariaDB/servers
- Microsoft.Devices/IotHubs
- Microsoft. EventGrid/domény
- Microsoft.EventGrid/topics
- Microsoft. EventHub/clustery
- Microsoft.EventHub/namespaces
- Microsoft. Insights/Components
- Microsoft.Network/dnsZones
- Microsoft.Network/loadBalancers
- Microsoft. Network/natGateways
- Microsoft. Network/privateEndpoints
- Microsoft. Network/privateLinkServices
- Microsoft.Network/trafficManagerProfiles
- Microsoft.OperationalInsights/workspaces
- Microsoft.PowerBIDedicated/capacities
- Microsoft. Relay/obory názvů
- Microsoft.ServiceBus/namespaces
- Microsoft.Sql/managedInstances
- Microsoft.Sql/servers/databases
- Microsoft.Sql/servers/elasticPools
- Microsoft.Storage/storageAccounts
- Microsoft.Storage/storageAccounts/blobServices
- Microsoft. Storage/storageAccounts/služby
- Microsoft.Storage/storageAccounts/queueServices
- Microsoft.Storage/storageAccounts/tableServices
- Microsoft.Web/hostingEnvironments/multiRolePools
- Microsoft.Web/hostingEnvironments/workerPools
- Microsoft.Web/serverfarms
- Microsoft.Web/sites
- Microsoft.Web/sites/slots

> [!NOTE]
> Vytváření pravidel upozornění na více prostředků v Virtual Machines **aktuálně není podporováno**. Tento článek se aktualizuje, jakmile bude tato funkce k dispozici.

Pro prostředky, které ještě nejsou k dispozici ve druhé generaci výstrah, můžete nadále používat [klasické výstrahy](../azure-monitor/platform/alerts-classic.overview.md) . 

Při použití volání PowerShellu/ARM/REST k vytvoření upozornění na metriku budete muset nastavit "umístění" výstrahy metriky na "USGov) – Virginia" nebo "USGov Iowa". Příklad nastavení je následující:

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "USGov Virginia" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Další informace o používání PowerShellu najdete v tématu [Veřejná dokumentace](../azure-monitor/platform/powershell-quickstart-samples.md).


## <a name="application-insights"></a>Application Insights

> [!NOTE]
> Monitorování agenta bez kódu nebo rozšíření na základě rozšíření pro Azure App Services se v tuto **chvíli**nepodporuje. Jakmile bude tato funkce k dispozici, bude tento článek aktualizován.

Tato část popisuje dodatečnou konfiguraci, která je nutná k použití Application Insights v Azure Government. Chcete-li získat další informace o Azure Monitor a Application Insights rezervovat [celou dokumentaci](https://docs.microsoft.com/azure/azure-monitor/overview).

### <a name="enable-application-insights-for-aspnet--aspnet-core-with-visual-studio"></a>Povolení Application Insights pro ASP.NET & ASP.NET Core se sadou Visual Studio

V současné době pro Azure Government zákazníky je jediným způsobem, jak povolit Application Insights prostřednictvím tradičního tlačítka **Přidat telemetrii Application Insights** v aplikaci Visual Studio, vyžadovat malé ruční řešení. Zákazníci, kteří mají přidružený problém, můžou zobrazit chybové zprávy, jako _je "neexistuje žádné předplatné Azure přidružené k tomuto účtu_ nebo _" vybrané předplatné nepodporuje Application Insights,_ i když má poskytovatel prostředků `microsoft.insights` stav zaregistrován pro předplatné. Chcete-li tento problém zmírnit, je nutné provést následující kroky:

1. Přepněte Visual Studio a [Zaměřte se na Azure Government Cloud](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-vs).

2. Vytvořte (nebo pokud již existující sada) proměnnou prostředí uživatele pro AzureGraphApiVersion následujícím způsobem: (Pokud chcete vytvořit proměnnou prostředí uživatele, přečtěte si **Ovládací panely** > **systémové** > **Pokročilé nastavení systému** > **Pokročilé** **proměnné prostředí** > .)

    `Variable name: AzureGraphApiVersion` `Variable value: 2014-04-01`

3. Proveďte příslušné úpravy koncového bodu sady Application Insights SDK pro [ASP.NET](https://docs.microsoft.com/azure/azure-government/documentation-government-services-monitoringandmanagement#net-with-applicationinsightsconfig) nebo [ASP.NET Core](#aspnet-core) v závislosti na typu projektu.

### <a name="snapshot-debugger"></a>Ladicí program snímků

Snapshot Debugger je teď k dispozici pro Azure Government zákazníky. Pokud chcete použít Snapshot Debugger jedinou další podmínkou pro jistotu, že používáte [Snapshot collector verze 1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5-pre-1906.403) nebo novější. Pak stačí postupovat podle standardní [dokumentace Snapshot Debugger](https://docs.microsoft.com/azure/azure-monitor/app/snapshot-debugger).

### <a name="sdk-endpoint-modifications"></a>Změny koncových bodů sady SDK

Aby bylo možné odesílat data z Application Insights do Azure Government oblasti, bude nutné upravit výchozí adresy koncových bodů, které jsou používány Application Insights SDK. Každá sada SDK vyžaduje mírně odlišnou změnu.

### <a name="net-with-applicationinsightsconfig"></a>.NET s ApplicationInsights. config

> [!NOTE]
> Soubor ApplicationInsights. config je automaticky přepsán, kdykoli je proveden upgrade sady SDK. Po provedení upgradu sady SDK nezapomeňte znovu zadat hodnoty koncového bodu specifické pro oblast.

```xml
<ApplicationInsights>
  ...
  <TelemetryModules>
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <QuickPulseServiceEndpoint>https://quickpulse.applicationinsights.us/QuickPulseService.svc</QuickPulseServiceEndpoint>
    </Add>
  </TelemetryModules>
    ...
  <TelemetryChannel>
     <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </TelemetryChannel>
  ...
  <ApplicationIdProvider Type="Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId.ApplicationInsightsApplicationIdProvider, Microsoft.ApplicationInsights">
    <ProfileQueryEndpoint>https://dc.applicationinsights.us/api/profiles/{0}/appId</ProfileQueryEndpoint>
  </ApplicationIdProvider>
  ...
</ApplicationInsights>
```

### <a name="aspnet-core"></a>Jádro ASP.NET

Upravte soubor appSettings. JSON v projektu následujícím způsobem a upravte hlavní koncový bod:

```json
"ApplicationInsights": {
    "InstrumentationKey": "instrumentationkey",
    "TelemetryChannel": {
      "EndpointAddress": "https://dc.applicationinsights.us/v2/track"
    }
  }
```

Hodnoty pro živé metriky a koncový bod dotazu profilu lze nastavit pouze prostřednictvím kódu. Chcete-li přepsat výchozí hodnoty pro všechny hodnoty koncových bodů prostřednictvím kódu, proveďte následující změny v metodě `ConfigureServices` `Startup.cs` souboru:

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Extensibility.Implementation.ApplicationId;
using Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel; //place at top of Startup.cs file

   services.ConfigureTelemetryModule<QuickPulseTelemetryModule>((module, o) => module.QuickPulseServiceEndpoint="https://quickpulse.applicationinsights.us/QuickPulseService.svc");

   services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

   services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });

    //place in ConfigureServices method. If present, place this prior to   services.AddApplicationInsightsTelemetry("instrumentation key");
```

### <a name="azure-functions"></a>Azure Functions

Do projektu funkce prosím nainstalujte následující balíčky:

- Microsoft. ApplicationInsights verze 2.10.0
- Microsoft. ApplicationInsights. PerfCounterCollector verze 2.10.0
- Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel verze 2.10.0

A také přidejte (nebo upravte) spouštěcí kód pro vaši aplikaci Function:

```csharp
[assembly: FunctionsStartup(typeof(Example.Startup))]
namespace Example
{
  class Startup : FunctionsStartup
  {
      public override void Configure(IFunctionsHostBuilder builder)
      {
          var quickPulseFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryModule) && 
                                               sd.ImplementationType == typeof(QuickPulseTelemetryModule));
          if (quickPulseFactory != null)
          {
              builder.Services.Remove(quickPulseFactory);
          }

          var appIdFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(IApplicationIdProvider));
          if (appIdFactory != null)
          {
              builder.Services.Remove(appIdFactory);
          }

          var channelFactory = builder.Services.FirstOrDefault(sd => sd.ServiceType == typeof(ITelemetryChannel));
          if (channelFactory != null)
          {
              builder.Services.Remove(channelFactory);
          }

          builder.Services.AddSingleton<ITelemetryModule, QuickPulseTelemetryModule>(_ =>
              new QuickPulseTelemetryModule
              {
                  QuickPulseServiceEndpoint = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
              });

          builder.Services.AddSingleton<IApplicationIdProvider, ApplicationInsightsApplicationIdProvider>(_ => new ApplicationInsightsApplicationIdProvider() { ProfileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId" });

          builder.Services.AddSingleton<ITelemetryChannel>(_ => new ServerTelemetryChannel() { EndpointAddress = "https://dc.applicationinsights.us/v2/track" });
      }
  }
}
```

### <a name="java"></a>Java

Upravte soubor ApplicationInsights. XML a změňte výchozí adresu koncového bodu.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
  <InstrumentationKey>ffffeeee-dddd-cccc-bbbb-aaaa99998888</InstrumentationKey>
  <TelemetryModules>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
  </TelemetryModules>
  <TelemetryInitializers>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
    <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
  </TelemetryInitializers>
  <!--Add the following Channel value to modify the Endpoint address-->
  <Channel type="com.microsoft.applicationinsights.channel.concrete.inprocess.InProcessTelemetryChannel">
  <EndpointAddress>https://dc.applicationinsights.us/v2/track</EndpointAddress>
  </Channel>
</ApplicationInsights>
```

### <a name="spring-boot"></a>Spring Boot

Upravte soubor `application.properties` a přidejte:

```yaml
azure.application-insights.channel.in-process.endpoint-address= https://dc.applicationinsights.us/v2/track
```

### <a name="nodejs"></a>Node.js

```javascript
var appInsights = require("applicationinsights");
appInsights.setup('INSTRUMENTATION_KEY');
appInsights.defaultClient.config.endpointUrl = "https://dc.applicationinsights.us/v2/track"; // ingestion
appInsights.defaultClient.config.profileQueryEndpoint = "https://dc.applicationinsights.us/api/profiles/{0}/appId"; // appid/profile lookup
appInsights.defaultClient.config.quickPulseHost = "https://quickpulse.applicationinsights.us/QuickPulseService.svc"; //live metrics
appInsights.Configuration.start();
```

Koncové body lze také konfigurovat pomocí proměnných prostředí:

```
Instrumentation Key: "APPINSIGHTS_INSTRUMENTATIONKEY"
Profile Endpoint: "https://dc.applicationinsights.us/api/profiles/{0}/appId"
Live Metrics Endpoint: "https://quickpulse.applicationinsights.us/QuickPulseService.svc"
```

### <a name="javascript"></a>JavaScript

```javascript
<script type="text/javascript">
   var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){
      function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/next/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t
   }({
      instrumentationKey:"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
      endpointUrl: "https://dc.applicationinsights.us/v2/track"
   });

   window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
</script>
```

### <a name="firewall-exceptions"></a>Výjimky brány firewall

Služba Azure Application Insights používá několik IP adres. Pokud je aplikace, kterou sledujete, hostovaná za bránou firewall, může být potřeba tyto adresy znát.

> [!NOTE]
> I když jsou tyto adresy statické, je možné, že je budete muset kdykoli změnit. Veškerý provoz Application Insights představuje odchozí provoz s výjimkou monitorování dostupnosti a webhooků, které vyžadují pravidla brány firewall pro příchozí připojení.

### <a name="outgoing-ports"></a>Odchozí porty
Musíte otevřít některé Odchozí porty v bráně firewall serveru, aby sada SDK Application Insights a/nebo Monitorování stavu mohla odesílat data na portál:

| Účel | zprostředkovatele identity | IP adresa | Porty |
| --- | --- | --- | --- |
| Telemetrická data | dc.applicationinsights.us | 23.97.4.113 | 443 |

## <a name="azure-monitor-logs"></a>Protokoly služby Azure Monitor
Protokoly Azure Monitor jsou všeobecně dostupné v Azure Government.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

### <a name="variations"></a>Odlišnost

* Mezi řešení, která jsou k dispozici v Azure Government, patří:
  * [Network Performance Monitor (npm)](https://blogs.msdn.microsoft.com/azuregov/2017/09/05/network-performance-monitor-general-availability/) – npm je cloudové řešení monitorování sítě pro veřejná a hybridní cloudová prostředí. Organizace používají NPM k monitorování dostupnosti sítě v místních i cloudových prostředích.  Monitorování koncového bodu – funkce NPM sleduje síťové připojení k aplikacím.

Následující Azure Monitor protokoly a řešení nejsou v tuto chvíli k dispozici v Azure Government.

* Řešení, která jsou ve verzi Preview v Microsoft Azure, včetně:
  * Mapa služeb
  * Řešení Upgrade Analytics pro Windows 10
  * Řešení Application Insights
  * Řešení analýzy skupin zabezpečení sítě Azure
  * Řešení Azure Automation Analytics
  * Řešení Key Vault Analytics
* Řešení a funkce, které vyžadují aktualizace místního softwaru, včetně:
  * Řešení Surface Hub
* Funkce, které jsou ve verzi Preview v globálním Azure, včetně:
  * Export dat do Power BI
* Metriky Azure a Azure Diagnostics

Adresy URL protokolů Azure Monitor se v Azure Government liší:

| Veřejné Azure | Azure Government | Poznámky: |
| --- | --- | --- |
| mms.microsoft.com |oms.microsoft.us |Portál Log Analyticsch pracovních prostorů |
| *ID pracovního prostoru*. ODS.opinsights.Azure.com |*ID pracovního prostoru*. ODS.opinsights.Azure.us |[Rozhraní API kolekce dat](../azure-monitor/platform/data-collector-api.md) |
| \*.ods.opinsights.azure.com |\*. ods.opinsights.azure.us |Komunikace s agentem – [Konfigurace nastavení brány firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.oms.opinsights.azure.com |\*. oms.opinsights.azure.us |Komunikace s agentem – [Konfigurace nastavení brány firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| \*.blob.core.windows.net |\*. blob.core.usgovcloudapi.net |Komunikace s agentem – [Konfigurace nastavení brány firewall](../log-analytics/log-analytics-proxy-firewall.md) |
| portal.loganalytics.io |portal.loganalytics.us |Portál pro pokročilou analýzu – [Konfigurace nastavení brány firewall](../azure-monitor/log-query/portals.md) |
| api.loganalytics.io |api.loganalytics.us |Portál pro pokročilou analýzu – [Konfigurace nastavení brány firewall](../azure-monitor/log-query/portals.md) |
| docs.loganalytics.io |docs.loganalytics.us |Portál pro pokročilou analýzu – [Konfigurace nastavení brány firewall](../azure-monitor/log-query/portals.md) |
| \*.azure-automation.net |\*. azure-automation.us |Azure Automation – [Konfigurace nastavení brány firewall](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |
| neuvedeno | *. usgovtrafficmanager.net | Azure Traffic Manager – [Konfigurace nastavení brány firewall](../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements) |

Následující Azure Monitor protokoly se chovají odlišně v Azure Government:

* Chcete-li připojit skupinu pro správu System Center Operations Manager k protokolům Azure Monitor, je nutné stáhnout a importovat aktualizované sady Management Pack.
  + System Center Operations Manager 2016
    1. Nainstalujte [kumulativní aktualizaci 2 pro System Center Operations Manager 2016](https://support.microsoft.com/help/3209591).
    2. Importujte sady Management Pack, které jsou součástí kumulativní aktualizace 2, do Operations Manager. Informace o tom, jak importovat Management Pack z disku, najdete v tématu [Postup importu sady management pack Operations Manager](https://technet.microsoft.com/library/hh212691.aspx).
    3. Pokud chcete připojit Operations Manager k Azure Monitor protokolů, postupujte podle pokynů v části [připojení Operations Manager k Azure monitor protokolů](../azure-monitor/platform/om-agents.md).
  + System Center Operations Manager 2012 R2 UR3 (nebo novější)/Operations Manager 2012 SP1 UR7 (nebo novější)
    1. Stáhněte a uložte [aktualizované sady Management Pack](https://go.microsoft.com/fwlink/?LinkId=828749).
    2. Rozbalte stažený soubor.
    3. Importujte sady Management Pack do Operations Manager. Informace o tom, jak importovat Management Pack z disku, najdete v tématu [Postup importu sady management pack Operations Manager](https://technet.microsoft.com/library/hh212691.aspx).
    4. Pokud chcete připojit Operations Manager k Azure Monitor protokolů, postupujte podle pokynů v části [připojení Operations Manager k Azure monitor protokolů](../azure-monitor/platform/om-agents.md).

* Další informace o používání skupin počítačů z Configuration Manager najdete v tématu [připojení Configuration Manager k Azure monitor](../azure-monitor/platform/collect-sccm.md).

### <a name="frequently-asked-questions"></a>Nejčastější dotazy
* Můžu migrovat data z Azure Monitor protokolů Microsoft Azure na Azure Government?
  * Ne. Není možné přesunout data nebo pracovní prostor z Microsoft Azure do Azure Government.
* Je možné přepínat mezi pracovními prostory Microsoft Azure a Azure Government na portálu Operations Management Suite?
  * Ne. Portály pro Microsoft Azure a Azure Government jsou oddělené a nesdílejí informace.

Další informace najdete v tématu [Azure monitor protokoluje veřejnou dokumentaci](../log-analytics/log-analytics-overview.md).

## <a name="scheduler"></a>Scheduler
Informace o této službě a o tom, jak ji používat, najdete v [dokumentaci ke službě Azure Scheduler](../scheduler/index.md).

## <a name="azure-portal"></a>Azure Portal
Portál Azure Government může být k dispozici [zde](https://portal.azure.us).

## <a name="azure-resource-manager"></a>Azure Resource Manager
Informace o této službě a o tom, jak ji používat, najdete v [dokumentaci Azure Resource Manager](../azure-resource-manager/management/overview.md).

## <a name="next-steps"></a>Další kroky
* Přihlášení k odběru [blogu Azure Government](https://blogs.msdn.microsoft.com/azuregov/)
* Získání pomoci s Stack Overflow pomocí [Azure-gov](https://stackoverflow.com/questions/tagged/azure-gov)
* Sdělte nám svůj názor nebo požádejte o nové funkce prostřednictvím [fóra Azure Government Feedback](https://feedback.azure.com/forums/558487-azure-government) .

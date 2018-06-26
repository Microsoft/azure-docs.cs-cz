---
title: Rozšíření Azure Diagnostics 1.2 schéma konfigurace
description: Platí pouze pokud používáte Azure SDK 2.5 s Azure virtuálních počítačů sady škálování virtuálního počítače, Service Fabric nebo cloudové služby.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 8c3980231404e5c8068dbd011d20759f207d7fff
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937950"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Schéma konfigurace Azure Diagnostics 1.2
> [!NOTE]
> Azure Diagnostics je komponenta, kterou používá ke shromažďování čítačů výkonu a další statistiky z virtuálních počítačů Azure, sady škálování virtuálního počítače, Service Fabric a cloudové služby.  Tato stránka je pouze relevantní, pokud používáte některou z těchto služeb.
>

Azure Diagnostics se používá s dalšími produkty Microsoftu diagnostiky jako monitorování Azure, Application Insights a analýzy protokolů.

Toto schéma definuje možné hodnoty, které můžete použít k chybě při inicializaci konfigurace diagnostiky nastavení při spuštění diagnostiky monitorování.  


 Stáhněte si definici veřejné konfigurace souboru schématu spuštěním následujícího příkazu Powershellu:  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Další informace o používání Azure Diagnostics najdete v tématu [povolení diagnostiky ve službě Azure Cloud Services](http://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Příklad konfiguračního souboru diagnostiky  
 Následující příklad ukazuje konfigurační soubor typické diagnostics:  

```xml
<?xml version="1.0" encoding="utf-8"?>  
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">  
  <WadCfg>  
    <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  
      <PerformanceCounters scheduledTransferPeriod="PT1M">  
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />  
      </PerformanceCounters>  
      <Directories scheduledTransferPeriod="PT5M">  
        <IISLogs containerName="iislogs" />  
        <FailedRequestLogs containerName="iisfailed" />  
        <DataSources>  
          <DirectoryConfiguration containerName="mynewprocess">  
            <Absolute path="C:\MyNewProcess" expandEnvironment="false" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="badapp">  
            <Absolute path="%SYSTEMDRIVE%\BadApp" expandEnvironment="true" />  
          </DirectoryConfiguration>  
          <DirectoryConfiguration containerName="goodapp">  
            <LocalResource name="Skippy" relativePath="..\PeanutButter"/>  
          </DirectoryConfiguration>  
        </DataSources>  
      </Directories>  
      <EtwProviders>  
        <EtwEventSourceProviderConfiguration provider="MyProviderClass" scheduledTransferPeriod="PT5M">  
          <Event id="0"/>  
          <Event id="1" eventDestination="errorTable"/>  
          <DefaultEvents />  
        </EtwEventSourceProviderConfiguration>  
        <EtwManifestProviderConfiguration provider="5974b00b-84c2-44bc-9e58-3a2451b4e3ad" scheduledTransferLogLevelFilter="Information" scheduledTransferPeriod="PT2M">  
          <Event id="0"/>  
          <DefaultEvents eventDestination="defaultTable"/>  
        </EtwManifestProviderConfiguration>  
      </EtwProviders>  
      <WindowsEventLog scheduledTransferPeriod="PT5M">  
        <DataSource name="System!*[System[Provider[@Name='Microsoft Antimalware']]]"/>  
        <DataSource name="System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]" />  
        <DataSource name="System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]" />  
      </WindowsEventLog>  
      <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
        <CrashDumpConfiguration processName="mynewprocess.exe" />  
        <CrashDumpConfiguration processName="badapp.exe"/>  
      </CrashDumps>  
    </DiagnosticMonitorConfiguration>  
  </WadCfg>  
</PublicConfig>  

```  

## <a name="diagnostics-configuration-namespace"></a>Namespace konfigurace diagnostiky  
 Obor názvů XML pro konfigurační soubor diagnostiky je:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>PublicConfig Element  
 Element nejvyšší úrovně v souboru konfigurace diagnostiky. Následující tabulka popisuje prvky konfiguračního souboru.  

|Název elementu|Popis|  
|------------------|-----------------|  
|**WadCfg**|Povinná hodnota. Konfigurace nastavení pro data telemetrie které se mají shromažďovat.|  
|**StorageAccount**|Název účtu úložiště Azure pro ukládání dat v. To můžete také zadat jako parametr při spuštění rutiny Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Adresář na virtuálním počítači, který bude používat k ukládání dat události Monitoring Agent. Pokud není sada, výchozí adresář slouží:<br /><br /> Pro roli pracovního procesu nebo webové: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Pro virtuální počítač: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Povinné atributy jsou:<br /><br /> -                      **cesta** -adresáři na serveru, který má být používána Azure Diagnostics.<br /><br /> -                      **expandEnvironment** -Určuje, zda jsou v názvu cesty rozbalit proměnné prostředí.|  

## <a name="wadcfg-element"></a>WadCFG Element  
Definuje nastavení konfigurace pro telemetrická data, které se mají shromažďovat. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Povinná hodnota. Volitelné atributy jsou:<br /><br /> -                     **overallQuotaInMB** -maximální množství místa na místní disk, který může být využívány službou různé typy diagnostických dat shromažďovaných Azure Diagnostics. Výchozí nastavení je 5 120 MB.<br /><br /> -                     **useProxyServer** -konfigurovat Azure Diagnostics chcete použít nastavení proxy serveru jako sada v nastavení aplikace Internet Explorer.|  
|**CrashDumps**|Povolte shromažďování výpisy stavu systému. Volitelné atributy jsou:<br /><br /> -                     **containerName** -název kontejneru objektů blob v účtu úložiště Azure, který se má použít k uložení výpisy stavu systému.<br /><br /> -                     **crashDumpType** -nakonfiguruje Azure Diagnostics ke shromažďování havárií malé nebo úplné výpisy paměti.<br /><br /> -                     **directoryQuotaPercentage**-nakonfiguruje procento **overallQuotaInMB** jako vyhrazené pro výpisů stavu systému ve virtuálním počítači.|  
|**DiagnosticInfrastructureLogs**|Povolte shromažďování protokolů generovaných Azure Diagnostics. Infrastruktura diagnostické protokoly jsou užitečné pro řešení potíží s samotného systému diagnostiky. Volitelné atributy jsou:<br /><br /> -                     **scheduledTransferLogLevelFilter** -nakonfiguruje závažnost minimální úroveň shromážděné protokoly.<br /><br /> -                     **scheduledTransferPeriod** -interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datového typu."](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Adresáře**|Povoluje shromažďování obsah adresáře, protokoly žádost o přístup služby IIS se nezdařilo nebo protokoly služby IIS. Volitelný atribut:<br /><br /> **scheduledTransferPeriod** -interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datového typu."](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Nakonfiguruje shromažďování událostí trasování událostí pro Windows z EventSource nebo Manifest trasování událostí pro Windows na základě zprostředkovatele.|  
|**Metriky**|Tento element umožňuje generovat tabulku čítače výkonu, která je optimalizovaná pro rychlé dotazy. Jednotlivých čítačů výkonu, která je definována v **čítače výkonu** element je uložené v tabulce metriky kromě tabulky čítače výkonu. Požadovaný atribut:<br /><br /> **resourceId** – to je ID prostředku nasazujete Azure Diagnostics do virtuálního počítače. Získat **resourceID** z [portál Azure](https://portal.azure.com). Vyberte **Procházet** -> **skupiny prostředků** -> **< název\>**. Klikněte **vlastnosti** dlaždici a zkopírujte hodnotu z **ID** pole.|  
|**Čítače výkonu**|Povoluje shromažďování čítačů výkonu. Volitelný atribut:<br /><br /> **scheduledTransferPeriod** -interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ".](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Umožňuje kolekce protokoly událostí systému Windows. Volitelný atribut:<br /><br /> **scheduledTransferPeriod** -interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ".](http://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>CrashDumps Element  
 Umožňuje kolekce výpisy stavu systému. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Povinná hodnota. Požadovaný atribut:<br /><br /> **název_procesu** -název procesu chcete shromažďovat výpis stavu pro Azure Diagnostics.|  
|**crashDumpType**|Nakonfiguruje Azure Diagnostics ke shromažďování výpisů paměti mini nebo úplné selhat.|  
|**directoryQuotaPercentage**|Nakonfiguruje procento **overallQuotaInMB** jako vyhrazené pro výpisů stavu systému ve virtuálním počítači.|  

## <a name="directories-element"></a>Element adresáře  
 Povoluje shromažďování obsah adresáře, protokoly žádost o přístup služby IIS se nezdařilo nebo protokoly služby IIS. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**Zdroje dat**|Seznam adresáře, které chcete monitorovat.|  
|**FailedRequestLogs**|Umožňuje shromažďování protokolů o neúspěšných požadavků na web služby IIS nebo aplikaci, včetně tento prvek v konfiguraci. Je také nutné povolit trasování možnosti v části **systému. Webový server** v **Web.config**.|  
|**IISLogs**|Tento element včetně v konfiguraci povoluje shromažďování protokoly služby IIS:<br /><br /> **containerName** -název kontejneru objektů blob v účtu úložiště Azure, který se má použít k ukládání protokolů služby IIS.|  

## <a name="datasources-element"></a>Element zdrojů dat  
 Seznam adresáře, které chcete monitorovat. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Povinná hodnota. Požadovaný atribut:<br /><br /> **containerName** -název kontejneru objektů blob v účtu úložiště Azure, který se má použít k ukládání souborů protokolu.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 **DirectoryConfiguration** může obsahovat buď **absolutní** nebo **LocalResource** elementu, ale ne obojí. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**Absolutní**|Absolutní cesta k adresáři pro monitorování. Vyžadují se následující atributy:<br /><br /> -                     **Cesta** -absolutní cestu k adresáři pro monitorování.<br /><br /> -                      **expandEnvironment** – konfiguruje, zda jsou rozbalit proměnné prostředí v cestě.|  
|**LocalResource**|Cesta relativní k místní prostředek pro monitorování. Povinné atributy jsou:<br /><br /> -                     **Název** -místní prostředek, který obsahuje adresář, který chcete monitorovat<br /><br /> -                     **relativePath** – relativní název, který obsahuje adresář, který chcete monitorovat cestu|  

## <a name="etwproviders-element"></a>EtwProviders Element  
 Nakonfiguruje shromažďování událostí trasování událostí pro Windows z EventSource nebo Manifest trasování událostí pro Windows na základě zprostředkovatele. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Nakonfiguruje kolekce událostí generovaných [EventSource – třída](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Požadovaný atribut:<br /><br /> **Zprostředkovatel** -název třídy událostí EventSource.<br /><br /> Volitelné atributy jsou:<br /><br /> -                     **scheduledTransferLogLevelFilter** -úroveň závažnosti minimální přenést do účtu úložiště.<br /><br /> -                     **scheduledTransferPeriod** -interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [datový typ XML trvání](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Požadovaný atribut:<br /><br /> **Zprostředkovatel** -GUID zprostředkovatele událostí<br /><br /> Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** -úroveň závažnosti minimální přenést do účtu úložiště.<br /><br /> -                     **scheduledTransferPeriod** -interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [datový typ XML trvání](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 Nakonfiguruje kolekce událostí generovaných [EventSource – třída](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br /><br /> **eventDestination** -název tabulku pro ukládání událostí v|  
|**Události**|Požadovaný atribut:<br /><br /> **ID** -id události.<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** -název tabulku pro ukládání událostí v|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br /><br /> **eventDestination** -název tabulku pro ukládání událostí v|  
|**Události**|Požadovaný atribut:<br /><br /> **ID** -id události.<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** -název tabulku pro ukládání událostí v|  

## <a name="metrics-element"></a>Element metriky  
 Umožňuje generovat tabulku čítače výkonu, která je optimalizovaná pro rychlé dotazy. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**MetricAggregation**|Požadovaný atribut:<br /><br /> **scheduledTransferPeriod** -interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [datový typ XML trvání](http://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>PerformanceCounters – Element  
 Povoluje shromažďování čítačů výkonu. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Vyžadují se následující atributy:<br /><br /> -                     **counterSpecifier** – název čítače výkonu. Například, `\Processor(_Total)\% Processor Time`. Chcete-li získat seznam výkonu čítače na hostiteli spusťte příkaz `typeperf`.<br /><br /> -                     **sampleRate** – jak často se čítač odeberou.<br /><br /> Volitelný atribut:<br /><br /> **jednotka** -měrnou čítače.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration Element  
 Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**Poznámky**|Požadovaný atribut:<br /><br /> **displayName** -zobrazovaný název čítače<br /><br /> Volitelný atribut:<br /><br /> **národní prostředí** -národní prostředí používané při zobrazení název čítače|  

## <a name="windowseventlog-element"></a>WindowsEventLog Element  
 Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**Zdroj dat**|Protokol událostí systému Windows ke shromažďování. Požadovaný atribut:<br /><br /> **název** – dotaz XPath popisující události systému windows, které se mají shromažďovat. Příklad:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Ke shromažďování všech událostí, zadejte "*".|

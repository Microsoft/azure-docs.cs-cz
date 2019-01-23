---
title: Rozšíření Azure Diagnostics 1.2 schéma konfigurace
description: POUZE relevantní, pokud používáte Azure SDK 2.5 s Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric nebo cloudové služby.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: 6a0061c03a10f5a5bd518c9ea01d8edd542e4e39
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54470558"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Schéma konfigurace Azure Diagnostics 1.2
> [!NOTE]
> Diagnostika Azure je součástí používanou ke shromažďování čítačů výkonu a další statistiky z Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric a Cloud Services.  Tato stránka je pouze relevantní, pokud použijete jednu z těchto služeb.
>

Diagnostika Azure se používá s dalšími produkty Microsoftu diagnostiky jako je Azure Monitor, Application Insights a Log Analytics.

Toto schéma definuje možných hodnot, které můžete použít k inicializaci konfigurace diagnostiky nastavení při spuštění monitorování diagnostiky.  


 Stáhněte definici schématu soubor veřejné konfigurace spuštěním následujícího příkazu Powershellu:  

```PowerShell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Další informace o použití diagnostiky Azure najdete v tématu [povolení diagnostiky v Azure Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Příklad konfiguračního souboru diagnostiky  
 Následující příklad ukazuje typické diagnostického konfiguračního souboru:  

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
 Je obor názvů XML pro konfigurační soubor diagnostiky:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>Elementu PublicConfig  
 Element nejvyšší úrovně v souboru konfigurace diagnostiky. Následující tabulka popisuje prvky konfigurační soubor.  

|Název elementu|Popis|  
|------------------|-----------------|  
|**WadCfg**|Povinná hodnota. Nastavení konfigurace pro telemetrická data se mají shromažďovat.|  
|**StorageAccount**|Název účtu služby Azure Storage k ukládání dat v. To může taky možné specifikovat jako parametr při spuštění rutiny Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Adresář na virtuálním počítači Agent monitorování používané k ukládání dat události. Pokud není použita sada, výchozí adresář:<br /><br /> Pro pracovní nebo webová role: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Pro virtuální počítač: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Jsou povinné atributy:<br /><br /> -                      **cesta** -directory v systému pro Azure Diagnostics.<br /><br /> -                      **expandEnvironment** – Určuje, zda jsou proměnné prostředí rozbaleny v názvu cesty.|  

## <a name="wadcfg-element"></a>WadCFG Element  
Definuje nastavení konfigurace pro telemetrická data se mají shromažďovat. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Povinná hodnota. Volitelné atributy jsou:<br /><br /> -                     **overallQuotaInMB** – maximální množství místa místního disku, které může využívat různé typy diagnostických dat shromažďovaných Azure Diagnostics. Ve výchozím nastavení je 5 120 MB.<br /><br /> -                     **useProxyServer** – konfigurace diagnostiky Azure použít nastavení proxy serveru nastavený v nastavení aplikace Internet Explorer.|  
|**Havarijního výpisu**|Povolte shromažďování výpisy při selhání. Volitelné atributy jsou:<br /><br /> -                     **containerName** – název kontejneru objektů blob v účtu Azure Storage, který se má použít k ukládání výpisy při selhání.<br /><br /> -                     **crashDumpType** – konfiguruje Azure Diagnostics shromažďovat Mini nebo úplné výpisy stavu systému.<br /><br /> -                     **directoryQuotaPercentage**– konfiguruje procento **overallQuotaInMB** které budou rezervovány pro výpisy stavu na virtuálním počítači.|  
|**DiagnosticInfrastructureLogs**|Povolte shromažďování protokolů generovaných systémem Azure Diagnostics. Protokoly infrastruktury diagnostiky jsou užitečné při řešení potíží s samotný systém diagnostiky. Volitelné atributy jsou:<br /><br /> -                     **scheduledTransferLogLevelFilter** – Konfiguruje minimální úroveň závažnosti shromažďovaných protokolů.<br /><br /> -                     **Hodnota scheduledTransferPeriod** – interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Adresáře**|Povoluje shromažďování obsah do adresáře, žádosti o zobrazení protokolů služby IIS se nezdařilo a/nebo protokoly služby IIS. Volitelný atribut:<br /><br /> **Hodnota scheduledTransferPeriod** – interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Konfigurace shromažďování událostí trasování událostí pro Windows z EventSource nebo manifestu trasování událostí pro Windows na základě poskytovatelů.|  
|**Metriky**|Tento prvek umožňuje generovat tabulku čítače výkonu, který je optimalizovaný pro rychlé zpracování dotazů. Každý čítač výkonu, který je definován v **čítače výkonu** element je uložena v tabulce metrik kromě tabulce čítače výkonu. Požadovaný atribut:<br /><br /> **resourceId** – to je ID prostředku nasazujete diagnostiky Azure pro virtuální počítač. Získejte **resourceID** z [webu Azure portal](https://portal.azure.com). Vyberte **Procházet** -> **skupiny prostředků** -> **< název\>**. Klikněte na tlačítko **vlastnosti** dlaždici a zkopírujte hodnotu z **ID** pole.|  
|**PerformanceCounters**|Povolí shromažďování čítačů výkonu. Volitelný atribut:<br /><br /> **Hodnota scheduledTransferPeriod** – interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Povolí shromažďování protokolů událostí Windows. Volitelný atribut:<br /><br /> **Hodnota scheduledTransferPeriod** – interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>Element havarijního výpisu  
 Povolí shromažďování výpisy při selhání. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Povinná hodnota. Požadovaný atribut:<br /><br /> **processName** -název procesu, kterou chcete diagnostiky Azure shromažďovat výpis stavu systému pro.|  
|**crashDumpType**|Nakonfiguruje Azure Diagnostics shromažďovat výpisy mini nebo úplné poruše.|  
|**directoryQuotaPercentage**|Nastaví procento **overallQuotaInMB** které budou rezervovány pro výpisy stavu na virtuálním počítači.|  

## <a name="directories-element"></a>Prvek adresáře  
 Povoluje shromažďování obsah do adresáře, žádosti o zobrazení protokolů služby IIS se nezdařilo a/nebo protokoly služby IIS. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DataSources**|Seznam adresářů pro monitorování.|  
|**FailedRequestLogs**|Včetně tohoto elementu v konfiguraci umožňuje shromažďování protokolů o neúspěšných požadavků na web služby IIS nebo aplikaci. Musíte také povolit trasování možností v části **systému. Webový server** v **Web.config**.|  
|**IISLogs**|Včetně tohoto elementu v konfiguraci povoluje shromažďování protokolů služby IIS:<br /><br /> **containerName** – název kontejneru objektů blob ve vašem účtu Azure Storage, který se má použít k ukládání protokolů služby IIS.|  

## <a name="datasources-element"></a>Element zdroje dat  
 Seznam adresářů pro monitorování. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Povinná hodnota. Požadovaný atribut:<br /><br /> **containerName** – název kontejneru objektů blob ve vašem účtu Azure Storage, který se má použít k ukládání souborů protokolu.|  

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration – Element  
 **DirectoryConfiguration** může obsahovat buď **absolutní** nebo **LocalResource** element, ale ne obojí. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**Absolutní**|Absolutní cesta k adresáři pro monitorování. Vyžadují se následující atributy:<br /><br /> -                     **Cesta** -absolutní cestu k adresáři pro monitorování.<br /><br /> -                      **expandEnvironment** – konfiguruje, zda jsou rozbaleny proměnné prostředí v cestě.|  
|**LocalResource**|Cesta relativní k místní prostředek pro monitorování. Jsou povinné atributy:<br /><br /> -                     **Název** – místní prostředek, který obsahuje adresář, který chcete monitorovat<br /><br /> -                     **relativePath** -cesta relativní vůči název, který obsahuje adresář, který chcete monitorovat|  

## <a name="etwproviders-element"></a>EtwProviders – Element  
 Konfigurace shromažďování událostí trasování událostí pro Windows z EventSource nebo manifestu trasování událostí pro Windows na základě poskytovatelů. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurace shromažďování událostí generovaných [EventSource – třída](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Požadovaný atribut:<br /><br /> **Zprostředkovatel** – název třídy událostí EventSource.<br /><br /> Volitelné atributy jsou:<br /><br /> -                     **scheduledTransferLogLevelFilter** – minimální úroveň závažnosti přenést do účtu úložiště.<br /><br /> -                     **Hodnota scheduledTransferPeriod** – interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [datový typ XML doba trvání](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Požadovaný atribut:<br /><br /> **Zprostředkovatel** -identifikátor GUID zprostředkovatele událostí<br /><br /> Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** – minimální úroveň závažnosti přenést do účtu úložiště.<br /><br /> -                     **Hodnota scheduledTransferPeriod** – interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [datový typ XML doba trvání](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 Konfigurace shromažďování událostí generovaných [EventSource – třída](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br /><br /> **eventDestination** -název tabulky k uložení událostí v|  
|**Události**|Požadovaný atribut:<br /><br /> **ID** – id události.<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** -název tabulky k uložení událostí v|  

## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br /><br /> **eventDestination** -název tabulky k uložení událostí v|  
|**Události**|Požadovaný atribut:<br /><br /> **ID** – id události.<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** -název tabulky k uložení událostí v|  

## <a name="metrics-element"></a>Element metriky  
 Umožňuje generovat tabulku čítače výkonu, který je optimalizovaný pro rychlé zpracování dotazů. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**MetricAggregation**|Požadovaný atribut:<br /><br /> **Hodnota scheduledTransferPeriod** – interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [datový typ XML doba trvání](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>PerformanceCounters – Element  
 Povolí shromažďování čítačů výkonu. Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Vyžadují se následující atributy:<br /><br /> -                     **counterSpecifier** – název čítače výkonu. Například, `\Processor(_Total)\% Processor Time`. Chcete-li získat seznam výkonu čítače na hostiteli spustit příkaz `typeperf`.<br /><br /> -                     **sampleRate** – jak často se čítač vzorkování.<br /><br /> Volitelný atribut:<br /><br /> **jednotka** – jednotka měření čítače.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration – Element  
 Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**Poznámky**|Požadovaný atribut:<br /><br /> **displayName** – zobrazovaný název čítače<br /><br /> Volitelný atribut:<br /><br /> **národní prostředí** -národní prostředí pro použití při zobrazení název čítače|  

## <a name="windowseventlog-element"></a>WindowsEventLog – Element  
 Následující tabulka popisuje podřízených elementů:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DataSource**|Protokoly událostí Windows a shromažďovat. Požadovaný atribut:<br /><br /> **název** – dotaz XPath popisující události systému windows, které se mají shromažďovat. Příklad:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Ke shromažďování všech událostí, zadejte "*".|


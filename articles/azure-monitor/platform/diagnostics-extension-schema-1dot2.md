---
title: Schéma konfigurace Azure Diagnostics rozšíření 1,2
description: Je relevantní jenom v případě, že používáte sadu Azure SDK 2,5 s Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric nebo Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: dae74e730d6e175fa3e447150adce4caecd3d7a3
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237841"
---
# <a name="azure-diagnostics-12-configuration-schema"></a>Schéma konfigurace Azure Diagnostics 1,2
> [!NOTE]
> Azure Diagnostics je komponenta, která se používá ke shromažďování čítačů výkonu a dalších statistik z Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric a Cloud Services.  Tato stránka je relevantní pouze v případě, že používáte některou z těchto služeb.
>

Azure Diagnostics se používá s dalšími produkty pro diagnostiku Microsoftu, jako je Azure Monitor, včetně Application Insights a Log Analytics.

Toto schéma definuje možné hodnoty, které můžete použít k inicializaci nastavení konfigurace diagnostiky při spuštění monitorování diagnostiky.  


 Stáhněte si definici schématu veřejného konfiguračního souboru spuštěním následujícího příkazu PowerShellu:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

 Další informace o používání Azure Diagnostics najdete v tématu [Povolení diagnostiky v Azure Cloud Services](https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-diagnostics/).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Příklad konfiguračního souboru diagnostiky  
 Následující příklad ukazuje typický konfigurační soubor diagnostiky:  

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

## <a name="diagnostics-configuration-namespace"></a>Obor názvů konfigurace diagnostiky  
 Obor názvů XML pro konfigurační soubor diagnostiky je:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="publicconfig-element"></a>Element PublicConfig  
 Element nejvyšší úrovně konfiguračního souboru diagnostiky. Následující tabulka popisuje prvky konfiguračního souboru.  

|Název elementu|Popis|  
|------------------|-----------------|  
|**WadCfg**|Povinný parametr. Nastavení konfigurace pro shromažďování dat telemetrie.|  
|**StorageAccount**|Název účtu Azure Storage, do kterého se mají ukládat data To může být také zadáno jako parametr při spuštění rutiny Set-AzureServiceDiagnosticsExtension.|  
|**LocalResourceDirectory**|Adresář na virtuálním počítači, který má agent monitorování použít k ukládání dat událostí. Pokud není nastaven, použije se výchozí adresář:<br /><br /> Pro pracovní proces nebo webovou roli:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Pro virtuální počítač:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Požadované atributy jsou:<br /><br /> -                      **cesta** – adresář v systému, který má Azure Diagnostics použít.<br /><br /> -                      **expandEnvironment** – určuje, jestli se v názvu cesty rozbalí proměnné prostředí.|  

## <a name="wadcfg-element"></a>Element WadCFG  
Definuje nastavení konfigurace pro shromažďování dat telemetrie. Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DiagnosticMonitorConfiguration**|Povinný parametr. Volitelné atributy jsou:<br /><br /> -                     **overallQuotaInMB** – maximální místo na místním disku, které mohou být spotřebovány různými typy diagnostických dat shromažďovaných pomocí Azure Diagnostics. Výchozí nastavení je 5120MB.<br /><br /> -                     **useProxyServer** – nakonfigurujte Azure Diagnostics, aby používala nastavení proxy server nastavená v nastavení IE.|  
|**Verzí**|Povolí shromažďování výpisů stavu systému. Volitelné atributy jsou:<br /><br /> -                     **ContainerName** – název kontejneru objektů BLOB ve vašem účtu Azure Storage, který se má použít k ukládání výpisů stavu systému.<br /><br /> -                     **crashDumpType** – konfiguruje Azure Diagnostics pro shromažďování minimálních nebo úplných výpisů stavu systému.<br /><br /> -                     **directoryQuotaPercentage**– nastaví procento **overallQuotaInMB** , které se má rezervovat pro výpisy stavu systému na virtuálním počítači.|  
|**DiagnosticInfrastructureLogs**|Povolí shromažďování protokolů generovaných Azure Diagnostics. Protokoly diagnostické infrastruktury jsou užitečné při řešení problémů samotného diagnostického systému. Volitelné atributy jsou:<br /><br /> -                     **scheduledTransferLogLevelFilter** – konfiguruje minimální úroveň závažnosti protokolů, které se shromáždily.<br /><br /> -                     **scheduledTransferPeriod** – interval mezi plánovanými transfery do úložiště se zaokrouhluje na nejbližší minutu. Hodnota je [datový typ doba trvání "XML".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**Adresáře**|Umožňuje shromažďování obsahu adresáře, protokolů požadavků na přístup služby IIS a protokolů služby IIS. Volitelný atribut:<br /><br /> **scheduledTransferPeriod** – interval mezi plánovanými transfery do úložiště se zaokrouhluje na nejbližší minutu. Hodnota je [datový typ doba trvání "XML".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**EtwProviders**|Konfiguruje shromažďování událostí ETW od zprostředkovatelů EventSource a/nebo poskytovatelů ETW založených na manifestech ETW.|  
|**Metriky**|Tento prvek umožňuje vygenerovat tabulku čítače výkonu, která je optimalizována pro rychlé dotazy. Každý čítač výkonu, který je definován v elementu **čítače výkonu** , je uložen v tabulce metrik společně s tabulkou čítače výkonu. Požadovaný atribut:<br /><br /> **ResourceID** – Toto je ID prostředku virtuálního počítače, do kterého nasazujete Azure Diagnostics. Získá **ResourceID** z [Azure Portal](https://portal.azure.com). Vyberte **Procházet** -> **skupiny** **prostředků<\>název**. ->  Klikněte na dlaždici **vlastnosti** a zkopírujte hodnotu z pole **ID** .|  
|**Čítače výkonu**|Povolí shromažďování čítačů výkonu. Volitelný atribut:<br /><br /> **scheduledTransferPeriod** – interval mezi plánovanými transfery do úložiště se zaokrouhluje na nejbližší minutu. Value je [XML "datový typ doby trvání".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  
|**WindowsEventLog**|Povolí shromažďování protokolů událostí systému Windows. Volitelný atribut:<br /><br /> **scheduledTransferPeriod** – interval mezi plánovanými transfery do úložiště se zaokrouhluje na nejbližší minutu. Value je [XML "datový typ doby trvání".](https://www.w3schools.com/xml/schema_dtypes_date.asp)|  

## <a name="crashdumps-element"></a>Element verzí  
 Povolí shromažďování výpisů stavu systému. Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**CrashDumpConfiguration**|Povinný parametr. Požadovaný atribut:<br /><br /> název **procesu** – název procesu, pro který chcete Azure Diagnostics shromažďovat výpis stavu systému.|  
|**crashDumpType**|Nakonfiguruje Azure Diagnostics ke shromažďování minimálních nebo úplných výpisů stavu systému.|  
|**directoryQuotaPercentage**|Nastaví procentuální hodnotu **overallQuotaInMB** , která se má rezervovat pro výpisy stavu systému na virtuálním počítači.|  

## <a name="directories-element"></a>Adresář – element  
 Umožňuje shromažďování obsahu adresáře, protokolů požadavků na přístup služby IIS a protokolů služby IIS. Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**Zdroje dat**|Seznam adresářů, které se mají monitorovat|  
|**FailedRequestLogs**|Zahrnutí tohoto elementu v konfiguraci umožňuje shromažďování protokolů o neúspěšných požadavcích na web nebo aplikaci služby IIS. Také je nutné povolit možnosti trasování v části **systém. Webový server** v **souboru Web. config**.|  
|**IISLogs**|Zahrnutí tohoto elementu v konfiguraci umožňuje shromažďování protokolů služby IIS:<br /><br /> **ContainerName** – název kontejneru objektů BLOB ve vašem účtu Azure Storage, který se má použít k ukládání protokolů IIS.|  

## <a name="datasources-element"></a>Element DataSources  
 Seznam adresářů, které se mají monitorovat Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DirectoryConfiguration**|Povinný parametr. Požadovaný atribut:<br /><br /> **ContainerName** – název kontejneru objektů BLOB ve vašem účtu Azure Storage, který se má použít k ukládání souborů protokolu.|  

## <a name="directoryconfiguration-element"></a>Element DirectoryConfiguration  
 **DirectoryConfiguration** může zahrnovat buď **absolutní** , nebo element **LocalResource** , ale ne obojí. Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**Absolutně**|Absolutní cesta k adresáři, který se má monitorovat Jsou vyžadovány následující atributy:<br /><br /> -                     **Cesta** – absolutní cesta k adresáři, který se má monitorovat<br /><br /> -                      **expandEnvironment** – konfiguruje, zda jsou proměnné prostředí v cestě rozbaleny.|  
|**LocalResource**|Cesta relativní k místnímu prostředku, který se má monitorovat Požadované atributy jsou:<br /><br /> -                     **Název** – místní prostředek, který obsahuje adresář, který se má monitorovat<br /><br /> -                     **RelativePath** – cesta relativní k názvu, který obsahuje adresář, který se má monitorovat|  

## <a name="etwproviders-element"></a>Element EtwProviders  
 Konfiguruje shromažďování událostí ETW od zprostředkovatelů EventSource a/nebo poskytovatelů ETW založených na manifestech ETW. Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfiguruje kolekci událostí vygenerovaných z [třídy EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Požadovaný atribut:<br /><br /> **Provider** – název třídy události EventSource.<br /><br /> Volitelné atributy jsou:<br /><br /> -                     **scheduledTransferLogLevelFilter** – minimální úroveň závažnosti, která se má přenést do svého účtu úložiště.<br /><br /> -                     **scheduledTransferPeriod** – interval mezi plánovanými transfery do úložiště se zaokrouhluje na nejbližší minutu. Hodnota je [datový typ doba trvání XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  
|**EtwManifestProviderConfiguration**|Požadovaný atribut:<br /><br /> **poskytovatel** – identifikátor GUID zprostředkovatele událostí<br /><br /> Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** – minimální úroveň závažnosti, která se má přenést do svého účtu úložiště.<br /><br /> -                     **scheduledTransferPeriod** – interval mezi plánovanými transfery do úložiště se zaokrouhluje na nejbližší minutu. Hodnota je [datový typ doba trvání XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="etweventsourceproviderconfiguration-element"></a>Element EtwEventSourceProviderConfiguration  
 Konfiguruje kolekci událostí vygenerovaných z [třídy EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br /><br /> **eventDestination** – název tabulky, do které se mají ukládat události|  
|**Události**|Požadovaný atribut:<br /><br /> **ID** – ID události<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** – název tabulky, do které se mají ukládat události|  

## <a name="etwmanifestproviderconfiguration-element"></a>Element EtwManifestProviderConfiguration  
 Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br /><br /> **eventDestination** – název tabulky, do které se mají ukládat události|  
|**Události**|Požadovaný atribut:<br /><br /> **ID** – ID události<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** – název tabulky, do které se mají ukládat události|  

## <a name="metrics-element"></a>Metrika – element  
 Umožňuje vygenerovat tabulku čítače výkonu, která je optimalizována pro rychlé dotazy. Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**MetricAggregation**|Požadovaný atribut:<br /><br /> **scheduledTransferPeriod** – interval mezi plánovanými transfery do úložiště se zaokrouhluje na nejbližší minutu. Hodnota je [datový typ doba trvání XML](https://www.w3schools.com/xml/schema_dtypes_date.asp).|  

## <a name="performancecounters-element"></a>Element čítače výkonu  
 Povolí shromažďování čítačů výkonu. Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**PerformanceCounterConfiguration**|Jsou vyžadovány následující atributy:<br /><br /> -                     **counterSpecifier** – název čítače výkonu. Například, `\Processor(_Total)\% Processor Time`. Chcete-li získat seznam čítačů výkonu na hostiteli, spusťte příkaz `typeperf`.<br /><br /> -                     **sampleRate** – jak často se má čítač vzorkovat.<br /><br /> Volitelný atribut:<br /><br /> **jednotka** – Měrná jednotka čítače.|  

## <a name="performancecounterconfiguration-element"></a>Element PerformanceCounterConfiguration  
 Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**poznámky**|Požadovaný atribut:<br /><br /> **DisplayName** – zobrazovaný název čítače<br /><br /> Volitelný atribut:<br /><br /> **locale** – národní prostředí, které se má použít při zobrazení názvu čítače|  

## <a name="windowseventlog-element"></a>Element WindowsEventLog  
 Následující tabulka popisuje podřízené prvky:  

|Název elementu|Popis|  
|------------------|-----------------|  
|**DataSource**|Protokoly událostí systému Windows, které mají být shromažďovány. Požadovaný atribut:<br /><br /> **název** – dotaz XPath popisující události systému Windows, které mají být shromažďovány. Příklad:<br /><br /> `Application!*[System[(Level >= 3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level >= 3]]`<br /><br /> Chcete-li shromáždit všechny události, zadejte "*".|


---
title: Schéma konfigurace Azure Diagnostics 1.0
description: Platí pouze pokud používáte Azure SDK 2.4 a dále s Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric nebo cloudové služby.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 7c5bfa96e7f9ef7812b8487c479b34d926633287
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2019
ms.locfileid: "54105912"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Schéma konfigurace Azure Diagnostics 1.0
> [!NOTE]
> Diagnostika Azure je součástí používanou ke shromažďování čítačů výkonu a další statistiky z Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric a Cloud Services.  Tato stránka je pouze relevantní, pokud použijete jednu z těchto služeb.
>

Diagnostika Azure se používá s dalšími produkty Microsoftu diagnostiky jako je Azure Monitor, Application Insights a Log Analytics.

Konfigurační soubor diagnostiky Azure definuje hodnoty, které se používají k inicializaci monitorování diagnostiky. Tento soubor slouží k inicializaci konfigurace diagnostiky nastavení při spuštění monitorování diagnostiky.  

 Ve výchozím nastavení, je nainstalován soubor schématu konfigurace diagnostiky Azure do `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` adresáře. Nahraďte `<version>` s nainstalovanou verzí [sady Azure SDK](https://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Konfigurační soubor diagnostiky se obvykle používá pomocí úlohy po spuštění, které vyžadují diagnostických dat, které se mají shromažďovat dříve v procesu spouštění. Další informace o použití diagnostiky Azure najdete v tématu [shromažďování dat protokolování pomocí diagnostiky Azure pomocí](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Příklad konfiguračního souboru diagnostiky  
 Následující příklad ukazuje typické diagnostického konfiguračního souboru:  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>DiagnosticsConfiguration Namespace  
 Je obor názvů XML pro konfigurační soubor diagnostiky:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Prvky schématu  
 Konfigurační soubor diagnostiky obsahuje následující prvky.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration – Element  
Element nejvyšší úrovně diagnostického konfiguračního souboru.  

Atributy:

|Atribut  |Typ   |Požaduje se| Výchozí | Popis|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|doba trvání|Nepovinné | PT1M| Určuje interval, ve kterém se monitorování diagnostiky dotazuje změny v konfiguraci diagnostiky.|  
|**overallQuotaInMB**|celé číslo bez znaménka|Nepovinné| 4000 MB. Pokud zadáte hodnotu, nesmí být delší než toto množství |Celkové množství přidělené vyrovnávací paměti všech protokolování úložiště v systému souborů.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs – Element  
Definuje konfiguraci vyrovnávací paměti pro protokoly, které jsou generovány základní diagnostické infrastruktury.

Nadřazený Element: [DiagnosticMonitorConfiguration Element](#DiagnosticMonitorConfiguration).  

Atributy:

|Atribut|Typ|Popis|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost úložiště v systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje minimální úroveň závažnosti pro položky protokolu, které byly převedeny. Výchozí hodnota je **Nedefinováno**. Další možné hodnoty jsou **Verbose**, **informace**, **upozornění**, **chyba**, a **kritický**.|  
|**Hodnota scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhluje nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="logs-element"></a>Protokoly – Element  
 Definuje konfiguraci vyrovnávací paměti pro základní protokolů Azure.

 Nadřazený element: [DiagnosticMonitorConfiguration Element](#DiagnosticMonitorConfiguration).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost úložiště v systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje minimální úroveň závažnosti pro položky protokolu, které byly převedeny. Výchozí hodnota je **Nedefinováno**. Další možné hodnoty jsou **Verbose**, **informace**, **upozornění**, **chyba**, a **kritický**.|  
|**Hodnota scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhluje nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="directories-element"></a>Prvek adresáře  
Definuje konfiguraci vyrovnávací paměti založené na souborech protokolů, které můžete definovat.

Nadřazený element: [DiagnosticMonitorConfiguration Element](#DiagnosticMonitorConfiguration).  


Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost úložiště v systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**Hodnota scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhluje nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="crashdumps-element"></a>Element havarijního výpisu  
 Definuje adresář výpisy při selhání.

 Nadřazený Element: [Element adresáře](#Directories).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Kontejner**|řetězec|Název kontejneru, ve kterém se přenášet obsah adresáře.|  
|**directoryQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs – Element  
 Definuje adresář protokolu chybných požadavků.

 Nadřazený Element [adresáře Element](#Directories).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Kontejner**|řetězec|Název kontejneru, ve kterém se přenášet obsah adresáře.|  
|**directoryQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

##  <a name="iislogs-element"></a>IISLogs – Element  
 Definuje adresář protokolu služby IIS.

 Nadřazený Element [adresáře Element](#Directories).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Kontejner**|řetězec|Název kontejneru, ve kterém se přenášet obsah adresáře.|  
|**directoryQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="datasources-element"></a>Element zdroje dat  
 Definuje nula nebo více adresářů, dalších protokolů.

 Nadřazený Element: [Element adresáře](#Directories).

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration – Element  
 Definuje adresář souborů protokolu sledování.

 Nadřazený Element: [Element zdroje dat](#DataSources).

Atributy:

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Kontejner**|řetězec|Název kontejneru, ve kterém se přenášet obsah adresáře.|  
|**directoryQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="absolute-element"></a>Absolutní – Element  
 Definuje absolutní cesta adresáře ke sledování s rozšíření prostředí volitelné.

 Nadřazený Element: [DirectoryConfiguration Element](#DirectoryConfiguration).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Cesta**|řetězec|Povinná hodnota. Absolutní cesta k adresáři pro monitorování.|  
|**expandEnvironment**|Boolean|Povinná hodnota. Pokud hodnotu **true**, proměnné prostředí v cestě jsou rozbaleny.|  

## <a name="localresource-element"></a>LocalResource – Element  
 Definuje cestu relativní vzhledem k místní prostředek definovaný v definici služby.

 Nadřazený Element: [DirectoryConfiguration Element](#DirectoryConfiguration).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Jméno**|řetězec|Povinná hodnota. Název místního prostředku, která obsahuje adresář, který chcete monitorovat.|  
|**RelativePath**|řetězec|Povinná hodnota. Cesta relativní k místní prostředek, který chcete monitorovat.|  

## <a name="performancecounters-element"></a>PerformanceCounters – Element  
 Definuje cestu k čítači výkonu pro shromažďování.

 Nadřazený Element: [DiagnosticMonitorConfiguration Element](#DiagnosticMonitorConfiguration).


 Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost úložiště v systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**Hodnota scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhluje nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration – Element  
 Definuje čítač výkonu ke shromažďování.

 Nadřazený Element: [PerformanceCounters – Element](#PerformanceCounters).  

 Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**counterSpecifier**|řetězec|Povinná hodnota. Cesta k čítači výkonu pro shromažďování.|  
|**sampleRate**|doba trvání|Povinná hodnota. Rychlost, jakou mají shromažďovat čítač výkonu.|  

## <a name="windowseventlog-element"></a>WindowsEventLog – Element  
 Definuje protokoly událostí k monitorování.

 Nadřazený Element: [DiagnosticMonitorConfiguration Element](#DiagnosticMonitorConfiguration).

  Atributy:

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost úložiště v systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje minimální úroveň závažnosti pro položky protokolu, které byly převedeny. Výchozí hodnota je **Nedefinováno**. Další možné hodnoty jsou **Verbose**, **informace**, **upozornění**, **chyba**, a **kritický**.|  
|**Hodnota scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhluje nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="datasource-element"></a>Zdroj dat – Element  
 Definuje protokolu události monitorování.

 Nadřazený Element: [WindowsEventLog Element](#windowsEventLog).  

 Atributy:

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Jméno**|řetězec|Povinná hodnota. Výraz XPath zadání protokolu ke shromažďování.|  

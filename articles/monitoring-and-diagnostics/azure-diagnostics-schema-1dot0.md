---
title: Schéma konfigurace Azure Diagnostics 1.0
description: Platí pouze pokud používáte Azure SDK 2.4 a pod s Azure virtuálních počítačů sady škálování virtuálního počítače, Service Fabric nebo cloudové služby.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 916e2123262402e23f35778e66683ecce2cec4b7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262581"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Schéma konfigurace Azure Diagnostics 1.0
> [!NOTE]
> Azure Diagnostics je komponenta, kterou používá ke shromažďování čítačů výkonu a další statistiky z virtuálních počítačů Azure, sady škálování virtuálního počítače, Service Fabric a cloudové služby.  Tato stránka je pouze relevantní, pokud používáte některou z těchto služeb.
>

Azure Diagnostics se používá s dalšími produkty Microsoftu diagnostiky jako monitorování Azure, Application Insights a analýzy protokolů.

Konfigurační soubor Azure Diagnostics definuje hodnoty, které se používají k inicializaci monitorování diagnostiky. Tento soubor se používá k chybě při inicializaci konfigurace diagnostiky nastavení při spuštění diagnostiky monitorování.  

 Ve výchozím nastavení je konfiguračního souboru schématu Azure Diagnostics nainstaluje pro `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` adresáře. Nahraďte `<version>` s nainstalovanou verzí [Azure SDK](http://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Konfigurační soubor diagnostiky se obvykle používá s spuštění úlohy, které vyžadují diagnostických dat, které se mají shromažďovat dříve v procesu spuštění. Další informace o používání Azure Diagnostics najdete v tématu [shromažďovat protokolování dat pomocí Azure Diagnostics](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Příklad konfiguračního souboru diagnostiky  
 Následující příklad ukazuje konfigurační soubor typické diagnostics:  

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

## <a name="diagnosticsconfiguration-namespace"></a>Namespace DiagnosticsConfiguration  
 Obor názvů XML pro konfigurační soubor diagnostiky je:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Prvky schématu  
 Diagnostika konfigurační soubor obsahuje následující prvky.


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element  
Element nejvyšší úrovně v souboru konfigurace diagnostiky.  

Atributy:

|Atribut  |Typ   |Požaduje se| Výchozí | Popis|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|doba trvání|Nepovinné | PT1M| Určuje interval, ve kterém se monitorování diagnostiky dotazuje změny konfigurace diagnostiky.|  
|**overallQuotaInMB**|celé číslo bez znaménka|Nepovinné| 4000 MB. Pokud zadáte hodnotu, nesmí být delší než toto množství |Celkové množství přidělené všechny vyrovnávací paměti protokolování úložiště systému souborů.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs Element  
Definuje konfiguraci vyrovnávací paměti pro protokoly, které jsou generovány základní infrastruktura diagnostiky.

Nadřazený Element: [DiagnosticMonitorConfiguration Element](#DiagnosticMonitorConfiguration).  

Atributy:

|Atribut|Typ|Popis|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální množství úložiště systému souboru, který je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje úroveň závažnosti minimální pro položky protokolu, které se přenáší. Výchozí hodnota je **Undefined**. Další možné hodnoty jsou **podrobné**, **informace**, **upozornění**, **chyba**, a **kritický**.|  
|**scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhlený nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="logs-element"></a>Element protokoly  
 Definuje konfiguraci vyrovnávací paměti pro základní Azure protokoly.

 Nadřazený element: [DiagnosticMonitorConfiguration Element](#DiagnosticMonitorConfiguration).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální množství úložiště systému souboru, který je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje úroveň závažnosti minimální pro položky protokolu, které se přenáší. Výchozí hodnota je **Undefined**. Další možné hodnoty jsou **podrobné**, **informace**, **upozornění**, **chyba**, a **kritický**.|  
|**scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhlený nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="directories-element"></a>Element adresáře  
Definuje konfiguraci vyrovnávací paměti na základě souborů protokolů, které můžete definovat.

Nadřazený element: [DiagnosticMonitorConfiguration Element](#DiagnosticMonitorConfiguration).  


Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální množství úložiště systému souboru, který je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhlený nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="crashdumps-element"></a>CrashDumps Element  
 Definuje adresář výpisy havárií.

 Nadřazený Element: [adresáře Element](#Directories).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**kontejner**|řetězec|Název kontejneru, kde je obsah adresáře, které se mají přenést.|  
|**directoryQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs Element  
 Definuje adresář protokolu chybných požadavků.

 Nadřazený Element [adresáře Element](#Directories).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**kontejner**|řetězec|Název kontejneru, kde je obsah adresáře, které se mají přenést.|  
|**directoryQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

##  <a name="iislogs-element"></a>IISLogs Element  
 Definuje adresář protokolu služby IIS.

 Nadřazený Element [adresáře Element](#Directories).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**kontejner**|řetězec|Název kontejneru, kde je obsah adresáře, které se mají přenést.|  
|**directoryQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="datasources-element"></a>Element zdrojů dat  
 Definuje nula nebo více dodatečných protokolů adresáře.

 Nadřazený Element: [adresáře Element](#Directories).

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 Definuje adresář souborů protokolu ke sledování.

 Nadřazený Element: [zdrojů dat Element](#DataSources).

Atributy:

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**kontejner**|řetězec|Název kontejneru, kde je obsah adresáře, které se mají přenést.|  
|**directoryQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="absolute-element"></a>Absolutní – Element  
 Definuje absolutní cesta adresář, který chcete monitorovat pomocí prostředí volitelné rozšíření.

 Nadřazený Element: [DirectoryConfiguration Element](#DirectoryConfiguration).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Cesta**|řetězec|Povinná hodnota. Absolutní cesta k adresáři pro monitorování.|  
|**expandEnvironment**|Boolean|Povinná hodnota. Pokud nastavena na **true**, jsou-li rozbalit proměnné prostředí v cestě.|  

## <a name="localresource-element"></a>LocalResource Element  
 Definuje cestu k místních prostředků v definici služby.

 Nadřazený Element: [DirectoryConfiguration Element](#DirectoryConfiguration).  

Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Jméno**|řetězec|Povinná hodnota. Název místní prostředek, který obsahuje adresář, který chcete monitorovat.|  
|**RelativePath**|řetězec|Povinná hodnota. Cesta relativní k místní prostředek pro monitorování.|  

## <a name="performancecounters-element"></a>PerformanceCounters – Element  
 Definuje cestu k čítači výkonu ke shromažďování.

 Nadřazený Element: [DiagnosticMonitorConfiguration Element](#DiagnosticMonitorConfiguration).


 Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální množství úložiště systému souboru, který je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhlený nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration Element  
 Definuje čítač výkonu ke shromažďování.

 Nadřazený Element: [PerformanceCounters – Element](#PerformanceCounters).  

 Atributy:  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**counterSpecifier**|řetězec|Povinná hodnota. Cesta ke shromažďování čítače výkonu.|  
|**sampleRate**|doba trvání|Povinná hodnota. Rychlost, jakou by měly být shromažďovány čítače výkonu.|  

## <a name="windowseventlog-element"></a>WindowsEventLog Element  
 Definuje protokoly událostí k monitorování.

 Nadřazený Element: [DiagnosticMonitorConfiguration Element](#DiagnosticMonitorConfiguration).

  Atributy:

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|celé číslo bez znaménka|Volitelné. Určuje maximální množství úložiště systému souboru, který je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje úroveň závažnosti minimální pro položky protokolu, které se přenáší. Výchozí hodnota je **Undefined**. Další možné hodnoty jsou **podrobné**, **informace**, **upozornění**, **chyba**, a **kritický**.|  
|**scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhlený nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="datasource-element"></a>Element zdroje dat  
 Definuje protokol událostí a monitorování.

 Nadřazený Element: [WindowsEventLog Element](#windowsEventLog).  

 Atributy:

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Jméno**|řetězec|Povinná hodnota. Výraz XPath určující protokolu, který chcete shromáždit.|  

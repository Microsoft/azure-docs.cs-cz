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
ms.subservice: diagnostic-extension
ms.openlocfilehash: 36b9e6c97a10f7608a4faaef005ca4eeb1fc09c6
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811524"
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

|Atribut  |Type   |Požaduje se| Výchozí | Popis|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|doba trvání|Nepovinné | PT1M| Určuje interval, ve kterém se monitorování diagnostiky dotazuje změny v konfiguraci diagnostiky.|  
|**overallQuotaInMB**|unsignedInt|Nepovinné| 4000 MB. Pokud zadáte hodnotu, nesmí být delší než toto množství |Celkové množství přidělené vyrovnávací paměti všech protokolování úložiště v systému souborů.|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs – Element  
Definuje konfiguraci vyrovnávací paměti pro protokoly, které jsou generovány základní diagnostické infrastruktury.

Nadřazený Element: DiagnosticMonitorConfiguration Element.  

Atributy:

|Atribut|Type|Popis|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost úložiště v systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje minimální úroveň závažnosti pro položky protokolu, které byly převedeny. Výchozí hodnota je **Nedefinováno**. Další možné hodnoty jsou **Verbose**, **informace**, **upozornění**, **chyba**, a **kritický**.|  
|**scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhluje nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="logs-element"></a>Protokoly – Element  
 Definuje konfiguraci vyrovnávací paměti pro základní protokolů Azure.

 Nadřazený element: DiagnosticMonitorConfiguration Element.  

Atributy:  

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost úložiště v systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje minimální úroveň závažnosti pro položky protokolu, které byly převedeny. Výchozí hodnota je **Nedefinováno**. Další možné hodnoty jsou **Verbose**, **informace**, **upozornění**, **chyba**, a **kritický**.|  
|**scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhluje nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="directories-element"></a>Prvek adresáře  
Definuje konfiguraci vyrovnávací paměti založené na souborech protokolů, které můžete definovat.

Nadřazený element: DiagnosticMonitorConfiguration Element.  


Atributy:  

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost úložiště v systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhluje nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="crashdumps-element"></a>Element havarijního výpisu  
 Definuje adresář výpisy při selhání.

 Nadřazený Element: Element adresáře.  

Atributy:  

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**container**|řetězec|Název kontejneru, ve kterém se přenášet obsah adresáře.|  
|**directoryQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs – Element  
 Definuje adresář protokolu chybných požadavků.

 Nadřazený prvek adresáře prvku.  

Atributy:  

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**container**|řetězec|Název kontejneru, ve kterém se přenášet obsah adresáře.|  
|**directoryQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

##  <a name="iislogs-element"></a>IISLogs – Element  
 Definuje adresář protokolu služby IIS.

 Nadřazený prvek adresáře prvku.  

Atributy:  

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**container**|řetězec|Název kontejneru, ve kterém se přenášet obsah adresáře.|  
|**directoryQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="datasources-element"></a>Element zdroje dat  
 Definuje nula nebo více adresářů, dalších protokolů.

 Nadřazený Element: Element adresáře.

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration – Element  
 Definuje adresář souborů protokolu sledování.

 Nadřazený Element: Element datových zdrojů.

Atributy:

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**container**|řetězec|Název kontejneru, ve kterém se přenášet obsah adresáře.|  
|**directoryQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="absolute-element"></a>Absolutní – Element  
 Definuje absolutní cesta adresáře ke sledování s rozšíření prostředí volitelné.

 Nadřazený Element: DirectoryConfiguration Element.  

Atributy:  

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**Cesta**|řetězec|Povinná hodnota. Absolutní cesta k adresáři pro monitorování.|  
|**expandEnvironment**|Boolean|Povinná hodnota. Pokud hodnotu **true**, proměnné prostředí v cestě jsou rozbaleny.|  

## <a name="localresource-element"></a>LocalResource – Element  
 Definuje cestu relativní vzhledem k místní prostředek definovaný v definici služby.

 Nadřazený Element: DirectoryConfiguration Element.  

Atributy:  

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**Jméno**|řetězec|Povinná hodnota. Název místního prostředku, která obsahuje adresář, který chcete monitorovat.|  
|**relativePath**|řetězec|Povinná hodnota. Cesta relativní k místní prostředek, který chcete monitorovat.|  

## <a name="performancecounters-element"></a>PerformanceCounters – Element  
 Definuje cestu k čítači výkonu pro shromažďování.

 Nadřazený Element: DiagnosticMonitorConfiguration Element.


 Atributy:  

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost úložiště v systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhluje nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration – Element  
 Definuje čítač výkonu ke shromažďování.

 Nadřazený Element: PerformanceCounters – Element.  

 Atributy:  

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**counterSpecifier**|řetězec|Povinná hodnota. Cesta k čítači výkonu pro shromažďování.|  
|**sampleRate**|doba trvání|Povinná hodnota. Rychlost, jakou mají shromažďovat čítač výkonu.|  

## <a name="windowseventlog-element"></a>WindowsEventLog – Element  
 Definuje protokoly událostí k monitorování.

 Nadřazený Element: DiagnosticMonitorConfiguration Element.

  Atributy:

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost úložiště v systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje minimální úroveň závažnosti pro položky protokolu, které byly převedeny. Výchozí hodnota je **Nedefinováno**. Další možné hodnoty jsou **Verbose**, **informace**, **upozornění**, **chyba**, a **kritický**.|  
|**scheduledTransferPeriod**|doba trvání|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhluje nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="datasource-element"></a>Zdroj dat – Element  
 Definuje protokolu události monitorování.

 Nadřazený Element: WindowsEventLog Element.  

 Atributy:

|Atribut|Type|Popis|  
|---------------|----------|-----------------|  
|**Jméno**|řetězec|Povinná hodnota. Výraz XPath zadání protokolu ke shromažďování.|  


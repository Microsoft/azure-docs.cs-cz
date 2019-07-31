---
title: Schéma konfigurace Azure Diagnostics 1,0
description: Je relevantní jenom v případě, že používáte Azure SDK 2,4 a nižší s Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric nebo Cloud Services.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.subservice: diagnostic-extension
ms.openlocfilehash: ac2b79d670b803573a359dfc9f8738f972f2d9b5
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "60237859"
---
# <a name="azure-diagnostics-10-configuration-schema"></a>Schéma konfigurace Azure Diagnostics 1,0
> [!NOTE]
> Azure Diagnostics je komponenta, která se používá ke shromažďování čítačů výkonu a dalších statistik z Azure Virtual Machines, Virtual Machine Scale Sets, Service Fabric a Cloud Services.  Tato stránka je relevantní pouze v případě, že používáte některou z těchto služeb.
>

Azure Diagnostics se používá s dalšími produkty pro diagnostiku Microsoftu, jako je Azure Monitor, včetně Application Insights a Log Analytics.

Konfigurační soubor Azure Diagnostics definuje hodnoty, které se používají k inicializaci monitorování diagnostiky. Tento soubor slouží k inicializaci nastavení konfigurace diagnostiky při spuštění monitorování diagnostiky.  

 Ve výchozím nastavení se soubor konfiguračního schématu Azure Diagnostics nainstaluje do `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` adresáře. Nahraďte `<version>` nainstalovanou verzí sady [Azure SDK](https://www.windowsazure.com/develop/downloads/).  

> [!NOTE]
>  Konfigurační soubor diagnostiky se obvykle používá u úloh po spuštění, které vyžadují shromažďování diagnostických dat dříve v procesu spuštění. Další informace o použití Azure Diagnostics najdete v tématu [shromáždění dat protokolování pomocí Azure Diagnostics](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Příklad konfiguračního souboru diagnostiky  
 Následující příklad ukazuje typický konfigurační soubor diagnostiky:  

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

## <a name="diagnosticsconfiguration-namespace"></a>Obor názvů DiagnosticsConfiguration  
 Obor názvů XML pro konfigurační soubor diagnostiky je:  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>Prvky schématu  
 Konfigurační soubor diagnostiky obsahuje následující prvky.


## <a name="diagnosticmonitorconfiguration-element"></a>Element DiagnosticMonitorConfiguration  
Element nejvyšší úrovně konfiguračního souboru diagnostiky.  

Atributy:

|Atribut  |type   |Požadováno| Výchozí | Popis|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|volitelná, | PT1M| Určuje interval, ve kterém se monitorování diagnostiky dotazuje na změny konfigurace diagnostiky.|  
|**overallQuotaInMB**|unsignedInt|volitelná,| 4000 MB. Pokud zadáte hodnotu, nesmí překročit tuto částku. |Celková velikost úložiště systému souborů přiděleného pro všechny vyrovnávací paměti protokolování.|  

## <a name="diagnosticinfrastructurelogs-element"></a>Element DiagnosticInfrastructureLogs  
Definuje konfiguraci vyrovnávací paměti pro protokoly, které jsou generovány pomocí základní diagnostické infrastruktury.

Nadřazený element: Element DiagnosticMonitorConfiguration  

Atributy:

|Atribut|type|Popis|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost úložiště systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje minimální úroveň závažnosti pro přenesené položky protokolu. Výchozí hodnota není **definována**. Další možné hodnoty jsou **verbose**, **informace**, **varování**, **Chyba**a kritická.|  
|**scheduledTransferPeriod**|duration|Volitelné. Určuje interval mezi plánovanými přenosy dat, zaokrouhlený na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="logs-element"></a>Logs – element  
 Definuje konfiguraci vyrovnávací paměti pro základní protokoly Azure.

 Nadřazený element: Element DiagnosticMonitorConfiguration  

Atributy:  

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost úložiště systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje minimální úroveň závažnosti pro přenesené položky protokolu. Výchozí hodnota není **definována**. Další možné hodnoty jsou **verbose**, **informace**, **varování**, **Chyba**a kritická.|  
|**scheduledTransferPeriod**|duration|Volitelné. Určuje interval mezi plánovanými přenosy dat, zaokrouhlený na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="directories-element"></a>Adresář – element  
Definuje konfiguraci vyrovnávací paměti pro souborové protokoly, které můžete definovat.

Nadřazený element: Element DiagnosticMonitorConfiguration  


Atributy:  

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost úložiště systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferPeriod**|duration|Volitelné. Určuje interval mezi plánovanými přenosy dat, zaokrouhlený na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="crashdumps-element"></a>Element verzí  
 Definuje adresář výpisů stavu systému.

 Nadřazený element: Element adresáře.  

Atributy:  

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**container**|řetězec|Název kontejneru, do kterého se má přenést obsah adresáře|  
|**directoryQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="failedrequestlogs-element"></a>Element FailedRequestLogs  
 Definuje adresář protokolu chybných žádostí.

 Element adresářů nadřazeného elementu.  

Atributy:  

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**container**|řetězec|Název kontejneru, do kterého se má přenést obsah adresáře|  
|**directoryQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

##  <a name="iislogs-element"></a>Element IISLogs  
 Definuje adresář protokolu služby IIS.

 Element adresářů nadřazeného elementu.  

Atributy:  

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**container**|řetězec|Název kontejneru, do kterého se má přenést obsah adresáře|  
|**directoryQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="datasources-element"></a>Element DataSources  
 Definuje nula nebo více dalších adresářů protokolů.

 Nadřazený element: Element adresáře.

## <a name="directoryconfiguration-element"></a>Element DirectoryConfiguration  
 Definuje adresář souborů protokolu, které se mají monitorovat.

 Nadřazený element: Element DataSources.

Atributy:

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**container**|řetězec|Název kontejneru, do kterého se má přenést obsah adresáře|  
|**directoryQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost adresáře v megabajtech.<br /><br /> Výchozí hodnota je 0.|  

## <a name="absolute-element"></a>Absolutní element  
 Definuje absolutní cestu k adresáři, který se má monitorovat, s volitelným rozšířením prostředí.

 Nadřazený element: Element DirectoryConfiguration  

Atributy:  

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**Cesta**|řetězec|Povinný parametr. Absolutní cesta k adresáři, který se má monitorovat|  
|**expandEnvironment**|boolean|Povinný parametr. Pokud je nastaveno na **hodnotu true**, jsou rozbaleny proměnné prostředí v cestě.|  

## <a name="localresource-element"></a>Element LocalResource  
 Definuje relativní cestu k místnímu prostředku definovanému v definici služby.

 Nadřazený element: Element DirectoryConfiguration  

Atributy:  

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**name**|řetězec|Povinný parametr. Název místního prostředku, který obsahuje adresář, který se má monitorovat|  
|**relativePath**|řetězec|Povinný parametr. Cesta relativní k místnímu prostředku, který se má monitorovat|  

## <a name="performancecounters-element"></a>Element čítače výkonu  
 Definuje cestu k čítači výkonu, který se má shromáždit.

 Nadřazený element: Element DiagnosticMonitorConfiguration


 Atributy:  

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost úložiště systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferPeriod**|duration|Volitelné. Určuje interval mezi plánovanými přenosy dat, zaokrouhlený na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="performancecounterconfiguration-element"></a>Element PerformanceCounterConfiguration  
 Definuje čítač výkonu, který se má shromáždit.

 Nadřazený element: Element čítače výkonu  

 Atributy:  

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**counterSpecifier**|řetězec|Povinný parametr. Cesta k čítači výkonu, který se má shromáždit.|  
|**sampleRate**|duration|Povinný parametr. Rychlost, s jakou se má počítadlo výkonu shromažďovat|  

## <a name="windowseventlog-element"></a>Element WindowsEventLog  
 Definuje protokoly událostí, které se mají monitorovat.

 Nadřazený element: Element DiagnosticMonitorConfiguration

  Atributy:

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|Volitelné. Určuje maximální velikost úložiště systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|řetězec|Volitelné. Určuje minimální úroveň závažnosti pro přenesené položky protokolu. Výchozí hodnota není **definována**. Další možné hodnoty jsou **verbose**, **informace**, **varování**, **Chyba**a kritická.|  
|**scheduledTransferPeriod**|duration|Volitelné. Určuje interval mezi plánovanými přenosy dat, zaokrouhlený na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  

## <a name="datasource-element"></a>Element DataSource  
 Definuje protokol událostí, který se má monitorovat.

 Nadřazený element: Element WindowsEventLog  

 Atributy:

|Atribut|type|Popis|  
|---------------|----------|-----------------|  
|**name**|řetězec|Povinný parametr. Výraz XPath určující protokol, který se má shromáždit.|  


---
title: Schéma rozšíření diagnostiky Windows
description: Referenční informace ke schématu konfigurace pro rozšíření diagnostiky systému Windows (WAD) v Azure Monitor.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: d2b1afea746410e966b43bef01a039a8471d4ae7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87007924"
---
# <a name="windows-diagnostics-extension-schema"></a>Schéma rozšíření diagnostiky Windows
Azure Diagnostics rozšíření je agent v Azure Monitor, který shromažďuje data monitorování z hostovaného operačního systému a zatížení výpočetních prostředků Azure. Tento článek podrobně popisuje schéma používané pro konfiguraci diagnostického rozšíření na virtuálních počítačích s Windows a dalších výpočetních prostředcích.

> [!NOTE]
> Schéma v tomto článku je platné pro verze 1,3 a novější (Azure SDK 2,4 a novější). Novější konfigurační oddíly jsou komentáře, aby se zobrazily informace o tom, jaká verze byla přidána. Archivovaná verze 1,0 a 1,2 schématu byla archivována a již není k dispozici. 

## <a name="public-configuration-file-schema"></a>Schéma souboru veřejného konfigurace

Stáhněte si definici schématu veřejného konfiguračního souboru spuštěním následujícího příkazu PowerShellu:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Společné typy atributů  
 atribut **scheduledTransferPeriod** se zobrazí v několika prvcích. Je to interval mezi plánovanými přenosy do úložiště zaokrouhlený na nejbližší minutu. Hodnota je [datový typ doba trvání "XML".](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>Element DiagnosticsConfiguration  
 *Strom: root-DiagnosticsConfiguration*

Přidáno ve verzi 1,3.  

Element nejvyšší úrovně konfiguračního souboru diagnostiky.  

**Atribut**  xmlns-obor názvů XML pro konfigurační soubor diagnostiky je:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**PublicConfig**|Povinná hodnota. Viz popis jinde na této stránce.|  
|**PrivateConfig**|Nepovinný parametr. Viz popis jinde na této stránce.|  
|**IsEnabled**|Datového. Viz popis jinde na této stránce.|  

## <a name="publicconfig-element"></a>Element PublicConfig  
 *Strom: root-DiagnosticsConfiguration-PublicConfig*

 Popisuje konfiguraci veřejné diagnostiky.  

|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**WadCfg**|Povinná hodnota. Viz popis jinde na této stránce.|  
|**StorageAccount**|Název účtu Azure Storage, do kterého se mají ukládat data Může být také zadáno jako parametr při spuštění rutiny Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Může být *Table*, *BLOB*nebo *TableAndBlob*. Tabulka je výchozí. Je-li zvolena možnost TableAndBlob, jsou diagnostická data do každého typu zapisována dvakrát.|  
|**LocalResourceDirectory**|Adresář na virtuálním počítači, kde agent monitorování ukládá data událostí. Pokud ne, nastavte, že se použije výchozí adresář:<br /><br /> Pro pracovní proces nebo webovou roli: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Pro virtuální počítač: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Požadované atributy jsou:<br /><br /> - **cesta** – adresář v systému, který má Azure Diagnostics použít.<br /><br /> - **expandEnvironment** – určuje, jestli se v názvu cesty rozbalí proměnné prostředí.|  

## <a name="wadcfg-element"></a>Element WadCFG  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG*

 Identifikuje a konfiguruje data telemetrie, která se mají shromáždit.  


## <a name="diagnosticmonitorconfiguration-element"></a>Element DiagnosticMonitorConfiguration
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration*

 Vyžadováno

|Atributy|Description|  
|----------------|-----------------|  
| **overallQuotaInMB** | Maximální místo na místním disku, které mohou být spotřebovány různými typy diagnostických dat shromažďovaných Azure Diagnostics. Výchozí nastavení je 4096 MB.<br />
|**useProxyServer** | Nakonfigurujte Azure Diagnostics, aby používala nastavení proxy server nastavená v nastavení IE.|
|**jímky** | Přidáno v 1,5. Nepovinný parametr. Odkazuje na umístění jímky, aby také odesílal diagnostická data pro všechny podřízené prvky, které podporují jímky. Příklad jímky je Application Insights nebo Event Hubs. Všimněte si, že je nutné přidat vlastnost *ResourceID* pod element *metriky* , pokud chcete, aby události odeslané do Event Hubs měly ID prostředku. |  


<br /> <br />

|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**Verzí**|Viz popis jinde na této stránce.|  
|**DiagnosticInfrastructureLogs**|Povolí shromažďování protokolů generovaných Azure Diagnostics. Protokoly diagnostické infrastruktury jsou užitečné při řešení problémů samotného diagnostického systému. Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** – konfiguruje minimální úroveň závažnosti protokolů, které se shromáždily.<br /><br /> - **scheduledTransferPeriod** – interval mezi plánovanými transfery do úložiště se zaokrouhluje na nejbližší minutu. Hodnota je [datový typ doba trvání "XML".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Adresáře**|Viz popis jinde na této stránce.|  
|**EtwProviders**|Viz popis jinde na této stránce.|  
|**Metriky**|Viz popis jinde na této stránce.|  
|**Čítače výkonu**|Viz popis jinde na této stránce.|  
|**WindowsEventLog**|Viz popis jinde na této stránce.|
|**DockerSources**|Viz popis jinde na této stránce. |



## <a name="crashdumps-element"></a>Element verzí  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-verzí*

 Povolte shromažďování výpisů stavu systému.  

|Atributy|Description|  
|----------------|-----------------|  
|**containerName**|Nepovinný parametr. Název kontejneru objektů BLOB v účtu Azure Storage, který se má použít k ukládání výpisů stavu systému.|  
|**crashDumpType**|Nepovinný parametr.  Nakonfiguruje Azure Diagnostics ke shromažďování minimálních nebo úplných výpisů stavu systému.|  
|**directoryQuotaPercentage**|Nepovinný parametr.  Nastaví procentuální hodnotu **overallQuotaInMB** , která se má rezervovat pro výpisy stavu systému na virtuálním počítači.|  

|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Povinná hodnota. Definuje hodnoty konfigurace pro každý proces.<br /><br /> Vyžaduje se taky následující atribut:<br /><br /> název **procesu** – název procesu, pro který chcete Azure Diagnostics shromažďovat výpis stavu systému.|  

## <a name="directories-element"></a>Adresář – element
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-adresáře*

 Umožňuje shromažďování obsahu adresáře, protokolů požadavků na přístup služby IIS a protokolů služby IIS.  

 Volitelný atribut **scheduledTransferPeriod** Viz vysvětlení výše.  

|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**IISLogs**|Zahrnutí tohoto elementu v konfiguraci umožňuje shromažďování protokolů služby IIS:<br /><br /> **ContainerName** – název kontejneru objektů BLOB ve vašem účtu Azure Storage, který se má použít k ukládání protokolů IIS.|   
|**FailedRequestLogs**|Zahrnutí tohoto elementu v konfiguraci umožňuje shromažďování protokolů o neúspěšných požadavcích na web nebo aplikaci služby IIS. Také je nutné povolit možnosti trasování v části **systém. WebServer** v **Web.config**.|  
|**Zdroje dat**|Seznam adresářů, které se mají monitorovat|




## <a name="datasources-element"></a>Element DataSources  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-directorys-zdroje dat*

 Seznam adresářů, které se mají monitorovat  

|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Povinná hodnota. Požadovaný atribut:<br /><br /> **ContainerName** – název kontejneru objektů BLOB ve vašem účtu Azure Storage, který se má použít k ukládání souborů protokolu.|  





## <a name="directoryconfiguration-element"></a>Element DirectoryConfiguration  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Directory-DataSources-DirectoryConfiguration*

 Může zahrnovat buď **absolutní** , nebo element **LocalResource** , ale ne obojí.  

|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**Absolutní**|Absolutní cesta k adresáři, který se má monitorovat Jsou vyžadovány následující atributy:<br /><br /> - **Cesta** – absolutní cesta k adresáři, který se má monitorovat<br /><br /> - **expandEnvironment** – konfiguruje, zda jsou proměnné prostředí v cestě rozbaleny.|  
|**LocalResource**|Cesta relativní k místnímu prostředku, který se má monitorovat Požadované atributy jsou:<br /><br /> - **Název** – místní prostředek, který obsahuje adresář, který se má monitorovat<br /><br /> - **RelativePath** – cesta relativní k názvu, který obsahuje adresář, který se má monitorovat|  



## <a name="etwproviders-element"></a>Element EtwProviders  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders*

 Konfiguruje shromažďování událostí ETW od zprostředkovatelů EventSource a/nebo poskytovatelů ETW založených na manifestech ETW.  

|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfiguruje kolekci událostí vygenerovaných z [třídy EventSource](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1). Požadovaný atribut:<br /><br /> **Provider** – název třídy události EventSource.<br /><br /> Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** – minimální úroveň závažnosti, která se má přenést do svého účtu úložiště.<br /><br /> - **scheduledTransferPeriod** – interval mezi plánovanými transfery do úložiště se zaokrouhluje na nejbližší minutu. Hodnota je [datový typ doba trvání "XML".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Požadovaný atribut:<br /><br /> **poskytovatel** – identifikátor GUID zprostředkovatele událostí<br /><br /> Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** – minimální úroveň závažnosti, která se má přenést do svého účtu úložiště.<br /><br /> - **scheduledTransferPeriod** – interval mezi plánovanými transfery do úložiště se zaokrouhluje na nejbližší minutu. Hodnota je [datový typ doba trvání "XML".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>Element EtwEventSourceProviderConfiguration  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwEventSourceProviderConfiguration*

 Konfiguruje kolekci událostí vygenerovaných z [třídy EventSource](/dotnet/api/system.diagnostics.tracing.eventsource?view=netcore-3.1).  

|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br/><br/> **eventDestination** – název tabulky, do které se mají ukládat události|  
|**Událost**|Požadovaný atribut:<br /><br /> **ID** – ID události<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** – název tabulky, do které se mají ukládat události|  



## <a name="etwmanifestproviderconfiguration-element"></a>Element EtwManifestProviderConfiguration  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-EtwProviders-EtwManifestProviderConfiguration*

|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br /><br /> **eventDestination** – název tabulky, do které se mají ukládat události|  
|**Událost**|Požadovaný atribut:<br /><br /> **ID** – ID události<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** – název tabulky, do které se mají ukládat události|  



## <a name="metrics-element"></a>Metrika – element  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-Metrics*

 Umožňuje vygenerovat tabulku čítače výkonu, která je optimalizována pro rychlé dotazy. Každý čítač výkonu, který je definován v elementu **čítače výkonu** , je uložen v tabulce metrik společně s tabulkou čítače výkonu.  

 Atribut **ResourceID** je povinný.  ID prostředku virtuálního počítače nebo sady škálování virtuálních počítačů, do které nasazujete Azure Diagnostics. Získá **ResourceID** z [Azure Portal](https://portal.azure.com). Vyberte **Procházet**  ->  **skupiny prostředků**  ->  **<název \> **. Klikněte na dlaždici **vlastnosti** a zkopírujte hodnotu z pole **ID** .  Tato vlastnost resourceID se používá pro odesílání vlastních metrik a pro přidání vlastnosti resourceID do dat odesílaných do Event Hubs. Všimněte si, že je nutné přidat vlastnost *ResourceID* pod element *metriky* , pokud chcete, aby události odeslané do Event Hubs měly ID prostředku.

|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**MetricAggregation**|Požadovaný atribut:<br /><br /> **scheduledTransferPeriod** – interval mezi plánovanými transfery do úložiště se zaokrouhluje na nejbližší minutu. Hodnota je [datový typ doba trvání "XML".](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>Element čítače výkonu  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-čítače výkonu*

 Povolí shromažďování čítačů výkonu.  

 Volitelný atribut:  

 Volitelný atribut **scheduledTransferPeriod** Viz vysvětlení výše.

|Podřízený element|Description|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Jsou vyžadovány následující atributy:<br /><br /> - **counterSpecifier** – název čítače výkonu. Například, `\Processor(_Total)\% Processor Time`. Chcete-li získat seznam čítačů výkonu na hostiteli, spusťte příkaz `typeperf` .<br /><br /> - **sampleRate** – jak často se má čítač vzorkovat.<br /><br /> Volitelný atribut:<br /><br /> **jednotka** – Měrná jednotka čítače. Hodnoty jsou k dispozici na [jednotkách UnitType třídě](/dotnet/api/microsoft.azure.management.sql.models.unittype?view=azure-dotnet) |
|**jímky** | Přidáno v 1,5. Nepovinný parametr. Odkazuje na umístění jímky, aby bylo možné také odeslat diagnostická data. Například Azure Monitor nebo Event Hubs. Všimněte si, že je nutné přidat vlastnost *ResourceID* pod element *metriky* , pokud chcete, aby události odeslané do Event Hubs měly ID prostředku.|    




## <a name="windowseventlog-element"></a>Element WindowsEventLog
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-WindowsEventLog*

 Povolí shromažďování protokolů událostí systému Windows.  

 Volitelný atribut **scheduledTransferPeriod** Viz vysvětlení výše.  

|Podřízený element|Description|  
|-------------------|-----------------|  
|**Datového**|Protokoly událostí systému Windows, které mají být shromažďovány. Požadovaný atribut:<br /><br /> **název** – dotaz XPath popisující události systému Windows, které mají být shromažďovány. Například:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Chcete-li shromáždit všechny události, zadejte "*" |
|**jímky** | Přidáno v 1,5. Nepovinný parametr. Odkazuje na umístění jímky, aby také odesílal diagnostická data pro všechny podřízené prvky, které podporují jímky. Příklad jímky je Application Insights nebo Event Hubs.|  


## <a name="logs-element"></a>Logs – element  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-logs*

 K dispozici ve verzi 1,0 a 1,1. Chybějící v 1,2. Přidáno zpět v 1,3.  

 Definuje konfiguraci vyrovnávací paměti pro základní protokoly Azure.  

|Atribut|Typ|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**unsignedInt**|Nepovinný parametr. Určuje maximální velikost úložiště systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|**řetezce**|Nepovinný parametr. Určuje minimální úroveň závažnosti pro přenesené položky protokolu. Výchozí hodnota není **definována**, což přenáší všechny protokoly. Další možné hodnoty (v pořadí od nejvyšších po nejnižší) jsou **podrobné**, **informace**, **varování**, **Chyba**a **kritická**.|  
|**scheduledTransferPeriod**|**úkolu**|Nepovinný parametr. Určuje interval mezi plánovanými přenosy dat, zaokrouhlený na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  
|**jímky** |**řetezce**| Přidáno v 1,5. Nepovinný parametr. Odkazuje na umístění jímky, aby bylo možné také odeslat diagnostická data. Například Application Insights nebo Event Hubs. Všimněte si, že je nutné přidat vlastnost *ResourceID* pod element *metriky* , pokud chcete, aby události odeslané do Event Hubs měly ID prostředku.|  

## <a name="dockersources"></a>DockerSources
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-DiagnosticMonitorConfiguration-DockerSources*

 Přidáno v 1,9.

|Název prvku|Description|  
|------------------|-----------------|  
|**Statistické**|Oznamuje systému, aby shromáždil statistiku pro kontejnery Docker.|  

## <a name="sinksconfig-element"></a>Element SinksConfig  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig*

 Seznam umístění pro odeslání diagnostických dat do a konfigurace související s těmito umístěními.  

|Název prvku|Description|  
|------------------|-----------------|  
|**Jímka**|Viz popis jinde na této stránce.|  

## <a name="sink-element"></a>Element jímky
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-jímka*

 Přidáno ve verzi 1,5.  

 Definuje umístění, do kterých se budou posílat diagnostická data. Například služba Application Insights.  

|Atribut|Typ|Description|  
|---------------|----------|-----------------|  
|**Jméno**|řetězec|Řetězec identifikující jímku.|  

|Prvek|Typ|Description|  
|-------------|----------|-----------------|  
|**Application Insights**|řetězec|Používá se pouze při odesílání dat do Application Insights. Obsahuje klíč instrumentace pro aktivní účet Application Insights, ke kterému máte přístup.|  
|**Kanály**|řetězec|Jedno pro každé další filtrování, které je datového proudu|  

## <a name="channels-element"></a>Element Channels  
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-jímka – kanály*

 Přidáno ve verzi 1,5.  

 Definuje filtry pro streamování dat protokolu procházející jímkou.  

|Prvek|Typ|Description|  
|-------------|----------|-----------------|  
|**Kanál**|řetězec|Viz popis jinde na této stránce.|  

## <a name="channel-element"></a>Element kanálu
 *Strom: root-DiagnosticsConfiguration-PublicConfig-WadCFG-SinksConfig-jímka-kanály-kanál*

 Přidáno ve verzi 1,5.  

 Definuje umístění, do kterých se budou posílat diagnostická data. Například služba Application Insights.  

|Atributy|Typ|Description|  
|----------------|----------|-----------------|  
|**logLevel**|**řetezce**|Určuje minimální úroveň závažnosti pro přenesené položky protokolu. Výchozí hodnota není **definována**, což přenáší všechny protokoly. Další možné hodnoty (v pořadí od nejvyšších po nejnižší) jsou **podrobné**, **informace**, **varování**, **Chyba**a **kritická**.|  
|**Jméno**|**řetezce**|Jedinečný název kanálu, na který se má odkazovat|  


## <a name="privateconfig-element"></a>Element PrivateConfig
 *Strom: root-DiagnosticsConfiguration-PrivateConfig*

 Přidáno ve verzi 1,3.  

 Volitelné  

 Ukládá soukromé údaje o účtu úložiště (název, klíč a koncový bod). Tyto informace se odesílají do virtuálního počítače, ale nedají se z něho načíst.  

|Podřízené elementy|Description|  
|--------------------|-----------------|  
|**StorageAccount**|Účet úložiště, který se má použít. Jsou vyžadovány následující atributy.<br /><br /> - **název** – název účtu úložiště.<br /><br /> - **klíč** -klíč k účtu úložiště.<br /><br /> - **koncový** bod – koncový bod pro přístup k účtu úložiště. <br /><br /> -**sasToken** (přidané 1.8.1) – v privátní konfiguraci můžete místo klíče účtu úložiště zadat token SAS. Pokud je zadaný, klíč účtu úložiště se ignoruje. <br />Požadavky na token SAS: <br />– Podporuje jenom token SAS účtu. <br />- jsou požadovány typy služeb *b*, *t* . <br /> - jsou vyžadována oprávnění *a*, *c*, *u*, *w* . <br /> - jsou požadovány typy prostředků *c*, *o* . <br /> – Podporuje jenom protokol HTTPS. <br /> – Čas zahájení a vypršení platnosti musí být platný.|  


## <a name="isenabled-element"></a>Nepovolený element  
 *Strom: root-DiagnosticsConfiguration--povoleno*

 Datového. Použijte `true` k povolení diagnostiky nebo `false` zakázání diagnostiky.

## <a name="example-configuration"></a>Příklad konfigurace
 Následuje kompletní Ukázková konfigurace pro rozšíření diagnostiky Windows zobrazené v JSON a XML.

 
### <a name="json"></a>JSON

*PublicConfig* a *PrivateConfig* jsou oddělené, protože ve většině případů použití JSON jsou předány jako jiné proměnné. Mezi tyto případy patří šablony Správce prostředků, PowerShell a Visual Studio.

> [!NOTE]
> Definice veřejné jímky Azure Monitor pro konfiguraci má dvě vlastnosti, *ResourceID* a *region*. Ty se vyžadují jenom pro klasické virtuální počítače a klasické cloudové služby. Vlastnost *region* by se neměla používat pro jiné prostředky, na virtuálních počítačích ARM se používá vlastnost *ResourceID* k naplnění pole ResourceID v protokolech odeslaných do Event Hubs.

```json
"PublicConfig" {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 10000,
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "AzureMonitorSink",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT1M",
                        "unit": "percent"
                    }
                ]
            },
            "Directories": {
                "scheduledTransferPeriod": "PT5M",
                "IISLogs": {
                    "containerName": "iislogs"
                },
                "FailedRequestLogs": {
                    "containerName": "iisfailed"
                },
                "DataSources": [
                    {
                        "containerName": "mynewprocess",
                        "Absolute": {
                            "path": "C:\\MyNewProcess",
                            "expandEnvironment": false
                        }
                    },
                    {
                        "containerName": "badapp",
                        "Absolute": {
                            "path": "%SYSTEMDRIVE%\\BadApp",
                            "expandEnvironment": true
                        }
                    },
                    {
                        "containerName": "goodapp",
                        "LocalResource": {
                            "relativePath": "..\\PeanutButter",
                            "name": "Skippy"
                        }
                    }
                ]
            },
            "EtwProviders": {
                "sinks": "",
                "EtwEventSourceProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT5M",
                        "provider": "MyProviderClass",
                        "Event": [
                            {
                                "id": 0
                            },
                            {
                                "id": 1,
                                "eventDestination": "errorTable"
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ],
                "EtwManifestProviderConfiguration": [
                    {
                        "scheduledTransferPeriod": "PT2M",
                        "scheduledTransferLogLevelFilter": "Information",
                        "provider": "5974b00b-84c2-44bc-9e58-3a2451b4e3ad",
                        "Event": [
                            {
                                "id": 0
                            }
                        ],
                        "DefaultEvents": {
                        }
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT5M",
                "DataSource": [
                    {
                        "name": "System!*[System[Provider[@Name='Microsoft Antimalware']]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='NTFS'] and (EventID=55)]]"
                    },
                    {
                        "name": "System!*[System[Provider[@Name='disk'] and (EventID=7 or EventID=52 or EventID=55)]]"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Verbose",
                "sinks": "ApplicationInsights.AppLogs"
            },
            "CrashDumps": {
                "directoryQuotaPercentage": 30,
                "dumpType": "Mini",
                "containerName": "wad-crashdumps",
                "CrashDumpConfiguration": [
                    {
                        "processName": "mynewprocess.exe"
                    },
                    {
                        "processName": "badapp.exe"
                    }
                ]
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "AzureMonitorSink",
                    "AzureMonitor":
                    {
                        "ResourceId": "{insert resourceId if a classic VM or cloud service, else property not needed}",
                        "Region": "{insert Azure region of resource if a classic VM or cloud service, else property not needed}"
                    }
                },
                {
                    "name": "ApplicationInsights",
                    "ApplicationInsights": "{Insert InstrumentationKey}",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "Errors"
                            },
                            {
                                "logLevel": "Verbose",
                                "name": "AppLogs"
                            }
                        ]
                    }
                },
                {
                    "name": "EventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryEventHub",
                    "EventHub": {
                        "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                        "SharedAccessKeyName": "SendRule",
                        "usePublisherId": false
                    }
                },
                {
                    "name": "secondaryStorageAccount",
                    "StorageAccount": {
                        "name": "secondarydiagstorageaccount",
                        "endpoint": "https://core.windows.net"
                    }
                }
            ]
        }
    },
    "StorageAccount": "diagstorageaccount",
    "StorageType": "TableAndBlob"
}
```

> [!NOTE]
> Definice privátního jímky konfigurace Azure Monitor má dvě vlastnosti, *PrincipalId* a *tajný kód*. Ty se vyžadují jenom pro klasické virtuální počítače a klasické cloudové služby. Tyto vlastnosti by neměly být používány pro jiné prostředky.


```json
"PrivateConfig" {
    "storageAccountName": "diagstorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "storageAccountSasToken": "{sas token}",
    "EventHub": {
        "Url": "https://myeventhub-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    },
    "AzureMonitorAccount": {
        "ServicePrincipalMeta": {
            "PrincipalId": "{Insert service principal client Id}",
            "Secret": "{Insert service principal client secret}"
        }
    },
    "SecondaryStorageAccounts": {
        "StorageAccount": [
            {
                "name": "secondarydiagstorageaccount",
                "key": "{base64 encoded key}",
                "endpoint": "https://core.windows.net",
                "sasToken": "{sas token}"
            }
        ]
    },
    "SecondaryEventHubs": {
        "EventHub": [
            {
                "Url": "https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub",
                "SharedAccessKeyName": "SendRule",
                "SharedAccessKey": "{base64 encoded key}"
            }
        ]
    }
}

```

### <a name="xml"></a>XML

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
    <WadCfg>  
      <DiagnosticMonitorConfiguration overallQuotaInMB="10000">  

        <PerformanceCounters scheduledTransferPeriod="PT1M", sinks="AzureMonitorSink">  
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
          <EtwEventSourceProviderConfiguration   
                       provider="MyProviderClass"   
                       scheduledTransferPeriod="PT5M">  
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

        <Logs  bufferQuotaInMB="1024"   
             scheduledTransferPeriod="PT1M"   
             scheduledTransferLogLevelFilter="Verbose"   
             sinks="ApplicationInsights.AppLogs"/>  <!-- sinks attribute added in 1.5 -->  

        <CrashDumps containerName="wad-crashdumps" directoryQuotaPercentage="30" dumpType="Mini">  
          <CrashDumpConfiguration processName="mynewprocess.exe" />  
          <CrashDumpConfiguration processName="badapp.exe"/>  
        </CrashDumps>  

        <DockerSources> <!-- Added in 1.9 -->
          <Stats enabled="true" sampleRate="PT1M" scheduledTransferPeriod="PT1M" />
        </DockerSources>

      </DiagnosticMonitorConfiguration>  

      <SinksConfig>   <!-- Added in 1.5 -->  
        <Sink name="AzureMonitorSink">
            <AzureMonitor> <!-- Added in 1.11 -->
                <resourceId>{insert resourceId}</ResourceId> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs-->
                <Region>{insert Azure region of resource}</Region> <!-- Parameter only needed for classic VMs and Classic Cloud Services, exclude VMSS and Resource Manager VMs -->
            </AzureMonitor>
        </Sink>
        <Sink name="ApplicationInsights">   
          <ApplicationInsights>{Insert InstrumentationKey}</ApplicationInsights>   
          <Channels>   
            <Channel logLevel="Error" name="Errors"  />   
            <Channel logLevel="Verbose" name="AppLogs"  />   
          </Channels>   
        </Sink>   
        <Sink name="EventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryEventHub"> <!-- Added in 1.7 -->
          <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" usePublisherId="false" />
        </Sink>
        <Sink name="secondaryStorageAccount"> <!-- Added in 1.7 -->
          <StorageAccount name="secondarydiagstorageaccount" endpoint="https://core.windows.net" />
        </Sink>
   </SinksConfig>

  </WadCfg>  

  <StorageAccount>diagstorageaccount</StorageAccount>
  <StorageType>TableAndBlob</StorageType> <!-- Added in 1.8 -->  
  </PublicConfig>  

  <PrivateConfig>  <!-- Added in 1.3 -->  
    <StorageAccount name="" key="" endpoint="" sasToken="{sas token}"  />  <!-- sasToken in Private config added in 1.8.1 -->  
    <EventHub Url="https://myeventhub-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />

    <AzureMonitorAccount>
        <ServicePrincipalMeta> <!-- Added in 1.11; only needed for classic VMs and Classic cloud services -->
            <PrincipalId>{Insert service principal clientId}</PrincipalId>
            <Secret>{Insert service principal client secret}</Secret>
        </ServicePrincipalMeta>
    </AzureMonitorAccount>

    <SecondaryStorageAccounts>
       <StorageAccount name="secondarydiagstorageaccount" key="{base64 encoded key}" endpoint="https://core.windows.net" sasToken="{sas token}" />
    </SecondaryStorageAccounts>

    <SecondaryEventHubs>
       <EventHub Url="https://myeventhub-ns.servicebus.windows.net/secondarydiageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
    </SecondaryEventHubs>

  </PrivateConfig>  
  <IsEnabled>true</IsEnabled>  
</DiagnosticsConfiguration>  

```  
> [!NOTE]
> Definice veřejné jímky Azure Monitor pro konfiguraci má dvě vlastnosti, resourceId a region. Ty se vyžadují jenom pro klasické virtuální počítače a klasické cloudové služby. Tyto vlastnosti by se neměly používat pro Správce prostředků Virtual Machines nebo Virtual Machine Scale Sets.
> K dispozici je také další soukromý prvek konfigurace pro Azure Monitor jímka, který projde identifikátorem zabezpečení a tajným kódem. Tento požadavek se vyžaduje jenom pro klasické virtuální počítače a klasické Cloud Services. Pro Správce prostředků virtuální počítače a VMSS definici Azure Monitor v soukromém elementu konfigurace lze vyloučit.
>

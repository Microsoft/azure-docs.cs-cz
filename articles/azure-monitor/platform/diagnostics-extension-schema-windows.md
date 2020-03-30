---
title: Schéma rozšíření diagnostiky systému Windows
description: Odkaz na schéma konfigurace pro rozšíření diagnostiky Systému Windows (WAD) v Azure Monitoru.
ms.subservice: diagnostic-extension
ms.topic: reference
author: bwren
ms.author: bwren
ms.date: 01/20/2020
ms.openlocfilehash: 4c711e1b0a63fbcf978c0e4467eadaed8d91f3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274707"
---
# <a name="windows-diagnostics-extension-schema"></a>Schéma rozšíření diagnostiky systému Windows
Rozšíření Azure Diagnostics je agent ve službě Azure Monitor, který shromažďuje data monitorování z hostovaného operačního systému a úloh výpočetních prostředků Azure. Tento článek podrobně popisuje schéma používané pro konfiguraci rozšíření diagnostiky na virtuálních počítačích s Windows a dalších výpočetních prostředků.

> [!NOTE]
> Schéma v tomto článku je platné pro verze 1.3 a novější (Azure SDK 2.4 a novější). Novější konfigurační oddíly jsou komentovány, aby se zobrazily, v jaké verzi byly přidány. Verze 1.0 a 1.2 schématu byly archivovány a již nejsou k dispozici. 

## <a name="public-configuration-file-schema"></a>Schéma veřejného konfiguračního souboru

Stáhněte si definici schématu veřejného konfiguračního souboru spuštěním následujícího příkazu Prostředí PowerShell:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  


## <a name="common-attribute-types"></a>Běžné typy atributů  
 **scheduledTransferPeriod** atribut se zobrazí v několika prvcích. Jedná se o interval mezi plánovanými převody do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](https://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsKonfigurační prvek  
 *Strom: Kořen – diagnostikaKonfigurace*

Přidáno ve verzi 1.3.  

Prvek nejvyšší úrovně diagnostického konfiguračního souboru.  

**Atribut** xmlns - Obor názvů XML pro konfigurační soubor diagnostiky je:  
`http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration`


|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**PublicConfig**|Povinná hodnota. Viz popis jinde na této stránce.|  
|**PrivateConfig**|Nepovinný parametr. Viz popis jinde na této stránce.|  
|**IsEnabled**|Boolean. Viz popis jinde na této stránce.|  

## <a name="publicconfig-element"></a>PublicConfig Element  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig*

 Popisuje konfiguraci veřejné diagnostiky.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**WadCfg**|Povinná hodnota. Viz popis jinde na této stránce.|  
|**StorageAccount**|Název účtu Azure Storage pro ukládání dat. Může být také zadán jako parametr při provádění rutiny Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Může být *tabulka*, *blob*nebo *tableandblob*. Tabulka je výchozí. Je-li vybrána možnost TableAndBlob, diagnostická data se zapisují dvakrát – jednou do každého typu.|  
|**LocalResourceDirectory**|Adresář na virtuálním počítači, kde agent monitorování ukládá data událostí. Pokud ne, nastavte, použije se výchozí adresář:<br /><br /> Pro roli pracovníka/webu:`C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Pro virtuální počítač:`C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Požadované atributy jsou:<br /><br /> - **cesta** – adresář v systému, který má být používán diagnostikou Azure.<br /><br /> - **expandEnvironment** - Určuje, zda proměnné prostředí jsou rozbaleny v názvu cesty.|  

## <a name="wadcfg-element"></a>WadCFG prvek  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG*

 Identifikuje a konfiguruje data telemetrie, která mají být shromažďována.  


## <a name="diagnosticmonitorconfiguration-element"></a>Diagnostickýkonfigurační prvek
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration*

 Požaduje se

|Atributy|Popis|  
|----------------|-----------------|  
| **celková kvótaInMB** | Maximální množství místa na místním disku, které mohou být spotřebovány různými typy diagnostických dat shromážděných diagnostikou Azure. Výchozí nastavení je 4096 MB.<br />
|**useProxyServer** | Nakonfigurujte Diagnostiku Azure tak, aby používala nastavení proxy serveru nastavená v nastavení aplikace IE.|
|**Propadů** | Přidáno v 1.5. Nepovinný parametr. Odkazuje na umístění jímky také odeslat diagnostická data pro všechny podřízené prvky, které podporují jímky. Příklad jímky je Application Insights nebo Event Hubs.|  


<br /> <br />

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**Crashdumps**|Viz popis jinde na této stránce.|  
|**DiagnosticInfrastructureLogs**|Povolte shromažďování protokolů generovaných diagnostikou Azure. Protokoly diagnostické infrastruktury jsou užitečné pro řešení potíží samotného diagnostického systému. Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** - Nakonfiguruje minimální úroveň závažnosti shromážděných protokolů.<br /><br /> - **scheduledTransferPeriod** - interval mezi plánovanými převody do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Adresáře**|Viz popis jinde na této stránce.|  
|**Poskytovatelé etw**|Viz popis jinde na této stránce.|  
|**Metriky**|Viz popis jinde na této stránce.|  
|**Čítače výkonu**|Viz popis jinde na této stránce.|  
|**Program WindowsEventLog**|Viz popis jinde na této stránce.|
|**Zdroje dockeru**|Viz popis jinde na této stránce. |



## <a name="crashdumps-element"></a>CrashDumps Element  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps*

 Povolte kolekci výpisů stavu.  

|Atributy|Popis|  
|----------------|-----------------|  
|**containerName**|Nepovinný parametr. Název kontejneru objektů blob ve vašem účtu Azure Storage, který se má použít k ukládání výpisů stavu.|  
|**crashDumpType**|Nepovinný parametr.  Konfiguruje Diagnostika Azure tak, aby shromažďovala mini nebo úplné výpisy stavu systému.|  
|**adresářQuotaPercentage**|Nepovinný parametr.  Nakonfiguruje procento **celkové quotaInMB,** které mají být vyhrazeny pro výpisy stavu systému na virtuálním počítači.|  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**CrashDumpKonfigurace**|Povinná hodnota. Definuje hodnoty konfigurace pro každý proces.<br /><br /> Je také vyžadován následující atribut:<br /><br /> **processName** – název procesu, který má diagnostika Azure shromažďovat výpis stavu systému.|  

## <a name="directories-element"></a>Element adresářů
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Adresáře*

 Povolí shromažďování obsahu adresáře, protokolů neúspěšných žádostí o přístup služby IIS a protokolů služby IIS.  

 Volitelný atribut **scheduledTransferPeriod.** Viz vysvětlení dříve.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**Logy iIS**|Zahrnutí tohoto prvku do konfigurace umožňuje shromažďování protokolů služby IIS:<br /><br /> **containerName** – název kontejneru objektů blob ve vašem účtu služby Azure Storage, který se má použít k ukládání protokolů služby IIS.|   
|**Protokoly FailedRequestLogs**|Zahrnutí tohoto prvku do konfigurace umožňuje shromažďování protokolů o neúspěšných požadavcích na web služby IIS nebo aplikaci. Je také nutné povolit možnosti trasování v **systému. WebServer** v **souboru Web.config**.|  
|**Datasources**|Seznam adresářů ke sledování.|




## <a name="datasources-element"></a>Element Zdroje dat  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Adresáře - DataSources*

 Seznam adresářů ke sledování.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**Konfigurace adresářů**|Povinná hodnota. Povinný atribut:<br /><br /> **containerName** – název kontejneru objektů blob ve vašem účtu služby Azure Storage, který se má použít k ukládání souborů protokolu.|  





## <a name="directoryconfiguration-element"></a>Prvek Konfigurace adresářů  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Adresáře - Datové zdroje - DirectoryConfiguration*

 Může obsahovat element **Absolute** nebo **LocalResource,** ale ne obojí.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**Absolutní**|Absolutní cesta k adresáři ke sledování. Jsou vyžadovány následující atributy:<br /><br /> - **Cesta** - Absolutní cesta k adresáři sledovat.<br /><br /> - **expandEnvironment** - Konfiguruje, zda jsou rozbaleny proměnné prostředí v path.|  
|**Místní zdroj**|Cesta vzhledem k místnímu prostředku ke sledování. Požadované atributy jsou:<br /><br /> - **Název** - Místní prostředek, který obsahuje adresář ke sledování<br /><br /> - **relativePath** - Cesta relativní k Name, která obsahuje adresář sledovat|  



## <a name="etwproviders-element"></a>Prvek EtwProviders  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders*

 Konfiguruje kolekci událostí ETW z EventSource a/nebo zprostředkovatelů založených na Manifestu ETW.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**Konfigurace poskytovatele etwEventSourceProvider**|Konfiguruje kolekci událostí generovaných z [třídy EventSource .](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx) Povinný atribut:<br /><br /> **provider** - Název třídy události EventSource.<br /><br /> Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** - Minimální úroveň závažnosti pro přenos do účtu úložiště.<br /><br /> - **scheduledTransferPeriod** - interval mezi plánovanými převody do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderKonfigurace**|Povinný atribut:<br /><br /> **zprostředkovatel** - IDENTIFIKÁTOR GUID poskytovatele události<br /><br /> Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** - Minimální úroveň závažnosti pro přenos do účtu úložiště.<br /><br /> - **scheduledTransferPeriod** - interval mezi plánovanými převody do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>Prvek konfigurace etwEventSourceProvider  
 *Strom: Kořen - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders- EtwEventSourceProviderKonfigurace*

 Konfiguruje kolekci událostí generovaných z [třídy EventSource .](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx)  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**Výchozí události**|Volitelný atribut:<br/><br/> **eventDestination** - Název tabulky pro uložení událostí|  
|**Událost**|Povinný atribut:<br /><br /> **id** - ID události.<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** - Název tabulky pro uložení událostí|  



## <a name="etwmanifestproviderconfiguration-element"></a>Konfigurační prvek EtwManifestProvider  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwManifestProviderConfiguration*

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**Výchozí události**|Volitelný atribut:<br /><br /> **eventDestination** - Název tabulky pro uložení událostí|  
|**Událost**|Povinný atribut:<br /><br /> **id** - ID události.<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** - Název tabulky pro uložení událostí|  



## <a name="metrics-element"></a>Element metriky  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Metriky*

 Umožňuje generovat tabulku čítačů výkonu, která je optimalizována pro rychlé dotazy. Každý čítač výkonu, který je definován v **PerformanceCounters** prvek je uložen v tabulce Metriky kromě tabulky Čítač výkonu.  

 Je vyžadován atribut **resourceId.**  ID prostředku virtuálního počítače nebo škálovací sady virtuálních strojů, do které nasazujete diagnostiku Azure. Získejte **id prostředků** z [webu Azure Portal](https://portal.azure.com). Vyberte **procházet** -> **skupiny**  ->  prostředků **<název\>**. Klikněte na dlaždici **Vlastnosti** a zkopírujte hodnotu z pole **ID.**  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**MetricAgregation**|Povinný atribut:<br /><br /> **scheduledTransferPeriod** - interval mezi plánovanými převody do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](https://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters Element  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - PerformanceCounters*

 Umožňuje shromažďování čítačů výkonu.  

 Volitelný atribut:  

 Volitelný atribut **scheduledTransferPeriod.** Viz vysvětlení dříve.

|Podřízený element|Popis|  
|-------------------|-----------------|  
|**Konfigurace čítače PerformanceCounter**|Jsou vyžadovány následující atributy:<br /><br /> - **counterSpecifier** - Název čítače výkonu. Například, `\Processor(_Total)\% Processor Time`. Chcete-li získat seznam čítačů výkonu `typeperf`na hostiteli, spusťte příkaz .<br /><br /> - **sampleRate** - Jak často by měl být čítač vzorkován.<br /><br /> Volitelný atribut:<br /><br /> **jednotka** - Měrná jednotka čítače. Hodnoty jsou k dispozici ve [třídě UnitType.](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.sql.models.unittype?view=azure-dotnet) |
|**Propadů** | Přidáno v 1.5. Nepovinný parametr. Odkazuje na umístění jímky a odesílá také diagnostická data. Například Azure Monitor nebo Centra událostí.|    




## <a name="windowseventlog-element"></a>WindowsEventLog Element
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog*

 Povolí kolekci protokolů událostí systému Windows.  

 Volitelný atribut **scheduledTransferPeriod.** Viz vysvětlení dříve.  

|Podřízený element|Popis|  
|-------------------|-----------------|  
|**Datasource**|Protokoly událostí systému Windows ke shromažďování. Povinný atribut:<br /><br /> **Name** - Dotaz XPath popisující události systému Windows, které mají být shromažďovány. Například:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Chcete-li shromáždit všechny události, zadejte "*"|  




## <a name="logs-element"></a>Element protokolů  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - Protokoly*

 Přítomno ve verzích 1.0 a 1.1. Chybí v 1.2. Přidáno zpět v 1.3.  

 Definuje konfiguraci vyrovnávací paměti pro základní protokoly Azure.  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**nesignedInt**|Nepovinný parametr. Určuje maximální velikost úložiště systému souborů, které je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilter**|**Řetězec**|Nepovinný parametr. Určuje minimální úroveň závažnosti pro položky protokolu, které jsou přeneseny. Výchozí hodnota je **Undefined**, která přenáší všechny protokoly. Další možné hodnoty (v pořadí od většiny až nejmenších informací) jsou **Podrobné**, **Informace**, **Upozornění**, **Chyba**a **Kritické**.|  
|**scheduledTransferPeriod**|**Doba trvání**|Nepovinný parametr. Určuje interval mezi plánovanými přenosy dat zaokrouhlenými nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  
|**Propadů** |**Řetězec**| Přidáno v 1.5. Nepovinný parametr. Odkazuje na umístění jímky a odesílá také diagnostická data. Například Application Insights nebo Event Hubs.|  

## <a name="dockersources"></a>Zdroje dockeru
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources*

 Přidáno v 1.9.

|Název prvku|Popis|  
|------------------|-----------------|  
|**Statistiky**|Říká systému, aby shromažďoval statistiky pro kontejnery Dockeru|  

## <a name="sinksconfig-element"></a>SinksConfig Element  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - SinksConfig*

 Seznam umístění, do kterých se mají odesílat diagnostická data, a konfigurace přidružená k těmto umístěním.  

|Název prvku|Popis|  
|------------------|-----------------|  
|**Jímka**|Viz popis jinde na této stránce.|  

## <a name="sink-element"></a>Prvek jímky
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - SinksConfig - Sink*

 Přidáno ve verzi 1.5.  

 Definuje umístění, do kterých chcete odesílat diagnostická data. Například služba Application Insights.  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Jméno**|řetězec|Řetězec identifikující název pořezaní.|  

|Element|Typ|Popis|  
|-------------|----------|-----------------|  
|**Application Insights**|řetězec|Používá se pouze při odesílání dat do Application Insights. Obsahují klíč instrumentace pro aktivní účet Application Insights, ke kterému máte přístup.|  
|**Kanály**|řetězec|Jeden pro každé další filtrování, které stream, který jste|  

## <a name="channels-element"></a>Element kanálů  
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - SinksConfig - Sink - Kanály*

 Přidáno ve verzi 1.5.  

 Definuje filtry pro datové proudy dat protokolu procházející jímky.  

|Element|Typ|Popis|  
|-------------|----------|-----------------|  
|**Kanál**|řetězec|Viz popis jinde na této stránce.|  

## <a name="channel-element"></a>Prvek kanálu
 *Strom: Kořen - DiagnostikaKonfigurace - PublicConfig - WadCFG - SinksConfig - Sink - Kanály - Kanál*

 Přidáno ve verzi 1.5.  

 Definuje umístění, do kterých chcete odesílat diagnostická data. Například služba Application Insights.  

|Atributy|Typ|Popis|  
|----------------|----------|-----------------|  
|**Loglevel**|**Řetězec**|Určuje minimální úroveň závažnosti pro položky protokolu, které jsou přeneseny. Výchozí hodnota je **Undefined**, která přenáší všechny protokoly. Další možné hodnoty (v pořadí od většiny až nejmenších informací) jsou **Podrobné**, **Informace**, **Upozornění**, **Chyba**a **Kritické**.|  
|**Jméno**|**Řetězec**|Jedinečný název kanálu, na který se má odkazovat|  


## <a name="privateconfig-element"></a>PrivateConfig Element
 *Strom: Kořen - DiagnostikaKonfigurace - PrivateConfig*

 Přidáno ve verzi 1.3.  

 Nepovinné  

 Ukládá soukromé podrobnosti účtu úložiště (název, klíč a koncový bod). Tyto informace se odešlou do virtuálního počítače, ale nelze z něj načíst.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**StorageAccount**|Účet úložiště, který chcete použít. Následující atributy jsou povinné<br /><br /> - **name** - Název účtu úložiště.<br /><br /> - **klíč** - Klíč k účtu úložiště.<br /><br /> - **koncový bod** - koncový bod pro přístup k účtu úložiště. <br /><br /> -**sasToken** (přidáno 1.8.1) - Můžete zadat token SAS namísto klíče účtu úložiště v privátní konfigurace. Pokud je k dispozici, klíč účtu úložiště je ignorována. <br />Požadavky na token SAS: <br />- Podporuje pouze token SAS účtu <br />- *b* *,* jsou vyžadovány typy služeb t. <br /> - *a*, *c*, *u*, *w* oprávnění jsou požadovány. <br /> - *c*, *o* typy prostředků jsou povinné. <br /> - Podporuje pouze protokol HTTPS <br /> - Doba zahájení a vypršení platnosti musí být platná.|  


## <a name="isenabled-element"></a>Prvek Isenabled  
 *Strom: Kořen – diagnostikaKonfigurace – IsEnabled*

 Boolean. Slouží `true` k povolení diagnostiky nebo `false` k zakázání diagnostiky.

## <a name="example-configuration"></a>Příklad konfigurace
 Následuje kompletní ukázková konfigurace pro rozšíření diagnostiky systému Windows zobrazená v jazycích JSON i XML.

 
### <a name="json"></a>JSON

*PublicConfig* a *PrivateConfig* jsou odděleny, protože ve většině případů použití JSON jsou předány jako různé proměnné. Mezi tyto případy patří šablony Správce prostředků, Prostředí PowerShell a Visual Studio.

> [!NOTE]
> Veřejná konfigurace Azure Monitor jíme definice má dvě vlastnosti, *resourceId* a *oblast*. Ty jsou potřeba jenom pro klasické virtuální aplikace a klasické cloudové služby. Tyto vlastnosti by neměly být použity pro jiné prostředky.

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
> Privátní konfigurace Azure Monitor jímky definice má dvě vlastnosti *PrincipalId* a *tajné*. Ty jsou potřeba jenom pro klasické virtuální aplikace a klasické cloudové služby. Tyto vlastnosti by neměly být použity pro jiné prostředky.


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
> Veřejná konfigurace Azure Monitor definice jímky má dvě vlastnosti, resourceId a oblast. Ty jsou potřeba jenom pro klasické virtuální aplikace a klasické cloudové služby. Tyto vlastnosti by neměly být používány pro virtuální počítače správce prostředků nebo škálovací sady virtuálních počítačů.
> K dispozici je také další private config prvek pro jímky Azure Monitor, který prochází v Id jistiny a tajné. To se vyžaduje jenom pro klasické virtuální choda a klasické cloudové služby. Pro virtuální počítače správce prostředků a VMSS definice Azure Monitor v privátní konfigurační prvek může být vyloučena.
>

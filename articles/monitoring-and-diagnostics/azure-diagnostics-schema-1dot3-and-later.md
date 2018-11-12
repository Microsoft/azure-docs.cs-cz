---
title: Rozšíření Azure Diagnostics 1.3 a novější schéma konfigurace
description: Schéma verze 1.3 a novější diagnostiky Azure se dodávají jako součást Microsoft Azure SDK 2.4 a později.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/20/2018
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 5e74a845fbf263791e3e07006e79e4332646dfd5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51256302"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Azure Diagnostics 1.3 a novější schéma konfigurace
> [!NOTE]
> Rozšíření Azure Diagnostics se používá ke shromažďování čítačů výkonu a další statistiky z komponenty:
> - Azure Virtual Machines
> - Virtual Machine Scale Sets
> - Service Fabric
> - Cloud Services
> - Network Security Groups (Skupiny zabezpečení sítě)
>
> Tato stránka je pouze relevantní, pokud použijete jednu z těchto služeb.

Tato stránka platí pro verze 1.3 a novější (Azure SDK 2.4 nebo novější). Novější konfigurační oddíly jsou pro zobrazení, v jaké verze byly přidány opatřený komentáři.  

Konfigurační soubor je zde popsáno, slouží k nastavení konfigurace diagnostiky při spuštění monitorování diagnostiky.  

Rozšíření se používá ve spojení s dalšími produkty Microsoftu diagnostiky jako je Azure Monitor, Application Insights a Log Analytics.



Stáhněte definici schématu soubor veřejné konfigurace spuštěním následujícího příkazu Powershellu:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Další informace o použití diagnostiky Azure najdete v tématu [rozšíření Azure Diagnostics](azure-diagnostics.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Příklad konfiguračního souboru diagnostiky  
 Následující příklad ukazuje typické diagnostického konfiguračního souboru:  

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
> Veřejná konfigurace definice jímka Azure monitoru má dvě vlastnosti, resourceId a oblast. Jedná se pouze požadované pro klasické virtuální počítače a klasické cloudové služby. Tyto vlastnosti neměl by se používat pro správce prostředků virtuálních počítačů nebo škálovacích sad virtuálních počítačů.
> Je také další prvek privátní konfigurace pro jímka Azure monitoru, která předá objekt Id a tajný klíč. Toto je pouze požadované pro klasické virtuální počítače a cloudové služby Classic. Pro virtuální počítače Resource Manageru a VMSS Azure Monitor můžete vyloučit definice v elementu privátní konfigurace.
>

JSON ekvivalentní předchozí konfiguračního souboru XML.

PublicConfig a PrivateConfig jsou oddělené, protože ve většině případů použití formátu json, jsou předány jako jiné proměnné. Tyto případy zahrnují šablony správce prostředků, Škálovací sady virtuálních počítačů prostředí PowerShell a sady Visual Studio.

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
> Veřejná konfigurace definice jímka Azure monitoru má dvě vlastnosti, resourceId a oblast. Jedná se pouze požadované pro klasické virtuální počítače a klasické cloudové služby.
> Tyto vlastnosti neměl by se používat pro správce prostředků virtuálních počítačů nebo škálovacích sad virtuálních počítačů.
>

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

> [!NOTE]
> Existuje další prvek privátní konfigurace pro jímka Azure monitoru, která předá objekt Id a tajný klíč. Toto je pouze požadované pro klasické virtuální počítače a cloudové služby Classic. Pro virtuální počítače Resource Manageru a VMSS Azure Monitor můžete vyloučit definice v elementu privátní konfigurace.
>


## <a name="reading-this-page"></a>Čtení na této stránce  
 Následující značky jsou zhruba v pořadí uvedeném v předchozím příkladu.  Pokud se nezobrazí úplný popis kde očekáváte, hledání elementu nebo atributu.  

## <a name="common-attribute-types"></a>Běžné typy atributů  
 **Hodnota scheduledTransferPeriod** atributu se zobrazí v několika prvků. Je interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](http://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration – Element  
 *Stromu: Kořen - DiagnosticsConfiguration*

Byly přidány ve verzi 1.3.  

Element nejvyšší úrovně diagnostického konfiguračního souboru.  

**Atribut** je xmlns – obor názvů XML pro konfigurační soubor diagnostiky:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**PublicConfig**|Povinná hodnota. Na této stránce najdete v popisu jinde.|  
|**PrivateConfig**|Volitelné. Na této stránce najdete v popisu jinde.|  
|**hodnotu isEnabled**|Datový typ Boolean. Na této stránce najdete v popisu jinde.|  

## <a name="publicconfig-element"></a>Elementu PublicConfig  
 *Stromové struktury: PublicConfig Root - DiagnosticsConfiguration –*

 Popisuje veřejné konfiguraci diagnostiky.  

|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**WadCfg**|Povinná hodnota. Na této stránce najdete v popisu jinde.|  
|**Účet úložiště**|Název účtu služby Azure Storage k ukládání dat v. Může taky možné specifikovat jako parametr při spuštění rutiny Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Může být *tabulky*, *Blob*, nebo *TableAndBlob*. Tabulka je výchozí. Při výběru TableAndBlob diagnostická data se zapisují dvakrát – jednou pro každý typ.|  
|**LocalResourceDirectory**|Adresář na virtuálním počítači, kde Monitoring Agent ukládá data událostí. Pokud není, nastavit, je použit výchozí adresář:<br /><br /> Pro pracovní nebo webová role: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Pro virtuální počítač: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Jsou povinné atributy:<br /><br /> - **cesta** -directory v systému pro Azure Diagnostics.<br /><br /> - **expandEnvironment** – Určuje, zda jsou proměnné prostředí rozbaleny v názvu cesty.|  

## <a name="wadcfg-element"></a>WadCFG Element  
 *Stromu: - DiagnosticsConfiguration - PublicConfig - WadCFG kořen*

 Identifikuje a nakonfiguruje telemetrických dat, které se mají shromažďovat.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration – Element
 *Stromové struktury: DiagnosticMonitorConfiguration PublicConfig - WadCFG - kořenové - DiagnosticsConfiguration –*

 Požaduje se

|Atributy|Popis|  
|----------------|-----------------|  
| **overallQuotaInMB** | Maximální množství místa místního disku, které může využívat různé typy diagnostická data shromážděná službou Azure Diagnostics. Ve výchozím nastavení je 4 096 MB.<br />
|**useProxyServer** | Konfigurace diagnostiky Azure použít nastavení proxy serveru nastavený v nastavení aplikace Internet Explorer.|
|**jímky** | Přidat do 1.5. Volitelné. Odkazuje na umístění jímky a také posílat diagnostická data pro všechny podřízené prvky, které podporují jímky. Příklad jímky je Application Insights nebo Event Hubs.|  


<br /> <br />

|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**Havarijního výpisu**|Na této stránce najdete v popisu jinde.|  
|**DiagnosticInfrastructureLogs**|Povolte shromažďování protokolů generovaných systémem Azure Diagnostics. Protokoly infrastruktury diagnostiky jsou užitečné při řešení potíží s samotný systém diagnostiky. Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** – Konfiguruje minimální úroveň závažnosti shromažďovaných protokolů.<br /><br /> - **Hodnota scheduledTransferPeriod** – interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Adresáře**|Na této stránce najdete v popisu jinde.|  
|**EtwProviders**|Na této stránce najdete v popisu jinde.|  
|**Metriky**|Na této stránce najdete v popisu jinde.|  
|**Čítače výkonu**|Na této stránce najdete v popisu jinde.|  
|**WindowsEventLog**|Na této stránce najdete v popisu jinde.|
|**DockerSources**|Na této stránce najdete v popisu jinde. |



## <a name="crashdumps-element"></a>Element havarijního výpisu  
 *Stromu: DiagnosticMonitorConfiguration PublicConfig - WadCFG - kořenové - DiagnosticsConfiguration – - havarijního výpisu*

 Povolte shromažďování výpisy při selhání.  

|Atributy|Popis|  
|----------------|-----------------|  
|**containerName**|Volitelné. Název kontejneru objektů blob v účtu Azure Storage, který se má použít k ukládání výpisy při selhání.|  
|**crashDumpType**|Volitelné.  Nakonfiguruje Azure Diagnostics shromažďovat výpisy mini nebo úplné poruše.|  
|**directoryQuotaPercentage**|Volitelné.  Nastaví procento **overallQuotaInMB** které budou rezervovány pro výpisy stavu na virtuálním počítači.|  

|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Povinná hodnota. Definuje hodnoty konfigurace pro každý proces.<br /><br /> Také je vyžadován následující atribut:<br /><br /> **processName** -název procesu, kterou chcete diagnostiky Azure shromažďovat výpis stavu systému pro.|  

## <a name="directories-element"></a>Prvek adresáře
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - adresáře*

 Povoluje shromažďování obsah do adresáře, žádosti o zobrazení protokolů služby IIS se nezdařilo a/nebo protokoly služby IIS.  

 Volitelné **hodnota scheduledTransferPeriod** atribut. Viz vysvětlení dříve.  

|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**IISLogs**|Včetně tohoto elementu v konfiguraci povoluje shromažďování protokolů služby IIS:<br /><br /> **containerName** – název kontejneru objektů blob ve vašem účtu Azure Storage, který se má použít k ukládání protokolů služby IIS.|   
|**FailedRequestLogs**|Včetně tohoto elementu v konfiguraci umožňuje shromažďování protokolů o neúspěšných požadavků na web služby IIS nebo aplikaci. Musíte také povolit trasování možností v části **systému. Webový server** v **Web.config**.|  
|**Zdroje dat**|Seznam adresářů pro monitorování.|




## <a name="datasources-element"></a>Element zdroje dat  
 *Stromové struktury: Zdroje dat PublicConfig - WadCFG - DiagnosticMonitorConfiguration - adresáře - kořenové - DiagnosticsConfiguration –*

 Seznam adresářů pro monitorování.  

|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Povinná hodnota. Požadovaný atribut:<br /><br /> **containerName** – název kontejneru objektů blob ve službě Azure Storage účtu, který se použije k ukládání souborů protokolu.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration – Element  
 *Stromu: Zdroje dat PublicConfig - adresáře WadCFG - DiagnosticMonitorConfiguration – - - DiagnosticsConfiguration - - DirectoryConfiguration kořen*

 Může obsahovat buď **absolutní** nebo **LocalResource** element, ale ne obojí.  

|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**Absolutní**|Absolutní cesta k adresáři pro monitorování. Vyžadují se následující atributy:<br /><br /> - **Cesta** -absolutní cestu k adresáři pro monitorování.<br /><br /> - **expandEnvironment** – konfiguruje, zda jsou rozbaleny proměnné prostředí v cestě.|  
|**LocalResource**|Cesta relativní k místní prostředek pro monitorování. Jsou povinné atributy:<br /><br /> - **Název** – místní prostředek, který obsahuje adresář, který chcete monitorovat<br /><br /> - **relativePath** -cesta relativní vůči název, který obsahuje adresář, který chcete monitorovat|  



## <a name="etwproviders-element"></a>EtwProviders – Element  
 *Stromu: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders kořen*

 Konfigurace shromažďování událostí trasování událostí pro Windows z EventSource nebo manifestu trasování událostí pro Windows na základě poskytovatelů.  

|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Konfigurace shromažďování událostí generovaných [EventSource – třída](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Požadovaný atribut:<br /><br /> **Zprostředkovatel** – název třídy událostí EventSource.<br /><br /> Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** – minimální úroveň závažnosti přenést do účtu úložiště.<br /><br /> - **Hodnota scheduledTransferPeriod** – interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Požadovaný atribut:<br /><br /> **Zprostředkovatel** -identifikátor GUID zprostředkovatele událostí<br /><br /> Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** – minimální úroveň závažnosti přenést do účtu úložiště.<br /><br /> - **Hodnota scheduledTransferPeriod** – interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration – Element  
 *Stromu: Kořen - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwEventSourceProviderConfiguration*

 Konfigurace shromažďování událostí generovaných [EventSource – třída](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br/><br/> **eventDestination** -název tabulky k uložení událostí v|  
|**Události**|Požadovaný atribut:<br /><br /> **ID** – id události.<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** -název tabulky k uložení událostí v|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration – Element  
 *Stromové struktury: EtwManifestProviderConfiguration PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - kořenové - DiagnosticsConfiguration –*

|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br /><br /> **eventDestination** -název tabulky k uložení událostí v|  
|**Události**|Požadovaný atribut:<br /><br /> **ID** – id události.<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** -název tabulky k uložení událostí v|  



## <a name="metrics-element"></a>Element metriky  
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration – metriky*

 Umožňuje generovat tabulku čítače výkonu, který je optimalizovaný pro rychlé zpracování dotazů. Každý čítač výkonu, který je definován v **čítače výkonu** element je uložena v tabulce metrik kromě tabulce čítače výkonu.  

 **ResourceId** atribut je vyžadován.  ID prostředku virtuálního počítače nebo Škálovací sady virtuálních počítačů jsou diagnostiky Azure pro nasazení. Získejte **resourceID** z [webu Azure portal](https://portal.azure.com). Vyberte **Procházet** -> **skupiny prostředků** -> **< název\>**. Klikněte na tlačítko **vlastnosti** dlaždici a zkopírujte hodnotu z **ID** pole.  

|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**MetricAggregation**|Požadovaný atribut:<br /><br /> **Hodnota scheduledTransferPeriod** – interval mezi naplánované přenosy do úložiště zaokrouhluje nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datový typ."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters – Element  
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration – čítače výkonu*

 Povolí shromažďování čítačů výkonu.  

 Volitelný atribut:  

 Volitelné **hodnota scheduledTransferPeriod** atribut. Viz vysvětlení dříve.

|Podřízený Element.|Popis|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Vyžadují se následující atributy:<br /><br /> - **counterSpecifier** – název čítače výkonu. Například, `\Processor(_Total)\% Processor Time`. Pokud chcete získat seznam čítačů výkonu na hostiteli, spusťte příkaz `typeperf`.<br /><br /> - **sampleRate** – jak často se čítač vzorkování.<br /><br /> Volitelný atribut:<br /><br /> **jednotka** – jednotka měření čítače.|
|**jímky** | Přidat do 1.5. Volitelné. Odkazuje na umístění jímky a také posílat diagnostická data. Například Azure Monitor nebo Event Hubs.|    




## <a name="windowseventlog-element"></a>WindowsEventLog – Element
 *Stromu: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog kořen*

 Povolí shromažďování protokolů událostí Windows.  

 Volitelné **hodnota scheduledTransferPeriod** atribut. Viz vysvětlení dříve.  

|Podřízený Element.|Popis|  
|-------------------|-----------------|  
|**Zdroj dat**|Protokoly událostí Windows a shromažďovat. Požadovaný atribut:<br /><br /> **název** – dotaz XPath popisující události systému windows, které se mají shromažďovat. Příklad:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Ke shromažďování všech událostí, zadejte "*"|  




## <a name="logs-element"></a>Protokoly – Element  
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration – protokoly*

 K dispozici ve verzi 1.0 a 1.1. Chybí 1.2. Přidat zpět v 1.3.  

 Definuje konfiguraci vyrovnávací paměti pro základní protokolů Azure.  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**celé číslo bez znaménka**|Volitelné. Určuje maximální velikost úložiště v systému souborů, která je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilterr**|**řetězec**|Volitelné. Určuje minimální úroveň závažnosti pro položky protokolu, které byly převedeny. Výchozí hodnota je **Nedefinováno**, který převede všechny protokoly. Další možné hodnoty (v pořadí podle nejvíce alespoň informace) jsou **Verbose**, **informace**, **upozornění**, **chyba**a **Kritické**.|  
|**Hodnota scheduledTransferPeriod**|**Doba trvání**|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhluje nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  
|**jímky** |**řetězec**| Přidat do 1.5. Volitelné. Odkazuje na umístění jímky a také posílat diagnostická data. Například Application Insights nebo Event Hubs.|  

## <a name="dockersources"></a>DockerSources
 *Stromu: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources kořen*

 Přidáno v 1.9.

|Název elementu|Popis|  
|------------------|-----------------|  
|**Statistiky**|Říká systému ke shromažďování statistik pro kontejnery Dockeru|  

## <a name="sinksconfig-element"></a>SinksConfig – Element  
 *Stromové struktury: SinksConfig PublicConfig - WadCFG - kořenové - DiagnosticsConfiguration –*

 Seznam umístění k odesílání diagnostických dat a konfigurace související s těchto umístěních.  

|Název elementu|Popis|  
|------------------|-----------------|  
|**jímka**|Na této stránce najdete v popisu jinde.|  

## <a name="sink-element"></a>Jímky – Element
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig – jímky*

 Byly přidány ve verzi 1.5.  

 Definuje umístění, kde se posílat diagnostická data. Například služba Application Insights.  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Jméno**|řetězec|Řetězec, který identifikuje sinkname.|  

|Element|Typ|Popis|  
|-------------|----------|-----------------|  
|**Application Insights**|řetězec|Používá pouze při odesílání dat do služby Application Insights. Obsahují Instrumentační klíč pro aktivní účet Application Insights, máte přístup.|  
|**kanály**|řetězec|Jeden pro každý další filtrování datového proudu, který jste|  

## <a name="channels-element"></a>Element kanály  
 *Stromové struktury: Kanály SinksConfig – jímky – Root - DiagnosticsConfiguration - PublicConfig - WadCFG –*

 Byly přidány ve verzi 1.5.  

 Definuje filtry pro datové proudy prochází jímky dat protokolu.  

|Element|Typ|Popis|  
|-------------|----------|-----------------|  
|**Kanál**|řetězec|Na této stránce najdete v popisu jinde.|  

## <a name="channel-element"></a>Kanál – Element
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG – SinksConfig – jímky – kanály – kanál*

 Byly přidány ve verzi 1.5.  

 Definuje umístění, kde se posílat diagnostická data. Například služba Application Insights.  

|Atributy|Typ|Popis|  
|----------------|----------|-----------------|  
|**LogLevel**|**řetězec**|Určuje minimální úroveň závažnosti pro položky protokolu, které byly převedeny. Výchozí hodnota je **Nedefinováno**, který převede všechny protokoly. Další možné hodnoty (v pořadí podle nejvíce alespoň informace) jsou **Verbose**, **informace**, **upozornění**, **chyba**a **Kritické**.|  
|**Jméno**|**řetězec**|Jedinečný název kanálu pro odkazování na|  


## <a name="privateconfig-element"></a>Elementu PrivateConfig
 *Stromové struktury: PrivateConfig Root - DiagnosticsConfiguration –*

 Byly přidány ve verzi 1.3.  

 Nepovinné  

 Ukládá soukromé podrobnosti o účtu úložiště (název, klíč a koncový bod). Tyto informace je odeslána k virtuálnímu počítači, ale nelze načíst z něj.  

|Podřízené prvky|Popis|  
|--------------------|-----------------|  
|**Účet úložiště**|Účet úložiště používat. Následující atributy jsou povinné<br /><br /> - **název** – název účtu úložiště.<br /><br /> - **klíč** – klíč k účtu úložiště.<br /><br /> - **koncový bod** – koncový bod pro přístup k účtu úložiště. <br /><br /> -**sasToken** (přidáno 1.8.1)-tokenu SAS místo klíče účtu úložiště můžete zadat v privátní konfigurace. Pokud je zadán, klíče účtu úložiště se ignoruje. <br />Požadavky pro SAS Token: <br />– Podporuje pouze token SAS účtu <br />- *b*, *t* jsou požadovány typy služeb. <br /> - *a*, *c*, *u*, *w* jsou požadována oprávnění. <br /> - *c*, *o* typy prostředků jsou povinné. <br /> – Podporuje pouze protokol HTTPS <br /> – Začněte a čas vypršení platnosti musí být platné.|  


## <a name="isenabled-element"></a>IsEnabled Element  
 *Stromové struktury: IsEnabled Root - DiagnosticsConfiguration –*

 Datový typ Boolean. Použití `true` povolit diagnostiku nebo `false` zakázat diagnostiku.

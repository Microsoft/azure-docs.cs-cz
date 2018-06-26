---
title: Rozšíření Azure Diagnostics 1.3 a novější schéma konfigurace
description: Verze schématu 1.3 a novější Azure diagnostics dodávána jako součást Microsoft Azure SDK 2.4 a později.
services: azure-monitor
author: rboucher
ms.service: azure-monitor
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/15/2017
ms.author: robb
ms.component: diagnostic-extension
ms.openlocfilehash: 501e28cf3d01385d65a2308db06702d2db0d91ee
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36937909"
---
# <a name="azure-diagnostics-13-and-later-configuration-schema"></a>Azure Diagnostics 1.3 a novější schéma konfigurace
> [!NOTE]
> Rozšíření diagnostiky Azure je komponenta, kterou používá ke shromažďování čítačů výkonu a další statistiky z:
> - Azure Virtual Machines 
> - Virtual Machine Scale Sets
> - Service Fabric 
> - Cloud Services 
> - Network Security Groups (Skupiny zabezpečení sítě)
> 
> Tato stránka je pouze relevantní, pokud používáte některou z těchto služeb.

Tato stránka je platná pro verze 1.3 a novější (Azure SDK 2.4 nebo novější). Novější konfigurační oddíly jsou vloženy do komentáře k zobrazení, v jaké verze byly přidány.  

V souboru konfigurace zde popsané slouží k nastavení konfigurace diagnostiky při spuštění diagnostiky monitorování.  

Rozšíření se používá ve spojení s dalšími produkty Microsoftu diagnostiky jako monitorování Azure, Application Insights a analýzy protokolů.



Stáhněte si definici veřejné konfigurace souboru schématu spuštěním následujícího příkazu Powershellu:  

```powershell  
(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File –Encoding utf8 -FilePath 'C:\temp\WadConfig.xsd'  
```  

Další informace o používání Azure Diagnostics najdete v tématu [rozšíření diagnostiky Azure](azure-diagnostics.md).  

## <a name="example-of-the-diagnostics-configuration-file"></a>Příklad konfiguračního souboru diagnostiky  
 Následující příklad ukazuje konfigurační soubor typické diagnostics:  

```xml  
<?xml version="1.0" encoding="utf-8"?>  
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">   
  <PublicConfig>  
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

JSON ekvivalent předchozí konfiguračního souboru XML. 

PublicConfig a PrivateConfig jsou oddělené, protože ve většině případů použití json jsou předávány jako jiné proměnné. Tyto případy patří šablony Resource Manageru, sady škálování virtuálního počítače prostředí PowerShell a Visual Studio. 

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

## <a name="reading-this-page"></a>Čtení této stránce  
 Značky následující jsou přibližně v pořadí uvedeném v předchozím příkladu.  Pokud nevidíte úplný popis kde očekáváte, vyhledejte stránce elementu nebo atributu.  

## <a name="common-attribute-types"></a>Běžné typy atributů  
 **scheduledTransferPeriod** atributu se zobrazí v několika elementy. Je interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datového typu."](http://www.w3schools.com/xml/schema_dtypes_date.asp)


## <a name="diagnosticsconfiguration-element"></a>DiagnosticsConfiguration Element  
 *Stromu: Kořen - DiagnosticsConfiguration*

Přidána do verze 1.3.  

Element nejvyšší úrovně v souboru konfigurace diagnostiky.  

**Atribut** xmlns – obor názvů XML pro konfigurační soubor diagnostiky je:  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  


|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**PublicConfig**|Povinná hodnota. Na této stránce najdete v popisu jinde.|  
|**PrivateConfig**|Volitelné. Na této stránce najdete v popisu jinde.|  
|**Hodnotu IsEnabled**|Logická hodnota. Na této stránce najdete v popisu jinde.|  

## <a name="publicconfig-element"></a>PublicConfig Element  
 *Stromové struktury: PublicConfig kořenové - DiagnosticsConfiguration-*

 Popisuje konfiguraci veřejné diagnostiky.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**WadCfg**|Povinná hodnota. Na této stránce najdete v popisu jinde.|  
|**StorageAccount**|Název účtu úložiště Azure pro ukládání dat v. Můžete také zadat jako parametr při spuštění rutiny Set-AzureServiceDiagnosticsExtension.|  
|**StorageType**|Může být *tabulky*, *Blob*, nebo *TableAndBlob*. Tabulka je výchozí. Při výběru TableAndBlob diagnostická data se zapisují dvakrát – jednou pro každý typ.|  
|**LocalResourceDirectory**|Adresář na virtuálním počítači, kde Monitoring Agent ukládá data události. Pokud ne, nastavit, používá výchozí adresář:<br /><br /> Pro roli pracovního procesu nebo webové: `C:\Resources\<guid>\directory\<guid>.<RoleName.DiagnosticStore\`<br /><br /> Pro virtuální počítač: `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<WADVersion>\WAD<WADVersion>`<br /><br /> Povinné atributy jsou:<br /><br /> - **cesta** -adresáři na serveru, který má být používána Azure Diagnostics.<br /><br /> - **expandEnvironment** -Určuje, zda jsou v názvu cesty rozbalit proměnné prostředí.|  

## <a name="wadcfg-element"></a>WadCFG Element  
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG*
 
 Identifikuje a nakonfiguruje telemetrická data, které se mají shromažďovat.  


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration Element 
 *Stromové struktury: DiagnosticMonitorConfiguration PublicConfig - WadCFG - kořenové - DiagnosticsConfiguration-*

 Požaduje se 

|Atributy|Popis|  
|----------------|-----------------|  
| **overallQuotaInMB** | Maximální množství místa na místní disk, který může být využívány službou různé typy diagnostická data shromažďovaná společností Azure Diagnostics. Výchozí nastavení je 4 096 MB.<br />
|**useProxyServer** | Konfigurace Azure Diagnostics chcete použít nastavení proxy serveru jako sada v nastavení aplikace Internet Explorer.|  

<br /> <br />

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**CrashDumps**|Na této stránce najdete v popisu jinde.|  
|**DiagnosticInfrastructureLogs**|Povolte shromažďování protokolů generovaných Azure Diagnostics. Infrastruktura diagnostické protokoly jsou užitečné pro řešení potíží s samotného systému diagnostiky. Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** -nakonfiguruje závažnost minimální úroveň shromážděné protokoly.<br /><br /> - **scheduledTransferPeriod** -interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datového typu."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**Adresáře**|Na této stránce najdete v popisu jinde.|  
|**EtwProviders**|Na této stránce najdete v popisu jinde.|  
|**Metriky**|Na této stránce najdete v popisu jinde.|  
|**Čítače výkonu**|Na této stránce najdete v popisu jinde.|  
|**WindowsEventLog**|Na této stránce najdete v popisu jinde.| 
|**DockerSources**|Na této stránce najdete v popisu jinde. | 



## <a name="crashdumps-element"></a>CrashDumps Element  
 *Stromu: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - CrashDumps kořen*
 
 Povolení shromažďování těchto výpisy stavu systému.  

|Atributy|Popis|  
|----------------|-----------------|  
|**containerName**|Volitelné. Název kontejneru objektů blob v účtu úložiště Azure, který se má použít k uložení výpisy stavu systému.|  
|**crashDumpType**|Volitelné.  Nakonfiguruje Azure Diagnostics ke shromažďování výpisů paměti mini nebo úplné selhat.|  
|**directoryQuotaPercentage**|Volitelné.  Nakonfiguruje procento **overallQuotaInMB** jako vyhrazené pro výpisů stavu systému ve virtuálním počítači.|  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**CrashDumpConfiguration**|Povinná hodnota. Definuje hodnoty konfigurace pro jednotlivé procesy.<br /><br /> Je také nutný následující atribut:<br /><br /> **název_procesu** -název procesu chcete shromažďovat výpis stavu pro Azure Diagnostics.|  

## <a name="directories-element"></a>Element adresáře 
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - adresáře*

 Povoluje shromažďování obsah adresáře, protokoly žádost o přístup služby IIS se nezdařilo nebo protokoly služby IIS.  

 Volitelné **scheduledTransferPeriod** atribut. Viz vysvětlení dříve.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**IISLogs**|Tento element včetně v konfiguraci povoluje shromažďování protokoly služby IIS:<br /><br /> **containerName** -název kontejneru objektů blob v účtu úložiště Azure, který se má použít k ukládání protokolů služby IIS.|   
|**FailedRequestLogs**|Umožňuje shromažďování protokolů o neúspěšných požadavků na web služby IIS nebo aplikaci, včetně tento prvek v konfiguraci. Je také nutné povolit trasování možnosti v části **systému. Webový server** v **Web.config**.|  
|**Zdroje dat**|Seznam adresáře, které chcete monitorovat.| 




## <a name="datasources-element"></a>Element zdrojů dat  
 *Stromové struktury: Zdroje dat PublicConfig - WadCFG - DiagnosticMonitorConfiguration - adresáře - kořenové - DiagnosticsConfiguration-*

 Seznam adresáře, které chcete monitorovat.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**DirectoryConfiguration**|Povinná hodnota. Požadovaný atribut:<br /><br /> **containerName** -název kontejneru objektů blob v Azure Storage účtu, který se má použít k ukládání souborů protokolu.|  





## <a name="directoryconfiguration-element"></a>DirectoryConfiguration Element  
 *Stromu: Zdroje dat PublicConfig - WadCFG - DiagnosticMonitorConfiguration - adresáře - kořenové - DiagnosticsConfiguration - - DirectoryConfiguration*

 Může obsahovat buď **absolutní** nebo **LocalResource** elementu, ale ne obojí.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**Absolutní**|Absolutní cesta k adresáři pro monitorování. Vyžadují se následující atributy:<br /><br /> - **Cesta** -absolutní cestu k adresáři pro monitorování.<br /><br /> - **expandEnvironment** – konfiguruje, zda jsou rozbalit proměnné prostředí v cestě.|  
|**LocalResource**|Cesta relativní k místní prostředek pro monitorování. Povinné atributy jsou:<br /><br /> - **Název** -místní prostředek, který obsahuje adresář, který chcete monitorovat<br /><br /> - **relativePath** – relativní název, který obsahuje adresář, který chcete monitorovat cestu|  



## <a name="etwproviders-element"></a>EtwProviders Element  
 *Stromu: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders kořen*

 Nakonfiguruje shromažďování událostí trasování událostí pro Windows z EventSource nebo Manifest trasování událostí pro Windows na základě zprostředkovatele.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**EtwEventSourceProviderConfiguration**|Nakonfiguruje kolekce událostí generovaných [EventSource – třída](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx). Požadovaný atribut:<br /><br /> **Zprostředkovatel** -název třídy událostí EventSource.<br /><br /> Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** -úroveň závažnosti minimální přenést do účtu úložiště.<br /><br /> - **scheduledTransferPeriod** -interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datového typu."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  
|**EtwManifestProviderConfiguration**|Požadovaný atribut:<br /><br /> **Zprostředkovatel** -GUID zprostředkovatele událostí<br /><br /> Volitelné atributy jsou:<br /><br /> - **scheduledTransferLogLevelFilter** -úroveň závažnosti minimální přenést do účtu úložiště.<br /><br /> - **scheduledTransferPeriod** -interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datového typu."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="etweventsourceproviderconfiguration-element"></a>EtwEventSourceProviderConfiguration Element  
 *Stromu: Kořen - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - EtwEventSourceProviderConfiguration*

 Nakonfiguruje kolekce událostí generovaných [EventSource – třída](http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource\(v=vs.110\).aspx).  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br/><br/> **eventDestination** -název tabulku pro ukládání událostí v|  
|**Události**|Požadovaný atribut:<br /><br /> **ID** -id události.<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** -název tabulku pro ukládání událostí v|  



## <a name="etwmanifestproviderconfiguration-element"></a>EtwManifestProviderConfiguration Element  
 *Stromové struktury: EtwManifestProviderConfiguration PublicConfig - WadCFG - DiagnosticMonitorConfiguration - EtwProviders - kořenové - DiagnosticsConfiguration-*

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**DefaultEvents**|Volitelný atribut:<br /><br /> **eventDestination** -název tabulku pro ukládání událostí v|  
|**Události**|Požadovaný atribut:<br /><br /> **ID** -id události.<br /><br /> Volitelný atribut:<br /><br /> **eventDestination** -název tabulku pro ukládání událostí v|  



## <a name="metrics-element"></a>Element metriky  
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - metriky*

 Umožňuje generovat tabulku čítače výkonu, která je optimalizovaná pro rychlé dotazy. Jednotlivých čítačů výkonu, která je definována v **čítače výkonu** element je uložené v tabulce metriky kromě tabulky čítače výkonu.  

 **ResourceId** atribut je požadován.  ID prostředku virtuálního počítače nebo sadu škálování virtuálního počítače, které nasazujete do Azure Diagnostics. Získat **resourceID** z [portál Azure](https://portal.azure.com). Vyberte **Procházet** -> **skupiny prostředků** -> **< název\>**. Klikněte **vlastnosti** dlaždici a zkopírujte hodnotu z **ID** pole.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**MetricAggregation**|Požadovaný atribut:<br /><br /> **scheduledTransferPeriod** -interval mezi naplánované přenosy do úložiště zaokrouhlený nahoru na nejbližší minutu. Hodnota je [XML "Doba trvání datového typu."](http://www.w3schools.com/xml/schema_dtypes_date.asp) |  



## <a name="performancecounters-element"></a>PerformanceCounters – Element  
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - čítače výkonu*

 Povoluje shromažďování čítačů výkonu.  

 Volitelný atribut:  

 Volitelné **scheduledTransferPeriod** atribut. Viz vysvětlení dříve.

|Podřízený Element|Popis|  
|-------------------|-----------------|  
|**PerformanceCounterConfiguration**|Vyžadují se následující atributy:<br /><br /> - **counterSpecifier** – název čítače výkonu. Například, `\Processor(_Total)\% Processor Time`. Pokud chcete získat seznam čítačů výkonu na hostiteli, spusťte příkaz `typeperf`.<br /><br /> - **sampleRate** – jak často se čítač odeberou.<br /><br /> Volitelný atribut:<br /><br /> **jednotka** -měrnou čítače.|  




## <a name="windowseventlog-element"></a>WindowsEventLog Element
 *Stromu: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - WindowsEventLog kořen*
 
 Umožňuje kolekce protokoly událostí systému Windows.  

 Volitelné **scheduledTransferPeriod** atribut. Viz vysvětlení dříve.  

|Podřízený Element|Popis|  
|-------------------|-----------------|  
|**Zdroj dat**|Protokol událostí systému Windows ke shromažďování. Požadovaný atribut:<br /><br /> **název** – dotaz XPath popisující události systému windows, které se mají shromažďovat. Příklad:<br /><br /> `Application!*[System[(Level <=3)]], System!*[System[(Level <=3)]], System!*[System[Provider[@Name='Microsoft Antimalware']]], Security!*[System[(Level <= 3)]`<br /><br /> Ke shromažďování všech událostí, zadejte "*"|  




## <a name="logs-element"></a>Element protokoly  
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - protokoly*

 Prezentovat v verze 1.0 a 1.1. Chybí v 1.2. Přidat zpět v 1.3.  

 Definuje konfiguraci vyrovnávací paměti pro základní Azure protokoly.  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|**celé číslo bez znaménka**|Volitelné. Určuje maximální množství úložiště systému souboru, který je k dispozici pro zadaná data.<br /><br /> Výchozí hodnota je 0.|  
|**scheduledTransferLogLevelFilterr**|**Řetězec**|Volitelné. Určuje úroveň závažnosti minimální pro položky protokolu, které se přenáší. Výchozí hodnota je **Undefined**, která přenáší všechny protokoly. Další možné hodnoty (v pořadí podle nejvíc minimálně informace) jsou **podrobné**, **informace**, **upozornění**, **chyba**a **Kritické**.|  
|**scheduledTransferPeriod**|**Doba trvání**|Volitelné. Určuje interval mezi naplánované přenosů dat, zaokrouhlený nahoru na nejbližší minutu.<br /><br /> Výchozí hodnota je PT0S.|  
|**jímky** přidali v 1.5|**Řetězec**|Volitelné. Bodů na jímka umístění a také posílat diagnostická data. Například Application Insights.|  

## <a name="dockersources"></a>DockerSources
 *Stromu: - DiagnosticsConfiguration - PublicConfig - WadCFG - DiagnosticMonitorConfiguration - DockerSources kořen*

 Přidaná do 1.9.

|Název elementu|Popis|  
|------------------|-----------------|  
|**Statistiky**|Říká systému ke shromažďování statistik pro Docker kontejnery|  

## <a name="sinksconfig-element"></a>SinksConfig Element  
 *Stromové struktury: SinksConfig PublicConfig - WadCFG - kořenové - DiagnosticsConfiguration-*

 Seznam umístění odesílat data diagnostiky a konfigurace přidružené těchto umístěních.  

|Název elementu|Popis|  
|------------------|-----------------|  
|**Podřízený**|Na této stránce najdete v popisu jinde.|  

## <a name="sink-element"></a>Jímky – Element
 *Strom: Root - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - jímka*

 Přidána do verze 1.5.  

 Definuje umístění poslat diagnostická data. Například službu Application Insights.  

|Atribut|Typ|Popis|  
|---------------|----------|-----------------|  
|**Jméno**|řetězec|Řetězec identifikující sinkname.|  

|Element|Typ|Popis|  
|-------------|----------|-----------------|  
|**Application Insights**|řetězec|Použít pouze při odesílání dat do služby Application Insights. Obsahovat klíč instrumentace pro aktivní účet Application Insights, máte přístup.|  
|**Kanály**|řetězec|Jeden pro každou další filtrování, který datového proudu, který jste|  

## <a name="channels-element"></a>Element kanály  
 *Stromové struktury: Kanály SinksConfig - jímka - kořenové - DiagnosticsConfiguration - PublicConfig - WadCFG-*

 Přidána do verze 1.5.  

 Definuje filtry pro datové proudy prošla jímku dat protokolu.  

|Element|Typ|Popis|  
|-------------|----------|-----------------|  
|**Kanál**|řetězec|Na této stránce najdete v popisu jinde.|  

## <a name="channel-element"></a>Element kanálu
 *Strom: Kořenové - DiagnosticsConfiguration - PublicConfig - WadCFG - SinksConfig - jímka - kanály - kanál*

 Přidána do verze 1.5.  

 Definuje umístění poslat diagnostická data. Například službu Application Insights.  

|Atributy|Typ|Popis|  
|----------------|----------|-----------------|  
|**logLevel**|**Řetězec**|Určuje úroveň závažnosti minimální pro položky protokolu, které se přenáší. Výchozí hodnota je **Undefined**, která přenáší všechny protokoly. Další možné hodnoty (v pořadí podle nejvíc minimálně informace) jsou **podrobné**, **informace**, **upozornění**, **chyba**a **Kritické**.|  
|**Jméno**|**Řetězec**|Jedinečný název kanálu k odkazování na|  


## <a name="privateconfig-element"></a>PrivateConfig Element 
 *Stromové struktury: PrivateConfig kořenové - DiagnosticsConfiguration-*

 Přidána do verze 1.3.  

 Nepovinné  

 Ukládá soukromé podrobnosti o účtu úložiště (název, klíč a koncového bodu). Tyto informace je odeslána do virtuálního počítače, ale nelze načíst z něj.  

|Podřízené elementy|Popis|  
|--------------------|-----------------|  
|**StorageAccount**|Účet úložiště používat. Následující atributy jsou požadovány<br /><br /> - **název** – název účtu úložiště.<br /><br /> - **klíč** -klíč k účtu úložiště.<br /><br /> - **koncový bod** -koncový bod pro přístup k účtu úložiště. <br /><br /> -**sasToken** (přidají 1.8.1)-tokenu SAS místo klíč účtu úložiště můžete zadat v privátní konfigurace. Pokud je zadán, klíče účtu úložiště se ignoruje. <br />Požadavky pro SAS Token: <br />– Podporuje pouze tokenu SAS účtu <br />- *b*, *t* jsou požadovány typy služby. <br /> - *a*, *c*, *u*, *w* jsou požadována oprávnění. <br /> - *c*, *o* jsou požadovány typy prostředků. <br /> – Podporuje protokol HTTPS <br /> -Spuštění a čas vypršení platnosti musí být platné.|  


## <a name="isenabled-element"></a>Element hodnotu IsEnabled  
 *Stromové struktury: Hodnotu IsEnabled kořenové - DiagnosticsConfiguration-*

 Logická hodnota. Použití `true` povolit diagnostiku nebo `false` zakázat diagnostiku.

---
title: Odesílání dat z rozšíření diagnostiky Windows Azure do Azure Event Hubs
description: Nakonfigurujte rozšíření diagnostiky v Azure Monitoru pro odesílání dat do Centra událostí Azure, abyste je mohli předávat do umístění mimo Azure.
ms.subservice: diagnostic-extension
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 5e5034e99d37d3681192c2ad066f28acd1c4aeeb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672527"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Odesílání dat z rozšíření diagnostiky Windows Azure do Azure Event Hubs
Rozšíření diagnostiky Azure je agent ve službě Azure Monitor, který shromažďuje data monitorování z hostovaného operačního systému a úlohy virtuálních počítačů Azure a dalších výpočetních prostředků. Tento článek popisuje, jak odesílat data z rozšíření Windows Azure Diagnostic (WAD) do [Azure Event Hubs,](https://azure.microsoft.com/services/event-hubs/) abyste mohli přeposílat do umístění mimo Azure.

## <a name="supported-data"></a>Podporovaná data

Data shromážděná z hostovaného operačního systému, která lze odeslat do centra událostí, zahrnují následující. Jiné zdroje dat shromážděné pomocí protokolu WAD, včetně protokolů iis a výpisů stavu zabezpečení, nelze odeslat do centra událostí.

* Události Trasování událostí pro Windows
* Čítače výkonu
* Protokoly událostí systému Windows, včetně protokolů aplikací v protokolu událostí systému Windows
* Protokolů infrastruktury Azure Diagnostics

## <a name="prerequisites"></a>Požadavky

* Rozšíření diagnostiky systému Windows 1.6 nebo vyšší. Informace o podporovaných prostředcích najdete v [tématu verze a historie](diagnostics-extension-versions.md) konfigurace rozšíření diagnostiky [Azure](diagnostics-extension-overview.md) a historie.
* Obor názvů Event Hubs musí být vždy zřízen. Podrobnosti [najdete v tématu Začínáme s centrummi událostí.](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)


## <a name="configuration-schema"></a>Schéma konfigurace
Viz [Instalace a konfigurace rozšíření diagnostiky Windows Azure (WAD)](diagnostics-extension-windows-install.md) pro různé možnosti povolení a konfigurace rozšíření diagnostiky a schéma konfigurace [Diagnostika Azure](diagnostics-extension-schema-windows.md) pro odkaz na schéma konfigurace. Zbytek tohoto článku bude popisovat, jak pomocí této konfigurace odesílat data do centra událostí. 

Diagnostika Azure vždy odesílá protokoly a metriky do účtu Azure Storage. Můžete nakonfigurovat jeden nebo více *jímek dat,* které odesílají data do dalších umístění. Každý jímka je definována v [SinksConfig prvek](diagnostics-extension-schema-windows.md#sinksconfig-element) veřejné konfigurace s citlivými informacemi v privátní konfiguraci. Tato konfigurace pro centra událostí používá hodnoty v následující tabulce.

| Vlastnost | Popis |
|:---|:---|
| Name (Název) | Popisný název pro umyvadlo. Používá se v konfiguraci k určení zdrojů dat, které mají být odeslány do jímky. |
| URL  | Adresa URL centra událostí \<ve formuláři event-hubs-namespace\>.servicebus.windows.net/\<název event-hub-name\>.          |
| SharedAccessKeyName | Název zásady sdíleného přístupu pro centrum událostí, které má alespoň **oprávnění Odeslat.** |
| SharedAccessKey     | Primární nebo sekundární klíč ze zásad y sdíleného přístupu pro centrum událostí. |

Příkladveřejné veřejné a soukromé konfigurace jsou uvedeny níže. Jedná se o minimální konfiguraci s jedním čítačem výkonu a protokolem událostí, které ilustrují, jak konfigurovat a používat jímka dat centra událostí. Složitější příklad najdete [v tématu schéma konfigurace diagnostiky Azure.](diagnostics-extension-schema-windows.md)

### <a name="public-configuration"></a>Veřejná konfigurace

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120
        },
        "PerformanceCounters": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
            "PerformanceCounterConfiguration": [
                {
                    "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                    "sampleRate": "PT3M"
                }
            ]
        },
        "WindowsEventLog": {
            "scheduledTransferPeriod": "PT1M",
            "sinks": "myEventHub",
                "DataSource": [
                {
                    "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                }
            ]
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "myEventHub",
                    "EventHub": {
                        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                        "SharedAccessKeyName": "SendRule"
                    }
                }
            ]
        }
    },
    "StorageAccount": "mystorageaccount",
}
```


### <a name="private-configuration"></a>Soukromá konfigurace

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "{base64 encoded key}",
    "storageAccountEndPoint": "https://core.windows.net",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```



## <a name="configuration-options"></a>Možnosti konfigurace
Chcete-li odeslat data do jímky dat, zadejte atribut **jímky** v uzlu zdroje dat. Kde umístíte **jímky** atribut určuje rozsah přiřazení. V následujícím příkladu je atribut **jímky** definován uzlu **PerformanceCounters,** který způsobí, že všechny podřízené čítače výkonu budou odeslány do centra událostí.

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "MyEventHub",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```


V následujícím příkladu je atribut **jímky** použit přímo na tři čítače, které způsobí, že do centra událostí budou odeslány pouze tyto čítače výkonu. 

```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "MyEventHub"
        }
    ]
}
```

## <a name="validating-configuration"></a>Ověření konfigurace
Můžete použít různé metody k ověření, že data jsou odesílána do centra událostí. ne přímočará metoda je použití zachycení centra událostí, jak je popsáno v [události capture prostřednictvím Azure Event Hubs v Azure Blob Storage nebo Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Poradce při potížích s propady centra událostí

- Podívejte se na tabulku Úložiště Azure **WADDiagnosticInfrastructureLogsTable,** která obsahuje protokoly a chyby pro samotnou diagnostiku Azure. Jednou z možností je použití nástroje, jako je [Například Průzkumník úložiště Azure](https://www.storageexplorer.com) pro připojení k tomuto účtu úložiště, zobrazení této tabulky a přidání dotazu pro časové razítko za posledních 24 hodin. Pomocí tohoto nástroje můžete exportovat soubor .csv a otevřít jej v aplikaci, jako je například aplikace Microsoft Excel. Aplikace Excel usnadňuje vyhledávání řetězců volající karty, jako je například **EventHubs**, aby zjistila, jaká chyba je hlášena.  

- Zkontrolujte, zda je centrum událostí úspěšně zřízeno. Všechny informace o připojení v části **PrivateConfig** konfigurace musí odpovídat hodnotám vašeho prostředku, jak je vidět na portálu. Ujistěte se, že máte definované zásady SAS *(SendRule* v příkladu) na portálu a že *oprávnění Odeslat* je uděleno.  

## <a name="next-steps"></a>Další kroky

* [Přehled centra událostí](../../event-hubs/event-hubs-about.md)
* [Vytvoření centra událostí](../../event-hubs/event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png




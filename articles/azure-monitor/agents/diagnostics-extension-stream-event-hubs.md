---
title: Odeslání dat z rozšíření Windows Azure Diagnostics do Azure Event Hubs
description: Nakonfigurujte diagnostické rozšíření v Azure Monitor, aby se odesílala data do centra událostí Azure, abyste je mohli přesměrovat do umístění mimo Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/18/2020
ms.openlocfilehash: 19c39632a1ed040636372d6bad53bf6859960b94
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732033"
---
# <a name="send-data-from-windows-azure-diagnostics-extension-to-azure-event-hubs"></a>Odeslání dat z rozšíření Windows Azure Diagnostics do Azure Event Hubs
Rozšíření Azure Diagnostics je agent v Azure Monitor, který shromažďuje data monitorování z hostovaného operačního systému a úloh virtuálních počítačů Azure a dalších výpočetních prostředků. Tento článek popisuje, jak odeslat data z diagnostického rozšíření Windows Azure (WAD) do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) , abyste mohli přesměrovat do umístění mimo Azure.

## <a name="supported-data"></a>Podporovaná data

Data shromážděná z hostovaného operačního systému, který je možné odeslat do Event Hubs, zahrnují následující. Do Event Hubs nelze odeslat další zdroje dat shromážděné službou WAD, včetně protokolů IIS a výpisů stavu systému.

* Události Trasování událostí pro Windows
* Čítače výkonu
* Protokoly událostí systému Windows, včetně protokolů aplikace v protokolu událostí systému Windows
* Protokolů infrastruktury Azure Diagnostics

## <a name="prerequisites"></a>Požadavky

* Windows Diagnostics Extension 1,6 nebo vyšší. V tématu [Azure Diagnostics verze schématu konfigurace rozšíření a historie](diagnostics-extension-versions.md) Historie verzí a [Azure Diagnostics rozšíření](diagnostics-extension-overview.md) pro podporované prostředky.
* Obor názvů Event Hubs musí být vždy zřízen. Podrobnosti najdete v tématu [Začínáme s Event Hubs](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) .


## <a name="configuration-schema"></a>Schéma konfigurace
V tématu [instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)](diagnostics-extension-windows-install.md) najdete různé možnosti pro povolení a konfiguraci rozšíření diagnostiky a [schématu konfigurace Azure Diagnostics](diagnostics-extension-schema-windows.md) pro referenci schématu konfigurace. Ve zbývající části tohoto článku se dozvíte, jak tuto konfiguraci použít k posílání dat do centra událostí. 

Azure Diagnostics vždy odesílá protokoly a metriky do účtu Azure Storage. Můžete nakonfigurovat jednu nebo více *datových umyvadel* , které odesílají data do dalších umístění. Každá jímka je definována v [elementu SinksConfig](diagnostics-extension-schema-windows.md#sinksconfig-element) veřejné konfigurace s citlivými informacemi v privátní konfiguraci. Tato konfigurace pro centra událostí používá hodnoty v následující tabulce.

| Vlastnost | Popis |
|:---|:---|
| Název | Popisný název jímky. Používá se v konfiguraci k určení zdrojů dat, které se mají odeslat do jímky. |
| URL  | Adresa URL centra událostí ve formátu \<event-hubs-namespace\> . ServiceBus.Windows.NET/ \<event-hub-name\> .          |
| SharedAccessKeyName | Název zásady sdíleného přístupu pro centrum událostí, která má aspoň autoritu pro **odesílání** . |
| SharedAccessKey     | Primární nebo sekundární klíč ze zásad sdíleného přístupu pro centrum událostí. |

Příklad veřejné a privátní konfigurace jsou uvedeny níže. Jedná se o minimální konfiguraci s jedním čítačem výkonu a protokolem událostí, který ilustruje, jak nakonfigurovat a používat datovou jímku centra událostí. Složitější příklad najdete v tématu [schéma konfigurace Azure Diagnostics](diagnostics-extension-schema-windows.md) .

### <a name="public-configuration"></a>Veřejná konfigurace

```JSON
{
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 5120,
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
            }
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


### <a name="private-configuration"></a>Privátní konfigurace

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
Chcete-li odeslat data do datové jímky, zadáte atribut **jímky** v uzlu zdroje dat. Kam umístíte atribut **jímky** , určuje rozsah přiřazení. V následujícím příkladu je atribut **jímky** definován pro uzel **čítače výkonu** , což způsobí, že všechny podřízené čítače výkonu budou odeslány do centra událostí.

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


V následujícím příkladu je atribut **jímky** použit přímo na tři čítače, což způsobí, že se do centra událostí odesílají jenom tyto čítače výkonu. 

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

## <a name="validating-configuration"></a>Ověřování konfigurace
K ověření, že se data odesílají do centra událostí, můžete použít celou řadu metod. Ne jednoduchá metoda je použití Event Hubsho zachycení, jak je popsáno v tématu [zachycení událostí prostřednictvím azure Event Hubs v azure BLOB Storage nebo Azure Data Lake Storage](../../event-hubs/event-hubs-capture-overview.md). 


## <a name="troubleshoot-event-hubs-sinks"></a>Řešení potíží s Event Hubsmi jímkami

- Podívejte se na Azure Storage tabulce **WADDiagnosticInfrastructureLogsTable** , která obsahuje protokoly a chyby pro Azure Diagnostics sebe sama. Jednou z možností je použít pro připojení k tomuto účtu úložiště nástroj, například [Průzkumník služby Azure Storage](https://www.storageexplorer.com) , zobrazit tuto tabulku a přidat dotaz pro časové razítko za posledních 24 hodin. Pomocí tohoto nástroje můžete exportovat soubor. csv a otevřít ho v aplikaci, jako je Microsoft Excel. Aplikace Excel usnadňuje hledání řetězců volacích karet, jako je například **EventHubs**, k zobrazení informace o tom, jaká chyba je hlášena.  

- Ověřte, že se vaše centrum událostí úspěšně zřídilo. Všechny informace o připojení v části **PrivateConfig** konfigurace se musí shodovat s hodnotami prostředku, jak je vidět na portálu. Ujistěte se, že jste na portálu definovali zásadu SAS (*SendRule* ) a že je udělené oprávnění *Odeslat* .  

## <a name="next-steps"></a>Další kroky

* [Přehled Event Hubs](../../event-hubs/event-hubs-about.md)
* [Vytvoření centra událostí](../../event-hubs/event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](../../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png




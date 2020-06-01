---
title: Připojení zdrojů dat prostřednictvím Logstash ke službě Azure Sentinel | Microsoft Docs
description: Naučte se používat Logstash k posílání protokolů z externích zdrojů dat do Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: 79d29ef228fc27655da30edbeb64abcb01e45d5e
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2020
ms.locfileid: "84237175"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Použití Logstash k připojení zdrojů dat k Sentinel Azure

> [!IMPORTANT]
> Přijímání dat pomocí modulu plug-in Logstash Output je momentálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pomocí nového výstupního modulu plug-in Azure Sentinel pro **modul pro shromažďování dat Logstash**teď můžete poslat libovolný typ protokolu prostřednictvím Logstash přímo do vašeho pracovního prostoru Log Analytics ve službě Azure Sentinel. Vaše protokoly se odešlou do vlastní tabulky, kterou budete definovat pomocí modulu plug-in výstup.

Další informace o práci s modulem pro shromažďování dat Logstash najdete v tématu [Začínáme s Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

## <a name="overview"></a>Přehled

### <a name="architecture-and-background"></a>Architektura a pozadí

   ![Architektura Logstash](./media/connect-logstash/logstash-architecture.png)

Modul Logstash se skládá ze tří součástí:

- Vstupní moduly plug-in – přizpůsobená kolekce dat z různých zdrojů
- Filtrovat moduly plug-in – manipulace a normalizace dat podle zadaných kritérií
- Výstupní moduly plug-in – přizpůsobené odesílání shromážděných a zpracovaných dat do různých cílů

> [!NOTE]
> Sentinel Azure podporuje pouze vlastní dodaný výstupní modul plug-in. Nepodporuje výstupní moduly plug-in třetích stran pro službu Azure Sentinel nebo jakýkoli jiný modul plug-in Logstash jakéhokoli typu.

Modul plug-in Azure Sentinel Output pro Logstash odesílá data ve formátu JSON do vašeho pracovního prostoru Log Analytics pomocí Log Analytics REST API kolekce dat HTTP. Data se ingestují do vlastních protokolů.

- [Přečtěte si další informace o REST API Log Analytics](https://docs.microsoft.com/rest/api/loganalytics/create-request).
- [Přečtěte si další informace o vlastních protokolech](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-custom-logs).

## <a name="installing-and-configuring-the-azure-sentinel-output-plugin-in-logstash"></a>Instalace a konfigurace modulu plug-in Azure Sentinel pro výstup v Logstash

1. **Instalace**

    Modul plug-in Azure Sentinel Output je k dispozici v kolekci Logstash.

    - Pokud chcete nainstalovat modul plug-in ***Microsoft-Logstash-Output-Azure-loganalytics*** , postupujte podle pokynů v dokumentu Logstash [Working with plugins](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) .
    - Pokud váš systém Logstash nemá přístup k Internetu, připravte a použijte offline balíček modulu plug-in pomocí pokynů v dokumentu [správy modulu plug-in](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) Logstash. (To bude vyžadovat, abyste vytvořili jiný Logstash systém s přístupem k Internetu.)

1. **Konfigurace**

    Pomocí informací ve struktuře Logstash dokumentu [konfiguračního souboru](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) přidejte modul plug-in Azure Sentinel Output do konfigurace pomocí následujících klíčů a hodnot (viz Správná syntaxe konfiguračního souboru pod tabulkou):

    | **Název pole** | **Datový typ** | **Popis** |
    |----------------|---------------|-----------------|
    | `workspace_id` | řetězec | Zadejte identifikátor GUID ID vašeho pracovního prostoru. |
    | `workspace_key` | řetězec | Zadejte identifikátor GUID primárního klíče pracovního prostoru. |
    | `custom_log_table_name` | řetězec | Nastavte název tabulky, do které budou přijímány protokoly. Pro každý výstupní modul plug-in se dá nakonfigurovat jenom jeden název tabulky. V části **protokoly**v **tabulkách** v kategorii **vlastní protokoly** se jako přípona zobrazí tabulka protokolu Azure Sentinel `_CL` . |
    | `endpoint` | řetězec | Volitelné pole. Ve výchozím nastavení je to koncový bod Log Analytics. Pomocí tohoto pole můžete nastavit alternativní koncový bod. |
    | `time_generated_field` | řetězec | Volitelné pole. Tato vlastnost přepisuje výchozí pole **TimeGenerated** v Log Analytics. Zadejte název pole časového razítka ve zdroji dat. Data v poli musí odpovídat formátu ISO 8601 ( `YYYY-MM-DDThh:mm:ssZ` ). |
    | `key_names` | pole | Zadejte seznam Log Analytics výstupních polí schématu. Každá položka seznamu by měla být uzavřena do jednoduchých uvozovek a položek oddělených čárkami a celým seznamem uzavřeným v hranatých závorkách. Viz následující příklad. |
    | `plugin_flush_interval` | číslo | Volitelné pole. Nastavte pro definování maximálního intervalu (v sekundách) mezi přenosy zpráv, které se mají Log Analytics. Výchozí hodnota je 5. |
    | `amount_resizing` | Boolean | True nebo false. Povolte nebo zakažte mechanismus automatického škálování, který upraví velikost vyrovnávací paměti zpráv podle objemu přijatých dat protokolu. |
    | `max_items` | číslo | Volitelné pole. Platí pouze v případě, že je `amount_resizing` nastavena hodnota "NEPRAVDA". Použijte k nastavení limitu velikosti vyrovnávací paměti zpráv (v záznamech). Výchozí hodnota je 2000.  |

    **Ukázková konfigurace**

        input {
            tcp {
                port => 514
                type => syslog
            }
        }

        filter {
            grok {
                match => { "message" => "<%{NUMBER:PRI}>1 (?<TIME_TAG>[0-9]{4}-[0-9]{1,2}-[0-9]{1,2}T[0-9]{1,2}:[0-9]{1,2}:[0-9]{1,2})[^ ]* (?<HOSTNAME>[^ ]*) %{GREEDYDATA:MSG}" }
            }
        }

        output {
            logstash-output-azure-loganalytics {
                workspace_id => "<WS_ID>"
                workspace_key => "${WS_KEY}"
                custom_log_table_name => "logstashCustomTable"
                key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
                plugin_flush_interval => 5
            }
        } 

    > [!NOTE]
    > Další informace o vnitřních pracovních sesledováních, konfiguraci a nastavení výkonu najdete na [GitHubu](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) výstupního modulu plug-in.

1. **Restartovat Logstash**

1. **Zobrazit příchozí protokoly v Azure Sentinel**

    1. Ověřte, že se zprávy odesílají do výstupního modulu plug-in.

    1. V nabídce navigace v Azure Sentinel klikněte na **protokoly**. V záhlaví **tabulky** rozbalte kategorii **vlastní protokoly** . Vyhledejte a klikněte na název tabulky, kterou jste zadali (s `_CL` příponou) v konfiguraci.

        ![Vlastní protokoly Logstash](./media/connect-logstash/logstash-custom-logs-menu.png)

    1. Chcete-li zobrazit záznamy v tabulce, proveďte dotaz na tabulku pomocí názvu tabulky jako schématu.

        ![Dotaz na vlastní protokoly Logstash](./media/connect-logstash/logstash-custom-logs-query.png)

## <a name="monitor-output-plugin-audit-logs"></a>Monitorování protokolů auditu výstupního modulu plug-in

Pokud chcete monitorovat připojení a aktivitu modulu plug-in Azure Sentinel Output, povolte příslušný soubor protokolu Logstash. Podívejte se na dokument [rozložení adresáře Logstash](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) pro umístění souboru protokolu.

Pokud nevidíte žádná data v tomto souboru protokolu, vygenerujte a odešlete některé události místně (prostřednictvím vstupních a filtrovacích modulů plug-in) a ujistěte se, že výstupní modul plug-in přijímá data. Azure Sentinel bude podporovat jenom problémy týkající se výstupního modulu plug-in.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat Logstash k připojení externích zdrojů dat ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte s detekcí hrozeb pomocí služby Azure Sentinel a pomocí [předdefinovaných](tutorial-detect-threats-built-in.md) nebo [vlastních](tutorial-detect-threats-custom.md) pravidel.
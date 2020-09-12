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
ms.date: 09/10/2020
ms.author: yelevin
ms.openlocfilehash: 7fe47289dcc6b6d6af4d13b36b5c3b1dae3baaf5
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89663907"
---
# <a name="use-logstash-to-connect-data-sources-to-azure-sentinel"></a>Použití Logstash k připojení zdrojů dat k Sentinel Azure

> [!IMPORTANT]
> Přijímání dat pomocí modulu plug-in Logstash Output je momentálně ve verzi Public Preview. Tato funkce se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Pomocí nového výstupního modulu plug-in Azure Sentinel pro **modul pro shromažďování dat Logstash**teď můžete poslat libovolný typ protokolu prostřednictvím Logstash přímo do vašeho pracovního prostoru Log Analytics ve službě Azure Sentinel. Vaše protokoly se odešlou do vlastní tabulky, kterou budete definovat pomocí modulu plug-in výstup.

Další informace o práci s modulem pro shromažďování dat Logstash najdete v tématu [Začínáme s Logstash](https://www.elastic.co/guide/en/logstash/current/getting-started-with-logstash.html).

## <a name="overview"></a>Přehled

### <a name="architecture-and-background"></a>Architektura a pozadí

![Diagram architektury dočasného ukládání protokolů](./media/connect-logstash/logstash-architecture.png)

Modul Logstash se skládá ze tří součástí:

- Vstupní moduly plug-in: přizpůsobená kolekce dat z různých zdrojů.
- Filtrovat moduly plug-in: manipulace a normalizace dat podle zadaných kritérií.
- Výstupní moduly plug-in: přizpůsobení odesílání shromážděných a zpracovaných dat do různých cílů.

> [!NOTE]
> Sentinel Azure podporuje pouze vlastní dodaný výstupní modul plug-in. Nepodporuje výstupní moduly plug-in třetích stran pro službu Azure Sentinel nebo jakýkoli jiný modul plug-in Logstash jakéhokoli typu.

Modul plug-in Azure Sentinel Output pro Logstash odesílá data ve formátu JSON do vašeho pracovního prostoru Log Analytics pomocí Log Analytics REST API kolekce dat HTTP. Data se ingestují do vlastních protokolů.

- Přečtěte si další informace o [REST API Log Analytics](https://docs.microsoft.com/rest/api/loganalytics/create-request).
- Přečtěte si další informace o [vlastních protokolech](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-custom-logs).

## <a name="deploy-the-azure-sentinel-output-plugin-in-logstash"></a>Nasazení modulu plug-in Azure Sentinel Output v Logstash

### <a name="step-1-installation"></a>Krok 1: instalace

Modul plug-in Azure Sentinel Output je k dispozici v kolekci Logstash.

- Pokud chcete nainstalovat modul plug-in ***Microsoft-Logstash-Output-Azure-loganalytics*** , postupujte podle pokynů v dokumentu Logstash [Working with plugins](https://www.elastic.co/guide/en/logstash/current/working-with-plugins.html) .
   
- Pokud váš systém Logstash nemá přístup k Internetu, připravte a použijte offline balíček modulu plug-in pomocí pokynů v dokumentu [správy modulu plug-in](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html) Logstash. (To bude vyžadovat, abyste vytvořili jiný Logstash systém s přístupem k Internetu.)

### <a name="step-2-configuration"></a>Krok 2: Konfigurace

Použijte informace ze struktury Logstash dokumentu [konfiguračního souboru](https://www.elastic.co/guide/en/logstash/current/configuration-file-structure.html) a přidejte do konfigurace modul plug-in Azure Sentinel Output s následujícími klíči a hodnotami. (Správná syntaxe konfiguračního souboru se zobrazí za tabulkou.)

| Název pole | Datový typ | Popis |
|----------------|---------------|-----------------|
| `workspace_id` | řetězec | Zadejte identifikátor GUID ID vašeho pracovního prostoru. * |
| `workspace_key` | řetězec | Zadejte identifikátor GUID primárního klíče pracovního prostoru. * |
| `custom_log_table_name` | řetězec | Nastavte název tabulky, do které budou přijímány protokoly. Pro každý výstupní modul plug-in se dá nakonfigurovat jenom jeden název tabulky. V části **protokoly**v **tabulkách** v kategorii **vlastní protokoly** se jako přípona zobrazí tabulka protokolu Azure Sentinel `_CL` . |
| `endpoint` | řetězec | Volitelné pole. Ve výchozím nastavení je to koncový bod Log Analytics. Pomocí tohoto pole můžete nastavit alternativní koncový bod. |
| `time_generated_field` | řetězec | Volitelné pole. Tato vlastnost přepisuje výchozí pole **TimeGenerated** v Log Analytics. Zadejte název pole časového razítka ve zdroji dat. Data v poli musí odpovídat formátu ISO 8601 ( `YYYY-MM-DDThh:mm:ssZ` ). |
| `key_names` | array | Zadejte seznam Log Analytics výstupních polí schématu. Každá položka seznamu by měla být uzavřena do jednoduchých uvozovek a položek oddělených čárkami a celým seznamem uzavřeným v hranatých závorkách. Viz následující příklad. |
| `plugin_flush_interval` | číslo | Volitelné pole. Nastavte pro definování maximálního intervalu (v sekundách) mezi přenosy zpráv, které se mají Log Analytics. Výchozí hodnota je 5. |
    | `amount_resizing` | boolean | True nebo false Povolte nebo zakažte mechanismus automatického škálování, který upraví velikost vyrovnávací paměti zpráv podle objemu přijatých dat protokolu. |
| `max_items` | číslo | Volitelné pole. Platí pouze v případě, že je `amount_resizing` nastavena hodnota "NEPRAVDA". Použijte k nastavení limitu velikosti vyrovnávací paměti zpráv (v záznamech). Výchozí hodnota je 2000.  |

\* ID a primární klíč pracovního prostoru můžete najít v prostředku pracovního prostoru v části **Správa agentů**.

#### <a name="sample-configurations"></a>Ukázkové konfigurace

Tady je několik ukázkových konfigurací, které používají několik různých možností.

- Základní konfigurace, která používá vstupní kanál typu \ ráz:

   ```ruby
    input {
        beats {
            port => "5044"
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
    
- Základní konfigurace, která používá vstupní kanál protokolu TCP:

   ```ruby
    input {
        tcp {
            port => "514"
            type => syslog #optional, will effect log type in table
        }
    }
    filter {
    }
    output {
        microsoft-logstash-output-azure-loganalytics {
          workspace_id => "4g5tad2b-a4u4-147v-a4r7-23148a5f2c21" # <your workspace id>
          workspace_key => "u/saRtY0JGHJ4Ce93g5WQ3Lk50ZnZ8ugfd74nk78RPLPP/KgfnjU5478Ndh64sNfdrsMni975HJP6lp==" # <your workspace key>
          custom_log_table_name => "tableName"
        }
    }
   ```
  
- Pokročilá konfigurace:

   ```ruby    
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
        microsoft-logstash-output-azure-loganalytics {
            workspace_id => "<WS_ID>"
            workspace_key => "${WS_KEY}"
            custom_log_table_name => "logstashCustomTable"
            key_names => ['PRI','TIME_TAG','HOSTNAME','MSG']
            plugin_flush_interval => 5
        }
    } 
   ```

   > [!NOTE]
   > Podívejte se na [úložiště GitHubu](https://github.com/Azure/Azure-Sentinel/tree/master/DataConnectors/microsoft-logstash-output-azure-loganalytics) výstupního modulu plug-in, kde najdete další informace o vnitřních pracovních postupech, konfiguraci a nastavení výkonu.

### <a name="step-3-restart-logstash"></a>Krok 3: restartování Logstash

### <a name="step-4-view-incoming-logs-in-azure-sentinel"></a>Krok 4: zobrazení příchozích protokolů v ověřovacím protokolu Azure

1. Ověřte, že se zprávy odesílají do výstupního modulu plug-in.

1. V nabídce navigace v Azure Sentinel klikněte na **protokoly**. V záhlaví **tabulky** rozbalte kategorii **vlastní protokoly** . Vyhledejte a klikněte na název tabulky, kterou jste zadali (s `_CL` příponou) v konfiguraci.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-menu.png" alt-text="Snímek obrazovky s vlastními protokoly pro dočasné ukládání protokolu":::

1. Chcete-li zobrazit záznamy v tabulce, proveďte dotaz na tabulku pomocí názvu tabulky jako schématu.

   :::image type="content" source="./media/connect-logstash/logstash-custom-logs-query.png" alt-text="Snímek obrazovky s dotazem vlastního protokolu pro dočasné ukládání protokolů":::

## <a name="monitor-output-plugin-audit-logs"></a>Monitorování protokolů auditu výstupního modulu plug-in

Pokud chcete monitorovat připojení a aktivitu modulu plug-in Azure Sentinel Output, povolte příslušný soubor protokolu Logstash. Prohlédněte si dokument [rozložení adresáře Logstash](https://www.elastic.co/guide/en/logstash/current/dir-layout.html#dir-layout) pro umístění souboru protokolu.

Pokud nevidíte žádná data v tomto souboru protokolu, vygenerujte a odešlete některé události místně (prostřednictvím vstupních a filtrovacích modulů plug-in) a ujistěte se, že výstupní modul plug-in přijímá data. Azure Sentinel bude podporovat jenom problémy týkající se výstupního modulu plug-in.

## <a name="next-steps"></a>Další kroky

V tomto dokumentu jste zjistili, jak používat Logstash k připojení externích zdrojů dat ke službě Azure Sentinel. Další informace o Sentinel Azure najdete v následujících článcích:
- Naučte se [, jak získat přehled o vašich datech a potenciálních hrozbách](quickstart-get-visibility.md).
- Začněte s detekcí hrozeb pomocí služby Azure Sentinel a pomocí [předdefinovaných](tutorial-detect-threats-built-in.md) nebo [vlastních](tutorial-detect-threats-custom.md) pravidel.

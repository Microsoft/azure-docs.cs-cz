---
title: Ingestování dat z Logstash do Průzkumníka dat Azure
description: V tomto článku se dozvíte, jak ingestovat (načíst) data do Průzkumníka dat Azure z Logstash
author: tamirkamara
ms.author: takamara
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 86f6732cbf2409d3c79a3d7709100e8af24988a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66494540"
---
# <a name="ingest-data-from-logstash-to-azure-data-explorer"></a>Ingestování dat z Logstash do Průzkumníka dat Azure

[Logstash](https://www.elastic.co/products/logstash) je kanál pro zpracování dat na straně serveru s otevřeným zdrojovým kódem, který současně ingestuje data z mnoha zdrojů, transformuje data a poté odesílá data do vašeho oblíbeného "skrýše". V tomto článku budete odesílat tato data do Průzkumníka dat Azure, což je rychlá a vysoce škálovatelná služba pro zkoumání dat pro data protokolu a telemetrie. Nejprve vytvoříte tabulku a mapování dat v testovacím clusteru a pak nasměrujete Logstash k odeslání dat do tabulky a ověření výsledků.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ho nemáte, vytvořte si [bezplatný účet Azure,](https://azure.microsoft.com/free/) než začnete.
* Testovací cluster [a databáze](create-cluster-database-portal.md) Průzkumníka dat Azure
* Logstash verze 6+ [Pokyny k instalaci](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)

## <a name="create-a-table"></a>Vytvoření tabulky

Po vytvoření clusteru a databáze je čas vytvořit tabulku.

1. Chcete-li vytvořit tabulku, spusťte v okně dotazu databáze následující příkaz:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Spuštěním následujícího příkazu ověřte, zda byla vytvořena nová tabulka `logs` a zda je prázdná:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Vytvoření mapování

Mapování používá Azure Data Explorer k transformaci příchozích dat do schématu cílové tabulky. Následující příkaz vytvoří nové `basicmsg` mapování s názvem, který extrahuje vlastnosti `path` z příchozí json, jak je uvedeno v a výstupy je do `column`.

V okně dotazu spusťte následující příkaz:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Instalace výstupního pluginu Logstash

Výstupní modul plug-in Logstash komunikuje s Průzkumníkem dat Azure a odesílá data do služby.
Spusťte následující příkaz v kořenovém adresáři Logstash a nainstalujte plugin:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Konfigurace logstash pro generování ukázkové datové sady

Logstash můžete generovat ukázkové události, které lze použít k testování kanálu end-to-end.
Pokud již používáte Logstash a máte přístup k vlastnímu datovému proudu událostí, přejděte k další části. 

> [!NOTE]
> Pokud používáte vlastní data, změňte tabulku a mapování objektů definovaných v předchozích krocích.

1. Upravte nový textový soubor, který bude obsahovat požadovaná nastavení kanálu (pomocí vi):

    ```sh
    vi test.conf
    ```

1. Vložte následující nastavení, která řeknou logstash generovat 1000 testovacích událostí:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Tato konfigurace také `stdin` obsahuje vstupní plugin, který vám umožní psát více zpráv sami (nezapomeňte použít *Enter* k jejich odeslání do kanálu).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Konfigurace logstash pro odesílání dat do Průzkumníka dat Azure

Vložte následující nastavení do stejného konfiguračního souboru, který byl použit v předchozím kroku. Nahraďte všechny zástupné symboly příslušnými hodnotami pro nastavení. Další informace naleznete [v tématu Vytvoření aplikace AAD](/azure/kusto/management/access-control/how-to-provision-aad-app). 

```ruby
output {
    kusto {
            path => "/tmp/kusto/%{+YYYY-MM-dd-HH-mm-ss}.txt"
            ingest_url => "https://ingest-<cluster name>.kusto.windows.net/"
            app_id => "<application id>"
            app_key => "<application key/secret>"
            app_tenant => "<tenant id>"
            database => "<database name>"
            table => "<target table>" # logs as defined above
            mapping => "<mapping name>" # basicmsg as defined above
    }
}
```

| Název parametru | Popis |
| --- | --- |
| **Cestu** | Modul plug-in Logstash zapisuje události do dočasných souborů před jejich odesláním do Průzkumníka dat Azure. Tento parametr zahrnuje cestu, kde by měly být zapsány soubory a časový výraz pro střídání souborů k aktivaci nahrávání do služby Azure Data Explorer.|
| **ingest_url** | Koncový bod Kusto pro komunikaci související s ingestováním.|
| **app_id**, **app_key**a **app_tenant**| Přihlašovací údaje potřebné pro připojení k Průzkumníku dat Azure. Nezapomeňte použít aplikaci s oprávněními ingestování. |
| **Databáze**| Název databáze pro umístění událostí. |
| **Tabulka** | Název cílové tabulky pro umístění událostí. |
| **mapování** | Mapování se používá k mapování řetězce json příchozí události do správného formátu řádku (definuje, která vlastnost přejde do kterého sloupce). |

## <a name="run-logstash"></a>Spustit logstash

Nyní jsme připraveni spustit Logstash a otestovat naše nastavení.

1. V kořenovém adresáři Logstash spusťte následující příkaz:

    ```sh
    bin/logstash -f test.conf
    ```

    Měli byste vidět informace vytištěné na obrazovce a pak 1000 zpráv generovaných naší ukázkovou konfigurací. V tomto okamžiku můžete také zadat další zprávy ručně.

1. Po několika minutách spusťte následující dotaz Průzkumníka dat a zoněkolikáte zprávy v definované tabulce:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Chcete-li ukončit protokol Logstash, vyberte Ctrl+C.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li tabulku vyčistit, spusťte v databázi `logs` následující příkaz:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Další kroky

* [Zápis dotazů](write-queries.md)
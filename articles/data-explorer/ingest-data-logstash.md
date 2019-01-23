---
title: 'Rychlý start: Ingestování dat z Logstash do Průzkumníku dat Azure'
description: V tomto rychlém startu se dozvíte, jak se přijmout data (načíst) do Průzkumníku dat Azure z Logstash
services: data-explorer
author: tamirkamara
ms.author: takamara
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 1/14/2019
ms.openlocfilehash: 4b5934123f5bb7fa9eaa2c67c212bcef2a5de0aa
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479455"
---
# <a name="quickstart-ingest-data-from-logstash-to-azure-data-explorer"></a>Rychlý start: Ingestování dat z Logstash do Průzkumníku dat Azure

[Logstash](https://www.elastic.co/products/logstash) je open source, kanál zpracování dat na straně serveru, který ingestuje data z mnoha zdrojů současně, transformuje data a posílá ji do vašich oblíbených "dočasně". V tomto rychlém startu budete odesílat data do Průzkumníku dat Azure, jde o službu zkoumání dat rychlé a vysoce škálovatelné pro data protokolů a telemetrie. Budete úvodnímu vytvoření tabulky a mapování dat v testu clusteru a potom směrovat Logstash pro odesílání dat do tabulky a ověřte výsledky.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ho nemáte, vytvořte [bezplatný účet Azure](https://azure.microsoft.com/free/) předtím, než začnete.
* Průzkumník dat Azure [testování clusteru a databáze](create-cluster-database-portal.md)
* Verze 6 a Logstash [pokyny k instalaci](https://www.elastic.co/guide/en/logstash/current/installing-logstash.html)

## <a name="create-a-table"></a>Vytvoření tabulky

Po vytvoření clusteru a databáze, je čas vytvořit tabulku.

1. Spusťte následující příkaz v okně dotazu databázi vytvořte tabulku:

    ```Kusto
    .create table logs (timestamp: datetime, message: string)
    ```

1. Spuštění následujícího příkazu ověřte, že nová tabulka `logs` se vytvořila a že je prázdný:
    ```Kusto
    logs
    | count
    ```

## <a name="create-a-mapping"></a>Vytvořte mapování

Mapování používá Průzkumník dat Azure pro transformaci příchozích dat do cílové schéma tabulky. Následující příkaz vytvoří nové mapování s názvem `basicmsg` , který extrahuje z příchozí json, jak je uvedeno ve vlastnosti `path` a uloží je `column`.

V okně dotazu spusťte následující příkaz:

```Kusto
.create table logs ingestion json mapping 'basicmsg' '[{"column":"timestamp","path":"$.@timestamp"},{"column":"message","path":"$.message"}]'
```

## <a name="install-the-logstash-output-plugin"></a>Nainstalujte modul plug-in výstupu

Modul plug-in výstup komunikuje pomocí Průzkumníku dat Azure a odesílá data do služby.
Spuštěním následujícího příkazu v kořenovém adresáři Logstash instalace modulu plug-in:

```sh
bin/logstash-plugin install logstash-output-kusto
```

## <a name="configure-logstash-to-generate-a-sample-dataset"></a>Konfigurace Logstash pro generování ukázkovou datovou sadou

Ukázkové události, které můžete použít k testování začátku do konce profilace můžete vygenerovat Logstash.
Pokud už používáte Logstash a mají přístup k vlastním datového proudu událostí, přejděte k další části. 

> [!NOTE]
> Pokud používáte vlastní data, změňte mapování tabulky a objekty, které jsou definované v předchozích krocích.

1. Upravte nový textový soubor obsahující nastavení požadovaná kanálu (pomocí editoru vi):

    ```sh
    vi test.conf
    ```

1. Vložte následující nastavení, která vám sdělí Logstash generovat události 1000 testu:

    ```ruby
    input {
        stdin { }
        generator {
            message => "Test Message 123"
            count => 1000
        }
    }
    ```

Tato konfigurace zahrnuje také `stdin` vstupu modulu plug-in, který vám umožní zapsat další zprávy sami (nezapomeňte použít *Enter* k odeslání do kanálu).

## <a name="configure-logstash-to-send-data-to-azure-data-explorer"></a>Konfigurace Logstash pro odesílání dat do Průzkumníku dat Azure

Vložte následující nastavení na stejný konfigurační soubor jako v předchozím kroku. Všechny zástupné znaky nahraďte příslušné hodnoty pro nastavení. Další informace najdete v tématu [vytvořit aplikaci AAD](/azure/kusto/management/access-control/how-to-provision-aad-app). 

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
| --- | --- | --- |
| **Cesta** | Modul plug-in zapisuje události do dočasné soubory před jejich odesláním do Průzkumníku dat Azure. Tento parametr obsahuje cestu, kam by měly být zapsány soubory a výrazu času pro rotaci souborů k aktivaci nahrávání ve službě Průzkumník dat Azure.|
| **ingest_url** | Kusto koncový bod pro komunikaci týkající se přijímání.|
| **app_id**, **app_key**, a **app_tenant**| Přihlašovací údaje požadované pro připojení do Průzkumníku dat Azure. Nezapomeňte použít aplikace s oprávněními pro ingestování. |
| **database**| Název databáze umístit události. |
| **Tabulka** | Název cílové tabulky umístit události. |
| **Mapování** | Mapování se používá k mapování příchozího řetězce json událostí do formátu správný řádek (definuje, která vlastnost obsahuje sloupec). |

## <a name="run-logstash"></a>Spusťte Logstash

Nyní jsme připraveni ke spuštění Logstash a naše nastavení testu.

1. V kořenovém adresáři Logstash spusťte následující příkaz:

    ```sh
    bin/logstash -f test.conf
    ```

    Měli byste vidět informace, které jsou zobrazeny na obrazovce a potom 1000 zprávy generované naší ukázkové konfiguraci. V tomto okamžiku můžete také zadat další zprávy ručně.

1. Po několika minutách spusťte následující dotaz Průzkumník dat zobrazíte zprávy v tabulce, které že jste definovali:

    ```Kusto
    logs
    | order by timestamp desc
    ```

1. Vyberte kombinaci kláves Ctrl + C ukončete Logstash.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Spuštěním následujícího příkazu v databázi pro vyčištění `logs` tabulky:

```Kusto
.drop table logs
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zápis dotazů](write-queries.md)
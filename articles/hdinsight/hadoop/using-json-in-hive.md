---
title: Analýza & zpracování JSON pomocí Apache Hive – Azure HDInsight
description: Naučte se používat dokumenty JSON a analyzovat je pomocí Apache Hive v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 1c519533625835677ddae0a274c9ce9f10edc6dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73098000"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Zpracování a analýza dokumentů JSON pomocí Apache Hive v Azure HDInsight

Naučte se zpracovávat a analyzovat soubory JavaScript Object Notation (JSON) pomocí Apache Hive v Azure HDInsight. Tento článek používá následující dokument JSON:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Soubor lze nalézt `wasb://processjson@hditutorialdata.blob.core.windows.net/`na adrese . Další informace o tom, jak používat úložiště objektů blob Azure s HDInsight, najdete [v tématu použití úložiště objektů blob Azure kompatibilního s HDFS s Apache Hadoop ve službě HDInsight](../hdinsight-hadoop-use-blob-storage.md). Soubor můžete zkopírovat do výchozího kontejneru clusteru.

V tomto článku použijete konzolu Apache Hive. Pokyny k otevření konzole Hive najdete v [tématu Použití apache ambari hive view s Apache Hadoop v HDInsight](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>Sloučení dokumentů JSON

Metody uvedené v další části vyžadují, aby se dokument JSON skládal z jednoho řádku. Proto je nutné slono dokumentu JSON na řetězec. Pokud je váš dokument JSON již sloučí, můžete tento krok přeskočit a přejít přímo k další části analýzy dat JSON. Chcete-li slopísem dokumentu JSON, spusťte následující skript:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Nezpracovaný soubor JSON `wasb://processjson@hditutorialdata.blob.core.windows.net/`je umístěn na adrese . Tabulka **StudentsRaw** Hive odkazuje na nezpracovaný dokument JSON, který není srovnaný.

Tabulka **StudentsOneLine** Hive ukládá data ve výchozím souborovém systému HDInsight pod cestou **/json/students/.**

Příkaz **INSERT** naplní tabulku **StudentOneLine** sjednocovanými daty JSON.

Příkaz **SELECT** vrátí pouze jeden řádek.

Zde je výstup příkazu **SELECT:**

![HdInsight sloučí dokument JSON](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Analýza dokumentů JSON v Úlu

Hive poskytuje tři různé mechanismy pro spouštění dotazů na dokumenty JSON, nebo můžete napsat vlastní:

* Použijte get_json_object uživatelem definovanou funkci (UDF).
* Použijte json_tuple UDF.
* Použijte vlastní serializátor/deserializer (SerDe).
* Napište si vlastní UDF pomocí Pythonu nebo jiných jazyků. Další informace o tom, jak spustit vlastní kód Pythonu s Hive, najdete v [tématu Python UDF s Apache Hive a Apache Pig](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Použití get_json_object UDF

Hive poskytuje předdefinovaný UDF s názvem [get_json_object,](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) který může provádět dotazy JSON za běhu. Tato metoda trvá dva argumenty -- název tabulky a název metody, která má složený dokument JSON a pole JSON, které je třeba analyzovat. Podívejme se na příklad, jak tento UDF funguje.

Následující dotaz vrátí jméno a příjmení každého studenta:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Zde je výstup při spuštění tohoto dotazu v okně konzoly:

![Apache Hive získat json objekt UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

Existují omezení get_json_object UDF:

* Vzhledem k tomu, že každé pole v dotazu vyžaduje úpravu dotazu, ovlivňuje výkon.
* **GET\_JSON_OBJECT()** vrátí řetězcovou reprezentaci pole. Chcete-li převést toto pole na pole Hive, musíte použít regulární výrazy k nahrazení hranatých závorek "[" a "]" a potom také musíte volat split, abyste získali pole.

Proto wiki Hive doporučuje používat **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Použití json_tuple UDF

Jiný UDF poskytovaný Hive se nazývá [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), který funguje lépe než [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Tato metoda trvá sadu klíčů a řetězec JSON a vrátí řazené kolekce členů s hodnotami pomocí jedné funkce. Následující dotaz vrátí ID studenta a hodnocení z dokumentu JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Výstup tohoto skriptu v konzole Hive:

![Výsledky dotazu Apache Hive json](./media/using-json-in-hive/hdinsight-json-tuple.png)

Json_tuple UDF používá syntaxi [bočního zobrazení](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) v Hive, která umožňuje řazené kolekce členů json\_vytvořit virtuální tabulku použitím funkce UDT na každý řádek původní tabulky. Komplexní JSONs se příliš těžkopádné, protože opakované použití **laterální pohled**. Kromě toho **JSON_TUPLE** nemůže zpracovat vnořené JSONs.

### <a name="use-a-custom-serde"></a>Použití vlastního SerDe

SerDe je nejlepší volbou pro analýzu vnořených dokumentů JSON. Umožňuje definovat schéma JSON a potom můžete použít schéma k analýzu dokumentů. Pokyny najdete [v tématu Jak používat vlastní JSON SerDe s Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Souhrn

Na závěr typ operátoru JSON v Hive, který zvolíte, závisí na vašem scénáři. Pokud máte jednoduchý dokument JSON a máte k dispozici pouze jedno pole, můžete použít **get_json_object**Hive UDF . Pokud máte více než jeden klíč, na který se můžete podívat, můžete použít **json_tuple**. Pokud máte vnořený dokument, měli byste použít **JSON SerDe**.

## <a name="next-steps"></a>Další kroky

Související články naleznete v:

* [Použití Apache Hive a HiveQL s Apache Hadoop v HDInsight k analýze ukázkového souboru Apache log4j](../hdinsight-use-hive.md)
* [Analýza dat zpoždění letu pomocí interaktivního dotazu v HDInsightu](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analyzujte data z Twitteru pomocí Apache Hive v HDInsightu](../hdinsight-analyze-twitter-data-linux.md)

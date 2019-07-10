---
title: Analýza a zpracování dokumentů JSON pomocí Apache Hive v Azure HDInsight
description: Zjistěte, jak používat dokumentů JSON a analyzovat pomocí Apache Hive v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 5ec766cea2135f7c00df032ad0df4ada033d6293
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461989"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Zpracování a analýza dokumentů JSON pomocí Apache Hive v Azure HDInsight

Zjistěte, jak zpracovávat a analyzovat soubory JavaScript Object Notation (JSON) s použitím Apache Hive v Azure HDInsight. Tento článek používá následující dokument JSON:

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

Soubor můžete najít v `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Další informace o tom, jak používat Azure Blob storage s HDInsight naleznete v tématu [použití HDFS kompatibilního Azure Blob storage s využitím Apache Hadoop v HDInsight](../hdinsight-hadoop-use-blob-storage.md). Zkopírujte soubor do kontejneru výchozí vašeho clusteru.

V tomto článku můžete použít konzolu Apache Hive. Pokyny o tom, jak používat konzolu Hive najdete v tématu [použití Apache Ambari Hive zobrazení s Apache Hadoop v HDInsight](apache-hadoop-use-hive-ambari-view.md).

## <a name="flatten-json-documents"></a>Sloučit dokumentů JSON
Metody uvedené v následující části vyžadovat, že dokument JSON se skládá z jednoho řádku. Ano musíte sloučit dokumentu JSON na řetězec. Pokud už je sloučený dokumentu JSON, můžete tento krok přeskočit a přejít přímo k další části k analýze dat JSON. Pokud chcete sloučit dokumentu JSON, spusťte následující skript:

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

Nezpracovaný soubor JSON se nachází v `wasb://processjson@hditutorialdata.blob.core.windows.net/`. **StudentsRaw** Hive body tabulku nezpracovaných dokumentu JSON, který se sloučí.

**StudentsOneLine** tabulky Hive v HDInsight výchozí systém souborů pod ukládá data **/json/studenty/** cestu.

**Vložit** naplní příkaz **StudentOneLine** tabulku s plochou data JSON.

**Vyberte** příkaz vrátí jenom jeden řádek.

Zde je výstup **vyberte** – příkaz:

![Sloučení dokumentu JSON](./media/using-json-in-hive/flatten.png)

## <a name="analyze-json-documents-in-hive"></a>Analýza dokumentů JSON v Hivu
Hive poskytuje tři různé mechanismy ke spouštění dotazů na dokumenty JSON, nebo napište vlastní:

* Použijte get_json_object uživatelem definované funkce (UDF).
* Použijte json_tuple UDF.
* Použijte vlastní serializátor/deserializátor (SerDe).
* Napište vlastní UDF pomocí Pythonu nebo jiných jazycích. Další informace o způsobu spuštění kódu Pythonu s Hivem najdete v tématu [uživatelsky definované funkce Pythonu s Apache Hivu a Apache Pig] [hdinsight pythonu].

### <a name="use-the-getjsonobject-udf"></a>Použití get_json_object UDF
Hive obsahuje vestavěné UDF volá [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) , který umožňuje dotazování JSON za běhu. Tato metoda přebírá dva argumenty – název tabulky a název metody, která má pole JSON, který je analyzovat a plochá dokumentů JSON. Pojďme se podívat na příklad, který najdete v článku Jak funguje tento UDF.

Následující dotaz vrátí křestní jméno a příjmení pro každého studenta:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Zde je výstup při spuštění tohoto dotazu v okně konzoly:

![get_json_object UDF](./media/using-json-in-hive/getjsonobject.png)

Existují omezení get_json_object UDF:

* Protože každé pole v dotazu vyžaduje reparsing dotazu, ovlivňuje výkon.
* **ZÍSKAT\_JSON_OBJECT()** vrátí řetězcovou reprezentaci pole. Pokud chcete převést toto pole k poli Hive, budete muset použít regulární výrazy k nahrazení hranaté závorky "[" a "]", a pak budete muset volat rozdělení zobrazíte pole.

To je důvod, proč Hive wiki doporučuje, abyste použili json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Použití json_tuple UDF
Jiné UDF poskytované Hive se nazývá [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), který provádí lepší než [get_ json d_ostupné](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Tato metoda přebírá sadu klíčů a řetězec formátu JSON a vrací řazenou kolekci členů hodnot pomocí funkce. Následující dotaz vrátí ID studenta a třída z dokumentu JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Výstup tohoto skriptu v konzole nástroje Hive:

![json_tuple UDF](./media/using-json-in-hive/jsontuple.png)

Json_tuple používá systém souborů UDF [laterální zobrazení](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) syntaxe v poznámce Hive, která umožňuje json\_řazené kolekce členů k vytvoření virtuální tabulky použitím UDT funkce na každý řádek v původní tabulce. Komplexní JSONs být příliš nepraktické z důvodu opakovaného použití **LATERÁLNÍ zobrazení**. Kromě toho **JSON_TUPLE** nemůže zpracovat vnořené JSONs.

### <a name="use-a-custom-serde"></a>Použít vlastní SerDe
SerDe je nejlepší volbou pro potřeby analýzy vnořených dokumentů JSON. Umožňuje definovat schéma JSON a pak můžete použít schéma k analýze dokumenty. Pokyny najdete v tématu [použití vlastní SerDe JSON s Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Souhrn
Na závěr typ operátoru JSON v Hivu, který zvolíte, závisí na váš scénář. Pokud máte jednoduchou dokumentů JSON a máte pouze jedno pole pro vyhledávání, můžete použít get_json_object Hive UDF. Pokud máte více než jeden klíč k vyhledání, můžete použít json_tuple. Pokud máte vnořené dokumentu, měli byste použít JSON SerDe.

## <a name="next-steps"></a>Další postup

Související články naleznete v tématu:

* [Použití Apache Hive a HiveQL s Apache Hadoop v HDInsight k analýze ukázkového souboru Apache log4j](../hdinsight-use-hive.md)
* [Analyzovat zpoždění letů pomocí interaktivního dotazu v HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analýza dat Twitteru pomocí Apache Hive v HDInsight](../hdinsight-analyze-twitter-data-linux.md)

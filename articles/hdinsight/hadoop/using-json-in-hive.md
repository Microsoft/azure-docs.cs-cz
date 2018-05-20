---
title: Analýza a zpracování dokumentů JSON s Apache Hive v Azure HDInsight | Microsoft Docs
description: Naučte se používat dokumentů JSON a analyzovat je pomocí apache Hive v Azure HDInsight
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: e17794e8-faae-4264-9434-67f61ea78f13
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: 19d8e74e6f3ca8e7a0b0a64bdd782ea21b233c17
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Zpracovávat a analyzovat dokumenty JSON pomocí Apache Hive v Azure HDInsight

Zjistěte, jak zpracovávat a analyzovat JavaScript Object Notation (JSON) souborů pomocí Apache Hive v Azure HDInsight. Tento kurz používá následující dokumentu JSON:

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

Soubor můžete najít v **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Další informace o tom, jak používat úložiště objektů Azure Blob s HDInsight naleznete v tématu [HDFS kompatibilního použití Azure Blob storage s Hadoop v HDInsight](../hdinsight-hadoop-use-blob-storage.md). Zkopírujte soubor do kontejneru výchozí clusteru.

V tomto kurzu použijete konzolu Hive. Pokyny o tom, otevřete konzolu Hive naleznete v tématu [používání Hive s Hadoop v HDInsight pomocí vzdálené plochy](apache-hadoop-use-hive-remote-desktop.md).

## <a name="flatten-json-documents"></a>Vyrovnání dokumentů JSON
Metody uvedené v následující části vyžadují, aby dokumentu JSON skládá z jednoho řádku. Ano musí vyrovnání dokumentu JSON na řetězec. Pokud už je průmětu dokumentu JSON, můžete tento krok přeskočte a přejděte rovnou k další části Analýza dat JSON. Chcete-li shrnout dokumentu JSON, spusťte následující skript:

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

Soubor raw JSON se nachází v **wasb://processjson@hditutorialdata.blob.core.windows.net/**. **StudentsRaw** Hive tabulky body k nezpracované dokument JSON, který se nesloučí.

**StudentsOneLine** tabulku Hive ukládá data do HDInsight výchozí systém souborů v části **/json/studenty/** cesta.

**Vložit** příkaz naplní **StudentOneLine** tabulku s plochou data JSON.

**Vyberte** příkaz vrátí jenom jeden řádek.

Tady je výstup **vyberte** příkaz:

![Vyrovnání dokumentu JSON][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analýza dokumentů JSON v Hive
Hive poskytuje tři různé mechanismy pro spouštění dotazů na dokumenty JSON, nebo můžete napsat vlastní:

* Použijte get_json_object uživatelem definované funkce (UDF).
* Použijte json_tuple UDF.
* Použijte vlastní serializátor/deserializátor (SerDe).
* Napište vlastní UDF pomocí Python nebo jiných jazyků. Další informace o tom, jak spustit vlastní kód Python s Hive najdete v tématu [UDF Python s Apache Hive a Pig][hdinsight-python].

### <a name="use-the-getjsonobject-udf"></a>Použití get_json_object UDF
Hive poskytuje integrované UDF názvem [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) , můžete provést JSON dotazování za běhu. Tato metoda přebírá dva argumenty – název tabulky a název metody, který má sloučeném dokumentu JSON a pole JSON, který potřebujete analyzovat. Podívejme se na příklad zobrazíte fungování této UDF.

Následující dotaz vrátí křestní jméno a příjmení pro každý student:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Toto je výstup při spuštění tohoto dotazu v okně konzoly:

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

Existují omezení get_json_object UDF:

* Protože každé pole v dotazu vyžaduje reparsing dotazu, ovlivňuje výkon.
* **ZÍSKAT\_JSON_OBJECT()** vrátí řetězcovou reprezentaci pole. Chcete-li převést toto pole k poli Hive, budete muset použít regulární výrazy k nahrazení hranaté závorky "[" a "]", a pak budete také muset volat rozdělení získat pole.

Z tohoto důvodu Hive wiki doporučuje používat json_tuple.  

### <a name="use-the-jsontuple-udf"></a>Použití json_tuple UDF
Jiné UDF poskytované Hive se nazývá [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), která provede lepší, než [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Tato metoda přebírá sadu klíčů a řetězec formátu JSON a vrátí řazené kolekce členů hodnot pomocí funkce. Následující dotaz vrátí student ID a úrovni z dokumentu JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Výstup skriptu v konzole nástroje Hive:

![json_tuple UDF][image-hdi-hivejson-jsontuple]

Json_tuple používá UDF [pomoci odhalit laterální zobrazení](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) syntaxi Hive, která umožňuje json\_řazené kolekce členů a vytvořte virtuální tabulku použitím funkce UDT na každém řádku původní tabulky. Komplexní JSONs být příliš nepraktické kvůli opakované použití **LATERÁLNÍ zobrazení**. Kromě toho **JSON_TUPLE** nemůže zpracovat vnořené JSONs.

### <a name="use-a-custom-serde"></a>Použít vlastní SerDe
SerDe je nejlepší volbou pro analýzu vnořených dokumentů JSON. Umožňuje definovat schématu JSON a pak můžete použít schéma analyzovat dokumenty. Pokyny najdete v tématu [použití vlastní SerDe JSON s Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Souhrn
Na závěr typ operátor JSON v Hive, který zvolíte, závisí na váš scénář. Pokud máte jednoduchý dokumentu JSON a máte pouze jedno pole pro vyhledávání, můžete použít get_json_object Hive UDF. Pokud máte více než jeden klíč k vyhledání, můžete použít json_tuple. Pokud máte vnořené dokumentu, měli byste použít JSON SerDe.

## <a name="next-steps"></a>Další postup

Související články najdete v části:

* [Použití Hive a HiveQL s Hadoop v HDInsight k analýze ukázkového souboru Apache log4j](../hdinsight-use-hive.md)
* [Analýza dat zpoždění letu pomocí Hive v HDInsight](../hdinsight-analyze-flight-delay-data.md)
* [Analýza dat Twitteru pomocí Hive v HDInsight](../hdinsight-analyze-twitter-data.md)

[hdinsight-python]:python-udf-hdinsight.md

[image-hdi-hivejson-flatten]: ./media/using-json-in-hive/flatten.png
[image-hdi-hivejson-getjsonobject]: ./media/using-json-in-hive/getjsonobject.png
[image-hdi-hivejson-jsontuple]: ./media/using-json-in-hive/jsontuple.png
[image-hdi-hivejson-jdk]: ./media/hdinsight-using-json-in-hive/jdk.png
[image-hdi-hivejson-maven]: ./media/hdinsight-using-json-in-hive/maven.png
[image-hdi-hivejson-serde]: ./media/hdinsight-using-json-in-hive/serde.png
[image-hdi-hivejson-addjar]: ./media/hdinsight-using-json-in-hive/addjar.png
[image-hdi-hivejson-serde_query1]: ./media/hdinsight-using-json-in-hive/serde_query1.png
[image-hdi-hivejson-serde_query2]: ./media/hdinsight-using-json-in-hive/serde_query2.png
[image-hdi-hivejson-serde_query3]: ./media/hdinsight-using-json-in-hive/serde_query3.png
[image-hdi-hivejson-serde_result]: ./media/hdinsight-using-json-in-hive/serde_result.png


---
title: Analýza a zpracování dokumentů JSON pomocí Apache Hive v Azure HDInsight
description: Zjistěte, jak používat dokumentů JSON a analyzovat pomocí apache Hivu ve službě Azure HDInsight
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 0322b95c6fecc120fd6a50b05dbf502f28afa114
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2018
ms.locfileid: "51006562"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Zpracování a analýza dokumentů JSON pomocí Apache Hive v Azure HDInsight

Zjistěte, jak zpracovávat a analyzovat soubory JavaScript Object Notation (JSON) s použitím Apache Hive v Azure HDInsight. V tomto kurzu používá následující dokument JSON:

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

Soubor můžete najít v **wasb://processjson@hditutorialdata.blob.core.windows.net/**. Další informace o tom, jak používat Azure Blob storage s HDInsight naleznete v tématu [úložiště objektů Blob v Azure použití HDFS kompatibilního se systémem Hadoop v HDInsight](../hdinsight-hadoop-use-blob-storage.md). Zkopírujte soubor do kontejneru výchozí vašeho clusteru.

V tomto kurzu použijete konzolu Hive. Pokyny o tom, jak používat konzolu Hive najdete v tématu [použití Hivu s Hadoopem v HDInsight pomocí vzdálené plochy](apache-hadoop-use-hive-remote-desktop.md).

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

Nezpracovaný soubor JSON se nachází v **wasb://processjson@hditutorialdata.blob.core.windows.net/**. **StudentsRaw** Hive body tabulku nezpracovaných dokumentu JSON, který se sloučí.

**StudentsOneLine** tabulky Hive v HDInsight výchozí systém souborů pod ukládá data **/json/studenty/** cestu.

**Vložit** naplní příkaz **StudentOneLine** tabulku s plochou data JSON.

**Vyberte** příkaz vrátí jenom jeden řádek.

Zde je výstup **vyberte** – příkaz:

![Sloučení dokumentu JSON][image-hdi-hivejson-flatten]

## <a name="analyze-json-documents-in-hive"></a>Analýza dokumentů JSON v Hivu
Hive poskytuje tři různé mechanismy ke spouštění dotazů na dokumenty JSON, nebo napište vlastní:

* Použijte get_json_object uživatelem definované funkce (UDF).
* Použijte json_tuple UDF.
* Použijte vlastní serializátor/deserializátor (SerDe).
* Napište vlastní UDF pomocí Pythonu nebo jiných jazycích. Další informace o způsobu spuštění kódu Pythonu s Hivem najdete v tématu [uživatelsky definované funkce Pythonu s Apache Hive a Pig][hdinsight-python].

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

![get_json_object UDF][image-hdi-hivejson-getjsonobject]

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

![json_tuple UDF][image-hdi-hivejson-jsontuple]

Json_tuple používá systém souborů UDF [laterální zobrazení](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) syntaxe v poznámce Hive, která umožňuje json\_řazené kolekce členů k vytvoření virtuální tabulky použitím UDT funkce na každý řádek v původní tabulce. Komplexní JSONs být příliš nepraktické z důvodu opakovaného použití **LATERÁLNÍ zobrazení**. Kromě toho **JSON_TUPLE** nemůže zpracovat vnořené JSONs.

### <a name="use-a-custom-serde"></a>Použít vlastní SerDe
SerDe je nejlepší volbou pro potřeby analýzy vnořených dokumentů JSON. Umožňuje definovat schéma JSON a pak můžete použít schéma k analýze dokumenty. Pokyny najdete v tématu [použití vlastní SerDe JSON s Microsoft Azure HDInsight](https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Souhrn
Na závěr typ operátoru JSON v Hivu, který zvolíte, závisí na váš scénář. Pokud máte jednoduchou dokumentů JSON a máte pouze jedno pole pro vyhledávání, můžete použít get_json_object Hive UDF. Pokud máte více než jeden klíč k vyhledání, můžete použít json_tuple. Pokud máte vnořené dokumentu, měli byste použít JSON SerDe.

## <a name="next-steps"></a>Další postup

Související články naleznete v tématu:

* [Použití Hive a HiveQL s Hadoop v HDInsight k analýze ukázkového souboru Apache log4j](../hdinsight-use-hive.md)
* [Analyzovat zpoždění letů pomocí Hivu ve službě HDInsight](../hdinsight-analyze-flight-delay-data.md)
* [Analýza dat Twitteru pomocí Hivu ve službě HDInsight](../hdinsight-analyze-twitter-data.md)

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


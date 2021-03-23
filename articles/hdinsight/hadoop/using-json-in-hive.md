---
title: Analýza & procesu JSON pomocí Apache Hive – Azure HDInsight
description: Naučte se používat dokumenty JSON a analyzovat je pomocí Apache Hive ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: d3c5077450dd4ec59f5ea7bc8f37879f9aa775bf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868932"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Zpracování a analýza dokumentů JSON pomocí Apache Hive ve službě Azure HDInsight

Naučte se zpracovávat a analyzovat soubory JavaScript Object Notation (JSON) pomocí Apache Hive ve službě Azure HDInsight. Tento článek používá následující dokument JSON:

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

Soubor najdete na adrese `wasb://processjson@hditutorialdata.blob.core.windows.net/` . Další informace o tom, jak používat úložiště objektů BLOB v Azure se službou HDInsight, najdete v článku [použití služby Azure Blob Storage kompatibilní s HDFS s Apache Hadoop v HDInsight](../hdinsight-hadoop-use-blob-storage.md). Soubor můžete zkopírovat do výchozího kontejneru clusteru.

V tomto článku použijete konzolu Apache Hive. Pokyny k otevření konzoly podregistru najdete v tématu [použití zobrazení podregistru Apache Ambari s Apache Hadoop v HDInsight](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> Zobrazení podregistru už není dostupné v HDInsight 4,0.

## <a name="flatten-json-documents"></a>Sloučení dokumentů JSON

Metody uvedené v další části vyžadují, aby dokument JSON byl složený z jednoho řádku. Proto je nutné dokument JSON sloučit do řetězce. Pokud je váš dokument JSON již plochý, můžete tento krok přeskočit a přejít rovnou k další části týkající se analýzy dat JSON. Pro sloučení dokumentu JSON spusťte následující skript:

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

Nezpracovaný soubor JSON je umístěný na adrese `wasb://processjson@hditutorialdata.blob.core.windows.net/` . Tabulka podregistru **StudentsRaw** odkazuje na nezpracovaný dokument JSON, který není plochý.

Tabulka podregistr **StudentsOneLine** ukládá data do výchozího systému souborů HDInsight pod cestou **/JSON/Students/** .

Příkaz **INSERT** naplní tabulku **StudentOneLine** pomocí shrnutých dat JSON.

Příkaz **Select** vrátí pouze jeden řádek.

Zde je výstup příkazu **Select** :

:::image type="content" source="./media/using-json-in-hive/hdinsight-flatten-json.png" alt-text="HDInsight – sloučení dokumentu JSON" border="true":::

## <a name="analyze-json-documents-in-hive"></a>Analyzovat dokumenty JSON v podregistru

Podregistr poskytuje tři různé mechanismy pro spouštění dotazů na dokumentech JSON, nebo můžete napsat vlastní:

* Použijte get_json_object uživatelem definovanou funkci (UDF).
* Použijte json_tuple systému souborů UDF.
* Použijte vlastní serializátor nebo deserializaci (SerDe).
* Vytvářejte vlastní systém souborů UDF pomocí Pythonu nebo jiných jazyků. Další informace o tom, jak spustit vlastní kód Pythonu s podregistrem, najdete v tématu [Python UDF s Apache Hive a Apache prasete](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Použití get_json_object UDF

Podregistr poskytuje integrovaný systém souborů UDF s názvem [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) , který se dotazuje JSON během běhu. Tato metoda přijímá dva argumenty: název tabulky a název metody. Název metody obsahuje sloučený dokument JSON a pole JSON, které je nutné analyzovat. Pojďme se podívat na příklad, jak tento systém souborů UDF funguje.

Následující dotaz vrátí křestní jméno a příjmení pro každého studenta:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Toto je výstup při spuštění tohoto dotazu v okně konzoly:

:::image type="content" source="./media/using-json-in-hive/hdinsight-get-json-object.png" alt-text="Apache Hive Získá objekt JSON typu UDF." border="true":::

K dispozici jsou omezení get_json_object systému souborů UDF:

* Vzhledem k tomu, že každé pole v dotazu vyžaduje přeanalýzu dotazu, má vliv na výkon.
* **Získat \_ JSON_OBJECT ()** vrátí řetězcovou reprezentaci pole. Chcete-li převést toto pole na pole podregistru, je nutné použít regulární výrazy k nahrazení hranatých závorek "[" a "]" a pak je také nutné volat rozdělení pro získání pole.

Tento převod představuje důvody, proč wiki v podregistru doporučuje použít **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Použití json_tuple UDF

Další UDF, který poskytuje podregistr, se nazývá [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), což je lepší než [get_ JSON _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Tato metoda přebírá sadu klíčů a řetězec JSON. Pak vrátí řazenou kolekci hodnot. Následující dotaz vrátí ID studenta a třídu z dokumentu JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Výstup tohoto skriptu v konzole podregistru:

:::image type="content" source="./media/using-json-in-hive/hdinsight-json-tuple.png" alt-text="Apache Hive výsledků dotazu JSON" border="true":::

`json_tuple`Systém UDF používá syntaxi [zobrazení](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) v podregistru, která umožňuje \_ řazené kolekci členů JSON vytvořit virtuální tabulku použitím funkce UDT na každý řádek původní tabulky. Komplexní JSON se přestanou nepraktický z důvodu opakovaného použití **bočního zobrazení**. **JSON_TUPLE** navíc nemůže zpracovat vnořené JSON.

### <a name="use-a-custom-serde"></a>Použití vlastního SerDe

SerDe je nejlepší volbou pro analýzu vnořených dokumentů JSON. Umožňuje definovat schéma JSON a pak můžete použít schéma k analýze dokumentů. Pokyny najdete v tématu [Jak používat vlastní SERDE JSON s Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/).

## <a name="summary"></a>Souhrn

Typ operátoru JSON v podregistru, který zvolíte, závisí na vašem scénáři. Pomocí jednoduchého dokumentu JSON a jednoho pole, které se má vyhledat, vyberte **Get_json_object** podregistr UDF. Pokud máte více než jeden klíč k vyhledání, můžete použít **json_tuple**. U vnořených dokumentů použijte **SerDe JSON**.

## <a name="next-steps"></a>Další kroky

Související články najdete v tématech:

* [Analýza ukázkového souboru Apache log4j pomocí Apache Hive a HiveQL s Apache Hadoop v HDInsight](./hdinsight-use-hive.md)
* [Analýza dat zpoždění letů pomocí interaktivního dotazu ve službě HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
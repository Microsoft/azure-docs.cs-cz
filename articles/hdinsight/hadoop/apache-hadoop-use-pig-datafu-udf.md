---
title: Použití DataFu s Pig v HDInsight - Azure | Microsoft Docs
description: DataFu je sada knihoven pro použití s Hadoop. Zjistěte, jak používat DataFu s Pig v clusteru HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.assetid: 0016721a-82be-4773-88ad-91e6b2c21cbb
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/10/2018
ms.author: larryfr
ms.openlocfilehash: 30243d0b7db41fbe19c60d6c11d56fb7e801797b
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401002"
---
# <a name="use-datafu-with-pig-on-hdinsight"></a>Použití DataFu s pig v HDInsight

Další informace o použití DataFu s HDInsight. DataFu je kolekce knihovny Open Source pro použití s Pig na Hadoop.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure.

* Cluster Azure HDInsight (Linux nebo systémem Windows)

  > [!IMPORTANT]
  > HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Základní znalost [pomocí Pig v HDInsight](hdinsight-use-pig.md)

## <a name="install-datafu-on-linux-based-hdinsight"></a>Nainstalujte DataFu na HDInsight se systémem Linux

> [!IMPORTANT]
> DataFu je nainstalován na clusterech se systémem Linux verze 3.3 a vyšší a na clusterech se systémem Windows. Není nainstalován v clusterech se systémem Linux starší než 3.3.
>
> Pokud používáte cluster systému Windows nebo clusteru vyšší než verze 3.3 systémem Linux, tuto část přeskočte.

DataFu je možné stáhnout a nainstalovat z úložiště Maven. Chcete-li přidat DataFu ke svému clusteru HDInsight pomocí následujících kroků:

1. Připojte ke clusteru HDInsight se systémem Linux pomocí protokolu SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Použijte následující příkaz ke stažení na soubor jar DataFu pomocí nástroje wget nebo zkopírujte a vložte odkaz do prohlížeče zahájíte stahování.

    ```
    wget http://central.maven.org/maven2/com/linkedin/datafu/datafu/1.2.0/datafu-1.2.0.jar
    ```

3. V dalším kroku nahrajte soubor do výchozího úložiště pro váš cluster HDInsight. Ve výchozím umístění souboru úložiště je k dispozici na všech uzlech v clusteru.

    ```
    hdfs dfs -put datafu-1.2.0.jar /example/jars
    ```

    > [!NOTE]
    > Předchozí příkaz ukládá jar v `/example/jars` vzhledem k tomu, že tento adresář již existuje v úložišti clusteru. Můžete použít libovolného umístění, které chcete v úložišti clusteru HDInsight.

## <a name="use-datafu-with-pig"></a>Použití DataFu s Pig

Postup v této části se předpokládá, že jste obeznámeni s používáním Pig v HDInsight. Další informace o používání Pig s HDInsight naleznete v tématu [použijte Pig s HDInsight](hdinsight-use-pig.md).

> [!IMPORTANT]
> Pokud jste ručně instalovali DataFu pomocí kroků v předchozí části, je nutné jej zaregistrovat před jeho použitím.
>
> * Pokud váš cluster používá Azure Storage, použijte `wasb://` cesta. Například, `register wasb:///example/jars/datafu-1.2.0.jar`.
>
> * Pokud váš cluster používá Azure Data Lake Store, použijte `adl://` cesta. Například, `register adl://home/example/jars/datafu-1.2.0.jar`.

Často definujete alias pro DataFu funkce. V následujícím příkladu definuje zástupce `SHA`:

```piglatin
DEFINE SHA datafu.pig.hash.SHA();
```

Tento alias ve skriptu Pig Latin můžete potom použít ke generování hodnoty hash pro vstupní data. Následující kód například nahrazuje umístění vstupních dat s hodnotou hash:

```piglatin
raw = LOAD '/HdiSamples/HdiSamples/SensorSampleData/building/building.csv' USING
    org.apache.pig.piggybank.storage.CSVExcelStorage(',', 'NO_MULTILINE', 'UNIX', 'SKIP_INPUT_HEADER') AS
    (int1:int,
     id1:chararray,
     int2:int,
     id2:chararray,
     location:chararray);
mask = FOREACH raw GENERATE int1, id1, int2, id2, SHA(location);
DUMP mask;
```

Vygeneruje následující výstup:

    (1,M1,25,AC1000,aa5ab35a9174c2062b7f7697b33fafe5ce404cf5fecf6bfbbf0dc96ba0d90046)
    (2,M2,27,FN39TG,7a1ca4ef7515f7276bae7230545829c27810c9d9e98ab2c06066bee6270d5153)
    (3,M3,28,JDNS77,07f62b021771d3cf67e2e1faf18769cc5e5c119ad7d4d1847a11e11d6d5a7ecb)
    (4,M4,17,GG1919,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (5,M5,3,ACMAX22,1ed8c7e56c947bebc0cfcf88c4ea0f02c944568f71df893a99970e4f0c78cddc)
    (6,M6,9,AC1000,c96dd81db196cca5f57bd4270bbb9d9e9d1b242d67f9364005ee1dfdc2632523)
    (7,M7,13,FN39TG,3e049d78d958038ac6bd5dcf038075cc73362b4956aaf7308c3a69c8eca76297)
    (8,M8,25,JDNS77,c1ef40ce0484c698eb4bd27fe56c1e7b68d74f9780ed674210d0e5013dae45e9)
    (9,M9,11,GG1919,a7d355b26bda6bf1196ccffead0b2cf2b81f0a9de5b4876b44407f1dc07e51e6)
    (10,M10,23,ACMAX22,10436829032f361a3de50048de41755140e581467bc1895e6c1a17f423e42d10)
    (11,M11,14,AC1000,348841ef53dbd5a64008a86be432973db790774fb28b59b0d99702a3188b3705)
    (12,M12,26,FN39TG,aed8f531aa7e785be255bc435e2582e74c58defedebcb629eeabf365b809bd6f)
    (13,M13,25,JDNS77,ed9ad13611d7164839dd3feaf9a7f6a75a4138f389e0d45f8e07fa38da1116a2)
    (14,M14,17,GG1919,80db4ccdca106d37b920206331fcfe3e9e50a9e763d89b54ce3ad5ac8cf30f03)
    (15,M15,19,ACMAX22,3552ac0c032467fbf592cb4d10c5c9267800c01e343ad8ca557256d882ae9327)
    (16,M16,23,AC1000,07c67d76ef92ac9853588e098983fefba4ba5965f8fec95f42ab0d04c27865ba)
    (17,M17,11,FN39TG,557c1599d9a04895d3817d293e0806a4419a14de31958386182798d0d2ed3a56)
    (18,M18,25,JDNS77,dbc74a36d8e7439c45c64d856388506cc9b1218619cef979c3d605115a7a4546)
    (19,M19,14,GG1919,be55ef3f4c4e6c2d9c2afe2a33ac90ad0f50d4de7f9163999877e2a9ca5a54f8)
    (20,M20,19,ACMAX22,ea0b937ea317101ee2c26b03a4843a19ceced8a2b9673c3cf409a726ca2b0fd8)

## <a name="next-steps"></a>Další postup

Další informace o DataFu nebo Pig najdete v následujících dokumentech:

* [Příručka Apache DataFu Pig](http://datafu.incubator.apache.org/docs/datafu/guide.html).
* [Použití Pigu se službou HDInsight](hdinsight-use-pig.md)

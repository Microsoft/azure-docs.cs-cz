---
title: Použijte Hadoop Pig pomocí vzdálené plochy v HDInsight - Azure | Microsoft Docs
description: Další informace o použití příkazu Pig spustit příkazy Pig Latin z připojení vzdálené plochy na clusteru systému Windows Hadoop v HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: e034a286-de0f-465f-8bf1-3d085ca6abed
ms.service: hdinsight
ms.devlang: na
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: larryfr
ROBOTS: NOINDEX
ms.openlocfilehash: 700569684888428032997dd81c10023c6fd5a7d8
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823301"
---
# <a name="run-pig-jobs-from-a-remote-desktop-connection"></a>Spuštění úlohy Pig z připojení vzdálené plochy
[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Tento dokument poskytuje návod pro používání Pig příkaz ke spuštění příkazů Pig Latin z připojení vzdálené plochy ke clusteru HDInsight se systémem Windows. Pig Latin můžete vytvořit MapReduce aplikace prostřednictvím popisu transformace dat, nikoli mapování a snížit funkce.

> [!IMPORTANT]
> Vzdálená plocha je dostupná pouze na clustery HDInsight, které používají Windows jako operační systém. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4 nebo větší, projděte si téma [použijte Pig s HDInsight a SSH](apache-hadoop-use-pig-ssh.md) informace o interaktivním spouštění úlohy Pig přímo na clusteru z příkazového řádku.

## <a id="prereq"></a>Požadavky
Pokud chcete provést kroky v tomto článku, budete potřebovat.

* Cluster HDInsight se systémem Windows (Hadoop v HDInsight)
* Klientský počítač se systémem Windows 10, Windows 8 nebo Windows 7

## <a id="connect"></a>Připojit pomocí vzdálené plochy
Povolení vzdálené plochy pro HDInsight cluster a pak připojit pomocí pokynů uvedených v [připojení ke clusterům HDInsight pomocí protokolu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="pig"></a>Použijte příkaz Pig
1. Až budete mít připojení vzdálené plochy, spusťte **Hadoop příkazového řádku** pomocí ikony na ploše.
2. Ke spuštění příkazu Pig použijte následující:

        %pig_home%\bin\pig

    Zobrazí se `grunt>` řádku.
3. Zadejte následující příkaz:

        LOGS = LOAD 'wasb:///example/data/sample.log';

    Tento příkaz načte obsah souboru sample.log do souborů PROTOKOLŮ. Pomocí následujícího příkazu můžete zobrazit obsah souboru:

        DUMP LOGS;
4. Transformace dat s použitím regulárních výrazů k extrakci pouze úroveň protokolování z každý záznam:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Můžete použít **DUMP** chcete zobrazit data po transformaci. V takovém případě `DUMP LEVELS;`.
5. Pokračujte v použití transformací pomocí následující příkazy. Použití `DUMP` zobrazíte výsledek transformace po dokončení každého kroku.

    <table>
    <tr>
    <th>Výraz</th><th>Co dělá</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = filtr úrovně podle LOGLEVEL není null.</td><td>Odebere řádky, které obsahují hodnotu null pro úroveň protokolu a ukládá výsledky do FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS skupiny podle LOGLEVEL;</td><td>Seskupí řádky podle úrovně protokolu a ukládá výsledky do GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREKVENCE = foreach GROUPEDLEVELS generovat skupiny jako LOGLEVEL, počet (FILTEREDLEVELS. LOGLEVEL) jako počet;</td><td>Vytvoří novou sadu dat, která obsahuje každý jedinečný protokolu dojde k hodnota úrovně a jak často se. To je uložena do frekvence</td>
    </tr>
    <tr>
    <td>VÝSLEDEK = pořadí FREKVENCÍ podle počtu desc;</td><td>Řadí úrovní záznamu do protokolu podle počtu (sestupně) a ukládá do výsledku</td>
    </tr>
</table>
6. Můžete také uložit výsledky transformace pomocí `STORE` příkaz. Například následující příkaz uloží `RESULT` k **/example/data/pigout** adresář ve výchozím kontejneru úložiště pro cluster:

        STORE RESULT into 'wasb:///example/data/pigout'

   > [!NOTE]
   > Jsou data uložena v adresáři zadané v souborech s názvem **část nnnnn**. Pokud adresář již existuje, zobrazí se chybová zpráva.
   >
   >
7. Chcete-li ukončit řádku grunt, zadejte následující příkaz.

        QUIT;

### <a name="pig-latin-batch-files"></a>Pig Latin dávkové soubory
Můžete také použít příkaz Pig ke spuštění Pig Latin, který je obsažený v souboru.

1. Po ukončení řádku grunt, otevřete **Poznámkový blok** a vytvořte nový soubor s názvem **pigbatch.pig** v **PIG_HOME %** adresáře.
2. Zadejte nebo vložte následující řádky do **pigbatch.pig** souboru a pak ho uložte:

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Použijte tento příkaz ke spuštění **pigbatch.pig** souboru pomocí příkazu pig.

        pig %PIG_HOME%\pigbatch.pig

    Po dokončení úlohy batch, byste měli vidět následující výstup, který by měl být stejný jako při použití `DUMP RESULT;` v předchozích krocích:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>Shrnutí
Jak vidíte, příkaz Pig umožňuje interaktivně spustit MapReduce operations, nebo spustit úlohy Pig Latin, které jsou uložené v dávkovém souboru.

## <a id="nextsteps"></a>Další kroky
Obecné informace o Pig v HDInsight:

* [Použijte Pig s Hadoop v HDInsight](hdinsight-use-pig.md)

Informace o jiných způsobech můžete pracovat s Hadoop v HDInsight:

* [Použijte Hive s Hadoop v HDInsight](hdinsight-use-hive.md)
* [Používání nástroje MapReduce s Hadoop v HDInsight](hdinsight-use-mapreduce.md)

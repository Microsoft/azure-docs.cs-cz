---
title: Použití Apache Pig s HDInsight – Azure přes vzdálenou plochu
description: Další informace o použití příkazu Pig spouští příkazy Apache Pig Latin z připojení ke vzdálené ploše do clusteru Windows založené na Apache Hadoop v HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/17/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 69eeadd8252d20dd25ac6d0abbbf0c6c158383d5
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438621"
---
# <a name="run-apache-pig-jobs-from-a-remote-desktop-connection"></a>Spouštět úlohy Apache Pig z připojení ke vzdálené ploše
[!INCLUDE [pig-selector](../../../includes/hdinsight-selector-use-pig.md)]

Tento dokument poskytuje návod pro použití příkazu Apache Pig spouští příkazy Pig Latin z připojení ke vzdálené ploše na cluster HDInsight se systémem Windows. Pig Latin můžete vytvářet aplikace MapReduce zadáním popisu vašeho nového transformace dat, spíše než mapovací a redukční funkce.

> [!IMPORTANT]  
> Vzdálená plocha je dostupná pouze na clustery HDInsight, používající jako operační systém Windows. HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).
>
> HDInsight 3.4 nebo větší, přečtěte si téma [použití Apache Pig s HDInsight a SSH](apache-hadoop-use-pig-ssh.md) informace o tom, z příkazového řádku interaktivně spustíte úlohy Pig přímo na clusteru.

## <a id="prereq"></a>Požadavky
K dokončení kroků v tomto článku, budete potřebovat.

* Cluster Windows systémem HDInsight (Hadoop v HDInsight)
* Klientský počítač s Windows 10, Windows 8 nebo Windows 7

## <a id="connect"></a>Připojte se přes vzdálenou plochu
Povolení vzdálené plochy pro HDInsight cluster a pak k němu připojit pomocí následujících pokynů na adrese [připojit ke clusterům HDInsight pomocí protokolu RDP](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

## <a id="pig"></a>Použití příkazu Pig
1. Po připojení ke vzdálené ploše, začít **příkazového řádku Hadoopu** pomocí ikony na ploše.
2. Pomocí následujícího postupu spustit příkaz Pig:

        %pig_home%\bin\pig

    Zobrazí se `grunt>` řádku.
3. Zadejte následující příkaz:

        LOGS = LOAD 'wasb:///example/data/sample.log';

    Tento příkaz načte obsah souboru sample.log do souborů PROTOKOLŮ. Obsah souboru můžete zobrazit pomocí následujícího příkazu:

        DUMP LOGS;
4. Transformace dat s použitím regulárních výrazů k extrakci jenom úroveň protokolování z každého záznamu:

        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;

    Můžete použít **VYPSAT** k zobrazení dat po transformaci. V takovém případě `DUMP LEVELS;`.
5. Pokračujte v použití transformací pomocí následujících příkazů. Použití `DUMP` abyste viděli výsledek transformace po provedení každého kroku.

    <table>
    <tr>
    <th>Výraz</th><th>Co dělá</th>
    </tr>
    <tr>
    <td>FILTEREDLEVELS = filtr úrovně podle LOGLEVEL není null.</td><td>Odebere řádky, které obsahují hodnotu null pro úroveň protokolování a uloží výsledky do FILTEREDLEVELS.</td>
    </tr>
    <tr>
    <td>GROUPEDLEVELS = FILTEREDLEVELS skupiny podle LOGLEVEL;</td><td>Seskupí řádky podle úroveň protokolu a ukládá výsledky do GROUPEDLEVELS.</td>
    </tr>
    <tr>
    <td>FREKVENCE = foreach GROUPEDLEVELS generovat skupiny jako LOGLEVEL, počet (FILTEREDLEVELS. LOGLEVEL) jako počet;</td><td>Vytvoří novou sadu dat, která obsahuje všechny jedinečné protokoly hodnota úrovně a jak často se vyvolá. To je uložen do frekvence</td>
    </tr>
    <tr>
    <td>VÝSLEDEK = pořadí FREKVENCÍ počet desc;</td><td>Seřadí úrovních protokolování podle počtu (sestupně) a uloží do výsledku</td>
    </tr>
</table>

6. Můžete také uložit výsledky transformace s využitím `STORE` příkazu. Například následující příkaz uloží `RESULT` k **/example/data/pigout** ve výchozí kontejner úložiště pro cluster:

        STORE RESULT into 'wasb:///example/data/pigout'

   > [!NOTE]  
   > Jsou data uložená v zadaném adresáři v souborech s názvem **část nnnnn**. Pokud adresář již existuje, zobrazí se chybová zpráva.
   >
   >
   
7. Pro ukončení řádku grunt, zadejte následující příkaz.

        QUIT;

### <a name="pig-latin-batch-files"></a>Pig Latin dávkové soubory
Můžete také Pig příkaz ke spuštění Pig Latin, které jsou obsaženy v souboru.

1. Po ukončení řádku grunt, otevřete **Poznámkový blok** a vytvořte nový soubor s názvem **pigbatch.pig** v **PIG_HOME %** adresáře.
2. Zadejte nebo vložte následující řádky do **pigbatch.pig** souboru a pak ho uložte:

        LOGS = LOAD 'wasb:///example/data/sample.log';
        LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
        FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
        GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
        FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
        RESULT = order FREQUENCIES by COUNT desc;
        DUMP RESULT;
3. Pomocí následujícího postupu spustit **pigbatch.pig** soubor pomocí příkazu pig.

        pig %PIG_HOME%\pigbatch.pig

    Po dokončení úlohy služby batch, byste měli vidět následující výstup, který by měl být stejný jako při použití `DUMP RESULT;` v předchozích krocích:

        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

## <a id="summary"></a>Shrnutí
Jak je vidět, příkaz Pig umožňuje interaktivně spusťte operace MapReduce nebo spouštět úlohy Pig Latin, které jsou uloženy v dávkovém souboru.

## <a id="nextsteps"></a>Další kroky
Obecné informace o Pig v HDInsight:

* [Použití Apache Pig s Apache Hadoop v HDInsight](hdinsight-use-pig.md)

Informace o jiných způsobech, jakými můžete pracovat s Hadoop v HDInsight:

* [Použití Apache Hivu s Apache Hadoop v HDInsight](hdinsight-use-hive.md)
* [Použití MapReduce se službou Apache Hadoop v HDInsight](hdinsight-use-mapreduce.md)

---
title: Zrychlené zápisy služby Azure HDInsight pro Apache HBase
description: Poskytuje přehled funkce Azure HDInsight Accelerated Writes, která používá prémiové spravované disky ke zlepšení výkonu protokolu zápisu dopředného zápisu Apache HBase.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 7165bab96d037f6782bc9aa6767cadd9b35f058c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76764594"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Zrychlené zápisy služby Azure HDInsight pro Apache HBase

Tento článek obsahuje informace o funkci **Accelerated Writes** pro Apache HBase v Azure HDInsight a jak ji lze efektivně použít ke zlepšení výkonu zápisu. **Akcelerované zápisy** používá [azure premium SSD spravované disky](../../virtual-machines/linux/disks-types.md#premium-ssd) ke zlepšení výkonu Apache HBase zápis uhlazeného protokolu (WAL). Další informace o Apache HBase najdete v tématu [Co je Apache HBase v HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Přehled architektury HBase

V HBase **se řádek** skládá z jednoho nebo více **sloupců** a je identifikován **klávesou řádku**. Tabulku tvoří více řádků **.** Sloupce obsahují **buňky**, které jsou časově označené verze hodnoty v tomto sloupci. Sloupce jsou seskupeny do **rodin sloupců**a všechny sloupce v rodině sloupců jsou uloženy společně v souborech úložiště s názvem **HFiles**.

**Oblasti** v HBase se používají k vyrovnání zatížení zpracování dat. HBase nejprve uloží řádky tabulky v jedné oblasti. Řádky jsou rozloženy do více oblastí, jak se zvyšuje množství dat v tabulce. **Servery oblastí** mohou zpracovávat požadavky pro více oblastí.

## <a name="write-ahead-log-for-apache-hbase"></a>Zápis dopředného protokolu pro Apache HBase

HBase nejprve zapíše aktualizace dat do typu protokolu potvrzení nazývaného protokol dopředného zápisu (WAL). Po aktualizaci je uložen a WAL, je zapsán a v paměti **MemStore**. Když data v paměti dosáhne maximální kapacity, je zapsána na disk jako **HFile**.

Pokud **RegionServer** dojde k chybě nebo přestane být k dispozici před MemStore je vyprázdněna, zápis dopředného protokolu lze přehrát aktualizace. Bez WAL, pokud **RegionServer** dojde k chybě před vyprázdnění aktualizace **HFile**, všechny tyto aktualizace jsou ztraceny.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Funkce zrychlených zápisů v Azure HDInsight pro Apache HBase

Funkce Accelerated Writes řeší problém s vyšší latencí zápisu způsobeným použitím protokolů dopředného zápisu, které jsou v cloudovém úložišti.  Funkce Accelerated Writes pro clustery HDInsight Apache HBase připojuje prémiové disky spravované ssd ke každému serveru RegionServer (pracovní uzel). Zápis dopředných protokolů jsou pak zapsány do souborového systému Hadoop (HDFS) připojené ho na těchto prémiových spravovaných disků namísto cloudového úložiště.  Prémiové spravované disky používají disky SSD a nabízejí vynikající vstupně-va/o výkon s odolností proti chybám.  Na rozdíl od nespravovaných disků, pokud jedna úložná jednotka přestane být, nebude to mít vliv na ostatní jednotky úložiště ve stejné sadě dostupnosti.  V důsledku toho spravované disky poskytují nízkou latenci zápisu a lepší odolnost proti chybám pro vaše aplikace. Další informace o diskech spravovaných Azure najdete [v tématu Úvod do spravovaných disků Azure](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Jak povolit zrychlené zápisy pro HBase v HDInsight

Chcete-li vytvořit nový cluster HBase s funkcí Zrychlené zápisy, postupujte podle pokynů v [části Nastavení clusterů v hdinsightu,](../hdinsight-hadoop-provision-linux-clusters.md) dokud nedosáhnete **kroku 3, úložiště**. V části **Nastavení Metastore**zaškrtněte políčko vedle **položky Povolit zápisy akcelerovaných zápisů HBase**. Potom pokračujte zbývajícími kroky pro vytvoření clusteru.

![Povolit možnost zrychleného zápisu pro HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>Další aspekty

Chcete-li zachovat odolnost dat, vytvořte cluster s minimálně třemi pracovními uzly. Po vytvoření nelze vertikálně snížit kapacitu clusteru na méně než tři pracovní uzly.

Před odstraněním clusteru vyprázdněte nebo zakažte tabulky HBase, abyste neztratili data protokolu dopředného zápisu.

```
flush 'mytable'
```

```
disable 'mytable'
```

Při škálování clusteru postupujte podle podobných kroků: vyprázdnění tabulek a zakázání tabulek, chcete-li zastavit příchozí data. Cluster nelze škálovat na méně než tři uzly.

Následující kroky zajistí úspěšné škálování a vyhnout se možnosti namenode přechodu do nouzového režimu z důvodu nedostatečně replikovaných nebo dočasných souborů.

Pokud se váš namenode po zmenšení rozsahu přejde do nouzového režimu, použijte příkazy HDFS k rereplikaci podreplikovaných bloků a získejte hdfs z nouzového režimu. Tato rereplikace vám umožní úspěšně restartovat HBase.

## <a name="next-steps"></a>Další kroky

* Oficiální dokumentace Apache HBase týkající se [funkce Zápis dopředného protokolu](https://hbase.apache.org/book.html#wal)
* Chcete-li upgradovat cluster HDInsight Apache HBase tak, aby používal akcelerované zápisy, přečtěte si téma [Migrace clusteru Apache HBase na novou verzi](apache-hbase-migrate-new-version.md).

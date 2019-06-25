---
title: Azure HDInsight Accelerated zápisy pro Apache HBase
description: Poskytuje přehled služby Azure HDInsight Accelerated zapíše funkci, která používá spravované disky úrovně premium pro zlepšení výkonu Apache HBase zápisu dopředu protokolu.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 4/29/2019
ms.openlocfilehash: 219899c2e336f544ff6572589cc79f84f555490d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233838"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Azure HDInsight Accelerated zápisy pro Apache HBase

Tento článek obsahuje na pozadí **Accelerated zapíše** funkce pro Apache HBase v Azure HDInsight a jak ho lze účinně ke zlepšení výkonu zápisu. **Urychlení zápisy** používá [spravované disky Azure úrovně premium SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) ke zlepšení výkonu z Apache HBase zápisu dopředu protokolu (WAL). Další informace o Apache HBase najdete v tématu [co je Apache HBase v HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Přehled architektury HBase

V HBase **řádek** obsahuje jeden nebo více **sloupce** a je identifikován **klíč řádku**. Více řádků tvoří **tabulky**. Sloupce obsahují **buňky**, které jsou verze časovým razítkem hodnoty ve sloupci. Sloupce jsou seskupené do **rodin sloupců**, a všechny sloupce v rodině sloupců jsou uloženy v souborech úložiště volána **HFiles**.

**Oblasti** v HBase, které se používají k vyrovnávání zatížení zpracování dat. Řádky v tabulce HBase nejprve ukládá v jedné oblasti. Řádky jsou rozděleny mezi více oblastí jako množství dat v tabulce zvyšuje. **Oblastní servery** může zpracovávat požadavky pro více oblastí.

## <a name="write-ahead-log-for-apache-hbase"></a>Zápis dopředné protokolování pro Apache HBase

HBase nejprve zapíše aktualizací dat na typ protokol transakcí volat napsat dopředu protokolu (WAL). Po aktualizaci je uložen v WAL, jsou zapsána do v paměťově **paměťového úložiště**. Když data v paměti dosáhne své maximální kapacity, se zapíšou na disk jako **hfile –** .

Pokud **RegionServer** selže nebo přestane být k dispozici před vyprázdní metody zapsat dopředu protokolu je možné přehrát aktualizace. Bez WAL Pokud **RegionServer** dojde k chybě před vyprazdňování aktualizace **hfile –** , všechny tyto aktualizace se ztratí.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Zrychlené zápisy funkce v Azure HDInsight pro Apache HBase

Funkce Accelerated zapíše řeší problém vyšší latence zápisu, způsobil pomocí zápisu dopředu protokoly, které jsou v cloudovém úložišti.  Clustery Accelerated zapíše funkce pro HDInsight Apache HBase, bude k obrazci SSD managed disks úrovně premium pro každý RegionServer (pracovní uzel). Zapsat že dopředu protokoly jsou pak zapsány do souboru systému HDFS (Hadoop) připojené na tyto managed disks úrovně premium – místo cloudového úložiště.  Managed disks úrovně Premium – použití Solid-State disků (SSD) a nabízejí mimořádný výkon vstupně-výstupní operace s odolností proti chybám.  Na rozdíl od nespravované disky Pokud jedna jednotka úložiště ocitne mimo provoz, nebude to mít vliv jiné jednotky úložiště ve stejné sadě dostupnosti.  V důsledku toho spravované disky nabízejí nízké latence zápisu a lepší odolnost proti chybám pro vaše aplikace. Další informace o službě Azure managed disks najdete v tématu [Úvod do služby Azure managed disks](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Jak povolit akcelerované zapíše pro HBase v HDInsight

K vytvoření nového clusteru HBase pomocí funkce Accelerated zapisuje, postupujte podle kroků v [nastavení clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) dokud nedosáhnete **kroku 3, úložiště**. V části **nastavení Metastoru**, klikněte na zaškrtávací políčko vedle položky **povolit akcelerované zapíše (preview)** . Pokračujte ve zbývajících krocích pro vytváření clusteru.

![Zrychlené zápisy volbu Povolit pro HDInsight Apache HBase](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>Další důležité informace

Pokud chcete zachovat odolnost dat, vytvoření clusteru s minimálně tři pracovní uzly. Po vytvoření nejde vertikálně snížit kapacitu clusteru na méně než tři pracovní uzly.

Vyprázdnění nebo zakázat tabulky HBase před odstraněním clusteru tak, aby neztratili data zapsat protokolu dopředu.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Další postup

* Oficiální dokumentaci Apache HBase na [funkce zápisu protokolu dopředu](https://hbase.apache.org/book.html#wal)
* Upgrade clusteru HDInsight Apache HBase používat urychlené zapisuje, naleznete v tématu [migrovat na novou verzi clusteru Apache HBase](apache-hbase-migrate-new-version.md).

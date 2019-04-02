---
title: Azure HDInsight rozšířeného zapíše pro Apache HBase (Náhled)
description: Poskytuje přehled služby Azure HDInsight rozšířeného zapíše funkci, která používá spravované disky úrovně premium pro zlepšení výkonu Apache HBase zápisu dopředu protokolu.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 3/27/2019
ms.openlocfilehash: b0c71d0f101ea262b6ce56c845ef9f375a7de85e
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804203"
---
# <a name="azure-hdinsight-enhanced-writes-for-apache-hbase-preview"></a>Azure HDInsight rozšířeného zapíše pro Apache HBase (Náhled)

Tento článek obsahuje na pozadí **rozšířeného zapíše** funkce pro Apache HBase v Azure HDInsight a jak ho lze účinně ke zlepšení výkonu zápisu. **Vylepšené zápisy** používá [spravované disky Azure úrovně premium SSD](../../virtual-machines/linux/disks-types.md#premium-ssd) ke zlepšení výkonu z Apache HBase zápisu dopředu protokolu (WAL). Další informace o Apache HBase najdete v tématu [co je Apache HBase v HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Přehled architektury HBase

V HBase **řádek** obsahuje jeden nebo více **sloupce** a je identifikován **klíč řádku**. Více řádků tvoří **tabulky**. Sloupce obsahují **buňky**, které jsou verze časovým razítkem hodnoty ve sloupci. Sloupce jsou seskupené do **rodin sloupců**, a všechny sloupce v rodině sloupců jsou uloženy v souborech úložiště volána **HFiles**.

**Oblasti** v HBase, které se používají k vyrovnávání zatížení zpracování dat. HBase nejprve ukládá řádky z tabulky v jedné oblasti a pak šíří řádky v několika oblastech jako množství dat v tabulce zvyšuje. **Oblastní servery** může zpracovávat požadavky pro více oblastí.

## <a name="write-ahead-log-for-apache-hbase"></a>Zápis dopředné protokolování pro Apache HBase

Aktualizace dat v HBase se nejprve zapisují do typu protokol transakcí volat napsat dopředu protokolu (WAL). Po aktualizaci je uložen v WAL, jsou zapsána do v paměťově **paměťového úložiště**. Když data v paměti dosáhne své maximální kapacity, se zapíšou na disk jako **hfile –**.

Pokud **RegionServer** selže nebo přestane být k dispozici před vyprázdní metody zapsat dopředu protokolu je možné přehrát aktualizace. Bez WAL Pokud **RegionServer** došlo k chybě před vyprazdňování aktualizace **hfile –**, všechny tyto aktualizace se ztratí.

## <a name="enhanced-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Rozšířené funkce zápisu v Azure HDInsight pro Apache HBase

Funkce rozšířeného zapíše řeší problém vyšší latence zápisu, způsobil pomocí zápisu dopředu protokoly, které jsou v cloudovém úložišti.  Funkce rozšířeného zapíše pro clustery HDInsight Apache HBase, bude k obrazci premium SSD spravovaných disků do každé RegionServer (pracovní uzel). Zapsat že dopředu protokoly jsou pak zapsány do souboru systému HDFS (Hadoop) připojené na tyto spravované disky úrovně premium místo cloudového úložiště.  Spravované disky úrovně Premium používat Solid-State disky (SSD) a nabízejí mimořádný výkon vstupně-výstupní operace s odolností proti chybám.  Na rozdíl od nespravované disky Pokud jedna jednotka úložiště ocitne mimo provoz, nebude to mít vliv jiné jednotky úložiště ve stejné sadě dostupnosti.  V důsledku toho spravované disky nabízejí nízké latence zápisu a lepší odolnost proti chybám pro vaše aplikace. Další informace o Azure managed disks, naleznete v tématu [Úvod do služby Azure managed disks](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-enhanced-writes-for-hbase-in-hdinsight"></a>Postup pro povolení rozšířeného zapíše pro HBase v HDInsight

K vytvoření nového clusteru HBase pomocí funkce rozšířeného zapisuje, postupujte podle kroků v [nastavení clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) dokud nedosáhnete **kroku 3, úložiště**. V části **nastavení Metastoru**, klikněte na zaškrtávací políčko vedle položky **povolení rozšířeného zapíše (preview)**. Pokračujte ve zbývajících krocích pro vytváření clusteru.

![Vylepšené zápisy volbu Povolit pro HDInsight Apache HBase](./media/apache-hbase-enhanced-writes/enhanced-writes-cluster-creation.jpg)

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
* Upgrade clusteru HDInsight Apache HBase pomocí rozšířeného zapisuje, naleznete v tématu [migrovat na novou verzi clusteru Apache HBase](apache-hbase-migrate-new-version.md).

---
title: Zrychlené zápisy služby Azure HDInsight pro Apache HBase
description: Poskytuje přehled funkce akcelerovaného zápisu Azure HDInsight, která využívá službu Managed disks úrovně Premium ke zvýšení výkonu protokolu Apache HBA pro zápis.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/24/2020
ms.openlocfilehash: 99253aa2e7e2e1f3f58f2ab7d5c40a695c2b9690
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654850"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Zrychlené zápisy služby Azure HDInsight pro Apache HBase

Tento článek poskytuje základní informace o funkci **akcelerovaných zápisů** pro Apache HBA ve službě Azure HDInsight a o tom, jak je možné ji efektivně využít ke zlepšení výkonu zápisu. **Urychlené zápisy** využívají [spravované disky Azure Premium SSD](../../virtual-machines/disks-types.md#premium-ssd) ke zvýšení výkonu protokolu Wal (Apache HBA Write). Další informace o Apache HBA najdete [v tématu Co je Apache HBA v HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Přehled architektury HBA

V adaptérech HBA se **řádek** skládá z jednoho nebo více **sloupců** a je identifikovaný **klíčovým řádkem**. **Tabulka**je tvořena více řádky. Sloupce obsahují **buňky**, které jsou verze hodnoty v tomto sloupci s časovým razítkem. Sloupce jsou seskupeny do **rodin sloupců**a všechny sloupce v řadě sloupců jsou uloženy společně v úložištích souborů s názvem **HFiles**.

**Oblasti** v adaptérech HBA slouží k vyrovnávání zatížení zpracování dat. HBA nejprve uloží řádky tabulky v jedné oblasti. Řádky jsou rozloženy mezi více oblastí, když se zvyšuje objem dat v tabulce. **Servery oblastí** mohou zpracovávat žádosti pro více oblastí.

## <a name="write-ahead-log-for-apache-hbase"></a>Zápis do protokolu pro Apache HBA

HBA nejprve zapisuje aktualizace dat do typu protokolu potvrzení nazvané protokol zápisu předem (WAL). Po uložení aktualizace do WAL se zapíše do **setSize paměťového úložiště**v paměti. Když data v paměti dosáhnou své maximální kapacity, je zapsána na disk jako **HFile**.

Pokud dojde k selhání **RegionServer** nebo dojde k nedostupnosti předtím, než se setSize paměťového úložiště vyprázdní, můžete k přehrání aktualizací použít protokol zápisu předem. Bez WAL, pokud dojde k chybě **RegionServer** před vyprázdněním aktualizací na **HFile**, ztratí všechny tyto aktualizace.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Funkce akcelerovaného zápisu ve službě Azure HDInsight pro Apache HBA

Funkce akcelerované zápisy řeší potíže s vyšší latencí zápisu způsobenou použitím protokolů pro zápis, které jsou v cloudovém úložišti.  Funkce akcelerované zápisy pro clustery HDInsight Apache HBA, připojuje disky spravované na disk SSD ke každému RegionServer (pracovní uzel). Protokoly pro zápis se pak zapisují do systému souborů Hadoop (HDFS) připojeného na tyto úrovně Premium Managed-disks místo cloudového úložiště.  Managed Premium – disky používají Solid-State disky (SSD) a nabízejí vynikající vstupně-výstupní výkon s odolností proti chybám.  Na rozdíl od nespravovaných disků v případě výpadku jedné jednotky úložiště nebude mít vliv na jiné jednotky úložiště ve stejné skupině dostupnosti.  Výsledkem je, že spravované disky poskytují nízkou latenci zápisu a lepší odolnost pro vaše aplikace. Další informace o discích spravovaných v Azure najdete v tématu [Úvod do služby Azure Managed disks](../../virtual-machines/managed-disks-overview.md).

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Jak povolit urychlené zápisy pro adaptéry HBA v HDInsight

Pokud chcete vytvořit nový cluster HBA s funkcí akcelerované zápisy, postupujte podle kroků v části [Nastavení clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) , dokud nedosáhnete **kroku 3, úložiště**. V části **Nastavení metastore**zaškrtněte políčko vedle **Povolit urychlení zápisů HBA**. Pak pokračujte zbývajícími kroky pro vytvoření clusteru.

![Povolit možnost urychleného zápisu pro HDInsight Apache HBA](./media/apache-hbase-accelerated-writes/azure-portal-cluster-storage-hbase.png)

## <a name="other-considerations"></a>Další důležité informace

Aby se zajistila odolnost dat, vytvořte cluster s minimálně třemi pracovními uzly. Po vytvoření se cluster nedá škálovat dolů na míň než tři pracovní uzly.

Vyprázdněte nebo zakažte tabulky HBA před odstraněním clusteru, abyste nepřišli o data protokolu pro zápis.

```
flush 'mytable'
```

```
disable 'mytable'
```

Při horizontálním navýšení kapacity clusteru postupujte podle podobných kroků: vyprázdněte tabulky a zakažte, aby se příchozí data zastavila. Cluster nemůžete škálovat dolů na méně než tři uzly.

Pomocí těchto kroků zajistíte úspěšné snížení kapacity a vyhnete se tak možnosti namenode v bezpečném režimu v důsledku replikovaných nebo dočasných souborů.

Pokud vaše namenode přejde do safemode po horizontálním snížení kapacity, použijte příkazy HDFS k opětovné replikaci replikovaných bloků a k získání HDFS z bezpečného režimu. Tato opakovaná replikace vám umožní úspěšně restartovat adaptéry HBA.

## <a name="next-steps"></a>Další kroky

* Oficiální dokumentace k Apache HBA v rámci [funkce protokolu pro zápis do dávky](https://hbase.apache.org/book.html#wal)
* Pokud chcete upgradovat cluster HDInsight Apache HBA tak, aby používal urychlené zápisy, přečtěte si téma [migrace clusteru Apache HBA na novou verzi](apache-hbase-migrate-new-version.md).

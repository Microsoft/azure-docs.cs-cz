---
title: Optimalizace Apache HBA s Apache Ambari ve službě Azure HDInsight
description: Ke konfiguraci a optimalizaci adaptérů Apache HBA použijte webové uživatelské rozhraní Apache Ambari.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: b262e07bd07320e4b10b12a2f2cf07b97e58c61e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91821693"
---
# <a name="optimize-apache-hbase-with-apache-ambari-in-azure-hdinsight"></a>Optimalizace Apache HBA s Apache Ambari ve službě Azure HDInsight

Apache Ambari je webové rozhraní pro správu a monitorování clusterů HDInsight. Úvod do webového uživatelského rozhraní Ambari najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

Konfigurace Apache HBA je upravena na kartě **Konfigurace HBA** . Následující části popisují některá z důležitých nastavení konfigurace, která ovlivňují výkon adaptérů HBA.

## <a name="set-hbase_heapsize"></a>Nastavit HBASE_HEAPSIZE

Velikost haldy HBA určuje maximální velikost haldy, která bude použita v megabajtech podle *oblasti* a *hlavních* serverů. Výchozí hodnota je 1 000 MB. Tato hodnota by měla být vyladěna pro zatížení clusteru.

1. Pokud ho chcete upravit, přejděte do podokna **Advanced HBA-ENV** na kartě **Konfigurace** HBA a pak vyhledejte `HBASE_HEAPSIZE` nastavení.

1. Změňte výchozí hodnotu na 5 000 MB.

    !["Apache Ambari Hbas velikost haldy paměti"](./media/optimize-hbase-ambari/ambari-hbase-heapsize.png)

## <a name="optimize-read-heavy-workloads"></a>Optimalizace úloh pro čtení a vysoké zatížení

Následující konfigurace jsou důležité pro zlepšení výkonu úloh náročných na čtení.

### <a name="block-cache-size"></a>Velikost mezipaměti bloků

Bloková mezipaměť je mezipaměť pro čtení. Jeho velikost je ovládána `hfile.block.cache.size` parametrem. Výchozí hodnota je 0,4, což je 40 procent celkové paměti serveru oblasti. Čím větší je velikost mezipaměti bloků, tím rychlejší náhodné čtení bude.

1. Chcete-li tento parametr změnit, přejděte na kartu **Nastavení** na kartě **Konfigurace** HBA a pak vyhledejte **% z RegionServer přidělených pro vyrovnávací paměti pro čtení**.

    ![Velikost mezipaměti bloků paměti Apache HBA](./media/optimize-hbase-ambari/hbase-block-cache-size.png)

1. Chcete-li změnit hodnotu, vyberte ikonu **Upravit** .

### <a name="memstore-size"></a>Velikost setSize paměťového úložiště

Všechny úpravy jsou uloženy v vyrovnávací paměti, která se označuje jako *setSize paměťového úložiště*. Tato vyrovnávací paměť zvyšuje celkové množství dat, která lze zapsat na disk v rámci jedné operace. Také urychlují přístup k nedávným úpravám. Velikost setSize paměťového úložiště je definována následujícími dvěma parametry:

* `hbase.regionserver.global.memstore.UpperLimit`: Definuje maximální procento serveru oblasti, který může setSize paměťového úložiště kombinovat.

* `hbase.regionserver.global.memstore.LowerLimit`: Definuje minimální procento serveru oblasti, který může setSize paměťového úložiště kombinovat.

Pro optimalizaci pro náhodná čtení můžete snížit horní a dolní limit setSize paměťového úložiště.

### <a name="number-of-rows-fetched-when-scanning-from-disk"></a>Počet řádků načtených při kontrole z disku

`hbase.client.scanner.caching`Nastavení definuje počet řádků načtených z disku při `next` volání metody na skeneru.  Výchozí hodnota je 100. Čím vyšší je počet vzdálených volání z klienta na server oblasti, což vede k rychlejšímu prověřování. Toto nastavení ale taky zvýší tlak paměti na straně klienta.

![Apache HBA počet načtených řádků](./media/optimize-hbase-ambari/hbase-num-rows-fetched.png)

> [!IMPORTANT]  
> Nenastavujte hodnotu tak, aby čas mezi voláním další metody na skeneru byl větší než časový limit skeneru. Doba trvání časového limitu skeneru je definována `hbase.regionserver.lease.period` vlastností.

## <a name="optimize-write-heavy-workloads"></a>Optimalizace náročných úloh pro zápis

Následující konfigurace jsou důležité pro zlepšení výkonu náročných úloh.

### <a name="maximum-region-file-size"></a>Maximální velikost souboru oblasti

HBA ukládají data do interního formátu souboru s názvem *HFile*. Vlastnost `hbase.hregion.max.filesize` definuje velikost jednoho HFile pro oblast.  Oblast je rozdělena do dvou oblastí, pokud je součet všech HFiles v oblasti větší než toto nastavení.

!["Apache Hbas HRegion Max velikost souboru"](./media/optimize-hbase-ambari/hbase-hregion-max-filesize.png)

Čím větší je velikost souboru oblasti, tím menší je počet rozdělení. Velikost souboru můžete zvětšit a určit tak hodnotu, která bude mít za následek maximální výkon zápisu.

### <a name="avoid-update-blocking"></a>Vyhnout se blokování aktualizací

* Vlastnost `hbase.hregion.memstore.flush.size` definuje velikost, při které je setSize paměťového úložiště vyprázdněna na disk. Výchozí velikost je 128 MB.

* Multiplikátor bloku pro oblasti HBA je definován `hbase.hregion.memstore.block.multiplier` . Výchozí hodnota je 4. Maximální povolená hodnota je 8.

* HBA zablokuje aktualizace, pokud setSize paměťového úložiště `hbase.hregion.memstore.flush.size`  *  `hbase.hregion.memstore.block.multiplier` bajty ().

    S výchozími hodnotami velikosti vyprázdnění a násobitele bloku jsou aktualizace zablokované, pokud setSize paměťového úložiště je 128 × 4 = 512 MB. Chcete-li snížit počet blokování aktualizací, zvyšte hodnotu `hbase.hregion.memstore.block.multiplier` .

![Multiplikátor bloku oblasti Apache HBA](./media/optimize-hbase-ambari/hbase-hregion-memstore-block-multiplier.png)

## <a name="define-memstore-size"></a>Definovat velikost setSize paměťového úložiště

Velikost setSize paměťového úložiště je definována `hbase.regionserver.global.memstore.upperLimit` `hbase.regionserver.global.memstore.lowerLimit` parametry a. Nastavení těchto hodnot je stejné jako u každého dalšího, při zápisu dojde k pozastavení, což také způsobuje častější vyprazdňování a vede k vyššímu výkonu zápisu.

## <a name="set-memstore-local-allocation-buffer"></a>Nastavit místní vyrovnávací paměť pro přidělení setSize paměťového úložiště

Využití vyrovnávací paměti pro místní přidělování setSize paměťového úložiště je určeno vlastností `hbase.hregion.memstore.mslab.enabled` . Pokud je povoleno (true), toto nastavení zabrání fragmentaci haldy během operace silného zápisu. Výchozí hodnotou je hodnota true.

![HBA. hregion. setSize paměťového úložiště. mslab. Enabled](./media/optimize-hbase-ambari/hbase-hregion-memstore-mslab-enabled.png)

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optimalizace clusterů](./hdinsight-changing-configs-via-ambari.md)
* [Optimalizace Apache Hivu](./optimize-hive-ambari.md)
* [Optimalizace Apache Pigu](./optimize-pig-ambari.md)

---
title: Optimalizace pro doporučení pro službu cluster Advisor
titleSuffix: Azure HDInsight
description: Optimalizujte Apache HBA pro doporučení služby Cluster Advisor ve službě Azure HDInsight.
author: ramkrish86
ms.author: ramvasu
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/03/2021
ms.openlocfilehash: acb7a6aeb4084949be3b0ad40e770a414a13ab6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943014"
---
# <a name="apache-hbase-advisories-in-azure-hdinsight"></a>Poradci pro Apache HBA ve službě Azure HDInsight

Tento článek popisuje několik poradců, které vám pomůžou s optimalizací výkonu Apache HBA ve službě Azure HDInsight. 

## <a name="optimize-hbase-to-read-most-recently-written-data"></a>Optimalizace adaptérů HBA pro čtení naposledy zapsaných dat

Pokud váš UseCase zahrnuje čtení naposledy zapsaných dat z adaptérů HBA, může vám tento poradce pomáhat. Pro zajištění vysokého výkonu je optimální, aby byly HBA čteny z setSize paměťového úložiště namísto vzdáleného úložiště.

Poradce pro dotazy indikuje, že pro danou rodinu sloupců v tabulce > 75% čtení, která se zpracovávají z setSize paměťového úložiště. Tento indikátor naznačuje, že i v případě, že na setSize paměťového úložiště dojde k vyprázdnění, je potřeba mít k dispozici poslední soubor, který musí být v mezipaměti. Data se nejdřív napíší, aby setSize paměťového úložiště systém přistupuje k posledním datům. Je pravděpodobné, že interní vlákna vyprázdnění adaptérů zjistila, že daná oblast dosáhla 128M (výchozí) velikosti a může aktivovat vyprázdnění. Tento scénář se stane i s nejaktuálnějšími daty, která byla zapsána při 128M velikosti setSize paměťového úložiště. Pozdější čtení těchto posledních záznamů proto může vyžadovat čtení souboru místo z setSize paměťového úložiště. Proto je nejlepší optimalizovat, že i nedávno vyčištěná data můžou být uložená v mezipaměti.

Chcete-li optimalizovat poslední data v mezipaměti, vezměte v úvahu následující nastavení konfigurace:

1. Nastavte `hbase.rs.cacheblocksonwrite` na `true` . Tato výchozí konfigurace v clusterech HDInsight je `true` , takže ověřte, že není nastavená na `false` .

2. Zvyšte `hbase.hstore.compactionThreshold` hodnotu, abyste se vyhnuli komprimaci v zahájení. Ve výchozím nastavení touto hodnotou je `3`. Můžete ji zvětšit na vyšší hodnotu `10` , třeba.

3. Pokud budete postupovat podle pokynů v části Krok 2 a compactionThreshold, pak změňte `hbase.hstore.compaction.max` na vyšší hodnotu `100` , například, a také hodnotu konfigurace `hbase.hstore.blockingStoreFiles` na vyšší hodnotu `300` .

4. Pokud si jste jisti, že potřebujete číst jenom poslední data, nastavte konfiguraci na `hbase.rs.cachecompactedblocksonwrite` **zapnuto**. Tato konfigurace oznamuje systému, že i když dojde k komprimaci, data zůstanou v mezipaměti. Konfigurace je možné nastavit také na úrovni rodiny. 

   V prostředí HBA spusťte následující příkaz pro nastavení `hbase.rs.cachecompactedblocksonwrite` Konfigurace:
   
   ```
   alter '<TableName>', {NAME => '<FamilyName>', CONFIGURATION => {'hbase.hstore.blockingStoreFiles' => '300'}}
   ```

5. Bloková mezipaměť může být pro danou rodinu v tabulce vypnutá. Ujistěte se, že je tato **funkce zapnutá** pro rodiny, které mají nejaktuálnější data čtení. Ve výchozím nastavení je zablokovaná mezipaměť zapnutá pro všechny rodiny v tabulce. V případě, že jste zakázali blokovou mezipaměť pro rodinu a potřebujete ji zapnout, použijte příkaz ALTER z prostředí HBA.

   Tyto konfigurace pomůžou zajistit, aby data byla dostupná v mezipaměti a aby se neprošla komprimací nedávných dat. Pokud je ve vašem scénáři přípustný hodnota TTL, zvažte použití komprimace na základě data. Další informace najdete v [Referenční příručce k Apache HBA: komprimace data s vrstvami](https://hbase.apache.org/book.html#ops.date.tiered)  

## <a name="optimize-the-flush-queue"></a>Optimalizace fronty vyprázdnění

Tento informační zpravodaj indikuje, že je možné vyladit HBA vyprázdnění. Aktuální konfigurace obslužných rutin vyprázdnění nemusí být dostatečně vysoka pro zpracování s přenosem dat pro zápis, což může vést k zpomalení vyprázdnění.

V uživatelském rozhraní serveru oblasti si všimněte, že fronta vyprázdnění překračuje 100. Tato prahová hodnota znamená, že vyprázdnění jsou pomalé a možná budete muset ladit   `hbase.hstore.flusher.count` konfiguraci. Ve výchozím nastavení je hodnota 2. Ujistěte se, že maximální počet vláken vyprázdnění není vyšší než 6.

Kromě toho si přečtěte, jestli máte doporučení pro optimalizaci počtu oblastí. Pokud ano, doporučujeme vám vyzkoušet ladění oblastí, abyste zjistili, jestli to pomáhá rychleji vyprázdnit. V opačném případě vám může pomáhat vyladit vlákna vyprázdnění.

## <a name="region-count-tuning"></a>Optimalizace počtu oblastí

Informační zpravodaj pro vyladění počtu oblastí indikuje, že adaptéry HBA obsahují blokované aktualizace a počet oblastí může být větší než optimální podporovaná velikost haldy. Můžete vyladit velikost haldy, velikost setSize paměťového úložiště a počet oblastí.

Jako příklad scénáře:

- Předpokládejme, že velikost haldy pro server oblasti je 10 GB. Ve výchozím nastavení `hbase.hregion.memstore.flush.size` je to `128M` . Výchozí hodnota pro `hbase.regionserver.global.memstore.size` je `0.4` . To znamená, že z 10 GB se přiděluje 4 GB pro setSize paměťového úložiště (globálně).

- Předpokládáme, že je ještě rovnoměrné rozdělení zátěže pro zápis ve všech oblastech a za předpokladu, že každé oblasti roste jenom až 128 MB, a maximální počet oblastí v této instalaci jsou `32` oblasti. Pokud je daný server oblasti nakonfigurovaný tak, aby měl 32 oblastí, systém lépe brání v blokování aktualizací.

- U těchto nastavení je počet oblastí 100. 4 GB globálních setSize paměťového úložiště je teď rozdělené mezi 100 oblastí. To znamená, že každá oblast získá pro setSize paměťového úložiště jenom 40 MB. Když jsou zápisy jednotné, systém se často vyprazdňuje a menší velikost pořadí < 40 MB. Příliš mnoho vláken vyprázdnění může zvýšit rychlost vyprázdnění `hbase.hstore.flusher.count` .

Poradenský poradce znamená, že by bylo dobré zvážit počet oblastí na server, velikost haldy a globální konfiguraci setSize paměťového úložiště velikosti společně s optimalizací vyprázdnit vlákna, aby se zabránilo tomu, že se aktualizace zablokují.

## <a name="compaction-queue-tuning"></a>Vyladění fronty komprese

Pokud se fronta komprimace HBA zvětšuje na více než 2000 a probíhá pravidelně, můžete zvětšit vlákna komprimace na větší hodnotu.

Pokud dojde k nadměrnému počtu souborů pro komprimaci, může to vést k většímu využití haldy, které souvisí s tím, jak soubory pracují se systémem souborů Azure. Proto je lepší dokončit komprimaci co nejrychleji. Ve starších clusterech můžou konfigurace komprimace týkající se omezování způsobit pomalejší rychlost komprimace.

Ověřte konfigurace `hbase.hstore.compaction.throughput.lower.bound` a `hbase.hstore.compaction.throughput.higher.bound` . Pokud jsou už nastavené na 50M a 100 milionů, ponechte je v tom, jak jsou. Pokud však tato nastavení nakonfigurujete na nižší hodnotu (což byl případ se staršími clustery), změňte omezení na 50M a 100 milionů.

Konfigurace jsou `hbase.regionserver.thread.compaction.small` a `hbase.regionserver.thread.compaction.large` (výchozí hodnota je 1).
Cap maximální hodnota této konfigurace bude menší než 3.

## <a name="full-table-scan"></a>Úplná kontrola tabulky

Úplný informační zpravodaj tabulky indikuje, že více než 75% vydaných kontrol jsou úplné kontroly tabulek a oblastí. Můžete přesměrovat způsob, jakým kód volá kontroly pro zlepšení výkonu dotazů. Vezměte v úvahu následující postupy:

* Pro každou kontrolu nastavte správný řádek začátek a zastavení.

* Použijte rozhraní **MultiRowRangeFilter** API, abyste se mohli dotazovat na různé rozsahy v jednom volání prověřování. Další informace najdete v [dokumentaci k rozhraní MultiRowRangeFilter API](https://hbase.apache.org/2.1/apidocs/org/apache/hadoop/hbase/filter/MultiRowRangeFilter.html).

* V případech, kdy potřebujete celou tabulku nebo oblast prohledávání, zkontrolujte, jestli existuje možnost vyhnout se použití mezipaměti pro tyto dotazy, aby jiné dotazy, které používají mezipaměť, nemusely vyřadit bloky, které jsou horké. Aby kontroly nepoužívaly mezipaměť, použijte rozhraní API pro **vyhledávání** s možností **setCaching (false)** ve vašem kódu: 

   ```
   scan#setCaching(false)
   ```
   
## <a name="next-steps"></a>Další kroky

[Optimalizace Apache HBA pomocí Ambari](../optimize-hbase-ambari.md)

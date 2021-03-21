---
title: Optimalizace pro Apache prasete s Apache Ambari ve službě Azure HDInsight
description: Použijte webové uživatelské rozhraní Apache Ambari ke konfiguraci a optimalizaci nástroje Apache prasete.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 690cfc66d0f08e63ac6ef1d21b6722fed59eca14
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939007"
---
# <a name="optimize-apache-pig-with-apache-ambari-in-azure-hdinsight"></a>Optimalizace pro Apache prasete s Apache Ambari ve službě Azure HDInsight

Apache Ambari je webové rozhraní pro správu a monitorování clusterů HDInsight. Úvod do webového uživatelského rozhraní Ambari najdete v tématu [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md).

Vlastnosti pro Apache prasete se dají upravit z webového uživatelského rozhraní Ambari pro optimalizaci dotazů na prasaty. Úprava vlastností prasete z Ambari přímo upravuje vlastnosti prasete v `/etc/pig/2.4.2.0-258.0/pig.properties` souboru.

1. Chcete-li změnit vlastnosti prasete, přejděte na kartu **Konfigurace** prasete a potom rozbalte podokno **Upřesnit prase – vlastnosti** .

1. Vyhledejte, zrušte komentář a změňte hodnotu vlastnosti, kterou chcete upravit.

1. V pravém horním rohu okna vyberte **Uložit** a uložte novou hodnotu. Některé vlastnosti mohou vyžadovat restartování služby.

    ![Rozšířené vlastnosti vepřového prasete pro Apache](./media/optimize-pig-ambari/advanced-pig-properties.png)

> [!NOTE]  
> Všechna nastavení na úrovni relace přepíšou hodnoty vlastností v `pig.properties` souboru.

## <a name="tune-execution-engine"></a>Modul pro spouštění ladění

Ke spouštění skriptů prasete jsou k dispozici dva spouštěcí moduly: MapReduce a TEZ. Tez je optimalizovaný modul a je mnohem rychlejší než MapReduce.

1. Chcete-li upravit spouštěcí modul, vyhledejte v podokně **Upřesnit prase – vlastnosti** `exectype` .

1. Výchozí hodnota je **MapReduce**. Změňte ji na **tez**.

## <a name="enable-local-mode"></a>Povolit místní režim

Podobně jako u podregistru se používá místní režim k urychlení úloh s relativně menšími objemy dat.

1. Pokud chcete povolit místní režim, nastavte `pig.auto.local.enabled` na **true**. Výchozí hodnota je False.

1. Úlohy, jejichž velikost vstupních dat je menší než `pig.auto.local.input.maxbytes` hodnota vlastnosti, se považují za malé úlohy. Výchozí hodnota je 1 GB.

## <a name="copy-user-jar-cache"></a>Kopírovat mezipaměť jar uživatele

Prase kopíruje soubory JAR vyžadované UDF do distribuované mezipaměti a zpřístupňuje je pro uzly úloh. Tyto JAR se často nemění. Pokud je tato možnost povolená, umožňuje, aby se `pig.user.cache.enabled` jar umístil do mezipaměti pro opakované použití pro úlohy spouštěné stejným uživatelem. Výsledkem tohoto nastavení je menší zvýšení výkonu úlohy.

1. Pokud ho chcete povolit, nastavte `pig.user.cache.enabled` na true. Výchozí hodnotou je hodnota false.

1. Chcete-li nastavit základní cestu jar uložených v mezipaměti, nastavte `pig.user.cache.location` na základní cestu. Výchozí formát je `/tmp`.

## <a name="optimize-performance-with-memory-settings"></a>Optimalizace výkonu s nastavením paměti

Následující nastavení paměti může přispět k optimalizaci výkonu skriptu prasete.

* `pig.cachedbag.memusage`: Velikost paměti, která je přidělena kontejneru. Penalta je kolekce řazených kolekcí členů. Řazená kolekce členů je uspořádaná sada polí a pole je částí dat. Pokud jsou data v kontejneru za danou pamětí, přestanou být na disk. Výchozí hodnota je 0,2, která představuje 20 procent dostupné paměti. Tato paměť se sdílí napříč všemi penaltami v aplikaci.

* `pig.spill.size.threshold`: Penalty větší než tato prahová hodnota velikosti pro přesahy (v bajtech) se překročí na disk. Výchozí hodnota je 5 MB.

## <a name="compress-temporary-files"></a>Komprimovat dočasné soubory

Při provádění úlohy vygeneruje prase dočasné soubory. Při komprimaci dočasných souborů dojde ke zvýšení výkonu při čtení nebo zápisu souborů na disk. Pro komprimaci dočasných souborů lze použít následující nastavení.

* `pig.tmpfilecompression`: V případě hodnoty true povolí kompresi dočasného souboru. Výchozí hodnota je False.

* `pig.tmpfilecompression.codec`: Kompresní kodek, který se použije pro komprimaci dočasných souborů. Doporučené kompresní kodeky jsou LZO a přichycení k nižšímu využití procesoru.

## <a name="enable-split-combining"></a>Povolit kombinování rozdělení

Pokud je povoleno, malé soubory jsou zkombinovány pro méně úlohy mapy. Toto nastavení zlepšuje efektivitu úloh s mnoha malými soubory. Pokud ho chcete povolit, nastavte `pig.noSplitCombination` na true. Výchozí hodnota je False.

## <a name="tune-mappers"></a>Ladit mapovače

Počet mapovačů je řízen úpravou vlastnosti `pig.maxCombinedSplitSize` . Tato vlastnost určuje velikost dat, která mají být zpracována jednou mapou úlohy. Výchozí hodnota je výchozí velikost bloku systému souborů. Zvýšení této hodnoty vede k menšímu počtu úloh mapovače.

## <a name="tune-reducers"></a>Vyladění reduktorů

Počet reduktorů se vypočítá na základě parametru `pig.exec.reducers.bytes.per.reducer` . Parametr určuje počet zpracovaných bajtů na redukci, ve výchozím nastavení 1 GB. Chcete-li omezit maximální počet reduktorů, nastavte `pig.exec.reducers.max` vlastnost ve výchozím nastavení 999.

## <a name="next-steps"></a>Další kroky

* [Správa clusterů HDInsight pomocí webového uživatelského rozhraní Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [REST API Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optimalizace clusterů](./hdinsight-changing-configs-via-ambari.md)
* [Optimalizace Apache HBase](./optimize-hbase-ambari.md)
* [Optimalizace Apache Hivu](./optimize-hive-ambari.md)

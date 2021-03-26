---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 324d8b4c9fc53ca24e62fe339065d4452577cb1f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607214"
---
# <a name="azure-hdinsight-release-notes"></a>Poznámky k verzi Azure HDInsight

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky, kteří se týkají Open Source analýz v Azure.

Pokud se chcete přihlásit k odběru poznámky k verzi, Sledujte verze v [tomto úložišti GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-03242021"></a>Datum vydání: 03/24/2021

Tato verze se týká HDInsight 3,6 i HDInsight 4,0. Vydání HDInsight je zpřístupněno pro všechny oblasti více než několik dní. Datum vydání znamená datum vydání první oblasti. Pokud nevidíte níže uvedené změny, počkejte, až bude verze ve vaší oblasti v průběhu několika dnů živá.

## <a name="new-features"></a>Nové funkce
### <a name="spark-30-preview"></a>Spark 3,0 Preview
HDInsight přidal podporu [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) do HDInsight 4,0 jako funkci verze Preview. 

### <a name="kafka-24-preview"></a>Kafka 2,4 Preview
HDInsight přidalo podporu [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) do HDInsight 4,0 jako funkci verze Preview.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Přechod na Azure Virtual Machine Scale Sets
HDInsight teď pomocí virtuálních počítačů Azure zřídí cluster. Služba se postupně migruje na [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md). Celý proces může trvat měsíce. Po migraci vašich oblastí a předplatných se nově vytvořené clustery HDInsight spustí ve službě Virtual Machine Scale Sets bez akcí zákazníků. Neočekává se žádná neprůlomová změna.

## <a name="deprecation"></a>Vyřazení
V této verzi není zastaralá.

## <a name="behavior-changes"></a>Změny chování
### <a name="default-cluster-version-is-changed-to-40"></a>Výchozí verze clusteru se změní na 4,0.
Výchozí verze clusteru HDInsight se změnila z 3,6 na 4,0. Další informace o dostupných verzích najdete v tématu [dostupné verze](./hdinsight-component-versioning.md). Přečtěte si další informace o tom, co je v [HDInsight 4,0](./hdinsight-version-release.md)novinkou.

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Výchozí velikosti virtuálních počítačů clusteru se změnily na Ev3-Series. 
Výchozí velikosti virtuálních počítačů clusteru se změnily z řady D-Series na Ev3-Series. Tato změna se vztahuje na hlavní uzly a pracovní uzly. Aby se této změně neovlivnila Vaše testované pracovní postupy, zadejte velikosti virtuálních počítačů, které chcete použít v šabloně ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Prostředek síťového rozhraní není viditelný pro clustery běžící na Azure Virtual Machine Scale Sets.
HDInsight se postupně migruje na Azure Virtual Machine Scale Sets. Pro clustery, které používají službu Azure Virtual Machine Scale Sets, už nejsou síťová rozhraní pro virtuální počítače viditelná pro zákazníky.

## <a name="upcoming-changes"></a>Nadcházející změny
V nadcházejících verzích dojde k následujícím změnám.

### <a name="os-version-upgrade"></a>Upgrade verze operačního systému
HDInsight bude upgradovat verzi operačního systému z Ubuntu 16,04 na 18,04. Upgrade se dokončí do dubna 2021.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Podpora HDInsight 3,6 na konci 30 2021. června
Podpora HDInsight 3,6 bude ukončena. Počínaje formulářem červeně 30 2021 nemohou zákazníci vytvářet nové clustery HDInsight 3,6. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na HDInsight 4,0, aby se předešlo potenciálnímu přerušení systému/podpory.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 

## <a name="component-version-change"></a>Změna verze součásti
Přidali jsme podporu pro Spark 3.0.0 a Kafka 2.4.1 jako verzi Preview. V [tomto dokumentu](./hdinsight-component-versioning.md)najdete aktuální verze komponent pro HDInsight 4,0 a HDInsight 3,6.

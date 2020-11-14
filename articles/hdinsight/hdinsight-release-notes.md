---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 1ae4e2a1e0d67a0a09c19b517245ffc6d92d17df
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2020
ms.locfileid: "94629916"
---
# <a name="azure-hdinsight-release-notes"></a>Poznámky k verzi Azure HDInsight

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Shrnutí

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky, kteří se týkají Open Source analýz v Azure.

Pokud se chcete přihlásit k odběru poznámky k verzi, Sledujte verze v [tomto úložišti GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11092020"></a>Datum vydání: 11/09/2020

Tato verze se týká HDInsight 3,6 i HDInsight 4,0. Vydání HDInsight je zpřístupněno pro všechny oblasti více než několik dní. Datum vydání znamená datum vydání první oblasti. Pokud nevidíte níže uvedené změny, počkejte, až bude verze ve vaší oblasti v průběhu několika dnů živá.

## <a name="new-features"></a>Nové funkce
### <a name="hdinsight-identity-broker-hib-is-now-ga"></a>HDInsight identity broker (HIB) je teď GA.
HDInsight identity broker (HIB), který umožňuje ověřování OAuth pro clustery ESP, je teď v této verzi všeobecně dostupné. Clustery HIB vytvořené po této verzi budou mít nejnovější funkce HIB:
- Vysoká dostupnost (HA)
- Podpora pro Multi-Factor Authentication (MFA)
- Přihlášení federovaných uživatelů bez synchronizace hodnot hash hesel do AAD-DS Další informace najdete v [dokumentaci k Hib](https://docs.microsoft.com/azure/hdinsight/domain-joined/identity-broker).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Přechod na Azure Virtual Machine Scale Sets
HDInsight teď pomocí virtuálních počítačů Azure zřídí cluster. Od této verze se služba postupně migruje na [Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Celý proces může trvat měsíce. Po migraci vašich oblastí a předplatných se nově vytvořené clustery HDInsight spustí ve službě Virtual Machine Scale Sets bez akcí zákazníků. Neočekává se žádná neprůlomová změna.

## <a name="deprecation"></a>Vyřazení
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Vyřazení clusteru HDInsight 3,6 ML Services
Typ clusteru HDInsight 3,6 ML Services bude na konci podpory od prosince 31 2020. Zákazníci nevytvoří nové clustery 3,6 ML Services po prosinci 31 2020. Existující clustery se spustí, a to bez podpory Microsoftu. [Tady](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)se podívejte na vypršení platnosti podpory pro verze a typy clusterů HDInsight.

### <a name="disabled-vm-sizes"></a>Zakázané velikosti virtuálních počítačů
Od listopadu 16 2020 bude HDInsight zablokuje novým zákazníkům vytváření clusterů pomocí standand_A8, standand_A9, standand_A10 a standand_A11 velikostí virtuálních počítačů. Stávající zákazníci, kteří použili tyto velikosti virtuálních počítačů v posledních třech měsících, to neovlivní. Počínaje formulářem leden 9 2021 bude HDInsight blokovat všem zákazníkům vytváření clusterů pomocí standand_A8, standand_A9, standand_A10 a standand_A11 velikostí virtuálních počítačů. Existující clustery se spustí, jak je. Zvažte přechod na HDInsight 4,0, aby se předešlo potenciálnímu přerušení systému/podpory.

## <a name="behavior-changes"></a>Změny chování
Žádná změna chování pro tuto verzi.

## <a name="upcoming-changes"></a>Nadcházející změny
V nadcházejících verzích dojde k následujícím změnám.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Možnost výběru různých velikostí virtuálních počítačů s Zookeeper pro služby Spark, Hadoop a ML
HDInsight dnes nepodporuje přizpůsobení velikosti uzlů Zookeeper pro typy clusterů Spark, Hadoop a ML. Ve výchozím nastavení se A2_v2/a2 velikosti virtuálních počítačů, které jsou poskytovány zdarma. V nadcházející verzi můžete vybrat velikost virtuálního počítače Zookeeper, která je pro váš scénář nejvhodnější. Budou se účtovat uzly Zookeeper s velikostí virtuálního počítače jinou než A2_v2/a2. Virtuální počítače A2_v2 a a2 jsou stále poskytovány zdarma.

### <a name="default-cluster-version-will-be-changed-to-40"></a>Výchozí verze clusteru se změní na 4,0.
Od února 2021 se výchozí verze clusteru HDInsight změní z 3,6 na 4,0. Další informace o dostupných verzích najdete v tématu [dostupné verze](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions). Další informace o tom, co je nového ve službě [HDInsight 4,0](https://docs.microsoft.com/azure/hdinsight/hdinsight-version-release)

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Podpora HDInsight 3,6 na konci 30 2021. června
Podpora HDInsight 3,6 bude ukončena. Počínaje formulářem červeně 30 2021 nemohou zákazníci vytvářet nové clustery HDInsight 3,6. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na HDInsight 4,0, aby se předešlo potenciálnímu přerušení systému/podpory.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 
### <a name="fix-issue-for-restarting-vms-in-cluster"></a>Oprava problému pro restartování virtuálních počítačů v clusteru
Problém pro restartování virtuálních počítačů v clusteru byl vyřešen, můžete použít [PowerShell nebo REST API k restartování uzlů v clusteru](https://docs.microsoft.com/azure/hdinsight/cluster-reboot-vm) .

## <a name="component-version-change"></a>Změna verze součásti
Pro tuto verzi se nezměnila žádná verze součásti. V [tomto dokumentu](./hdinsight-component-versioning.md)najdete aktuální verze komponent pro HDInsight 4,0 a HDInsight 3,6.

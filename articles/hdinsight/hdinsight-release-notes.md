---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 616e3e6c37faa3c085b8531173b557973e09fbf8
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974562"
---
# <a name="azure-hdinsight-release-notes"></a>Poznámky k verzi Azure HDInsight

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Shrnutí

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky, kteří se týkají Open Source analýz v Azure.

## <a name="release-date-10082020"></a>Datum vydání: 10/08/2020

Tato verze se týká HDInsight 3,6 i HDInsight 4,0. Vydání HDInsight je zpřístupněno pro všechny oblasti více než několik dní. Datum vydání znamená datum vydání první oblasti. Pokud nevidíte níže uvedené změny, počkejte, až bude verze ve vaší oblasti v průběhu několika dnů živá.

## <a name="new-features"></a>Nové funkce
### <a name="hdinsight-private-clusters-with-no-public-ip-and-private-link-preview"></a>Privátní clustery HDInsight bez veřejné IP adresy a privátního propojení (Preview)
HDInsight teď podporuje vytváření clusterů bez přístupu veřejných IP adres a privátních odkazů do clusterů ve verzi Preview. Zákazníci můžou pomocí nových pokročilých nastavení sítě vytvořit plně izolovaný cluster bez veřejné IP adresy a používat pro přístup ke clusteru vlastní privátní koncové body. 

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Přechod na Azure Virtual Machine Scale Sets
HDInsight teď pomocí virtuálních počítačů Azure zřídí cluster. Od této verze se služba postupně migruje na [Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Celý proces může trvat měsíce. Po migraci vašich oblastí a předplatných se nově vytvořené clustery HDInsight spustí ve službě Virtual Machine Scale Sets bez akcí zákazníků. Neočekává se žádná neprůlomová změna.

## <a name="deprecation"></a>Vyřazení
#### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Vyřazení clusteru HDInsight 3,6 ML Services
Typ clusteru HDInsight 3,6 ML Services bude na konci podpory od prosince 31 2020. Zákazníci nevytvoří nové clustery služby 3,6 ML. Existující clustery se spustí, a to bez podpory Microsoftu. [Tady](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#available-versions)se podívejte na vypršení platnosti podpory pro verze a typy clusterů HDInsight.

## <a name="behavior-changes"></a>Změny chování
Žádná změna chování pro tuto verzi.

## <a name="upcoming-changes"></a>Nadcházející změny
V nadcházejících verzích dojde k následujícím změnám.

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Možnost výběru různých velikostí virtuálních počítačů s Zookeeper pro služby Spark, Hadoop a ML
HDInsight dnes nepodporuje přizpůsobení velikosti uzlů Zookeeper pro typy clusterů Spark, Hadoop a ML. Ve výchozím nastavení se A2_v2/a2 velikosti virtuálních počítačů, které jsou poskytovány zdarma. V nadcházející verzi můžete vybrat velikost virtuálního počítače Zookeeper, která je pro váš scénář nejvhodnější. Budou se účtovat uzly Zookeeper s velikostí virtuálního počítače jinou než A2_v2/a2. Virtuální počítače A2_v2 a a2 jsou stále poskytovány zdarma.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 

## <a name="component-version-change"></a>Změna verze součásti
Pro tuto verzi se nezměnila žádná verze součásti. V [tomto dokumentu](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)najdete aktuální verze komponent pro HDInsight 4,0 a HDInsight 3,6.
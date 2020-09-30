---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/27/2020
ms.openlocfilehash: feb186fbe216305039fcc0a23a10419c44fd0483
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91535606"
---
# <a name="azure-hdinsight-release-notes"></a>Poznámky k verzi Azure HDInsight

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Shrnutí

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky, kteří se týkají Open Source analýz v Azure.

## <a name="release-date-09282020"></a>Datum vydání: 09/28/2020

Tato verze se týká HDInsight 3,6 i HDInsight 4,0. Vydání HDInsight je zpřístupněno pro všechny oblasti více než několik dní. Datum vydání znamená datum vydání první oblasti. Pokud nevidíte níže uvedené změny, počkejte, až bude verze ve vaší oblasti v průběhu několika dnů živá.

## <a name="new-features"></a>Nové funkce
### <a name="autoscale-for-interactive-query-with-hdinsight-40-is-now-generally-available"></a>Automatické škálování pro interaktivní dotaz s HDInsight 4,0 je teď všeobecně dostupné.
Automatické škálování pro typ clusteru interaktivních dotazů je teď všeobecně dostupné (GA) pro HDInsight 4,0. Všechna interaktivní clustery dotazů 4,0 vytvořená po 27. srpna 2020 budou mít podporu GA pro automatické škálování.

### <a name="hbase-cluster-supports-premium-adls-gen2"></a>Cluster HBA podporuje prémiové ADLS Gen2
HDInsight teď podporuje Premium ADLS Gen2 jako primární účet úložiště pro clustery HDInsight 3,6 a 4,0. Společně s [akcelerovanými zápisy](./hbase/apache-hbase-accelerated-writes.md)můžete dosáhnout lepšího výkonu clusterů HBA.

### <a name="kafka-partition-distribution-on-azure-fault-domains"></a>Kafka rozdělení oddílu do domén selhání Azure
Doména selhání je logické seskupení základního hardwaru v datovém centru Azure. Všechny domény selhání sdílí společný zdroje napájení a síťový přepínač. Předtím, než HDInsight Kafka může ukládat všechny repliky oddílů ve stejné doméně selhání. Od této verze už HDInsight podporuje automatickou distribuci oddílů Kafka na základě domén selhání Azure. 

### <a name="encryption-in-transit"></a>Šifrování během přenosu
Zákazníci můžou povolit šifrování při přenosu mezi uzly clusteru pomocí šifrování IPSec s klíči spravovanými platformou. Tuto možnost můžete povolit v době vytváření clusteru. Přečtěte si další podrobnosti o [tom, jak povolit šifrování při přenosu](./domain-joined/encryption-in-transit.md).

### <a name="encryption-at-host"></a>Šifrování na hostiteli
Pokud povolíte šifrování na hostiteli, data uložená na hostiteli virtuálního počítače se zašifrují v klidovém stavu a toky se zašifrují do služby úložiště. V této verzi můžete při vytváření clusteru **zapnout šifrování na hostiteli na dočasném datovém disku** . Šifrování na hostiteli se podporuje jenom u [některých SKU virtuálních počítačů v omezených oblastech](https://docs.microsoft.com/azure/virtual-machines/linux/disks-enable-host-based-encryption-portal). HDInsight podporuje [následující konfiguraci uzlů a SKU](./hdinsight-supported-node-configuration.md). Podívejte se na další podrobnosti o [tom, jak povolit šifrování na hostiteli](https://docs.microsoft.com/azure/hdinsight/disk-encryption#encryption-at-host-using-platform-managed-keys).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Přechod na Azure Virtual Machine Scale Sets
HDInsight teď pomocí virtuálních počítačů Azure zřídí cluster. Od této verze se služba postupně migruje na [Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview). Celý proces může trvat měsíce. Po migraci vašich oblastí a předplatných se nově vytvořené clustery HDInsight spustí ve službě Virtual Machine Scale Sets bez akcí zákazníků. Neočekává se žádná neprůlomová změna.

## <a name="deprecation"></a>Vyřazení
Pro tuto verzi není zastaralost.

## <a name="behavior-changes"></a>Změny chování
Žádná změna chování pro tuto verzi.

## <a name="upcoming-changes"></a>Nadcházející změny
V nadcházejících verzích dojde k následujícím změnám.

### <a name="ability-to-select-different-zookeeper-sku-for-spark-hadoop-and-ml-services"></a>Možnost výběru různých Zookeeper SKU pro služby Spark, Hadoop a ML
HDInsight dnes nepodporuje změnu Zookeeper SKU pro typy clusterů Spark, Hadoop a ML. Používá A2_v2 SKU/a2 pro uzly Zookeeper a zákazníkům se za ně neúčtují poplatky. V nadcházející verzi můžou zákazníci podle potřeby měnit SKU Zookeeper pro služby Spark, Hadoop a ML. Zookeeper uzly s jinou skladovou jednotkou než A2_v2/a2 budou účtovány. Výchozí SKU bude i nadále A2_V2/a2 a zadarmo.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 

## <a name="component-version-change"></a>Změna verze součásti
Pro tuto verzi se nezměnila žádná verze součásti. V [tomto dokumentu](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)najdete aktuální verze komponent pro HDInsight 4,0 a HDInsight 3,6.
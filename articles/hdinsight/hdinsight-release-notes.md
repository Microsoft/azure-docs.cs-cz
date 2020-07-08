---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/11/2020
ms.openlocfilehash: a4faab9ac6d5e1c39c1120e09dae792b95892d60
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85564414"
---
# <a name="release-notes"></a>Poznámky k verzi

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky, kteří se týkají Open Source analýz v Azure.

## <a name="release-date-06112020"></a>Datum vydání: 06/11/2020

Tato verze platí pro HDInsight 3,6 a 4,0. Vydání HDInsight je zpřístupněno pro všechny oblasti více než několik dní. Datum vydání znamená datum vydání první oblasti. Pokud nevidíte níže uvedené změny, počkejte, až bude verze ve vaší oblasti v průběhu několika dnů živá.

## <a name="new-features"></a>Nové funkce
### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Přechod na Azure Virtual Machine Scale Sets
HDInsight pomocí virtuálních počítačů Azure zřídí cluster. V této verzi se nově vytvořené clustery HDInsight začnou používat na Azure Virtual Machine Scale set. Tato změna se provádí postupně. Neměli byste očekávat žádné zásadní změny. Přečtěte si další informace o [službě Azure Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).
 
### <a name="reboot-vms-in-hdinsight-cluster"></a>Restartování virtuálních počítačů v clusteru HDInsight
V této verzi podporujeme restartování virtuálních počítačů v clusteru HDInsight k restartování nereagujících uzlů. V současné době je možné ji provést pouze prostřednictvím rozhraní API, podpora PowerShellu a rozhraní příkazového řádku. Další informace o rozhraní API najdete v [tomto dokumentu](https://github.com/Azure/azure-rest-api-specs/codeowners/master/specification/hdinsight/resource-manager/Microsoft.HDInsight/stable/2018-06-01-preview/virtualMachines.json).
 
## <a name="deprecation"></a>Vyřazení
### <a name="deprecation-of-spark-21-and-22-in-hdinsight-36-spark-cluster"></a>Ukončení podpory Sparku 2.1 a 2.2 v clusteru HDInsight 3.6 Spark
Od července 1 2020 nemohou zákazníci vytvářet nové clustery Spark pomocí Sparku 2,1 a 2,2 ve službě HDInsight 3,6. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na Spark 2,3 ve službě HDInsight 3,6 pomocí června 30 2020, aby se předešlo potenciálnímu přerušení systému/podpory.
 
### <a name="deprecation-of-spark-23-in-hdinsight-40-spark-cluster"></a>Ukončení podpory Sparku 2.3 v clusteru HDInsight 4.0 Spark
Od července 1 2020 nemohou zákazníci vytvářet nové clustery Spark se Sparkem 2,3 ve službě HDInsight 4,0. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na Spark 2.4 v HDInsight 4.0 nejpozději do 30. června 2020, abyste se vyhnuli možnému přerušení služeb nebo podpory.
 
### <a name="deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Ukončení podpory Kafka 1.1 v clusteru HDInsight 4.0 Kafka
Od července 1 2020 nebudou zákazníci moci vytvářet nové clustery Kafka s Kafka 1,1 ve službě HDInsight 4,0. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na Kafka 2.1 v HDInsight 4.0 nejpozději do 30. června 2020, abyste se vyhnuli možnému přerušení služeb nebo podpory.
 
## <a name="behavior-changes"></a>Změny chování
### <a name="esp-spark-cluster-head-node-size-change"></a>Změna velikosti hlavního uzlu clusteru Spark Spark 
Minimální povolená velikost hlavního uzlu pro cluster ESP Spark se změní na Standard_D13_V2. Virtuální počítače s nízkými jádry a pamětí jako hlavní uzel mohou způsobovat problémy s clusterem ESP z důvodu relativně nízké kapacity procesoru a paměti. Od verze použijte SKU vyšší než Standard_D13_V2 a Standard_E16_V3 jako hlavní uzel pro clustery ESP Spark.
 
### <a name="a-minimum-4-core-vm-is-required-for-head-node"></a>Pro hlavní uzel se vyžaduje minimální virtuální počítač se 4 jádry. 
Hlavní uzel vyžaduje minimálně 4 jádro virtuálního počítače, aby se zajistila vysoká dostupnost a spolehlivost clusterů HDInsight. Od dubna 6 2020 můžou zákazníci jako hlavní uzel pro nové clustery HDInsight vybrat jenom 4 Core nebo novější virtuální počítač. Existující clustery budou i nadále fungovat podle očekávání. 
 
### <a name="cluster-worker-node-provisioning-change"></a>Změna zřizování pracovních uzlů clusteru
Až 80% pracovních uzlů je připraveno, cluster přejde do **provozní** fáze. V této fázi můžou zákazníci provádět všechny operace roviny dat, jako je spouštění skriptů a úloh. Ale zákazníci nemůžou provádět žádné provozní roviny, jako je například horizontální navýšení kapacity. Podporuje se jenom odstranění.
 
Po skončení **provozní** fáze vyčká cluster dalších 20% pracovních uzlů za 60 minut. Na konci tohoto 60 minut se cluster přesune do **běžící** fáze, i když všechny pracovní uzly stále nejsou k dispozici. Jakmile cluster vstoupí do **běžící** fáze, můžete ho použít jako normální. Jsou přijímány i operace plánu řízení, jako je například vertikální navýšení/snížení kapacity a operace datového tarifu, jako je spouštění skriptů a úloh. Pokud některé z požadovaných pracovních uzlů nejsou k dispozici, bude cluster označen jako částečný úspěch. Účtují se vám poplatky za uzly, které se úspěšně nasadily. 
 
### <a name="create-new-service-principal-through-hdinsight"></a>Vytvoření nového instančního objektu prostřednictvím služby HDInsight
Při vytváření clusteru mohli zákazníci v minulosti vytvořit nový instanční objekt pro přístup k připojenému účtu ADLS fin 1 v rámci služby Azure Portal. Od června 15 2020 nemůže zákazníci vytvořit nový instanční objekt ve službě HDInsight pro vytváření pracovních postupů, podporuje se jenom existující instanční objekt. Viz téma [Vytvoření instančního objektu a certifikátů pomocí Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

### <a name="time-out-for-script-actions-with-cluster-creation"></a>Časový limit pro akce skriptu s vytvořením clusteru
HDInsight podporuje spouštění akcí skriptů s vytvářením clusteru. Z této verze musí být všechny akce skriptů s vytvořením clusteru dokončeny během **60 minut**nebo vypršel časový limit. Akce skriptu odeslané na spuštěné clustery neovlivní. Další podrobnosti najdete [tady](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux#script-action-in-the-cluster-creation-process).
 
## <a name="upcoming-changes"></a>Nadcházející změny
Žádné nadcházející nepotřebné změny, ke kterým byste měli věnovat pozornost.
 
## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 
 
## <a name="component-version-change"></a>Změna verze součásti
### <a name="hbase-20-to-216"></a>HBA 2,0 až 2.1.6
Verze HBA je upgradována z verze 2,0 na 2.1.6.
 
### <a name="spark-240-to-244"></a>Spark 2.4.0 na 2.4.4
Verze Sparku se upgraduje z verze 2.4.0 na 2.4.4.
 
### <a name="kafka-210-to-211"></a>Kafka 2.1.0 na 2.1.1
Verze Kafka je upgradována z verze 2.1.0 na verzi 2.1.1.
 
V [tomto dokumentu](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions) najdete aktuální verze komponent pro HDInsight 4,0 ad HDInsight 3,6.

## <a name="known-issues"></a>Známé problémy

### <a name="hive-warehouse-connector-issue"></a>Problém s konektorem skladu z podregistru
V této verzi se vyskytl problém s konektorem datového skladu pro podregistr. Oprava bude zahrnutá v další verzi. Existující clustery vytvořené před touto verzí nebudou ovlivněny. Pokud je to možné, vyhněte se vyřazení a opětovnému vytvoření clusteru. Pokud potřebujete další pomoc, otevřete prosím lístek podpory.

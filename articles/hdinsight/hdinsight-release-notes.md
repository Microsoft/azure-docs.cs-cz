---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 6addd7cffdd7001b5767a471fc9e0514749b18d1
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105564887"
---
# <a name="azure-hdinsight-release-notes"></a>Poznámky k verzi Azure HDInsight

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky, kteří se týkají Open Source analýz v Azure.

Pokud se chcete přihlásit k odběru poznámky k verzi, Sledujte verze v [tomto úložišti GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-02052021"></a>Datum vydání: 02/05/2021

Tato verze se týká HDInsight 3,6 i HDInsight 4,0. Vydání HDInsight je zpřístupněno pro všechny oblasti více než několik dní. Datum vydání znamená datum vydání první oblasti. Pokud nevidíte níže uvedené změny, počkejte, až bude verze ve vaší oblasti v průběhu několika dnů živá.

## <a name="new-features"></a>Nové funkce
### <a name="dav4-series-support"></a>Podpora řady Dav4-Series
HDInsight přidal v této verzi podporu řady Dav4-Series. Další informace o [Dav4-Series najdete tady](../virtual-machines/dav4-dasv4-series.md).

### <a name="kafka-rest-proxy-ga"></a>Kafka REST proxy GA 
Kafka REST proxy umožňuje komunikovat s clusterem Kafka prostřednictvím REST API přes HTTPS. Kafka REST proxy je od této verze všeobecně k dispozici. Další informace o [proxy Kafka REST najdete tady](./kafka/rest-proxy.md).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Přechod na Azure Virtual Machine Scale Sets
HDInsight teď pomocí virtuálních počítačů Azure zřídí cluster. Služba se postupně migruje na [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md). Celý proces může trvat měsíce. Po migraci vašich oblastí a předplatných se nově vytvořené clustery HDInsight spustí ve službě Virtual Machine Scale Sets bez akcí zákazníků. Neočekává se žádná neprůlomová změna.

## <a name="deprecation"></a>Vyřazení
### <a name="disabled-vm-sizes"></a>Zakázané velikosti virtuálních počítačů
Počínaje formulářem leden 9 2021 bude HDInsight blokovat všem zákazníkům vytváření clusterů pomocí standand_A8, standand_A9, standand_A10 a standand_A11 velikostí virtuálních počítačů. Existující clustery se spustí, jak je. Zvažte přechod na HDInsight 4,0, aby se předešlo potenciálnímu přerušení systému/podpory.

## <a name="behavior-changes"></a>Změny chování
### <a name="default-cluster-vm-size-changes-to-ev3-series"></a>Výchozí Změna velikosti virtuálního počítače clusteru na Ev3-Series 
Výchozí velikosti virtuálních počítačů clusteru budou změněny z řady D-Series na Ev3-Series. Tato změna se vztahuje na hlavní uzly a pracovní uzly. Aby se této změně neovlivnila Vaše testované pracovní postupy, zadejte velikosti virtuálních počítačů, které chcete použít v šabloně ARM.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Prostředek síťového rozhraní není viditelný pro clustery běžící na Azure Virtual Machine Scale Sets.
HDInsight se postupně migruje na Azure Virtual Machine Scale Sets. Pro clustery, které používají službu Azure Virtual Machine Scale Sets, už nejsou síťová rozhraní pro virtuální počítače viditelná pro zákazníky.


### <a name="breaking-change-for-net-for-apache-spark-100"></a>Průlomová změna pro .NET pro Apache Spark 1.0.0
S nejnovější verzí zavádí HDInsight první oficiální verzi v 1.0.0 knihovny [.NET for Apache Spark](https://github.com/dotnet/spark) . Poskytuje úplnost rozhraní API dataframe pro Spark 2.4. x a Spark 3.0. x společně s hostitelem [dalších funkcí](https://github.com/dotnet/spark/blob/master/docs/release-notes/1.0.0/release-1.0.0.md). U této hlavní verze dojde k zásadním změnám. Další informace o krocích potřebných k aktualizaci kódu a kanálů najdete [v příručce k migraci rozhraní .NET pro Apache Spark](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) . Další informace najdete [v příručce k rozhraní .NET pro Apache Spark v 1.0 v Azure HDInsight](./spark/spark-dotnet-version-update.md#using-net-for-apache-spark-v10-in-hdinsight).


## <a name="upcoming-changes"></a>Nadcházející změny
V nadcházejících verzích dojde k následujícím změnám.

### <a name="default-cluster-version-will-be-changed-to-40"></a>Výchozí verze clusteru se změní na 4,0.
Od února 2021 se výchozí verze clusteru HDInsight změní z 3,6 na 4,0. Další informace o dostupných verzích najdete v tématu [dostupné verze](./hdinsight-component-versioning.md). Přečtěte si další informace o tom, co je v [HDInsight 4,0](./hdinsight-version-release.md)novinkou.

### <a name="os-version-upgrade"></a>Upgrade verze operačního systému
HDInsight upgraduje verzi operačního systému z Ubuntu 16,04 na 18,04. Upgrade se dokončí do dubna 2021.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Podpora HDInsight 3,6 na konci 30 2021. června
Podpora HDInsight 3,6 bude ukončena. Počínaje formulářem červeně 30 2021 nemohou zákazníci vytvářet nové clustery HDInsight 3,6. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na HDInsight 4,0, aby se předešlo potenciálnímu přerušení systému/podpory.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 

## <a name="component-version-change"></a>Změna verze součásti
Pro tuto verzi se nezměnila žádná verze součásti. V [tomto dokumentu](./hdinsight-component-versioning.md)najdete aktuální verze komponent pro HDInsight 4,0 a HDInsight 3,6.
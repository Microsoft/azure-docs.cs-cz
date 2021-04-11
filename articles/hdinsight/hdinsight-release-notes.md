---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: a648ff3aa0c042aaefe16eaae0f9d73953241b3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065493"
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

### <a name="eav4-series-support"></a>Podpora řady Eav4-Series
HDInsight přidal v této verzi podporu řady Eav4-Series. Další informace o [Dav4-Series najdete tady](../virtual-machines/eav4-easv4-series.md). Řada byla zpřístupněna v následujících oblastech: 

* Austrálie – východ
* Brazílie – jih
* USA – střed
* Východní Asie
* East US
* Japonsko – východ
* Southeast Asia
* Spojené království – jih
* West Europe
* Západní USA 2

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

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Podpora Basic pro HDInsight 3,6 od 1. července 2021
Od 1. července 2021 bude Microsoft nabízet [Podpora Basic](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) pro určité typy clusterů HDInsight 3,6. Plán podpora Basic bude k dispozici až do 3. dubna 2022. Automaticky se zaregistrujete do podpora Basic od 1. července 2021. Nemusíte nic dělat, abyste se mohli přihlásit. V [naší dokumentaci](hdinsight-36-component-versioning.md) , pro které jsou typy clusterů zahrnuté v podpora Basic. 

Nedoporučujeme vytvářet žádná nová řešení ve službě HDInsight 3,6, zablokovat změny v existujících prostředích 3,6. Doporučujeme, abyste provedli [migraci clusterů do HDInsight 4,0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). Další informace o [tom, co je nového ve službě HDInsight 4,0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 

## <a name="component-version-change"></a>Změna verze součásti
Přidali jsme podporu pro Spark 3.0.0 a Kafka 2.4.1 jako verzi Preview. V [tomto dokumentu](./hdinsight-component-versioning.md)najdete aktuální verze komponent pro HDInsight 4,0 a HDInsight 3,6.

## <a name="recommanded-features"></a>Funkce s funkcí příkazu
### <a name="service-tags"></a>Značky služeb
Značky služeb zjednodušují přístup k síti ke službám Azure pro virtuální počítače Azure a virtuální sítě Azure. Značky služby ve skupinách zabezpečení sítě (NSG) povolují nebo odmítají provoz na určitou službu Azure. Pravidlo lze nastavit globálně nebo podle oblasti Azure. Azure poskytuje údržbu IP adres, které jsou základem jednotlivých značek. Značky služby HDInsight pro skupiny zabezpečení sítě (skupin zabezpečení sítě) jsou skupiny IP adres pro služby stavu a správy. Tyto skupiny vám pomůžou minimalizovat složitost pro vytváření pravidel zabezpečení. Zákazníci HDInsight můžou povolit značku služby prostřednictvím Azure Portal, PowerShellu a REST API. Další informace najdete v tématu [značky služby skupiny zabezpečení sítě (NSG) pro Azure HDInsight](./hdinsight-service-tags.md).

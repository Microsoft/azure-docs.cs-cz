---
title: Poznámky k verzi pro Azure HDInsight
description: Nejnovější poznámky k verzi pro Azure HDInsight. Získejte tipy pro vývoj a podrobnosti pro Hadoop, Spark, R Server, podregistr a další.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 6e37501a56bdf247bce2111e8e5a5bf02f37d898
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626117"
---
# <a name="azure-hdinsight-release-notes"></a>Poznámky k verzi Azure HDInsight

Tento článek poskytuje informace **o nejnovějších aktualizacích vydaných** verzí Azure HDInsight. Informace o dřívějších verzích najdete v tématu [archiv zpráv k vydání verze HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Souhrn

Azure HDInsight je jednou z nejoblíbenějších služeb pro podnikové zákazníky, kteří se týkají Open Source analýz v Azure.

Pokud se chcete přihlásit k odběru poznámky k verzi, Sledujte verze v [tomto úložišti GitHub](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-11182020"></a>Datum vydání: 11/18/2020

Tato verze se týká HDInsight 3,6 i HDInsight 4,0. Vydání HDInsight je zpřístupněno pro všechny oblasti více než několik dní. Datum vydání znamená datum vydání první oblasti. Pokud nevidíte níže uvedené změny, počkejte, až bude verze ve vaší oblasti v průběhu několika dnů živá.

## <a name="new-features"></a>Nové funkce
### <a name="auto-key-rotation-for-customer-managed-key-encryption-at-rest"></a>Automatické střídání klíčů pro šifrování spravovaného klíče zákazníka v klidovém umístění
Od této verze můžou zákazníci používat adresy URL šifrovacího klíče bez verze Azure KeyValut pro šifrování spravovaného klíče zákazníka v klidovém provozu. HDInsight tyto klíče automaticky otočí, jakmile vyprší platnost, nebo je nahradí novými verzemi. Další podrobnosti najdete [tady](./disk-encryption.md).

### <a name="ability-to-select-different-zookeeper-virtual-machine-sizes-for-spark-hadoop-and-ml-services"></a>Možnost výběru různých velikostí virtuálních počítačů s Zookeeper pro služby Spark, Hadoop a ML
Služba HDInsight dřív nepodporovala přizpůsobení velikosti uzlu Zookeeper pro typy clusterů Spark, Hadoop a ML. Ve výchozím nastavení se A2_v2/a2 velikosti virtuálních počítačů, které jsou poskytovány zdarma. Z této verze můžete vybrat velikost virtuálního počítače Zookeeper, která je pro váš scénář nejvhodnější. Budou se účtovat uzly Zookeeper s velikostí virtuálního počítače jinou než A2_v2/a2. Virtuální počítače A2_v2 a a2 jsou stále poskytovány zdarma.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Přechod na Azure Virtual Machine Scale Sets
HDInsight teď pomocí virtuálních počítačů Azure zřídí cluster. Od této verze se služba postupně migruje na [Azure Virtual Machine Scale Sets](../virtual-machine-scale-sets/overview.md). Celý proces může trvat měsíce. Po migraci vašich oblastí a předplatných se nově vytvořené clustery HDInsight spustí ve službě Virtual Machine Scale Sets bez akcí zákazníků. Neočekává se žádná neprůlomová změna.

## <a name="deprecation"></a>Vyřazení
### <a name="deprecation-of-hdinsight-36-ml-services-cluster"></a>Vyřazení clusteru HDInsight 3,6 ML Services
Typ clusteru HDInsight 3,6 ML Services bude na konci podpory od prosince 31 2020. Zákazníci nebudou moct vytvořit nové clustery 3,6 ML Services po prosinci 31 2020. Existující clustery se spustí, a to bez podpory Microsoftu. [Tady](./hdinsight-component-versioning.md#available-versions)se podívejte na vypršení platnosti podpory pro verze a typy clusterů HDInsight.

### <a name="disabled-vm-sizes"></a>Zakázané velikosti virtuálních počítačů
Od listopadu 16 2020 bude HDInsight zablokuje novým zákazníkům vytváření clusterů pomocí standand_A8, standand_A9, standand_A10 a standand_A11 velikostí virtuálních počítačů. Stávající zákazníci, kteří použili tyto velikosti virtuálních počítačů v posledních třech měsících, to neovlivní. Počínaje formulářem leden 9 2021 bude HDInsight blokovat všem zákazníkům vytváření clusterů pomocí standand_A8, standand_A9, standand_A10 a standand_A11 velikostí virtuálních počítačů. Existující clustery se spustí, jak je. Zvažte přechod na HDInsight 4,0, aby se předešlo potenciálnímu přerušení systému/podpory.

## <a name="behavior-changes"></a>Změny chování
### <a name="add-nsg-rule-checking-before-scaling-operation"></a>Přidat kontrolu pravidel NSG před operací škálování
HDInsight přidal skupiny zabezpečení sítě (skupin zabezpečení sítě) a uživatelsky definované trasy (udr), která má kontrolu nad operací škálování. Stejné ověřování se provádí pro škálování clusteru Kromě vytváření clusteru. Toto ověření pomáhá zabránit nepředvídatelným chybám. Pokud ověření neprojde, škálování se nezdařilo. Další informace o tom, jak správně nakonfigurovat skupin zabezpečení sítě a udr, najdete v tématu věnovaném [IP adresám správy HDInsight](./hdinsight-management-ip-addresses.md).

## <a name="upcoming-changes"></a>Nadcházející změny
V nadcházejících verzích dojde k následujícím změnám.

### <a name="breaking-change-for-net-for-apache-spark-100"></a>Průlomová změna pro .NET pro Apache Spark 1.0.0
HDInsight zavede první hlavní oficiální verzi rozhraní .NET pro Apache Spark v další verzi. Zajišťuje úplnost rozhraní API dataframe pro Spark 2.4. x a Spark 3.0. x spolu s dalšími funkcemi. U této hlavní verze dojde k zásadním změnám. Další informace najdete v [této příručce k migraci](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) , abyste pochopili kroky potřebné k aktualizaci kódu a kanálů. Další informace najdete [tady](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight).

### <a name="default-cluster-vm-size-will-be-changed-to-ev3-family"></a>Výchozí velikost virtuálního počítače clusteru se změní na Ev3 rodinu.
Od další verze (od konce ledna) se výchozí velikosti virtuálních počítačů clusteru změní z řady D na rodinu Ev3. Tato změna se vztahuje na hlavní uzly a pracovní uzly. Chcete-li se této změně vyhnout, zadejte velikosti virtuálních počítačů, které chcete použít v šabloně ARM.

### <a name="default-cluster-version-will-be-changed-to-40"></a>Výchozí verze clusteru se změní na 4,0.
Od února 2021 se výchozí verze clusteru HDInsight změní z 3,6 na 4,0. Další informace o dostupných verzích najdete v tématu [dostupné verze](./hdinsight-component-versioning.md#available-versions). Další informace o tom, co je nového ve službě [HDInsight 4,0](./hdinsight-version-release.md)

### <a name="os-version-upgrade"></a>Upgrade verze operačního systému
HDInsight upgraduje verzi operačního systému z 16,04 na 18,04. Upgrade se dokončí do dubna 2021.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>Podpora HDInsight 3,6 na konci 30 2021. června
Podpora HDInsight 3,6 bude ukončena. Počínaje formulářem červeně 30 2021 nemohou zákazníci vytvářet nové clustery HDInsight 3,6. Existující clustery se spustí, a to bez podpory Microsoftu. Zvažte přechod na HDInsight 4,0, aby se předešlo potenciálnímu přerušení systému/podpory.

## <a name="bug-fixes"></a>Opravy chyb
HDInsight nadále zdokonaluje spolehlivost a výkon clusteru. 

## <a name="component-version-change"></a>Změna verze součásti
Pro tuto verzi se nezměnila žádná verze součásti. V [tomto dokumentu](./hdinsight-component-versioning.md)najdete aktuální verze komponent pro HDInsight 4,0 a HDInsight 3,6.

## <a name="known-issues"></a>Známé problémy
### <a name="prevent-hdinsight-cluster-vms-from-rebooting-periodically"></a>Znemožnění pravidelného restartování virtuálních počítačů clusteru HDInsight

Od poloviny listopadu 2020 jste si mohli pravidelně zaznamenali restartování virtuálních počítačů clusteru HDInsight. Příčinou může být:

1.  V clusteru je povolený ClamAV. Nový balíček azsec-ClamAV spotřebovává velké množství paměti, které aktivuje restartování uzlu. 
2.  Úloha CRON je naplánována denně, která sleduje změny v seznamu certifikačních autorit používaných službami Azure. Když je k dispozici nový certifikát certifikační autority, skript přidá certifikát do úložiště důvěry JDK a naplánuje restart.

HDInsight nasazuje opravy a aplikuje opravu pro všechny spuštěné clustery pro oba problémy. Pokud chcete opravu použít hned a vyhnout se neočekávaným restartováním virtuálních počítačů, můžete spustit následující akce skriptu na všech uzlech clusteru jako akce trvalého skriptu. HDInsight bude po dokončení opravy a opravy publikovat další oznámení.
```
https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/replace_cacert_script.sh
https://healingscriptssa.blob.core.windows.net/healingscripts/ChangeOOMPolicyAndApplyLatestConfigForClamav.sh
```

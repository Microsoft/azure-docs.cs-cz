---
title: Plánování v Azure HDInsight kapacity clusteru | Microsoft Docs
description: Jak určit clusteru služby HDInsight pro kapacitu a výkon.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: maxluk
ms.openlocfilehash: ce8764e79d24d36eb3b162fb5f3f688b3e9fab3f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Plánování kapacity pro clustery HDInsight

Před nasazením clusteru služby HDInsight, plánování kapacity požadované clusteru tak, že určíte potřeby výkonu a možností škálování. Tento plánování pomůže optimalizovat použitelnost a náklady. Rozhodnutí týkající se kapacity některé clusteru nelze změnit po nasazení. Pokud parametry výkonu změnit, můžete se cluster zrušena a znovu vytvoří bez ztráty dat uložené.

Klíčové otázky týkající se plánování kapacity jsou:

* V které geografické oblasti musí nasadit cluster?
* Jak velké úložiště potřebujete?
* Jaký typ clusteru má můžete nasadit?
* Jaké velikost a typ virtuálního počítače (VM) by měl používat uzly clusteru?
* Počet uzlů pracovního procesu by měli mít cluster?

## <a name="choose-an-azure-region"></a>Vyberte oblast Azure

Oblast Azure Určuje, kde je váš cluster fyzicky zřízený. Chcete-li minimalizovat latence čtení a zápisu, by měl být clusteru téměř vaše data.

HDInsight je k dispozici v mnoha oblastech Azure. Nejbližší oblast, najdete v tématu *HDInsight Linux* položky v rámci *Data + analýzy* v [Azure produkty dostupné podle oblasti](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Vyberte umístění úložiště a velikosti

### <a name="location-of-default-storage"></a>Umístění úložiště, výchozí

Výchozí úložiště účtu Azure Storage nebo Azure Data Lake Store, musí být ve stejném umístění jako cluster. Azure Storage je k dispozici ve všech umístěních. Data Lake Store je k dispozici v některé oblasti – viz aktuální Data Lake Store dostupnosti v části *úložiště* v [Azure produkty dostupné podle oblasti](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Umístění existujících dat

Pokud již máte účet úložiště nebo Data Lake Store obsahující data a chcete používat toto úložiště jako váš cluster výchozí úložiště, musíte nasadit cluster v tomto stejné umístění.

### <a name="storage-size"></a>Velikost úložiště

Až budete mít cluster služby HDInsight nasadit, můžete připojit další účty Azure Storage nebo přístup k další úložiště Data Lake. Všechny účty úložiště se musí nacházet ve stejném umístění jako cluster. Data Lake Store může být v jiném umístění, i když to může způsobit určité zpoždění dat. pro čtení a zápis.

Úložiště Azure má některé [limity kapacity](../azure-subscription-service-limits.md#storage-limits), zatímco je prakticky neomezené Data Lake Store.

Cluster s podporou mají přístup k kombinaci jiným účtům úložiště. Typické příklady:

* Když množství dat, bude pravděpodobně překročit kapacitu úložiště kontejner úložiště jediného objektu blob.
* Pokud rychlost přístupu ke kontejneru objektů blob může překročit prahovou hodnotu vyskytl omezení.
* Pokud chcete, aby se data, jste již odeslali do kontejneru objektů blob v clusteru.
* Pokud chcete izolovat různé části úložiště z důvodů zabezpečení, nebo pro zjednodušení správy.

Pro cluster s podporou 48 uzlu doporučujeme 4 až 8 účty úložiště. I když už pravděpodobně dostatečná celkové úložiště, každý účet úložiště poskytuje dodatečnou šířku pásma sítě pro výpočetní uzly. Pokud máte více účtů úložiště, použijte náhodný název pro každý účet úložiště, bez předpony. Účelem náhodných pojmenování snižuje riziko vzniku kritických bodů úložiště (omezení) nebo selhání běžné režimu mezi všechny účty. Pro lepší výkon použijte pouze jeden kontejner na účet úložiště.

## <a name="choose-a-cluster-type"></a>Vyberte typ clusteru

Typ clusteru závisí na úlohy, jež clusteru HDInsight je nakonfigurovaná pro spuštění, například Hadoop, Storm, Kafka nebo Spark. Podrobný popis typy clusteru k dispozici, najdete v části [Úvod do Azure HDInsight](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Každý typ clusteru má topologii nasazení, která obsahuje požadavky na velikost a počet uzlů.

## <a name="choose-the-vm-size-and-type"></a>Zvolte velikost virtuálního počítače a typ

Každý typ clusteru má sadu typy uzlů a každý typ uzlu má konkrétní možnosti pro jejich velikost virtuálního počítače a typu.

K určení optimální velikost pro vaši aplikaci, můžete otestovat kapacity clusteru a zvětšete velikost, které je uvedené. Například můžete použít simulované zatížení, nebo *lesknice dotazu*. Simulované zatížení se spouští očekávané úlohy na jinou velikost clusterů, postupně zvýšení velikosti, dokud nebude dosaženo požadovaný výkon. Lesknice dotazu jde vložit pravidelně mezi jiné dotazy produkční zobrazíte zda clusteru nemá dostatek prostředků.

Velikost virtuálního počítače a typ je dáno zatížení procesoru napájení, velikost paměti RAM a latence sítě:

* Využití procesoru: Velikost virtuálního počítače určuje počet jader. Více jader, čím vyšší stupeň paralelní výpočty můžete dosáhnout každý uzel. Kromě toho některé typy virtuálních počítačů mají rychlejší jader.

* Paměť RAM: Velikost virtuálního počítače také určuje velikost paměti RAM, které jsou k dispozici ve virtuálním počítači. Pro úlohy, které slouží k ukládání dat v paměti pro zpracování, než čtení z disku, ujistěte se, pracovní uzly k dispozici dostatek paměti k datům.

* Síť: U většiny typů clusteru data zpracovaná službou clusteru není na místním disku, ale v služby externího úložiště, například Data Lake Store nebo úložiště Azure. Zvažte šířku pásma sítě a propustnost mezi uzlu virtuálního počítače a služby úložiště. Šířku pásma sítě dostupnou pro virtuální počítač s větší velikostí obvykle zvyšuje. Podrobnosti najdete v tématu [přehled velikostí virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Vyberte měřítko clusteru

Clusteru škála se určuje podle počtu uzlů jeho virtuálních počítačů. Pro všechny typy clusteru jsou typy uzlů, které mají určité měřítko a typy uzlů, které podporují škálování. Cluster může například vyžadovat přesně tři uzly ZooKeeper nebo dva uzly Head. Pracovní uzly, které provádějí zpracování dat distribuované způsobem můžete využívat výhod škálování, přidáním dalších pracovním uzlům.

V závislosti na typu vašeho clusteru zvýšit počet uzlů pracovního procesu přidává další výpočetní kapacity (například více jader), ale může také přidat do celkovou velikost paměti požadované pro celý cluster pro podporu úložiště v paměti zpracovávaných dat. Stejně jako u volba Velikost virtuálního počítače a typ, vybírá škálování správné clusteru je obvykle dosaženo empirically, pomocí simulované úlohy nebo lesknice dotazy.

Je možné škálovat se splnily požadavky na zatížení ve špičce a pak škálování zpět dolů, když už nejsou potřeba tyto další uzly clusteru. Další informace najdete v tématu [clusterů HDInsight škálování](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Životní cyklus clusteru

Budou se vám účtovat dobu jeho existence clusteru. Pokud jsou pouze konkrétní časy, je nutné, cluster nahoru a spuštěna, můžete [vytvářet clustery na vyžádání pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Můžete také vytvořit skripty prostředí PowerShell, které zřídit a odstranit cluster a pak tyto skripty pomocí naplánovat [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]
> Při odstranění clusteru je taky odstranit jeho výchozí metaúložiště Hive. Udržení metaúložiště pro další opakované vytvoření clusteru, použijte externí metadata úložiště jako databáze Azure nebo Oozie.
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Izolovat chyby úloh clusteru

Někdy chyby mohou nastat v důsledku paralelní provádění více mapy a snížit součásti v clusteru s více uzly. Abyste izolovat problém, zkuste distribuované testování spuštěním souběžných více úloh na jeden uzel clusteru, pak rozbalte tento přístup k více úloh na běželo clusterů obsahující více než jeden uzel. K vytvoření clusteru HDInsight jeden uzel v Azure, použijte *rozšířené* možnost.

Také můžete nainstalovat jedním uzlem vývojového prostředí v místním počítači a otestovat řešení existuje. Hortonworks poskytuje jeden uzel místního vývojového prostředí pro řešení založená na Hadoop, který je vhodný pro počáteční vývoj doklad konceptu a testování. Další informace najdete v tématu [Hortonworks karanténě](http://hortonworks.com/products/hortonworks-sandbox/).

K identifikaci problém na místní cluster jeden uzel můžete znovu spustit neúspěšné úlohy a upravit vstupních dat nebo použít menší datové sady. Jak spuštění těchto úloh závisí na platformu a typ aplikace.

## <a name="quotas"></a>Kvóty

Po určení cílového clusteru velikost virtuálního počítače, měřítko a typ, zkontrolujte aktuální limity kapacity kvóty předplatného. Po dosažení limit kvóty, nebudete moci nasadit nových clusterů nebo horizontální navýšení kapacity stávajících clusterů přidat další uzly pracovního procesu. Nejběžnější dosažen limit kvóty je kvóty jader procesoru, který v odběru, oblast a úrovně řady virtuálního počítače již existuje. Například může mít vaše předplatné celkový limit 200 jádra, s limitu 30 jader ve vaší oblasti a limitu 30 jader na instancí virtuálních počítačů. Můžete [požádejte podporu o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Ale existují některá omezení pevné kvótu, například v rámci jednoho předplatného Azure může mít maximálně 10 000 jader. Podrobnosti o těchto mezních hodnot, viz [předplatného Azure a omezení služby, kvóty a omezení](https://docs.microsoft.com/azure/azure-subscription-service-limits#limits-and-the-azure-resource-manager).

## <a name="next-steps"></a>Další postup

* [Nastavit clusterů v HDInsight Hadoop, Spark, Kafka a dalšími](hdinsight-hadoop-provision-linux-clusters.md): Zjistěte, jak nastavit a konfigurovat clusterů v HDInsight Hadoop, Spark, Kafka, interaktivní Hive, HBase, R Server nebo Storm.
* [Sledování výkonu clusteru](hdinsight-key-scenarios-to-monitor.md): Další informace o klíčových scénářů monitorování pro váš cluster HDInsight, které by mohly ovlivnit váš cluster kapacity.

---
title: Provozní kontinuita Azure HDInsight
description: Tento článek obsahuje přehled osvědčených postupů, dostupnosti s jednou oblastí a možnosti optimalizace pro plánování kontinuity podnikových procesů Azure HDInsight.
keywords: vysoká dostupnost Hadoop
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 74f8bdd26e000b89bfae84102077c241f85abf7e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933308"
---
# <a name="azure-hdinsight-business-continuity"></a>Provozní kontinuita Azure HDInsight

Clustery Azure HDInsight závisí na řadě služeb Azure, jako jsou úložiště, databáze, Active Directory, Active Directory Domain Services, sítě a Key Vault. Dobře navržená, vysoce dostupná a analytická aplikace odolná proti chybám by měla být navržená s dostatečnou redundancí pro vyrušování regionálních nebo místních přerušení v jedné nebo více těchto službách. Tento článek obsahuje přehled osvědčených postupů, dostupnosti s jednou oblastí a možnosti optimalizace pro plánování kontinuity podnikových procesů.

## <a name="general-best-practices"></a>Obecné osvědčené postupy

Tato část popisuje několik osvědčených postupů, které byste měli vzít v úvahu při plánování kontinuity podnikových procesů.

* Určete minimální obchodní funkčnost, kterou budete potřebovat v případě havárie a proč. Můžete například vyhodnotit, jestli potřebujete možnosti převzetí služeb při selhání pro vrstvu transformace dat (zobrazené žlutě) *a* vrstvu pro obsluhu dat (zobrazenou modře), nebo potřebujete převzetí služeb při selhání jenom pro vrstvu datové služby.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="transformace dat a vrstvy obsluhy dat":::

* Segmentujte své clustery na základě úloh, životního cyklu vývoje a oddělení. Větší počet clusterů snižuje riziko, že dojde k jedné velké chybě ovlivňující více různých obchodních procesů.

* Nastavte sekundární oblasti jen pro čtení. Oblasti převzetí služeb při selhání s možnostmi čtení i zápisu můžou vést ke složitým architekturám.

* Přechodný clustery je snazší spravovat, když dojde k havárii. Navrhněte úlohy tak, aby mohly být clustery v cyklech a aby se v clusterech neudržoval stav.

* Úlohy jsou často nedokončené, pokud dojde k havárii a potřebujete restartovat v nové oblasti. Navrhněte vaše úlohy tak, aby se idempotentníy v podstatě.

* Při nasazeních clusterů použijte automatizaci a zajistěte, aby nastavení konfigurace clusteru byla ve větším rozsahu, aby se zajistilo rychlé a plně automatizované nasazení, pokud dojde k havárii.

* Pomocí nástrojů pro monitorování Azure v HDInsight zjistíte neobvyklé chování v clusteru a nastavte odpovídající oznámení o výstrahách. Můžete nasadit předem nakonfigurovaná řešení pro správu specifická pro cluster HDInsight, která shromažďují důležité metriky výkonu konkrétního typu clusteru. Další informace najdete v tématu [monitorování Azure pro HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md).  

* Přihlaste se k odběru upozornění na stav Azure, abyste byli informováni o problémech se službou, plánované údržbě, službě stavu a zpravodajích zabezpečení pro předplatné, službu nebo oblast. Oznámení o stavu, která zahrnují příčinu problému a Resolute ETA, vám pomůžou lépe spustit převzetí služeb při selhání a failbacks. Další informace najdete v [dokumentaci Azure Service Health](../service-health/index.yml).

## <a name="single-region-availability"></a>Dostupnost jedné oblasti

Základní systém HDInsight má následující komponenty. Všechny komponenty mají své vlastní mechanismy odolnosti proti chybám v jedné oblasti.

* Výpočetní prostředky (virtuální počítače): cluster Azure HDInsight
* Metastore (e): Azure SQL Database
* Storage: Azure Data Lake Gen2 nebo BLOB Storage
* Ověřování: Azure Active Directory, Azure Active Directory Domain Services Balíček zabezpečení podniku
* Rozlišení názvu domény: Azure DNS

Existují i jiné volitelné služby, které lze použít, například Azure Key Vault a Azure Data Factory.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="Komponenty HDInsight":::

### <a name="azure-hdinsight-cluster-compute"></a>Cluster Azure HDInsight (COMPUTE)

HDInsight nabízí smlouvu SLA o dostupnosti 99,9%. Pro zajištění vysoké dostupnosti v jednom nasazení je HDInsight dodáváno s mnoha službami, které jsou ve výchozím nastavení v režimu vysoké dostupnosti. Mechanismy odolnosti proti chybám v HDInsight zajišťuje služba High Availability pro ekosystém Microsoft i Apache OSS.

Následující služby jsou navržené tak, aby byly vysoce dostupné:

#### <a name="infrastructure"></a>Infrastruktura

* Aktivní a pohotovostní hlavních
* Více uzlů brány
* Tři uzly kvora Zookeeper
* Pracovní uzly distribuované podle selhání a aktualizačních domén

#### <a name="service"></a>Služba

* Server Apache Ambari
* Závažnost časové osy aplikace pro PŘÍZe
* Server historie úlohy pro Hadoop MapReduce
* Apache Livy
* HDFS
* Správce prostředků nitě
* HBase Master

Další informace najdete v dokumentaci ke [službám vysoké dostupnosti, které podporuje Azure HDInsight](./hdinsight-high-availability-components.md) .

Nebere vždy závažnou událost pro dopad na obchodní funkce. Incidenty služeb v jedné nebo několika následujících službách v jedné oblasti můžou také vést ke ztrátě očekávaných podnikových funkcí.

### <a name="hdinsight-metastore"></a>Metastore HDInsight

HDInsight používá [Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/) jako metastore, což poskytuje SLA 99,99%. Tři repliky dat v datovém centru s synchronní replikací jsou trvalé. Pokud dojde ke ztrátě repliky, bude se bezproblémově obsluhovat alternativní replika. [Aktivní geografická replikace](../azure-sql/database/active-geo-replication-overview.md) je podporovaná mimo pole s maximálně čtyřmi datovými centry. Pokud dojde k převzetí služeb při selhání, ruční nebo datové centrum, první replika v hierarchii se automaticky změní na možnost podporuje čtení i zápis. Další informace najdete v tématu [Azure SQL Database provozní kontinuita](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="hdinsight-storage"></a>HDInsight Storage

HDInsight doporučuje Azure Data Lake Storage Gen2 jako podkladovou vrstvu úložiště. [Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/), včetně Azure Data Lake Storage Gen2, nabízí smlouvu SLA 99,9%. HDInsight používá službu LRS, ve které jsou tři repliky dat v rámci datového centra trvalé a replikace je synchronní. Dojde-li ke ztrátě repliky, replika bude zpracována bez problémů.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) poskytuje SLA 99,9%. Active Directory je globální služba s více úrovněmi interní redundance a automatické obnovy. Další informace najdete v tématu Jak [Microsoft neustále vylepšuje spolehlivost Azure Active Directory](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/).

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (služba AD DS)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) poskytuje SLA 99,9%. Azure služba AD DS je vysoce dostupná služba hostovaná v globálně distribuovaných datových centrech. Sady replik jsou funkcí verze Preview v Azure služba AD DS, která umožňuje obnově geografického zotavení po havárii v případě, že oblast Azure přejde do režimu offline. Další informace najdete v tématu [Koncepty a funkce sady replik Azure Active Directory Domain Services](../active-directory-domain-services/concepts-replica-sets.md) pro další informace.  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) poskytuje SLA 100%. HDInsight používá pro překlad názvů domén Azure DNS na různých místech.

## <a name="multi-region-cost-and-complexity-optimizations"></a>Optimalizace nákladů a složitosti ve více oblastech

Zlepšení kontinuity podnikových aplikací pomocí vysoce dostupného zotavení po havárii mezi různými oblastmi vyžaduje architekturu architektury vyšší složitosti a vyšší náklady. Následující tabulky obsahují podrobné informace o některých technických oblastech, které mohou zvýšit celkové náklady na vlastnictví.

### <a name="cost-optimizations"></a>Optimalizace nákladů

|Plošný|Příčina eskalace nákladů|Strategie optimalizace|
|----|------------------------|-----------------------|
|Úložiště dat|Duplikace primárních dat nebo tabulek v sekundární oblasti|Replikovat jenom dodaná data|
|Výstup dat|Přenosy dat odchozích dat mezi oblastmi přicházejí za cenu. Kontrola pokynů pro ceny šířky pásma|Replikovat jenom ta dodaná data, aby se snížila nároky na výstup oblasti|
|Výpočetní cluster|Další clustery HDInsight/s v sekundární oblasti|Pomocí automatizovaných skriptů nasaďte sekundární výpočetní prostředky po primárním selhání. Pomocí automatického škálování můžete zachovat minimální velikost sekundárního clusteru. Používejte levnější SKU virtuálních počítačů. Můžete vytvářet sekundární počítače v oblastech, kde je možné zlevněné jednotky virtuálních počítačů.|
|Authentication |Ve scénářích s více uživateli v sekundární oblasti se účtují další nastavení služby Azure služba AD DS.|Vyhněte se nastavením více uživatelů v sekundární oblasti.|

### <a name="complexity-optimizations"></a>Optimalizace složitosti

|Plošný|Příčina eskalace složitosti|Strategie optimalizace|
|----|------------------------|-----------------------|
|Čtení vzorů zápisu |Vyžadování pro čtení a zápis s povoleným primárním a sekundárním |Návrh sekundárního, aby byl jen pro čtení|
|0 RPO & RTO |Vyžadování nulové ztráty dat (RPO = 0) a nulová výpadky (RTO = 0) |Navrhněte RPO a RTO způsobem, jak snížit počet součástí, které je potřeba převzít služby při selhání.|
|Obchodní funkce |Vyžadování úplných firemních funkcí primární v sekundárním systému |Vyhodnoťte, jestli můžete spustit s minimální kritickou podmnožinou firemních funkcí v sekundární verzi.|
|Připojení |Vyžadování všech nadřazených a podřízených systémů z primárního na připojení k sekundárnímu|Omezte sekundární připojení na minimální kritickou podmnožinu.|

## <a name="next-steps"></a>Další kroky

Další informace o položkách, které jsou popsány v tomto článku, najdete v těchto tématech:

* [Architektury Azure HDInsight pro provozní kontinuitu](./hdinsight-business-continuity-architecture.md)
* [Případová studie architektury řešení Azure HDInsight s vysokou dostupností](./hdinsight-high-availability-case-study.md)
* [Co je Apache Hive a HiveQL ve službě Azure HDInsight?](./hadoop/hdinsight-use-hive.md)
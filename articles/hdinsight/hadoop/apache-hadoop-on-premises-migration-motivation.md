---
title: Migrace místních Apache Hadoop clusterů Azure HDInsight – výhody a motivace
description: Informace o motivace a výhody pro migrace místních Hadoop clusterů pro Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: efaedccde854d8cd7fa777aa9457db7203ce833a
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50221868"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---motivation-and-benefits"></a>Migrace místních Apache Hadoop clusterů Azure HDInsight – výhody a motivace

Tento článek je první z řady na osvědčené postupy pro migraci v místním nasazení ekosystému Apache Hadoop do Azure HDInsight. Tato série článků je pro uživatele, kteří odpovídají za návrh, nasazení a migrace řešení Apache Hadoop v Azure HDInsight. Role, které můžou mít užitek z těchto článků zahrnují cloudové architekty, správci systému Hadoop a technici DevOps. Vývojáři softwaru, datovými architekty a datovými vědci by měl také využívat vysvětlení jak různých typů činnosti clusterů v cloudu.

## <a name="why-to-migrate-to-azure-hdinsight"></a>Důvod, proč k migraci do Azure HDInsight

Azure HDInsight je Cloudová distribuce komponent Hadoop z [Hortonworks Data Platform(HDP)](https://hortonworks.com/products/data-center/hdp/). Azure HDInsight umožňuje snadné, rychlé a nákladově efektivní zpracování obrovského množství dat. HDInsight obsahuje nejoblíbenější opensourcové architektury, jako například:

- Apache Hadoop
- Apache Spark
- Apache Hive s funkcí LLAP
- Apache Kafka
- Apache Storm
- Apache HBase
- R

## <a name="advantages-that-azure-hdinsight-offers-over-on-premises-hadoop"></a>Výhody, které Azure HDInsight nabízí místního clusteru Hadoop

- **Nízkonákladové** -lze snížit náklady na [vytváření clusterů na vyžádání](../hdinsight-hadoop-create-linux-clusters-adf.md) a platit jenom za využité. Oddělený výpočetní výkon a úložiště nabízí flexibilitu udržováním objem dat nezávislé na velikosti clusteru.
- **Automatizované vytváření clusterů** – automatizované vytváření clusteru vyžaduje minimální instalaci a konfiguraci. Automation je možné pro clustery na vyžádání.
- **Spravovat hardware a konfiguraci** – není nutné se starat o fyzický hardware nebo infrastrukturu s clusterem HDInsight. Stačí zadat konfiguraci clusteru, a Azure nastaví ho.
- **Snadná škálovatelnost** – HDInsight umožňuje [škálování](../hdinsight-administer-use-portal-linux.md) úlohy směrem nahoru nebo dolů. Úloha opětovné vyvážení bez přerušení úloh zpracování dat a distribuci dat postará Azure.
- **Globální dostupnost** – HDInsight je dostupná ve více [oblastech](https://azure.microsoft.com/regions/services/) než všechny ostatní nabídka analýz velkých objemů dat. Služba Azure HDInsight je dostupná také pro Azure Government, Čínu a Německo a umožňuje tak splnit požadavky vašeho podniku v klíčových suverénních oblastech.
- **Zabezpečení a dodržování** – HDInsight umožňuje chránit datové prostředky vašeho podniku pomocí [Azure Virtual Network](../hdinsight-extend-hadoop-virtual-network.md), [šifrování](../hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md)a integraci s [Azure Active Adresář](../domain-joined/apache-domain-joined-introduction.md). HDInsight také splňuje nejoblíbenější oborové a vládní [standardy pro dodržování předpisů](https://azure.microsoft.com/overview/trusted-cloud).
- **Zjednodušená správa verzí** – Azure HDInsight verze součástí ekosystému Hadoop spravuje a udržuje je aktuální. Aktualizace softwaru jsou obvykle složitý proces pro místní nasazení.
- **Menší clustery optimalizované pro určité úlohy s méně závislosti mezi komponentami** – instalační program systému Hadoop typický místní používal jeden cluster, který slouží různým účelům. S Azure HDInsight je možné vytvářet clustery určeného pro konkrétní úlohy. Vytváření clusterů pro konkrétní úlohy odebere vyžadovalo udržování jeden cluster s rostoucí složitostí.
- **Zvýšení produktivity** – můžete použít různé nástroje pro Hadoop a Spark v upřednostňovaných vývojových prostředích.
- **Rozšíření s vlastní nástroje nebo aplikace jiných výrobců** – clustery HDInsight je možné rozšířit pomocí nainstalovaných komponent a je také možné integrovat s jinými řešeními pro velké objemy dat s využitím [jedním kliknutím](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/)  nasazení z Azure Market place.
- **Snadná správa, Správa a monitorování** – Azure HDInsight se integruje s [Azure Log Analytics](../hdinsight-hadoop-oms-log-analytics-tutorial.md) a poskytuje jednotné rozhraní, pomocí kterého můžete monitorování všech vašich clusterů.
- **Integrace s dalšími službami Azure** – HDInsight je možné snadno integrovat s dalšími oblíbenými službami Azure jako je následující:

    - Azure Data Factory (ADF)
    - Azure Blob Storage
    - Azure Data Lake Storage Gen2
    - Azure Cosmos DB
    - Azure SQL Database
    - Azure Analysis Services

- **Opravy procesů a komponent** – HDInsight neustále kontroluje infrastruktury a open source komponenty pomocí vlastní monitorování infrastruktury. Kritické chyby, jako je například nedostupnost opensourcové komponenty a uzly také automaticky obnoví. V Ambari se aktivují upozornění, pokud libovolné součásti OSS, se nezdařilo.

Další informace najdete v článku [co je Azure HDInsight a technologie hadoop](../hadoop/apache-hadoop-introduction.md).

## <a name="migration-planning-process"></a>Proces plánování migrace

Následující kroky se doporučují pro plánování migrace místních Hadoop clusterů pro Azure HDInsight:

1. Zjistěte aktuální místní nasazení a topologie.
2. Zjistěte aktuální rozsahu projektu, časové osy a odborných znalostí týmu.
3. Zjistěte požadavky pro Azure.
4. Vytvářejte si podrobný plán na základě osvědčených postupů.

## <a name="next-steps"></a>Další postup

Přečtěte si další článek v této sérii:

- [Osvědčené postupy architektury pro místní migrace Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-architecture.md)
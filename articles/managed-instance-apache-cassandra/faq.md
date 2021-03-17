---
title: Nejčastější dotazy týkající se spravované instance Azure pro Apache Cassandra z Azure Portal
description: Nejčastější dotazy týkající se spravované instance Azure pro Apache Cassandra. Tento článek popisuje otázky, kdy používat spravované instance, výhody, omezení propustnosti, podporované oblasti a další podrobnosti o konfiguraci.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 1ba2b7d648c86912118b83a566bf2eb0800baee2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748485"
---
# <a name="frequently-asked-questions-about-azure-managed-instance-for-apache-cassandra-preview"></a>Nejčastější dotazy týkající se spravované instance Azure pro Apache Cassandra (Preview)

Tento článek obsahuje nejčastější dotazy týkající se spravované instance Azure pro Apache Cassandra. Naučíte se, kdy používat spravované instance, jejich výhody, omezení propustnosti, podporované oblasti a jejich podrobnosti o konfiguraci.

> [!IMPORTANT]
> Spravovaná instance Azure pro Apache Cassandra je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="general-faq"></a>Obecné nejčastější dotazy

### <a name="what-are-the-benefits-azure-managed-instance-for-apache-cassandra"></a>Jaké jsou výhody spravované instance Azure pro Apache Cassandra?

Databáze Apache Cassandra je správnou volbou, pokud potřebujete škálovatelnost a vysokou dostupnost, aniž by došlo k narušení výkonu. Jedná se o skvělou platformu pro kritická data z důvodu lineární škálovatelnosti a osvědčené odolnosti proti chybám u komoditních hardwarových a cloudových infrastruktur. Spravovaná instance Azure pro Apache Cassandra je služba pro správu instancí Open Source datových center Apache Cassandra nasazených v Azure.

Dá se použít buď zcela v cloudu, nebo jako součást hybridního cloudového a místního clusteru. Tato služba je skvělou volbou v případě, že potřebujete jemně odstupňovanou konfiguraci a kontrolu, které máte v Open-Source Apache Cassandra, a to bez jakýchkoli režijních nákladů na údržbu.

### <a name="why-should-i-use-this-service-instead-of-azure-cosmos-db-cassandra-api"></a>Proč mám tuto službu používat místo Azure Cosmos DB rozhraní API Cassandra?

Spravovaná instance Azure pro Apache Cassandra je poskytována týmem Azure Cosmos DB. Jedná se o samostatnou spravovanou službu pro nasazení, údržbu a škálování Open Source datových center a clusterů Apache Cassandra. [Azure Cosmos DB rozhraní API Cassandra](../cosmos-db/cassandra-introduction.md) na druhé straně je platforma jako služba, která poskytuje vrstvu interoperability pro síťový protokol Apache Cassandra. Pokud očekáváte, že se platforma chová stejně jako jakýkoli cluster Apache Cassandra, měli byste zvolit službu Managed instance. Další informace najdete v článku [spravovaná instance Azure pro Apache Cassandra Vs Azure Cosmos DB rozhraní API Cassandra](compare-cosmosdb-managed-instance.md) .

### <a name="is-azure-managed-instance-for-apache-cassandra-dependent-on-azure-cosmos-db"></a>Je spravovaná instance Azure pro Apache Cassandra závislá na Azure Cosmos DB?

Ne, neexistují žádné závislosti architektury mezi spravovanou instancí Azure pro Apache Cassandra a Azure Cosmos DB back-end. 

#### <a name="can-i-deploy-azure-managed-instance-for-apache-cassandra-in-any-region"></a>Můžu nasazovat spravovanou instanci Azure pro Apache Cassandra v jakékoli oblasti?

Ve verzi Preview bude tato spravovaná instance k dispozici v omezeném počtu oblastí.

### <a name="what-are-the-storage-and-throughput-limits-of-azure-managed-instance-for-apache-cassandra"></a>Jaké jsou limity úložiště a propustnosti spravované instance Azure pro Apache Cassandra?

Tato omezení závisí na SKU virtuálních počítačů, které zvolíte.

### <a name="what-is-the-cost-of-azure-managed-instance-for-apache-cassandra"></a>Jaké jsou ceny spravované instance Azure pro Apache Cassandra?

Poplatky za spravované instance jsou založené na ceně za základní virtuální počítače s malým označením. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/managed-instance-apache-cassandra/).

### <a name="can-i-use-yaml-file-settings-to-configure-behavior"></a>Můžu použít nastavení souboru YAML ke konfiguraci chování?

Ano, konfigurace souborů YAML můžete vložit jako součást nasazení šablony Azure Resource Manager.

### <a name="how-can-i-monitor-infrastructure-along-with-throughput"></a>Jak můžu monitorovat infrastrukturu spolu s propustností?

Hostitelem serveru [Prometheus](https://prometheus.io/docs/introduction/overview/) je sledování aktivity napříč clusterem a zpřístupňuje koncový bod. Tím se zachová 10 minut nebo 10 GB dat (podle toho, jaká prahová hodnota dosáhne prvního). Chcete-li použít toto monitorování, je třeba nastavit [federaci](https://prometheus.io/docs/prometheus/latest/federation/) a příslušný nástroj řídicího panelu, například Grafana.

### <a name="does-azure-managed-instance-for-apache-cassandra-provide-full-backups"></a>Poskytuje Azure Managed instance pro Apache Cassandra úplné zálohy?

Ano, poskytuje úplné zálohy pro Azure Storage a obnovení do nového clusteru.

### <a name="how-can-i-migrate-data-from-my-existing-apache-cassandra-cluster-to-azure-managed-instance-for-apache-cassandra"></a>Jak můžu migrovat data z mého stávajícího clusteru Apache Cassandra do spravované instance Azure pro Apache Cassandra?

Spravovaná instance Azure pro Apache Cassandra podporuje všechny funkce v Apache Cassandra pro replikaci a streamování dat mezi datovými centry.

### <a name="can-i-pair-an-on-premises-apache-cassandra-cluster-with-the-azure-managed-instance-for-apache-cassandra"></a>Můžu párovat místní cluster Apache Cassandra se službou Azure Managed instance pro Apache Cassandra?

Ano, můžete nakonfigurovat hybridní cluster pomocí Azure Virtual Network vložená datová centra nasazená službou. Data spravované instance můžou centra komunikovat s místními datovými centry v rámci stejného okruhu clusteru.

### <a name="where-can-i-give-feedback-on-azure-managed-instance-for-apache-cassandra-features"></a>Kde můžu sdělit svůj názor na spravovanou instanci Azure pro funkce Apache Cassandra?

Sdělte nám svůj názor prostřednictvím [zpětné vazby uživatele](https://feedback.azure.com/forums/263030-azure-cosmos-db?category_id=398548) pomocí kategorie "Managed Apache Cassandra".

Pokud řešíte problém s účtem, podejte prostřednictvím webu Azure Portal [žádost o podporu](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="deployment-specific-faq"></a>Nejčastější dotazy týkající se nasazení

### <a name="will-the-managed-instance-support-node-addition-cluster-status-and-node-status-commands"></a>Bude spravovaná instance podporovat příkazy pro přidání uzlu, stav clusteru a stav uzlu?

Všechny příkazy Nodetool *jen pro čtení* , například `status` jsou k dispozici prostřednictvím rozhraní příkazového řádku Azure CLI. Operace, jako je *Přidání uzlu* , však nejsou k dispozici, protože spravujeme stav uzlů ve spravované instanci. V hybridním režimu se můžete ke clusteru připojit pomocí *Nodetool*. Použití Nodetool se ale nedoporučuje, protože by mohlo dojít ke zrušení stabilizace clusteru. Může také dojít ke zrušení platnosti jakékoli smlouvy SLA pro produkční podporu týkající se stavu Datacenter spravované instance v clusteru.

### <a name="what-happens-with-various-settings-for-table-metadata"></a>Co se stane s různými nastaveními pro metadata tabulky?

Nastavení pro metadata tabulky, jako je filtr Bloom, ukládání do mezipaměti, možnost opravy gc_grace a komprese memtable_flush_period, se plně podporují stejně jako u jakéhokoli prostředí Apache Cassandra v místním prostředí.

## <a name="next-steps"></a>Další kroky

Další informace o nejčastějších dotazech v jiných rozhraních API najdete v těchto tématech:

* [Vytvoření clusteru spravované instance z Azure Portal](create-cluster-portal.md)
* [Nasazení clusteru spravovaného Apache Spark s využitím Azure Databricks](deploy-cluster-databricks.md)
* [Správa spravované instance Azure pro prostředky Apache Cassandra pomocí Azure CLI](manage-resources-cli.md)
---
title: Přehled podnikového zabezpečení ve službě Azure HDInsight
description: Seznamte se s různými metodami, abyste zajistili podnikové zabezpečení ve službě Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: overview
ms.date: 09/23/2019
ms.openlocfilehash: e1863cc54759f6cc2266073629093d4923260525
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240409"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Přehled podnikového zabezpečení ve službě Azure HDInsight

Azure HDInsight nabízí řadu metod pro řešení vašich potřeb podnikového zabezpečení. Většina těchto řešení se ve výchozím nastavení neaktivuje. Tato flexibilita vám umožní zvolit funkce zabezpečení, které jsou pro vás nejdůležitější, a pomůže vám vyhnout se placení funkcí, které nechcete. To také znamená, že je vaší zodpovědností zajistit, aby byla pro vaše nastavení a prostředí povolena správná řešení.

Tento článek popisuje řešení zabezpečení, a to dělením řešení zabezpečení na řádcích čtyř tradičních pilířů zabezpečení: zabezpečení, ověřování, autorizace a šifrování.

Tento článek také zavádí **Azure HDInsight balíček zabezpečení podniku (ESP)** , která poskytuje ověřování založené na službě Active Directory, podporu více uživatelů a řízení přístupu na základě rolí pro clustery HDInsight.

## <a name="enterprise-security-pillars"></a>Pilíře podnikového zabezpečení

Jedním ze způsobů, jak hledat v podnikovém zabezpečení, jsou řešení zabezpečení rozdělená do čtyř hlavních skupin na základě typu ovládacího prvku. Tyto skupiny se označují také jako pilíře zabezpečení a jsou to tyto: zabezpečení, ověřování, autorizace a šifrování v hraniční síti.

### <a name="perimeter-security"></a>Zabezpečení hraničních

Hraniční zabezpečení v HDInsight se dosahuje pomocí [virtuálních sítí](../hdinsight-plan-virtual-network-deployment.md). Podnikový správce může vytvořit cluster ve virtuální síti (VNET) a používat skupiny zabezpečení sítě (NSG) k omezení přístupu k virtuální síti. Pouze povolené IP adresy ve příchozích NSG pravidlech budou moci komunikovat s clusterem HDInsight. Tato konfigurace poskytuje hraniční zabezpečení.

Všechny clustery nasazené ve virtuální síti budou mít taky privátní koncový bod, který se přeloží na soukromou IP adresu uvnitř virtuální sítě, aby se privátní přístup HTTP k branám clusteru.

### <a name="authentication"></a>Ověřování

[Balíček zabezpečení podniku](apache-domain-joined-architecture.md) ze služby HDInsight poskytuje ověřování založené na službě Active Directory, podporu více uživatelů a řízení přístupu na základě rolí. Integrace služby Active Directory se dosahuje pomocí [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). S těmito možnostmi můžete vytvořit cluster HDInsight, který je připojený ke spravované doméně služby Active Directory. Pak můžete nakonfigurovat seznam zaměstnanců z podnikového, který se může ověřit a přihlásit ke clusteru.

V rámci této instalace se zaměstnanci v podniku můžou přihlašovat k uzlům clusteru pomocí svých přihlašovacích údajů do domény. Můžou taky pomocí svých přihlašovacích údajů do domény ověřit pomocí dalších schválených koncových bodů, jako jsou zobrazení Apache Ambari, ODBC, JDBC, PowerShell a rozhraní REST API pro interakci s clusterem. 

### <a name="authorization"></a>Authorization

Osvědčeným postupem, který používá většina podniků, je zajistit, že ne každý zaměstnanec má přístup ke všem podnikovým prostředkům. Správce může podobně definovat zásady řízení přístupu na základě rolí pro prostředky clusteru. To je dostupné jenom v clusterech ESP.

Správce Hadoop může nakonfigurovat řízení přístupu na základě role (RBAC) pro zabezpečení [podregistru](apache-domain-joined-run-hive.md)Apache, [HBA](apache-domain-joined-run-hbase.md) a [Kafka](apache-domain-joined-run-kafka.md) pomocí těchto modulů plug-in v Apache Ranger. Konfigurace zásad RBAC vám umožní přidružit oprávnění k roli v organizaci. Tato vrstva abstrakce usnadňuje zajištění, že lidé mají pouze oprávnění potřebná k provádění jejich pracovních odpovědností. Ranger také umožňuje auditovat přístup k datům zaměstnanců a jakékoli změny provedené v zásadách řízení přístupu.

Správce může například nakonfigurovat [Apache Ranger](https://ranger.apache.org/), aby nastavil zásady řízení přístupu pro Hive. Tato funkce zajišťuje filtrování na úrovni řádků a sloupců (maskování dat) a filtruje citlivá data proti neautorizovaným uživatelům.

### <a name="auditing"></a>Auditování

Auditování veškerého přístupu k prostředkům clusteru a datům je nezbytné ke sledování neoprávněného nebo neúmyslného přístupu k prostředkům. Je to důležité jako ochrana prostředků clusteru HDInsight před neautorizovanými uživateli a zabezpečení dat.

Správce může zobrazit a ohlásit veškerý přístup k prostředkům a datům clusteru HDInsight. Správce může také zobrazit a ohlásit všechny změny v zásadách řízení přístupu, které jsou vytvořeny v rámci podporovaných koncových bodů Apache Ranger. 

Pro přístup k protokolům auditu Apache Ranger a Ambari a také k protokolům přístupu SSH [povolte Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) a zobrazte tabulky, které poskytují záznamy auditování.

### <a name="encryption"></a>Šifrování

Ochrana dat je důležitá pro splnění požadavků organizace na zabezpečení a dodržování předpisů. Společně s omezením přístupu k datům z neautorizovaných zaměstnanců byste ji měli zašifrovat.

Úložiště dat pro clustery HDInsight, Azure Blob Storage a Azure Data Lake Storage Gen1/Gen2 podporují transparentní šifrování neaktivních uložených [dat](../../storage/common/storage-service-encryption.md) na straně serveru. Zabezpečené clustery HDInsight budou plynule fungovat s touto schopností šifrování uložených dat na straně serveru.

## <a name="shared-responsibility-model"></a>Sdílený model odpovědnosti

Následující obrázek shrnuje hlavní oblasti zabezpečení systému a řešení zabezpečení, která jsou pro vás k dispozici. Také zvýrazňuje, které oblasti zabezpečení jsou vaší zodpovědností jako zákazník a které oblasti jsou zodpovědností služby HDInsight jako poskytovatel služeb.

![Diagram sdílených odpovědností HDInsight](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Následující tabulka obsahuje odkazy na prostředky pro jednotlivé typy řešení zabezpečení.

| Oblast zabezpečení | Dostupná řešení | Zodpovědná strana |
|---|---|---|
| Zabezpečení přístupu k datům | Konfigurace [seznamů řízení přístupu seznamy ACL](../../storage/blobs/data-lake-storage-access-control.md) pro Azure Data Lake Storage Gen1 a Gen2  | Zákazníka |
|  | U účtů úložiště Povolte vlastnost ["vyžaduje zabezpečený přenos"](../../storage/common/storage-require-secure-transfer.md) . | Zákazníka |
|  | Konfigurace [Azure Storage bran firewall](../../storage/common/storage-network-security.md) a virtuálních sítí | Zákazníka |
|  | Konfigurace [koncových bodů služby virtuální sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) pro Cosmos DB a [Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Zákazníka |
|  | Zajistěte, aby bylo [šifrování TLS](../../storage/common/storage-security-tls.md) povolené pro přenos dat. | Zákazníka |
|  | Konfigurace [klíčů spravovaných zákazníkem](../../storage/common/storage-encryption-keys-portal.md) pro šifrování Azure Storage | Zákazníka |
| Zabezpečení aplikací a middlewaru | Integrace s AAD-DS a [Konfigurace ověřování](apache-domain-joined-configure-using-azure-adds.md) | Zákazníka |
|  | Konfigurace zásad [autorizace Apache Ranger](apache-domain-joined-run-hive.md) | Zákazníka |
|  | Použití [protokolů Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Zákazníka |
| Zabezpečení operačního systému | Vytváření clusterů s nejnovější zabezpečenou základní imagí | Zákazníka |
|  | Zajistěte, aby byly [opravy operačního systému](../hdinsight-os-patching.md) v pravidelných intervalech | Zákazníka |
| Zabezpečení sítě | Konfigurace [virtuální sítě](../hdinsight-plan-virtual-network-deployment.md) |
|  | Konfigurace [pravidel skupiny zabezpečení příchozí sítě (NSG)](../hdinsight-plan-virtual-network-deployment.md#networktraffic) | Zákazníka |
|  | Konfigurace [omezení odchozích přenosů](../hdinsight-restrict-outbound-traffic.md) pomocí brány firewall (Preview) | Zákazníka |
| Virtualizovaná infrastruktura | neuvedeno | HDInsight (poskytovatel cloudu) |
| Zabezpečení fyzické infrastruktury | neuvedeno | HDInsight (poskytovatel cloudu) |

## <a name="next-steps"></a>Další kroky

* [Plánování clusterů HDInsight pomocí protokolu ESP](apache-domain-joined-architecture.md)
* [Konfigurace clusterů HDInsight s ESP](apache-domain-joined-configure.md)
* [Správa clusterů HDInsight pomocí protokolu ESP](apache-domain-joined-manage.md)

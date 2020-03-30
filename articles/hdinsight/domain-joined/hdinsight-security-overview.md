---
title: Přehled podnikového zabezpečení v Azure HDInsightu
description: Seznamte se s různými způsoby zabezpečení v Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.date: 03/03/2020
ms.openlocfilehash: 95bfe7d7788133d8548598cb30c8084bf64a977f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "78267709"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Přehled podnikového zabezpečení v Azure HDInsightu

Azure HDInsight nabízí řadu metod, jak řešit vaše potřeby podnikového zabezpečení. Většina těchto řešení není ve výchozím nastavení aktivována. Tato flexibilita umožňuje zvolit funkce zabezpečení, které jsou pro vás nejdůležitější, a pomáhá vám vyhnout se placení za funkce, které nechcete. To také znamená, že je vaší odpovědností zajistit, aby byla povolena správná řešení pro vaše nastavení a prostředí.

Tento článek se zabývá řešeními zabezpečení rozdělením řešení zabezpečení podle čtyř tradičních pilířů zabezpečení: zabezpečení perimetru, ověřování, autorizace a šifrování.

Tento článek také zavádí **balíček zabezpečení Azure HDInsight Enterprise Security Package (ESP),** který poskytuje ověřování založené na službě Active Directory, podporu více uživatelů a řízení přístupu na základě rolí pro clustery HDInsight.

## <a name="enterprise-security-pillars"></a>Pilíře zabezpečení podniku

Jeden způsob pohledu na podnikové zabezpečení rozděluje řešení zabezpečení do čtyř hlavních skupin na základě typu ovládacího prvku. Tyto skupiny se také nazývají pilíře zabezpečení a jsou následující: zabezpečení perimetru, ověřování, autorizace a šifrování.

### <a name="perimeter-security"></a>Zabezpečení obvodu

Zabezpečení perimetru v HDInsight je dosaženo prostřednictvím [virtuálních sítí](../hdinsight-plan-virtual-network-deployment.md). Podnikový správce může vytvořit cluster uvnitř virtuální sítě (VNET) a pomocí skupin zabezpečení sítě (NSG) omezit přístup k virtuální síti. Pouze povolené IP adresy v příchozích pravidlech skupiny nsg budou moci komunikovat s clusterem HDInsight. Tato konfigurace poskytuje zabezpečení obvodu.

Všechny clustery nasazené ve virtuální síti budou mít také privátní koncový bod, který se překládá na privátní IP adresu uvnitř virtuální sítě pro privátní přístup HTTP k bránám clusteru.

### <a name="authentication"></a>Ověřování

[Balíček Enterprise Security Package](apache-domain-joined-architecture.md) from HDInsight poskytuje ověřování založené na službě Active Directory, podporu pro více uživatelů a řízení přístupu založené na rolích. Integrace služby Active Directory je dosažena pomocí [služby Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). Pomocí těchto funkcí můžete vytvořit cluster HDInsight, který je připojen ke spravované doméně služby Active Directory. Potom můžete nakonfigurovat seznam zaměstnanců z rozlehlé sítě, kteří se mohou přihlásit a přihlásit se ke clusteru.

V tomto nastavení se zaměstnanci rozlehlé sítě mohou přihlásit k uzlům clusteru pomocí pověření domény. Mohou také použít svá pověření domény k ověření pomocí jiných schválených koncových bodů, jako jsou apache ambari zobrazení, ODBC, JDBC, PowerShell a REST API pro interakci s clusterem.

### <a name="authorization"></a>Autorizace

Osvědčeným postupem, kterým se řídí většina podniků, je zajistit, aby ne každý zaměstnanec měl přístup ke všem zdrojům organizace. Podobně správce můžete definovat zásady řízení přístupu na základě rolí pro prostředky clusteru. To je k dispozici pouze v clusterech ESP.

Správce hadoopu může nakonfigurovat řízení přístupu na základě rolí (RBAC) pro zabezpečení Apache [Hive](apache-domain-joined-run-hive.md), [HBase](apache-domain-joined-run-hbase.md)a [Kafka](apache-domain-joined-run-kafka.md) pomocí těchto pluginů v Apache Ranger. Konfigurace zásad RBAC umožňuje přidružit oprávnění k roli v organizaci. Tato vrstva abstrakce usnadňuje zajištění, že uživatelé mají pouze oprávnění potřebná k plnění svých pracovních povinností. Ranger také umožňuje auditovat přístup zaměstnanců k datům a všechny změny provedené v zásadách řízení přístupu.

Správce může například nakonfigurovat [Apache Ranger](https://ranger.apache.org/), aby nastavil zásady řízení přístupu pro Hive. Tato funkce zajišťuje filtrování na úrovni řádků a sloupců (maskování dat) a filtruje citlivá data od neoprávněných uživatelů.

### <a name="auditing"></a>Auditování

Auditování veškerého přístupu k prostředkům clusteru a dat je nezbytné ke sledování neoprávněného nebo neúmyslného přístupu k prostředkům. Je to stejně důležité jako ochrana prostředků clusteru HDInsight před neoprávněnými uživateli a zabezpečení dat.

Správce může zobrazit a hlásit veškerý přístup k prostředkům a datům clusteru HDInsight. Správce může také zobrazit a hlásit všechny změny zásad řízení přístupu vytvořených v koncových bodech podporovaných Apache Ranger.

Chcete-li získat přístup k protokolům auditu Apache Ranger a Ambari a protokoly ssh access, [povolte Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) a zobrazte tabulky, které poskytují záznamy auditování.

### <a name="encryption"></a>Šifrování

Ochrana dat je důležitá pro splnění požadavků na zabezpečení a dodržování předpisů organizace. Spolu s omezením přístupu k datům od neoprávněných zaměstnanců byste je měli zašifrovat.

Obě úložiště dat pro clustery HDInsight, úložiště objektů blob Azure a Azure Data Lake Storage Gen1/Gen2 podporují transparentní [šifrování dat](../../storage/common/storage-service-encryption.md) na straně serveru v klidovém stavu. Zabezpečené clustery HDInsight budou bez problémů fungovat s touto funkcí šifrování dat na straně serveru v klidovém stavu.

### <a name="compliance"></a>Dodržování předpisů

Nabídky dodržování předpisů Azure jsou založeny na různých typech záruk, včetně formálních certifikací, osvědčení, ověření, autorizací a hodnocení vytvořených nezávislými auditorskými společnostmi třetích stran, smluvních změnách, hodnocení a pokynů pro zákazníky vytvořených společností Microsoft. Informace o dodržování předpisů hdinsightu najdete v [Centru zabezpečení Microsoft u](https://www.microsoft.com/trust-center) A [Našetr a přehled dodržování předpisů Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Model sdílené odpovědnosti

Následující obrázek shrnuje hlavní oblasti zabezpečení systému a řešení zabezpečení, které jsou k dispozici v každém z nich. Zdůrazňuje také, které oblasti zabezpečení jsou vaší odpovědností jako zákazníka a které oblasti jsou odpovědností HDInsight jako poskytovatele služeb.

![HdInsight sdílené odpovědnosti diagram](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Následující tabulka obsahuje odkazy na prostředky pro každý typ řešení zabezpečení.

| Bezpečnostní oblast | Dostupná řešení | Odpovědná strana |
|---|---|---|
| Zabezpečení přístupu k datům | Konfigurace [seznamů seznamů aklů](../../storage/blobs/data-lake-storage-access-control.md) pro Azure Data Lake Storage Gen1 a Gen2  | Zákazník |
|  | Povolte vlastnost ["Zabezpečený převod povinný"](../../storage/common/storage-require-secure-transfer.md) na účtech úložiště. | Zákazník |
|  | Konfigurace [bran azure storage](../../storage/common/storage-network-security.md) a virtuálních sítí | Zákazník |
|  | Konfigurace [koncových bodů služby virtuální sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) pro Cosmos DB a Azure SQL [DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Zákazník |
|  | Ujistěte se, že je pro přenos dat povoleno [šifrování TLS.](../../storage/common/storage-security-tls.md) | Zákazník |
|  | Konfigurace [klíčů spravovaných zákazníkem](../../storage/common/storage-encryption-keys-portal.md) pro šifrování Azure Storage | Zákazník |
| Zabezpečení aplikací a middlewaru | Integrace s AAD-DS a [konfigurace ověřování](apache-domain-joined-configure-using-azure-adds.md) | Zákazník |
|  | Konfigurace zásad [autorizace apache rangeru](apache-domain-joined-run-hive.md) | Zákazník |
|  | Použití [protokolů Azure Monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Zákazník |
| Zabezpečení operačního systému | Vytvoření clusterů s nejnovější zabezpečenou základní bitovou kopii | Zákazník |
|  | Zajistěte [opravy operačního spoje](../hdinsight-os-patching.md) v pravidelných intervalech | Zákazník |
| Zabezpečení sítě | Konfigurace [virtuální sítě](../hdinsight-plan-virtual-network-deployment.md) |
|  | Konfigurace [pravidel skupiny zabezpečení příchozí sítě (NSG)](../hdinsight-plan-virtual-network-deployment.md#networktraffic) | Zákazník |
|  | Konfigurace [omezení odchozího přenosu](../hdinsight-restrict-outbound-traffic.md) pomocí brány firewall | Zákazník |
| Virtualizovaná infrastruktura | Není dostupné. | HDInsight (poskytovatel cloudu) |
| Zabezpečení fyzické infrastruktury | Není dostupné. | HDInsight (poskytovatel cloudu) |

## <a name="next-steps"></a>Další kroky

* [Plánování pro clustery HDInsight pomocí ESP](apache-domain-joined-architecture.md)
* [Konfigurace clusterů HDInsight pomocí protokolu ESP](apache-domain-joined-configure.md)
* [Správa clusterů HDInsight pomocí ESP](apache-domain-joined-manage.md)

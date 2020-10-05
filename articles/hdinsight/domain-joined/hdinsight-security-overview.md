---
title: Přehled podnikového zabezpečení ve službě Azure HDInsight
description: Seznamte se s různými metodami, abyste zajistili podnikové zabezpečení ve službě Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: seoapr2020
ms.date: 08/24/2020
ms.openlocfilehash: 9cfda93cb7f99851109ab7c4a4590517f785c8a1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89292975"
---
# <a name="overview-of-enterprise-security-in-azure-hdinsight"></a>Přehled podnikového zabezpečení ve službě Azure HDInsight

Azure HDInsight nabízí řadu metod pro řešení vašich potřeb podnikového zabezpečení. Většina těchto řešení není ve výchozím nastavení aktivována. Tato flexibilita vám umožní zvolit funkce zabezpečení, které jsou pro vás nejdůležitější, a pomůže vám vyhnout se placení funkcí, které nechcete. Tato flexibilita také znamená, že je vaší zodpovědností zajistit, aby byla pro vaše nastavení a prostředí povolena správná řešení.

Tento článek popisuje řešení zabezpečení a dělí řešení zabezpečení na čtyři tradiční pilíře zabezpečení: hraniční zabezpečení, ověřování, autorizace a šifrování.

Tento článek také zavádí **Azure HDInsight balíček zabezpečení podniku (ESP)**, která poskytuje ověřování založené na službě Active Directory, podporu více uživatelů a řízení přístupu na základě rolí pro clustery HDInsight.

## <a name="enterprise-security-pillars"></a>Pilíře podnikového zabezpečení

Jedním ze způsobů, jak hledat v podnikovém zabezpečení, jsou řešení zabezpečení rozdělená do čtyř hlavních skupin na základě typu ovládacího prvku. Tyto skupiny se označují také jako pilíře zabezpečení a jsou to tyto typy: zabezpečení, ověřování, autorizace a šifrování v hraniční síti.

### <a name="perimeter-security"></a>Zabezpečení hraničních

Hraniční zabezpečení v HDInsight se dosahuje pomocí [virtuálních sítí](../hdinsight-plan-virtual-network-deployment.md). Podnikový správce může vytvořit cluster ve virtuální síti (VNET) a používat skupiny zabezpečení sítě (NSG) k omezení přístupu k virtuální síti. S clusterem HDInsight můžou komunikovat jenom povolené IP adresy v příchozích NSG pravidlech. Tato konfigurace poskytuje hraniční zabezpečení.

Všechny clustery nasazené ve virtuální síti budou mít také privátní koncový bod. Koncový bod se přeloží na soukromou IP adresu v rámci virtuální sítě pro privátní přístup HTTP ke branám clusteru.

### <a name="authentication"></a>Authentication

[Balíček zabezpečení podniku](apache-domain-joined-architecture.md) ze služby HDInsight poskytuje ověřování založené na službě Active Directory, podporu více uživatelů a řízení přístupu na základě rolí. Integrace služby Active Directory se dosahuje pomocí [Azure Active Directory Domain Services](../../active-directory-domain-services/overview.md). S těmito možnostmi můžete vytvořit cluster HDInsight připojený k doméně služby Active Directory. Pak nakonfigurujte seznam zaměstnanců z podnikového, který se může ověřit v clusteru.

V rámci této instalace se zaměstnanci v podniku můžou přihlašovat k uzlům clusteru pomocí svých přihlašovacích údajů do domény. Můžou také použít svoje přihlašovací údaje do domény k ověření s jinými schválenými koncovými body. Podobně jako zobrazení Apache Ambari, rozhraní ODBC, JDBC, PowerShell a rozhraní REST API pro interakci s clusterem.

### <a name="authorization"></a>Autorizace

V souladu s osvědčenými postupy se zajišťují, že ne každý zaměstnanec má úplný přístup ke všem podnikovým prostředkům. Správce může podobně definovat zásady řízení přístupu na základě rolí pro prostředky clusteru. Tato akce je k dispozici pouze v clusterech ESP.

Správce Hadoop může nakonfigurovat řízení přístupu na základě role (RBAC). Konfigurace zabezpečeného [podregistru](apache-domain-joined-run-hive.md)Apache, [HBA](apache-domain-joined-run-hbase.md)a [Kafka](apache-domain-joined-run-kafka.md) s moduly plug-in Apache Ranger. Konfigurace zásad RBAC vám umožní přidružit oprávnění k roli v organizaci. Tato vrstva abstrakce usnadňuje zajištění, že uživatelé mají pouze oprávnění potřebná k tomu, aby mohli provádět své pracovní úkoly. Ranger také umožňuje auditovat přístup k datům zaměstnanců a jakékoli změny provedené v zásadách řízení přístupu.

Správce může například nakonfigurovat [Apache Ranger](https://ranger.apache.org/), aby nastavil zásady řízení přístupu pro Hive. Tato funkce zajišťuje filtrování na úrovni řádků a sloupců (maskování dat). A filtruje citlivá data od neautorizovaných uživatelů.

### <a name="auditing"></a>Auditování

Auditování přístupu k prostředkům clusteru je nezbytné ke sledování neautorizovaného nebo neúmyslného přístupu k prostředkům. Je to důležité jako ochrana prostředků clusteru před neoprávněným přístupem.

Správce může zobrazit a ohlásit veškerý přístup k prostředkům a datům clusteru HDInsight. Správce může zobrazit a ohlásit změny v zásadách řízení přístupu.

Pro přístup k protokolům auditu Apache Ranger a Ambari a k protokolům přístupu SSH [povolte Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md#cluster-auditing) a zobrazte tabulky, které poskytují záznamy auditování.

### <a name="encryption"></a>Šifrování

Ochrana dat je důležitá pro splnění požadavků organizace na zabezpečení a dodržování předpisů. Společně s omezením přístupu k datům z neautorizovaných zaměstnanců byste ji měli zašifrovat.

HDInsight podporuje šifrování dat v klidovém umístění s použitím spravovaných i [zákaznických klíčů](../disk-encryption.md)spravovaných platformou. Šifrování dat při přenosu se zpracovává pomocí TLS i IPSec. Další informace najdete [v tématu šifrování při přenosu pro Azure HDInsight](encryption-in-transit.md) .

### <a name="compliance"></a>Dodržování předpisů

Nabídky dodržování předpisů Azure vycházejí z různých typů ujištění, včetně formálních certifikací. Také ověření identity, ověřování a autorizace. Posouzení vytvářené nezávisle auditory třetích stran. Smluvní změny, samy posouzení a dokumenty o doporučení pro zákazníky, které vytvořil Microsoft. Informace o kompatibilitě HDInsight najdete na [webu Microsoft Trust Center](https://www.microsoft.com/trust-center) a v tématu [přehled dodržování předpisů Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).

## <a name="shared-responsibility-model"></a>Model sdílené odpovědnosti

Následující obrázek shrnuje hlavní oblasti zabezpečení systému a řešení zabezpečení, která jsou pro vás k dispozici. Také zvýrazňuje, které oblasti zabezpečení jsou vaší zodpovědností jako zákazník. A které oblasti jsou zodpovědností služby HDInsight jako poskytovatele služeb.

![Diagram sdílených odpovědností HDInsight](./media/hdinsight-security-overview/hdinsight-shared-responsibility.png)

Následující tabulka obsahuje odkazy na prostředky pro jednotlivé typy řešení zabezpečení.

| Oblast zabezpečení | Dostupná řešení | Zodpovědná strana |
|---|---|---|
| Zabezpečení přístupu k datům | Konfigurace [seznamů řízení přístupu seznamy ACL](../../storage/blobs/data-lake-storage-access-control.md) pro Azure Data Lake Storage Gen1 a Gen2  | Zákazník |
|  | U účtů úložiště Povolte vlastnost ["vyžaduje zabezpečený přenos"](../../storage/common/storage-require-secure-transfer.md) . | Zákazník |
|  | Konfigurace [Azure Storage bran firewall](../../storage/common/storage-network-security.md) a virtuálních sítí | Zákazník |
|  | Konfigurace [koncových bodů služby virtuální sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) pro Cosmos DB a [Azure SQL DB](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview) | Zákazník |
|  | Zajistěte, aby funkce [šifrování v rámci přenosu](./encryption-in-transit.md) povolila používání protokolu TLS a protokolu IPSec pro komunikaci mezi clustery. | Zákazník |
|  | Konfigurace [klíčů spravovaných zákazníkem](../../storage/common/storage-encryption-keys-portal.md) pro šifrování Azure Storage | Zákazník |
|  | Řízení přístupu k datům prostřednictvím podpory Azure pomocí [bezpečnostního modulu zákazníka](https://docs.microsoft.com/azure/security/fundamentals/customer-lockbox-overview) | Zákazník |
| Zabezpečení aplikací a middlewaru | Integrace s AAD-DS a [Konfigurace ověřování](apache-domain-joined-configure-using-azure-adds.md) | Zákazník |
|  | Konfigurace zásad [autorizace Apache Ranger](apache-domain-joined-run-hive.md) | Zákazník |
|  | Použití [protokolů Azure monitor](../hdinsight-hadoop-oms-log-analytics-tutorial.md) | Zákazník |
| Zabezpečení operačního systému | Vytváření clusterů s nejnovější zabezpečenou základní imagí | Zákazník |
|  | Zajistěte, aby byly [opravy operačního systému](../hdinsight-os-patching.md) v pravidelných intervalech | Zákazník |
| Zabezpečení sítě | Konfigurace [virtuální sítě](../hdinsight-plan-virtual-network-deployment.md) |
|  | Konfigurace [pravidel skupiny zabezpečení příchozí sítě (NSG)](../control-network-traffic.md) | Zákazník |
|  | Konfigurace [omezení odchozích přenosů](../hdinsight-restrict-outbound-traffic.md) pomocí brány firewall | Zákazník |
| Virtualizovaná infrastruktura | – | HDInsight (poskytovatel cloudu) |
| Zabezpečení fyzické infrastruktury | – | HDInsight (poskytovatel cloudu) |

## <a name="next-steps"></a>Další kroky

* [Plánování clusterů HDInsight pomocí protokolu ESP](apache-domain-joined-architecture.md)
* [Konfigurace clusterů HDInsight s ESP](apache-domain-joined-configure.md)
* [Správa clusterů HDInsight pomocí protokolu ESP](apache-domain-joined-manage.md)

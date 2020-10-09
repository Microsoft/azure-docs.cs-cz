---
title: Nejčastější dotazy týkající se Apache Kafka ve službě Azure HDInsight
description: Získejte odpovědi na běžné otázky týkající se Apache Kafka v Azure HDInsight, spravované cloudové službě Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: d27c648980338b3a9e12bd618eb4620fe9988fd7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "80436884"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Nejčastější dotazy k Apache Kafka ve službě Azure HDInsight

Tento článek popisuje některé běžné otázky týkající se používání Apache Kafka v Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Jaké verze Kafka podporuje HDInsight?

Další informace o oficiálně podporovaných verzích komponent v HDInsight najdete v části [Apache Hadoop komponenty a verze, které jsou k dispozici v HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions). K zajištění nejlepšího možného výkonu a uživatelského prostředí doporučujeme vždy používat nejnovější verzi.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Jaké prostředky jsou k dispozici v clusteru HDInsight Kafka a jaké prostředky se mi účtují?

Cluster HDInsight Kafka zahrnuje následující zdroje:

* Hlavní uzly
* Uzly Zookeeper
* Uzly zprostředkovatele (pracovní proces) 
* Azure Managed Disks připojen k uzlům zprostředkovatele
* Uzly brány

Všechny tyto prostředky se účtují na základě našeho [cenového modelu HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/), s výjimkou uzlů bran. Neúčtují se vám žádné z uzlů brány.

Podrobnější popis různých typů uzlů najdete v tématu [Architektura virtuální sítě Azure HDInsight](../hdinsight-virtual-network-architecture.md). Ceny vycházejí z využití uzlů na minuty. Ceny se liší v závislosti na velikosti uzlu, počtu uzlů, typu používaného spravovaného disku a oblasti.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Funguje v HDInsight Apache Kafka rozhraní API?

Ano, HDInsight používá nativní rozhraní Kafka API. Kód klientské aplikace nemusí být změněn. Viz [kurz: použití rozhraní API pro Apache Kafka výrobce a spotřebitele](./apache-kafka-producer-consumer-api.md) k zobrazení, jak můžete v clusteru používat rozhraní API pro producenta a příjemce založená na jazyce Java.

## <a name="can-i-change-cluster-configurations"></a>Můžu změnit konfiguraci clusteru?

Ano, prostřednictvím portálu Ambari. Každá součást na portálu má **konfigurační** část, kterou lze použít ke změně konfigurace součástí. Některé změny můžou vyžadovat restartování zprostředkovatele.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Jaký typ ověřování podporuje HDInsight pro Apache Kafka?

Pomocí [balíček zabezpečení podniku (ESP)](../domain-joined/apache-domain-joined-architecture.md)můžete pro své clustery Kafka získat zabezpečení na úrovni tématu. Další informace najdete v tématu [kurz: Konfigurace zásad Apache Kafka v HDInsight pomocí balíček zabezpečení podniku (Preview)](../domain-joined/apache-domain-joined-run-kafka.md).

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Jsou moje data zašifrovaná? Můžu používat vlastní klíče?

Všechny zprávy Kafka na spravovaných discích jsou šifrovány pomocí [šifrování služby Azure Storage (SSE)](../../storage/common/storage-service-encryption.md). Přenášená data (například data přenášená od klientů ke zprostředkovatelům a jiným způsobem kolem) nejsou ve výchozím nastavení šifrována. Takový provoz je možné zašifrovat nastavením protokolu [TLS na vlastní](./apache-kafka-ssl-encryption-authentication.md). Kromě toho umožňuje HDInsight spravovat vlastní klíče pro šifrování neaktivních dat. Další informace najdete v tématu [šifrování klíčového disku spravovaného zákazníkem](../disk-encryption.md).

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Návody připojit klienty k mému clusteru?

Aby mohli klienti Kafka komunikovat s Kafka brokery, musí být schopni kontaktovat zprostředkovatele v síti. U clusterů HDInsight je Virtual Network (VNet) hranice zabezpečení. Nejjednodušší způsob, jak propojit klienty s clusterem HDInsight, je vytvořit klienty v rámci stejné virtuální sítě jako cluster. Mezi další scénáře patří:

* Připojení klientů v jiné virtuální síti Azure – partnerský vztah pro virtuální síť clusteru a virtuální síť klienta a konfigurace clusteru pro [reklamu protokolu IP](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). Při použití reklamní reklamy musí Kafka klienti použít IP adresy zprostředkovatele pro připojení ke zprostředkovatelům místo plně kvalifikovaných názvů domény (FQDN).

* Připojení místních klientů – pomocí sítě VPN a nastavení vlastních serverů DNS, jak je popsáno v tématu [plánování virtuální sítě pro Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Vytvoření veřejného koncového bodu pro službu Kafka – Pokud vaše podnikové požadavky na zabezpečení povolují, můžete nasadit veřejný koncový bod pro vaše zprostředkovatele Kafka nebo samoobslužný počáteční zdrojový bod REST s veřejným koncovým bodem.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Můžu přidat další místo na disku v existujícím clusteru?

Pokud chcete zvýšit množství volného místa dostupného pro Kafka zprávy, můžete zvýšit počet uzlů. V současné době se nepodporují přidání dalších disků do existujícího clusteru.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Může cluster Kafka pracovat s datacihly? 

Ano, clustery Kafka můžou pracovat s datacihly, pokud jsou ve stejné virtuální síti. Pokud chcete použít cluster Kafka s datacihly, vytvořte virtuální síť s clusterem HDInsight Kafka a pak tuto virtuální síť zadejte při vytváření pracovního prostoru datacihly a používání injektáže virtuální sítě. Další informace najdete v tématu [nasazení Azure Databricks ve službě Azure Virtual Network (vkládání virtuální sítě)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Při vytváření pracovního prostoru datacihly bude nutné zadat názvy zprostředkovatelů spuštění clusteru Kafka. Informace o načtení názvů zprostředkovatelů Kafka najdete v tématu [získání informací o hostiteli Apache Zookeeper a Broker](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Jak můžu mít maximální odolnost dat?

Odolnost dat umožňuje dosáhnout nejnižšího rizika ztráty zprávy. Aby bylo možné dosáhnout maximální trvanlivosti dat, doporučujeme následující nastavení:

* použití minimálního faktoru replikace 3 ve většině oblastí
* Použijte minimální faktor replikace 4 v oblastech, které mají jenom dvě domény selhání.
* zakázat volby nečistého vedoucího procesu
* Nastavte **min. inSync. repliky** na 2 nebo více – Tato změna vymění počet replik, které musí být zcela synchronizované s vedoucím, než může zapisovat pokračovat.
* Nastavte vlastnost **ackes** na hodnotu **All** – Tato vlastnost vyžaduje, aby všechny repliky potvrdily všechny zprávy.

Konfigurace Kafka pro zajištění vyšší konzistence dat má vliv na dostupnost zprostředkovatelů při vytváření požadavků.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Můžu replikovat data do více clusterů?

Ano, data se dají replikovat do několika clusterů pomocí Kafka nástroje MirrorMaker. Podrobnosti o nastavení nástroje MirrorMaker najdete v [tématech zrcadlení Apache Kafka](apache-kafka-mirroring.md). Kromě toho existují i další samoobslužné Open Source technologie a dodavatele, které vám pomůžou dosáhnout replikace do několika clusterů, jako je [Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Můžu upgradovat svůj cluster? Jak mám upgradovat svůj cluster?

V současné době nepodporujeme místní upgrady verzí clusteru. Pokud chcete cluster aktualizovat na vyšší verzi Kafka, vytvořte nový cluster s požadovanou verzí a migrujte své klienty Kafka na použití nového clusteru.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Návody monitorovat můj cluster Kafka?

K analýze [protokolů Kafka](./apache-kafka-log-analytics-operations-management.md)použijte Azure monitor.

## <a name="next-steps"></a>Další kroky

* [Nastavení šifrování a ověřování TLS pro Apache Kafka ve službě Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Použití nástroje MirrorMaker k replikaci témat Apache Kafka s využitím platformy Kafka ve službě HDInsight](./apache-kafka-mirroring.md)

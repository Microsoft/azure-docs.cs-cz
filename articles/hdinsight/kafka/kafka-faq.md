---
title: Nejčastější dotazy k Apache Kafka v Azure HDInsight
description: Získejte odpovědi na běžné otázky týkající se Apache Kafka na Azure HDInsight, spravované cloudové službě Hadoop.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: d27c648980338b3a9e12bd618eb4620fe9988fd7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80436884"
---
# <a name="frequently-asked-questions-about-apache-kafka-in-azure-hdinsight"></a>Nejčastější dotazy k Apache Kafka ve službě Azure HDInsight

Tento článek řeší některé běžné otázky týkající se používání Apache Kafka na Azure HDInsight.

## <a name="what-kafka-versions-are-supported-by-hdinsight"></a>Jaké verze Kafka jsou podporovány HDInsight?

Další informace o oficiálně podporovaných verzích komponent HDInsight naleznete v části [Jaké jsou komponenty apache hadoop a verze dostupné v HDInsight?](../hdinsight-component-versioning.md#supported-hdinsight-versions) Doporučujeme vždy používat nejnovější verzi, abyste zajistili co nejlepší výkon a uživatelské prostředí.

## <a name="what-resources-are-provided-in-an-hdinsight-kafka-cluster-and-what-resources-am-i-charged-for"></a>Jaké prostředky jsou k dispozici v clusteru HDInsight Kafka a jaké prostředky se mi účtují?

Cluster HDInsight Kafka obsahuje následující prostředky:

* Hlavní uzly
* Zookeeper uzly
* Broker (pracovník) uzly 
* Spravované disky Azure připojené k uzlům zprostředkovatele
* Uzly brány

Všechny tyto prostředky se účtují na základě našeho [cenového modelu HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/), s výjimkou uzlů brány. Za uzly brány se neúčtují poplatky.

Podrobnější popis různých typů uzlů najdete v tématu [Architektura virtuální sítě Azure HDInsight](../hdinsight-virtual-network-architecture.md). Ceny jsou založeny na využití uzlu za minutu. Ceny se liší v závislosti na velikosti uzlu, počtu uzlů, typu spravovaného disku a oblasti.

## <a name="do-apache-kafka-apis-work-with-hdinsight"></a>Fungují rozhraní API Apache Kafka s HDInsight?

Ano, HDInsight používá nativní rozhraní API Kafka. Kód klientské aplikace se nemusí měnit. Viz [kurz: Pomocí apache kafka výrobce a spotřebitele API](./apache-kafka-producer-consumer-api.md) vidět, jak můžete použít Java-založené výrobce / spotřebitele API s clusteru.

## <a name="can-i-change-cluster-configurations"></a>Mohu změnit konfigurace clusteru?

Ano, přes portál Ambari. Každá komponenta na portálu má část **configs,** kterou lze použít ke změně konfigurace komponent. Některé změny mohou vyžadovat restartování zprostředkovatele.

## <a name="what-type-of-authentication-does-hdinsight-support-for-apache-kafka"></a>Jaký typ ověřování podporuje HDInsight pro Apache Kafka?

Pomocí [balíčku zabezpečení rozlehlé sítě (ESP)](../domain-joined/apache-domain-joined-architecture.md)můžete získat zabezpečení na úrovni tématu pro jejich clustery Kafka. Další informace [najdete v tématu Výuka: Konfigurace zásad Apache Kafka v HDInsightu pomocí balíčku podnikového zabezpečení (Preview).](../domain-joined/apache-domain-joined-run-kafka.md)

## <a name="is-my-data-encrypted-can-i-use-my-own-keys"></a>Jsou moje data šifrována? Mohu použít vlastní klíče?

Všechny zprávy Kafka na spravovaných discích jsou šifrované pomocí [Azure Storage Service Encryption (SSE)](../../storage/common/storage-service-encryption.md). Data v tranzitu (například data přenášená z klientů do zprostředkovatelů a naopak) není ve výchozím nastavení šifrována. Je možné šifrovat takový provoz [nastavením TLS na vlastní pěst](./apache-kafka-ssl-encryption-authentication.md). Kromě toho HDInsight umožňuje spravovat své vlastní klíče k šifrování dat v klidovém stavu. Další informace naleznete v [tématu Šifrování disku klíče spravované zákazníkem](../disk-encryption.md).

## <a name="how-do-i-connect-clients-to-my-cluster"></a>Jak lze připojit klienty ke svému clusteru?

Aby klienti Kafka mohli komunikovat s makléři Kafky, musí být schopni oslovit makléře přes síť. Pro clustery HDInsight je virtuální síť (VNet) hranice zabezpečení. Proto nejjednodušší způsob, jak připojit klienty k clusteru HDInsight je vytvořit klienty v rámci stejné virtuální sítě jako clusteru. Mezi další scénáře patří:

* Připojení klientů v jiné virtuální síti Azure – peer clusteru virtuální sítě a klientské virtuální sítě a nakonfigurovat cluster pro [IP reklama](apache-kafka-connect-vpn-gateway.md#configure-kafka-for-ip-advertising). Při použití IP reklamy musí klienti Kafka používat IP adresy Broker pro připojení k makléřům, namísto plně kvalifikovaných doménových jmen (FQDNs).

* Připojení místních klientů – použití sítě VPN a nastavení vlastních serverů DNS, jak je popsáno v [webu Plánování virtuální sítě pro Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md).

* Vytvoření veřejného koncového bodu pro vaši službu Kafka – Pokud to umožňují vaše požadavky na podnikové zabezpečení, můžete nasadit veřejný koncový bod pro zprostředkovatele Kafka nebo koncový bod REST s otevřeným zdrojovým kódem s vlastním spravovaným zdrojem s veřejným koncovým bodem.

## <a name="can-i-add-more-disk-space-on-an-existing-cluster"></a>Je možné přidat více místa na disku v existujícím clusteru?

Chcete-li zvýšit množství místa, které je k dispozici pro zprávy Kafka, můžete zvýšit počet uzlů. V současné době není přidání dalších disků do existujícího clusteru podporováno.

## <a name="can-a-kafka-cluster-work-with-databricks"></a>Může cluster Kafka pracovat s Databricks? 

Ano, clustery Kafka můžou pracovat s Databricks, pokud jsou ve stejné virtuální síti. Pokud chcete použít cluster Kafka s Databricks, vytvořte virtuální síť s clusterem HDInsight Kafka a pak určete tuto virtuální síť při vytváření pracovního prostoru Databricks a použití vkládání virtuální sítě. Další informace najdete [v tématu Nasazení Azure Databricks ve vaší virtuální síti Azure (VNet Injection)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Při vytváření pracovního prostoru Databricks budete muset zadat názvy zprostředkovatelů bootstrap clusteru Kafka. Informace o načítání názvů brokerů Kafka naleznete v [tématu Získání informací o hostiteli Apache Zookeeper a Broker](https://docs.microsoft.com/azure/hdinsight/kafka/apache-kafka-get-started#getkafkainfo).

## <a name="how-can-i-have-maximum-data-durability"></a>Jak mohu mít maximální odolnost dat?

Odolnost dat umožňuje dosáhnout nejnižšího rizika ztráty zprávy. Chcete-li dosáhnout maximální odolnosti dat, doporučujeme následující nastavení:

* používat minimální replikační faktor 3 ve většině regionů
* použít minimální faktor replikace 4 v oblastech s pouze dvěma doménami selhání
* zakázat nečisté volby vůdce
* nastavit **min.insync.replicas** na 2 nebo více - to změní počet replik, které musí být zcela synchronizovány s odkazem před zápisem může pokračovat
* nastavit vlastnost **acks** na **všechny** - tato vlastnost vyžaduje, aby všechny repliky potvrdily všechny zprávy

Konfigurace Kafka pro vyšší konzistenci dat ovlivňuje dostupnost zprostředkovatelů vytvářet požadavky.

## <a name="can-i-replicate-my-data-to-multiple-clusters"></a>Mohu replikovat data do více clusterů?

Ano, data mohou být replikována do více clusterů pomocí Kafka MirrorMaker. Podrobnosti o nastavení MirrorMakeru naleznete v [tématech Mirror Apache Kafka](apache-kafka-mirroring.md). Kromě toho existují další samostatně spravované open source technologie a dodavatelé, které mohou pomoci dosáhnout replikace do více clusterů, jako je [například Brooklin](https://github.com/linkedin/Brooklin/).

## <a name="can-i-upgrade-my-cluster-how-should-i-upgrade-my-cluster"></a>Mohu svůj cluster upgradovat? Jak mám upgradovat svůj cluster?

V současné době nepodporujeme inovace verzí clusteru na místě. Chcete-li aktualizovat cluster na vyšší verzi Kafka, vytvořte nový cluster s požadovanou verzí a migrujte klienty Kafka, abyste používali nový cluster.

## <a name="how-do-i-monitor-my-kafka-cluster"></a>Jak mohu sledovat svůj cluster Kafka?

Pomocí azure monitoru můžete analyzovat [protokoly Kafka](./apache-kafka-log-analytics-operations-management.md).

## <a name="next-steps"></a>Další kroky

* [Nastavení šifrování a ověřování TLS pro Apache Kafka v Azure HDInsight](./apache-kafka-ssl-encryption-authentication.md)
* [Použití nástroje MirrorMaker k replikaci témat Apache Kafka s využitím platformy Kafka ve službě HDInsight](./apache-kafka-mirroring.md)

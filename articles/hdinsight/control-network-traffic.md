---
title: Řízení síťového provozu ve službě Azure HDInsight
description: Naučte se techniky řízení příchozího a odchozího provozu do clusterů Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: 66b078e8dfa1daa100978f04283e9bba7158bddf
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867028"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Řízení síťového provozu ve službě Azure HDInsight

Síťový provoz ve virtuálních sítích Azure je možné řídit pomocí následujících metod:

* **Skupiny zabezpečení sítě** (NSG) umožňují filtrovat příchozí a odchozí provoz do sítě. Další informace najdete v dokumentu [filtrování provozu sítě s použitím skupin zabezpečení sítě](../virtual-network/network-security-groups-overview.md) .

* **Síťová virtuální zařízení** (síťové virtuální zařízení) se dají použít jenom u odchozích přenosů. Síťová virtuální zařízení replikuje funkce zařízení, jako jsou brány firewall a směrovače. Další informace najdete v dokumentu [Síťová zařízení](https://azure.microsoft.com/solutions/network-appliances) .

Jako spravovaná služba HDInsight vyžaduje neomezený přístup k stavům HDInsight a službám pro správu obou pro příchozí i odchozí provoz z virtuální sítě. Při použití skupin zabezpečení sítě je potřeba zajistit, že tyto služby můžou dál komunikovat s clusterem HDInsight.

:::image type="content" source="./media/control-network-traffic/hdinsight-vnet-diagram.png" alt-text="Diagram entit HDInsight vytvořených ve vlastní virtuální síti Azure" border="false":::

## <a name="hdinsight-with-network-security-groups"></a>HDInsight se skupinami zabezpečení sítě

Pokud plánujete používat **skupiny zabezpečení sítě** k řízení síťového provozu, proveďte před instalací HDInsight tyto akce:

1. Identifikujte oblast Azure, kterou plánujete použít pro HDInsight.

2. Identifikujte značky služeb, které služba HDInsight pro vaši oblast vyžaduje. Existují různé způsoby, jak tyto značky služeb získat:
    1. Podívejte se na seznam publikovaných značek služby ve [značkách služby NSG (Network Security Group) pro Azure HDInsight](hdinsight-service-tags.md). 
    2. Pokud se v seznamu nenachází vaše oblast, použijte [rozhraní API zjišťování značek služby](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) a vyhledejte značku služby pro vaši oblast.
    3. Pokud nemůžete používat rozhraní API, Stáhněte si [soubor JSON pro tag služby](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) a vyhledejte požadovanou oblast.


3. Vytvořte nebo upravte skupiny zabezpečení sítě pro podsíť, do které plánujete nainstalovat HDInsight.

    * __Skupiny zabezpečení sítě__: povolí __příchozí__ provoz na portu __443__ z IP adres. Tím se zajistí, že se služby HDInsight Management budou moci spojit s clusterem mimo virtuální síť. U clusterů s povoleným __Kafka REST__ povolte i __příchozí__ přenosy na portu __9400__ . Tím se zajistí, že proxy server Kafka REST je dosažitelný.

Další informace o skupinách zabezpečení sítě najdete v tématu [Přehled skupin zabezpečení sítě](../virtual-network/network-security-groups-overview.md).

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Řízení odchozího provozu z clusterů HDInsight

Další informace o řízení odchozího provozu z clusterů HDInsight najdete v tématu [Konfigurace omezení odchozích síťových přenosů pro clustery Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Vynucené tunelové propojení do místního prostředí

Vynucené tunelování je uživatelem definovaná konfigurace směrování, kdy se veškerý provoz z podsítě připravuje na konkrétní síť nebo umístění, třeba na místní síť nebo bránu firewall. Vynucené tunelování všech přenosů dat zpět do místní _instalace se nedoporučuje kvůli_ velkým objemům přenosu dat a potenciálním dopadům na výkon.

Zákazníci, kteří mají zájem o nastavení vynuceného tunelování, by měli používat [vlastní metaúložiště](./hdinsight-use-external-metadata-stores.md) a nastavit vhodné připojení z podsítě clusteru nebo místní sítě k těmto vlastním metaúložiště.

Příklad nastavení UDR pomocí Azure Firewall najdete v tématu [Konfigurace omezení odchozích síťových přenosů pro clustery Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

## <a name="required-ports"></a>Požadované porty

Pokud máte v úmyslu používat **bránu firewall** a přistupovat ke clusteru mimo jiné na určitých portech, budete možná muset na těchto portech, které jsou potřeba pro váš scénář, zapnout provoz. Ve výchozím nastavení se nevyžaduje žádné speciální filtrování portů, dokud provoz správy Azure, který je vysvětlen v předchozí části, může oslovit cluster na portu 443.

Seznam portů pro konkrétní služby najdete v tématu [porty používané službou Apache Hadoop Services na dokumentu HDInsight](hdinsight-hadoop-port-settings-for-services.md) .

Další informace o pravidlech brány firewall pro virtuální zařízení najdete v dokumentu [scénář virtuální zařízení](../virtual-network/virtual-network-scenario-udr-gw-nva.md) .

## <a name="next-steps"></a>Další kroky

* Ukázky kódu a příklady vytváření virtuálních sítí Azure najdete v tématu [Vytvoření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md).
* Ucelený příklad konfigurace služby HDInsight pro připojení k místní síti najdete v tématu [připojení HDInsight k místní síti](./connect-on-premises-network.md).
* Další informace o virtuálních sítích Azure najdete v tématu [Přehled azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md).
* Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).
* Další informace o virtuálních sítích najdete v tématu [plánování virtuální sítě pro HDInsight](./hdinsight-plan-virtual-network-deployment.md).
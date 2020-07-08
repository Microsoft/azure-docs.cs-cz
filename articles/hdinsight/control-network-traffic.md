---
title: Řízení síťového provozu ve službě Azure HDInsight
description: Naučte se techniky řízení příchozího a odchozího provozu do clusterů Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: 54a55789cf867c97cf2384b48f1e5545ee54dafc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83773402"
---
# <a name="control-network-traffic-in-azure-hdinsight"></a>Řízení síťového provozu ve službě Azure HDInsight

Síťový provoz ve virtuálních sítích Azure je možné řídit pomocí následujících metod:

* **Skupiny zabezpečení sítě** (NSG) umožňují filtrovat příchozí a odchozí provoz do sítě. Další informace najdete v dokumentu [filtrování provozu sítě s použitím skupin zabezpečení sítě](../virtual-network/security-overview.md) .

* **Síťová virtuální zařízení** (síťové virtuální zařízení) se dají použít jenom u odchozích přenosů. Síťová virtuální zařízení replikuje funkce zařízení, jako jsou brány firewall a směrovače. Další informace najdete v dokumentu [Síťová zařízení](https://azure.microsoft.com/solutions/network-appliances) .

Jako spravovaná služba HDInsight vyžaduje neomezený přístup k stavům HDInsight a službám pro správu obou pro příchozí i odchozí provoz z virtuální sítě. Při použití skupin zabezpečení sítě je potřeba zajistit, že tyto služby můžou dál komunikovat s clusterem HDInsight.

![Diagram entit HDInsight vytvořených ve vlastní virtuální síti Azure](./media/control-network-traffic/hdinsight-vnet-diagram.png)

## <a name="hdinsight-with-network-security-groups"></a>HDInsight se skupinami zabezpečení sítě

Pokud plánujete používat **skupiny zabezpečení sítě** k řízení síťového provozu, proveďte před instalací HDInsight tyto akce:

1. Identifikujte oblast Azure, kterou plánujete použít pro HDInsight.

2. Identifikujte značky služeb, které služba HDInsight pro vaši oblast vyžaduje. Další informace najdete v tématu [značky služby skupiny zabezpečení sítě (NSG) pro Azure HDInsight](hdinsight-service-tags.md).

3. Vytvořte nebo upravte skupiny zabezpečení sítě pro podsíť, do které plánujete nainstalovat HDInsight.

    * __Skupiny zabezpečení sítě__: povolí __příchozí__ provoz na portu __443__ z IP adres. Tím se zajistí, že se služby HDInsight Management budou moci spojit s clusterem mimo virtuální síť. U clusterů s povoleným __Kafka REST__ povolte i __příchozí__ přenosy na portu __9400__ . Tím se zajistí, že proxy server Kafka REST je dosažitelný.

Další informace o skupinách zabezpečení sítě najdete v tématu [Přehled skupin zabezpečení sítě](../virtual-network/security-overview.md).

## <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Řízení odchozího provozu z clusterů HDInsight

Další informace o řízení odchozího provozu z clusterů HDInsight najdete v tématu [Konfigurace omezení odchozích síťových přenosů pro clustery Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

### <a name="forced-tunneling-to-on-premises"></a>Vynucené tunelové propojení do místního prostředí

Vynucené tunelování je uživatelem definovaná konfigurace směrování, kdy se veškerý provoz z podsítě připravuje na konkrétní síť nebo umístění, třeba na místní síť nebo bránu firewall. Vynucené tunelování všech přenosů dat zpět do místní _instalace se nedoporučuje kvůli_ velkým objemům přenosu dat a potenciálním dopadům na výkon.

Zákazníci, kteří mají zájem o nastavení vynuceného tunelování, by měli používat [vlastní metaúložiště](./hdinsight-use-external-metadata-stores.md) a nastavit vhodné připojení z podsítě clusteru nebo místní sítě k těmto vlastním metaúložiště.

Příklad nastavení UDR pomocí Azure Firewall najdete v tématu [Konfigurace omezení odchozích síťových přenosů pro clustery Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

## <a name="required-ip-addresses"></a>Požadované IP adresy

Pokud ke kontrole provozu používáte skupiny zabezpečení sítě nebo trasy definované uživatelem, přečtěte si téma [IP adresy správy HDInsight](hdinsight-management-ip-addresses.md).

## <a name="required-ports"></a>Požadované porty

Pokud máte v úmyslu používat **bránu firewall** a přistupovat ke clusteru mimo jiné na určitých portech, budete možná muset na těchto portech, které jsou potřeba pro váš scénář, zapnout provoz. Ve výchozím nastavení se nevyžaduje žádný zvláštní seznam povolených portů, pokud provoz správy Azure, který je vysvětlen v předchozí části, má povolený přístup ke clusteru na portu 443.

Seznam portů pro konkrétní služby najdete v tématu [porty používané službou Apache Hadoop Services na dokumentu HDInsight](hdinsight-hadoop-port-settings-for-services.md) .

Další informace o pravidlech brány firewall pro virtuální zařízení najdete v dokumentu [scénář virtuální zařízení](../virtual-network/virtual-network-scenario-udr-gw-nva.md) .

## <a name="next-steps"></a>Další kroky

* Ukázky kódu a příklady vytváření virtuálních sítí Azure najdete v tématu [Vytvoření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md).
* Ucelený příklad konfigurace služby HDInsight pro připojení k místní síti najdete v tématu [připojení HDInsight k místní síti](./connect-on-premises-network.md).
* Další informace o virtuálních sítích Azure najdete v tématu [Přehled azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení sítě](../virtual-network/security-overview.md).
* Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).
* Další informace o virtuálních sítích najdete v tématu [plánování virtuální sítě pro HDInsight](./hdinsight-plan-virtual-network-deployment.md).

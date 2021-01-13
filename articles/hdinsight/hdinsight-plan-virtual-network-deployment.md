---
title: Plánování virtuální sítě pro Azure HDInsight
description: Naučte se, jak naplánovat nasazení služby Azure Virtual Network pro připojení HDInsight k jiným cloudovým prostředkům nebo prostředkům ve vašem datovém centru.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: 429f0b2ffe486279a1c429fc7a153e6528642f54
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2021
ms.locfileid: "98131657"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Plánování virtuální sítě pro Azure HDInsight

Tento článek poskytuje základní informace o používání [Azure Virtual Networks](../virtual-network/virtual-networks-overview.md) (virtuální sítě) se službou Azure HDInsight. Popisuje také rozhodnutí o návrhu a implementaci, která je nutné provést předtím, než budete moci implementovat virtuální síť pro cluster HDInsight. Po dokončení fáze plánování můžete pokračovat v [vytváření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md). Další informace o IP adresách pro správu HDInsight, které jsou potřeba ke správné konfiguraci skupin zabezpečení sítě (skupin zabezpečení sítě) a uživatelem definovaných tras, najdete v článku [IP adresy správy HDInsight](hdinsight-management-ip-addresses.md).

Použití Azure Virtual Network umožňuje následující scénáře:

* Připojení ke službě HDInsight přímo z místní sítě.
* Připojení HDInsight k úložištím dat ve službě Azure Virtual Network.
* Přímý přístup k Apache Hadoop službám, které nejsou veřejně dostupné po internetu. Například Apache Kafka rozhraní API nebo rozhraní Apache HBA Java API.

> [!IMPORTANT]
> Při vytváření clusteru HDInsight ve virtuální síti se vytvoří několik síťových prostředků, jako jsou síťové karty a nástroje pro vyrovnávání zatížení. Tyto síťové prostředky **neodstraňujte** , protože jsou potřeba pro správné fungování clusteru s virtuální sítí.
>
> Po 28. února 2019 budou síťové prostředky (například síťové karty, libry atd.) pro nové clustery HDInsight vytvořené ve virtuální síti zřízené ve stejné skupině prostředků clusteru HDInsight. Dříve byly tyto prostředky zřízeny ve skupině prostředků VNET. Nedošlo k žádným změnám aktuálně spuštěných clusterů a clusterů vytvořených bez virtuální sítě.

## <a name="planning"></a>Plánování

V následující části najdete otázky, které je potřeba zodpovědět při plánování instalace HDInsight ve virtuální síti:

* Potřebujete nainstalovat HDInsight do existující virtuální sítě? Nebo vytváříte novou síť?

    Pokud používáte existující virtuální síť, možná budete muset před instalací HDInsight změnit konfiguraci sítě. Další informace najdete v části [Přidání HDInsight do existující virtuální sítě](#existingvnet) .

* Chcete připojit virtuální síť obsahující HDInsight k jiné virtuální síti nebo místní síti?

    Abyste mohli snadno pracovat s prostředky napříč sítěmi, možná budete muset vytvořit vlastní DNS a nakonfigurovat předávání DNS. Další informace najdete v části [připojení více sítí](#multinet) .

* Chcete omezit nebo přesměrovat příchozí nebo odchozí provoz do HDInsight?

    HDInsight musí mít neomezenou komunikaci s konkrétními IP adresami v datovém centru Azure. K dispozici je také několik portů, které musí být povoleny prostřednictvím brány firewall pro komunikaci s klienty. Další informace najdete v tématu [řízení síťového provozu](./control-network-traffic.md).

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>Přidání HDInsight do existující virtuální sítě

Pomocí kroků v této části zjistíte, jak přidat novou službu HDInsight do existující Virtual Network Azure.

> [!NOTE]  
> - Existující cluster HDInsight nemůžete přidat do virtuální sítě.
> - Virtuální síť a cluster, který se má vytvořit, musí být ve stejném předplatném.

1. Používáte pro virtuální síť model nasazení Classic nebo Správce prostředků?

    HDInsight 3,4 a vyšší vyžaduje Správce prostředků virtuální síť. Starší verze HDInsight vyžadovaly klasickou virtuální síť.

    Pokud je vaše stávající síť klasickou virtuální sítí, musíte vytvořit Správce prostředků virtuální síť a pak tyto dvě připojit. [Připojení klasického virtuální sítě k novému virtuální sítě](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Po připojení může HDInsight nainstalovaná v Správce prostředků síti pracovat s prostředky v klasické síti.

2. Používáte skupiny zabezpečení sítě, trasy definované uživatelem nebo zařízení Virtual Network pro omezení provozu do nebo z virtuální sítě?

    Jako spravovaná služba HDInsight vyžaduje neomezený přístup k několika IP adresám v datovém centru Azure. Pokud chcete komunikaci s těmito IP adresami dovolit, aktualizujte všechny existující skupiny zabezpečení sítě nebo trasy definované uživatelem.

    Služba HDInsight hostuje několik služeb, které používají různé porty. Neblokujte provoz na tyto porty. Seznam portů, které mají být povoleny prostřednictvím bran firewall pro virtuální zařízení, naleznete v části zabezpečení.

    Pokud chcete najít stávající konfiguraci zabezpečení, použijte následující Azure PowerShell nebo příkazy rozhraní příkazového řádku Azure CLI:

    * skupiny zabezpečení sítě,

        Nahraďte `RESOURCEGROUP` názvem skupiny prostředků, která obsahuje virtuální síť, a pak zadejte příkaz:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Další informace najdete v dokumentu [Poradce při potížích se skupinami zabezpečení sítě](../virtual-network/diagnose-network-traffic-filter-problem.md) .

        > [!IMPORTANT]  
        > Pravidla skupiny zabezpečení sítě se aplikují v pořadí podle priority pravidla. Použije se první pravidlo, které odpovídá vzoru provozu, a pro tento provoz se neuplatní žádné další. Seřazení pravidel z nejvyšší moci až po nejméně povolující. Další informace najdete v dokumentu [filtrování provozu sítě s použitím skupin zabezpečení sítě](../virtual-network/network-security-groups-overview.md) .

    * Trasy definované uživatelem

        Nahraďte `RESOURCEGROUP` názvem skupiny prostředků, která obsahuje virtuální síť, a pak zadejte příkaz:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Další informace najdete v dokumentu věnovaném [řešení potíží s trasami](../virtual-network/diagnose-network-routing-problem.md) .

3. Vytvořte cluster HDInsight a během konfigurace vyberte Azure Virtual Network. Pro pochopení procesu vytváření clusteru použijte postup v následujících dokumentech:

    * [Vytvoření HDInsight pomocí webu Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Vytvoření HDInsight pomocí Azure PowerShellu](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Vytvoření HDInsight pomocí klasického rozhraní příkazového řádku Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Vytvoření HDInsight pomocí šablony Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Přidání služby HDInsight do virtuální sítě je volitelný krok konfigurace. Nezapomeňte při konfiguraci clusteru vybrat virtuální síť.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Připojení více sítí

Největší výzvou s konfigurací více sítí je překlad názvů mezi sítěmi.

Azure poskytuje překlad adres IP pro služby Azure, které jsou nainstalované ve virtuální síti. Toto integrované překlad IP adres umožňuje službě HDInsight připojit se k následujícím prostředkům pomocí plně kvalifikovaného názvu domény (FQDN):

* Libovolný prostředek, který je k dispozici na internetu. Například microsoft.com, windowsupdate.com.

* Libovolný prostředek, který je ve stejném Virtual Network Azure, pomocí __interního názvu DNS__ daného prostředku. Například při použití výchozího překladu názvů jsou zde uvedeny příklady interních názvů DNS přiřazených k pracovním uzlům HDInsight:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Oba tyto uzly můžou komunikovat přímo mezi sebou a dalšími uzly v HDInsight pomocí interních názvů DNS.

Výchozí rozlišení názvů __neumožňuje službě__ HDInsight přeložit názvy prostředků v sítích, které jsou připojené k virtuální síti. Například je běžné připojení k místní síti k virtuální síti. Služba HDInsight nemůže získat přístup k prostředkům v místní síti pouze s výchozím překladem IP adres podle názvu. Opak je také true, prostředky v místní síti nemůžou přistupovat k prostředkům ve virtuální síti podle názvu.

> [!WARNING]  
> Před vytvořením clusteru HDInsight musíte vytvořit vlastní server DNS a nakonfigurovat virtuální síť tak, aby se používala.

Chcete-li povolit překlad názvů mezi virtuální sítí a prostředky v připojených sítích, je nutné provést následující akce:

1. Vytvořte si vlastní server DNS v Azure Virtual Network, kde plánujete nainstalovat HDInsight.

2. Nakonfigurujte virtuální síť tak, aby používala vlastní server DNS.

3. Najděte příponu DNS přiřazenou k Azure pro vaši virtuální síť. Tato hodnota je podobná `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` . Informace o vyhledání přípony DNS najdete v části [Příklad: vlastní DNS](hdinsight-create-virtual-network.md#example-dns) .

4. Nakonfigurujte přesměrování mezi servery DNS. Konfigurace závisí na typu vzdálené sítě.

   * Pokud je vzdálená síť místní sítí, nakonfigurujte DNS následujícím způsobem:

     * __Vlastní DNS__ (ve virtuální síti):

         * Dodejte požadavky na příponu DNS virtuální sítě do rekurzivního překladače Azure (168.63.129.16). Azure zpracovává požadavky na prostředky ve virtuální síti.

         * Předejte všechny ostatní požadavky na místní server DNS. Místní DNS zpracovává všechny další požadavky na překlad IP adres, dokonce i požadavky na internetové prostředky, jako je Microsoft.com.

     * __Místní DNS__: předejte požadavky na příponu DNS virtuální sítě na vlastní server DNS. Vlastní server DNS se pak přepošle do rekurzivního překladače Azure.

       Tato konfigurace směruje požadavky na plně kvalifikované názvy domén, které obsahují příponu DNS virtuální sítě na vlastní server DNS. Všechny ostatní požadavky (i u veřejných internetových adres) jsou zpracovávány místním serverem DNS.

   * Pokud je vzdálená síť jinou Virtual Network Azure, nakonfigurujte DNS následujícím způsobem:

     * __Vlastní DNS__ (v každé virtuální síti):

         * Požadavky na příponu DNS virtuálních sítí se předávají na vlastní servery DNS. DNS v každé virtuální síti zodpovídá za překlad prostředků v rámci své sítě.

         * Předejte všechny ostatní požadavky do rekurzivního překladače Azure. Rekurzivní překladač je zodpovědný za řešení místních a internetových prostředků.

       Server DNS pro každou síť předávají požadavky do druhé na základě přípony DNS. Další požadavky jsou vyřešeny pomocí rekurzivního překladače Azure.

     Příklad každé konfigurace najdete v části [Příklad: vlastní DNS](hdinsight-create-virtual-network.md#example-dns) .

Další informace najdete v dokumentu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) .

## <a name="directly-connect-to-apache-hadoop-services"></a>Přímé připojení k Apache Hadoop službám

Ke clusteru se můžete připojit na adrese `https://CLUSTERNAME.azurehdinsight.net` . Tato adresa používá veřejnou IP adresu, která může být dosažitelná, pokud jste použili skupin zabezpečení sítě k omezení příchozího provozu z Internetu. Navíc platí, že když nasadíte cluster ve virtuální síti, můžete k němu přistupovat pomocí privátního koncového bodu `https://CLUSTERNAME-int.azurehdinsight.net` . Tento koncový bod se překládá na privátní IP adresu uvnitř virtuální sítě pro přístup k clusteru.

Pokud se chcete připojit k Apache Ambari a dalším webovým stránkám prostřednictvím virtuální sítě, použijte následující postup:

1. Pokud chcete zjistit interní plně kvalifikované názvy domény (FQDN) uzlů clusteru HDInsight, použijte jednu z následujících metod:

    Nahraďte `RESOURCEGROUP` názvem skupiny prostředků, která obsahuje virtuální síť, a pak zadejte příkaz:

    ```powershell
    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName "RESOURCEGROUP" | where-object {$_.Name -like "*node*"}

    $nodes = @()
    foreach($nic in $clusterNICs) {
        $node = new-object System.Object
        $node | add-member -MemberType NoteProperty -name "Type" -value $nic.Name.Split('-')[1]
        $node | add-member -MemberType NoteProperty -name "InternalIP" -value $nic.IpConfigurations.PrivateIpAddress
        $node | add-member -MemberType NoteProperty -name "InternalFQDN" -value $nic.DnsSettings.InternalFqdn
        $nodes += $node
    }
    $nodes | sort-object Type
    ```

    ```azurecli
    az network nic list --resource-group RESOURCEGROUP --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

    V seznamu vrácených uzlů Najděte plně kvalifikovaný název domény pro hlavní uzly a pomocí plně kvalifikovaných názvů domény se připojte k Ambari a dalším webovým službám. Použijte například `http://<headnode-fqdn>:8080` pro přístup k Ambari.

    > [!IMPORTANT]  
    > Některé služby hostované v hlavních uzlech jsou aktivní jenom na jednom uzlu. Pokud se pokusíte o přístup ke službě na jednom hlavním uzlu a vrátí se chyba 404, přepněte na jiný hlavní uzel.

2. Chcete-li určit uzel a port, na kterém je služba k dispozici, přečtěte si [porty používané službou Hadoop v dokumentu HDInsight](./hdinsight-hadoop-port-settings-for-services.md) .

## <a name="load-balancing"></a>Vyrovnávání zatížení

Při vytváření clusteru HDInsight se vytvoří taky Nástroj pro vyrovnávání zatížení. Typ tohoto nástroje pro vyrovnávání zatížení je na [základní úrovni SKU](../load-balancer/skus.md), která má určitá omezení. Jedním z těchto omezení je, že pokud máte dvě virtuální sítě v různých oblastech, nemůžete se připojit k základním nástrojům pro vyrovnávání zatížení. Další informace najdete v tématu [Nejčastější dotazy k virtuálním sítím VNet: omezení globálního partnerského vztahu virtuálních sítí](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

## <a name="next-steps"></a>Další kroky

* Ukázky kódu a příklady vytváření virtuálních sítí Azure najdete v tématu [Vytvoření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md).
* Ucelený příklad konfigurace služby HDInsight pro připojení k místní síti najdete v tématu [připojení HDInsight k místní síti](./connect-on-premises-network.md).
* Další informace o virtuálních sítích Azure najdete v tématu [Přehled azure Virtual Network](../virtual-network/virtual-networks-overview.md).
* Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md).
* Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).
* Další informace o řízení provozu, včetně integrace brány firewall, najdete v tématu [řízení síťového provozu](./control-network-traffic.md).

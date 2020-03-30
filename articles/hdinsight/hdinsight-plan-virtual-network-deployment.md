---
title: Plánování virtuální sítě pro Azure HDInsight
description: Zjistěte, jak naplánovat nasazení virtuální sítě Azure pro připojení HDInsightu k jiným cloudovým prostředkům nebo prostředkům ve vašem datovém centru.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/25/2020
ms.openlocfilehash: 30664d533215cb49fa6f436ec4cf88fa319c3300
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272263"
---
# <a name="plan-a-virtual-network-for-azure-hdinsight"></a>Plánování virtuální sítě pro Azure HDInsight

Tento článek obsahuje základní informace o používání [virtuálních sítí (Virtuální sítě)](../virtual-network/virtual-networks-overview.md) s Azure HDInsight. Popisuje také rozhodnutí o návrhu a implementaci, která musí být provedena před implementací virtuální sítě pro váš cluster HDInsight. Po dokončení fáze plánování můžete přejít k [vytvoření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md). Další informace o IP adresách HDInsight pro správu, které jsou potřeba ke správné konfiguraci skupin zabezpečení sítě (NSG) a uživatelem definovaných tras, naleznete v [tématu IP adresy správy HDInsight](hdinsight-management-ip-addresses.md).

Použití virtuální sítě Azure umožňuje následující scénáře:

* Připojení k HDInsight přímo z místní sítě.
* Připojení HDInsightu k datovým úložištím ve virtuální síti Azure.
* Přímý přístup ke službám Apache Hadoop, které nejsou veřejně dostupné přes internet. Například Apache Kafka API nebo Apache HBase Java API.

> [!IMPORTANT]
> Vytvoření clusteru HDInsight ve virtuální síti vytvoří několik síťových prostředků, jako jsou síťové karty a nástroje pro vyrovnávání zatížení. **Neodstraňujte** tyto síťové prostředky, protože jsou potřebné pro váš cluster správně fungovat s virtuální sítí.
>
> února 2019 budou síťové prostředky (například síťové karty, lbs atd.) pro nové clustery HDInsight vytvořené ve virtuální síti zřízeny ve stejné skupině prostředků clusteru HDInsight. Dříve byly tyto prostředky zřízeny ve skupině prostředků virtuální sítě. Neexistuje žádná změna aktuální spuštěné clustery a tyto clustery vytvořené bez virtuální sítě.

## <a name="planning"></a>Plánování

Následující jsou otázky, které je třeba odpovědět při plánování instalace HDInsight ve virtuální síti:

* Potřebujete nainstalovat HDInsight do existující virtuální sítě? Nebo vytváříte novou síť?

    Pokud používáte existující virtuální síť, možná budete muset před instalací HDInsight upravili konfiguraci sítě. Další informace najdete v [tématu přidání HDInsight do existující virtuální sítě](#existingvnet) části.

* Chcete připojit virtuální síť obsahující HDInsight k jiné virtuální síti nebo k místní síti?

    Chcete-li snadno pracovat s prostředky napříč sítěmi, bude pravděpodobně nutné vytvořit vlastní dns a nakonfigurovat předávání DNS. Další informace naleznete v části [připojení více sítí.](#multinet)

* Chcete omezit/přesměrovat příchozí nebo odchozí provoz na HDInsight?

    HDInsight musí mít neomezenou komunikaci s konkrétními IP adresami v datovém centru Azure. Existuje také několik portů, které musí být povoleny prostřednictvím brány firewall pro komunikaci s klienty. Další informace naleznete v části [Řízení síťového provozu.](#networktraffic)

## <a name="add-hdinsight-to-an-existing-virtual-network"></a><a id="existingvnet"></a>Přidání HDInsightu do existující virtuální sítě

Postupem v této části zjistíte, jak přidat nový HDInsight do existující virtuální sítě Azure.

> [!NOTE]  
> Existující cluster HDInsight nelze přidat do virtuální sítě.

1. Používáte klasický model nasazení nebo resource manager pro virtuální síť?

    HDInsight 3.4 a vyšší vyžaduje virtuální síť Resource Manager. Dřívější verze HDInsight vyžadovaly klasickou virtuální síť.

    Pokud je vaše stávající síť klasická virtuální síť, musíte vytvořit virtuální síť Resource Manager a pak je připojit. [Připojení klasických virtuálních sítí k novým virtuálním sítím](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Po připojení může HDInsight nainstalovaný v síti Resource Manager pracovat s prostředky v klasické síti.

2. Používáte skupiny zabezpečení sítě, uživatelem definované trasy nebo virtuální síťová zařízení k omezení provozu do nebo z virtuální sítě?

    Jako spravovaná služba vyžaduje HDInsight neomezený přístup k několika IP adresám v datovém centru Azure. Chcete-li povolit komunikaci s těmito adresami IP, aktualizujte všechny existující skupiny zabezpečení sítě nebo uživatelem definované trasy.

    HDInsight hostuje několik služeb, které používají různé porty. Neblokujte provoz do těchto portů. Seznam portů, které chcete povolit prostřednictvím bran firewall virtuálních zařízení, naleznete v části Zabezpečení.

    Pokud chcete najít stávající konfiguraci zabezpečení, použijte následující příkazy Azure PowerShellu nebo Azure CLI:

    * Skupiny zabezpečení sítě

        Nahraďte `RESOURCEGROUP` název skupiny prostředků, která obsahuje virtuální síť, a zadejte příkaz:

        ```powershell
        Get-AzNetworkSecurityGroup -ResourceGroupName  "RESOURCEGROUP"
        ```

        ```azurecli
        az network nsg list --resource-group RESOURCEGROUP
        ```

        Další informace naleznete v dokumentu [Poradce při potížích se skupinami zabezpečení sítě.](../virtual-network/diagnose-network-traffic-filter-problem.md)

        > [!IMPORTANT]  
        > Pravidla skupiny zabezpečení sítě jsou použita v pořadí na základě priority pravidla. Použije se první pravidlo, které odpovídá vzoru provozu, a pro tento provoz nejsou použity žádné jiné. Řádová pravidla od nejtolerantnějších po nejméně tolerantní. Další informace naleznete v dokumentu [Filtrovat síťový provoz pomocí skupin zabezpečení sítě.](../virtual-network/security-overview.md)

    * Trasy definované uživatelem

        Nahraďte `RESOURCEGROUP` název skupiny prostředků, která obsahuje virtuální síť, a zadejte příkaz:

        ```powershell
        Get-AzRouteTable -ResourceGroupName "RESOURCEGROUP"
        ```

        ```azurecli
        az network route-table list --resource-group RESOURCEGROUP
        ```

        Další informace naleznete v dokumentu [Poradce při potížích s postupy.](../virtual-network/diagnose-network-routing-problem.md)

3. Vytvořte cluster HDInsight a během konfigurace vyberte virtuální síť Azure. Postup v následujících dokumentech slouží k pochopení procesu vytváření clusteru:

    * [Vytvoření HDInsight pomocí webu Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Vytvoření HDInsight pomocí Azure PowerShellu](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Vytvoření HDInsightu pomocí rozhraní příkazového příkazu Azure Classic](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Vytvoření HDInsightu pomocí šablony Azure Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

   > [!IMPORTANT]  
   > Přidání HDInsight u virtuální sítě je volitelný krok konfigurace. Při konfiguraci clusteru nezapomeňte vybrat virtuální síť.

## <a name="connecting-multiple-networks"></a><a id="multinet"></a>Připojení více sítí

Největší výzvou pro konfiguraci s více sítěmi je překlad názvů mezi sítěmi.

Azure poskytuje překlad názvů pro služby Azure, které jsou nainstalované ve virtuální síti. Toto předdefinované překlad názvů umožňuje službě HDInsight připojit se k následujícím prostředkům pomocí plně kvalifikovaného názvu domény (FQDN):

* Jakýkoli zdroj, který je k dispozici na internetu. Například microsoft.com, windowsupdate.com.

* Všechny prostředky, které jsou ve stejné virtuální síti Azure, pomocí __internínázev DNS__ prostředku. Například při použití výchozího překladu názvů jsou následující příklady interních názvů DNS přiřazených pracovním uzlům HDInsight:

  * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
  * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Oba tyto uzly mohou komunikovat přímo mezi sebou a další uzly v HDInsight pomocí interních názvů DNS.

Výchozí překlad názvů __neumožňuje__ HDInsight přeložit názvy prostředků v sítích, které jsou připojeny k virtuální síti. Například je běžné připojit místní síť k virtuální síti. S pouze výchozí překlad názvů HDInsight nemůže přistupovat k prostředkům v místní síti podle názvu. Opak je také pravda, prostředky v místní síti nemůže přistupovat k prostředkům ve virtuální síti podle názvu.

> [!WARNING]  
> Před vytvořením clusteru HDInsight je nutné vytvořit vlastní server DNS a nakonfigurovat virtuální síť tak, aby jej používala.

Chcete-li povolit překlad názvů mezi virtuální sítí a prostředky ve spojených sítích, je nutné provést následující akce:

1. Vytvořte vlastní DNS server ve virtuální síti Azure, kde plánujete nainstalovat HDInsight.

2. Nakonfigurujte virtuální síť tak, aby používala vlastní server DNS.

3. Najděte příponu DNS přiřazenou pro Azure pro vaši virtuální síť. Tato hodnota je `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`podobná . Informace o hledání přípony DNS naleznete v [části Příklad: Vlastní DNS.](hdinsight-create-virtual-network.md#example-dns)

4. Konfigurace předávání mezi servery DNS. Konfigurace závisí na typu vzdálené sítě.

   * Pokud je vzdálená síť místní, nakonfigurujte službu DNS následujícím způsobem:

     * __Vlastní DNS__ (ve virtuální síti):

         * Přepošlete požadavky na příponu DNS virtuální sítě rekurzivnímu překládání Azure (168.63.129.16). Azure zpracovává požadavky na prostředky ve virtuální síti

         * Přepošlete všechny ostatní požadavky na místní server DNS. Místní služba DNS zpracovává všechny ostatní požadavky na překlad názvů, dokonce i požadavky na internetové prostředky, jako je Microsoft.com.

     * __Místní DNS__: Přesměrovává požadavky na příponu DNS virtuální sítě na vlastní server DNS. Vlastní SERVER DNS pak předá rekurzivní překladač Azure.

       Tato konfigurace směruje požadavky na plně kvalifikované názvy domén, které obsahují příponu DNS virtuální sítě k vlastnímu serveru DNS. Všechny ostatní požadavky (i pro veřejné internetové adresy) zpracovává místní server DNS.

   * Pokud je vzdálená síť jiná virtuální síť Azure, nakonfigurujte DNS následujícím způsobem:

     * __Vlastní DNS__ (v každé virtuální síti):

         * Požadavky na příponu DNS virtuálních sítí jsou předávány na vlastní servery DNS. Služba DNS v každé virtuální síti je zodpovědná za řešení prostředků v rámci své sítě.

         * Přepošlete všechny ostatní požadavky na rekurzivní překladač Azure. Rekurzivní překladač je zodpovědný za řešení místních a internetových zdrojů.

       Server DNS pro každou síť předává požadavky druhé straně na základě přípony DNS. Ostatní požadavky jsou vyřešeny pomocí azure rekurzivní překládání.

     Příklad každé konfigurace najdete v [části Příklad: Vlastní DNS.](hdinsight-create-virtual-network.md#example-dns)

Další informace najdete v [dokumentu Překlad názvů pro virtuální uživatele a instance rolí.](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

## <a name="directly-connect-to-apache-hadoop-services"></a>Přímé připojení ke službám Apache Hadoop

Ke clusteru se `https://CLUSTERNAME.azurehdinsight.net`můžete připojit na adrese . Tato adresa používá veřejnou IP adresu, která nemusí být dostupná, pokud jste k omezení příchozího provozu z Internetu použili sítě zabezpečení sítě. Navíc při nasazení clusteru ve virtuální síti můžete přistupovat pomocí `https://CLUSTERNAME-int.azurehdinsight.net`privátní koncový bod . Tento koncový bod se překládá na privátní IP adresu uvnitř virtuální sítě pro přístup ke clusteru.

Chcete-li se připojit k Apache Ambari a dalším webovým stránkám prostřednictvím virtuální sítě, postupujte takto:

1. Chcete-li zjistit interní plně kvalifikované názvy domén (FQDN) uzlů clusteru HDInsight, použijte jednu z následujících metod:

    Nahraďte `RESOURCEGROUP` název skupiny prostředků, která obsahuje virtuální síť, a zadejte příkaz:

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

    V seznamu vrácených uzlů vyhledejte hlavní účelový server pro hlavní uzly a pomocí hlavních kvandpovaných sítí se připojte k Ambari a dalším webovým službám. Použijte `http://<headnode-fqdn>:8080` například pro přístup k Ambari.

    > [!IMPORTANT]  
    > Některé služby hostované na hlavní uzly jsou aktivní pouze na jednom uzlu najednou. Pokud se pokusíte získat přístup ke službě na jednom hlavním uzlu a vrátí chybu 404, přepněte do druhého hlavního uzlu.

2. Chcete-li zjistit uzel a port, na kterých je služba k dispozici, přečtěte si [informace o portech používaných službami Hadoop v dokumentu HDInsight.](./hdinsight-hadoop-port-settings-for-services.md)

## <a name="controlling-network-traffic"></a><a id="networktraffic"></a>Řízení síťového provozu

### <a name="techniques-for-controlling-inbound-and-outbound-traffic-to-hdinsight-clusters"></a>Techniky řízení příchozího a odchozího provozu do clusterů HDInsight

Síťový provoz ve virtuálních sítích Azure lze řídit pomocí následujících metod:

* **Skupiny zabezpečení sítě** (NSG) umožňují filtrovat příchozí a odchozí přenosy do sítě. Další informace naleznete v dokumentu [Filtrovat síťový provoz pomocí skupin zabezpečení sítě.](../virtual-network/security-overview.md)

* **Síťová virtuální zařízení** (NVA) lze používat pouze s odchozím provozem. Virtuální zařízení replikují funkce zařízení, jako jsou brány firewall a směrovače. Další informace naleznete v dokumentu [Síťová zařízení.](https://azure.microsoft.com/solutions/network-appliances)

Jako spravovaná služba vyžaduje HDInsight neomezený přístup ke službám stavu a správy HDInsight pro příchozí i odchozí provoz z virtuální sítě. Při používání skupin nsg, musíte zajistit, že tyto služby mohou stále komunikovat s clusterem HDInsight.

![Diagram entit HDInsight vytvořených ve vlastní virtuální netu Azure](./media/hdinsight-plan-virtual-network-deployment/hdinsight-vnet-diagram.png)

### <a name="hdinsight-with-network-security-groups"></a>HDInsight se skupinami zabezpečení sítě

Pokud plánujete používat **skupiny zabezpečení sítě** k řízení síťového provozu, proveďte před instalací hdinsightu následující akce:

1. Identifikujte oblast Azure, kterou chcete použít pro HDInsight.

2. Identifikujte značky služeb vyžadované službou HDInsight pro vaši oblast. Další informace najdete v [tématu značky služeb skupiny zabezpečení sítě (NSG) pro Azure HDInsight](hdinsight-service-tags.md).

3. Vytvořte nebo upravte skupiny zabezpečení sítě pro podsíť, do které chcete nainstalovat HDInsight.

    * __Skupiny zabezpečení sítě__: povolit __příchozí__ přenosy na portu __443__ z IP adres. Tím zajistíte, že služby správy HDInsight mohou dosáhnout clusteru mimo virtuální síť.

Další informace o skupinách zabezpečení sítě naleznete v [přehledu skupin zabezpečení sítě](../virtual-network/security-overview.md).

### <a name="controlling-outbound-traffic-from-hdinsight-clusters"></a>Řízení odchozího provozu z clusterů HDInsight

Další informace o řízení odchozího provozu z clusterů HDInsight najdete [v tématu Konfigurace omezení odchozího síťového provozu pro clustery Azure HDInsight](hdinsight-restrict-outbound-traffic.md).

#### <a name="forced-tunneling-to-on-premises"></a>Vynucené tunelování do místního prostředí

Vynucené tunelové propojení je uživatelem definovaná konfigurace směrování, ve které je veškerý provoz z podsítě vynucen do určité sítě nebo umístění, například do místní sítě. HDInsight __nepodporuje__ vynucené tunelování provozu do místních sítí.

## <a name="required-ip-addresses"></a><a id="hdinsight-ip"></a>Požadované IP adresy

Pokud k řízení provozu používáte skupiny zabezpečení sítě nebo uživatelem definované trasy, přečtěte si informace o [adresách IP pro správu HDInsight](hdinsight-management-ip-addresses.md).

## <a name="required-ports"></a><a id="hdinsight-ports"></a>Požadované porty

Pokud plánujete používat **bránu firewall** a přistupovat ke clusteru zvenčí na určitých portech, možná budete muset povolit provoz na těchto portech potřebných pro váš scénář. Ve výchozím nastavení není potřeba žádné speciální whitelisting portů tak dlouho, dokud azure management provoz vysvětlený v předchozí části je povoleno dosáhnout clusteru na portu 443.

Seznam portů pro konkrétní služby najdete v [tématu Porty používané službami Apache Hadoop v dokumentu HDInsight.](hdinsight-hadoop-port-settings-for-services.md)

Další informace o pravidlech brány firewall pro virtuální zařízení naleznete v dokumentu [scénáře virtuálních zařízení.](../virtual-network/virtual-network-scenario-udr-gw-nva.md)

## <a name="load-balancing"></a>Vyrovnávání zatížení

Když vytvoříte cluster HDInsight, vytvoří se také správce zatížení. Typ tohoto systému vyrovnávání zatížení je na [základní úrovni skladové položky](../load-balancer/concepts-limitations.md#skus), která má určitá omezení. Jedním z těchto omezení je, že pokud máte dvě virtuální sítě v různých oblastech, nelze se připojit k základním nástrojům pro vyrovnávání zatížení. Další informace najdete [v nejčastějších dotazech k virtuálním sítím: omezení globálního partnerského vztahu virtuálnísítě.](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)

## <a name="transport-layer-security"></a>Zabezpečení transportní vrstvy

Připojení ke clusteru prostřednictvím `https://<clustername>.azurehdinsight.net` koncového bodu veřejného clusteru jsou proxied prostřednictvím uzlů brány clusteru. Tato připojení jsou zabezpečena pomocí protokolu nazvaného TLS. Vynucení vyšších verzí protokolu TLS na branách zlepšuje zabezpečení těchto připojení. Další informace o tom, proč byste měli používat novější verze tls, naleznete [v tématu Řešení problému TLS 1.0](https://docs.microsoft.com/security/solving-tls1-problem).

Ve výchozím nastavení clustery Azure HDInsight přijímají připojení TLS 1.2 na veřejných koncových bodech HTTPS a také starší verze pro zpětnou kompatibilitu. Minimální verzi TLS podporovanou na uzlech brány můžete řídit během vytváření clusteru pomocí portálu Azure nebo šablony správce prostředků. Pro portál vyberte verzi TLS na kartě **Zabezpečení + sítě** během vytváření clusteru. Pro šablonu správce prostředků v době nasazení použijte vlastnost **minSupportedTlsVersion.** Ukázkovou šablonu najdete v [tématu HDInsight minimální šablona rychlého startu TLS 1.2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-minimum-tls). Tato vlastnost podporuje tři hodnoty: "1.0", "1.1" a "1.2", které odpovídají TLS 1.0+, TLS 1.1+ a TLS 1.2+ v uvedeném pořadí.

> [!IMPORTANT]
> června 2020 bude Azure HDInsight vynucovat verze TLS 1.2 nebo novější pro všechna připojení HTTPS. Doporučujeme zajistit, aby všichni vaši klienti byli připraveni ke zpracování verzí TLS 1.2 nebo novějších verzí. Další informace najdete v [tématu Azure HDInsight TLS 1.2 Enforcement](https://azure.microsoft.com/updates/azure-hdinsight-tls-12-enforcement/).

## <a name="next-steps"></a>Další kroky

* Ukázky kódu a příklady vytváření virtuálních sítí Azure najdete v tématu [Vytváření virtuálních sítí pro clustery Azure HDInsight](hdinsight-create-virtual-network.md).
* Příklad konfigurace HDInsightu pro připojení k místní síti najdete v tématu [Připojení HDInsightu k místní síti](./connect-on-premises-network.md).
* Informace o konfiguraci clusterů Apache HBase ve virtuálních sítích Azure najdete v tématu [Vytváření clusterů Apache HBase na HDInsightu ve virtuální síti Azure](hbase/apache-hbase-provision-vnet.md).
* Informace o konfiguraci geografické replikace Apache HBase najdete v tématu [Nastavení replikace clusteru Apache HBase ve virtuálních sítích Azure](hbase/apache-hbase-replication.md).
* Další informace o virtuálních sítích Azure najdete v přehledu [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md).
* Další informace o skupinách zabezpečení sítě naleznete v [tématu Skupiny zabezpečení sítě](../virtual-network/security-overview.md).
* Další informace o uživatelem definovaných trasách naleznete v [tématu Uživatelem definované trasy a předávání IP](../virtual-network/virtual-networks-udr-overview.md)adres .

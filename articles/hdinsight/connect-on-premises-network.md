---
title: Připojení Azure HDInsight k místní síti
description: Naučte se, jak vytvořit cluster HDInsight v Virtual Network Azure a pak ho připojit k místní síti. Naučte se konfigurovat překlad IP adres mezi HDInsight a vaší místní sítí pomocí vlastního serveru DNS.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: cd787e1c846bfe4728577cbbce069385ce064a10
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943406"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Připojení HDInsightu k místní síti

Naučte se připojit HDInsight k místní síti pomocí Azure Virtual Networks a brány VPN. Tento dokument poskytuje informace o plánování:

* Použití služby HDInsight v Virtual Network Azure, která se připojuje k vaší místní síti.
* Konfigurace překladu názvů DNS mezi virtuální sítí a místní sítí.
* Konfigurace skupin zabezpečení sítě k omezení přístupu k Internetu do HDInsight.
* Porty poskytované službou HDInsight ve virtuální síti.

## <a name="overview"></a>Přehled

Chcete-li službě HDInsight a prostředkům v připojené síti umožňovat komunikaci podle názvu, je nutné provést následující akce:

1. Vytvořte Virtual Network Azure.
1. Vytvořte si vlastní server DNS na Virtual Network Azure.
1. Nakonfigurujte virtuální síť tak, aby používala vlastní server DNS namísto výchozího překladače rekurzivního překladu Azure.
1. Nakonfigurujte přesměrování mezi vlastním serverem DNS a místním serverem DNS.

Tyto konfigurace umožňují následující chování:

* Požadavky na plně kvalifikované názvy domén, které mají příponu DNS __pro virtuální síť__ , se předají do vlastního serveru DNS. Vlastní server DNS pak tyto požadavky přepošle do rekurzivního překladače Azure, který vrací IP adresu.
* Všechny ostatní požadavky se předají na místní server DNS. I požadavky na veřejné internetové prostředky, jako je microsoft.com, se předávají na místní server DNS pro překlad IP adres.

V následujícím diagramu jsou zelenými řádky požadavky na prostředky, které končí příponou DNS virtuální sítě. Modré řádky jsou požadavky na prostředky v místní síti nebo na veřejném Internetu.

![Diagram postupu při řešení požadavků DNS v konfiguraci](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Požadavky

* Klient SSH. Další informace najdete v tématu [Připojení ke službě HDInsight (Apache Hadoop) pomocí SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Pokud používáte PowerShell, budete potřebovat [AZ Module](/powershell/azure/).
* Pokud chcete použít rozhraní příkazového řádku Azure a ještě jste ho nenainstalovali, přečtěte si téma [instalace Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Vytvořit konfiguraci virtuální sítě

Pomocí následujících dokumentů se naučíte, jak vytvořit Virtual Network Azure, která je připojená k vaší místní síti:

* [Pomocí webu Azure Portal](../vpn-gateway/tutorial-site-to-site-portal.md)
* [Použití Azure Powershell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Použití Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Vytvořit vlastní server DNS

> [!IMPORTANT]  
> Před instalací HDInsight do virtuální sítě je potřeba vytvořit a nakonfigurovat server DNS.

Tyto kroky používají [Azure Portal](https://portal.azure.com) k vytvoření virtuálního počítače Azure. Další způsoby vytvoření virtuálního počítače najdete v tématu [Vytvoření virtuálního počítače – Azure CLI](../virtual-machines/linux/quick-create-cli.md) a [Vytvoření virtuálního počítače – Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  K vytvoření virtuálního počítače se systémem Linux, který používá software DNS [BIND](https://www.isc.org/downloads/bind/) , použijte následující postup:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
  
1. V horní nabídce vyberte **+ vytvořit prostředek**.

    ![Vytvoření virtuálního počítače s Ubuntu](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. Vyberte **COMPUTE**  >  **virtuální počítač** a přejdete na stránku **vytvořit virtuální počítač** .

1. Na kartě __základy__ zadejte následující informace:  
  
    | Pole | Hodnota |
    | --- | --- |
    |Předplatné |Vyberte odpovídající předplatné.|
    |Skupina prostředků |Vyberte skupinu prostředků, která obsahuje dříve vytvořenou virtuální síť.|
    |Název virtuálního počítače | Zadejte popisný název, který identifikuje tento virtuální počítač. V tomto příkladu se používá **DNSProxy**.|
    |Region (Oblast) | Vyberte stejnou oblast jako dříve vytvořenou virtuální síť.  Ne všechny velikosti virtuálních počítačů jsou dostupné ve všech oblastech.  |
    |Možnosti dostupnosti |  Vyberte požadovanou úroveň dostupnosti.  Azure nabízí řadu možností pro správu dostupnosti a odolnosti pro vaše aplikace.  Architekt svého řešení pro použití replikovaných virtuálních počítačů v Zóny dostupnosti nebo skupin dostupnosti k ochraně vašich aplikací a dat před výpadky datacentra a událostmi údržby. V tomto příkladu se **nepožaduje žádná redundance infrastruktury**. |
    |Image | Ponechte na **Ubuntu serveru 18,04 LTS**. |
    |Typ ověřování | __Heslo__ nebo __veřejný klíč SSH__: metoda ověřování pro účet SSH. Doporučujeme používat veřejné klíče, protože jsou bezpečnější. V tomto příkladu se používá **heslo**.  Další informace najdete v dokumentu [Vytvoření a použití klíčů ssh pro virtuální počítače se systémem Linux](../virtual-machines/linux/mac-create-ssh-keys.md) .|
    |Uživatelské jméno |Zadejte uživatelské jméno správce pro virtuální počítač.  V tomto příkladu se používá **sshuser**.|
    |Heslo nebo veřejný klíč SSH | Dostupné pole je určeno podle vaší volby pro **typ ověřování**.  Zadejte odpovídající hodnotu.|
    |Veřejné příchozí porty|Vyberte možnost **Povolení vybraných portů**. Pak v rozevíracím seznamu **vybrat příchozí porty** vyberte **SSH (22)** .|

    ![Základní konfigurace virtuálního počítače](./media/connect-on-premises-network/virtual-machine-basics.png)

    Ponechte výchozí hodnoty na další položky a pak vyberte kartu **síť** .

4. Na kartě **sítě** zadejte následující informace:

    | Pole | Hodnota |
    | --- | --- |
    |Virtuální síť | Vyberte virtuální síť, kterou jste vytvořili dříve.|
    |Podsíť | Vyberte výchozí podsíť pro virtuální síť, kterou jste vytvořili dříve. Nevybírejte __podsíť__ , kterou používá brána sítě VPN.|
    |Veřejná IP adresa | Použijte automaticky vyplněnou hodnotu.  |

    ![Nastavení virtuální sítě HDInsight](./media/connect-on-premises-network/virtual-network-settings.png)

    Ponechte výchozí hodnoty na další položky a potom vyberte **zkontrolovat + vytvořit**.

5. Na kartě **Revize + vytvořit** vyberte **vytvořit** a vytvořte tak virtuální počítač.

### <a name="review-ip-addresses"></a>Kontrola IP adres

Po vytvoření virtuálního počítače se zobrazí oznámení o **úspěšném nasazení** s tlačítkem **Přejít na prostředek** .  Vyberte **Přejít k prostředku** a přejít na nový virtuální počítač.  Ve výchozím zobrazení nového virtuálního počítače pomocí těchto kroků Identifikujte přidružené IP adresy:

1. V **Nastavení** vyberte **vlastnosti**.

2. Poznamenejte si hodnoty pro **veřejnou IP adresu/název DNS popisek** a **privátní IP adresu** pro pozdější použití.

   ![Veřejné a privátní IP adresy](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalace a konfigurace služby BIND (software DNS)

1. Pomocí SSH se připojte k __veřejné IP adrese__ virtuálního počítače. Nahraďte `sshuser` uživatelským účtem SSH, který jste zadali při vytváření virtuálního počítače. Následující příklad se připojuje k virtuálnímu počítači na adrese 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. K instalaci vazby použijte následující příkazy z relace SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Chcete-li konfigurovat službu BIND pro přeposílání požadavků na překlad názvů na místní server DNS, použijte jako obsah souboru následující text `/etc/bind/named.conf.options` :

    ```DNS Zone file
    acl goodclients {
        10.0.0.0/16; # Replace with the IP address range of the virtual network
        10.1.0.0/16; # Replace with the IP address range of the on-premises network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            192.168.0.1; # Replace with the IP address of the on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```

    > [!IMPORTANT]  
    > Nahraďte hodnoty v `goodclients` oddílu rozsahem IP adres virtuální sítě a místní sítě. V této části se definují adresy, od kterých tento server DNS přijímá požadavky.
    >
    > Nahraďte `192.168.0.1` položku v `forwarders` části IP adresou místního serveru DNS. Tato položka směruje požadavky DNS na váš místní server DNS na rozlišení.

    Chcete-li tento soubor upravit, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Pokud chcete soubor uložit, použijte __CTRL + X__, __Y__ a pak __Zadejte__.

4. Z relace SSH použijte následující příkaz:

    ```bash
    hostname -f
    ```

    Tento příkaz vrátí hodnotu podobnou následujícímu textu:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net`Text je __přípona DNS__ pro tuto virtuální síť. Tuto hodnotu uložte, protože se používá později.

5. Pokud chcete nakonfigurovat službu BIND k překladu názvů DNS pro prostředky v rámci virtuální sítě, jako obsah souboru použijte následující text `/etc/bind/named.conf.local` :

    ```DNS Zone file
    // Replace the following with the DNS suffix for your virtual network
    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # The Azure recursive resolver
    };
    ```

    > [!IMPORTANT]  
    > Je nutné nahradit `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` příponu DNS, kterou jste získali dříve.

    Chcete-li tento soubor upravit, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Pokud chcete soubor uložit, použijte __CTRL + X__, __Y__ a pak __Zadejte__.

6. K zahájení vazby použijte následující příkaz:

    ```bash
    sudo service bind9 restart
    ```

7. Chcete-li ověřit, zda může vazba přeložit názvy prostředků v místní síti, použijte následující příkazy:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Nahraďte `dns.mynetwork.net` plně kvalifikovaným názvem domény (FQDN) prostředku ve vaší místní síti.
    >
    > Nahraďte `10.0.0.4` __interní IP adresou__ vašeho vlastního serveru DNS ve virtuální síti.

    Odpověď se zobrazí jako následující text:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Nakonfigurujte virtuální síť tak, aby používala vlastní server DNS.

Pokud chcete virtuální síť nakonfigurovat tak, aby místo rekurzivního překladače Azure používala vlastní server DNS, použijte následující postup [Azure Portal](https://portal.azure.com):

1. V nabídce vlevo přejděte na **všechny služby**  >  **sítě**  >  **virtuální sítě**.

2. Vyberte ze seznamu svou virtuální síť, čímž otevřete výchozí zobrazení vaší virtuální sítě.  

3. Ve výchozím zobrazení v části **Nastavení** vyberte **servery DNS**.  

4. Vyberte __vlastní__ a zadejte **privátní IP adresu** vlastního serveru DNS.

5. Vyberte __Uložit__.  <br />  

    ![Nastavení vlastního serveru DNS pro síť](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Konfigurace místního serveru DNS

V předchozí části jste nakonfigurovali vlastní server DNS pro přeposílání požadavků na místní server DNS. V dalším kroku musíte nakonfigurovat místní server DNS tak, aby předal požadavky na vlastní server DNS.

Konkrétní postup konfigurace serveru DNS najdete v dokumentaci k vašemu softwaru DNS Server. Vyhledejte kroky, jak nakonfigurovat __podmíněný předávací server__.

Podmíněný posun pouze přepošle požadavky na konkrétní příponu DNS. V takovém případě musíte nakonfigurovat službu pro směrování pro příponu DNS virtuální sítě. Žádosti o tuto příponu by se měly přesměrovat na IP adresu vlastního serveru DNS. 

Následující text představuje příklad konfigurace podmíněného dopředné pro software DNS **BIND** :

```DNS Zone file
zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
    type forward;
    forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
};
```

Informace o použití DNS v **systému Windows Server 2016** najdete v dokumentaci k [Přidání-DnsServerConditionalForwarderZone](/powershell/module/dnsserver/add-dnsserverconditionalforwarderzone) ...

Po nakonfigurování místního serveru DNS můžete použít `nslookup` z místní sítě a ověřit tak, že můžete přeložit názvy ve virtuální síti. Následující příklad 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

V tomto příkladu se k překladu názvu vlastního serveru DNS používá místní server DNS na adrese 196.168.0.4. Nahraďte IP adresu druhou pro místní server DNS. Adresu nahraďte `dnsproxy` plně kvalifikovaným názvem domény vlastního serveru DNS.

## <a name="optional-control-network-traffic"></a>Volitelné: řízení síťového provozu

K řízení síťového provozu můžete použít skupiny zabezpečení sítě (NSG) nebo uživatelsky definované trasy (UDR). Skupin zabezpečení sítě umožňují filtrovat příchozí a odchozí provoz a povolit nebo odepřít provoz. Udr vám umožní řídit tok přenosů mezi prostředky ve virtuální síti, internetem a místní sítí.

> [!WARNING]  
> HDInsight vyžaduje příchozí přístup z konkrétních IP adres v cloudu Azure a neomezený odchozí přístup. Při použití skupin zabezpečení sítě nebo udr k řízení provozu je nutné provést následující kroky:

1. Vyhledejte IP adresy pro umístění, které obsahuje vaši virtuální síť. Seznam požadovaných IP adres podle umístění najdete v části [požadované IP adresy](./hdinsight-management-ip-addresses.md).

2. Pro IP adresy identifikované v kroku 1 povolte příchozí provoz z těchto IP adres.

   * Pokud používáte __NSG__: Povolit __příchozí__ provoz na portu __443__ pro IP adresy.
   * Pokud používáte __udr__: nastavte typ dalšího segmentu __Směrování__ trasy na __Internet__ pro IP adresy.

Příklad použití Azure PowerShell nebo rozhraní příkazového řádku Azure ke tvorbě skupin zabezpečení sítě najdete v dokumentu věnovaném [Rozšířené službě HDInsight s Azure Virtual Networks](hdinsight-create-virtual-network.md#hdinsight-nsg) .

## <a name="create-the-hdinsight-cluster"></a>Vytvoření clusteru HDInsight

> [!WARNING]  
> Než začnete službu HDInsight ve virtuální síti instalovat, musíte nakonfigurovat vlastní server DNS.

Pomocí postupu v části [Vytvoření clusteru HDInsight pomocí Azure Portalho](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu vytvořte cluster HDInsight.

> [!WARNING]  
> * Během vytváření clusteru musíte zvolit umístění, které obsahuje vaši virtuální síť.
> * V části __Upřesnit nastavení__ v části konfigurace musíte vybrat virtuální síť a podsíť, které jste vytvořili dříve.

## <a name="connecting-to-hdinsight"></a>Připojování k HDInsight

Většina dokumentace k HDInsight předpokládá, že máte přístup ke clusteru přes Internet. To znamená, že se ke clusteru můžete připojit třeba na `https://CLUSTERNAME.azurehdinsight.net`. Tato adresa používá veřejnou bránu, která není dostupná, pokud jste k omezení přístupu z Internetu použili skupin zabezpečení sítě nebo udr.

Některá dokumentace také odkazuje `headnodehost` při připojování ke clusteru z relace SSH. Tato adresa je dostupná jenom z uzlů v rámci clusteru a nedá se použít na klientech připojených přes virtuální síť.

Pokud se chcete ke službě HDInsight připojit přímo přes virtuální síť, použijte následující postup:

1. K vyhledání interních plně kvalifikovaných názvů domén uzlů clusteru HDInsight použijte jednu z následujících metod:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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
    az network nic list --resource-group <resourcegroupname> --output table --query "[?contains(name,'node')].{NICname:name,InternalIP:ipConfigurations[0].privateIpAddress,InternalFQDN:dnsSettings.internalFqdn}"
    ```

2. Pokud chcete zjistit port, na kterém je služba dostupná, přečtěte si [porty používané službou Apache Hadoop Services na dokumentu HDInsight](./hdinsight-hadoop-port-settings-for-services.md) .

    > [!IMPORTANT]  
    > Některé služby hostované v hlavních uzlech jsou aktivní jenom na jednom uzlu. Pokud se pokusíte o přístup ke službě na jednom hlavním uzlu a tato operace se nezdařila, přepněte na jiný hlavní uzel.
    >
    > Například Apache Ambari je aktivní jenom v jednom hlavní uzlu. Pokud se pokusíte získat přístup k Ambari na jednom hlavním uzlu a vrátí 404 chybu, bude spuštěna na druhém hlavním uzlu.

## <a name="next-steps"></a>Další kroky

* Další informace o používání služby HDInsight ve virtuální síti najdete v tématu [Plánování nasazení virtuální sítě pro clustery Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

* Další informace o virtuálních sítích Azure najdete v tématu [Přehled azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení sítě](../virtual-network/network-security-groups-overview.md).

* Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).
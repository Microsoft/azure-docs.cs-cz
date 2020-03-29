---
title: Připojení Azure HDInsight k místní síti
description: Zjistěte, jak vytvořit cluster HDInsight ve virtuální síti Azure a pak ho připojit k místní síti. Přečtěte si, jak nakonfigurovat překlad názvů mezi HDInsightem a místní sítí pomocí vlastního serveru DNS.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/04/2020
ms.openlocfilehash: 2ed7a5b9c81d1b50f80f379a88688b69c49ed382
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78897920"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Připojení HDInsightu k místní síti

Zjistěte, jak připojit HDInsight k místní síti pomocí virtuálních sítí Azure a brány VPN. Tento dokument obsahuje informace o plánování:

* Použití HDInsightu ve virtuální síti Azure, která se připojuje k vaší místní síti.
* Konfigurace překladu názvů DNS mezi virtuální sítí a místní sítí.
* Konfigurace skupin zabezpečení sítě pro omezení přístupu k internetu na HDInsight.
* Porty poskytované HDInsight ve virtuální síti.

## <a name="overview"></a>Přehled

Chcete-li povolit komunikaci HDInsight a prostředků ve spojené síti podle názvu, je nutné provést následující akce:

1. Vytvořte virtuální síť Azure.
1. Vytvořte vlastní DNS server ve virtuální síti Azure.
1. Nakonfigurujte virtuální síť tak, aby místo výchozího rekurzivního překládání Azure používala vlastní server DNS.
1. Nakonfigurujte předávání mezi vlastním serverem DNS a místním serverem DNS.

Tyto konfigurace umožňují následující chování:

* Požadavky na plně kvalifikované názvy domén, které mají příponu DNS __pro virtuální síť,__ jsou předávány na vlastní server DNS. Vlastní server DNS pak předá tyto požadavky Azure Rekurzivní resolver, který vrátí IP adresu.
* Všechny ostatní požadavky jsou předávány místnímu serveru DNS. Dokonce i požadavky na veřejné internetové prostředky, jako jsou microsoft.com jsou předávány místnímu serveru DNS pro překlad názvů.

V následujícím diagramu jsou zelené čáry požadavky na prostředky, které končí v příponě DNS virtuální sítě. Modré čáry jsou požadavky na prostředky v místní síti nebo na veřejném internetu.

![Diagram řešení požadavků DNS v konfiguraci](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

## <a name="prerequisites"></a>Požadavky

* Klient SSH. Další informace naleznete [v tématu Připojení k HDInsight (Apache Hadoop) pomocí SSH](./hdinsight-hadoop-linux-use-ssh-unix.md).
* Pokud používáte PowerShell, budete potřebovat [modul AZ](https://docs.microsoft.com/powershell/azure/overview).
* Pokud chcete používat Azure CLI a ještě jste ho [nenainstalovali, přečtěte si informace o instalaci příkazového příkazového příkazu k Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-virtual-network-configuration"></a>Vytvoření konfigurace virtuální sítě

Pomocí následujících dokumentů se dozvíte, jak vytvořit virtuální síť Azure připojenou k vaší místní síti:

* [Používání portálu Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Použití Azure Powershell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Použití Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="create-custom-dns-server"></a>Vytvoření vlastního serveru DNS

> [!IMPORTANT]  
> Před instalací služby HDInsight do virtuální sítě je nutné vytvořit a nakonfigurovat server DNS.

Tyto kroky používají [portál Azure](https://portal.azure.com) k vytvoření virtuálního počítače Azure. Další způsoby vytvoření virtuálního počítače najdete v tématu [Vytvoření virtuálního počítače – Azure CLI](../virtual-machines/linux/quick-create-cli.md) a [Vytvoření virtuálního počítače – Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md).  Chcete-li vytvořit virtuální počítač s Linuxem, který používá software [Bind](https://www.isc.org/downloads/bind/) DNS, použijte následující kroky:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
  
1. V horní nabídce vyberte **+ Vytvořit prostředek**.

    ![Vytvoření virtuálního počítače Ubuntu](./media/connect-on-premises-network/azure-portal-create-resource.png)

1. Vyberte **Výpočetní** > **virtuální počítač,** chcete-li přejít na **stránku Vytvořit virtuální počítač.**

1. Na kartě __Základy__ zadejte následující informace:  
  
    | Pole | Hodnota |
    | --- | --- |
    |Předplatné |Vyberte příslušné předplatné.|
    |Skupina prostředků |Vyberte skupinu prostředků, která obsahuje dříve vytvořenou virtuální síť.|
    |Název virtuálního počítače | Zadejte popisný název, který identifikuje tento virtuální počítač. Tento příklad používá **DNSProxy**.|
    |Region (Oblast) | Vyberte stejnou oblast jako virtuální síť vytvořená dříve.  Ne všechny velikosti virtuálních počítače jsou k dispozici ve všech oblastech.  |
    |Možnosti dostupnosti |  Vyberte požadovanou úroveň dostupnosti.  Azure nabízí celou řadu možností pro správu dostupnosti a odolnosti pro vaše aplikace.  Navršte své řešení tak, aby používalo replikované virtuální počítače v zónách dostupnosti nebo v sadách dostupnosti k ochraně aplikací a dat před výpadky dat a událostmi údržby. Tento příklad používá **není vyžadována žádná redundance infrastruktury**. |
    |Image | Nechte na **Ubuntu Server 18.04 LTS**. |
    |Typ ověřování | __Heslo__ nebo __veřejný klíč SSH__: Metoda ověřování pro účet SSH. Doporučujeme používat veřejné klíče, protože jsou bezpečnější. Tento příklad používá **heslo**.  Další informace najdete v [tématu Vytvoření a použití klíčů SSH pro linuxové virtuální počítače](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentu.|
    |Uživatelské jméno |Zadejte uživatelské jméno správce pro virtuální ho.  Tento příklad používá **sshuser**.|
    |Heslo nebo veřejný klíč SSH | Pole dostupné je určeno volbou typu **Ověřování**.  Zadejte příslušnou hodnotu.|
    |Veřejné příchozí porty|Vyberte **Povolit vybrané porty**. Pak vyberte **SSH (22)** z rozevíracího seznamu **Select inbound ports.**|

    ![Základní konfigurace virtuálního počítače](./media/connect-on-premises-network/virtual-machine-basics.png)

    Ostatní položky ponechejte na výchozích hodnotách a pak vyberte kartu **Síť.**

4. Na kartě **Síť** zadejte následující informace:

    | Pole | Hodnota |
    | --- | --- |
    |Virtuální síť | Vyberte virtuální síť, kterou jste vytvořili dříve.|
    |Podsíť | Vyberte výchozí podsíť pro virtuální síť, kterou jste vytvořili dříve. __Nevybírejte__ podsíť používanou bránou VPN.|
    |Veřejná IP adresa | Použijte automaticky vyplněnou hodnotu.  |

    ![Nastavení virtuální sítě HDInsight](./media/connect-on-premises-network/virtual-network-settings.png)

    Ostatní položky ponechejte na výchozích hodnotách a pak vyberte **možnost Revize + vytvořit**.

5. Na kartě **Revize + vytvoření** vyberte **Vytvořit** a vytvořte virtuální počítač.

### <a name="review-ip-addresses"></a>Kontrola IP adres

Po vytvoření virtuálního počítače obdržíte oznámení **o úspěšném nasazení** pomocí tlačítka **Přejít na prostředek.**  Vyberte **Přejít na prostředek** a přejděte do nového virtuálního počítače.  Ve výchozím zobrazení nového virtuálního počítače můžete identifikovat přidružené adresy IP následujícím způsobem:

1. V **části Nastavení**vyberte **Vlastnosti**.

2. Poznamenejte si hodnoty pro **veřejné IP adresy/DNS name label** a soukromé IP **adresy** pro pozdější použití.

   ![Veřejné a soukromé IP adresy](./media/connect-on-premises-network/virtual-machine-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalace a konfigurace vazby (software DNS)

1. Pomocí SSH se připojte k __veřejné IP adrese__ virtuálního počítače. Nahraďte `sshuser` uživatelskýúčet SSH, který jste zadali při vytváření virtuálního účtu. Následující příklad se připojí k virtuálnímu počítači na 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

2. Chcete-li nainstalovat vazbu, použijte následující příkazy z relace SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Chcete-li nakonfigurovat vazbu pro předávání požadavků na překlad názvů místnímu `/etc/bind/named.conf.options` serveru DNS, použijte jako obsah souboru následující text:

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

    > [!IMPORTANT]  
    > Nahraďte hodnoty `goodclients` v části rozsahem IP adres virtuální sítě a místní sítě. Tato část definuje adresy, od kterých tento server DNS přijímá požadavky.
    >
    > Nahraďte položku `192.168.0.1` `forwarders` v části IP adresou místního serveru DNS. Tato položka směruje požadavky DNS na místní server DNS pro řešení.

    Chcete-li tento soubor upravit, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Chcete-li soubor uložit, použijte __kombinaci kláves Ctrl+X__, __Y__a potom __klávesu Enter__.

4. V relaci SSH použijte následující příkaz:

    ```bash
    hostname -f
    ```

    Tento příkaz vrátí hodnotu podobnou následujícímu textu:

    ```output
    dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net
    ```

    Text `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` je __přípona DNS__ pro tuto virtuální síť. Uložte tuto hodnotu, jak se používá později.

5. Chcete-li nakonfigurovat vazbu pro překlad názvů DNS pro prostředky ve `/etc/bind/named.conf.local` virtuální síti, použijte jako obsah souboru následující text:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]  
    > Musíte nahradit `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` příponu DNS, kterou jste načetli dříve.

    Chcete-li tento soubor upravit, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Chcete-li soubor uložit, použijte __kombinaci kláves Ctrl+X__, __Y__a potom __klávesu Enter__.

6. Chcete-li spustit vazbu, použijte následující příkaz:

    ```bash
    sudo service bind9 restart
    ```

7. Chcete-li ověřit, že vazba může přeložit názvy prostředků v místní síti, použijte následující příkazy:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]  
    > Nahraďte `dns.mynetwork.net` plně kvalifikovanýnázev domény (Plně kvalifikovaný název domény) prostředku v místní síti.
    >
    > Nahraďte `10.0.0.4` __ji interní IP adresou__ vlastního serveru DNS ve virtuální síti.

    Odpověď se podobá následujícímu textu:

    ```output
    Server:         10.0.0.4
    Address:        10.0.0.4#53

    Non-authoritative answer:
    Name:   dns.mynetwork.net
    Address: 192.168.0.4
    ```

## <a name="configure-virtual-network-to-use-the-custom-dns-server"></a>Konfigurace virtuální sítě pro použití vlastního serveru DNS

Chcete-li nakonfigurovat virtuální síť tak, aby používala vlastní server DNS namísto rekurzivního překládání Azure, použijte následující kroky z [webu Azure Portal](https://portal.azure.com):

1. V levém menu přejděte na **všechny služby** > **sítě** > **Virtuální sítě**.

2. Vyberte virtuální síť ze seznamu, který otevře výchozí zobrazení pro vaši virtuální síť.  

3. Ve výchozím zobrazení vyberte v části **Nastavení** **servery DNS**.  

4. Vyberte __Vlastní__a zadejte **soukromou ADRESU IP** vlastního serveru DNS.

5. Vyberte __Uložit__.  <br />  

    ![Nastavení vlastního serveru DNS pro síť](./media/connect-on-premises-network/configure-custom-dns.png)

## <a name="configure-on-premises-dns-server"></a>Konfigurace místního serveru DNS

V předchozí části jste nakonfigurovali vlastní server DNS tak, aby přesměrovává požadavky na místní server DNS. Dále je nutné nakonfigurovat místní server DNS tak, aby přesměrovává požadavky na vlastní server DNS.

Konkrétní kroky týkající se konfigurace serveru DNS naleznete v dokumentaci k softwaru serveru DNS. Vyhledejte postup konfigurace __podmíněného serveru pro předávání__.

Podmíněný přeposílá pouze požadavky na určitou příponu DNS. V takovém případě je nutné nakonfigurovat server pro předávání pro příponu DNS virtuální sítě. Požadavky na tuto příponu by měly být předány na adresu IP vlastního serveru DNS. 

Následující text je příkladem podmíněné konfigurace serveru pro předávání softwaru **Bind** DNS:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Informace o použití služby DNS v **systému Windows Server 2016**naleznete v dokumentaci [k potřebě Add-DnsServerConditionalForwarderZone...](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone)

Po konfiguraci místního serveru DNS můžete použít `nslookup` místní síť k ověření, že můžete přeložit názvy ve virtuální síti. Následující příklad 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Tento příklad používá místní server DNS na adrese 196.168.0.4 k překladu názvu vlastního serveru DNS. Nahraďte adresu IP adresou místního serveru DNS. Nahraďte `dnsproxy` adresu plně kvalifikovaným názvem domény vlastního serveru DNS.

## <a name="optional-control-network-traffic"></a>Volitelné: Řízení síťového provozu

K řízení síťového provozu můžete použít skupiny zabezpečení sítě (NSG) nebo uživatelem definované trasy (UDR). Soubory sítě nsg umožňují filtrovat příchozí a odchozí přenosy a povolit nebo odepřít provoz. UDR umožňují řídit, jak toky provozu mezi prostředky ve virtuální síti, internetu a místní síti.

> [!WARNING]  
> HDInsight vyžaduje příchozí přístup z konkrétních IP adres v cloudu Azure a neomezený odchozí přístup. Při řízení provozu pomocí nsg nebo udr síly nebo udr.

1. Vyhledejte IP adresy pro umístění, které obsahuje vaši virtuální síť. Seznam požadovaných IP adres podle umístění naleznete v tématu [Povinné adresy IP](./hdinsight-management-ip-addresses.md).

2. Pro adresy IP uvedené v kroku 1 povolte příchozí přenosy z této adresy IP.

   * Pokud používáte __službu NSG__: Povolit __příchozí__ provoz na portu __443__ pro IP adresy.
   * Pokud používáte __UDR__: Nastavte pro IP adresy typ __další směrování__ trasy k __Internetu.__

Příklad použití Azure PowerShellu nebo rozhraní příkazového příkazu k azure k vytvoření sítí zabezpečení sítě najdete v dokumentu [Rozšíření HDInsightu pomocí virtuálních sítí Azure.](hdinsight-create-virtual-network.md#hdinsight-nsg)

## <a name="create-the-hdinsight-cluster"></a>Vytvoření clusteru HDInsight

> [!WARNING]  
> Před instalací služby HDInsight do virtuální sítě je nutné nakonfigurovat vlastní server DNS.

Pomocí kroků v [clusteru Vytvoření HDInsight pomocí](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu portálu Azure vytvořte cluster HDInsight.

> [!WARNING]  
> * Během vytváření clusteru je nutné zvolit umístění, které obsahuje vaši virtuální síť.
> * V části __Upřesnit nastavení__ konfigurace je nutné vybrat virtuální síť a podsíť, které jste vytvořili dříve.

## <a name="connecting-to-hdinsight"></a>Připojení k HDInsight

Většina dokumentace na HDInsight předpokládá, že máte přístup ke clusteru přes internet. To znamená, že se ke clusteru můžete připojit třeba na `https://CLUSTERNAME.azurehdinsight.net`. Tato adresa používá veřejnou bránu, která není k dispozici, pokud jste k omezení přístupu z Internetu použili soubory zabezpečení sítě nebo udr.

Některé dokumenty také `headnodehost` odkazuje při připojování ke clusteru z relace SSH. Tato adresa je k dispozici pouze z uzlů v rámci clusteru a není použitelná u klientů připojených přes virtuální síť.

Chcete-li se přímo připojit k HDInsight prostřednictvím virtuální sítě, použijte následující kroky:

1. Chcete-li zjistit interní plně kvalifikované názvy domén uzlů clusteru HDInsight, použijte jednu z následujících metod:

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

2. Chcete-li zjistit port, na který je služba k dispozici, podívejte se na [porty používané službami Apache Hadoop v dokumentu HDInsight.](./hdinsight-hadoop-port-settings-for-services.md)

    > [!IMPORTANT]  
    > Některé služby hostované na hlavní uzly jsou aktivní pouze na jednom uzlu najednou. Pokud se pokusíte získat přístup ke službě na jednom hlavním uzlu a selže, přepněte na druhý hlavní uzel.
    >
    > Například Apache Ambari je aktivní pouze na jednom hlavním uzlu najednou. Pokud se pokusíte získat přístup k Ambari na jednom hlavním uzlu a vrátí chybu 404, pak je spuštěn a druhý hlavní uzel.

## <a name="next-steps"></a>Další kroky

* Další informace o používání HDInsightu ve virtuální síti najdete v [tématu Plánování nasazení virtuální sítě pro clustery Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

* Další informace o virtuálních sítích Azure najdete v přehledu [virtuální sítě Azure](../virtual-network/virtual-networks-overview.md).

* Další informace o skupinách zabezpečení sítě naleznete v [tématu Skupiny zabezpečení sítě](../virtual-network/security-overview.md).

* Další informace o uživatelem definovaných trasách naleznete v [tématu Uživatelem definované trasy a předávání IP](../virtual-network/virtual-networks-udr-overview.md)adres .

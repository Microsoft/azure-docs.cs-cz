---
title: HDInsight se připojit k vaší místní sítě – Azure HDInsight
description: Zjistěte, jak vytvořit HDInsight cluster ve službě Azure Virtual Network a pak ho připojíte k místní síti. Zjistěte, jak nakonfigurovat překlad mezi HDInsight a v místní síti pomocí vlastního serveru DNS.
author: jasonwhowell
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: jasonh
ms.openlocfilehash: c7186298c95667f1f29febca8c85d6799710deab
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39597503"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Připojení HDInsightu k místní síti

Zjistěte, jak připojit HDInsight k místní síti pomocí virtuální sítě Azure a bránu VPN. Tento dokument obsahuje informace o plánování na:

* Pomocí HDInsight ve virtuální síti Azure, která se připojuje k vaší místní síti.

* Konfigurace překladu názvů DNS mezi virtuální sítí a místní sítí.

* Konfigurace skupin zabezpečení sítě pro omezení přístupu k Internetu k HDInsight.

* Porty, které poskytuje HDInsight ve virtuální síti.

## <a name="create-the-virtual-network-configuration"></a>Vytvoření konfigurace virtuální sítě

Zjistěte, jak vytvořit virtuální síť Azure, který je připojený k vaší místní síti pomocí následujících dokumentech:
    
* [Pomocí webu Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Použití Azure PowerShellu](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Použití Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>Nakonfigurovat překlad názvů

Povolit HDInsight a prostředky v připojeném k síti komunikovat podle názvu, je třeba provést následující akce:

* Vytvoření vlastního serveru DNS ve virtuální síti Azure.

* Konfigurace virtuální sítě pro použití vlastního serveru DNS. místo výchozího rekurzivní překladače Azure.

* Konfigurace předávání mezi vlastního serveru DNS a místním serverem DNS.

Tato konfigurace umožňuje toto chování:

* Požadavky pro plně kvalifikované názvy domén, které mají příponu DNS __pro virtuální síť__ se předávají do vlastního serveru DNS. Tyto požadavky vlastního serveru DNS. potom předá do rekurzivní překladače Azure, které vrací IP adresu.

* Všechny ostatní žádosti jsou předávány na místním serveru DNS. Dokonce i požadavky na veřejné internetové prostředky, třeba microsoft.com jsou předány na místním serveru DNS pro překlad názvů.

V následujícím diagramu zelenou řádky jsou požadavky na prostředky, které končí příponou DNS ve virtuální síti. Modré čáry jsou požadavky na prostředky v místní síti nebo na veřejný internet.

![Diagram způsob řešení žádosti DNS v konfiguraci v tomto dokumentu](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>Vytvoření vlastního serveru DNS

> [!IMPORTANT]
> Musíte vytvořit a konfigurovat DNS server před instalací HDInsight do virtuální sítě.

Vytvoření virtuálního počítače s Linuxem, který používá [svázat](https://www.isc.org/downloads/bind/) DNS software, použijte následující postup:

> [!NOTE]
> Následující kroky použijte [webu Azure portal](https://portal.azure.com) k vytvoření virtuálního počítače Azure. Další možnosti pro vytvoření virtuálního počítače, naleznete v následujících dokumentech:
>
> * [Vytvoření virtuálního počítače – rozhraní příkazového řádku Azure](../virtual-machines/linux/quick-create-cli.md)
> * [Vytvoření virtuálního počítače – Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. Z [webu Azure portal](https://portal.azure.com)vyberte __+__, __Compute__, a __Ubuntu Server 16.04 LTS__.

    ![Vytvoření virtuálního počítače s Ubuntu](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. V části __Základy__ zadejte následující informace:

    * __Název__: popisný název, který identifikuje tento virtuální počítač. Například __DNSProxy__.
    * __Uživatelské jméno__: název účtu SSH.
    * __Veřejný klíč SSH__ nebo __heslo__: metodu ověřování pro účet SSH. Doporučujeme pomocí veřejných klíčů, protože jde o zabezpečení. Další informace najdete v tématu [vytvoření a použití klíčů SSH pro virtuální počítače s Linuxem](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentu.
    * __Skupina prostředků__: vyberte __použít existující__a pak vyberte skupinu prostředků obsahující virtuální síť vytvořili dříve.
    * __Umístění__: Vybrat stejné umístění jako virtuální síť.

    ![Základní konfigurace virtuálního počítače](./media/connect-on-premises-network/vm-basics.png)

    Další položky ponechte výchozí hodnoty a pak vyberte __OK__.

3. Z __zvolte velikost__ vyberte velikost virtuálního počítače. Pro účely tohoto kurzu vyberte možnost nejmenší a nejnižší náklady. Chcete-li pokračovat, použijte __vyberte__ tlačítko.

4. Z __nastavení__ části, zadejte následující informace:

    * __Virtuální síť__: vyberte virtuální síť, kterou jste vytvořili dříve.

    * __Podsíť__: Vyberte výchozí podsíť pro virtuální síť. Proveďte __není__ vyberte podsíť používá bránu sítě VPN.

    * __Účet úložiště diagnostiky__: Vyberte existující účet úložiště nebo vytvořte novou.

    ![Nastavení virtuální sítě](./media/connect-on-premises-network/virtual-network-settings.png)

    Ostatní položky ponechte výchozí hodnotu a pak vyberte __OK__ pokračujte.

5. Z __nákupní__ vyberte __nákupní__ pro vytvoření virtuálního počítače.

6. Po vytvoření virtuálního počítače, jeho __přehled__ oddíl se zobrazí. V seznamu na levé straně vyberte __vlastnosti__. Uložit __veřejnou IP adresu__ a __privátní IP adresa__ hodnoty. Použijete v další části.

    ![Veřejných a privátních IP adres](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalace a konfigurace vazby (DNS software)

1. Pomocí SSH se připojte k __veřejnou IP adresu__ virtuálního počítače. Následující příklad se připojí k virtuálnímu počítači na 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Nahraďte `sshuser` pomocí uživatelského účtu SSH, jste zadali při vytváření clusteru.

    > [!NOTE]
    > Existuje řada různých způsobů, jak získat `ssh` nástroj. V systému Linux, Unix a macOS se poskytuje jako součást operačního systému. Pokud používáte Windows, zvažte jednu z následujících možností:
    >
    > * [Azure Cloud Shell](../cloud-shell/quickstart.md)
    > * [Bash na Ubuntu ve Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git)https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH)https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. K instalaci vazby, použijte následující příkazy z relace SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Ke konfiguraci vazby pro předávání žádosti o překlad názvu na místní server DNS, použijte následující text jako obsah `/etc/bind/named.conf.options` souboru:

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
    > Nahraďte hodnoty v `goodclients` část s rozsahem IP adres virtuální sítě a v místní síti. Tento oddíl definuje adresy, které přijímá požadavky od tento server DNS.
    >
    > Nahraďte `192.168.0.1` položku `forwarders` část s IP adresou vašeho serveru DNS v místním. Tato položka směruje žádosti DNS na místní server DNS pro překlad.

    K úpravě tohoto souboru použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Chcete-li uložit soubor, použijte __Ctrl + X__, __Y__a potom __Enter__.

4. Z relace SSH použijte následující příkaz:

    ```bash
    hostname -f
    ```

    Tento příkaz vrátí hodnotu podobné následujícímu textu:

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` Text je __přípona DNS__ pro tuto virtuální síť. Uložte tuto hodnotu, bude se hodit později.

5. Ke konfiguraci vazby k překladu názvů DNS pro prostředky v rámci virtuální sítě, použijte následující text jako obsah `/etc/bind/named.conf.local` souboru:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > Je třeba nahradit `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` s příponou DNS, který jste získali dříve.

    K úpravě tohoto souboru použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Chcete-li uložit soubor, použijte __Ctrl + X__, __Y__a potom __Enter__.

6. Spuštění vazby, použijte následující příkaz:

    ```bash
    sudo service bind9 restart
    ```

7. Chcete-li ověřit, že vazby dokáže přeložit názvy prostředků ve vaší místní síti, použijte následující příkazy:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > Nahraďte `dns.mynetwork.net` s plně kvalifikovaný název domény (FQDN) prostředků ve vaší místní síti.
    >
    > Nahraďte `10.0.0.4` s __interní IP adresa__ vašeho vlastního serveru DNS ve virtuální síti.

    Odpověď se zobrazí podobný následujícímu textu:

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurace virtuální sítě pro použití vlastního serveru DNS.

Ke konfiguraci virtuální sítě, abyste místo Azure rekurzivní překladač použít vlastní server DNS, použijte následující kroky:

1. V [webu Azure portal](https://portal.azure.com), vyberte virtuální síť a pak vyberte __servery DNS__.

2. Vyberte __vlastní__a zadejte __interní IP adresa__ vlastního serveru DNS. Nakonec vyberte __Uložit__.

    ![Nastavení vlastního serveru DNS pro síť](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>Konfigurace místního serveru DNS

V předchozí části jste nakonfigurovali vlastní server DNS ke směrování žádostí na server DNS místní. Dále musíte nakonfigurovat na místním serveru DNS ke směrování žádostí na vlastní server DNS.

Konkrétní kroky pro konfiguraci serveru DNS najdete v dokumentaci pro váš software serveru DNS. Hledat podle pokynů na tom, jak nakonfigurovat __podmíněné předávání__.

Podmíněné předávání pouze předává požadavky pro konkrétní příponu DNS. V takovém případě musíte nakonfigurovat server pro předávání pro příponu DNS virtuální sítě. Požadavky pro tuto příponu mají předávat IP adresu serveru DNS. 

Následující text je příklad konfigurace pro podmíněné předávání **svázat** DNS software:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Informace o použití DNS na **Windows serveru 2016**, najdete v článku [přidat DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) dokumentace...

Jakmile jste nakonfigurovali na místním serveru DNS, můžete použít `nslookup` z místní sítě k ověření, aby dokázaly vyhledat názvy virtuální sítě. Následující příklad 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Tento příklad používá na místním serveru DNS na 196.168.0.4 názvu vlastního serveru DNS. Nahraďte IP adresu pro místní server DNS. Nahradit `dnsproxy` adresu se plně kvalifikovaný název vlastního serveru DNS.

## <a name="optional-control-network-traffic"></a>Volitelné: Řízení síťového provozu

Skupiny zabezpečení sítě (NSG) nebo trasy definované uživatelem (UDR) můžete použít k řízení síťového provozu. Skupiny zabezpečení sítě povolují filtrování příchozího a odchozího provozu a povolit nebo zakázat provoz. Trasy definované uživatelem umožňují řídit tok provozu mezi prostředky virtuální sítě, Internetu a v místní síti.

> [!WARNING]
> HDInsight vyžaduje příchozí přístup z konkrétních IP adres v cloudu Azure a neomezený odchozí přístup. Při použití skupin zabezpečení sítě nebo udr pro řízení provozu, je třeba provést následující kroky:

1. Najdete IP adresy pro umístění, která obsahuje virtuální síť. Seznam požadované IP adresy podle umístění najdete v tématu [požadované IP adresy](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).

2. Pro IP adresy, určenou v kroku 1, povolí příchozí provoz z této IP adresy.

   * Pokud používáte __NSG__: Povolit __příchozí__ přenosy na portu __443__ pro IP adresy.
   * Pokud používáte __uživatelem definovaná TRASA__: nastavte __dalšího segmentu směrování__ trasy, která má typ __Internet__ pro IP adresy.

Příklad použití Azure Powershellu nebo rozhraní příkazového řádku Azure k vytvoření skupin zabezpečení sítě, najdete v článku [rozšířit HDInsight s Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) dokumentu.

## <a name="create-the-hdinsight-cluster"></a>Vytvoření clusteru HDInsight

> [!WARNING]
> Před instalací HDInsight ve virtuální síti musíte nakonfigurovat vlastní server DNS.

Postupujte podle kroků v [vytvoření clusteru HDInsight pomocí webu Azure portal](./hdinsight-hadoop-create-linux-clusters-portal.md) dokumentu k vytvoření clusteru HDInsight.

> [!WARNING]
> * Při vytváření clusteru musíte zvolit umístění, která obsahuje virtuální síť.
>
> * V __upřesňující nastavení__ část konfigurace, je nutné vybrat virtuální síť a podsíť, která jste vytvořili dříve.

## <a name="connecting-to-hdinsight"></a>Připojení k HDInsight

Většina dokumentace na HDInsight předpokládá, že máte přístup ke clusteru přes internet. To znamená, že se ke clusteru můžete připojit třeba na https://CLUSTERNAME.azurehdinsight.net. Tato adresa se používá veřejnou brány, která není k dispozici, pokud jste použili skupiny zabezpečení sítě nebo udr pro omezení přístupu z Internetu.

Také odkazuje na některé dokumentaci `headnodehost` při připojování ke clusteru z relace SSH. Tato adresa je dostupná pouze z uzlů v rámci clusteru a není použitelný na klientů připojených přes virtuální síť.

K přímému připojení k HDInsight prostřednictvím virtuální sítě, použijte následující postup:

1. Chcete-li zjistit, interní plně kvalifikované názvy domény uzlů clusteru HDInsight, použijte jednu z následujících metod:

    ```powershell
    $resourceGroupName = "The resource group that contains the virtual network used with HDInsight"

    $clusterNICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName | where-object {$_.Name -like "*node*"}

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

2. Pokud chcete určit port, který je k dispozici na službu, naleznete v tématu [porty používané služby Hadoop v HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentu.

    > [!IMPORTANT]
    > Některé služby hostované na hlavní uzly jsou aktivní na jednom uzlu jenom po jednom. Pokud pokusu o přístup k službě na jeden hlavní uzel a selže, přejděte k hlavnímu uzlu.
    >
    > Například Ambari je aktivní pouze na jeden hlavní uzel v čase. Pokud se pokusíte přístup k Ambari na jeden hlavní uzel a vrátí chybu 404, je spuštěna na k hlavnímu uzlu.

## <a name="next-steps"></a>Další postup

* Další informace o používání HDInsight ve virtuální síti najdete v tématu [rozšířit HDInsight pomocí Azure Virtual Networks](./hdinsight-extend-hadoop-virtual-network.md).

* Další informace o virtuálních sítích Azure najdete v článku [Přehled služby Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení sítě](../virtual-network/security-overview.md).

* Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).

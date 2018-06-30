---
title: Připojit k místní síti - Azure HDInsight HDInsight | Microsoft Docs
description: Zjistěte, jak vytvořit cluster služby HDInsight ve virtuální síti Azure a připojte ho k síti na pracovišti. Zjistěte, jak nakonfigurovat překlad mezi HDInsight a místní sítí pomocí vlastního serveru DNS.
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: larryfr
ms.openlocfilehash: bfb6515ba9b7f36e90783444fc474dc575b32f37
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37113618"
---
# <a name="connect-hdinsight-to-your-on-premises-network"></a>Připojení HDInsightu k místní síti

Zjistěte, jak připojit HDInsight k místní síti pomocí virtuální sítě Azure a brány VPN. Tento dokument obsahuje informace o plánování na:

* Pomocí HDInsight ve virtuální síti Azure, která se připojuje k síti na pracovišti.

* Konfigurace překlad názvu DNS mezi virtuální sítí a v místní síti.

* Konfigurace skupin zabezpečení sítě pro omezení přístupu k Internetu do HDInsight.

* Porty poskytovaných v HDInsight ve virtuální síti.

## <a name="create-the-virtual-network-configuration"></a>Vytvoření konfigurace virtuální sítě

Naučte se vytvořit virtuální síť Azure, která je připojena k místní síti pomocí v následujících dokumentech:
    
* [Pomocí webu Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)

* [Použití Azure PowerShellu](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)

* [Použití Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md)

## <a name="configure-name-resolution"></a>Konfigurování překladu

Chcete-li povolit HDInsight a prostředky v připojené k síti komunikovat podle názvu, musíte provést následující akce:

* Vytvoření vlastního serveru DNS ve virtuální síti Azure.

* Konfigurace virtuální sítě pro použití vlastního serveru DNS místo výchozího Azure rekurzivní překladač.

* Konfigurace předávání mezi vlastního serveru DNS a serveru DNS na místě.

Tato konfigurace umožňuje následující chování:

* Požadavky pro plně kvalifikované názvy domény které mají příponu DNS __pro virtuální síť__ se předávají do vlastního serveru DNS. Tyto požadavky vlastního serveru DNS potom předává do překladače rekurzivní Azure, která vrátí hodnotu IP adresu.

* Všechny ostatní požadavky jsou předávány na místní server DNS. I požadavky na veřejné internetové prostředky, jako je například microsoft.com jsou předány na místním serveru DNS pro rozlišení názvu.

Zelená řádky v následujícím diagramu jsou požadavky na prostředky, které končí příponou DNS virtuální sítě. Modré řádky jsou požadavky na prostředky v místní síti nebo na veřejného Internetu.

![Diagram způsob řešení požadavky na DNS v konfiguraci v tomto dokumentu](./media/connect-on-premises-network/on-premises-to-cloud-dns.png)

### <a name="create-a-custom-dns-server"></a>Vytvoření vlastního serveru DNS

> [!IMPORTANT]
> Musíte vytvořit a nakonfigurovat DNS server před instalací HDInsight do virtuální sítě.

Chcete-li vytvořit virtuální počítač Linux, který používá [vazby](https://www.isc.org/downloads/bind/) DNS software, použijte následující postup:

> [!NOTE]
> Následující postup použijte [portál Azure](https://portal.azure.com) vytvořit virtuální počítač Azure. Pro další způsoby vytvoření virtuálního počítače, najdete v následujících dokumentech:
>
> * [Vytvoření virtuálního počítače – rozhraní příkazového řádku Azure](../virtual-machines/linux/quick-create-cli.md)
> * [Vytvoření virtuálního počítače - prostředí Azure PowerShell](../virtual-machines/linux/quick-create-portal.md)

1. Z [portál Azure](https://portal.azure.com), vyberte __+__, __výpočetní__, a __Ubuntu Server 16.04 LTS__.

    ![Vytvoření virtuálního počítače Ubuntu](./media/connect-on-premises-network/create-ubuntu-vm.png)

2. V části __Základy__ zadejte následující informace:

    * __Název__: popisný název, který identifikuje tento virtuální počítač. Například __DNSProxy__.
    * __Uživatelské jméno__: název účtu SSH.
    * __Veřejný klíč SSH__ nebo __heslo__: metody ověřování pro účet SSH. Doporučujeme používat veřejných klíčů, protože se jedná o bezpečnější. Další informace najdete v tématu [vytvoření a použití klíče SSH pro virtuální počítače s Linuxem](../virtual-machines/linux/mac-create-ssh-keys.md) dokumentu.
    * __Skupina prostředků__: vyberte __použít existující__a pak vyberte skupinu prostředků, která obsahuje virtuální síť vytvořili dříve.
    * __Umístění__: Vybrat stejné umístění jako virtuální síť.

    ![Základní konfigurace virtuálního počítače](./media/connect-on-premises-network/vm-basics.png)

    Nechte ostatní položky na výchozí hodnoty a potom vyberte __OK__.

3. Z __zvolte velikost__ vyberte velikost virtuálního počítače. V tomto kurzu vyberte možnost nejmenší a nejnižší náklady. Chcete-li pokračovat, použijte __vyberte__ tlačítko.

4. Z __nastavení__ části, zadejte následující informace:

    * __Virtuální síť__: vyberte virtuální síť, kterou jste vytvořili dříve.

    * __Podsíť__: Vyberte výchozí podsíť virtuální sítě. Proveďte __není__ vyberte podsíť používá bránu sítě VPN.

    * __Účet úložiště diagnostiky__: Vyberte existující účet úložiště, nebo vytvořte novou.

    ![Nastavení virtuální sítě](./media/connect-on-premises-network/virtual-network-settings.png)

    Nechte ostatní položky na výchozí hodnotu a pak vyberte __OK__ pokračujte.

5. Z __nákupu__ vyberte __nákupu__ tlačítko pro vytvoření virtuálního počítače.

6. Po vytvoření virtuálního počítače, jeho __přehled__ části se zobrazí. V seznamu na levé straně vyberte __vlastnosti__. Uložit __veřejnou IP adresu__ a __privátní IP adresa__ hodnoty. Použije se v další části.

    ![Veřejné a privátní IP adresy](./media/connect-on-premises-network/vm-ip-addresses.png)

### <a name="install-and-configure-bind-dns-software"></a>Instalace a konfigurace vazby (DNS software)

1. Použití SSH se připojit k __veřejnou IP adresu__ virtuálního počítače. Následující příklad se připojí k virtuálnímu počítači na 40.68.254.142:

    ```bash
    ssh sshuser@40.68.254.142
    ```

    Nahraďte `sshuser` k uživatelskému účtu SSH, který jste zadali při vytváření clusteru.

    > [!NOTE]
    > Existuje mnoho různých způsobů, jak získat `ssh` nástroj. Na systému Linux, Unix a systému macOS je poskytována jako součást operačního systému. Pokud používáte systém Windows, zvažte jednu z následujících možností:
    >
    > * [Azure Cloud Shell](../cloud-shell/quickstart.md)
    > * [Bash na Ubuntu na Windows 10](https://msdn.microsoft.com/commandline/wsl/about)
    > * [Git (https://git-scm.com/)](https://git-scm.com/)
    > * [OpenSSH (https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)](https://github.com/PowerShell/Win32-OpenSSH/wiki/Install-Win32-OpenSSH)

2. Chcete-li nainstalovat vazby, použijte následující příkazy z relace SSH:

    ```bash
    sudo apt-get update -y
    sudo apt-get install bind9 -y
    ```

3. Ke konfiguraci vazby k předávání žádostí o překlad názvu na místní server DNS, použijte následující text jako obsah `/etc/bind/named.conf.options` souboru:

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
    > Nahraďte hodnoty v `goodclients` části s použitým rozsahem IP adres virtuální sítě i místní sítě. Tento oddíl definuje adresy, které tento server DNS přijímá požadavky od.
    >
    > Nahraďte `192.168.0.1` položku v `forwarders` část s IP adresou serveru DNS na místě. Tato položka směruje požadavky DNS na serveru místní DNS pro rozlišení.

    K úpravě tohoto souboru, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.options
    ```

    Chcete-li uložit soubor, použijte __Ctrl + X__, __Y__a potom __Enter__.

4. Z relace SSH použijte následující příkaz:

    ```bash
    hostname -f
    ```

    Tento příkaz vrátí hodnotu podobná následující text:

        dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net

    `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` Text je __příponu DNS__ pro tuto virtuální síť. Uložte tuto hodnotu, bude se hodit později.

5. Ke konfiguraci vazby k překladu názvů DNS pro prostředky v rámci virtuální sítě, použijte následující text jako obsah `/etc/bind/named.conf.local` souboru:

        // Replace the following with the DNS suffix for your virtual network
        zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
            type forward;
            forwarders {168.63.129.16;}; # The Azure recursive resolver
        };

    > [!IMPORTANT]
    > Musíte `icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net` s příponou DNS, který jste získali dříve.

    K úpravě tohoto souboru, použijte následující příkaz:

    ```bash
    sudo nano /etc/bind/named.conf.local
    ```

    Chcete-li uložit soubor, použijte __Ctrl + X__, __Y__a potom __Enter__.

6. Pokud chcete spustit vazby, použijte následující příkaz:

    ```bash
    sudo service bind9 restart
    ```

7. Pokud chcete ověřit, že vazby může překládat názvy zdrojů v síti na pracovišti, použijte následující příkazy:

    ```bash
    sudo apt install dnsutils
    nslookup dns.mynetwork.net 10.0.0.4
    ```

    > [!IMPORTANT]
    > Nahraďte `dns.mynetwork.net` s plně kvalifikovaný název domény (FQDN) prostředku v síti na pracovišti.
    >
    > Nahraďte `10.0.0.4` s __interní IP adresu__ vašeho vlastního serveru DNS ve virtuální síti.

    Odpověď se zobrazí podobná následující text:

        Server:         10.0.0.4
        Address:        10.0.0.4#53

        Non-authoritative answer:
        Name:   dns.mynetwork.net
        Address: 192.168.0.4

### <a name="configure-the-virtual-network-to-use-the-custom-dns-server"></a>Konfigurace virtuální sítě pro použití vlastního serveru DNS

Ke konfiguraci virtuální sítě pro použití vlastního serveru DNS místo Azure rekurzivní překladač, použijte následující kroky:

1. V [portál Azure](https://portal.azure.com), vyberte virtuální síť a potom vyberte __servery DNS__.

2. Vyberte __vlastní__a zadejte __interní IP adresu__ vlastního serveru DNS. Nakonec vyberte __Uložit__.

    ![Nastavení vlastního serveru DNS pro síť](./media/connect-on-premises-network/configure-custom-dns.png)

### <a name="configure-the-on-premises-dns-server"></a>Konfigurace serveru DNS na místě

V předchozí části že jste nakonfigurovali vlastního serveru DNS pro předávání požadavků na místním serveru DNS. Dále musíte nakonfigurovat na místním serveru DNS pro předávání požadavků na vlastního serveru DNS.

Konkrétní kroky pro konfiguraci serveru DNS najdete v dokumentaci pro software serveru DNS. Vyhledejte kroky pro konfiguraci __pro podmíněné předávání__.

Podmíněné dopředného pouze předá požadavky pro konkrétní příponu DNS. V takovém případě musíte nakonfigurovat server pro předávání pro příponu DNS virtuální sítě. Požadavky pro tuto příponu předáte vlastního serveru DNS na IP adresu. 

Tento text je příklad konfigurace pro podmíněné předávání pro **vazby** DNS softwaru:

    zone "icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The custom DNS server's internal IP address
    };

Informace o použití DNS na **systému Windows Server 2016**, najdete v článku [přidat DnsServerConditionalForwarderZone](https://technet.microsoft.com/itpro/powershell/windows/dnsserver/add-dnsserverconditionalforwarderzone) dokumentace...

Jakmile jste nakonfigurovali na místním serveru DNS, můžete použít `nslookup` z místní sítě chcete-li ověřit, zda je možné přeložit názvy ve virtuální síti. Následující příklad 

```bash
nslookup dnsproxy.icb0d0thtw0ebifqt0g1jycdxd.ex.internal.cloudapp.net 196.168.0.4
```

Tento příklad používá místní server DNS v 196.168.0.4 pro překlad názvu vlastního serveru DNS. Nahraďte IP adresu pro místní server DNS. Nahraďte `dnsproxy` adresa se plně kvalifikovaný název domény vlastního serveru DNS.

## <a name="optional-control-network-traffic"></a>Volitelné: Řízení síťového provozu

Skupiny zabezpečení sítě (NSG) nebo trasy definované uživatelem (UDR) můžete použít k řízení síťových přenosů. Skupiny Nsg umožňují filtrovat příchozí a odchozí přenosy a povolit nebo odepřít provoz. Udr umožňují řídit tok přenosů mezi prostředky ve virtuální síti, internet a místní sítě.

> [!WARNING]
> HDInsight vyžaduje příchozí přístup z konkrétní IP adresy v cloudu Azure a neomezený přístup pro odchozí připojení. Pokud používáte skupiny Nsg nebo udr k řízení provozu, je třeba provést následující kroky:

1. Najít IP adresy pro umístění, které obsahuje virtuální síť. Seznam požadované IP adresy podle umístění najdete v tématu [požadované IP adresy](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip).

2. Pro IP adresy identifikovaného v kroku 1, povolí příchozí komunikaci z této IP adresy.

   * Pokud používáte __NSG__: Povolit __příchozí__ přenosy na portu __443__ IP adresy.
   * Pokud používáte __UDR__: nastavte __další směrování__ typ trasy k __Internet__ IP adresy.

Příklad použití Azure PowerShell nebo rozhraní příkazového řádku Azure k vytvoření skupin Nsg, naleznete v části [rozšířit HDInsight s virtuálními sítěmi Azure](./hdinsight-extend-hadoop-virtual-network.md#hdinsight-nsg) dokumentu.

## <a name="create-the-hdinsight-cluster"></a>Vytvoření clusteru HDInsight

> [!WARNING]
> Před instalací HDInsight ve virtuální síti musíte nakonfigurovat vlastního serveru DNS.

Postupujte podle kroků v [vytvoření clusteru HDInsight pomocí portálu Azure](./hdinsight-hadoop-create-linux-clusters-portal.md) dokument k vytvoření clusteru HDInsight.

> [!WARNING]
> * Při vytváření clusteru musíte zvolit umístění, které obsahuje virtuální síť.
>
> * V __upřesňující nastavení__ část konfigurace, musíte vybrat virtuální síť a podsíť, kterou jste vytvořili dříve.

## <a name="connecting-to-hdinsight"></a>Připojení k HDInsight

Většina dokumentace v HDInsight předpokládá, že máte přístup ke clusteru přes internet. To znamená, že se ke clusteru můžete připojit třeba na https://CLUSTERNAME.azurehdinsight.net. Tato adresa se používá veřejný brány, která není k dispozici, pokud jste použili skupiny Nsg nebo udr k omezení přístupu z Internetu.

Některé dokumentaci odkazuje také `headnodehost` při připojování ke clusteru z relace SSH. Tato adresa je dostupná pouze z uzlů v rámci clusteru a již není použitelné na klientů připojených přes virtuální síť.

K přímému připojení k HDInsight prostřednictvím virtuální sítě, použijte následující postup:

1. Pokud chcete zjistit, interní plně kvalifikované názvy domény uzlů clusteru HDInsight, použijte jednu z následujících metod:

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

2. Ke zjištění portu, která je dostupná na služby, najdete v článku [porty používané služby Hadoop v HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentu.

    > [!IMPORTANT]
    > Některé služby hostované o hlavních uzlech aktivní pouze na jednom uzlu současně. Pokud se pokusíte přístup k službě jeden hlavního uzlu a ona selže, přepněte do jiného hlavního uzlu.
    >
    > Například Ambari je aktivní pouze jeden hlavního uzlu současně. Pokud se pokusíte přístup k Ambari na jeden hlavní uzel a vrátí chybu 404, je spuštěna z hlavního uzlu.

## <a name="next-steps"></a>Další postup

* Další informace o používání HDInsight ve virtuální síti, najdete v části [rozšířit HDInsight pomocí virtuálních sítí Azure](./hdinsight-extend-hadoop-virtual-network.md).

* Další informace o virtuálních sítí Azure, najdete v článku [Přehled virtuálních sítí Azure](../virtual-network/virtual-networks-overview.md).

* Další informace o skupinách zabezpečení sítě najdete v tématu [skupin zabezpečení sítě](../virtual-network/security-overview.md).

* Další informace o trasy definované uživatelem, najdete v části [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).

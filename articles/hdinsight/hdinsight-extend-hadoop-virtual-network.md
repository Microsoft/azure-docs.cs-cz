---
title: "Rozšíření prostředí HDInsight pomocí virtuální sítě - Azure | Microsoft Docs"
description: "Naučte se používat pro připojení HDInsight k jiným cloudovým prostředkům nebo prostředkům ve vašem datovém centru Azure Virtual Network"
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 37b9b600-d7f8-4cb1-a04a-0b3a827c6dcc
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/21/2018
ms.author: larryfr
ms.openlocfilehash: ea686ea5dd6166d6e4e4055cb4a2cdd70af81ab4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Rozšíření Azure HDInsight pomocí virtuální síť Azure

Další informace o použití prostředí HDInsight pomocí [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Použití virtuální sítě Azure umožňuje následující scénáře:

* Připojení k HDInsight přímo z místní sítě.

* Připojování k datům HDInsight ukládá v Azure virtuální sítě.

* Přímý přístup k služby Hadoop, které nejsou k dispozici veřejně přes internet. Například Kafka rozhraní API nebo rozhraní API HBase Java.

> [!WARNING]
> Informace v tomto dokumentu vyžaduje znalosti o protokolu TCP/IP v síti. Pokud nejste obeznámeni s prací v síti TCP/IP, by měla spolupracovat s uživatelem, který je před provedením změny produkční sítě.

> [!IMPORTANT]
> Pokud hledáte pokyny krok za krokem k připojení HDInsight do místní sítě pomocí virtuální síť Azure, najdete v článku [HDInsight připojit k místní síti](connect-on-premises-network.md) dokumentu.

## <a name="planning"></a>Plánování

Tady jsou otázky, které je nutné zodpovědět při plánování instalace HDInsight ve virtuální síti:

* Potřebujete k instalaci HDInsight do existující virtuální síť? Nebo můžete vytvořit novou síť?

    Pokud používáte stávající virtuální síť, musíte změnit síťovou konfiguraci, před instalací HDInsight. Další informace najdete v tématu [přidat HDInsight k existující virtuální síti](#existingvnet) části.

* Opravdu chcete připojit virtuální síť obsahující HDInsight k jiné virtuální síti nebo v místní síti?

    Snadno pracovat s prostředky v sítích, můžete vytvořit vlastní DNS a nakonfigurujte předávání DNS. Další informace najdete v tématu [připojení více sítí](#multinet) části.

* Chcete omezit či přesměrování příchozích a odchozích přenosů do HDInsight

    HDInsight musí mít neomezený komunikace s konkrétní IP adresy v datového centra Azure. Existují také několik portů, které musí být povoleno přes brány firewall pro komunikaci klienta. Další informace najdete v tématu [řízení síťového provozu](#networktraffic) části.

## <a id="existingvnet"></a>Přidání HDInsight do existující virtuální síť

Pokud chcete zjistit, jak přidat nové HDInsight do existující virtuální síť Azure, postupujte podle kroků v této části.

> [!NOTE]
> Nelze přidat stávající cluster HDInsight do virtuální sítě.

1. Používáte pro virtuální síť klasický nebo modelu nasazení Resource Manager?

    HDInsight 3.4 a větší vyžaduje virtuální sítě Resource Manager. Dřívějších verzích HDInsight vyžaduje klasickou virtuální síť.

    Pokud vaší stávající sítě klasickou virtuální síť, musíte vytvořit virtuální síť Resource Manager a potom připojení dvě. [Připojení virtuální sítě classic k nové virtuální sítě](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Jakmile připojený, HDInsight v síti Resource Manager nainstalovaný mohou komunikovat s prostředky v síti classic.

2. Používáte vynucené tunelování? Vynucené tunelování je nastavení podsítě, které vynutí odchozí přenosy z Internetu do zařízení pro kontroly a protokolování. HDInsight nepodporuje vynucené tunelování. Buď odeberte vynucené tunelování před instalací HDInsight do podsítě, nebo vytvořit novou podsíť pro HDInsight.

3. Používáte skupiny zabezpečení sítě, trasy definované uživatelem nebo virtuální síťové zařízení k omezení přenosu do nebo z virtuální sítě?

    Jako spravovanou službu vyžaduje HDInsight neomezený přístup k několika IP adresy v datového centra Azure. Povolit komunikaci se tyto IP adresy, aktualizujte všechny existující skupiny zabezpečení sítě nebo trasy definované uživatelem.

    HDInsight je hostitelem více služeb, které používají různé porty. Nejsou blokovány přenosy na těchto portech. Seznam portů pro tvorbu přes virtuální zařízení brány firewall, naleznete v části [zabezpečení](#security) části.

    Pokud chcete vyhledat existující konfiguraci zabezpečení, použijte následující příkazy prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure:

    * Skupiny zabezpečení sítě

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermnetworksecuritygroup -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network nsg list --resource-group $RESOURCEGROUP
        ```

        Další informace najdete v tématu [odstraňování skupin zabezpečení sítě](../virtual-network/virtual-network-nsg-troubleshoot-portal.md) dokumentu.

        > [!IMPORTANT]
        > Pravidla skupiny zabezpečení sítě jsou použity v pořadí podle priority pravidel. První pravidlo, který odpovídá vzorku provoz se použije a žádné jiné se použijí pro tento přenos. Pravidla pořadí od nejvíce projektovou na omezenou. Další informace najdete v tématu [filtrování provozu sítě přenosů se skupinami zabezpečení sítě](../virtual-network/virtual-networks-nsg.md) dokumentu.

    * Trasy definované uživatelem

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermroutetable -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network route-table list --resource-group $RESOURCEGROUP
        ```

        Další informace najdete v tématu [řešení potíží s trasy](../virtual-network/virtual-network-routes-troubleshoot-portal.md) dokumentu.

4. Vytvoření clusteru HDInsight a vyberte virtuální síť Azure během konfigurace. Porozumět procesu vytváření clusteru, postupujte podle kroků v následujících dokumentech:

    * [Vytvoření HDInsight pomocí webu Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Vytvoření HDInsight pomocí Azure PowerShellu](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Vytvoření HDInsight pomocí Azure CLI 1.0](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Vytvoření HDInsight pomocí šablony Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

  > [!IMPORTANT]
  > Přidání HDInsight k virtuální síti je krok volitelné konfiguraci. Je nutné vybrat virtuální síť, při konfiguraci clusteru.

## <a id="multinet"></a>Připojení více sítí

Největší výzvou s konfigurací s více sítě je překlad mezi sítěmi.

Azure poskytuje překlad názvů pro služby Azure, které jsou nainstalovány ve virtuální síti. Toto řešení předdefinovaným názvem umožňuje HDInsight pro připojení v následujících zdrojích informací s použitím platný plně kvalifikovaný název domény (FQDN):

* Jakémukoli prostředku, který je dostupný na Internetu. Například microsoft.com, google.com.

* Jakémukoli prostředku, který je ve stejné virtuální síti Azure, pomocí __interní název DNS__ prostředku. Například pokud používáte překlad výchozí, následuje příklad interní DNS názvy přiřazené k pracovním uzlům HDInsight:

    * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
    * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Obě tyto uzly může komunikovat přímo s a jiné uzly v HDInsight pomocí interní názvy DNS.

Rozlišení názvů výchozí nemá __není__ povolit HDInsight překládat názvy prostředků v sítích, které jsou připojeny k virtuální síti. Například je společné pro připojení k místní síti do virtuální sítě. S pouze výchozí název řešení HDInsight nemají přístup k prostředkům v místní síti podle názvu. Naopak je také nastavena hodnota true, prostředky ve vaší místní síti nemají přístup k prostředkům ve virtuální síti podle názvu.

> [!WARNING]
> Musíte vytvořit vlastního serveru DNS a konfigurovat virtuální sítě, abyste ho použít před vytvořením clusteru HDInsight.

Chcete-li povolit překlad mezi virtuální sítě a prostředky v připojené k sítím, musíte provést následující akce:

1. Vytvoření vlastního serveru DNS ve virtuální síti Azure, kam chcete nainstalovat HDInsight.

2. Konfigurace virtuální sítě pro použití vlastního serveru DNS.

3. Najít že přiřazené přípona DNS pro vaši virtuální síť Azure. Tato hodnota je podobná `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Informace o hledání přípon DNS, najdete v článku [příklad: vlastní DNS](#example-dns) části.

4. Konfigurace předávání mezi servery DNS. Konfigurace závisí na typu vzdálené sítě.

    * Pokud vzdálené sítě do místní sítě, nakonfigurujte DNS takto:
        
        * __Vlastní DNS__ (ve virtuální síti):

            * Předat dál požadavků pro přípony DNS virtuální sítě do Azure rekurzivní překladač (168.63.129.16). Zpracovává požadavky na prostředky ve virtuální síti Azure

            * Předávání všech ostatních požadavků na místní server DNS. Místní DNS zpracovává všechny ostatní požadavky na rozlišení názvů, i požadavky na internetové prostředky, jako je například Microsoft.com.

        * __Místní DNS__: předávat požadavky pro příponu DNS virtuální sítě do vlastního serveru DNS. Vlastního serveru DNS se potom předá do překladače Azure rekurzivní.

        Tato požadavky na konfiguraci tras pro plně kvalifikované názvy domény, které obsahují příponu DNS virtuální sítě do vlastního serveru DNS. Zpracovává všechny požadavky (i pro veřejné internetové adresy) na místním serveru DNS.

    * Pokud je vzdálené síť jinou virtuální sítí Azure, nakonfigurujte DNS následujícím způsobem:

        * __Vlastní DNS__ (v každé virtuální sítě):

            * Požadavky pro příponu DNS virtuální sítě se předávají do vlastní servery DNS. Služba DNS v každé virtuální sítě je zodpovědná za řešení prostředky ve své síti.

            * Předávání všech ostatních požadavků na Azure rekurzivní překladač. Rekurzivní překladač zodpovídá za řešení místní a prostředků z Internetu.

        DNS server pro každou síť předá požadavky na druhý, na základě přípony DNS. Ostatní požadavky jsou vyřešeny pomocí Azure rekurzivní překladač.

    Příklad každé konfiguraci, naleznete v části [příklad: vlastní DNS](#example-dns) části.

Další informace najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-using-your-own-dns-server) dokumentu.

## <a name="directly-connect-to-hadoop-services"></a>Připojovat přímo k služby Hadoop

Většina dokumentace v HDInsight předpokládá, že máte přístup ke clusteru přes internet. Pro příklad, který můžete připojit ke clusteru v https://CLUSTERNAME.azurehdinsight.net. Tato adresa se používá veřejný brány, která není k dispozici, pokud jste použili skupiny Nsg nebo udr k omezení přístupu z Internetu.

Pro připojení k Ambari a další webové stránky prostřednictvím virtuální sítě, použijte následující kroky:

1. Pokud chcete zjistit, interní plně kvalifikované názvy domény (FQDN) uzlů clusteru HDInsight, použijte jednu z následujících metod:

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

    V seznamu uzlů vrátil najít plně kvalifikovaný název domény pro hlavních uzlech a použít plně kvalifikované názvy domény pro připojení k Ambari a dalších webových služeb. Například použít `http://<headnode-fqdn>:8080` pro přístup k Ambari.

    > [!IMPORTANT]
    > Některé služby hostované o hlavních uzlech aktivní pouze na jednom uzlu současně. Pokud se pokusíte přístup k službě jeden hlavního uzlu a vrátí chybu 404, přepněte do jiného hlavního uzlu.

2. Zjistit uzel a port, který je k dispozici na služby, najdete v článku [porty používané služby Hadoop v HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentu.

## <a id="networktraffic"></a> Řízení síťového provozu

Síťový provoz v Azure Virtual Network se dá řídit pomocí následujících metod:

* **Skupin zabezpečení sítě** (NSG) vám umožní filtrovat příchozí a odchozí přenosy v síti. Další informace najdete v tématu [filtrování provozu sítě přenosů se skupinami zabezpečení sítě](../virtual-network/virtual-networks-nsg.md) dokumentu.

    > [!WARNING]
    > HDInsight nepodporuje omezení odchozí provoz.

* **Trasy definované uživatelem** (UDR) definovat tok přenosů mezi prostředky v síti. Další informace najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md) dokumentu.

* **Síťových virtuálních zařízení** replikace funkce zařízení, jako jsou brány firewall a směrovače. Další informace najdete v tématu [síťových zařízení](https://azure.microsoft.com/solutions/network-appliances) dokumentu.

Jako spravovanou službu vyžaduje HDInsight neomezený přístup ke službám Azure stavu a správu v cloudu Azure. Pokud používáte skupiny Nsg a udr, je nutné zajistit, že HDInsight tyto služby můžete stále komunikovat s HDInsight.

HDInsight poskytuje služby na několika portech. Při použití brány firewall virtuální zařízení, musí umožňovat komunikaci na portech používaných pro tyto služby. Další informace najdete v části [požadované porty].

### <a id="hdinsight-ip"></a> Prostředí HDInsight pomocí skupin zabezpečení sítě a trasy definované uživatelem

Pokud máte v úmyslu používat **skupin zabezpečení sítě** nebo **trasy definované uživatelem** k řízení síťových přenosů, proveďte následující akce před instalací HDInsight:

1. Určete oblast Azure, který chcete použít pro HDInsight.

2. Určete IP adresy, které vyžadují HDInsight. Další informace najdete v tématu [IP adresy, které vyžadují HDInsight](#hdinsight-ip) části.

3. Vytvoření nebo úprava skupiny zabezpečení sítě nebo trasy definované uživatelem, které chcete nainstalovat HDInsight do podsítě.

    * __Skupin zabezpečení sítě__: Povolit __příchozí__ přenosy na portu __443__ z IP adresy.
    * __Trasy definované uživatelem__: vytvoření směrování pro každou IP adresu a nastavení __typ dalšího směrování__ k __Internet__.

Další informace o skupinách zabezpečení sítě nebo trasy definované uživatelem naleznete v následující dokumentaci:

* [Skupina zabezpečení sítě](../virtual-network/virtual-networks-nsg.md)

* [Trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling"></a>Vynucené tunelování

Vynucené tunelování je uživatelem definované konfigurace směrování, kde veškerý provoz z podsítě musí v určité síti nebo umístění, jako například do místní sítě. HDInsight nemá __není__ podporu vynuceného tunelování.

## <a id="hdinsight-ip"></a> Požadované IP adresy

> [!IMPORTANT]
> Stav a správu služeb Azure musí být schopen komunikovat s HDInsight. Pokud používáte skupiny zabezpečení sítě nebo trasy definované uživatelem, povolí provoz z IP adres pro tyto služby k dosažení HDInsight.
>
> Pokud nepoužijete skupin zabezpečení sítě nebo trasy definované uživatelem pro řízení provozu, můžete ignorovat v této části.

Pokud používáte skupiny zabezpečení sítě nebo trasy definované uživatelem, musíte povolit přenosy z Azure stavu a správu služeb k dosažení HDInsight. Použijte následující postup k vyhledání IP adresy, které musí být povoleno:

1. Vždy musí povolit provoz z následujících IP adres:

    | IP adresa | Povolené portu | Směr |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Příchozí |
    | 23.99.5.239 | 443 | Příchozí |
    | 168.61.48.131 | 443 | Příchozí |
    | 138.91.141.162 | 443 | Příchozí |

2. Pokud váš cluster HDInsight v jednom z následujících oblastí, musíte povolit přenosy z IP adresy uvedené pro oblast:

    > [!IMPORTANT]
    > Pokud není uvedené oblast Azure, kterou používáte, pak použijte pouze čtyři IP adresy z kroku 1.

    | Země | Oblast | Povolené IP adresy | Povolené portu | Směr |
    | ---- | ---- | ---- | ---- | ----- |
    | Asie | Východní Asie | 23.102.235.122</br>52.175.38.134 | 443 | Příchozí |
    | &nbsp; | Jihovýchodní Asie | 13.76.245.160</br>13.76.136.249 | 443 | Příchozí |
    | Austrálie | Austrálie – východ | 104.210.84.115</br>13.75.152.195 | 443 | Příchozí |
    | &nbsp; | Austrálie – jihovýchod | 13.77.2.56</br>13.77.2.94 | 443 | Příchozí |
    | Brazílie | Brazílie – jih | 191.235.84.104</br>191.235.87.113 | 443 | Příchozí |
    | Kanada | Východní Kanada | 52.229.127.96</br>52.229.123.172 | 443 | Příchozí |
    | &nbsp; | Střední Kanada | 52.228.37.66</br>52.228.45.222 | 443 | Příchozí |
    | Čína | Čína – sever | 42.159.96.170</br>139.217.2.219 | 443 | Příchozí |
    | &nbsp; | Čína – východ | 42.159.198.178</br>42.159.234.157 | 443 | Příchozí |
    | Evropa | Severní Evropa | 52.164.210.96</br>13.74.153.132 | 443 | Příchozí |
    | &nbsp; | Západní Evropa| 52.166.243.90</br>52.174.36.244 | 443 | Příchozí |
    | Německo | Německo – střed | 51.4.146.68</br>51.4.146.80 | 443 | Příchozí |
    | &nbsp; | Německo – severovýchod | 51.5.150.132</br>51.5.144.101 | 443 | Příchozí |
    | Indie | Střed Indie | 52.172.153.209</br>52.172.152.49 | 443 | Příchozí |
    | Japonsko | Japonsko – východ | 13.78.125.90</br>13.78.89.60 | 443 | Příchozí |
    | &nbsp; | Japonsko – západ | 40.74.125.69</br>138.91.29.150 | 443 | Příchozí |
    | Korea | Korea – střed | 52.231.39.142</br>52.231.36.209 | 433 | Příchozí |
    | &nbsp; | Korea – jih | 52.231.203.16</br>52.231.205.214 | 443 | Příchozí
    | Spojené království | Spojené království – západ | 51.141.13.110</br>51.141.7.20 | 443 | Příchozí |
    | &nbsp; | Spojené království – jih | 51.140.47.39</br>51.140.52.16 | 443 | Příchozí |
    | Spojené státy | Střed USA | 13.67.223.215</br>40.86.83.253 | 443 | Příchozí |
    | &nbsp; | Východ USA | 13.82.225.233</br>40.71.175.99 | 443 | Příchozí |
    | &nbsp; | Střed USA – sever | 157.56.8.38</br>157.55.213.99 | 443 | Příchozí |
    | &nbsp; | Západní střed USA | 52.161.23.15</br>52.161.10.167 | 443 | Příchozí |
    | &nbsp; | Západní USA | 13.64.254.98</br>23.101.196.19 | 443 | Příchozí |
    | &nbsp; | Západní USA 2 | 52.175.211.210</br>52.175.222.222 | 443 | Příchozí |

    Informace o IP adresy pro Azure Government, najdete v článku [Azure Government Intelligence + analýzy](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) dokumentu.

3. Pokud používáte vlastního serveru DNS s vaší virtuální síti, musíte také povolit přístup z __168.63.129.16__. Tato adresa se Azure rekurzivní překladač. Další informace najdete v tématu [překlad názvů pro virtuální počítače a Role instance](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentu.

Další informace najdete v tématu [řízení síťového provozu](#networktraffic) části.

## <a id="hdinsight-ports"></a> Požadované porty

Pokud máte v úmyslu používat síť **virtuální zařízení brány firewall** zabezpečit virtuální síť, musíte povolit odchozí přenosy na následující porty:

* 53
* 443
* 1433
* 11000-11999
* 14000-14999

Seznam portů pro určité služby, najdete v článku [porty používané služby Hadoop v HDInsight](hdinsight-hadoop-port-settings-for-services.md) dokumentu.

Další informace o pravidlech brány firewall pro virtuální zařízení, najdete v článku [virtuální zařízení scénář](../virtual-network/virtual-network-scenario-udr-gw-nva.md) dokumentu.

## <a id="hdinsight-nsg"></a>Příklad: skupin zabezpečení sítě s HDInsight

Příklady v této části ukazují, jak vytvořit skupiny pravidla, která umožňují HDInsight ke komunikaci se službami Azure správu zabezpečení sítě. Před použitím v příkladech, upravte IP adresy tak, aby odpovídala těm, které jsou pro oblast Azure, kterou používáte. Tyto informace můžete najít [prostředí HDInsight pomocí skupin zabezpečení sítě a trasy definované uživatelem](#hdinsight-ip) části.

### <a name="azure-resource-management-template"></a>Šablony Azure Správa prostředků

Následující šablony správy prostředků vytvoří virtuální síť, která omezuje příchozí provoz, ale povoluje provoz z IP adresy, které vyžadují HDInsight. Tato šablona vytvoří HDInsight cluster také ve virtuální síti.

* [Nasazení zabezpečených virtuální síť Azure a cluster systému HDInsight Hadoop](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]
> Změna IP adresy použité v tomto příkladu tak, aby odpovídaly oblasti Azure, který používáte. Tyto informace můžete najít [prostředí HDInsight pomocí skupin zabezpečení sítě a trasy definované uživatelem](#hdinsight-ip) části.

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí následujícího skriptu prostředí PowerShell k vytvoření virtuální sítě, která omezuje příchozí provoz a umožňuje přenos z IP adres pro oblast Severní Evropa.

> [!IMPORTANT]
> Změna IP adresy použité v tomto příkladu tak, aby odpovídaly oblasti Azure, který používáte. Tyto informace můžete najít [prostředí HDInsight pomocí skupin zabezpečení sítě a trasy definované uživatelem](#hdinsight-ip) části.

```powershell
$vnetName = "Replace with your virtual network name"
$resourceGroupName = "Replace with the resource group the virtual network is in"
$subnetName = "Replace with the name of the subnet that you plan to use for HDInsight"
# Get the Virtual Network object
$vnet = Get-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $resourceGroupName
# Get the region the Virtual network is in.
$location = $vnet.Location
# Get the subnet object
$subnet = $vnet.Subnets | Where-Object Name -eq $subnetName
# Create a Network Security Group.
# And add exemptions for the HDInsight health and management services.
$nsg = New-AzureRmNetworkSecurityGroup `
    -Name "hdisecure" `
    -ResourceGroupName $resourceGroupName `
    -Location $location `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -name "hdirule1" `
        -Description "HDI health and management address 52.164.210.96" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "52.164.210.96" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 300 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule2" `
        -Description "HDI health and management 13.74.153.132" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "13.74.153.132" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 301 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule3" `
        -Description "HDI health and management 168.61.49.99" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.49.99" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 302 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule4" `
        -Description "HDI health and management 23.99.5.239" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "23.99.5.239" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 303 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule5" `
        -Description "HDI health and management 168.61.48.131" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "168.61.48.131" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 304 `
        -Direction Inbound `
    | Add-AzureRmNetworkSecurityRuleConfig `
        -Name "hdirule6" `
        -Description "HDI health and management 138.91.141.162" `
        -Protocol "*" `
        -SourcePortRange "*" `
        -DestinationPortRange "443" `
        -SourceAddressPrefix "138.91.141.162" `
        -DestinationAddressPrefix "VirtualNetwork" `
        -Access Allow `
        -Priority 305 `
        -Direction Inbound `
# Set the changes to the security group
Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
# Apply the NSG to the subnet
Set-AzureRmVirtualNetworkSubnetConfig `
    -VirtualNetwork $vnet `
    -Name $subnetName `
    -AddressPrefix $subnet.AddressPrefix `
    -NetworkSecurityGroup $nsg
$vnet | Set-AzureRmVirtual Network
```

> [!IMPORTANT]
> Tento příklad ukazuje, jak přidat pravidla, která povolí příchozí komunikaci na požadované IP adresy. Neobsahuje na pravidlo můžete omezit příchozí přístup z jiných zdrojů.
>
> Následující příklad ukazuje, jak povolit přístup SSH z Internetu:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-cli"></a>Azure CLI

Pomocí následujících kroků vytvořte virtuální síť, která omezuje příchozí provoz, ale povoluje provoz z IP adresy, které vyžadují HDInsight.

1. Použijte následující příkaz k vytvoření nové skupiny zabezpečení sítě s názvem `hdisecure`. Nahraďte **RESOURCEGROUPNAME** ke skupině prostředků, který obsahuje virtuální síť Azure. Nahraďte **umístění** s umístěním (oblastí), která byla skupina vytvořena v.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Po vytvoření skupiny, zobrazí informace o nové skupiny.

2. Následující informace vám pomůžou přidat pravidla pro novou skupinu zabezpečení sítě, které umožní příchozí komunikaci na portu 443 ze služby Azure HDInsight stavu a správu. Nahraďte **RESOURCEGROUPNAME** s názvem skupiny prostředků, který obsahuje virtuální síť Azure.

    > [!IMPORTANT]
    > Změna IP adresy použité v tomto příkladu tak, aby odpovídaly oblasti Azure, který používáte. Tyto informace můžete najít [prostředí HDInsight pomocí skupin zabezpečení sítě a trasy definované uživatelem](#hdinsight-ip) části.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Pro načtení jedinečný identifikátor pro tuto skupinu zabezpečení sítě, použijte následující příkaz:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Tento příkaz vrátí hodnotu podobná následující text:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Pokud nejste očekávané výsledky, použijte dvojité uvozovky kolem id v příkazu.

4. Chcete-li použít skupinu zabezpečení sítě k podsíti, použijte následující příkaz. Nahraďte __GUID__ a __RESOURCEGROUPNAME__ hodnoty s těmi vrácené z předchozího kroku. Nahraďte __VNETNAME__ a __SUBNETNAME__ s název virtuální sítě a název podsítě, který chcete vytvořit.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Po dokončení tohoto příkazu můžete nainstalovat HDInsight do virtuální sítě.

> [!IMPORTANT]
> Tyto kroky otevíraly jenom přístup k službě HDInsight stavu a správu v cloudu Azure. Jiný přístup ke clusteru HDInsight z mimo virtuální síť je blokována. Pokud chcete povolit přístup mimo virtuální síť, musíte přidat další pravidla skupiny zabezpečení sítě.
>
> Následující příklad ukazuje, jak povolit přístup SSH z Internetu:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> Příklad: Konfigurace DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Překlad mezi virtuální sítí a připojené místní sítě

Tento příklad vytvoří následující předpoklady:

* Máte virtuální síť Azure, která je připojena k místní síti pomocí brány VPN.

* Vlastního serveru DNS ve virtuální síti běží jako operační systém Linux nebo Unix.

* [Vytvoření vazby](https://www.isc.org/downloads/bind/) je nainstalován na vlastního serveru DNS.

Na vlastního serveru DNS ve virtuální síti:

1. Použití Azure PowerShell nebo rozhraní příkazového řádku Azure k nalezení přípona DNS virtuální sítě:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Na vlastního serveru DNS pro virtuální síť, použijte následující text jako obsah `/etc/bind/named.conf.local` souboru:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Nahraďte `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` hodnotu s příponou DNS virtuální sítě.

    Tato konfigurace směrovat všechny požadavky na DNS pro příponu DNS virtuální sítě na Azure rekurzivní překladač.

2. Na vlastního serveru DNS pro virtuální síť, použijte následující text jako obsah `/etc/bind/named.conf.options` souboru:

    ```
    // Clients to accept requests from
    // TODO: Add the IP range of the joined network to this list
    acl goodclients {
        10.0.0.0/16; # IP address range of the virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            # All other requests are sent to the following
            forwarders {
                192.168.0.1; # Replace with the IP address of your on-premises DNS server
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Nahraďte `10.0.0.0/16` hodnotu s rozsah IP adres vaší virtuální sítě. Tato položka umožňuje název řešení požadavků adresy v tomto rozsahu.

    * Přidat rozsah IP adres místní sítě, aby `acl goodclients { ... }` oddílu.  položka umožňuje požadavky na rozlišení názvů z prostředků v místní síti.
    
    * Nahraďte hodnotu `192.168.0.1` s IP adresou serveru DNS na místě. Tato položka směrování všech ostatních požadavků na DNS na místní server DNS.

3. Pokud chcete používat konfiguraci, restartujte vazby. Například, `sudo service bind9 restart`.

4. Přidejte do místní server DNS pro podmíněné předávání. Nakonfigurujte pro podmíněné předávání na odesílání žádostí pro příponu DNS z kroku 1 na vlastního serveru DNS.

    > [!NOTE]
    > V dokumentaci pro váš software DNS pro konkrétní o tom, jak přidat server pro podmíněné předávání.

Po dokončení těchto kroků se můžete připojit k prostředkům v síti, buď pomocí plně kvalifikovaných názvů domény (FQDN). HDInsight můžete nainstalovat do virtuální sítě.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Překlad mezi dvěma připojené virtuální sítě

Tento příklad vytvoří následující předpoklady:

* Máte dvě virtuální sítě Azure, které jsou připojené pomocí brány VPN nebo partnerský vztah.

* Vlastní server DNS v obě sítě běží jako operační systém Linux nebo Unix.

* [Vytvoření vazby](https://www.isc.org/downloads/bind/) je nainstalován na vlastních serverech DNS.

1. Použití Azure PowerShell nebo rozhraní příkazového řádku Azure k nalezení příponu DNS obě virtuální sítě:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Použít následující text jako obsah `/etc/bind/named.config.local` souboru na vlastního serveru DNS. Tuto změnu proveďte na serveru DNS vlastní obě virtuální sítě.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Nahraďte `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` hodnotu s příponu DNS __jiných__ virtuální sítě. Tato položka směruje požadavky pro příponu DNS vzdálené sítě na vlastní DNS v síti.

3. Na vlastní servery DNS v obě virtuální sítě, použijte následující text jako obsah `/etc/bind/named.conf.options` souboru:

    ```
    // Clients to accept requests from
    acl goodclients {
        10.1.0.0/16; # The IP address range of one virtual network
        10.0.0.0/16; # The IP address range of the other virtual network
        localhost;
        localnets;
    };

    options {
            directory "/var/cache/bind";

            recursion yes;

            allow-query { goodclients; };

            forwarders {
            168.63.129.16;   # Azure recursive resolver         
            };

            dnssec-validation auto;

            auth-nxdomain no;    # conform to RFC1035
            listen-on { any; };
    };
    ```
    
    * Nahraďte `10.0.0.0/16` a `10.1.0.0/16` hodnot pomocí IP adresy rozsahů virtuálních sítí. Tato položka umožňuje prostředky v každé sítě odesílá požadavky na servery DNS.

    Překladač Azure rekurzivní zpracovává všechny žádosti, které nejsou pro přípony DNS virtuální sítě (například microsoft.com).

4. Pokud chcete používat konfiguraci, restartujte vazby. Například `sudo service bind9 restart` na obou serverech DNS.

Po dokončení těchto kroků se můžete připojit k prostředkům ve virtuální síti pomocí plně kvalifikovaných názvů domény (FQDN). HDInsight můžete nainstalovat do virtuální sítě.

## <a name="next-steps"></a>Další postup

* Příklad začátku do konce konfigurace HDInsight pro připojení k místní síti, najdete v části [HDInsight připojit k místní síti](./connect-on-premises-network.md).
* Konfigurace clustery Hbase v Azure virtuální sítě, naleznete v části [clusterů vytvořte HBase v HDInsight v Azure Virtual Network](hbase/apache-hbase-provision-vnet.md).
* Konfigurace geografická replikace HBase, najdete v části [nastavení HBase clusteru replikace v virtuálních sítí Azure](hbase/apache-hbase-replication.md).
* Další informace o virtuálních sítí Azure, najdete v článku [Přehled virtuálních sítí Azure](../virtual-network/virtual-networks-overview.md).

* Další informace o skupinách zabezpečení sítě najdete v tématu [skupin zabezpečení sítě](../virtual-network/virtual-networks-nsg.md).

* Další informace o trasy definované uživatelem, najdete v části [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).

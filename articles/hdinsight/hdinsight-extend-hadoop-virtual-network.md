---
title: Rozšíření pomocí virtuální sítě – Azure HDInsight
description: Zjistěte, jak pomocí Azure Virtual Network připojení HDInsight k jiným cloudovým prostředkům nebo prostředkům ve vašem datovém centru
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/26/2018
ms.openlocfilehash: 98c62f54e2413bd67600db182c452d0d5965f239
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972177"
---
# <a name="extend-azure-hdinsight-using-an-azure-virtual-network"></a>Rozšíření Azure HDInsight pomocí Azure Virtual Network

[!INCLUDE [classic-cli-warning](../../includes/requires-classic-cli.md)]

Zjistěte, jak používat HDInsight s [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Použití služby Azure Virtual Network umožňuje následující scénáře:

* Připojení k HDInsight přímo z místní sítě.

* Připojování k datům HDInsight ukládají ve službě Azure Virtual network.

* Přímý přístup k systému Hadoop služby, které nejsou k dispozici veřejně přes internet. Například rozhraní API systému Kafka nebo HBase Java API.

> [!WARNING]
> Informace v tomto dokumentu jsou nutné znalosti toho sítě TCP/IP. Pokud nejste obeznámeni s prací v síti TCP/IP, by měla spolupracovat s někým, kdo je před provedením změny do produkčních sítích.

> [!IMPORTANT]
> Pokud hledáte pokyny krok za krokem k připojování HDInsight k místní síti pomocí služby Azure Virtual Network, najdete v článku [HDInsight připojit k místní síti](connect-on-premises-network.md) dokumentu.

## <a name="planning"></a>Plánování

Následují otázky, které je nutné odpovědět při plánování instalace HDInsight ve virtuální síti:

* Je třeba nainstalovat do existující virtuální síť HDInsight? Nebo vytváříte nové sítě?

    Pokud použijete existující virtuální sítě, budete muset upravit konfiguraci sítě, abyste mohli nainstalovat HDInsight. Další informace najdete v tématu [přidat do existující virtuální síť HDInsight](#existingvnet) oddílu.

* Chcete pro připojení virtuální sítě s HDInsight k jiné virtuální síti nebo v místní síti?

    Snadno pracovat s prostředky v sítích, budete muset vytvořit vlastní DNS a nakonfigurujte předávání DNS. Další informace najdete v tématu [připojení více sítí](#multinet) oddílu.

* Opravdu chcete omezit/přesměrování příchozí nebo odchozí provoz do HDInsight?

    HDInsight, musí mít stejně neomezený komunikaci s konkrétní IP adresy v datovém centru Azure. Existuje také několik portů, které musí povolit v bránách firewall pro komunikaci s klienty. Další informace najdete v tématu [řízení síťového provozu](#networktraffic) oddílu.

## <a id="existingvnet"></a>Přidat do existující virtuální síť HDInsight

Pomocí kroků v této části zjistíte, jak přidat nové HDInsight do existující virtuální sítě Azure.

> [!NOTE]
> Nelze přidat existující cluster HDInsight do virtuální sítě.

1. Používáte pro virtuální síť modelu classic nebo model nasazení Resource Manager?

    HDInsight 3.4 a vyšší vyžaduje virtuální síť Resource Manageru. Dřívějších verzích HDInsight vyžaduje klasickou virtuální síť.

    Pokud váš existující síť je klasickou virtuální síť, musíte vytvořit virtuální síť Resource Manageru a pak připojit dva. [Připojení klasických virtuálních sítí k novým virtuálním sítím](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

    Jakmile připojený, HDInsight, které jsou nainstalovány v síti správce prostředků může interagovat s prostředky v klasickou síť.

2. Používáte vynucené tunelování? Vynucené tunelování znamená nastavení podsítě, která vynutí odchozí internetový provoz do zařízení pro kontrolu a protokolování. HDInsight nepodporuje vynucené tunelování. Buď odeberte vynucené tunelování před instalací HDInsight do podsítě, nebo vytvořte novou podsíť pro HDInsight.

3. Používáte skupiny zabezpečení sítě, tras definovaných uživatelem nebo virtuální síťová zařízení k omezení provozu do nebo ven z virtuální sítě?

    HDInsight je spravovaná služba, vyžaduje neomezený přístup k několika IP adresami v datovém centru Azure. Povolit komunikaci se tyto IP adresy, aktualizujte všechny existující skupiny zabezpečení sítě nebo trasy definované uživatelem.

    HDInsight je hostitelem více služeb, které používají různé porty. Neblokujete provoz na těchto portech. Seznam portů, aby přes virtuální zařízení brány firewall, najdete v článku [zabezpečení](#security) oddílu.

    Najít existující konfiguraci zabezpečení, použijte následující příkazy prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure Classic:

    * Skupiny zabezpečení sítě

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermnetworksecuritygroup -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network nsg list --resource-group $RESOURCEGROUP
        ```

        Další informace najdete v tématu [řešení potíží se skupinami zabezpečení sítě](../virtual-network/diagnose-network-traffic-filter-problem.md) dokumentu.

        > [!IMPORTANT]
        > Pravidla skupiny zabezpečení sítě se použijí v pořadí podle priority pravidla. Použít první pravidlo, které se shoduje se vzorem provozu a žádné jiné se použijí pro tento přenos. Pravidla pořadí od nejvíce omezující na omezenou. Další informace najdete v tématu [filtrování provozu sítě s použitím skupin zabezpečení sítě](../virtual-network/security-overview.md) dokumentu.

    * Trasy definované uživatelem

        ```powershell
        $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
        get-azurermroutetable -resourcegroupname $resourceGroupName
        ```

        ```azurecli-interactive
        read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
        az network route-table list --resource-group $RESOURCEGROUP
        ```

        Další informace najdete v tématu [řešení potíží s trasami](../virtual-network/diagnose-network-routing-problem.md) dokumentu.

4. Vytvoření clusteru HDInsight a vyberte virtuální síť Azure během konfigurace. Vysvětlení procesu vytváření clusteru pomocí kroků v následujících dokumentech:

    * [Vytvoření HDInsight pomocí webu Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md)
    * [Vytvoření HDInsight pomocí Azure PowerShellu](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)
    * [Vytvoření HDInsight pomocí rozhraní příkazového řádku Azure Classic](hdinsight-hadoop-create-linux-clusters-azure-cli.md)
    * [Vytvoření HDInsight pomocí šablony Azure Resource Manageru](hdinsight-hadoop-create-linux-clusters-arm-templates.md)

  > [!IMPORTANT]
  > Přidání HDInsight k virtuální síti je volitelná konfigurace krok. Je nutné vybrat virtuální síť, při konfiguraci clusteru.

## <a id="multinet"></a>Připojení víc sítí

Největším problémem s několika síťovými konfigurací se překlad mezi sítěmi.

Azure nabízí řešení názvů pro služby Azure, které jsou nainstalovány ve virtuální síti. Ten integrované umožňuje HDInsight pro připojení k následujícím prostředkům pomocí použitím plně kvalifikovaného názvu domény (FQDN):

* Prostředek, který je dostupný na Internetu. Třeba microsoft.com, google.com.

* Prostředek, který je ve stejné virtuální síti Azure, pomocí __interní název DNS__ prostředku. Například při použití výchozí název rozhodnutí, následují interní názvy DNS příklad přiřazené pracovní uzly HDInsight:

    * wn0-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net
    * wn2-hdinsi.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net

    Oba tyto uzly může komunikovat přímo s a dalších uzlů v HDInsight, pomocí interní názvy DNS.

Nemá na výchozí název řešení __není__ povolit HDInsight k překladu názvů prostředky v sítích, které jsou připojené k virtuální síti. Například je společné pro připojení místní sítě k virtuální síti. Se pouze výchozí název řešení HDInsight nemají přístup k prostředkům v místní síti podle názvu. Opak platí to i naopak prostředků ve vaší místní síti nemají přístup k prostředkům ve virtuální síti podle názvu.

> [!WARNING]
> Musíte vytvořit vlastní server DNS a konfigurace virtuální sítě pro použití před vytvořením clusteru HDInsight.

Pokud chcete povolit překlad mezi virtuální sítí a prostředky v sítích připojené k doméně, je třeba provést následující akce:

1. Vytvoření vlastního serveru DNS v Azure Virtual Network, ve kterém plánujete nainstalovat HDInsight.

2. Konfigurace virtuální sítě na používání vlastního serveru DNS.

3. Najdete že přiřazené přípona DNS pro vaši virtuální síť Azure. Tato hodnota je podobný `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net`. Informace o hledání přípon DNS, najdete v článku [příklad: vlastní DNS](#example-dns) oddílu.

4. Konfigurace předávání mezi servery DNS. Konfigurace závisí na typu vzdálené sítě.

    * Pokud vzdálené sítě je v místní síti, nakonfigurujte DNS následujícím způsobem:
        
        * __Vlastní DNS__ (ve virtuální síti):

            * Směrovala požadavky pro příponu DNS virtuální sítě do Azure rekurzivní překladače (168.63.129.16). Zpracovává požadavky na prostředky ve virtuální síti Azure

            * Předávání všech ostatních požadavků na místní server DNS. DNS v místním zpracovává všechny ostatní požadavky na rozlišení názvů, dokonce i požadavky na internetové prostředky, jako je například Microsoft.com.

        * __DNS v místním__: předávat požadavky pro příponu DNS virtuální sítě na vlastní server DNS. Vlastní server DNS se potom předá do Azure rekurzivní překladače.

        Žádosti o konfiguraci této trasy pro plně kvalifikované názvy domény, které obsahují přípony DNS virtuální sítě na vlastní server DNS. Na místním serveru DNS jsou zpracovány všechny ostatní požadavky (i pro veřejné internetové adresy).

    * Pokud vzdálené sítě se dvěma virtuálními sítěmi Azure, nakonfigurujte DNS následujícím způsobem:

        * __Vlastní DNS__ (v obou virtuálních sítích):

            * Požadavky pro příponu DNS virtuální sítě se předávají do vlastní servery DNS. DNS v obou virtuálních sítích je zodpovědná za překlad prostředků v rámci příslušné síti.

            * Předávání všech ostatních požadavků na Azure rekurzivní překladač. Rekurzivní překladač zodpovídá za řešení místním prostředím a internetovými prostředky.

        DNS server pro každou síť směruje požadavky do jiné, na základě přípony DNS. Ostatní požadavky jsou vyřešeny použitím překladač Azure rekurzivní.

    Příklad každou konfiguraci, najdete v článku [příklad: vlastní DNS](#example-dns) oddílu.

Další informace najdete v tématu [překlad názvů pro virtuální počítače a instance rolí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentu.

## <a name="directly-connect-to-hadoop-services"></a>Připojit přímo k služby Hadoop

Většina dokumentace na HDInsight předpokládá, že máte přístup ke clusteru přes internet. To znamená, že se ke clusteru můžete připojit třeba na https://CLUSTERNAME.azurehdinsight.net. Tato adresa se používá veřejnou brány, která není k dispozici, pokud jste použili skupiny zabezpečení sítě nebo udr pro omezení přístupu z Internetu.

Pro připojení k Ambari a další webové stránky prostřednictvím virtuální sítě, postupujte následovně:

1. Ke zřízení uzlů clusteru HDInsight interní plně kvalifikované názvy domény (FQDN), použijte jednu z následujících metod:

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

    V seznamu uzlů vrátil vyhledat hlavní uzly plně kvalifikovaný název domény a používat plně kvalifikované názvy pro připojení k Ambari a další webové služby. Například použít `http://<headnode-fqdn>:8080` pro přístup k Ambari.

    > [!IMPORTANT]
    > Některé služby hostované na hlavní uzly jsou aktivní na jednom uzlu jenom po jednom. Pokud pokusu o přístup k službě na jeden hlavní uzel a vrátí chybu 404, přejděte k hlavnímu uzlu.

2. Zjistíte uzlu a port, který je k dispozici na služby, najdete v článku [porty používané služby Hadoop v HDInsight](./hdinsight-hadoop-port-settings-for-services.md) dokumentu.

## <a id="networktraffic"></a> Řízení síťového provozu

Síťový provoz v Azure Virtual Network se dá řídit pomocí následujících metod:

* **Skupiny zabezpečení sítě** (NSG) umožňují filtrovat příchozí a odchozí přenosy v síti. Další informace najdete v tématu [filtrování provozu sítě s použitím skupin zabezpečení sítě](../virtual-network/security-overview.md) dokumentu.

    > [!WARNING]
    > HDInsight nepodporuje omezení odchozího provozu.

* **Trasy definované uživatelem** (UDR) definovat provozní tok mezi prostředky v síti. Další informace najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md) dokumentu.

* **Síťová virtuální zařízení** replikovat funkce zařízení, jako jsou brány firewall a směrovače. Další informace najdete v tématu [síťová zařízení](https://azure.microsoft.com/solutions/network-appliances) dokumentu.

HDInsight je spravovaná služba, vyžaduje neomezený přístup ke stavu a Správa služby Azure v cloudu Azure. Při použití skupin zabezpečení sítě a trasy definované uživatelem, musíte zajistit, že HDInsight tyto služby nadále komunikovat s HDInsight.

HDInsight poskytuje služby na několika portech. Při použití brány firewall virtuální zařízení, musí umožňovat provoz na portech používaných pro tyto služby. Další informace najdete v části [požadované porty].

### <a id="hdinsight-ip"></a> HDInsight se skupinami zabezpečení sítě a trasy definované uživatelem

Pokud máte v úmyslu používat **skupiny zabezpečení sítě** nebo **trasy definované uživatelem** k řízení síťového provozu, proveďte následující akce před instalací HDInsight:

1. Identifikujte oblasti Azure, který chcete použít pro HDInsight.

2. Identifikujte IP adresy, které vyžadují HDInsight. Další informace najdete v tématu [IP adresy, které jsou vyžadované HDInsight](#hdinsight-ip) oddílu.

3. Vytvoření nebo úprava skupiny zabezpečení sítě nebo trasy definované uživatelem pro podsíť, která budete chtít nainstalovat do HDInsight.

    * __Skupiny zabezpečení sítě__: Povolit __příchozí__ přenosy na portu __443__ z IP adresy.
    * __Trasy definované uživatelem__: vytvořit trasu pro každou IP adresu a nastavit __typem dalšího segmentu směrování__ k __Internet__.

Další informace o skupinách zabezpečení sítě nebo trasy definované uživatelem najdete v následující dokumentaci:

* [Skupina zabezpečení sítě](../virtual-network/security-overview.md)

* [Trasy definované uživatelem](../virtual-network/virtual-networks-udr-overview.md)

#### <a name="forced-tunneling"></a>Vynucené tunelování

Vynucené tunelování znamená konfigurace směrování definované uživatelem, ve kterém musí veškerý provoz z jedné podsítě určitého síťového umístění nebo umístění, například v místní síti. HDInsight nepodporuje __není__ podporu vynucené tunelování.

## <a id="hdinsight-ip"></a> Požadované IP adresy

> [!IMPORTANT]
> Stav a správu služeb Azure musí být schopný komunikovat s HDInsight. Pokud používáte skupiny zabezpečení sítě nebo trasy definované uživatelem, umožňují provoz z IP adresy pro tyto služby k dosažení HDInsight.
>
> Pokud je velmi riskantní používat skupiny zabezpečení sítě nebo uživatelem definované trasy k řízení provozu, můžete ignorovat tento oddíl.

Pokud používáte skupiny zabezpečení sítě nebo trasy definované uživatelem, musíte povolit provoz z Azure stavu a Správa služby k dosažení HDInsight. Pokud chcete zjistit IP adresy, které musí být povoleno, postupujte následovně:

1. Vždy musí povolit provoz z následujících IP adres:

    | IP adresa | Povolený port | Směr |
    | ---- | ----- | ----- |
    | 168.61.49.99 | 443 | Příchozí |
    | 23.99.5.239 | 443 | Příchozí |
    | 168.61.48.131 | 443 | Příchozí |
    | 138.91.141.162 | 443 | Příchozí |

2. Pokud váš cluster HDInsight je v jednom z následujících oblastí, musíte povolit přenosy z IP adresy uvedené pro oblast:

    > [!IMPORTANT]
    > Pokud není uveden oblast Azure, které používáte, pak použijte pouze čtyři IP adresy z kroku 1.

    | Země | Oblast | Povolené IP adresy | Povolený port | Směr |
    | ---- | ---- | ---- | ---- | ----- |
    | Asie | Východní Asie | 23.102.235.122</br>52.175.38.134 | 443 | Příchozí |
    | &nbsp; | Jihovýchodní Asie | 13.76.245.160</br>13.76.136.249 | 443 | Příchozí |
    | Austrálie | Austrálie – východ | 104.210.84.115</br>13.75.152.195 | 443 | Příchozí |
    | &nbsp; | Austrálie – jihovýchod | 13.77.2.56</br>13.77.2.94 | 443 | Příchozí |
    | Brazílie | Brazílie – jih | 191.235.84.104</br>191.235.87.113 | 443 | Příchozí |
    | Kanada | Kanada – východ | 52.229.127.96</br>52.229.123.172 | 443 | Příchozí |
    | &nbsp; | Kanada – střed | 52.228.37.66</br>52.228.45.222 | 443 | Příchozí |
    | Čína | Čína – sever | 42.159.96.170</br>139.217.2.219 | 443 | Příchozí |
    | &nbsp; | Čína – východ | 42.159.198.178</br>42.159.234.157 | 443 | Příchozí |
    | Evropa | Severní Evropa | 52.164.210.96</br>13.74.153.132 | 443 | Příchozí |
    | &nbsp; | Západní Evropa| 52.166.243.90</br>52.174.36.244 | 443 | Příchozí |
    | Německo | Německo – střed | 51.4.146.68</br>51.4.146.80 | 443 | Příchozí |
    | &nbsp; | Německo – severovýchod | 51.5.150.132</br>51.5.144.101 | 443 | Příchozí |
    | Indie | Střed Indie | 52.172.153.209</br>52.172.152.49 | 443 | Příchozí |
    | &nbsp; | Indie – jih | 104.211.223.67<br/>104.211.216.210 | 443 | Příchozí |
    | Japonsko | Japonsko – východ | 13.78.125.90</br>13.78.89.60 | 443 | Příchozí |
    | &nbsp; | Japonsko – západ | 40.74.125.69</br>138.91.29.150 | 443 | Příchozí |
    | Jižní Korea | Jižní Korea – střed | 52.231.39.142</br>52.231.36.209 | 433 | Příchozí |
    | &nbsp; | Jižní Korea – jih | 52.231.203.16</br>52.231.205.214 | 443 | Příchozí
    | Spojené království | Spojené království – západ | 51.141.13.110</br>51.141.7.20 | 443 | Příchozí |
    | &nbsp; | Velká Británie – jih | 51.140.47.39</br>51.140.52.16 | 443 | Příchozí |
    | Spojené státy | USA – střed | 13.67.223.215</br>40.86.83.253 | 443 | Příchozí |
    | &nbsp; | USA – východ | 13.82.225.233</br>40.71.175.99 | 443 | Příchozí |
    | &nbsp; | Střed USA – sever | 157.56.8.38</br>157.55.213.99 | 443 | Příchozí |
    | &nbsp; | Západní střed USA | 52.161.23.15</br>52.161.10.167 | 443 | Příchozí |
    | &nbsp; | USA – západ | 13.64.254.98</br>23.101.196.19 | 443 | Příchozí |
    | &nbsp; | USA – západ 2 | 52.175.211.210</br>52.175.222.222 | 443 | Příchozí |

    Informace o IP adres k používání pro Azure Government, najdete v článku [Azure Government inteligence a analýza](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics) dokumentu.

3. Pokud používáte vlastní server DNS s virtuální sítí, musíte také povolit přístup z __168.63.129.16__. Tato adresa je rekurzivní překladače Azure. Další informace najdete v tématu [překlad názvů pro virtuální počítače a Role instancí](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) dokumentu.

Další informace najdete v tématu [řízení síťového provozu](#networktraffic) oddílu.

## <a id="hdinsight-ports"></a> Požadované porty

Pokud máte v úmyslu používat síť **virtuální zařízení brány firewall** pro zabezpečení virtuální sítě, musíte povolit odchozí přenosy na následující porty:

* 53
* 443
* 1433
* 11000-11999
* 14000-14999

Seznam portů pro určité služby, najdete v článku [porty používané služby Hadoop v HDInsight](hdinsight-hadoop-port-settings-for-services.md) dokumentu.

Další informace o pravidlech brány firewall pro virtuální zařízení, najdete v článku [virtuální zařízení scénář](../virtual-network/virtual-network-scenario-udr-gw-nva.md) dokumentu.

## <a id="hdinsight-nsg"></a>Příklad: skupiny zabezpečení sítě s HDInsight

Příklady v této části ukazují, jak vytvořit pravidla skupiny, které umožňují HDInsight komunikace se službami Azure správu zabezpečení sítě. Před použitím v příkladech, upravte IP adres tak, aby odpovídaly pro oblast Azure, které používáte. Tyto informace můžete najít [HDInsight pomocí skupin zabezpečení sítě a trasy definované uživatelem](#hdinsight-ip) oddílu.

### <a name="azure-resource-management-template"></a>Šablony Azure Resource Manageru

Následující šablony Resource Manageru se vytvoří virtuální síť, která omezují příchozí provoz, ale umožní provoz z IP adresy vyžaduje HDInsight. Tato šablona také vytvoří HDInsight cluster ve virtuální síti.

* [Nasazení zabezpečené virtuální síti Azure a cluster HDInsight Hadoop](https://azure.microsoft.com/resources/templates/101-hdinsight-secure-vnet/)

> [!IMPORTANT]
> Změna IP adresy používané v tomto příkladu tak, aby odpovídaly oblast Azure, které používáte. Tyto informace můžete najít [HDInsight pomocí skupin zabezpečení sítě a trasy definované uživatelem](#hdinsight-ip) oddílu.

### <a name="azure-powershell"></a>Azure PowerShell

Pomocí následujícího skriptu prostředí PowerShell k vytvoření virtuální sítě, která omezuje příchozí provoz a umožní provoz z IP adres pro oblast Severní Evropa.

> [!IMPORTANT]
> Změna IP adresy používané v tomto příkladu tak, aby odpovídaly oblast Azure, které používáte. Tyto informace můžete najít [HDInsight pomocí skupin zabezpečení sítě a trasy definované uživatelem](#hdinsight-ip) oddílu.

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
$vnet | Set-AzureRmVirtualNetwork
```

> [!IMPORTANT]
> Tento příklad ukazuje, jak přidat pravidla, která povolí příchozí provoz na požadované IP adresy. Neobsahuje pravidlo, které omezují příchozí přístup z jiných zdrojů.
>
> Následující příklad ukazuje, jak povolit přístup přes SSH z Internetu:
>
> ```powershell
> Add-AzureRmNetworkSecurityRuleConfig -Name "SSH" -Description "SSH" -Protocol "*" -SourcePortRange "*" -DestinationPortRange "22" -SourceAddressPrefix "*" -DestinationAddressPrefix "VirtualNetwork" -Access Allow -Priority 306 -Direction Inbound
> ```

### <a name="azure-classic-cli"></a>Klasické rozhraní příkazového řádku Azure

Následujícím postupem vytvoření virtuální sítě, které omezují příchozí provoz, ale umožní provoz z IP adresy vyžaduje HDInsight.

1. Pomocí následujícího příkazu vytvořte novou skupinu zabezpečení sítě s názvem `hdisecure`. Nahraďte **RESOURCEGROUPNAME** s vybranou skupinou prostředků, která obsahuje virtuální síť Azure. Nahraďte **umístění** umístěním (oblastí), která byla skupina vytvořena v.

    ```azurecli
    az network nsg create -g RESOURCEGROUPNAME -n hdisecure -l LOCATION
    ```

    Po vytvoření skupiny se zobrazí informace o nové skupiny.

2. Použijte následující postup k přidání pravidel do nové skupiny zabezpečení sítě, které umožní příchozí komunikaci na portu 443 ze stavu a Správa služby Azure HDInsight. Nahraďte **RESOURCEGROUPNAME** s názvem skupiny prostředků, která obsahuje virtuální síť Azure.

    > [!IMPORTANT]
    > Změna IP adresy používané v tomto příkladu tak, aby odpovídaly oblast Azure, které používáte. Tyto informace můžete najít [HDInsight pomocí skupin zabezpečení sítě a trasy definované uživatelem](#hdinsight-ip) oddílu.

    ```azurecli
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule1 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "52.164.210.96" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 300 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "13.74.153.132" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 301 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.49.99" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 302 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "23.99.5.239" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 303 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "168.61.48.131" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 304 --direction "Inbound"
    az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule2 --protocol "*" --source-port-range "*" --destination-port-range "443" --source-address-prefix "138.91.141.162" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 305 --direction "Inbound"
    ```

3. Pokud chcete načíst jedinečný identifikátor pro tuto skupinu zabezpečení sítě, použijte následující příkaz:

    ```azurecli
    az network nsg show -g RESOURCEGROUPNAME -n hdisecure --query 'id'
    ```

    Tento příkaz vrátí hodnotu podobné následujícímu textu:

        "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"

    Pokud neobdržíte očekávané výsledky, použijte dvojité uvozovky kolem id v příkazu.

4. Chcete-li použít skupinu zabezpečení sítě k podsíti použijte následující příkaz. Nahradit __GUID__ a __RESOURCEGROUPNAME__ vrácené hodnoty s těmi v předchozím kroku. Nahraďte __VNETNAME__ a __SUBNETNAME__ s názvem virtuální sítě a název podsítě, kterou chcete vytvořit.

    ```azurecli
    az network vnet subnet update -g RESOURCEGROUPNAME --vnet-name VNETNAME --name SUBNETNAME --set networkSecurityGroup.id="/subscriptions/GUID/resourceGroups/RESOURCEGROUPNAME/providers/Microsoft.Network/networkSecurityGroups/hdisecure"
    ```

    Po dokončení tohoto příkazu můžete nainstalovat HDInsight do virtuální sítě.

> [!IMPORTANT]
> Tyto kroky otevřít pouze přístup k službě stavu a správa HDInsight v cloudu Azure. Je blokován jiným přístup ke clusteru HDInsight z mimo virtuální síť. Pokud chcete povolit přístup mimo virtuální síť, musíte přidat další pravidla skupiny zabezpečení sítě.
>
> Následující příklad ukazuje, jak povolit přístup přes SSH z Internetu:
>
> ```azurecli
> az network nsg rule create -g RESOURCEGROUPNAME --nsg-name hdisecure -n hdirule5 --protocol "*" --source-port-range "*" --destination-port-range "22" --source-address-prefix "*" --destination-address-prefix "VirtualNetwork" --access "Allow" --priority 306 --direction "Inbound"
> ```

## <a id="example-dns"></a> Příklad: Konfigurace DNS

### <a name="name-resolution-between-a-virtual-network-and-a-connected-on-premises-network"></a>Překlad mezi virtuální sítí a připojené v místní síti

Tento příklad vytvoří následující předpoklady:

* Máte virtuální síť Azure, které je připojené k místní síti pomocí brány VPN.

* Vlastní server DNS ve virtuální síti se systémem Linux nebo Unix jako operační systém.

* [Vytvoření vazby](https://www.isc.org/downloads/bind/) je nainstalovaný na vlastní server DNS.

Na vlastní server DNS ve virtuální síti:

1. Najít příponu DNS virtuální sítě pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure Classic:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Na vlastní server DNS pro virtuální síť, použijte následující text jako obsah `/etc/bind/named.conf.local` souboru:

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {168.63.129.16;}; # Azure recursive resolver
    };
    ```

    Nahraďte `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` hodnotu s příponou DNS virtuální sítě.

    Tato konfigurace směruje všechny žádosti DNS pro příponu DNS virtuální sítě do Azure rekurzivní překladač.

2. Na vlastní server DNS pro virtuální síť, použijte následující text jako obsah `/etc/bind/named.conf.options` souboru:

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
    
    * Nahraďte `10.0.0.0/16` hodnotu s rozsahem IP adres vaší virtuální sítě. Tato položka umožňuje název řešení požadavků adresy v tomto rozsahu.

    * Přidat rozsah IP adres místní sítě, aby `acl goodclients { ... }` oddílu.  položka umožňuje požadavky na název řešení z prostředků v místní síti.
    
    * Nahraďte hodnotu `192.168.0.1` IP adresou vašeho místního serveru DNS. Tato položka všech ostatních požadavků na DNS směruje na místní server DNS.

3. Pokud chcete použít konfiguraci, restartujte vazby. Například, `sudo service bind9 restart`.

4. Přidáte podmíněné předávání do místního serveru DNS. Konfigurace pro podmíněné předávání na odesílání žádostí pro příponu DNS z kroku 1 na vlastní server DNS.

    > [!NOTE]
    > V dokumentaci softwaru pro konkrétní o tom, jak přidat pro podmíněné předávání DNS.

Po dokončení těchto kroků se můžete připojit k prostředkům v obou sítích pomocí plně kvalifikované názvy domény (FQDN). HDInsight teď můžete nainstalovat do virtuální sítě.

### <a name="name-resolution-between-two-connected-virtual-networks"></a>Překlad mezi dvěma propojenými virtuálními sítěmi

Tento příklad vytvoří následující předpoklady:

* Máte dvě virtuální sítě Azure, které jsou připojené pomocí brány sítě VPN nebo partnerského vztahu.

* Vlastní server DNS v obou sítích se systémem Linux nebo Unix jako operační systém.

* [Vytvoření vazby](https://www.isc.org/downloads/bind/) je nainstalován na vlastních serverech DNS.

1. Najít příponu DNS obě virtuální sítě pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure Classic:

    ```powershell
    $resourceGroupName = Read-Input -Prompt "Enter the resource group that contains the virtual network used with HDInsight"
    $NICs = Get-AzureRmNetworkInterface -ResourceGroupName $resourceGroupName
    $NICs[0].DnsSettings.InternalDomainNameSuffix
    ```

    ```azurecli-interactive
    read -p "Enter the name of the resource group that contains the virtual network: " RESOURCEGROUP
    az network nic list --resource-group $RESOURCEGROUP --query "[0].dnsSettings.internalDomainNameSuffix"
    ```

2. Použijte následující text jako obsah `/etc/bind/named.config.local` souboru na vlastní server DNS. Provedení této změny na vlastní server DNS v obou virtuálních sítích.

    ```
    // Forward requests for the virtual network suffix to Azure recursive resolver
    zone "0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net" {
        type forward;
        forwarders {10.0.0.4;}; # The IP address of the DNS server in the other virtual network
    };
    ```

    Nahradit `0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net` hodnotu přípony DNS __jiných__ virtuální sítě. Tato položka směruje požadavky pro příponu DNS vzdálené sítě na vlastní DNS v dané síti.

3. Na vlastní servery DNS v obou virtuálních sítích, použijte následující text jako obsah `/etc/bind/named.conf.options` souboru:

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
    
    * Nahradit `10.0.0.0/16` a `10.1.0.0/16` hodnot pomocí IP adresy rozsahů virtuálních sítí. Tato položka umožňuje prostředky v každé sítě k podání žádostí o serverů DNS.

    Všechny žádosti, které nejsou určené pro přípony DNS virtuální sítě (např. microsoft.com) zařizuje služba Azure rekurzivní překladač.

4. Pokud chcete použít konfiguraci, restartujte vazby. Například `sudo service bind9 restart` na obou serverech DNS.

Po dokončení těchto kroků se můžete připojit k prostředkům ve virtuální síti pomocí plně kvalifikované názvy domény (FQDN). HDInsight teď můžete nainstalovat do virtuální sítě.

## <a name="next-steps"></a>Další postup

* Příklad začátku do konce konfigurace HDInsight pro připojení k místní síti, najdete v části [HDInsight připojit k místní síti](./connect-on-premises-network.md).
* Konfigurace clusterů Hbase ve virtuálních sítích Azure, najdete v části [vytváření clusterů HBase v HDInsight ve virtuální síti Azure](hbase/apache-hbase-provision-vnet.md).
* Konfigurace geografické replikace HBase, naleznete v tématu [nastavení replikace clusteru HBase ve virtuálních sítích Azure](hbase/apache-hbase-replication.md).
* Další informace o virtuálních sítích Azure najdete v článku [Přehled služby Azure Virtual Network](../virtual-network/virtual-networks-overview.md).

* Další informace o skupinách zabezpečení sítě najdete v tématu [skupiny zabezpečení sítě](../virtual-network/security-overview.md).

* Další informace o trasách definovaných uživatelem najdete v tématu [trasy definované uživatelem a předávání IP](../virtual-network/virtual-networks-udr-overview.md).

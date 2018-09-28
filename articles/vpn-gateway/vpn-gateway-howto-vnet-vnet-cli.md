---
title: 'Propojení virtuální sítě s jinou virtuální síti s použitím připojení typu VNet-to-VNet: Azure CLI | Dokumentace Microsoftu'
description: Propojení virtuálních sítí s použitím připojení typu VNet-to-VNet a Azure CLI.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: jpconnock
editor: ''
tags: azure-resource-manager
ms.assetid: 0683c664-9c03-40a4-b198-a6529bf1ce8b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/14/2018
ms.author: cherylmc
ms.openlocfilehash: 2fc25235325db8a403c2b258dd5e4b3effc46ace
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971956"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Konfigurace připojení brány VPN typu VNet-to-VNet pomocí Azure CLI

Tento článek vám pomůže propojit virtuální sítě s použitím typu připojení VNet-to-VNet. Virtuální sítě se můžou nacházet ve stejné oblasti nebo v různých oblastech a můžou patřit do stejného předplatného nebo do různých předplatných. Pokud připojujete virtuální sítě z různých předplatných, tato předplatná nemusí být přidružená ke stejnému tenantovi Active Directory.

Postupy v tomto článku se týkají modelu nasazení Resource Manager a používají Azure CLI. Tuto konfiguraci můžete vytvořit také pomocí jiného nástroje nasazení nebo pro jiný model nasazení, a to výběrem jiné možnosti z následujícího seznamu:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (Classic)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Propojení různých modelů nasazení – Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Propojení různých modelů nasazení – PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about"></a>Informace o propojování virtuálních sítí

Existuje více způsobů propojení virtuálních sítí. Následující oddíly popisují různé způsoby připojení virtuálních sítí.

### <a name="vnet-to-vnet"></a>VNet-to-VNet

Konfigurace připojení VNet-to-VNet je dobrým způsobem, jak snadno připojit virtuální sítě. Propojení virtuální sítě s jinou virtuální sítí s použitím typu připojení VNet-to-VNet je podobné jako vytvoření připojení Site-to-Site IPsec k místnímu umístění. Oba typy připojení využívají bránu VPN k poskytnutí zabezpečeného tunelového propojení prostřednictvím protokolu IPsec/IKE a oba komunikují stejným způsobem. Rozdílem mezi těmito typy připojení je způsob konfigurace místní síťové brány. Při vytváření připojení typu VNet-to-VNet se nezobrazí adresní prostor místní síťové brány. Vytvoří a naplní se automaticky. Pokud aktualizujete adresní prostor pro jednu virtuální síť, druhá virtuální síť bude automaticky znát trasu do aktualizovaného adresního prostoru. Vytvoření připojení VNet-to-VNet je obvykle rychlejší a snazší než vytvoření připojení Site-to-Site mezi virtuálními sítěmi.

### <a name="connecting-vnets-using-site-to-site-ipsec-steps"></a>Připojení virtuálních sítí pomocí kroků Site-to-Site (IPsec)

Pokud pracujete se složitou konfigurací sítě, můžete chtít vaše virtuální sítě raději připojit pomocí kroků [Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-cli.md) než pomocí kroků VNet-to-VNet. Při použití kroků Site-to-Site vytvoříte a nakonfigurujete brány místní sítě ručně. Brána místní sítě pro každou virtuální síť zpracovává jiné virtuální sítě jako místní síť. Ten vám umožní pro místní síťovou bránu zadat další adresní prostor pro směrování provozu. Pokud se adresní prostor pro virtuální síť změní, musíte ručně aktualizovat odpovídající bránu místní sítě tak, aby tuto změnu odrážela. Nedojde k automatické aktualizaci.

### <a name="vnet-peering"></a>Partnerské vztahy virtuálních sítí

Můžete chtít zvážit připojení vašich virtuálních sítí pomocí VNET Peering. VNET Peering nepoužívá bránu sítě VPN a má jiná omezení. Kromě toho [ceny pro VNET Peering](https://azure.microsoft.com/pricing/details/virtual-network) se vypočítávají odlišně než [ceny pro VNet-to-VNet VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Další informace najdete v tématu [Partnerské vztahy virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

## <a name="why"></a>Proč vytvářet připojení typu VNet-to-VNet?

Virtuální sítě může být vhodné propojit pomocí připojení VNet-to-VNet z následujících důvodů:

* **Geografická redundance napříč oblastmi a geografická přítomnost**

  * Můžete nastavit vlastní geografickou replikaci nebo synchronizaci se zabezpečeným připojením bez procházení koncovými body připojenými k internetu.
  * Pomocí Azure Traffic Manageru a služby Load Balancer je možné vytvářet úlohy s vysokou dostupností s geografickou redundancí nad několika oblastmi Azure. Jedním z důležitých příkladů je nastavení technologie SQL Always On se skupinami dostupnosti nad několika oblastmi Azure.
* **Regionální vícevrstvé aplikace s izolací nebo administrativní hranicí**

  * V rámci stejné oblasti můžete vytvářet vícevrstvé aplikace s několika virtuálními sítěmi propojenými z důvodu izolace nebo požadavků na správu.

Komunikaci typu VNet-to-VNet můžete kombinovat s konfiguracemi s více servery. Díky tomu je možné vytvářet topologie sítí, ve kterých se používá propojování více míst i propojování virtuálních sítí.

## <a name="steps"></a>Které kroky VNet-to-VNet mám použít?

V tomto článku uvidíte dvě různé sady kroků připojení VNet-to-VNet. Jedna sada kroků pro [virtuální sítě patřící do stejného předplatného](#samesub) a jedna pro [virtuální sítě patřící do různých předplatných](#difsub). 

Pro toto cvičení můžete konfigurace kombinovat nebo prostě vybrat tu, se kterou chcete pracovat. Všechny konfigurace používají typ připojení VNet-to-VNet. Provoz probíhá mezi virtuálními sítěmi, které jsou vzájemně přímo propojené. V tomto cvičení se provoz ze sítě TestVNet4 nesměruje do sítě TestVNet5.

* [Virtuální sítě patřící do stejného předplatného:](#samesub) V postupu pro tuto konfiguraci se používají sítě TestVNet1 a TestVNet4.

  ![Diagram v2v](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

* [Virtuální sítě patřící do různých předplatných:](#difsub) V postupu pro tuto konfiguraci se používají sítě TestVNet1 a TestVNet5.

  ![Diagram v2v](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)


## <a name="samesub"></a>Propojení virtuálních sítí patřících ke stejnému předplatnému

### <a name="before-you-begin"></a>Než začnete

Než začnete, nainstalujte si nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématu [Instalace Azure CLI](/cli/azure/install-azure-cli).

### <a name="Plan"></a>Plánování rozsahů IP adres

V následujících krocích vytvoříte dvě virtuální sítě spolu s příslušnými podsítěmi a konfiguracemi brány. Potom vytvoříte propojení VPN mezi oběma virtuálními sítěmi. Je důležité určit rozsahy IP adres pro konfiguraci vaší sítě. Mějte na paměti, že je třeba zajistit, aby se žádné rozsahy virtuálních sítí ani místní síťové rozsahy žádným způsobem nepřekrývaly. V těchto příkladech nezahrnujeme server DNS. Pokud chcete překlad IP adres pro virtuální sítě, přečtěte si téma [Překlad IP adres](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

V příkladech používáme následující hodnoty:

**Hodnoty pro virtuální síť TestVNet1:**

* Název virtuální sítě: TestVNet1
* Skupina prostředků: TestRG1
* Umístění: Východní USA
* TestVNet1: 10.11.0.0/16 a 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* Název brány: VNet1GW
* Veřejná IP adresa: VNet1GWIP
* Typ sítě VPN: RouteBased
* Připojení (1 ke 4): VNet1toVNet4
* Připojení (1 k 5): VNet1toVNet5 (pro virtuální sítě v různých předplatných)

**Hodnoty pro virtuální síť TestVNet4:**

* Název virtuální sítě: TestVNet4
* TestVNet2: 10.41.0.0/16 a 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Skupina prostředků: TestRG4
* Umístění: Západní USA
* Název brány: VNet4GW
* Veřejná IP adresa: VNet4GWIP
* Typ sítě VPN: RouteBased
* Připojení: VNet4toVNet1

### <a name="Connect"></a>Krok 1: Připojení k vašemu předplatnému

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="TestVNet1"></a>Krok 2: Vytvoření a konfigurace virtuální sítě TestVNet1

1. Vytvořte skupinu prostředků.

  ```azurecli
  az group create -n TestRG1  -l eastus
  ```
2. Vytvořte virtuální síť TestVNet1 a její podsítě. Tento příklad vytvoří virtuální síť TestVNet1 a podsíť FrontEnd.

  ```azurecli
  az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
  ```
3. Vytvořte další adresní prostor pro podsíť back-endu. Všimněte si, že v tomto kroku zadáváme jak adresní prostor, který jsme vytvořili dříve, tak i další adresní prostor, který chceme přidat. Důvodem je, že příkaz [az network vnet update](https://docs.microsoft.com/cli/azure/network/vnet#az_network_vnet_update) přepíše předchozí nastavení. Nezapomeňte při použití tohoto příkazu zadat všechny předpony adres.

  ```azurecli
  az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestRG1
  ```
4. Vytvořte podsíť back-endu.
  
  ```azurecli
  az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestRG1 --address-prefix 10.12.0.0/24 
  ```
5. Vytvořte podsíť brány. Všimněte si, že podsíť brány má název GatewaySubnet. Název je povinný. V příkladu používá podsíť brány možnost /27. I když je možné vytvořit podsíť brány s minimální velikostí /29, doporučujeme vytvořit větší podsíť, která pojme více adres, tzn. vybrat velikost alespoň /28 nebo /27. Tím vznikne dostatečný prostor pro adresy, který umožní nastavení případných dalších konfigurací v budoucnu.

  ```azurecli 
  az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestRG1 --address-prefix 10.12.255.0/27
  ```
6. Vyžádejte si veřejnou IP adresu, která bude přidělena bráně, kterou vytvoříte pro příslušnou virtuální síť. Všimněte si, že metoda AllocationMethod je dynamická. Není možné určit IP adresu, kterou chcete používat. Přiděluje se pro bránu dynamicky.

  ```azurecli
  az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
  ```
7. Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Konfigurace propojení VNet-to-VNet vyžadují typ sítě VPN RouteBased. Pokud tento příkaz spustíte s použitím parametru --no-wait, nezobrazí se žádná zpětná vazba ani výstup. Parametr --no-wait umožňuje bránu vytvořit na pozadí. Neznamená to, že se brána VPN vytvoří okamžitě. Vytvoření brány může obvykle trvat 45 minut nebo déle, a to v závislosti na použité skladové jednotce (SKU) brány.

  ```azurecli
  az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="TestVNet4"></a>Krok 3: Vytvoření a konfigurace virtuální sítě TestVNet4

1. Vytvořte skupinu prostředků.

  ```azurecli
  az group create -n TestRG4  -l westus
  ```
2. Vytvořte virtuální síť TestVNet4.

  ```azurecli
  az network vnet create -n TestVNet4 -g TestRG4 --address-prefix 10.41.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.41.0.0/24
  ```

3. Vytvořte další podsítě pro virtuální síť TestVNet4.

  ```azurecli
  az network vnet update -n TestVNet4 --address-prefixes 10.41.0.0/16 10.42.0.0/16 -g TestRG4 
  az network vnet subnet create --vnet-name TestVNet4 -n BackEnd -g TestRG4 --address-prefix 10.42.0.0/24 
  ```
4. Vytvořte podsíť brány.

  ```azurecli
   az network vnet subnet create --vnet-name TestVNet4 -n GatewaySubnet -g TestRG4 --address-prefix 10.42.255.0/27
  ```
5. Vyžádejte si veřejnou IP adresu.

  ```azurecli
  az network public-ip create -n VNet4GWIP -g TestRG4 --allocation-method Dynamic
  ```
6. Vytvořte bránu virtuální sítě TestVNet4.

  ```azurecli
  az network vnet-gateway create -n VNet4GW -l westus --public-ip-address VNet4GWIP -g TestRG4 --vnet TestVNet4 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="createconnect"></a>Krok 4: Vytvoření připojení

Nyní máte dvě virtuální sítě s bránami VPN. Dalším krokem je vytvoření propojení bran VPN mezi bránami virtuálních sítí. Pokud jste použili výše uvedené příklady, vaše brány virtuálních sítí jsou v různých skupinách prostředků. Když jsou brány v různých skupinách prostředků, musíte při vytváření propojení identifikovat a zadat ID prostředků pro každou bránu. Pokud jsou vaše virtuální sítě ve stejné skupině prostředků, můžete použít [druhou sadu pokynů](#samerg), protože nemusíte zadávat ID prostředků.

### <a name="diffrg"></a>Propojení virtuálních sítí patřících do různých skupin prostředků

1. Z výstupu následujícího příkazu získejte ID prostředku brány VNet1GW:

  ```azurecli
  az network vnet-gateway show -n VNet1GW -g TestRG1
  ```

  Ve výstupu vyhledejte řádek, který začíná na "id:". Hodnoty v uvozovkách budete potřebovat pro vytvoření propojení v další části. Zkopírujte tyto hodnoty do textového editoru, jako je Poznámkový blok, abyste je při vytváření propojení mohli jednoduše vložit.

  Příklad výstupu:

  ```
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65515, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
   }, 
  "enableBgp": false, 
  "etag": "W/\"ecb42bc5-c176-44e1-802f-b0ce2962ac04\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW", 
  "ipConfigurations":
  ```

  Zkopírujte hodnoty v uvozovkách následující po **"id":**.

  ```
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
 ```

2. Získejte ID prostředku brány VNet4GW a zkopírujte hodnoty do textového editoru.

  ```azurecli
  az network vnet-gateway show -n VNet4GW -g TestRG4
  ```

3. Vytvořte připojení virtuální sítě TestVNet1 k virtuální síti TestVNet4. V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 do virtuální sítě TestVNet4. V příkladech se uvádí sdílený klíč. Pro sdílený klíč můžete použít vlastní hodnoty. Důležité je, že se sdílený klíč pro obě připojení musí shodovat. Vytvoření připojení nějakou dobu trvá.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW 
  ```
4. Vytvořte připojení virtuální sítě TestVNet4 k virtuální síti TestVNet1. Tento krok je podobný předchozímu, vytváříte však připojení z virtuální sítě TestVNet4 do virtuální sítě TestVNet1. Ověřte, že se sdílené klíče shodují. Navázání připojení trvá několik minut.

  ```azurecli
  az network vpn-connection create -n VNet4ToVNet1 -g TestRG4 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW -l westus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1G
  ```
5. Ověřte stav připojení. Viz [Ověření stavu připojení](#verify).

### <a name="samerg"></a>Propojení virtuálních sítí patřících do stejné skupiny prostředků

1. Vytvořte připojení virtuální sítě TestVNet1 k virtuální síti TestVNet4. V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 do virtuální sítě TestVNet4. Všimněte si, že skupiny prostředků v příkladech jsou stejné. V příkladech také vidíte uvedený sdílený klíč. Pro sdílený klíč můžete použít vlastní hodnoty, ale sdílené klíče pro obě připojení se musí shodovat. Vytvoření připojení nějakou dobu trvá.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
  ```
2. Vytvořte připojení virtuální sítě TestVNet4 k virtuální síti TestVNet1. Tento krok je podobný předchozímu, vytváříte však připojení z virtuální sítě TestVNet4 do virtuální sítě TestVNet1. Ověřte, že se sdílené klíče shodují. Navázání připojení trvá několik minut.

  ```azurecli
  az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
  ```
3. Ověřte stav připojení. Viz [Ověření stavu připojení](#verify).

## <a name="difsub"></a>Propojení virtuálních sítí patřících k různým předplatným

V tomto scénáři propojíte sítě TestVNet1 a TestVNet5. Virtuální sítě patří k různým předplatným. Předplatná nemusí být přidružená ke stejnému tenantovi Active Directory. Tento postup přidá nové propojení VNet-to-VNet pro připojení virtuální sítě TestVNet1 k virtuální síti TestVNet5.

### <a name="TestVNet1diff"></a>Krok 5: Vytvoření a konfigurace virtuální sítě TestVNet1

Tyto pokyny navazují na kroky v předchozích částech. Je třeba vytvořit a konfigurovat virtuální síť TestVNet1 a bránu VPN pro virtuální síť TestVNet1 provedením [kroku 1](#Connect) a [kroku 2](#TestVNet1). Pro tuto konfiguraci není nutné vytvářet virtuální síť TestVNet4 z předchozí části, ale pokud ji vytvoříte, nebude to s těmito kroky v konfliktu. Po dokončení kroků 1 a 2 pokračujte krokem 6 (níže).

### <a name="verifyranges"></a>Krok 6: Ověření rozsahů IP adres

Při vytváření dalších připojení je důležité ověřit, že se adresní prostor IP adres nové virtuální sítě nepřekrývá se žádným z rozsahů jiných virtuálních sítí ani rozsahů bran místních sítí. Pro tento postup použijte následující hodnoty pro virtuální síť TestVNet5:

**Hodnoty pro virtuální síť TestVNet5:**

* Název virtuální sítě: TestVNet5
* Skupina prostředků: TestRG5
* Umístění: Japonsko – východ
* TestVNet5: 10.51.0.0/16 a 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* Název brány: VNet5GW
* Veřejná IP adresa: VNet5GWIP
* Typ sítě VPN: RouteBased
* Připojení: VNet5toVNet1
* Typ připojení: VNet2VNet

### <a name="TestVNet5"></a>Krok 7: Vytvoření a konfigurace virtuální sítě TestVNet5

Tento krok je třeba provést v rámci nového předplatného (předplatné 5). Tuto část může provést správce v organizaci, která je vlastníkem druhého předplatného. Pro přepínání mezi předplatnými použijte příkaz „az account list --all“, který vypíše dostupná předplatná pro váš účet, a pak pomocí příkazu „az account set --subscription <subscriptionID>“ přepněte na předplatné, které chcete použít.

1. Ujistěte se, že jste připojeni k předplatnému 5, a pak vytvořte skupinu prostředků.

  ```azurecli
  az group create -n TestRG5  -l japaneast
  ```
2. Vytvořte virtuální síť TestVNet5.

  ```azurecli
  az network vnet create -n TestVNet5 -g TestRG5 --address-prefix 10.51.0.0/16 -l japaneast --subnet-name FrontEnd --subnet-prefix 10.51.0.0/24
  ```

3. Přidejte podsítě.

  ```azurecli
  az network vnet update -n TestVNet5 --address-prefixes 10.51.0.0/16 10.52.0.0/16 -g TestRG5
  az network vnet subnet create --vnet-name TestVNet5 -n BackEnd -g TestRG5 --address-prefix 10.52.0.0/24
  ```

4. Přidejte podsíť brány.

  ```azurecli
  az network vnet subnet create --vnet-name TestVNet5 -n GatewaySubnet -g TestRG5 --address-prefix 10.52.255.0/27
  ```

5. Vyžádejte si veřejnou IP adresu.

  ```azurecli
  az network public-ip create -n VNet5GWIP -g TestRG5 --allocation-method Dynamic
  ```
6. Vytvoření brány virtuální sítě TestVNet5

  ```azurecli
  az network vnet-gateway create -n VNet5GW -l japaneast --public-ip-address VNet5GWIP -g TestRG5 --vnet TestVNet5 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
  ```

### <a name="connections5"></a>Krok 8: Vytvoření připojení

Vzhledem k tomu, že brány patří do různých předplatných, je tento krok rozdělený do dvou relací rozhraní příkazového řádku označených jako **[Předplatné 1]** a **[Předplatné 5]**. Pro přepínání mezi předplatnými použijte příkaz „az account list --all“, který vypíše dostupná předplatná pro váš účet, a pak pomocí příkazu „az account set --subscription <subscriptionID>“ přepněte na předplatné, které chcete použít.

1. **[Předplatné 1]** Přihlaste a připojte se k předplatnému 1. Spusťte následující příkaz a z výstupu získejte název a ID brány:

  ```azurecli
  az network vnet-gateway show -n VNet1GW -g TestRG1
  ```

  Zkopírujte část výstupu uvedeného textem „id:“. E-mailem nebo jiným způsobem odešlete ID a název brány virtuální sítě (VNet1GW) správci předplatného 5.

  Příklad výstupu:

  ```
  "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
  ```

2. **[Předplatné 5]** Přihlaste a připojte se k předplatnému 5. Spusťte následující příkaz a z výstupu získejte název a ID brány:

  ```azurecli
  az network vnet-gateway show -n VNet5GW -g TestRG5
  ```

  Zkopírujte část výstupu uvedeného textem „id:“. E-mailem nebo jiným způsobem odešlete ID a název brány virtuální sítě (VNet5GW) správci předplatného 1.

3. **[Předplatné 1]** V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 k virtuální síti TestVNet5. Pro sdílený klíč můžete použít vlastní hodnoty, ale sdílené klíče pro obě připojení se musí shodovat. Vytvoření připojení může nějakou dobu trvat. Ujistěte se, že jste připojeni k předplatnému 1.

  ```azurecli
  az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
  ```

4. **[Předplatné 5]** Tento krok je podobný předchozímu, vytváříte však připojení z virtuální sítě TestVNet5 k virtuální síti TestVNet1. Zkontrolujte, že se sdílené klíče shodují a že se připojujete k předplatnému 5.

  ```azurecli
  az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
  ```

## <a name="verify"></a>Ověření stavu připojení
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="faq"></a>Nejčastější dotazy týkající se propojení VNet-to-VNet
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Další kroky

* Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Další informace najdete v [dokumentaci ke službě Virtual Machines](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
* Informace o protokolu BGP najdete v tématech [Přehled protokolu BGP](vpn-gateway-bgp-overview.md) a [Postup při konfiguraci protokolu BGP](vpn-gateway-bgp-resource-manager-ps.md).
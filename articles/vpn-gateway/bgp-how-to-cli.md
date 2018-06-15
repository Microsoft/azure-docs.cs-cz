---
title: 'Konfigurace protokolu BGP na bránu VPN Azure VPN: Resource Manager a rozhraní příkazového řádku | Microsoft Docs'
description: Tento článek vás provede procesem konfigurace protokolu BGP pomocí služby Azure VPN gateway pomocí Azure Resource Manager a rozhraní příkazového řádku.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.openlocfilehash: 98cd606ce930624ec5c591ffd8f13e0feae1a6c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884038"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Postup konfigurace protokolu BGP na služby Azure VPN gateway pomocí rozhraní příkazového řádku

Tento článek vám pomůže aktivovat BGP na připojení Site-to-Site (S2S) VPN mezi různými místy a připojení VNet-to-VNet (to znamená, připojení mezi virtuálními sítěmi) pomocí modelu nasazení Azure Resource Manager a rozhraní příkazového řádku Azure.

## <a name="about-bgp"></a>Informace o protokolu BGP

BGP je standardní směrovací protokol běžně používaných na Internetu k výměně informací o směrování a dostupnosti mezi dvěma nebo více sítěmi. Protokol BGP umožňuje brány sítě VPN a vaše místní zařízení VPN, nazývají partnerské uzly protokolu BGP nebo Sousedé BGP, pro výměnu směrování. Trasy informovat obě brány o dostupnosti a dosažitelnosti předpon projít těmito bránami nebo trasami související se situací. Protokol BGP můžete také povolit směrování přenosu mezi více sítěmi pomocí šíření tras, které brána s protokolem BGP zjistí od jednoho partnerského uzlu protokolu BGP, ostatním partnerům BGP.

Další informace o výhodách protokolu BGP a pochopit technickými požadavky a důležité informace o použití protokolu BGP najdete v tématu [přehled protokolu BGP pomocí bran Azure VPN](vpn-gateway-bgp-overview.md).

Tento článek vám pomůže s následující úlohy:

* [Povolit protokol BGP pro bránu VPN](#enablebgp) (povinné)

  Pak můžete použít jednu z těchto částí nebo obě:

* [Připojení mezi různými místy pomocí protokolu BGP](#crossprembgp)
* [Připojení VNet-to-VNet s protokolem BGP](#v2vbgp)

Každý z těchto tří částí tvoří základní stavební blok pro povolení protokolu BGP v připojení k síti. Pokud dokončíte všechny tři části, jak je znázorněno v následujícím diagramu sestavení topologie:

![Topologii BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Můžete kombinovat tyto části k vytvoření složitějších vícenásobným tranzitní sítě, která vyhovuje vašim potřebám.

## <a name ="enablebgp"></a>Povolit protokol BGP pro bránu sítě VPN

Tento oddíl je povinný, před provedením kroků v dalších částech dvě konfigurace. Následující kroky konfigurace nastavit parametry protokolu BGP brána Azure VPN, jak je znázorněno v následujícím diagramu:

![Protokol BGP brány](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Než začnete

Nainstalujte nejnovější verzi rozhraní příkazového řádku příkazy (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI 2.0](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Krok 1: Vytvoření a konfigurace virtuální sítě TestVNet1

#### <a name="Login"></a>1. Připojení k vašemu předplatnému

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Vytvoření skupiny prostředků

Následující příklad vytvoří skupinu prostředků s názvem TestRG1 v umístění "eastus". Pokud už máte skupinu prostředků v oblasti, kde chcete vytvořit virtuální síť, můžete použít než místo.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Vytvoření virtuální sítě TestVNet1

Následující příklad vytvoří virtuální síť s názvem TestVNet1 a tři podsítě: GatewaySubnet, front-endové a back-end. Při nahrazování hodnot je důležité vždy název podsítě brány GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

První příkaz vytvoří front-end adresní prostor a podsítě front-endu. Druhý příkaz vytvoří další adresní prostor podsítě back-end. Příkazy třetí a čtvrtý vytvořit podsíť back-end a GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2: Vytvoření služby VPN gateway pro virtuální síť TestVNet1 s parametry protokolu BGP

#### <a name="1-create-the-public-ip-address"></a>1. Vytvoření veřejné IP adresy

Vyžádejte si veřejnou IP adresu. Ke službě VPN gateway, které vytvoříte pro vaši virtuální síť se přidělí veřejnou IP adresu.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Vytvoření brány VPN s číslo AS

Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Protokol BGP vyžaduje brány VPN založené na trasách. Musíte taky další parametr `-Asn` nastavit číslo autonomního systému (ASN) pro virtuální síť TestVNet1. Vytvoření brány může chvíli trvat (45 minut nebo déle) k dokončení. 

Pokud spustíte tento příkaz pomocí `--no-wait` parametr nevidíte žádné zpětnou vazbu nebo výstup. `--no-wait` Parametr povoluje bránu vytvořit na pozadí. Neznamená to okamžitě vytvoření brány VPN.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Získat IP adresu partnera Azure BGP

Po vytvoření brány, budete muset získat IP adresu na bráně Azure VPN partnerský uzel protokolu BGP. Tato adresa je potřeba ke konfiguraci brány VPN jako partnerské zařízení protokolu BGP pro vaše místní zařízení VPN.

Spusťte následující příkaz a zkontrolujte `bgpSettings` část v horní části výstup:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Po vytvoření brány, můžete tuto bránu k navázání připojení mezi různými místy nebo připojení VNet-to-VNet s protokolem BGP.

## <a name ="crossprembgp"></a>Připojení mezi různými místy pomocí protokolu BGP

Navázat připojení mezi různými místy, musíte vytvořit bránu místní sítě představují vaše místní zařízení VPN. Brána Azure VPN se potom připojují k bráně místní sítě. I když tyto kroky jsou podobné vytvoření další připojení, obsahují další vlastnosti, které jsou zapotřebí zadat parametry konfigurace protokolu BGP.

![Protokol BGP pro více míst](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Krok 1: Vytvoření a konfigurace brány místní sítě

Toto cvičení i nadále sestavení konfigurace znázorněné na obrázku. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci. Při práci s brány místní sítě, mějte na paměti následující věci:

* Brána místní sítě může být ve stejném umístění a skupině prostředků jako bránu sítě VPN, nebo může být v jiné umístění a skupině prostředků. Tento příklad ukazuje brány v různých skupinách prostředků v různých umístěních.
* Minimální předponu, která je třeba deklarovat pro bránu místní sítě je adresa hostitele BGP IP adresy partnerského uzlu ve vašem zařízení VPN. V takovém případě je /32 předpona 10.52.255.254/32.
* Připomínáme je nutné použít různá čísla ASN protokolu BGP mezi místními sítěmi a virtuální síť Azure. Pokud se shodují, budete muset změnit číslo ASN vaší virtuální sítě, pokud vaše místní zařízení VPN protokol číslo Autonomního rovnocenných počítačů sousední ostatní směrovače BGP.

Než budete pokračovat, ujistěte se, že jste dokončili [povolit protokol BGP pro bránu VPN](#enablebgp) části tohoto cvičení a že jste stále připojeni k předplatnému 1. Všimněte si, že v tomto příkladu vytvoříte novou skupinu prostředků. Všimněte si také, dva další parametry pro bránu místní sítě: `Asn` a `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2: Připojení brány virtuální sítě a brány místní sítě

V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 k Site5. Je nutné zadat `--enable-bgp` parametr, pokud chcete povolit protokol BGP pro toto připojení. 

V tomto příkladu brány virtuální sítě a brány místní sítě jsou v různých skupinách prostředků. Když jsou brány v různých skupinách prostředků, je nutné zadat ID prostředku celý dvě brány nastavit připojení mezi virtuálními sítěmi.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Získat prostředek ID VNet1GW

Použijte následující příkaz z získat ID prostředku pro VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Ve výstupu najít `"id":` řádku. Je nutné hodnoty v uvozovkách k vytvoření připojení v další části.

Příklad výstupu:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Zkopírujte hodnoty po `"id":` do textového editoru, například Poznámkový blok, takže můžete snadno vložit je při vytváření připojení. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Získat prostředek ID Site5

Chcete-li získat prostředek ID Site5 z výstupu použijte následující příkaz:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Vytvoření připojení virtuální sítě TestVNet1 Site5

V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 k Site5. Jak už jsme probírali výše, je možné, že připojení protokolu BGP i bez protokolu BGP pro tutéž bránu Azure VPN. Pokud je protokol BGP povolený ve vlastnosti připojení, Azure nebude povolit protokol BGP pro toto připojení, i když BGP parametry jsou již nakonfigurované na obě brány. ID odběru nahraďte vlastními.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Pro toto cvičení následující příklad uvádí parametry, zadejte v části Konfigurace protokolu BGP vašeho místního zařízení VPN:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Připojení by se mělo vytvořit během několika minut. Relaci partnerského vztahu protokolu BGP se spustí po navázání připojení protokolu IPsec.

## <a name ="v2vbgp"></a>Připojení VNet-to-VNet s protokolem BGP

Tato část přidá připojení VNet-to-VNet s protokolem BGP, jak je znázorněno v následujícím diagramu: 

![Protokol BGP pro síť VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Podle následujících pokynů pokračovat od kroky v předchozí části. Chcete-li vytvořit a nakonfigurovat virtuální síť TestVNet1 a bránu VPN s protokolem BGP, musíte provést [povolit protokol BGP pro bránu VPN](#enablebgp) části.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Krok 1: Vytvoření TestVNet2 a brány VPN

Je důležité se ujistit, že adresní prostor IP adres nové virtuální sítě, TestVNet2, nepřekrývá s žádným z rozsahů vaší virtuální sítě.

V tomto příkladu patří virtuální sítě ve stejném předplatném. Můžete nastavit připojení VNet-to-VNet mezi různých předplatných. Další informace najdete v tématu [konfigurace připojení typu VNet-to-VNet](vpn-gateway-howto-vnet-vnet-cli.md). Ujistěte se, že přidáte `-EnableBgp $True` při vytváření připojení k povolení protokolu BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Vytvoření nové skupiny prostředků

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Vytvoření TestVNet2 do nové skupiny prostředků

První příkaz vytvoří front-end adresní prostor a podsítě front-endu. Druhý příkaz vytvoří další adresní prostor podsítě back-end. Příkazy třetí a čtvrtý vytvořit podsíť back-end a GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Vytvoření veřejné IP adresy

Vyžádejte si veřejnou IP adresu. Ke službě VPN gateway, které vytvoříte pro vaši virtuální síť se přidělí veřejnou IP adresu.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Vytvoření brány VPN s číslo AS

Vytvoření brány virtuální sítě pro TestVNet2. Na Azure VPN Gateway je nutné přepsat výchozí číslo ASN. Čísla ASN pro připojené virtuální sítě se musí lišit povolit protokol BGP a směrování přenosu.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2: Připojení brány virtuální sítě TestVNet1 a TestVNet2

V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 k Site5. Chcete-li povolit protokol BGP pro toto připojení, musíte zadat `--enable-bgp` parametr.

V následujícím příkladu brány virtuální sítě a brány místní sítě jsou v různých skupinách prostředků. Když jsou brány v různých skupinách prostředků, je nutné zadat ID prostředku celý dvě brány nastavit připojení mezi virtuálními sítěmi. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Získat prostředek ID VNet1GW 

Získáte prostředek ID VNet1GW z výstupu následující příkaz:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Získat prostředek ID VNet2GW

Získáte prostředek ID VNet2GW z výstupu následující příkaz:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Vytvoření připojení

Vytvořte připojení z virtuální sítě TestVNet1 k TestVNet2 a připojení z TestVNet2 do virtuální sítě TestVNet1. ID odběru nahraďte vlastními.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Povolit protokol BGP pro *i* připojení.
> 
> 

Po dokončení těchto kroků, připojení bude vytvořeno za pár minut. Relaci partnerského vztahu protokolu BGP bude až po dokončení připojení VNet-to-VNet.

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Pokyny najdete v tématu [vytvořit virtuální počítač](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

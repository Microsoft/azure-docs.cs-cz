---
title: Konfigurace protokolu BGP v bráně Azure VPN:rozhraní příkazového příkazu
description: Tento článek vás provede konfigurací protokolu BGP s bránou Azure VPN pomocí Azure Resource Manager a CLI.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2018
ms.author: yushwang
ms.openlocfilehash: 42a07ac00fd8a26918164f6547bf57c2b021d14c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863610"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Jak nakonfigurovat bgp na bráně Azure VPN pomocí rozhraní příkazového příkazu

Tento článek vám pomůže povolit protokol BGP v mezimístním připojení množiny VPN mezi místními lokalitami (S2S) a připojení virtuální sítě k virtuální síti (tj. připojení mezi virtuálními sítěmi) pomocí modelu nasazení Azure Resource Manager u azure resource manageru a rozhraní příkazového příkazového příkazového příkazu Konto.

## <a name="about-bgp"></a>Informace o protokolu BGP

Protokol BGP je standardní směrovací protokol běžně používaný v Síti Internet k výměně informací o směrování a dosažitelnosti mezi dvěma nebo více sítěmi. Protokol BGP umožňuje privátním bránám VPN a místním zařízením VPN, nazývaným partnerské sítě BGP nebo sousedním uživatelům, vyměňovat si trasy. Trasy informují obě brány o dostupnosti a dosažitelnosti pro prefixy, které procházejí zúčastněnými branami nebo směrovači. Protokol BGP může také povolit směrování tranzitu mezi více sítěmi šířením tras, které se brána Protokolu BGP učí z jednoho partnera Protokolu BGP, do všech ostatních partnerů protokolu BGP.

Další informace o výhodách protokolu BGP a pochopení technických požadavků a aspektů používání protokolu BGP naleznete v [tématu Přehled protokolu BGP s branami azure vpn](vpn-gateway-bgp-overview.md).

Tento článek vám pomůže s následujícími úkoly:

* [Povolení protokolu BGP pro bránu VPN](#enablebgp) (povinné)

  Potom můžete dokončit jednu z následujících částí nebo obojí:

* [Navázat propojení mezi prostory s protokolem BGP](#crossprembgp)
* [Navázání připojení virtuální sítě k virtuální síti pomocí protokolu BGP](#v2vbgp)

Každá z těchto tří částí tvoří základní stavební blok pro povolení protokolu BGP v síťovém připojení. Pokud dokončíte všechny tři části, vytvoříte topologii, jak je znázorněno na následujícím diagramu:

![Topologie Protokolu BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Tyto oddíly můžete kombinovat a vytvořit tak složitější tranzitní síť s více kanály, která vyhovuje vašim potřebám.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>Povolení protokolu BGP pro bránu VPN

Tato část je vyžadována před provedením některého z kroků v dalších dvou částech konfigurace. Následující kroky konfigurace nastavit bgp parametry brány Azure VPN, jak je znázorněno na následujícím diagramu:

![Brána Protokolu BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Než začnete

Nainstalujte nejnovější verzi příkazů příkazu příkazu příkazu CLI (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Krok 1: Vytvoření a konfigurace testu VNet1

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Připojení k předplatnému

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Vytvoření skupiny prostředků

Následující příklad vytvoří skupinu prostředků s názvem TestRG1 v umístění "eastus". Pokud již máte skupinu prostředků v oblasti, kde chcete vytvořit virtuální síť, můžete použít, že jeden místo.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Vytvořit TestVNet1

Následující příklad vytvoří virtuální síť s názvem TestVNet1 a tři podsítě: GatewaySubnet, FrontEnd a BackEnd. Když nahradíte hodnoty, je důležité, abyste vždy pojmenovali podsíť brány specificky GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

První příkaz vytvoří adresní prostor front-endu a podsíť FrontEnd. Druhý příkaz vytvoří další adresní prostor pro podsíť Back-End. Třetí a čtvrtý příkaz y vytvářejí podsíť Back-End a GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2: Vytvoření brány VPN pro TestVNet1 s parametry Protokolu BGP

#### <a name="1-create-the-public-ip-address"></a>1. Vytvoření veřejné IP adresy

Vyžádejte si veřejnou IP adresu. Veřejná IP adresa bude přidělena bráně VPN, kterou vytvoříte pro virtuální síť.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Vytvořte bránu VPN s číslem AS

Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Protokol BGP vyžaduje bránu VPN založenou na trase. Potřebujete také další `-Asn` parametr pro nastavení čísla autonomního systému (ASN) pro TestVNet1. Vytvoření brány může chvíli trvat (45 minut nebo více) k dokončení. 

Pokud spustíte tento příkaz `--no-wait` pomocí parametru, nevidíte žádnou zpětnou vazbu ani výstup. Parametr `--no-wait` umožňuje vytvoření brány na pozadí. To neznamená, že brána VPN je vytvořena okamžitě.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Získání IP adresy partnerské strany Azure BGP

Po vytvoření brány je potřeba získat IP adresu partnerského zařízení BGP v bráně Azure VPN. Tato adresa je potřeba ke konfiguraci brány VPN jako partnerské sítě BGP pro vaše místní zařízení VPN.

Spusťte následující příkaz `bgpSettings` a zkontrolujte oddíl v horní části výstupu:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Po vytvoření brány můžete tuto bránu použít k navázání připojení mezi místními sítěmi nebo připojení k virtuální síti k virtuální síti s protokolem BGP.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Navázat propojení mezi prostory s protokolem BGP

Chcete-li vytvořit mezimístní připojení, musíte vytvořit bránu místní sítě, která bude reprezentovat vaše místní zařízení VPN. Pak připojíte bránu Azure VPN k bráně místní sítě. Přestože tyto kroky jsou podobné vytváření jiných připojení, obsahují další vlastnosti potřebné k určení parametrů konfigurace protokolu BGP.

![BGP pro víceprostorové](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Krok 1: Vytvoření a konfigurace brány místní sítě

Toto cvičení pokračuje v sestavení konfigurace uvedené v diagramu. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci. Při práci s bránami v místní síti mějte na paměti následující věci:

* Brána místní sítě může být ve stejném umístění a skupině prostředků jako brána VPN nebo může být v jiném umístění a skupině prostředků. Tento příklad ukazuje brány v různých skupinách prostředků v různých umístěních.
* Minimální předpona, kterou je třeba deklarovat pro bránu místní sítě, je adresa hostitele ip adresy partnerské strany Protokolu BGP v zařízení VPN. V tomto případě je to /32 předpona 10.51.255.254/32.
* Připomínáme, že musíte používat různé asn protokolu BGP mezi místními sítěmi a virtuální sítí Azure. Pokud jsou stejné, musíte změnit asn virtuální sítě, pokud vaše místní zařízení VPN už používají ASN k partnerské síti s ostatními sousedy Protokolu BGP.

Než budete pokračovat, ujistěte se, že jste v tomto cvičení dokončili část [Povolit bgp pro bránu VPN](#enablebgp) a že jste stále připojeni k předplatnému 1. Všimněte si, že v tomto příkladu vytvoříte novou skupinu prostředků. Všimněte si také dvou dalších parametrů `Asn` brány `BgpPeerAddress`místní sítě: a .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2: Připojení brány virtuální sítě a brány místní sítě

V tomto kroku vytvoříte připojení z TestVNet1 na Site5. Je nutné `--enable-bgp` zadat parametr pro povolení protokolu BGP pro toto připojení. 

V tomto příkladu jsou brána virtuální sítě a brána místní sítě v různých skupinách prostředků. Pokud jsou brány v různých skupinách prostředků, je nutné zadat celé ID prostředku dvou bran, abyste nastavili připojení mezi virtuálními sítěmi.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Získání ID prostředku v Síti VNet1GW

Pomocí výstupu z následujícího příkazu získat ID prostředku pro VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Ve výstupu najděte `"id":` řádek. K vytvoření připojení v další části potřebujete hodnoty v uvozovkách.

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

Zkopírujte hodnoty `"id":` poté do textového editoru, například poznámkový blok, abyste je mohli snadno vložit při vytváření připojení. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Získejte ID zdroje webu5

Pomocí následujícího příkazu získejte ID prostředku Site5 z výstupu:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Vytvoření připojení TestVNet1-to-Site5

V tomto kroku vytvoříte připojení z TestVNet1 na Site5. Jak již bylo popsáno dříve, je možné mít připojení BGP i non-BGP pro stejnou bránu Azure VPN. Pokud není ve vlastnosti připojení povoleno protokol BGP, Azure pro toto připojení nepovolí protokol BGP, přestože jsou parametry protokolu BGP již nakonfigurované v obou branách. Nahraďte ID předplatného vlastními.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Pro toto cvičení v následujícím příkladu jsou uvedeny parametry, které mají být uvedeny v části konfigurace protokolu BGP vašeho místního zařízení VPN:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Připojení by se mělo vytvořit během několika minut. Partnerská relace protokolu BGP se spustí po navázání připojení Protokolu IPsec.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Navázání připojení virtuální sítě k virtuální síti pomocí protokolu BGP

Tato část přidá připojení virtuální sítě k virtuální síti s protokolem BGP, jak je znázorněno na následujícím diagramu: 

![BGP pro virtuální síť k virtuální síti](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Následující pokyny pokračují z kroků v předchozích částech. Chcete-li vytvořit a nakonfigurovat TestVNet1 a bránu VPN pomocí protokolu BGP, musíte dokončit oddíl [Povolit protokol BGP pro bránu VPN.](#enablebgp)

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Krok 1: Vytvoření testu VNetu2 a brány VPN

Je důležité se ujistit, že IP adresní prostor nové virtuální sítě, TestVNet2, se nepřekrývá s žádným rozsahem virtuální sítě.

V tomto příkladu virtuální sítě patří do stejného předplatného. Můžete nastavit připojení virtuální sítě k virtuální síti mezi různými předplatnými. Další informace najdete [v tématu Konfigurace připojení virtuální sítě k virtuální síti](vpn-gateway-howto-vnet-vnet-cli.md). Ujistěte se, `-EnableBgp $True` že přidáte při vytváření připojení povolit BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Vytvoření nové skupiny prostředků

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Vytvoření testu VNetu2 v nové skupině prostředků

První příkaz vytvoří adresní prostor front-endu a podsíť FrontEnd. Druhý příkaz vytvoří další adresní prostor pro podsíť Back-End. Třetí a čtvrtý příkaz y vytvářejí podsíť Back-End a GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Vytvoření veřejné IP adresy

Vyžádejte si veřejnou IP adresu. Veřejná IP adresa bude přidělena bráně VPN, kterou vytvoříte pro virtuální síť.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Vytvořte bránu VPN s číslem AS

Vytvořte bránu virtuální sítě pro TestVNet2. Musíte přepsat výchozí ASN na vašich branách Azure VPN. Čísla ASN pro připojené virtuální sítě se musí lišit, aby bylo možné povolit protokol BGP a tranzitní směrování.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2: Připojení bran TestVNet1 a TestVNet2

V tomto kroku vytvoříte připojení z TestVNet1 na Site5. Chcete-li povolit protokol BGP `--enable-bgp` pro toto připojení, je nutné zadat parametr.

V následujícím příkladu jsou brána virtuální sítě a brána místní sítě v různých skupinách prostředků. Pokud jsou brány v různých skupinách prostředků, je nutné zadat celé ID prostředku dvou bran, abyste nastavili připojení mezi virtuálními sítěmi. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Získání ID prostředku v Síti VNet1GW 

Získejte ID prostředku VNet1GW z výstupu následujícího příkazu:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Získání ID prostředku v síti VNet2GW

Získejte ID prostředku VNet2GW z výstupu následujícího příkazu:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Vytvořte připojení

Vytvořte připojení z TestVNet1 na TestVNet2 a připojení z TestVNet2 na TestVNet1. Nahraďte ID předplatného vlastními.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Povolte protokol BGP pro *obě* připojení.
> 
> 

Po dokončení těchto kroků bude připojení navázáno během několika minut. Relace partnerského vztahu Protokolu BGP bude po dokončení připojení virtuální sítě k virtuální síti.

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete do virtuálních sítí přidat virtuální počítače. Postup najdete [v tématu Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

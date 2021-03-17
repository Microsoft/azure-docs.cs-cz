---
title: 'Konfigurace protokolu BGP v Azure VPN Gateway: CLI'
description: Tento článek vás provede konfigurací protokolu BGP s bránou Azure VPN Gateway pomocí Azure Resource Manager a CLI.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: a69ce0592b79be0868dd7c15ac054910eee75fc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89393594"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Postup konfigurace protokolu BGP v bráně Azure VPN pomocí rozhraní příkazového řádku

Tento článek vám pomůže povolit protokol BGP pro připojení VPN typu Site-to-Site (S2S) a připojení typu VNet-to-VNet (to znamená připojení mezi virtuálními sítěmi) pomocí modelu nasazení Azure Resource Manager a Azure CLI.

## <a name="about-bgp"></a>Informace o protokolu BGP

BGP je standardní směrovací protokol, který se běžně používá na internetu k výměně informací o směrování a dostupnosti mezi dvěma nebo více sítěmi. Protokol BGP umožňuje vyměňovat trasy pomocí bran VPN a místních zařízení VPN, která se nazývají partnerské uzly protokolu BGP nebo sousedními sítěmi. Trasy informují brány o dostupnosti a dosažitelnosti předpon, aby mohli projít branami nebo směrovači. Protokol BGP může také povolit směrování přenosu mezi více sítěmi tím, že šíří trasy, které brána BGP zjišťuje z jednoho partnerského uzlu protokolu BGP, pro všechny ostatní partnerské uzly protokolu BGP.

Další informace o výhodách protokolu BGP a porozumění technickým požadavkům a doporučeních k použití protokolu BGP najdete v tématu [Přehled protokolu BGP se službou Azure VPN Gateway](vpn-gateway-bgp-overview.md).

Tento článek vám pomůže s následujícími úlohami:

* [Povolit protokol BGP pro bránu VPN](#enablebgp) (povinné)

  Pak můžete provést jednu z následujících částí nebo obojí:

* [Navázání připojení mezi různými místy pomocí protokolu BGP](#crossprembgp)
* [Navázání připojení typu VNet-to-VNet k protokolu BGP](#v2vbgp)

Každá z těchto tří částí tvoří základní stavební blok pro povolení protokolu BGP v připojení k síti. Pokud dokončíte všechny tři části, sestavíte topologii, jak je znázorněno na následujícím diagramu:

![Topologie BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Tyto části můžete kombinovat a vytvořit tak složitější multihop tranzitní síť, která vyhovuje vašim potřebám.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>Povolení protokolu BGP pro bránu VPN

Tato část je nutná před provedením kroků v dalších dvou oddílech konfigurace. Následující kroky konfigurace nastaví parametry protokolu BGP brány Azure VPN, jak je znázorněno na následujícím diagramu:

![Brána BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Než začnete

Nainstalujte nejnovější verzi příkazů rozhraní příkazového řádku (2,0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Krok 1: vytvoření a konfigurace virtuální sítě testvnet1

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Připojte se k předplatnému

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Vytvoření skupiny prostředků

Následující příklad vytvoří skupinu prostředků s názvem TestRG1 v umístění "eastus". Pokud již máte skupinu prostředků v oblasti, ve které chcete vytvořit virtuální síť, můžete ji místo toho použít.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. vytvoření virtuální sítě testvnet1

Následující příklad vytvoří virtuální síť s názvem virtuální sítě testvnet1 a tři podsítě: GatewaySubnet, front-end a back-end. Při nahrazování hodnot je důležité vždycky pojmenovat podsíť brány specificky GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

První příkaz vytvoří front-end adresní prostor a podsíť front-endu. Druhý příkaz vytvoří další adresní prostor pro podsíť back-endu. Třetí a čtvrtý příkaz vytvoří podsíť back-end a GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2: Vytvoření brány VPN Gateway pro virtuální sítě testvnet1 s parametry BGP

#### <a name="1-create-the-public-ip-address"></a>1. vytvoření veřejné IP adresy

Vyžádejte si veřejnou IP adresu. Veřejná IP adresa se přidělí k bráně VPN, kterou vytvoříte pro virtuální síť.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Vytvořte bránu VPN s číslem AS

Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Protokol BGP vyžaduje bránu Route-Based VPN. `-Asn`K nastavení čísla autonomního systému (ASN) pro virtuální sítě testvnet1 potřebujete také další parametr. Vytvoření brány může trvat až (45 minut). 

Pokud tento příkaz spustíte pomocí `--no-wait` parametru, nezobrazí se žádná zpětná vazba ani výstup. `--no-wait`Parametr umožňuje vytvořit bránu na pozadí. Neznamená to, že se Brána VPN vytvoří hned.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. získání IP adresy partnerského uzlu protokolu BGP Azure

Po vytvoření brány je potřeba získat IP adresu partnerského uzlu BGP v bráně Azure VPN. Tato adresa je potřeba ke konfiguraci brány VPN jako partnerského uzlu protokolu BGP pro vaše místní zařízení VPN.

Spusťte následující příkaz a podívejte se na `bgpSettings` oddíl v horní části výstupu:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Po vytvoření brány můžete tuto bránu použít k navázání připojení mezi různými místy nebo připojení typu VNet-to-VNet s protokolem BGP.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Navázání připojení mezi různými místy pomocí protokolu BGP

K navázání připojení mezi různými místy musíte vytvořit bránu místní sítě, která bude představovat vaše místní zařízení VPN. Pak připojíte bránu Azure VPN k bráně místní sítě. I když jsou tyto kroky podobné vytváření dalších připojení, zahrnují další vlastnosti, které jsou potřeba k určení parametrů konfigurace protokolu BGP.

![Protokol BGP pro různé prostory](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Krok 1: vytvoření a konfigurace brány místní sítě

Toto cvičení pokračuje v sestavování konfigurace zobrazené v diagramu. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci. Při práci s branami místní sítě Pamatujte na tyto věci:

* Brána místní sítě může být ve stejném umístění a skupině prostředků jako brána sítě VPN, nebo může být v jiném umístění a skupině prostředků. Tento příklad ukazuje brány v různých skupinách prostředků v různých umístěních.
* Minimální předpona, kterou musíte deklarovat pro bránu místní sítě, je adresa hostitele vaší IP adresy partnerského uzlu protokolu BGP na vašem zařízení VPN. V tomto případě je to předpona/32 10.51.255.254/32.
* Jako připomenutí musíte použít různé čísla ASN protokolu BGP mezi místními sítěmi a virtuální sítí Azure. Pokud jsou stejné, je nutné změnit číslo ASN vaší virtuální sítě, pokud vaše místní zařízení VPN již používají ASN k partnerským uzlům s jinými sousedními uzly protokolu BGP.

Než budete pokračovat, ujistěte se, že jste pro toto cvičení dokončili část [Povolení protokolu BGP pro vaši bránu VPN](#enablebgp) a že jste stále připojeni k předplatnému 1. Všimněte si, že v tomto příkladu vytvoříte novou skupinu prostředků. Všimněte si také dvou dalších parametrů pro bránu místní sítě: `Asn` a `BgpPeerAddress` .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2: připojení brány virtuální sítě a brány místní sítě

V tomto kroku vytvoříte připojení z virtuální sítě testvnet1 k site5. Je nutné zadat `--enable-bgp` parametr pro povolení protokolu BGP pro toto připojení. 

V tomto příkladu jsou brány virtuální sítě a brána místní sítě v různých skupinách prostředků. Když jsou brány v různých skupinách prostředků, musíte zadat celé ID prostředku dvou bran, aby se nastavilo připojení mezi virtuálními sítěmi.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Získejte ID prostředku VNet1GW.

K získání ID prostředku pro VNet1GW použijte výstup z následujícího příkazu:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Ve výstupu vyhledejte `"id":` řádek. K vytvoření připojení v další části budete potřebovat hodnoty v uvozovkách.

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

Zkopírujte hodnoty za `"id":` textový editor, například do poznámkového bloku, abyste je mohli snadno vložit při vytváření připojení. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Získejte ID prostředku site5.

K získání ID prostředku site5 z výstupu použijte následující příkaz:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. vytvoření připojení virtuální sítě testvnet1-to-site5

V tomto kroku vytvoříte připojení z virtuální sítě testvnet1 k site5. Jak už bylo popsáno výše, je možné mít připojení BGP i bez protokolu BGP pro stejnou bránu Azure VPN. V případě, že ve vlastnosti připojení není povolen protokol BGP, Azure pro toto připojení nepovolí protokol BGP, a to i v případě, že parametry protokolu BGP jsou v obou bránách už nakonfigurované. Nahraďte ID předplatného vlastním.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Pro účely tohoto cvičení je v následujícím příkladu uveden seznam parametrů, které se mají zadat do konfiguračního oddílu protokolu BGP místního zařízení VPN:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Připojení by se mělo vytvořit během několika minut. Relace partnerského vztahu protokolu BGP začíná po navázání připojení IPsec.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Navázání připojení typu VNet-to-VNet k protokolu BGP

Tato část přidá připojení typu VNet-to-VNet s protokolem BGP, jak je znázorněno na následujícím obrázku: 

![Protokol BGP pro VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Následující pokyny pokračují v krocích v předchozích částech. Pokud chcete vytvořit a nakonfigurovat virtuální sítě testvnet1 a bránu VPN s protokolem BGP, musíte [pro oddíl povolit protokol BGP pro bránu VPN](#enablebgp) .

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Krok 1: vytvoření TestVNet2 a brány VPN

Je důležité se ujistit, že se adresní prostor IP adres nové virtuální sítě, TestVNet2, nepřekrývá s žádným z rozsahů virtuální sítě.

V tomto příkladu virtuální sítě patří do stejného předplatného. Mezi různými předplatnými můžete nastavit připojení VNet-to-VNet. Další informace najdete v tématu [Konfigurace připojení typu VNet-to-VNet](vpn-gateway-howto-vnet-vnet-cli.md). Nezapomeňte přidat `-EnableBgp $True` při vytváření připojení k povolení protokolu BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Vytvořte novou skupinu prostředků.

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. vytvoření TestVNet2 v nové skupině prostředků

První příkaz vytvoří front-end adresní prostor a podsíť front-endu. Druhý příkaz vytvoří další adresní prostor pro podsíť back-endu. Třetí a čtvrtý příkaz vytvoří podsíť back-end a GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. vytvoření veřejné IP adresy

Vyžádejte si veřejnou IP adresu. Veřejná IP adresa se přidělí k bráně VPN, kterou vytvoříte pro virtuální síť.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Vytvořte bránu VPN s číslem AS.

Vytvořte bránu virtuální sítě pro TestVNet2. Musíte přepsat výchozí číslo ASN na vašich branách Azure VPN Gateway. Čísla ASN pro připojené virtuální sítě musí být odlišné, aby bylo možné povolit směrování protokolu BGP a přenosu.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2: připojení bran virtuální sítě testvnet1 a TestVNet2

V tomto kroku vytvoříte připojení z virtuální sítě testvnet1 k site5. Chcete-li pro toto připojení povolit protokol BGP, je nutné zadat `--enable-bgp` parametr.

V následujícím příkladu jsou brány virtuální sítě a brána místní sítě v různých skupinách prostředků. Když jsou brány v různých skupinách prostředků, musíte zadat celé ID prostředku dvou bran, aby se nastavilo připojení mezi virtuálními sítěmi. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Získejte ID prostředku VNet1GW. 

Získejte ID prostředku VNet1GW z výstupu následujícího příkazu:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Získejte ID prostředku VNet2GW.

Získejte ID prostředku VNet2GW z výstupu následujícího příkazu:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. vytvoření připojení

Vytvořte připojení z virtuální sítě testvnet1 k TestVNet2 a připojení od TestVNet2 k virtuální sítě testvnet1. Nahraďte ID předplatného vlastním.

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

Po dokončení těchto kroků se připojení naváže za několik minut. Po dokončení připojení VNet-to-VNet bude relace partnerského vztahu protokolu BGP zapnutá.

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete virtuální počítače přidat do svých virtuálních sítí. Postup najdete v tématu [Vytvoření virtuálního počítače](../virtual-machines/windows/quick-create-portal.md).

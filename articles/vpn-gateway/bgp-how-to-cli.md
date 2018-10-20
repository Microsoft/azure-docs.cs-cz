---
title: 'Konfigurace protokolu BGP ve službě Azure VPN gateway: Resource Manager a rozhraní příkazového řádku | Dokumentace Microsoftu'
description: Tento článek vás provede konfigurací protokolu BGP se službou Azure VPN gateway pomocí Azure Resource Manageru a rozhraní příkazového řádku.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2018
ms.author: yushwang
ms.openlocfilehash: f0367a360de97d3935c7fa8de9f3dafa6555811e
ms.sourcegitcommit: 62759a225d8fe1872b60ab0441d1c7ac809f9102
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49471352"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Konfigurace protokolu BGP ve službě Azure VPN gateway pomocí rozhraní příkazového řádku

Tento článek pomáhá povolit protokol BGP pro připojení VPN typu Site-to-Site (S2S) mezi místními a připojení VNet-to-VNet (to znamená, připojení mezi virtuálními sítěmi) s použitím modelu nasazení Azure Resource Manageru a Azure CLI.

## <a name="about-bgp"></a>Informace o protokolu BGP

Protokol BGP je standardní směrovací protokol běžně používaných na Internetu k výměně informací o směrování a připojení mezi dvěma nebo více sítí. Protokol BGP umožňuje bran VPN Gateway a vaše místní zařízení VPN volá partnerské uzly protokolu BGP nebo Sousedé, k výměně tras. Trasy informovat o dostupnosti a dosažitelnosti předpon procházejí těmito bránami nebo trasami, které jsou zahrnuté obě brány. BGP také umožňuje směrování přenosu mezi více sítěmi pomocí šíření tras, které brána s protokolem BGP zjistí od jednoho partnerského uzlu protokolu BGP, do všech dalších partnerských uzlů protokolu BGP.

Další informace o výhodách protokolu BGP a pochopit technickými požadavky a důležité informace o použití protokolu BGP najdete v tématu [přehled protokolu BGP s bránami Azure VPN Gateway](vpn-gateway-bgp-overview.md).

Tento článek vám pomůže s těmito úkoly:

* [Povolit protokol BGP pro bránu VPN](#enablebgp) (povinné)

  Potom můžete dokončit jeden z následujících částí nebo oba:

* [Navázání připojení mezi různými místy pomocí protokolu BGP](#crossprembgp)
* [Připojení VNet-to-VNet s protokolem BGP](#v2vbgp)

Všechny tyto tři části tvoří základní stavební blok pro povolení protokolu BGP v připojení k síti. Jestliže dokončíte všechny tři oddíly, jak je znázorněno v následujícím diagramu vytvářet topologie:

![Topologii BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Tyto části vytvářet složitější s vícenásobným směrováním tranzitní síť, která bude vyhovovat vašim potřebám, můžete kombinovat.

## <a name ="enablebgp"></a>Povolit protokol BGP pro bránu VPN

Tato část se vyžaduje před provedením kroků v další dvě konfigurační oddíly. Následující kroky konfigurace nastavit parametry protokolu BGP brány Azure VPN, jak je znázorněno v následujícím diagramu:

![Brána protokolu BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Než začnete

Nainstalujte nejnovější verzi příkazů rozhraní příkazového řádku (2.0 nebo novější). Informace o instalaci příkazů rozhraní příkazového řádku najdete v tématech [Instalace Azure CLI](/cli/azure/install-azure-cli) a [Začínáme s Azure CLI](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Krok 1: Vytvoření a konfigurace virtuální sítě TestVNet1

#### <a name="Login"></a>1. Připojení k vašemu předplatnému

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Vytvoření skupiny prostředků

Následující příklad vytvoří skupinu prostředků TestRG1 v umístění "eastus". Pokud již máte skupinu prostředků v oblasti, ve kterém chcete vytvořit virtuální síť, můžete místo toho použít, že jedna.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Vytvoření virtuální sítě TestVNet1

Následující příklad vytvoří virtuální síť s názvem TestVNet1 a tři podsítě: GatewaySubnet, front-endu a back-endu. Při nahrazování hodnot je důležité vždy název podsítě brány konkrétně GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

První příkaz vytvoří front-endu adresního prostoru a podsítě front-endu. Druhý příkaz vytvoří další adresní prostor pro podsíť back-endu. Třetí a čtvrtá příkazy vytvoří back-endové podsítě a podsíť brány.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2: Vytvoření brány sítě VPN pro virtuální síť TestVNet1 s parametry protokolu BGP

#### <a name="1-create-the-public-ip-address"></a>1. Vytvoření veřejné IP adresy

Vyžádejte si veřejnou IP adresu. Veřejná IP adresa přidělí k bráně VPN, který vytvoříte pro vaše virtuální síť.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Vytvoření brány VPN s použitím čísla AS

Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Protokol BGP vyžaduje bránu sítě VPN založené na směrování. Budete také potřebovat další parametr `-Asn` nastavit číslo autonomního systému (ASN) pro virtuální síť TestVNet1. Vytvoření brány může chvíli trvat (45 minut nebo déle) k dokončení. 

Pokud spustíte tento příkaz pomocí `--no-wait` parametr, se nezobrazí žádná zpětná vazba ani výstup. `--no-wait` Parametr umožňuje bránu vytvořit na pozadí. Neznamená to, je vytvořená služba VPN gateway okamžitě.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Získejte IP adresu partnera Azure BGP

Po vytvoření brány, budete muset získat IP adresu ve službě Azure VPN gateway partnerský uzel protokolu BGP. Tato adresa je potřeba ke konfiguraci brány sítě VPN jako partnerský uzel protokolu BGP pro vaše místní zařízení VPN.

Spusťte následující příkaz a zkontrolujte, `bgpSettings` části v horní části výstupu:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Po vytvoření brány můžete tuto bránu můžete použít k navázání připojení mezi různými místy nebo připojení VNet-to-VNet pomocí protokolu BGP.

## <a name ="crossprembgp"></a>Navázání připojení mezi různými místy pomocí protokolu BGP

K navázání připojení mezi různými místy, je potřeba vytvořit bránu místní sítě k reprezentaci vaše místní zařízení VPN. Potom připojení Azure VPN gateway se brána místní sítě. I když tyto kroky jsou podobné jako vytvoření další připojení, zahrnují další vlastnosti nutné zadat parametry konfigurace protokolu BGP.

![Protokol BGP pro více míst](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Krok 1: Vytvoření a konfigurace brány místní sítě

V tomto cvičení se nadále sestavení konfigurace znázorněné v diagramu. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci. Při práci s brány místní sítě, mějte na paměti následující věci:

* Brána místní sítě může být ve stejném umístění a skupině prostředků jako brány sítě VPN, nebo může být v jiném umístění a skupině prostředků. Tento příklad ukazuje brány v různých skupinách prostředků v různých umístěních.
* Minimální předponu, která je potřeba deklarovat pro bránu místní sítě je adresa hostitele IP adresy partnerského uzlu váš protokol BGP na vašem zařízení VPN. V tomto případě jde /32 předponu 10.52.255.254/32.
* Připomínáme je nutné použít různá čísla ASN protokolu BGP mezi místními sítěmi a Azure virtual network. Pokud se shodují, musíte změnit ASN virtuální sítě v případě, že vaše místní zařízení VPN už používáte k vytvoření partnerského vztahu s dalším sousedům BGP číslo ASN.

Než budete pokračovat, ujistěte se, že jste dokončili [povolit protokol BGP pro bránu VPN](#enablebgp) části v tomto cvičení a že jste stále připojeni k předplatnému 1. Všimněte si, že v tomto příkladu vytvoříte novou skupinu prostředků. Všimněte si také, dva další parametry pro bránu místní sítě: `Asn` a `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2: Připojení brány virtuální sítě a bránu místní sítě

V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 do Site5. Je nutné zadat `--enable-bgp` parametr se povolit protokol BGP pro toto připojení. 

V tomto příkladu brány virtuální sítě a bránu místní sítě jsou v různých skupinách prostředků. Když jsou brány v různých skupinách prostředků, je nutné zadat celý zdroj ID dvě brány k nastavení připojení mezi virtuálními sítěmi.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Získat prostředek ID brány VNet1GW

Pomocí výstupu následujícího příkazu Získejte ID prostředku brány vnet1gw:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Ve výstupu vyhledejte `"id":` řádku. Hodnoty v uvozovkách k vytvoření připojení v další části budete potřebovat.

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

Zkopírujte hodnoty po `"id":` do textového editoru, jako je například Poznámkový blok, tak, aby mohli jednoduše vložit je při vytváření připojení. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Získat prostředek ID Site5

Použijte následující příkaz k získání prostředku ID Site5 z výstupu:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Vytvoření připojení virtuální sítě TestVNet1 Site5

V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 do Site5. Jak je uvedeno výše, je možné mít připojení protokolu BGP i bez protokolu BGP pro tutéž bránu Azure VPN. Pokud je protokol BGP povolen ve vlastnosti připojení, Azure nebude povolit protokol BGP pro toto připojení, i když BGP parametry jsou už nakonfigurovaná na obě brány. ID předplatného nahraďte vlastními.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Pro toto cvičení v následujícím příkladu jsou uvedeny parametry zadejte v části Konfigurace protokolu BGP vašeho místního zařízení VPN:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Připojení by se mělo vytvořit během několika minut. Po vytvoření připojení IPsec spustí relaci partnerského vztahu protokolu BGP.

## <a name ="v2vbgp"></a>Připojení VNet-to-VNet s protokolem BGP

V této části přidá připojení VNet-to-VNet pomocí protokolu BGP, jak je znázorněno v následujícím diagramu: 

![Protokol BGP pro připojení typu VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Následující pokyny jsou pokračováním kroků v předchozí části. Vytvoření a konfigurace virtuální sítě TestVNet1 a bránu VPN s protokolem BGP, musíte [povolit protokol BGP pro bránu VPN](#enablebgp) oddílu.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Krok 1: Vytvoření TestVNet2 a brány VPN

Je důležité, abyste měli jistotu, že se adresní prostor IP adres nové virtuální sítě, TestVNet2, nepřekrývá s žádným z rozsahů virtuálních sítí.

V tomto příkladu patří virtuální sítě do stejného předplatného. Můžete nastavit připojení VNet-to-VNet mezi různých předplatných. Další informace najdete v tématu [konfigurace připojení typu VNet-to-VNet](vpn-gateway-howto-vnet-vnet-cli.md). Ujistěte se, že přidáte `-EnableBgp $True` při vytváření připojení se povolit protokol BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Vytvoření nové skupiny prostředků

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Vytvoření TestVNet2 v nové skupině prostředků

První příkaz vytvoří front-endu adresního prostoru a podsítě front-endu. Druhý příkaz vytvoří další adresní prostor pro podsíť back-endu. Třetí a čtvrtá příkazy vytvoří back-endové podsítě a podsíť brány.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Vytvoření veřejné IP adresy

Vyžádejte si veřejnou IP adresu. Veřejná IP adresa přidělí k bráně VPN, který vytvoříte pro vaše virtuální síť.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Vytvoření brány VPN s použitím čísla AS

Vytvořte bránu virtuální sítě pro TestVNet2. Je nutné přepsat výchozí číslo ASN pro Azure VPN Gateway. Čísla ASN pro připojené virtuální sítě musí být odlišný povolit protokol BGP a směrování provozu.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2: Připojení brány virtuální sítě TestVNet1 a TestVNet2

V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 do Site5. Chcete-li povolit protokol BGP pro toto připojení, musíte zadat `--enable-bgp` parametru.

V následujícím příkladu se Brána virtuální sítě a bránu místní sítě jsou v různých skupinách prostředků. Když jsou brány v různých skupinách prostředků, je nutné zadat celý zdroj ID dvě brány k nastavení připojení mezi virtuálními sítěmi. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Získat prostředek ID brány VNet1GW 

Získejte ID brány VNet1GW prostředků z výstupu následujícího příkazu:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Získat prostředek ID VNet2GW

Získáte prostředek ID VNet2GW z výstupu následujícího příkazu:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Vytvoření připojení

Vytvoření připojení z virtuální sítě TestVNet1 k TestVNet2 a připojení z TestVNet2 k virtuální síti TestVNet1. ID předplatného nahraďte vlastními.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Povolit protokol BGP pro *obě* připojení.
> 
> 

Po dokončení těchto kroků, naváže se připojení během pár minut. Relaci partnerského vztahu protokolu BGP bude po dokončení připojení VNet-to-VNet.

## <a name="next-steps"></a>Další postup

Po dokončení připojení můžete přidat virtuální počítače k virtuálním sítím. Pokyny najdete v tématu [vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

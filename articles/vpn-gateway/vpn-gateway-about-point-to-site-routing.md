---
title: Informace o Azure Point-to-Site směrování | Dokumentace Microsoftu
description: Tento článek vám pomůže porozumět chování směrování VPN typu Point-to-Site.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/28/2019
ms.author: anzaman
ms.openlocfilehash: 486a910226db5dc7b36aaf873e7bb8115eb78805
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55189642"
---
# <a name="about-point-to-site-vpn-routing"></a>Směrování VPN typu Point-to-Site

Tento článek vám pomůže porozumět chování směrování Azure Point-to-Site VPN. Chování směrování P2S VPN je závislá na klientský operační systém, protokol použitý pro připojení k síti VPN a připojení virtuální sítě (Vnet) k sobě navzájem.

Azure aktuálně podporuje dva protokoly pro vzdálený přístup, IKEv2 a SSTP. Protokol IKEv2 je podporován v mnoha operačních systémech klienta, včetně Windows, Linux, MacOS, Android a iOS. SSTP je podporována pouze na Windows. Pokud provedete změnu topologii vaší sítě a máte klienty Windows VPN, musí balíček klienta VPN pro klienty Windows stáhnout a nainstalovat znovu v pořadí, změny se použijí ke klientovi.

> [!NOTE]
> Tento článek se týká pouze pro IKEv2.
>

## <a name="diagrams"></a>Informace o diagramech

Existuje mnoho různých diagramů v tomto článku. Každá část ukazuje různé topologie nebo konfigurace. Pro účely tohoto článku Site-to-Site (S2S) a připojení VNet-to-VNet fungovat stejným způsobem, jako jsou oba tunely IPsec. Všechny brány sítě VPN v tomto článku jsou založené na směrování.

## <a name="isolatedvnet"></a>Jeden samostatný virtuální sítě

Připojení brány VPN typu Point-to-Site v tomto příkladu je pro virtuální síť, který není připojený, nebo v partnerském vztahu s žádné jiné virtuální síti (síti VNet1). V tomto příkladu klientů pomocí protokolu SSTP nebo IKEv2 přístupná ze sítě VNet1.

![samostatný virtuální sítě, směrování](./media/vpn-gateway-about-point-to-site-routing/1.jpg "samostatný směrování virtuální sítě")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Přidání trasy

* Trasy přidané klientům Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům mimo Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup k síti VNet1

* Klienti bez Windows mají přístup k síti VNet1

## <a name="multipeered"></a>Několika navázání partnerského vztahu virtuálních sítí

V tomto příkladu připojení brány VPN typu Point-to-Site je ze sítě vnet1. Síť VNet1 je v partnerském vztahu s ze sítě VNet2. 2 virtuální sítě je v partnerském vztahu s síti VNet3. Síť VNet1 je v partnerském vztahu s virtuální síti Testvnet4. Neexistuje žádné přímé partnerský vztah mezi ze sítě VNet1 a síti VNet3. Ze sítě VNet1 má "Povolit průchod bránou" a ze sítě VNet2 "Používat vzdálené brány" povolena.

Klienti, kteří používají Windows mají přístup k přímo partnerských virtuálních sítích, ale klient VPN musí znova stáhnout, pokud jsou všechny změny provedené partnerský vztah virtuální sítě nebo topologii sítě. Klienti bez Windows mají přístup k přímo partnerských virtuálních sítích. Přístup není tranzitivní a je omezená na pouze přímo partnerských virtuálních sítích.

![několika navázání partnerského vztahu virtuálních sítí](./media/vpn-gateway-about-point-to-site-routing/2.jpg "několika navázání partnerského vztahu virtuálních sítí")

### <a name="address-space"></a>Adresní prostor:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Přidání trasy

* Trasy přidané klientům Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Trasy přidané klientům mimo Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup k ze sítě VNet1 a VNet2, virtuální síti Testvnet4, ale k nějakým změnám topologii projevily musí znova stáhnout klienta VPN.

* Klienti bez Windows mají přístup k ze sítě VNet1 a VNet2, virtuální síti Testvnet4

## <a name="multis2s"></a>Více virtuálních sítí připojenou pomocí S2S VPN

V tomto příkladu připojení brány VPN typu Point-to-Site je ze sítě vnet1. Je připojen ze sítě VNet1 do sítě VNet2 pomocí připojení Site-to-Site VPN. Síť VNet2 je připojen do sítě VNet3 pomocí připojení Site-to-Site VPN. Neexistuje žádné přímé vytvoření partnerského vztahu nebo připojení Site-to-Site VPN mezi ze sítě VNet1 a síti VNet3. Všechna připojení Site-to-Site neběží pro směrování protokolu BGP.

Klienti, kteří používají Windows, nebo jiný podporovaný operační systém, můžete přistupovat pouze ze sítě VNet1. Pro přístup k další virtuální sítě, se musí použít protokol BGP.

![více virtuálních sítí a S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "více virtuálních sítí a S2S")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Přidání trasy

* Trasy přidané klientům Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům Non-Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup pouze k ze sítě VNet1

* Klienti non-Windows mají přístup k síti VNet1 pouze

## <a name="multis2sbgp"></a>Více virtuálních sítí připojenou pomocí VPN S2S (BGP)

V tomto příkladu připojení brány VPN typu Point-to-Site je ze sítě vnet1. Je připojen ze sítě VNet1 do sítě VNet2 pomocí připojení Site-to-Site VPN. Síť VNet2 je připojen do sítě VNet3 pomocí připojení Site-to-Site VPN. Neexistuje žádné přímé vytvoření partnerského vztahu nebo připojení Site-to-Site VPN mezi ze sítě VNet1 a síti VNet3. Všechna připojení Site-to-Site fungují pro směrování protokolu BGP.

Klienti, kteří používají Windows, nebo jiný podporovaný operační systém, dostanete všechny virtuální sítě, které jsou propojeny pomocí připojení Site-to-Site VPN, ale muset ručně přidat do klientů Windows trasy a připojené virtuální sítě.

![více virtuálních sítí a S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "více virtuálních sítí a S2S protokol BGP")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Přidání trasy

* Trasy přidané klientům Windows: 10.1.0.0/16

* Trasy přidané klientům Non-Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup k síti VNet3, ze sítě VNet1 a VNet2, ale bude nutné ručně přidat trasy do sítě VNet2 a síti VNet3.

* Klienti bez Windows mají přístup k síti VNet3, ze sítě VNet1 a VNet2

## <a name="vnetbranch"></a>Jednu virtuální síť a pobočky

V tomto příkladu připojení brány VPN typu Point-to-Site je ze sítě vnet1. Ze sítě VNet1 není připojený / partnerský vztah s druhou virtuální síť, ale je připojené k místní lokalitě prostřednictvím připojení VPN typu Site-to-Site, která není spuštěná protokolu BGP.

Klienty se systémy Windows a jiných Windows můžete přistupovat pouze ze sítě VNet1.

![směrování s virtuální síť a pobočky](./media/vpn-gateway-about-point-to-site-routing/5.jpg "směrování s virtuální síť a pobočky")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* Web1: 10.101.0.0/16

### <a name="routes-added"></a>Přidání trasy

* Trasy přidané klientům Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům Non-Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup k pouze ze sítě VNet1

* Klienti non-Windows mají přístup k síti VNet1 pouze

## <a name="vnetbranchbgp"></a>Jednu virtuální síť a pobočky (BGP)

V tomto příkladu připojení brány VPN typu Point-to-Site je ze sítě vnet1. Ze sítě VNet1 není připojený nebo partnerský vztah s druhou virtuální síť, ale je připojená k místní lokalitě (web1) přes připojení VPN typu Site-to-Site s BGP.

Klienti Windows mají přístup k virtuální síti a pobočce (web1), ale trasy pro web1 musí ručně přidali do klienta. Klienti bez Windows mají přístup k virtuální síti, stejně jako místní firemní pobočce.

![jednu virtuální síť a pobočky (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "jednu virtuální síť a pobočky")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* Web1: 10.101.0.0/16

### <a name="routes-added"></a>Přidání trasy

* Trasy přidané klientům Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům Non-Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup k síti VNet1 a web1, ale bude nutné ručně přidat trasy a web1.

* Klienti Windows bez přístupná ze sítě VNet1 a web1.


## <a name="multivnets2sbranch"></a>Více virtuálních sítí připojenou pomocí S2S a firemní pobočky

V tomto příkladu připojení brány VPN typu Point-to-Site je ze sítě vnet1. Je připojen ze sítě VNet1 do sítě VNet2 pomocí připojení Site-to-Site VPN. Síť VNet2 je připojen do sítě VNet3 pomocí připojení Site-to-Site VPN. Neexistuje žádné přímé vytvoření partnerského vztahu ani tunelového připojení sítě Site-to-Site VPN mezi sítí ze sítě VNet1 a síti VNet3. Síti VNet3 je připojený k firemní pobočka (web1) pomocí připojení Site-to-Site VPN. Všechna připojení VPN nejsou spuštěné protokolu BGP.

Všichni klienti můžete přistupovat pouze ze sítě VNet1.

![Multi-virtuální síť S2S a větev office](./media/vpn-gateway-about-point-to-site-routing/7.jpg "multi-virtuální síť S2S a větev office")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Web1: 10.101.0.0/16

### <a name="routes-added"></a>Přidání trasy

* Trasy přidali klientů: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům Non-Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup k síti VNet1 pouze

* Klienti non-Windows mají přístup k síti VNet1 pouze

## <a name="multivnets2sbranchbgp"></a>Více virtuálních sítí připojenou pomocí S2S a že firemní pobočka (BGP)

V tomto příkladu připojení brány VPN typu Point-to-Site je ze sítě vnet1. Je připojen ze sítě VNet1 do sítě VNet2 pomocí připojení Site-to-Site VPN. Síť VNet2 je připojen do sítě VNet3 pomocí připojení Site-to-Site VPN. Neexistuje žádné přímé vytvoření partnerského vztahu ani tunelového připojení sítě Site-to-Site VPN mezi sítí ze sítě VNet1 a síti VNet3. Síti VNet3 je připojený k firemní pobočka (web1) pomocí připojení Site-to-Site VPN. Všechna připojení VPN běží protokolu BGP.

Klienti, kteří používají Windows mít přístup k virtuálním sítím a lokalitami propojenými pomocí připojení Site-to-Site VPN, ale trasy do sítě VNet2 a síti VNet3 web1 musí ručně přidali do klienta. Klienti bez Windows mají přístup k virtuálním sítím a lokalitami propojenými pomocí připojení Site-to-Site VPN bez ručního zásahu. Přístup je přenosné a klienti mají přístup k prostředkům v propojení virtuálních sítí a serverů (místní).

![Multi-virtuální síť S2S a větev office](./media/vpn-gateway-about-point-to-site-routing/8.jpg "multi-virtuální síť S2S a větev office")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Web1: 10.101.0.0/16

### <a name="routes-added"></a>Přidání trasy

* Trasy přidali klientů: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům Non-Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup k sítě VNet1, ze sítě VNet2, síti VNet3 a web1, ale trasy do sítě VNet2 a síti VNet3 web1 musí ručně přidali do klienta.

* Klienti non-Windows mají přístup k síti VNet1, ze sítě Vnet2, síti VNet3 a web1.

## <a name="next-steps"></a>Další postup

Zobrazit [P2S VPN pomocí webu Azure portal vytvořit](vpn-gateway-howto-point-to-site-resource-manager-portal.md) a začněte vytvářet svou síť VPN P2S.

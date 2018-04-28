---
title: O Azure Point-to-Site směrování | Microsoft Docs
description: Tento článek vám pomůže pochopit chování směrování Point-to-Site VPN.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2018
ms.author: anzaman
ms.openlocfilehash: d25709fb4abb1b8a35596c3dc246f7419a99419b
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="about-point-to-site-vpn-routing"></a>O směrování Point-to-Site VPN

Tento článek vám pomůže pochopit chování směrování Azure Point-to-Site VPN. Chování směrování P2S VPN je závislá na klientského operačního systému, protokol použitý pro připojení k síti VPN a jak jsou navzájem připojené virtuální sítě (virtuální sítě).

Azure aktuálně podporuje dva protokoly pro vzdálený přístup, a to díky protokolům IKEv2 a SSTP. Protokol IKEv2 je podporován v mnoha klientskými operačními systémy včetně systému Windows, Linux, systému MacOS, Android a iOS. SSTP je podporována pouze v systému Windows. Pokud změňte topologie sítě a klienti VPN ve Windows, musí být Stáhnout balíček klienta VPN pro klienty se systémem Windows a znovu nainstalovat změny, které má být použita pro klienta.

> [!NOTE]
> Tento článek se týká pouze pro IKEv2.
>

## <a name="diagrams"></a>O diagramy

Existuje několik různých diagramů v tomto článku. Každá část ukazuje různé topologie nebo konfigurace. Pro účely tohoto článku Site-to-Site (S2S) a připojení VNet-to-VNet fungovat stejně, jako jsou obě tunelových propojení IPsec. Všechny brány sítě VPN v tomto článku jsou založené na trasách.

## <a name="isolatedvnet"></a>Jeden izolované virtuální sítě

Připojení brány VPN Point-to-Site v tomto příkladu je pro virtuální síť, který není připojený nebo peered s žádné jiné virtuální síti (VNet1). V tomto příkladu můžete přístup klientů pomocí protokolu SSTP nebo IKEv2 VNet1.

![izolované virtuální sítě směrování](./media/vpn-gateway-about-point-to-site-routing/1.jpg "izolované virtuální sítě směrování")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Přidání tras

* Trasy přidané klientům Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům jiný systém než Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup k VNet1

* Klienti jiných systémů než Windows mají přístup k VNet1

## <a name="multipeered"></a>Více peered virtuálních sítí

V tomto příkladu je připojení brány VPN Point-to-Site pro VNet1. VNet1 je peered s VNet2. Virtuální sítě 2 je peered s VNet3. VNet1 je peered s VNet4. Není žádný přímý partnerský vztah mezi VNet1 a VNet3. VNet1 má "Povolit přenosu brány" a VNet2 "Použití vzdáleného brány" povoleno.

Klienti, kteří používají Windows můžete přistupovat přímo peered virtuálních sítí, ale klient VPN musí znova stáhnout, pokud jsou udělají jakékoli změny partnerského vztahu virtuální síť nebo síťové topologie. Klienti jiných systémů než Windows můžou přistupovat přímo peered virtuální sítě. Přístup není přenositelné a je omezený pouze přímo peered virtuálních sítí.

![více peered virtuálních sítí](./media/vpn-gateway-about-point-to-site-routing/2.jpg "peered více virtuálních sítí")

### <a name="address-space"></a>Adresní prostor:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Přidání tras

* Trasy přidané klientům Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Trasy přidané klientům jiný systém než Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* VNet1, VNet2 a VNet4 mají přístup klienti systému Windows, ale klient VPN musí být znovu stažena pro všechny topologie změny se projeví.

* VNet1, VNet2 a VNet4 mají přístup klienti nejsou pro Windows

## <a name="multis2s"></a>Více virtuálních sítí připojené pomocí S2S VPN

V tomto příkladu je připojení brány VPN Point-to-Site pro VNet1. VNet1 je připojený k VNet2 pomocí připojení Site-to-Site VPN. VNet2 je připojený k VNet3 pomocí připojení Site-to-Site VPN. Neexistuje žádné přímé partnerský vztah nebo připojení Site-to-Site VPN mezi VNet1 a VNet3. Všechna připojení Site-to-Site neběží pro směrování protokolu BGP.

Klienti, kteří používají Windows nebo jiné podporovaný operační systém, můžete přístup jenom k VNet1. Pro přístup k další virtuální sítě, se musí použít protokol BGP.

![více virtuálních sítí a S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "více virtuálních sítí a S2S")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Přidání tras

* Trasy přidané klientům Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům nejsou pro Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup pouze k VNet1

* Klienti jiných systémů než Windows mají přístup k VNet1 pouze

## <a name="multis2sbgp"></a>Více virtuálních sítí připojených přes VPN S2S (BGP)

V tomto příkladu je připojení brány VPN Point-to-Site pro VNet1. VNet1 je připojený k VNet2 pomocí připojení Site-to-Site VPN. VNet2 je připojený k VNet3 pomocí připojení Site-to-Site VPN. Neexistuje žádné přímé partnerský vztah nebo připojení Site-to-Site VPN mezi VNet1 a VNet3. Všechna připojení Site-to-Site běží pro směrování protokolu BGP.

Klienti, kteří používají Windows nebo jiné podporovaný operační systém, můžete přístup všechny virtuální sítě, které jsou připojené pomocí připojení Site-to-Site VPN, ale trasy připojených virtuálních sítí musí ručně přidat do klientů systému Windows.

![více virtuálních sítí a S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "více virtuálních sítí a S2S protokol BGP")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Přidání tras

* Trasy přidané klientům Windows: 10.1.0.0/16

* Trasy přidané klientům nejsou pro Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* VNet1, VNet2 a VNet3 mají přístup klienti systému Windows, ale trasy VNet2 a VNet3 bude muset ručně přidat.

* VNet1, VNet2 a VNet3 mají přístup klienti nejsou pro Windows

## <a name="vnetbranch"></a>Jedna virtuální síť a pobočky

V tomto příkladu je připojení brány VPN Point-to-Site pro VNet1. VNet1 není připojen / peered s jiné virtuální síti, ale je připojen na místní web prostřednictvím připojení k síti VPN Site-to-Site, která neběží protokolu BGP.

VNet1 a na pobočce (web1) mají přístup klienti systému Windows, ale jsou trasy k web1 musí ručně přidat do klienta. Klienti jiných systémů než Windows mají přístup k VNet1, stejně jako místní web1.

![směrování s virtuální síť a pobočky](./media/vpn-gateway-about-point-to-site-routing/5.jpg "směrování s virtuální síť a pobočky")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* Web1: 10.101.0.0/16

### <a name="routes-added"></a>Přidání tras

* Trasy přidané klientům Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům nejsou pro Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows můžou přistupovat jenom VNet1

* Klienti jiných systémů než Windows mají přístup k VNet1 pouze

## <a name="vnetbranchbgp"></a>Jedna virtuální síť a pobočky (BGP)

V tomto příkladu je připojení brány VPN Point-to-Site pro VNet1. VNet1 není připojen nebo peered s jiné virtuální síti, ale je připojená k místní lokalitě (web1) prostřednictvím připojení VPN typu Site-to-Site s BGP.

Klienti Windows mají přístup k virtuální síti a na pobočce (web1), ale jsou trasy k web1 musí ručně přidat do klienta. Klienti jiných systémů než Windows mají přístup k virtuální síti a také na místní pobočce.

![jedna virtuální síť a pobočky (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "jednu virtuální síť a pobočky")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* Web1: 10.101.0.0/16

### <a name="routes-added"></a>Přidání tras

* Trasy přidané klientům Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům nejsou pro Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* VNet1 a web1 mají přístup klienti systému Windows, ale bude nutné ručně přidat trasy k web1.

* Klienti jiných systémů než Windows mají přístup k VNet1 a web1.


## <a name="multivnets2sbranch"></a>Více virtuálních sítí, které jsou propojeny pomocí S2S a pobočky

V tomto příkladu je připojení brány VPN Point-to-Site pro VNet1. VNet1 je připojený k VNet2 pomocí připojení Site-to-Site VPN. VNet2 je připojený k VNet3 pomocí připojení Site-to-Site VPN. Neexistuje žádné přímé partnerský vztah nebo tunelu Site-to-Site VPN mezi sítěmi VNet1 a VNet3. VNet3 je připojený k firemní pobočku (web1) pomocí připojení Site-to-Site VPN. Všechna připojení VPN nejsou spuštěné protokolu BGP.

Všichni klienti mají přístup k VNet1 pouze.

![více VNet S2S a větve office](./media/vpn-gateway-about-point-to-site-routing/7.jpg "office S2S a větve více VNet")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Web1: 10.101.0.0/16

### <a name="routes-added"></a>Přidání tras

* Klienti přidat trasy: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům nejsou pro Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup k VNet1 pouze

* Klienti jiných systémů než Windows mají přístup k VNet1 pouze

## <a name="multivnets2sbranchbgp"></a>Více virtuálních sítí, které jsou propojeny pomocí S2S a pobočky (BGP)

V tomto příkladu je připojení brány VPN Point-to-Site pro VNet1. VNet1 je připojený k VNet2 pomocí připojení Site-to-Site VPN. VNet2 je připojený k VNet3 pomocí připojení Site-to-Site VPN. Neexistuje žádné přímé partnerský vztah nebo tunelu Site-to-Site VPN mezi sítěmi VNet1 a VNet3. VNet3 je připojený k firemní pobočku (web1) pomocí připojení Site-to-Site VPN. Všechna připojení VPN nejsou spuštěné protokolu BGP. Všechna připojení sítě VPN běží protokolu BGP.

Klienti, kteří používají Windows přístup k virtuálním sítím a lokalit, které jsou připojené pomocí připojení Site-to-Site VPN, ale jsou trasy k VNet2, VNet3 a web1, je třeba ručně přidat do klienta. Klienti jiných systémů než Windows mají přístup k virtuálním sítím a lokalit, které jsou připojené pomocí připojení Site-to-Site VPN bez jakékoli ruční zásah. Přístup je přenositelné a klienti mají přístup k prostředkům v všechny připojené virtuální sítě a lokality (místní).

![více VNet S2S a větve office](./media/vpn-gateway-about-point-to-site-routing/8.jpg "office S2S a větve více VNet")

### <a name="address-space"></a>Adresní prostor

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Web1: 10.101.0.0/16

### <a name="routes-added"></a>Přidání tras

* Klienti přidat trasy: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané klientům nejsou pro Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* VNet1, VNet2, VNet3 a web1 mají přístup klienti systému Windows, ale trasy VNet2, VNet3 a web1 musí ručně přidat do klienta.

* Klienti jiných systémů než Windows mají přístup k VNet1, Vnet2, VNet3 a web1.

## <a name="next-steps"></a>Další postup

V tématu [vytvořit pomocí portálu Azure VPN P2S](vpn-gateway-howto-point-to-site-resource-manager-portal.md) zahajte proces vytváření P2S síť VPN.

---
title: 'Azure VPN Gateway: informace o směrování P2S'
description: Přečtěte si o směrování sítě VPN typu Point-to-site pro různé operační systémy, protokoly vzdáleného přístupu a konfigurace virtuálních sítí.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0b9b8ba555cddd56c49c750709e69ec180291c95
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91827238"
---
# <a name="about-point-to-site-vpn-routing"></a>Směrování VPN typu Point-to-Site

Tento článek vám pomůže pochopit, jak se chová směrování typu Point-to-Site VPN. Chování směrování sítě VPN P2S závisí na operačním systému klienta, protokolu používaném pro připojení VPN a způsobu vzájemného propojení virtuálních sítí (virtuální sítě).

Azure v současné době podporuje dva protokoly pro vzdálený přístup, IKEv2 a SSTP. IKEv2 se podporuje v mnoha klientských operačních systémech, mezi které patří Windows, Linux, macOS, Android a iOS. SSTP se podporuje jenom v systému Windows. Pokud provedete změnu topologie sítě a máte klienty VPN se systémem Windows, musí se balíček klienta VPN pro klienty se systémem Windows stáhnout a nainstalovat znovu, aby se změny projevily u klienta.

> [!NOTE]
> Tento článek se týká pouze IKEv2.
>

## <a name="about-the-diagrams"></a><a name="diagrams"></a>O diagramech

Tento článek obsahuje řadu různých diagramů. V každé části se zobrazuje jiná topologie nebo konfigurace. Pro účely tohoto článku funguje připojení typu Site-to-Site (S2S) a VNet-to-VNet stejným způsobem, protože obě tunelová propojení používají protokol IPsec. Všechny brány VPN v tomto článku jsou založené na trasách.

## <a name="one-isolated-vnet"></a><a name="isolatedvnet"></a>Jedna izolovaná virtuální síť

Připojení brány VPN typu Point-to-site v tomto příkladu je pro virtuální síť, která není připojená nebo má partnerský vztah s jinou virtuální sítí (VNet1). V tomto příkladu můžou klienti získat přístup k VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg" alt-text="Směrování izolované virtuální sítě" lightbox="./media/vpn-gateway-about-point-to-site-routing/isolated.jpg":::

### <a name="address-space"></a>Adresní prostor

* VNet1:10.1.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů systému Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané na klienty jiného typu než Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mají přístup k VNet1

* Klienti s jiným systémem než Windows mají přístup k VNet1

## <a name="multiple-peered-vnets"></a><a name="multipeered"></a>Několik virtuální sítě s partnerským vztahem

V tomto příkladu je připojení brány VPN typu Point-to-site pro VNet1. VNet1 má partnerský vztah s VNet2. Virtuální síť 2 má partnerský vztah s síti vnet3. VNet1 má partnerský vztah s VNet4. Mezi VNet1 a síti vnet3 se nevztahují žádné přímé partnerské vztahy. VNet1 má možnost povolit přenos brány a VNet2 a VNet4 mít povolenou možnost používat vzdálené brány.

Klienti, kteří používají systém Windows, mají přístup přímo k virtuální sítě s partnerským vztahem, ale klient VPN se musí stáhnout znovu, pokud se změní na partnerský vztah VNet nebo síťová topologie. Klienti s jiným operačním systémem než Windows mají přístup přímo k virtuální sítě partnerských vztahů. Přístup není přenosný a je omezený jenom na přímo partnerský virtuální sítě.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg" alt-text="Několik virtuální sítě s partnerským vztahem" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple.jpg":::

### <a name="address-space"></a>Adresní prostor:

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* Síti vnet3:10.3.0.0/16

* VNet4:10.4.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů systému Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Trasy přidané do klientů jiných výrobců než Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mají přístup k VNet1, VNet2 a VNet4, ale je nutné znovu stáhnout klienta VPN, aby se projevily změny topologie.

* Klienti s jiným systémem než Windows mají přístup k VNet1, VNet2 a VNet4

## <a name="multiple-vnets-connected-using-an-s2s-vpn"></a><a name="multis2s"></a>Vícenásobné virtuální sítě připojené pomocí S2S VPN

V tomto příkladu je připojení brány VPN typu Point-to-site pro VNet1. VNet1 je připojen k VNet2 pomocí připojení VPN typu Site-to-site. VNet2 je připojen k síti vnet3 pomocí připojení VPN typu Site-to-site. Mezi VNet1 a síti vnet3 není žádné přímé připojení VPN typu Site-to-site. Všechna připojení Site-to-site nepoužívají protokol BGP pro směrování.

Klienti, kteří používají systém Windows nebo jiný podporovaný operační systém, mohou přistupovat pouze k VNet1. Pro přístup k dalším virtuální sítě se musí použít protokol BGP.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg" alt-text="Několik virtuální sítě a S2S" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-s2s.jpg":::

### <a name="address-space"></a>Adresní prostor

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* Síti vnet3:10.3.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů systému Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané na klienty jiného typu než Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti Windows mají přístup jenom k VNet1

* Klienti s jiným systémem než Windows mají přístup jenom k VNet1

## <a name="multiple-vnets-connected-using-an-s2s-vpn-bgp"></a><a name="multis2sbgp"></a>Vícenásobné virtuální sítě připojené pomocí S2S VPN (BGP)

V tomto příkladu je připojení brány VPN typu Point-to-site pro VNet1. VNet1 je připojen k VNet2 pomocí připojení VPN typu Site-to-site. VNet2 je připojen k síti vnet3 pomocí připojení VPN typu Site-to-site. Mezi VNet1 a síti vnet3 není žádné přímé připojení VPN typu Site-to-site. Všechna připojení Site-to-site spouští protokol BGP pro směrování.

Klienti s Windows nebo jiným podporovaným operačním systémem mají přístup ke všem virtuální sítě, která jsou připojená pomocí připojení VPN typu Site-to-site, ale trasy k připojeným virtuální sítě se musí do klientů Windows přidat ručně.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg" alt-text="Vícenásobná virtuální sítě a S2S (BGP)" lightbox="./media/vpn-gateway-about-point-to-site-routing/multiple-bgp.jpg":::

### <a name="address-space"></a>Adresní prostor

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* Síti vnet3:10.3.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů systému Windows: 10.1.0.0/16

* Trasy přidané do klientů jiných výrobců než Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mají přístup k VNet1, VNet2 a síti vnet3, ale trasy do VNet2 a síti vnet3 bude nutné přidat ručně.

* Klienti s jiným systémem než Windows mají přístup k VNet1, VNet2 a síti vnet3

## <a name="one-vnet-and-a-branch-office"></a><a name="vnetbranch"></a>Jedna virtuální síť a firemní pobočky

V tomto příkladu je připojení brány VPN typu Point-to-site pro VNet1. VNet1 není připojen/k partnerskému vztahu s žádnou jinou virtuální sítí, ale je připojen k místní lokalitě prostřednictvím připojení VPN typu Site-to-site, které nepoužívá protokol BGP.

Klienti s Windows a jiným systémem než Windows mají přístup jenom k VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg" alt-text="Směrování pomocí virtuální sítě a firemní pobočky" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-office.jpg":::

### <a name="address-space"></a>Adresní prostor

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů systému Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané na klienty jiného typu než Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mají přístup pouze k VNet1

* Klienti s jiným systémem než Windows mají přístup jenom k VNet1

## <a name="one-vnet-and-a-branch-office-bgp"></a><a name="vnetbranchbgp"></a>Jedna virtuální síť a odloučená kancelář (BGP)

V tomto příkladu je připojení brány VPN typu Point-to-site pro VNet1. VNet1 není připojená nebo má partnerský vztah s žádnou jinou virtuální sítí, ale je připojená k místní lokalitě (Site1) prostřednictvím připojení VPN typu Site-to-site s protokolem BGP.

Klienti Windows mají přístup k virtuální síti a pobočce (Site1), ale trasy k Site1 je nutné do klienta přidat ručně. Klienti s jiným systémem než Windows mají přístup k virtuální síti i k místní pobočce.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg" alt-text="Směrování pomocí virtuální sítě a protokolu BGP poboček" lightbox="./media/vpn-gateway-about-point-to-site-routing/branch-bgp.jpg":::

### <a name="address-space"></a>Adresní prostor

* VNet1:10.1.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů systému Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané na klienty jiného typu než Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mohou přistupovat k VNet1 a Site1, ale trasy do Site1 bude nutné přidat ručně.

* Klienti s jiným systémem než Windows mají přístup k VNet1 a Site1.


## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office"></a><a name="multivnets2sbranch"></a>Vícenásobné virtuální sítě připojené pomocí S2S a pobočky

V tomto příkladu je připojení brány VPN typu Point-to-site pro VNet1. VNet1 je připojen k VNet2 pomocí připojení VPN typu Site-to-site. VNet2 je připojen k síti vnet3 pomocí připojení VPN typu Site-to-site. Mezi sítěmi VNet1 a síti vnet3 neexistují přímé tunelové propojení ani VPN typu Site-to-site. Síti vnet3 je připojen k podnikové pobočce (Site1) pomocí připojení VPN typu Site-to-site. Všechna připojení VPN neprovozují protokol BGP.

Všichni klienti mají přístup jenom k VNet1.

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg" alt-text="Diagram, který zobrazuje S2S s více virtuálními sítěmi a firemní pobočky" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch.jpg":::

### <a name="address-space"></a>Adresní prostor

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* Síti vnet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů systému Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané do klientů jiných výrobců než Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mají přístup pouze k VNet1

* Klienti s jiným systémem než Windows mají přístup jenom k VNet1

## <a name="multiple-vnets-connected-using-s2s-and-a-branch-office-bgp"></a><a name="multivnets2sbranchbgp"></a>Několik virtuální sítě propojených pomocí S2S a Branch Office (BGP)

V tomto příkladu je připojení brány VPN typu Point-to-site pro VNet1. VNet1 je připojen k VNet2 pomocí připojení VPN typu Site-to-site. VNet2 je připojen k síti vnet3 pomocí připojení VPN typu Site-to-site. Mezi sítěmi VNet1 a síti vnet3 neexistují přímé tunelové propojení ani VPN typu Site-to-site. Síti vnet3 je připojen k podnikové pobočce (Site1) pomocí připojení VPN typu Site-to-site. Všechna připojení VPN provozují protokol BGP.

Klienti, kteří používají systém Windows, mají přístup k virtuální sítě a lokalitám, které jsou připojené pomocí připojení VPN typu Site-to-site, ale trasy k VNet2, síti vnet3 a Site1 se musí do klienta přidat ručně. Klienti s jiným systémem než Windows mají přístup k virtuální sítě a lokalitám, které jsou připojené pomocí připojení VPN typu Site-to-site bez nutnosti ručního zásahu. Přístup je přenosný a klienti mají přístup k prostředkům ve všech připojených virtuální sítě a lokalitách (místně).

:::image type="content" source="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg" alt-text="připojení S2S a pobočky s více virtuálními sítěmi" lightbox="./media/vpn-gateway-about-point-to-site-routing/multi-branch-bgp.jpg":::

### <a name="address-space"></a>Adresní prostor

* VNet1:10.1.0.0/16

* VNet2:10.2.0.0/16

* Síti vnet3:10.3.0.0/16

* Site1:10.101.0.0/16

### <a name="routes-added"></a>Přidané trasy

* Trasy přidané do klientů systému Windows: 10.1.0.0/16, 192.168.0.0/24

* Trasy přidané do klientů jiných výrobců než Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Access

* Klienti systému Windows mohou přistupovat k VNet1, VNet2, síti vnet3 a Site1, ale trasy do VNet2, síti vnet3 a Site1 je nutné ručně přidat do klienta.

* Klienti s jiným systémem než Windows mají přístup k VNet1, Vnet2, síti vnet3 a Site1.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít vytvářet P2S VPN, přečtěte si téma [vytvoření sítě VPN s P2S pomocí Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

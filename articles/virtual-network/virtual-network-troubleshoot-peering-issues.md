---
title: Řešení potíží s partnerskými vztahy virtuálních sítí
description: Postup řešení většiny problémů s partnerským vztahem virtuální sítě.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 1fddbe908ccebc1384dcccde0810366f1a6d5da7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73796242"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Řešení potíží s partnerskými vztahy virtuálních sítí

Tento průvodce odstraňováním potíží obsahuje postup, který vám pomůže vyřešit většinu problémů [s partnerským vztahem virtuální sítě.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)

![Diagram partnerského vztahu virtuální sítě](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Konfigurace partnerského vztahu virtuální sítě mezi dvěma virtuálními sítěmi

Jsou virtuální sítě ve stejném předplatném nebo v různých předplatných?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Virtuální sítě jsou ve stejném předplatném

Chcete-li nakonfigurovat partnerský vztah virtuální sítě pro virtuální sítě, které jsou ve stejném předplatném, použijte metody v následujících článcích:

* Pokud jsou virtuální sítě *ve stejné oblasti*, [přečtěte si tématu Vytvoření partnerského vztahu](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Pokud se virtuální sítě nacházejí v *různých oblastech*, přečtěte si informace [o partnerské síti virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 

> [!Note]
> Připojení nefunguje přes partnerský vztah globální virtuální sítě pro následující prostředky: 
>
> * Virtuální počítače (vy vyvisu) za základní interní nástroj pro vyrovnávání zatížení (ILB) SKU
> * Redis cache (používá základní Skladovou položku ILB)
> * Aplikační brána (používá základní skladovou položku ILB)
> * Škálovací sady virtuálních strojů (používá základní skladovou položku ILB)
> * Clustery Azure Service Fabric (používá základní skladovou položku ILB)
> * SQL Server vždy zapnutý (používá základní skladovou položku ILB)
> * Prostředí služby Azure App Service pro PowerApps (používá základní sku ILB)
> * Azure API Management (používá základní sku ILB)
> * Azure Active Directory Domain Services (Azure AD DS) (používá základní skladovou položku ILB)

Další informace naleznete v [tématu požadavky a omezení](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globálního partnerského vztahu.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Virtuální sítě jsou v různých předplatných nebo klientech služby Active Directory.

Pokud chcete nakonfigurovat partnerský vztah virtuální sítě pro virtuální sítě v různých předplatných nebo v tenantech služby Active Directory, přečtěte si informace [o vytvoření partnerského vztahu v různých předplatných pro rozhraní příkazového příkazu k síti Azure](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Chcete-li konfigurovat partnerský vztah v síti, musíte mít oprávnění **síťového přispěvatele** v obou předplatných. Další informace naleznete v [tématu Oprávnění partnerského vztahu](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Konfigurace partnerského vztahu virtuální sítě pomocí topologie rozbočovače paprsků, která používá místní prostředky

![Diagram partnerského vztahu virtuální sítě s místním paprskem](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Pro připojení site-to-site nebo ExpressRoute připojení

Postupujte podle kroků v: [Konfigurace přenosu brány VPN pro partnerský vztah virtuální sítě](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="for-point-to-site-connections"></a>Pro připojení z bodu na pracoviště

1. Postupujte podle kroků v: [Konfigurace přenosu brány VPN pro partnerský vztah virtuální sítě](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Po navázání nebo změně partnerského vztahu virtuální sítě stáhněte a přeinstalujte balíček typu point-to-site tak, aby klienti typu point-to-site získali aktualizované trasy do virtuální sítě s paprsky.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Konfigurace partnerského vztahu virtuální sítě pomocí virtuální sítě topologie s rozbočovačem

![Diagram partnerského vztahu virtuální sítě s virtuální sítí](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Virtuální sítě jsou ve stejné oblasti


1. Ve virtuální síti rozbočovače nakonfigurujte síťové virtuální zařízení (NVA).
1. Ve virtuálních sítích paprsků mají uživatelem definované trasy s dalším typem směrování "síťové virtuální zařízení".

Další informace naleznete v [tématu Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Pokud potřebujete pomoc s nastavením nového výrobce softwaru, [obraťte se na dodavatele nva](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Nápovědu k řešení potíží s nastavením a směrováním zařízení síťového virtuálního zařízení najdete [v tématu Problémy s virtuálními zařízeními v Síti V Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="the-virtual-networks-are-in-different-regions"></a>Virtuální sítě jsou v různých regionech

Přenos přes partnerský vztah globální virtuální sítě je teď podporován. Připojení nefunguje přes partnerský vztah globální virtuální sítě pro následující prostředky:

* Virtuální jsou virtuální mise za základní sku ILB
* Redis cache (používá základní Skladovou položku ILB)
* Aplikační brána (používá základní skladovou položku ILB)
* Škálovací sady (používá základní skladovou položku ILB)
* Clustery Service Fabric (používá základní skladovou položku ILB)
* SQL Server vždy zapnutý (používá základní skladovou položku ILB)
* Prostředí služby App Service (používá základní skladovou položku ILB)
* Správa rozhraní API (používá základní skladovou položku ILB)
* Azure AD DS (používá základní skladovou položku ILB)

Další informace o požadavcích a omezeních globálního partnerského vztahu najdete [v tématu Partnerský vztah virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Řešení potíží s připojením mezi dvěma partnerskými virtuálními sítěmi

Přihlaste se k [portálu Azure](https://portal.azure.com/) pomocí účtu, který má potřebné [role a oprávnění](virtual-network-manage-peering.md#permissions). Vyberte virtuální síť, vyberte **Partnerský vztah**a zaškrtněte pole **Stav.** Jaký je stav?

### <a name="the-peering-status-is-connected"></a>Stav partnerského vztahu je "Připojeno"

Postup řešení tohoto problému:

1. Zkontrolujte toky síťového provozu:

   Pomocí [poradce při potížích s připojením](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) a [ověření toku IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) z hlavního virtuálního počítačů do cílového virtuálního počítačů určete, zda existuje nsg nebo UDR, který způsobuje rušení v tocích provozu.

   Pokud používáte bránu firewall nebo službu NVA: 
   1. Zdokumentujte parametry UDR, abyste je mohli po dokončení tohoto kroku obnovit.
   2. Odeberte UDR ze zdrojové podsítě virtuálního soudu nebo síťové sítě, která odkazuje na síťové virtuální sítě jako další směrování. Ověřte připojení ze zdrojového virtuálního virtuálního zařízení přímo k cíli, který je vynechání mnoství ovisu. Pokud tento krok nefunguje, přečtěte [si téma Poradce při potížích se službou NVA](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Trasování sítě: 
   1. Spusťte trasování sítě na cílovém virtuálním počítači. Pro Windows můžete použít **Netsh**. Pro Linux použijte **TCPDump**.
   2. Spusťte **protokol TcpPing** nebo **PsPing** ze zdroje do cílové ip adresy.

      Toto je příklad příkazu **TcpPing:**`tcping64.exe -t <destination VM address> 3389`

   3. Po dokončení **protokolu TcpPing** zastavte trasování sítě v cílovém umístění.
   4. Pokud pakety dorazí ze zdroje, neexistuje žádný problém se sítí. Zkontrolujte bránu firewall virtuálního počítače i aplikaci naslouchající na tomto portu a vyhledejte problém s konfigurací.

   > [!Note]
   > Nemůžete se připojit k následujícím typům prostředků prostředcích prostředcích globálního partnerského vztahu virtuální sítě (virtuální sítě v různých oblastech):
   >
   > * Virtuální jsou virtuální mise za základní sku ILB
   > * Redis cache (používá základní Skladovou položku ILB)
   > * Aplikační brána (používá základní skladovou položku ILB)
   > * Škálovací sady (používá základní skladovou položku ILB)
   > * Clustery Service Fabric (používá základní skladovou položku ILB)
   > * SQL Server vždy zapnutý (používá základní skladovou položku ILB)
   > * Prostředí služby App Service (používá základní skladovou položku ILB)
   > * Správa rozhraní API (používá základní skladovou položku ILB)
   > * Azure AD DS (používá základní skladovou položku ILB)

Další informace naleznete v [tématu požadavky a omezení](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globálního partnerského vztahu.

### <a name="the-peering-status-is-disconnected"></a>Stav partnerského vztahu je "Odpojeno"

Chcete-li tento problém vyřešit, odstraňte partnerský vztah z obou virtuálních sítí a pak je znovu vytvořte.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Řešení potíží s připojením mezi virtuální sítí s rozbočovačem a místním prostředkem

Používá vaše síť bránu NVA nebo VPN od jiného výrobce?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Moje síť používá bránu nva nebo vpn jiného výrobce

Informace o řešení problémů s připojením, které ovlivňují bránu nva nebo vpn jiného výrobce, naleznete v následujících článcích:

* [Poradce při potížích s nva](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Řetězení služeb](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Síť nepoužívá bránu síťového virtuálního zařízení nebo sítě VPN jiného výrobce

Má virtuální síť rozbočovače a virtuální síť s paprskem bránu VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Virtuální síť rozbočovače i virtuální síť s paprskem mají bránu VPN

Použití vzdálené brány není podporováno.

Pokud virtuální síť pro paprsky už má bránu VPN, možnost **Použít vzdálenou bránu** není ve virtuální síti s paprskem podporovaná. Důvodem je omezení partnerského vztahu virtuální sítě.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Virtuální síť rozbočovače i virtuální síť s paprskem nemají bránu VPN

U připojení site-to-site nebo Azure ExpressRoute zkontrolujte z místního webu následující hlavní příčiny problémů s připojením ke vzdálené virtuální síti:

* Ve virtuální síti, která má bránu, ověřte, že je **zaškrtnuté políčko Povolit předávaný provoz.**
* Ve virtuální síti, která nemá bránu, ověřte, že je zaškrtnuté políčko **Použít vzdálenou bránu.**
* Chcete- li správce sítě zkontrolovat místní zařízení, ověřte, zda jsou všechna o tom, zda jsou všechna vzdálená adresní místo virtuální sítě přidána.

Pro připojení z bodu na pracoviště:

* Ve virtuální síti, která má bránu, ověřte, že je **zaškrtnuté políčko Povolit předávaný provoz.**
* Ve virtuální síti, která nemá bránu, ověřte, že je zaškrtnuté políčko **Použít vzdálenou bránu.**
* Stáhněte a znovu nainstalujte klientský balíček point-to-site. Trasy virtuální sítě, které jsou nově partnerským vztahem, automaticky nepřidávají trasy klientům typu point-to-site.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Poradce při potížích s problémem s připojením k síti mezi virtuálními sítěmi s paprsky ve stejné oblasti

Síť rozbočovače musí obsahovat síťové virtuální zařízení. Nakonfigurujte udr s u paprsky, které mají sadu síťového virtuálního zařízení nastavenou jako další směrování, a **povolte povolit předávaný provoz** ve virtuální síti rozbočovače.

Další informace naleznete v [tématu Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)a diskutujte o těchto požadavcích s [dodavatelem služby NVA](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) podle vašeho výběru.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Poradce při potížích s problémem s připojením k síti mezi virtuálními sítěmi s paprsky v různých oblastech

Přenos přes partnerský vztah globální virtuální sítě je teď podporován. Připojení nefunguje přes partnerský vztah globální virtuální sítě pro následující prostředky:

* Virtuální jsou virtuální mise za základní sku ILB
* Redis cache (používá základní Skladovou položku ILB)
* Aplikační brána (používá základní skladovou položku ILB)
* Škálovací sady (používá základní skladovou položku ILB)
* Clustery Service Fabric (používá základní skladovou položku ILB)
* SQL Server vždy zapnutý (používá základní skladovou položku ILB)
* Prostředí služby App Service (používá základní skladovou položku ILB)
* Správa rozhraní API (používá základní skladovou položku ILB)
* Azure AD DS (používá základní skladovou položku ILB)

Další informace naleznete v [požadavcích a omezeních](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globálního partnerského vztahu a [různých topologie VPN](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Řešení potíží s problémem s připojením k síti rozbočovače mezi webovou aplikací a virtuální sítí s paprskem

Postup řešení tohoto problému:

1. Přihlaste se k portálu Azure. 
1. Ve webové aplikaci vyberte **síť**a pak vyberte **Integrace virtuální sítě**.
1. Zkontrolujte, zda vidíte vzdálenou virtuální síť. Ručně zadejte adresní prostor vzdálené virtuální sítě **(Synchronizovat síť** a **Přidat trasy**).

Další informace najdete v těchto článcích:

* [Integrace aplikace s virtuální sítí Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Směrování VPN typu Point-to-Site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Poradce při potížích s chybovou zprávou konfigurace partnerského vztahu virtuální sítě 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Aktuální `<TENANT ID>` tenant nemá oprávnění k přístupu k propojenému předplatnému.

Chcete-li tento problém vyřešit, najdete [v tématu vytvoření partnerského vztahu – Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="not-connected"></a>Nepřipojeno

Chcete-li tento problém vyřešit, odstraňte partnerský vztah z obou virtuálních sítí a znovu je vytvořte.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Nepodařilo se peer Databricks virtuální sítě

Chcete-li tento problém vyřešit, nakonfigurujte partnerský vztah virtuální sítě v části **Azure Databricks**a zadejte cílovou virtuální síť pomocí **ID prostředků**. Další informace naleznete [v tématu Peer a Databricks virtuální sítě do vzdálené virtuální sítě](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s připojením mezi virtuálními počítači Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

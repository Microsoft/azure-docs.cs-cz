---
title: Řešení potíží s partnerskými vztahy virtuálních sítí
description: Kroky, které vám pomůžou vyřešit většinu problémů s partnerskými vztahy virtuálních sítí.
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
ms.openlocfilehash: 608f3d72aecb0c6d53325c0d082b1779822e9c80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103200534"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Řešení potíží s partnerskými vztahy virtuálních sítí

Tato příručka pro řešení potíží poskytuje kroky, které vám pomohou vyřešit většinu problémů s [partnerskými vztahy virtuálních sítí](virtual-network-peering-overview.md) .

![Diagram partnerského vztahu virtuálních sítí](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>Konfigurace partnerského vztahu virtuálních sítí mezi dvěma virtuálními sítěmi

Jsou virtuální sítě ve stejném předplatném nebo v různých předplatných?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>Virtuální sítě jsou ve stejném předplatném.

Pro konfiguraci partnerského vztahu virtuálních sítí pro virtuální sítě, které jsou ve stejném předplatném, použijte metody v následujících článcích:

* Pokud jsou virtuální sítě ve *stejné oblasti*, přečtěte si téma [vytvoření partnerského vztahu](./virtual-network-manage-peering.md#create-a-peering).
* Pokud jsou virtuální sítě v *různých oblastech*, přečtěte si téma [virtuální sítě s partnerským vztahem](./virtual-network-peering-overview.md). 

> [!Note]
> Připojení nefunguje u globálních partnerských vztahů virtuálních sítí pro následující prostředky: 
>
> * Virtuální počítače za základní SKU interního nástroje pro vyrovnávání zatížení (interního nástroje)
> * Redis Cache (používá interního nástroje SKU úrovně Basic)
> * Application Gateway V1 (používá základní interního nástroje SKU)
> * Virtual Machine Scale Sets (používá základní interního nástroje SKU)
> * Clustery Azure Service Fabric (používá základní SKU interního nástroje)
> * SQL Server Always On (používá základní SKLADOVOU položku interního nástroje)
> * Azure App Service Environment pro PowerApps (používá interního nástroje SKU úrovně Basic)
> * Azure API Management (používá základní SKLADOVOU položku interního nástroje)
> * Azure Active Directory Domain Services (Azure služba AD DS) (používá základní SKLADOVOU položku interního nástroje)

Další informace najdete v [požadavcích a omezeních](./virtual-network-peering-overview.md#requirements-and-constraints) globálního partnerského vztahu.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>Virtuální sítě jsou v různých předplatných nebo klientech služby Active Directory.

Informace o konfiguraci partnerského vztahu virtuálních sítí pro virtuální sítě v různých předplatných nebo klientech služby Active Directory najdete v tématu [vytvoření partnerského vztahu v různých předplatných pro Azure CLI](./create-peering-different-subscriptions.md#cli).

> [!Note]
> Ke konfiguraci partnerského vztahu sítě musíte mít v obou předplatných oprávnění **Přispěvatel sítě** . Další informace najdete v tématu [oprávnění k partnerským partnerům](virtual-network-manage-peering.md#permissions).

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Konfigurace partnerského vztahu virtuálních sítí s topologií hvězdicové topologie, která používá místní prostředky

![Diagram partnerského vztahu virtuální sítě s místním paprskem](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>Připojení typu Site-to-site nebo připojení ExpressRoute

Postupujte podle kroků v části [Konfigurace přenosu brány VPN pro partnerský vztah virtuální sítě](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="for-point-to-site-connections"></a>Pro připojení Point-to-site

1. Postupujte podle kroků v části [Konfigurace přenosu brány VPN pro partnerský vztah virtuální sítě](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Po navázání nebo změně partnerského vztahu virtuální sítě Stáhněte a přeinstalujte balíček Point-to-site, aby klienti Point-to-site získali aktualizované trasy k virtuální síti paprsků.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>Konfigurace partnerského vztahu virtuálních sítí pomocí virtuální sítě topologie hvězdicové a hvězdicové topologie

![Diagram partnerského vztahu virtuální sítě s virtuální síťovou paprskou](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>Virtuální sítě jsou ve stejné oblasti.


1. Ve virtuální síti rozbočovače nakonfigurujte síťové virtuální zařízení (síťové virtuální zařízení).
1. Ve virtuálních sítích paprsků musí být uživatelem definované trasy s typem dalšího segmentu směrování použité síťové virtuální zařízení.

Další informace najdete v tématu [řetězení služeb](./virtual-network-peering-overview.md#service-chaining).

> [!Note]
> Pokud potřebujete podporu k nastavení síťové virtuální zařízení, [obraťte se na dodavatele síťové virtuální zařízení](https://mskb.pkisolutions.com/kb/2984655).

Pomoc při řešení potíží s nastavením a směrováním zařízení síťové virtuální zařízení najdete v tématu [problémy se síťovým virtuálním zařízením v Azure](./virtual-network-troubleshoot-nva.md).

### <a name="the-virtual-networks-are-in-different-regions"></a>Virtuální sítě jsou v různých oblastech.

Přenos přes globální partnerský vztah virtuálních sítí se teď podporuje. Připojení nefunguje u globálních partnerských vztahů virtuálních sítí pro následující prostředky:

* Virtuální počítače za interním nástrojem pro vyrovnávání zatížení se skladovou položkou Basic
* Redis Cache (používá interního nástroje SKU úrovně Basic)
* Application Gateway (používá základní interního nástroje SKU)
* Sady škálování (používá základní interního nástroje SKU)
* Clustery Service Fabric (využívají interní nástroj pro vyrovnávání zatížení se skladovou položkou Basic)
* SQL Server Always On (používá základní SKLADOVOU položku interního nástroje)
* App Service Environment (používá základní SKLADOVOU položku interního nástroje)
* API Management (využívá interní nástroj pro vyrovnávání zatížení se skladovou položkou Basic)
* Azure služba AD DS (používá základní SKLADOVOU položku interního nástroje)

Další informace o požadavcích na globální partnerské vztahy a omezeních najdete v tématu věnovaném [partnerským vztahům virtuální sítě](./virtual-network-peering-overview.md#requirements-and-constraints).

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>Řešení potíží s připojením mezi dvěma partnerskými virtuálními sítěmi

Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu, který má potřebné [role a oprávnění](virtual-network-manage-peering.md#permissions). Vyberte virtuální síť, vyberte **partnerský vztah** a potom zaškrtněte pole **stav** . Jaký je stav?

### <a name="the-peering-status-is-connected"></a>Stav partnerského vztahu je "připojeno".

Řešení tohoto problému:

1. Podívejte se na toky síťových přenosů:

   Pomocí [řešení potíží s připojením](../network-watcher/network-watcher-connectivity-overview.md) a [toku IP Ověřte](../network-watcher/network-watcher-ip-flow-verify-overview.md) ze zdrojového virtuálního počítače k cílovému virtuálnímu počítači, jestli existuje NSG nebo udr, která způsobuje rušení v přenosných tocích.

   Pokud používáte bránu firewall nebo síťové virtuální zařízení: 
   1. Zajistěte si dokumentaci parametrů UDR, abyste je mohli po dokončení tohoto kroku obnovit.
   2. Odeberte UDR ze zdrojové podsítě virtuálních počítačů nebo síťového adaptéru, který odkazuje na síťové virtuální zařízení jako další segment směrování. Ověřte připojení ze zdrojového virtuálního počítače přímo k cíli, který přeskočí síťové virtuální zařízení. Pokud tento krok nefunguje, přečtěte si téma [Poradce při potížích s síťové virtuální zařízení](./virtual-network-troubleshoot-nva.md).

2. Proveďte trasování sítě: 
   1. Spusťte trasování sítě na cílovém virtuálním počítači. Pro Windows můžete použít **netsh**. Pro Linux použijte **TCPDump**.
   2. Spusťte **TcpPing** nebo **PsPing** ze zdroje do cílové IP adresy.

      Toto je příklad příkazu **TcpPing** : `tcping64.exe -t <destination VM address> 3389`

   3. Po dokončení **TcpPing** zastavte trasování sítě v cíli.
   4. Pokud pakety přicházejí ze zdroje, nedochází k potížím se sítí. Vyhledejte problém s konfigurací tím, že prověřte bránu firewall virtuálního počítače i aplikaci, která naslouchá na tomto portu.

   > [!Note]
   > Nemůžete se připojit k následujícím typům prostředků přes globální partnerský vztah virtuální sítě (virtuální sítě v různých oblastech):
   >
   > * Virtuální počítače za interním nástrojem pro vyrovnávání zatížení se skladovou položkou Basic
   > * Redis Cache (používá interního nástroje SKU úrovně Basic)
   > * Application Gateway (používá základní interního nástroje SKU)
   > * Sady škálování (používá základní interního nástroje SKU)
   > * Clustery Service Fabric (využívají interní nástroj pro vyrovnávání zatížení se skladovou položkou Basic)
   > * SQL Server Always On (používá základní SKLADOVOU položku interního nástroje)
   > * App Service Environment (používá základní SKLADOVOU položku interního nástroje)
   > * API Management (využívá interní nástroj pro vyrovnávání zatížení se skladovou položkou Basic)
   > * Azure služba AD DS (používá základní SKLADOVOU položku interního nástroje)

Další informace najdete v [požadavcích a omezeních](./virtual-network-peering-overview.md#requirements-and-constraints) globálního partnerského vztahu.

### <a name="the-peering-status-is-disconnected"></a>Stav partnerského vztahu je "Odpojeno".

Pokud chcete tento problém vyřešit, odstraňte partnerský vztah z obou virtuálních sítí a pak je znovu vytvořte.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>Řešení potíží s připojením mezi virtuální sítí hvězdicové centra a místním prostředkem

Používá vaše síť síťové virtuální zařízení nebo VPN Gateway třetí strany?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Moje síť používá síťové virtuální zařízení nebo bránu VPN třetí strany.

Pokud chcete řešit problémy s připojením, které mají vliv na síťové virtuální zařízení nebo bránu VPN třetí strany, přečtěte si následující články:

* [Poradce při potížích s síťové virtuální zařízení](./virtual-network-troubleshoot-nva.md)
* [Řetězení služeb](./virtual-network-peering-overview.md#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>Moje síť nepoužívá síťové virtuální zařízení nebo bránu VPN třetí strany.

Má virtuální síť centrální sítě a virtuální síť hvězdicové bránu VPN?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>Virtuální síť rozbočovače i virtuální síť paprsků mají bránu VPN.

Použití vzdálené brány se nepodporuje.

Pokud už virtuální síť rozbočovače má bránu VPN, ve virtuální síti paprsků se nepodporuje možnost **použít vzdálenou bránu** . Důvodem je omezení partnerského vztahu virtuální sítě.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>Virtuální síť rozbočovače i virtuální síť paprsků nemají bránu VPN.

U připojení typu Site-to-site nebo Azure ExpressRoute ověřte následující primární příčiny potíží s připojením k vzdálené virtuální síti z místního prostředí:

* Ve virtuální síti, která má bránu, ověřte, že je zaškrtnuté políčko **Povolení přesměrovaného přenosu** .
* Ve virtuální síti, která nemá bránu, ověřte, zda je zaškrtnuto políčko **použít vzdálenou bránu** .
* Požádejte správce sítě o kontrolu vašich místních zařízení, aby se ověřilo, že všichni mají přidaný adresní prostor vzdálené virtuální sítě.

Pro připojení Point-to-site:

* Ve virtuální síti, která má bránu, ověřte, že je zaškrtnuté políčko **Povolení přesměrovaného přenosu** .
* Ve virtuální síti, která nemá bránu, ověřte, zda je zaškrtnuto políčko **použít vzdálenou bránu** .
* Stáhněte a znovu nainstalujte balíček klienta Point-to-site. Trasy virtuální sítě, které jsou nově partnerského vztahu, automaticky nepřidává trasy k klientům Point-to-site.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Řešení problémů se síťovým připojením mezi paprsky mezi virtuálními sítěmi ve stejné oblasti

Síť rozbočovače musí zahrnovat síťové virtuální zařízení. Nakonfigurujte udr v paprskech, které mají síťové virtuální zařízení sadu jako další segment směrování, a povolte možnost **Povolit předaný provoz** ve virtuální síti rozbočovače.

Další informace najdete v tématu [řetězení služeb](./virtual-network-peering-overview.md#service-chaining)a diskuzi o těchto požadavcích s [dodavatelem síťové virtuální zařízení](https://mskb.pkisolutions.com/kb/2984655) podle vašeho výběru.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Řešení problémů se síťovým připojením centra mezi virtuálními sítěmi v různých oblastech mezi koncovými servery

Přenos přes globální partnerský vztah virtuálních sítí se teď podporuje. Připojení nefunguje u globálních partnerských vztahů virtuálních sítí pro následující prostředky:

* Virtuální počítače za interním nástrojem pro vyrovnávání zatížení se skladovou položkou Basic
* Redis Cache (používá interního nástroje SKU úrovně Basic)
* Application Gateway (používá základní interního nástroje SKU)
* Sady škálování (používá základní interního nástroje SKU)
* Clustery Service Fabric (využívají interní nástroj pro vyrovnávání zatížení se skladovou položkou Basic)
* SQL Server Always On (používá základní SKLADOVOU položku interního nástroje)
* App Service Environment (používá základní SKLADOVOU položku interního nástroje)
* API Management (využívá interní nástroj pro vyrovnávání zatížení se skladovou položkou Basic)
* Azure služba AD DS (používá základní SKLADOVOU položku interního nástroje)

Další informace najdete v [požadavcích a omezeních](./virtual-network-peering-overview.md#requirements-and-constraints) globálních partnerských vztahů a [různých topologií sítě VPN](/archive/blogs/igorpag/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2).

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Řešení potíží se síťovým připojením mezi službou Web App a paprskovou virtuální sítí s rozbočovačem

Řešení tohoto problému:

1. Přihlaste se k webu Azure Portal. 
1. Ve webové aplikaci vyberte síť a potom vyberte **Integrace virtuální** **sítě**.
1. Zkontrolujte, jestli se vám zobrazí vzdálená virtuální síť. Zadejte ručně adresní prostor vzdálené virtuální sítě (**synchronizovat síť** a **Přidat trasy**).

Další informace najdete v následujících článcích:

* [Integrace aplikace s virtuální sítí Azure](../app-service/web-sites-integrate-with-vnet.md)
* [Směrování VPN typu Point-to-Site](../vpn-gateway/vpn-gateway-about-point-to-site-routing.md)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>Řešení potíží s chybovou zprávou o konfiguraci partnerského vztahu virtuálních sítí 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Aktuální tenant `<TENANT ID>` nemá autorizaci pro přístup k propojenému předplatnému.

Pokud chcete tento problém vyřešit, přečtěte si téma [vytvoření partnerského vztahu – Azure CLI](./create-peering-different-subscriptions.md#cli).

### <a name="not-connected"></a>Nepřipojeno

Pokud chcete tento problém vyřešit, odstraňte partnerský vztah z obou virtuálních sítí a pak je znovu vytvořte.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Nepodařilo se vytvořit partnerský vztah k virtuální síti datacihly.

Chcete-li tento problém vyřešit, nakonfigurujte partnerský vztah virtuální sítě v rámci **Azure Databricks** a pak zadejte cílovou virtuální síť pomocí **ID prostředku**. Další informace najdete v tématu [virtuální síť partnera a datacihly do vzdálené virtuální sítě](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-peering#id2).

### <a name="the-remote-virtual-network-lacks-a-gateway"></a>Ve vzdálené virtuální síti chybí brána.

K tomuto problému dochází, když máte partnerské virtuální sítě od různých tenantů a později chcete nakonfigurovat `Use Remote Gateways` . Omezení Azure Portal znamená, že nemůže ověřit přítomnost brány virtuální sítě ve virtuální síti jiného tenanta.

Existují dva způsoby, jak tento problém vyřešit:

 * Odstraňte partnerské vztahy a aktivujte `Use Remote Gateways` možnost při vytváření nového partnerského vztahu.
 * K povolení použijte PowerShell nebo CLI místo Azure Portal `Use Remote Gateways` .

## <a name="next-steps"></a>Další kroky

* [Řešení potíží s připojením mezi virtuálními počítači Azure](./virtual-network-troubleshoot-connectivity-problem-between-vms.md)

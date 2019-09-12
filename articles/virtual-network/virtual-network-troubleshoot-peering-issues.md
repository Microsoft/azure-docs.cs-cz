---
title: Řešení potíží s partnerskými vztahy virtuálních sítí
description: Kroky, které vám pomůžou vyřešit většinu problémů s partnerskými vztahy virtuálních sítí.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc01a20a077a1c624ed490600db919fe3197556c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901760"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>Řešení potíží s partnerskými vztahy virtuálních sítí

Tato příručka pro řešení potíží poskytuje kroky, které vám pomohou vyřešit většinu problémů s [partnerskými vztahy virtuálních sítí](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) .

![OBRAZU](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="scenario-1-configure-virtual-network-peering-between-two-virtual-networks"></a>Scénář 1: Konfigurace partnerského vztahu virtuálních sítí mezi dvěma virtuálními sítěmi

Jsou virtuální sítě ve stejném předplatném nebo v různých předplatných?

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-subscription"></a>Typ připojení 1: Virtuální sítě jsou ve stejném předplatném.

Ke konfiguraci partnerského vztahu virtuálních sítí pro virtuální sítě, které jsou ve stejném předplatném, použijte podle potřeby metody, které jsou k dispozici v následujících článcích:

* Pokud jsou virtuální sítě ve **stejné oblasti**, postupujte podle pokynů pro [vytvoření partnerského vztahu pro virtuální sítě ve stejném předplatném](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* Pokud jsou virtuální sítě v **různých oblastech**, postupujte podle kroků pro nastavení [globálního partnerského vztahu virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).  

> [!Note]
> Připojení nebude u globálních partnerských vztahů virtuálních sítí fungovat pro následující prostředky: 
>
> * Virtuální počítače za základní SKU interního nástroje
> * Redis Cache (používá základní SKLADOVOU položku interního nástroje)
> * Application Gateway (používá základní SKLADOVOU položku interního nástroje)
> * Sady škálování (používá základní interního nástroje SKU)
> * Clustery Service Fabric (používá základní SKLADOVOU položku interního nástroje)
> * SQL Always On (používá základní SKLADOVOU položku interního nástroje)
> * Prostředí App Service (pomocného mechanismu) (používá základní SKLADOVOU položku interního nástroje)
> * API Management (používá základní SKLADOVOU položku interního nástroje)
> * Služba Azure Active Directory Domain Service (přidávají) (používá základní interního nástroje SKU)

Další informace najdete v [požadavcích a omezeních](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globálního partnerského vztahu.

### <a name="connection-type-2-the-virtual-networks-are-in-different-subscriptions-or-ad-tenants"></a>Typ připojení 2: Virtuální sítě jsou v různých předplatných nebo klientech služby AD.

Pokud chcete nakonfigurovat partnerské vztahy virtuálních sítí pro virtuální sítě v různých předplatných nebo klientech služby Active Directory, postupujte podle kroků v části [vytvoření partnerského vztahu v různých předplatných pro Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> Ke konfiguraci partnerského vztahu sítě musíte mít v obou předplatných oprávnění **Přispěvatel sítě** . Další informace najdete v tématu [oprávnění k partnerským partnerům](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering%23permissions).

## <a name="scenario-2-configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>Scénář 2: Konfigurace partnerského vztahu virtuálních sítí s topologií hvězdicové topologie, která používá místní prostředky

![OBRAZU](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="connection-type-1-for-site-to-site-connection-or-expressroute-connection"></a>Typ připojení 1: Připojení Site-to-site nebo připojení ExpressRoute

Postupujte podle kroků v části: [Nakonfigurujte přenos brány VPN pro partnerský vztah virtuálních sítí](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### <a name="connection-type-2-for-point-to-site-connections"></a>Typ připojení 2: Pro připojení Point-to-site

1. Postupujte podle kroků v části: [Nakonfigurujte přenos brány VPN pro partnerský vztah virtuálních sítí](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. Po navázání nebo změně partnerského vztahu virtuální sítě se musí balíček Point-to-site stáhnout a nainstalovat znovu, aby klienti připojení Point-to-site získali aktualizované trasy k virtuální síti paprsků.

## <a name="scenario-3-configure-virtual-network-peering-with-hub-spoke-topology-for-azure-virtual-network"></a>Scénář 3: Konfigurace partnerského vztahu virtuálních sítí pomocí topologie centra s paprsky pro Azure Virtual Network

![OBRAZU](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="connection-type-1-the-virtual-networks-are-in-the-same-region"></a>Typ připojení 1: Virtuální sítě jsou ve stejné oblasti.

Ve virtuální síti rozbočovače musíte nakonfigurovat síťové virtuální zařízení (síťové virtuální zařízení) a mít uživatelsky definované trasy s virtuálním síťovým zařízením Next Hop použitým ve virtuálních sítích paprsků. Další informace najdete v tématu [řetězení služeb](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> Pokud potřebujete podporu k nastavení síťové virtuální zařízení, [obraťte se na dodavatele síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

Pomoc při řešení potíží s nastavením a směrováním zařízení síťové virtuální zařízení najdete v tématu [problémy se síťovým virtuálním zařízením v Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### <a name="connection-type-2-the-virtual-networks-are-in-different-regions"></a>Typ připojení 2: Virtuální sítě jsou v různých oblastech.

Nyní se podporuje přenos přes globální virtuální síť VNet. Připojení nefunguje u globálních partnerských vztahů virtuálních sítí pro následující prostředky:

* Virtuální počítače za základní SKU interního nástroje
* Redis Cache (používá základní SKLADOVOU položku interního nástroje)
* Application Gateway (používá základní SKLADOVOU položku interního nástroje)
* Sady škálování (používá základní interního nástroje SKU)
* Clustery Service Fabric (používá základní SKLADOVOU položku interního nástroje)
* SQL Always On (používá základní SKLADOVOU položku interního nástroje)
* Prostředí App Service (pomocného mechanismu) (používá základní SKLADOVOU položku interního nástroje)
* API Management (používá základní SKLADOVOU položku interního nástroje)
* Služba Azure Active Directory Domain Service (přidávají) (používá základní interního nástroje SKU)

Další informace o požadavcích na globální partnerské vztahy a omezeních najdete v tématu věnovaném [partnerským vztahům virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## <a name="scenario-4-i-have-a-connectivity-issue-between-two-peered-virtual-networks"></a>Scénář 4: Mám potíže s připojením mezi dvěma partnerskými virtuálními sítěmi

Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu, který má potřebné [role a oprávnění](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions). Vyberte virtuální síť, vyberte **partnerský vztah**a potom zaškrtněte pole **stav** . Jaký je stav?

### <a name="connection-type-1-the-peering-status-shows-connected"></a>Typ připojení 1: Stav partnerského vztahu zobrazuje ' připojeno '

Pokud chcete tento problém vyřešit, postupujte podle následujících kroků:

1. Podívejte se na toky síťových přenosů:

   Pomocí [řešení potíží s připojením](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) a [toku IP Ověřte](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) ze zdrojového virtuálního počítače k cílovému virtuálnímu počítači, jestli existuje NSG nebo udr, která způsobuje rušení v přenosných tocích.

   Pokud používáte zařízení s bránou firewall nebo síťové virtuální zařízení, postupujte podle následujících kroků: 
   1. Zdokumentujte parametry UDR, abyste je mohli obnovit po dokončení tohoto kroku.
   2. Odeberte UDR ze zdrojové podsítě virtuálních počítačů nebo síťového adaptéru, který odkazuje na síťové virtuální zařízení jako další segment směrování. Ověřte připojení ze zdrojového virtuálního počítače přímo k cíli, který přeskočí síťové virtuální zařízení. Pokud tento krok funguje, přečtěte si [Poradce při potížích s síťové virtuální zařízení](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Proveďte trasování sítě: 
   1. Spusťte trasování sítě na cílovém virtuálním počítači. Pro Windows můžete použít **netsh**. Pro Linux použijte **TCPDump**.
   2. Spusťte **TcpPing** nebo **PsPing** ze zdroje do cílové IP adresy.

   * Toto je příklad příkazu **TcpPing** :`tcping64.exe -t <destination VM address> 3389`

   3. Po dokončení **TcpPing** zastavte trasování sítě v cíli.
   4. Pokud pakety přicházejí ze zdroje, nedochází k potížím se sítí. Vyhledejte problém s konfigurací tím, že prověřte bránu firewall virtuálního počítače i aplikaci, která naslouchá na tomto portu.

   > [!Note]
   > Nemůžete se připojit k následujícím typům prostředků přes globální partnerský vztah virtuální sítě (virtuální sítě v různých oblastech):
   >
   > * Virtuální počítače za základní SKU interního nástroje
   > * Redis Cache (používá základní SKLADOVOU položku interního nástroje)
   > * Application Gateway (používá základní SKLADOVOU položku interního nástroje)
   > * Sady škálování (používá základní interního nástroje SKU)
   > * Clustery Service Fabric (používá základní SKLADOVOU položku interního nástroje)
   > * SQL Always On (používá základní SKLADOVOU položku interního nástroje)
   > * Prostředí App Service (pomocného mechanismu) (používá základní SKLADOVOU položku interního nástroje)
   > * API Management (používá základní SKLADOVOU položku interního nástroje)
   > * Služba Azure Active Directory Domain Service (přidávají) (používá základní interního nástroje SKU)

Další informace najdete v [požadavcích a omezeních](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globálního partnerského vztahu.

### <a name="connection-type-2-the-peering-status-shows-disconnected"></a>Typ připojení 2: Stav partnerského vztahu zobrazuje "Odpojeno"

Je nutné odstranit partnerské vztahy z obou virtuální sítě a znovu je vytvořit.

## <a name="scenario-5-i-have-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-on-premises-resource"></a>Scénář 5: Mám potíže s připojením mezi virtuální sítí hvězdicové centra a místním prostředkem

Používáte síťové virtuální zařízení nebo bránu VPN od jiného výrobce?

### <a name="connection-type-1-my-network-uses-a-third-party-nva-or-vpn-gateway"></a>Typ připojení 1: Moje síť používá síťové virtuální zařízení nebo bránu VPN třetí strany.

Pokud chcete řešit problémy s připojením, které mají vliv na síťové virtuální zařízení nebo bránu VPN třetí strany, přečtěte si následující články:

* [Poradce při potížích s síťové virtuální zařízení](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Řetězení služeb](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="connection-type-2-my-network-does-not-a-third-party-nva-or-vpn-gateway"></a>Typ připojení 2: Moje síť nepředstavuje síťové virtuální zařízení nebo bránu VPN třetí strany.

Mají virtuální sítě rozbočovač i paprsek bránu sítě VPN?

#### <a name="both-the-hub-and-spoke-virtual-networks-have-a-vpn-gateway"></a>Virtuální sítě rozbočovač i paprsek mají bránu VPN.

Použití vzdálené brány se nepodporuje.

Z důvodu omezení partnerského vztahu virtuálních sítí se **používání vzdálené brány** u virtuální sítě rozbočovače nepodporuje, pokud virtuální síť rozbočovače už má bránu VPN.

#### <a name="both-the-hub-and-spoke-virtual-networks-dont-have-a-vpn-gateway"></a>Virtuální sítě rozbočovač i paprsků nemají bránu VPN.

U připojení typu Site-to-site nebo ExpressRoute ověřte tyto primární příčiny potíží s připojením k vzdálené virtuální síti z místního prostředí.

* Ověřte, že je ve virtuální síti s bránou zaškrtnuto políčko **Povolení přesměrovaného přenosu** .
* Ověřte, zda je ve virtuální síti, která nemá bránu, zaškrtnuto políčko **použít vzdálenou bránu** .
* Požádejte správce sítě o kontrolu vašich místních zařízení, aby se ověřilo, že všichni mají přidaný adresní prostor vzdálené virtuální sítě.

Pro připojení Point-to-site:

* Ověřte, že je ve virtuální síti s bránou zaškrtnuto políčko **Povolení přesměrovaného přenosu** .
* Ověřte, zda je ve virtuální síti, která nemá bránu, zaškrtnuto políčko **použít vzdálenou bránu** .
* Stáhněte a nainstalujte klientský balíček Point-to-site znovu. Trasy virtuální sítě, které jsou nově partnerského vztahu, automaticky nepřidává trasy k klientům Point-to-site.

## <a name="scenario-6-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>Scénář 6: Mám potíže se síťovým připojením mezi koncovými virtuálními sítěmi ve stejné oblasti

Musíte mít síťové virtuální zařízení v síti rozbočovače, nakonfigurovat udr v paprskech, které mají síťové virtuální zařízení sadu jako další segment směrování, a povolit **předávání přenosů** ve virtuální síti rozbočovače.

Další informace najdete v tématu [řetězení služeb](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)a diskuzi o těchto požadavcích s [dodavatelem síťové virtuální zařízení](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) podle vašeho výběru.

## <a name="scenario-7-i-have-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>Scénář 7: Mám potíže se síťovým připojením mezi koncovými virtuálními sítěmi v různých oblastech

Nyní se podporuje přenos přes globální virtuální síť VNet. Připojení nebude u globálních partnerských vztahů virtuálních sítí fungovat pro následující prostředky:

* Virtuální počítače za základní SKU interního nástroje
* Redis Cache (používá základní SKLADOVOU položku interního nástroje)
* Application Gateway (používá základní SKLADOVOU položku interního nástroje)
* Sady škálování (používá základní interního nástroje SKU)
* Clustery Service Fabric (používá základní SKLADOVOU položku interního nástroje)
* SQL Always On (používá základní SKLADOVOU položku interního nástroje)
* Prostředí App Service (pomocného mechanismu) (používá základní SKLADOVOU položku interního nástroje)
* API Management (používá základní SKLADOVOU položku interního nástroje)
* Služba Azure Active Directory Domain Service (přidávají) (používá základní interního nástroje SKU)

Další informace najdete v [požadavcích a omezeních](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) globálních partnerských vztahů a [různých topologiích sítě VPN](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## <a name="scenario-8-i-have-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>Scénář 8: Mám potíže se síťovým připojením mezi webovou aplikací a koncovou virtuální sítí.

Pokud chcete tento problém vyřešit, postupujte podle těchto kroků:

1. Přihlaste se k Azure Portal. Přejít do webové aplikace, vyberte sítě a potom vyberte **Integrace virtuální** **sítě**.
2. Zkontrolujte, jestli se vám zobrazí vzdálená virtuální síť. Ručně zadejte adresní prostor vzdálené virtuální sítě (**synchronizovat síť** a **Přidat trasy**).

Další informace najdete v následujících článcích:

* [Integrace aplikace s Virtual Network Azure](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [Směrování sítě VPN typu Point-to-site](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="scenario-9-i-receive-an-error-when-configuring-virtual-network-peering"></a>Scénář 9: Při konfiguraci partnerského vztahu virtuálních sítí se zobrazí chyba

### <a name="error-1-current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>Chyba 1: Aktuální tenant `<TENANT ID>` nemá autorizaci pro přístup k propojenému předplatnému.

Pokud chcete tento problém vyřešit, postupujte podle kroků v části [vytvoření partnerského vztahu – Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### <a name="error-2-not-connected"></a>Chyba 2: Nepřipojeno

Je nutné odstranit partnerské vztahy z obou virtuální sítě a znovu je vytvořit.

### <a name="error-3-failed-to-peer-a-databricks-virtual-network"></a>Chyba 3: Nepodařilo se vytvořit partnerský vztah k virtuální síti datacihly.

Pokud chcete tento problém vyřešit, nakonfigurujte partnerský vztah virtuální sítě z okna **Azure Databricks** a pak zadejte cílovou virtuální síť pomocí **ID prostředku**. Další informace najdete v tématu [virtuální síť partnera a datacihly do vzdálené virtuální sítě](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## <a name="next-steps"></a>Další postup

* [Řešení potíží s připojením mezi virtuálními počítači Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
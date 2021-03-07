---
title: Konfigurace pravidel NAT sítě VPN pro bránu
titleSuffix: Azure Virtual WAN
description: Přečtěte si, jak nakonfigurovat pravidla překladu adres (NAT) pro bránu VPN VWAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 03/05/2021
ms.author: cherylmc
ms.openlocfilehash: 6fbee31f015953bd7e65648ea273e3ca84686115
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431186"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Nakonfigurujte pravidla překladu adres (NAT) pro virtuální síť WAN VPN Gateway – Preview

> [!IMPORTANT]
> Pravidla překladu adres (NAT) jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete nakonfigurovat virtuální síť WAN VPN Gateway se statickými pravidly pro překlad adres 1:1. Pravidlo překladu adres (NAT) poskytuje mechanismus pro nastavení překladu IP adres 1:1. Překlad adres (NAT) se dá použít k propojení dvou sítí IP, které mají nekompatibilní nebo překrývající se IP adresy. Typickým scénářem jsou větve s překrývajícími se IP adresami, které chtějí získat přístup k prostředkům virtuální sítě Azure.

Tato konfigurace používá tabulku toků ke směrování provozu z externí (hostitelské) IP adresy na interní IP adresu přidruženou ke koncovému bodu ve virtuální síti (virtuální počítač, počítač, kontejner atd.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagram znázorňující architekturu":::

## <a name="configure-nat-rules"></a><a name="rules"></a>Konfigurovat pravidla překladu adres (NAT)

Pravidla překladu adres (NAT) můžete kdykoli nakonfigurovat a zobrazit v nastavení služby VPN Gateway.

   :::image type="content" source="./media/nat-rules-vpn-gateway/edit-rules.png" alt-text="Snímek obrazovky zobrazující pravidla úprav":::

1. Přejděte do svého virtuálního centra.
1. Vyberte **síť VPN (site-to-site)**.
1. Vyberte **pravidla NAT (Upravit)**.
1. Na stránce **Upravit pravidlo překladu adres (NAT)** můžete **Přidat nebo upravit nebo odstranit** pravidlo překladu adres (NAT) pomocí následujících hodnot:

   * **Název:** Jedinečný název pro pravidlo překladu adres (NAT).
   * **Zadejte:** Tras. Statické NAT typu 1:1 vytváří vztah 1:1 mezi interní adresou a externí adresou.
   * **Režim:** IngressSnat nebo EgressSnat.  
      * Režim IngressSnat (označuje se taky jako zdrojové NAT příchozího přenosu dat) se vztahuje na provoz přicházející do brány VPN typu Site-to-site v centru Azure.
      * Režim EgressSnat (označovaný také jako výstupní zdroj překladu adres) se vztahuje na provoz, který opouští bránu VPN typu Site-to-site v centru Azure.
   * **InternalMapping:** Předpona adresy v rozsahu zdrojových IP adres v síti v síti, která bude namapována na sadu externích IP adres. Jinými slovy, váš rozsah předpon adres pro předběžné NAT.
   * **ExternalMapping:** Předpona adresy cílových IP adres na vnější síti, na kterou budou zdrojové IP adresy namapovány. Jinými slovy, rozsah předpon adres po překladu adres (NAT).
   * **Propojit připojení:** Prostředek připojení, který prakticky připojuje síť VPN k bráně VPN typu Site-to-site v centru Azure.

### <a name="configuration-considerations"></a><a name="considerations"></a>Pokyny pro konfiguraci

* Velikost podsítě pro interní i externí mapování musí být stejná pro NAT statických adres 1:1.
* Nezapomeňte Upravit lokalitu VPN v Azure Portal, aby se v poli privátní adresní prostor přidaly předpony **ExternalMapping** . V současné době musí weby s povoleným protokolem BGP zajistit, aby místní oznámení protokolu BGP (nastavení protokolu BGP zařízení) zahrnovalo položku pro předpony externích mapování.

## <a name="examples-and-verification"></a><a name="examples"></a>Příklady a ověřování

### <a name="ingress-mode-nat"></a>NAT v režimu příchozího přenosu dat

Pravidla překladu adres (NAT) v režimu příchozího přenosu dat se aplikují na pakety, které vstupují do Azure přes virtuální síť WAN VPN Gateway. V tomto scénáři chcete propojit dvě větve sítě VPN typu Site-to-site do Azure. Síť VPN site 1 se připojuje přes Link1 a VPN site 2 se připojuje prostřednictvím linku 2. Každá lokalita má adresní prostor 192.169.1.0/24.

Následující diagram znázorňuje plánovaný konečný výsledek:

:::image type="content" source="./media/nat-rules-vpn-gateway/ingress.png" alt-text="Diagram ukazující překlad adres (NAT) do režimu příchozího přenosu dat":::

1. Zadejte pravidlo překladu adres (NAT).

   Určete pravidlo překladu adres (NAT), abyste zajistili, že brána VPN typu Site-to-site může rozlišovat mezi dvěma větvemi s překrývajícími se adresními prostory (například 192.168.1.0/24). V tomto příkladu se zaměříme na Link1 pro VPN site 1.

   Následující pravidlo překladu adres (NAT) je možné nastavit a přidružit k propojení 1 jedné z větví. Vzhledem k tomu, že se jedná o statické pravidlo překladu adres (NAT), adresní prostory InternalMapping a ExternalMapping obsahují stejný počet IP adres.

   * **Název:** IngressRule01
   * **Zadejte:** Tras
   * **Režim:** IngressSnat
   * **InternalMapping:** 192.168.1.0/24
   * **ExternalMapping:** 10.1.1.0/24
   * **Propojit připojení:** Odkaz 1

1. Inzerovat správné ExternalMapping.

   V tomto kroku zajistěte, aby brána VPN typu Site-to-site inzerovala správný adresní prostor ExternalMapping do zbytku vašich prostředků Azure. V závislosti na tom, jestli je protokol BGP povolený, existují různé pokyny.

   **Příklad 1: protokol BGP je povolený.**

   * Zajistěte, aby byl místní mluvčí BGP umístěný v síti VPN 1 nakonfigurovaný tak, aby inzeroval adresní prostor 10.1.1.0/24.
   * V této verzi Preview musí weby s povoleným protokolem BGP zajistit, aby místní oznámení protokolu BGP (nastavení protokolu BGP zařízení) zahrnovalo zadání pro předpony externích mapování.

   **Příklad 2: protokol BGP není povolený.**

   * Přejděte do prostředku virtuálního rozbočovače, který obsahuje bránu VPN typu Site-to-site. Na stránce virtuální rozbočovač v části **připojení** vyberte **VPN (site-to-site)**.
   * Vyberte lokalitu VPN, která je připojená k virtuální síti WAN prostřednictvím propojení 1. Jako soukromý adresní prostor pro lokalitu VPN vyberte **Upravit web** a zadejte 10.1.1.0/24.

     :::image type="content" source="./media/nat-rules-vpn-gateway/edit-site.png" alt-text="Snímek obrazovky se stránkou upravit síť VPN":::

### <a name="packet-flow"></a>Tok paketů

V tomto příkladu chce místní zařízení dosáhnout hvězdicové virtuální sítě. Tok paketů je následující: s překlady NAT tučným písmem.

1. Provoz z místního prostředí se iniciuje.
   * Zdrojová IP adresa: **192.168.1.1**
   * Cílová IP adresa: 30.0.0.1
1. Provoz vstoupí do brány Site-to-site a je přeložen pomocí pravidla překladu adres (NAT) a pak se pošle na paprsku.
   * Zdrojová IP adresa: **10.1.1.1**
   * Cílová IP adresa: 30.0.0.1
1. Je zahájena odpověď od paprsku.
   * Zdrojová IP adresa: 30.0.0.1
   * Cílová IP adresa: **10.1.1.1**
1. Provoz vstoupí do brány VPN typu Site-to-site a překlad je obrácený a odeslaný do místního prostředí.
   * Zdrojová IP adresa: 30.0.0.1
   * Cílová IP adresa: **192.168.1.1**

### <a name="verification-checks"></a>Kontroly ověřování

V této části se dozvíte, jak ověřit, jestli je konfigurace správně nastavená.

#### <a name="validate-defaultroutetable-rules-and-routes"></a>Ověřit DefaultRouteTable, pravidla a trasy

Větve ve virtuální síti WAN přidružuje k **DefaultRouteTable**, což znamená, že všechna připojení větví se seznámí s trasami, které jsou vyplněny v rámci DefaultRouteTable. V platných trasách DefaultRouteTable se zobrazí pravidlo překladu adres (NAT) s předponou externích mapování.

Příklad:

* **Předpona:** 10.1.1.0/24  
* **Typ dalšího segmentu směrování:** VPN_S2S_Gateway
* **Další segment směrování:** Prostředek VPN_S2S_Gateway

#### <a name="validate-address-prefixes"></a>Ověření předpon adres

Tento příklad se vztahuje na prostředky ve virtuálních sítích, které jsou přidruženy k DefaultRouteTable.

**Platné trasy** k síťovým rozhraním (nic) libovolného virtuálního počítače, který je umístěn ve virtuální síti rozbočovače připojené k virtuální síti WAN, by měly obsahovat také předpony adres **ExternalMapping** pravidel NAT.

#### <a name="validate-bgp-advertisements"></a>Ověřit inzerování protokolu BGP

Pokud máte protokol BGP nakonfigurovaný na připojení k síti VPN, zkontrolujte místní mluvčí BGP a ujistěte se, že je inzerování položky pro externí mapování předpon.

## <a name="next-steps"></a>Další kroky

Další informace o konfiguracích site-to-site najdete v tématu [Konfigurace připojení typu Site-to-site virtuální sítě WAN](virtual-wan-site-to-site-portal.md).

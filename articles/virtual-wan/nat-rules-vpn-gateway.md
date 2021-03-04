---
title: Konfigurace pravidel NAT sítě VPN pro bránu
titleSuffix: Azure Virtual WAN
description: Přečtěte si, jak nakonfigurovat pravidla překladu adres (NAT) pro bránu VPN VWAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 02/17/2021
ms.author: cherylmc
ms.openlocfilehash: a31b3718eb1baa32aef39474383924efe8cf93b6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101744965"
---
# <a name="configure-nat-rules-for-your-virtual-wan-vpn-gateway---preview"></a>Nakonfigurujte pravidla překladu adres (NAT) pro virtuální síť WAN VPN Gateway – Preview

> [!IMPORTANT]
> Pravidla překladu adres (NAT) jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete nakonfigurovat virtuální síť WAN VPN Gateway se statickými pravidly pro překlad adres 1:1. Pravidlo překladu adres (NAT) poskytuje mechanismus pro nastavení překladu IP adres 1:1. Překlad adres (NAT) se dá použít k propojení dvou sítí IP, které mají nekompatibilní nebo překrývající se IP adresy. Typickým scénářem jsou větve s překrývajícími se IP adresami, které chtějí získat přístup k prostředkům virtuální sítě Azure.

Tato konfigurace používá tabulku toků ke směrování provozu z externí (hostitelské) IP adresy na interní IP adresu přidruženou ke koncovému bodu ve virtuální síti (virtuální počítač, počítač, kontejner atd.).

   :::image type="content" source="./media/nat-rules-vpn-gateway/diagram.png" alt-text="Diagram znázorňující architekturu":::

## <a name="configure-and-view-rules"></a><a name="view"></a>Konfigurace a zobrazení pravidel

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

### <a name="configuration-considerations"></a>Pokyny pro konfiguraci

* Velikost podsítě pro interní i externí mapování musí být stejná pro NAT statických adres 1:1.
* Nezapomeňte Upravit lokalitu VPN v Azure Portal, aby se v poli privátní adresní prostor přidaly předpony **ExternalMapping** . V současné době musí weby s povoleným protokolem BGP zajistit, aby místní oznámení protokolu BGP (nastavení protokolu BGP zařízení) zahrnovalo položku pro předpony externích mapování.

## <a name="next-steps"></a>Další kroky

Další informace o konfiguracích site-to-site najdete v tématu [Konfigurace připojení typu Site-to-site virtuální sítě WAN](virtual-wan-site-to-site-portal.md).

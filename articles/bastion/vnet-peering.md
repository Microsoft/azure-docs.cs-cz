---
title: Architektura VNet peering a Azure bastionu
description: V tomto článku se dozvíte, jak se pro připojení k virtuálním počítačům dá použít partnerský vztah virtuálních sítí a Azure bastionu společně.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: cherylmc
ms.openlocfilehash: ad3cf33dacffc8bcda9376857206784afedf7139
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2020
ms.locfileid: "94361979"
---
# <a name="vnet-peering-and-azure-bastion-preview"></a>VNet peering a Azure bastionu (Preview)

Partnerský vztah Azure bastionu a VNet se dá použít společně. Při konfiguraci partnerského vztahu virtuálních sítí nemusíte v každé partnerské virtuální síti nasazovat službu Azure bastionu. To znamená, že pokud máte hostitele Azure bastionu nakonfigurovaný v jedné virtuální síti (VNet), dá se použít k připojení k virtuálním počítačům nasazeným v partnerské virtuální síti bez nasazení dalšího hostitele bastionu. Další informace o partnerském vztahu virtuálních sítí najdete v tématu [informace o partnerském vztahu virtuálních sítí](../virtual-network/virtual-network-peering-overview.md).

Azure bastionu pracuje s následujícími typy partnerských vztahů:

* **Partnerský vztah virtuální sítě:** Propojte virtuální sítě v rámci stejné oblasti Azure.
* **Globální partnerský vztah virtuální sítě:** Propojení virtuálních sítí napříč oblastmi Azure.

## <a name="architecture"></a>Architektura

Když nakonfigurujete partnerský vztah virtuálních sítí, Azure bastionu se dá nasadit v topologiích hvězdicové nebo celé sítě. Nasazení Azure bastionu je vázané na virtuální síť, ne pro předplatné/účet nebo virtuální počítač.

Jakmile ve své virtuální síti zřídíte službu Azure bastionu, bude prostředí RDP/SSH dostupné pro všechny vaše virtuální počítače ve stejné virtuální síti i v partnerských virtuální sítě. To znamená, že můžete konsolidovat nasazení bastionu do jediné virtuální sítě a pořád oslovit virtuální počítače nasazené ve virtuální síti s partnerským vztahem a centralizovat celkové nasazení.

:::image type="content" source="./media/vnet-peering/design.png" alt-text="Diagram návrhu a architektury":::

Na tomto obrázku vidíte architekturu nasazení Azure bastionu v modelu hvězdicové architektury. V tomto diagramu uvidíte následující konfiguraci:

* Hostitel bastionu je nasazený ve virtuální síti centrálního centra.
* Je nasazena centralizovaná skupina zabezpečení sítě (NSG).
* Veřejná IP adresa se na virtuálním počítači Azure nevyžaduje.

**Uvedené**

1. Připojte se k Azure Portal pomocí libovolného prohlížeče HTML5.
1. Vyberte virtuální počítač, ke kterému se chcete připojit.
1. Azure bastionu se v partnerské virtuální síti plynule detekuje.
1. Jediným kliknutím se v prohlížeči otevře relace RDP/SSH. Omezení souběžných relací RDP a SSH najdete v tématu [relace RDP a SSH](bastion-faq.md#limits).

   :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Připojit":::

   Další informace o připojení k virtuálnímu počítači přes Azure bastionu najdete v těchto tématech:

   * [Připojte se k virtuálnímu počítači-RDP](bastion-connect-vm-rdp.md).
   * [Připojte se k virtuálnímu počítači – SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md).
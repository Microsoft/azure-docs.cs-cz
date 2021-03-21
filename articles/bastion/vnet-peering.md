---
title: Architektura VNet peering a Azure bastionu
description: V tomto článku se dozvíte, jak se pro připojení k virtuálním počítačům dá použít partnerský vztah virtuálních sítí a Azure bastionu společně.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: f72a3739fac1e7d6afdafd2676ea6fcefe847b2a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101710579"
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
2. Ujistěte se, že máte oprávnění **ke čtení** pro cílový virtuální počítač i pro partnerský virtuální síť. Dále ověřte v části IAM, ke které máte přístup pro čtení následujících prostředků:
   * Role čtenáře na virtuálním počítači.
   * Role čtecího zařízení na síťové kartě s privátní IP adresou virtuálního počítače
   * Role čtecího modulu v prostředku Azure bastionu
   * Role čtecího modulu na Virtual Network (není nutné, pokud není k dispozici žádná Partnerská virtuální síť).
3. Pokud se chcete podívat na bastionu v rozevírací nabídce **připojit** , musíte vybrat předplatné, ke kterému máte přístup v **předplatném > globální předplatné**.
4. Vyberte virtuální počítač, ke kterému se chcete připojit.
5. Azure bastionu se v partnerské virtuální síti plynule detekuje.
6. Jediným kliknutím se v prohlížeči otevře relace RDP/SSH. Omezení souběžných relací RDP a SSH najdete v tématu [relace RDP a SSH](bastion-faq.md#limits).

  :::image type="content" source="../../includes/media/bastion-vm-rdp/connect-vm.png" alt-text="Připojit":::

   Další informace o připojení k virtuálnímu počítači přes Azure bastionu najdete v těchto tématech:

   * [Připojte se k virtuálnímu počítači-RDP](bastion-connect-vm-rdp.md).
   * [Připojte se k virtuálnímu počítači – SSH](bastion-connect-vm-ssh.md).

## <a name="faq"></a>Časté otázky

[!INCLUDE [FAQ for VNet peering](../../includes/bastion-faq-peering-include.md)]

## <a name="next-steps"></a>Další kroky

Přečtěte si [Nejčastější dotazy k bastionu](bastion-faq.md).

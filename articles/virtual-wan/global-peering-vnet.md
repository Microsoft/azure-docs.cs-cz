---
title: Konfigurace globálního partnerského vztahu VNet pro Azure Virtual WAN | Microsoft Docs
description: Připojte virtuální síť v jiné oblasti k virtuálnímu rozbočovači sítě WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588223"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Konfigurace globálního partnerského vztahu virtuálních sítí (VNet mezi oblastmi) pro virtuální síť WAN

Virtuální síť můžete připojit v jiné oblasti k virtuálnímu rozbočovači WAN.

## <a name="before-you-begin"></a>Než začnete

Ověřte, že splňujete následující kritéria:

* Virtuální síť (paprsk) mezi oblastmi není připojená k jinému virtuálnímu centru WAN. Paprsky je možné připojit pouze k jednomu virtuálnímu rozbočovači.
* Virtuální síť (paprsek) neobsahuje bránu virtuální sítě (třeba bránu virtuální sítě Azure VPN Gateway nebo ExpressRoute). Pokud virtuální síť obsahuje bránu virtuální sítě, musíte bránu odebrat předtím, než propojíte virtuální síť rozbočovače s centrem.

## <a name="register"></a>Registrace této funkce

Pro tuto funkci se můžete zaregistrovat pomocí PowerShellu. Pokud v následujícím příkladu vyberete "vyzkoušet si", cloudové prostředí Azure se otevře a nebudete muset místně nainstalovat rutiny PowerShellu do vašeho počítače. V případě potřeby můžete změnit odběry pomocí rutiny Select-AzSubscription-SubscriptionId <subid>.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify"></a>Ověřit registraci

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="hub"></a>Připojení virtuální sítě k centru

V tomto kroku vytvoříte připojení partnerského vztahu mezi centrem a virtuální sítí mezi oblastmi. Uvedený postup zopakujte pro všechny virtuální sítě, které chcete připojit.

1. Na stránce vaší virtuální sítě WAN klikněte na **Připojení k virtuální síti**.
2. Na stránce připojení k virtuální síti klikněte na **+Add connection** (Přidat připojení).
3. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nesmí mít existující bránu virtuální sítě.
4. Kliknutím na **OK** vytvořte partnerské propojení.

## <a name="next-steps"></a>Další kroky

Další informace o virtuální síti WAN najdete v tématu [Přehled virtuálních sítí WAN](virtual-wan-about.md).
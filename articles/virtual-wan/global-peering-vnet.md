---
title: Konfigurace globálního partnerského vztahu virtuální sítě pro virtuální síť Azure | Dokumenty společnosti Microsoft
description: Připojte virtuální síť v jiné oblasti k rozbočovači Virtuální WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73588223"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Konfigurace globálního partnerského vztahu virtuální sítě (virtuální síť mezi oblastmi) pro virtuální síť WAN

Virtuální síť v jiné oblasti můžete připojit k rozbočovači Virtuální WAN.

## <a name="before-you-begin"></a>Než začnete

Ověřte, zda jste splnili následující kritéria:

* Virtuální síť (paprsková) mezi oblastmi není připojená k jinému virtuálnímu rozbočovači WAN. Paprsek lze připojit pouze k jednomu virtuálnímu rozbočovači.
* Virtuální síť (spoke) neobsahuje bránu virtuální sítě (například bránu Azure VPN gateway nebo bránu virtuální sítě ExpressRoute). Pokud virtuální síť obsahuje bránu virtuální sítě, musíte bránu před připojením virtuální sítě s paprskem k rozbočovači odebrat.

## <a name="register-this-feature"></a><a name="register"></a>Registrace této funkce

Pro tuto funkci se můžete zaregistrovat pomocí prostředí PowerShell. Pokud vyberete "Try It" z níže uvedeného příkladu, Azure Cloud-Shell se otevře a nebudete muset instalovat rutiny Prostředí PowerShell místně do počítače. V případě potřeby můžete změnit odběry pomocí rutiny "Select-AzSubscription -SubscriptionId <subid>".

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Ověření registrace

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Připojení virtuální sítě k rozbočovači

V tomto kroku vytvoříte připojení partnerského vztahu mezi rozbočovačem a virtuální sítí mezi oblastmi. Uvedený postup zopakujte pro všechny virtuální sítě, které chcete připojit.

1. Na stránce vaší virtuální sítě WAN klikněte na **Připojení k virtuální síti**.
2. Na stránce připojení k virtuální síti klikněte na **+Add connection** (Přidat připojení).
3. Na stránce **Add connection** (Přidat připojení) zadejte údaje do následujících polí:

    * **Connection name** (Název připojení) – zadejte název připojení.
    * **Hubs** (Rozbočovače) – vyberte rozbočovač, který chcete k tomuto připojení přidružit.
    * **Subscription** (Předplatné) – ověřte předplatné.
    * **Virtual network** (Virtuální síť) – vyberte virtuální síť, kterou chcete připojit k tomuto rozbočovači. Virtuální síť nesmí mít existující bránu virtuální sítě.
4. Kliknutím na **OK** vytvořte partnerské propojení.

## <a name="next-steps"></a>Další kroky

Další informace o virtuální paměti WAN najdete v [tématu Přehled virtuální sítě WAN](virtual-wan-about.md).
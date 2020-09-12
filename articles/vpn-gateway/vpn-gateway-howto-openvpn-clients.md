---
title: Jak nakonfigurovat OpenVPN klienty pro Azure VPN Gateway | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat OpenVPN pro Azure VPN Gateway pro klienty operačního systému Windows, Linux a Mac.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4be903fa17ce95e96c82241249b421e1d794c80f
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89435775"
---
# <a name="configure-openvpn-clients-for-azure-vpn-gateway"></a>Konfigurace klientů OpenVPN pro Azure VPN Gateway

Tento článek vám pomůže nakonfigurovat **klienty &reg; protokolu OpenVPN** .

## <a name="before-you-begin"></a>Než začnete

Ověřte, že jste dokončili postup konfigurace OpenVPN pro vaši bránu VPN. Podrobnosti najdete v tématu [Konfigurace OpenVPN pro Azure VPN Gateway](vpn-gateway-howto-openvpn.md).

[!INCLUDE [configuration steps](../../includes/vpn-gateway-vwan-config-openvpn-clients.md)]

## <a name="next-steps"></a>Další kroky

Pokud chcete, aby klienti VPN měli přístup k prostředkům v jiné virtuální síti, postupujte podle pokynů v článku [VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) a nastavte připojení typu VNet-to-VNet. Nezapomeňte povolit protokol BGP u bran a připojení, jinak přenos nebude pokračovat.

**"OpenVPN" je ochranná známka společnosti OpenVPN Inc.**

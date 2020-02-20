---
title: 'Povolit MFA pro uživatele sítě VPN: ověřování Azure AD'
description: Povolení služby Multi-Factor Authentication pro uživatele sítě VPN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: 13f012af95bb2b6098317e59e5293fb72804a6a6
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471502"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Povolit Azure Multi-Factor Authentication (MFA) pro uživatele sítě VPN

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enableauth"></a>Povolit ověřování

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="enablesign"></a>Konfigurovat nastavení přihlášení

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="peruser"></a>Možnost 1 – přístup na uživatele

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="conditional"></a>Možnost 2 – podmíněný přístup

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Další kroky

Pokud se chcete připojit k virtuální síti, musíte vytvořit a nakonfigurovat profil klienta VPN. Viz [Konfigurace ověřování Azure AD pro připojení Point-to-site k Azure](virtual-wan-point-to-site-azure-ad.md).

---
title: 'Povolit MFA pro uživatele sítě VPN: ověřování Azure AD'
description: Povolení služby Multi-Factor Authentication pro uživatele sítě VPN
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 7e29aafe55c8007182c6183d53d988d8a18dd82c
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424970"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Povolit Azure Multi-Factor Authentication (MFA) pro uživatele sítě VPN

[!INCLUDE [overview](../../includes/vpn-gateway-vwan-openvpn-enable-mfa-overview.md)]

## <a name="enable-authentication"></a><a name="enableauth"></a>Povolit ověřování

[!INCLUDE [enable authentication](../../includes/vpn-gateway-vwan-openvpn-enable-auth.md)]

## <a name="configure-sign-in-settings"></a><a name="enablesign"></a>Konfigurovat nastavení přihlášení

[!INCLUDE [sign in](../../includes/vpn-gateway-vwan-openvpn-sign-in.md)]

## <a name="option-1---per-user-access"></a><a name="peruser"></a>Možnost 1 – přístup na uživatele

[!INCLUDE [per user](../../includes/vpn-gateway-vwan-openvpn-per-user.md)]

## <a name="option-2---conditional-access"></a><a name="conditional"></a>Možnost 2 – podmíněný přístup

[!INCLUDE [conditional access](../../includes/vpn-gateway-vwan-openvpn-conditional.md)]

## <a name="next-steps"></a>Další kroky

Pokud se chcete připojit k virtuální síti, musíte vytvořit a nakonfigurovat profil klienta VPN. Viz téma [Konfigurace klienta VPN pro připojení P2S VPN](openvpn-azure-ad-client.md).
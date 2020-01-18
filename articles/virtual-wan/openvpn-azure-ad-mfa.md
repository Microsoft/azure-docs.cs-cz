---
title: 'Povolit MFA pro uživatele sítě VPN: ověřování Azure AD'
description: Povolení služby Multi-Factor Authentication pro uživatele sítě VPN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: alzam
ms.openlocfilehash: fcb60e80189da89b3f634c14582be606307536e6
ms.sourcegitcommit: d29e7d0235dc9650ac2b6f2ff78a3625c491bbbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76166705"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Povolit Azure Multi-Factor Authentication (MFA) pro uživatele sítě VPN

Pokud chcete, aby se uživatelé před udělením přístupu zobrazovali k druhému faktoru ověřování, můžete pro vašeho tenanta Azure AD nakonfigurovat službu Azure Multi-Factor Authentication (MFA). Kroky v tomto článku vám pomůžou zajistit požadavek na dvoustupňové ověřování.

## <a name="prereq"></a>Požadovaných součástí

Předpokladem pro tuto konfiguraci je konfigurovaný tenant Azure AD pomocí kroků v části [Konfigurace tenanta](openvpn-azure-ad-tenant.md).

[!INCLUDE [MFA steps](../../includes/vpn-gateway-vwan-openvpn-azure-ad-mfa.md)]

## <a name="enablesign"></a>Konfigurovat nastavení přihlášení

Na stránce **Azure VPN – vlastnosti** nakonfigurujte nastavení přihlášení.

1. Nastavte možnost **Povolit uživatelům přihlášení?** na **Ano**. To umožní všem uživatelům v tenantovi služby AD se úspěšně připojit k síti VPN.
2. Nastavit **přiřazení uživatele jako povinné?** Pokud chcete omezit přihlašování jenom na uživatele, kteří mají oprávnění k Azure VPN, nastavte **hodnotu Ano** .
3. Uložte provedené změny.

   ![Oprávnění](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Další kroky

Pokud se chcete připojit k virtuální síti, musíte vytvořit a nakonfigurovat profil klienta VPN. Viz [Konfigurace ověřování Azure AD pro připojení Point-to-site k Azure](virtual-wan-point-to-site-azure-ad.md).

---
title: 'Povolit MFA pro uživatele sítě VPN: ověřování Azure AD'
description: Povolení služby Multi-Factor Authentication pro uživatele sítě VPN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: alzam
ms.openlocfilehash: 9329ac33a771c829b8d67670bc07d9a353c58ff1
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930581"
---
# <a name="enable-azure-multi-factor-authentication-mfa-for-vpn-users"></a>Povolit Azure Multi-Factor Authentication (MFA) pro uživatele sítě VPN

Pokud chcete, aby se uživatelé před udělením přístupu zobrazovali k druhému faktoru ověřování, můžete pro vašeho tenanta Azure AD nakonfigurovat službu Azure Multi-Factor Authentication (MFA). Kroky v tomto článku vám pomůžou zajistit požadavek na dvoustupňové ověřování.

## <a name="prereq"></a>Požadovaných součástí

Předpokladem pro tuto konfiguraci je konfigurovaný tenant Azure AD pomocí kroků v části [Konfigurace tenanta](openvpn-azure-ad-tenant.md).

## <a name="mfa"></a>Otevření stránky MFA

1. Přihlaste se k portálu Azure.
2. Přejděte na **Azure Active Directory-> všechny uživatele**.
3. Výběrem **Multi-Factor Authentication** otevřete stránku Multi-Factor Authentication.

   ![Přihlásit se](./media/openvpn-azure-ad-mfa/mfa1.jpg)

## <a name="users"></a>Vybrat uživatele

1. Na stránce **Multi-Factor Authentication** vyberte uživatele, pro které chcete povolit MFA.
2. Vyberte **Povolit**.

   ![Vyberte](./media/openvpn-azure-ad-mfa/mfa2.jpg)

## <a name="enableauth"></a>Povolit ověřování

1. Přejděte na **Azure Active Directory > podnikové aplikace – > všechny aplikace**.
2. Na stránce **podnikové aplikace – všechny aplikace** vyberte **Azure VPN**.

   ![ID adresáře](./media/openvpn-azure-ad-mfa/user1.jpg)

## <a name="enablesign"></a>Konfigurovat nastavení přihlášení

Na stránce **Azure VPN – vlastnosti** nakonfigurujte nastavení přihlášení.

1. Nastavte možnost **Povolit uživatelům přihlášení?** na **Ano**. To umožní všem uživatelům v tenantovi služby AD se úspěšně připojit k síti VPN.
2. Nastavit **přiřazení uživatele jako povinné?** Pokud chcete omezit přihlašování jenom na uživatele, kteří mají oprávnění k Azure VPN, nastavte **hodnotu Ano** .
3. Uložte provedené změny.

   ![Oprávnění](./media/openvpn-azure-ad-mfa/user2.jpg)

## <a name="next-steps"></a>Další kroky

Pokud se chcete připojit k virtuální síti, musíte vytvořit a nakonfigurovat profil klienta VPN. Viz [Konfigurace ověřování Azure AD pro připojení Point-to-site k Azure](virtual-wan-point-to-site-azure-ad.md).

---
title: 'VPN Gateway: tenant Azure AD pro různé skupiny uživatelů: ověřování Azure AD'
description: P2S VPN můžete použít pro připojení k virtuální síti pomocí ověřování Azure AD.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 9a98383c359135f90fd787008704d1ce389a4d57
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89424993"
---
# <a name="create-an-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Vytvoření tenanta služby Active Directory (AD) pro připojení protokolu P2S OpenVPN

Při připojování k virtuální síti můžete použít ověřování založené na certifikátech nebo ověřování pomocí protokolu RADIUS. Když ale použijete otevřený protokol sítě VPN, můžete použít i Azure Active Directory ověřování. Pokud chcete, aby se různé skupiny uživatelů připojovaly k různým branám VPN, můžete v AD zaregistrovat několik aplikací a propojit je s různými bránami VPN. Tento článek vám pomůže nastavit tenanta Azure AD pro ověřování P2S OpenVPN a vytvořit a zaregistrovat víc aplikací ve službě Azure AD, aby se povolil jiný přístup pro různé uživatele a skupiny.

> [!NOTE]
> Ověřování Azure AD se podporuje jenom pro připojení OpenVPN® protokolu.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. povolení ověřování v bráně

V tomto kroku povolíte ověřování Azure AD v bráně VPN.

1. Povolte ověřování Azure AD v bráně VPN tak, že přejdete na **položku konfigurace Point-to-site** a jako **Typ tunelu zadáte** **OpenVPN (výběr protokolu SSL)** . Jako **typ ověřování** vyberte **Azure Active Directory** a potom zadejte informace v části **Azure Active Directory** .

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-ad-auth-portal.png)

    > [!NOTE]
    > Nepoužívejte ID aplikace klienta Azure VPN: udělí všem uživatelům přístup k bráně VPN. Použijte ID registrovaných aplikací.

2. Vytvořte a Stáhněte si profil Kliknutím na odkaz **stáhnout klienta VPN** .

3. Extrahujte stažený soubor zip.

4. Přejděte do složky unzip "AzureVPN".

5. Poznamenejte si umístění souboru azurevpnconfig.xml. azurevpnconfig.xml obsahuje nastavení pro připojení VPN a dá se importovat přímo do klientské aplikace VPN Azure. Tento soubor můžete také distribuovat všem uživatelům, kteří se potřebují připojit prostřednictvím e-mailu nebo jiným způsobem. Uživatel bude potřebovat platné přihlašovací údaje Azure AD pro úspěšné připojení.

## <a name="next-steps"></a>Další kroky

Aby bylo možné se připojit k virtuální síti, je nutné vytvořit a nakonfigurovat profil klienta VPN. Viz téma [Konfigurace klienta VPN pro připojení P2S VPN](openvpn-azure-ad-client.md).

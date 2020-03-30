---
title: 'Brána VPN: Tenant Azure AD pro různé skupiny uživatelů: Ověřování Azure AD'
description: K připojení k virtuální síti pomocí ověřování Azure AD můžete použít P2S VPN.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: alzam
ms.openlocfilehash: 118ea21cbdd2e0527659c7c1beb40d8e42fa1d10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485604"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Vytvoření klienta Služby Azure Active Directory pro připojení protokolu P2S OpenVPN

Při připojování k virtuální síti můžete použít ověřování na základě certifikátu nebo ověřování RADIUS. Pokud však používáte protokol Open VPN, můžete také použít ověřování služby Azure Active Directory. Pokud chcete, aby se jiná sada uživatelů mohla připojit k různým privátní bráně, můžete zaregistrovat více aplikací ve službě AD a propojit je s různými branami VPN. Tento článek vám pomůže nastavit tenanta Azure AD pro ověřování P2S OpenVPN a vytvořit a zaregistrovat více aplikací ve službě Azure AD pro povolení různého přístupu pro různé uživatele a skupiny.

> [!NOTE]
> Ověřování Azure AD je podporované jenom pro připojení protokolu OpenVPN®.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-enable-authentication-on-the-gateway"></a><a name="enable-authentication"></a>6. Povolení ověřování na bráně

V tomto kroku povolíte ověřování Azure AD na bráně VPN.

1. Povolte ověřování Azure AD na bráně VPN spuštěním následujících příkazů. Nezapomeňte upravit příkazy tak, aby odrážely vaše vlastní prostředí:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
    > [!NOTE]
    > Nepoužívejte ID aplikace klienta Azure VPN ve výše uvedených příkazech: Udělí všem uživatelům přístup k bráně VPN. Použijte ID aplikací, které jste zaregistrovali.

2. Vytvořte a stáhněte profil spuštěním následujících příkazů. Změňte hodnoty -ResourcGroupName a -Name tak, aby odpovídaly vašim vlastním.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Po spuštění příkazů se zobrazí výsledek podobný tomu níže. Zkopírujte adresu URL výsledků do prohlížeče a stáhněte si soubor zip profilu.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Extrahujte stažený soubor zip.

5. Přejděte do rozbalené složky "AzureVPN".

6. Poznamenejte si umístění souboru "azurevpnconfig.xml". Soubor azurevpnconfig.xml obsahuje nastavení pro připojení VPN a lze jej importovat přímo do aplikace klienta Azure VPN. Tento soubor můžete také distribuovat všem uživatelům, kteří se potřebují připojit prostřednictvím e-mailu nebo jiným způsobem. Uživatel bude k úspěšnému připojení potřebovat platná pověření Azure AD.

## <a name="next-steps"></a>Další kroky

Chcete-li se připojit k virtuální síti, musíte vytvořit a nakonfigurovat profil klienta VPN. Viz [Konfigurace klienta VPN pro připojení P2S VPN](openvpn-azure-ad-client.md).

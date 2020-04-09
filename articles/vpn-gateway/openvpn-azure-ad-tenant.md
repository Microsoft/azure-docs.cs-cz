---
title: 'Brána VPN: Klient Azure AD pro připojení VPN P2S: Ověřování Azure AD'
description: K připojení k virtuální síti pomocí ověřování Azure AD můžete použít P2S VPN.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: alzam
ms.openlocfilehash: 6f7558a7d382d13c822862b881e310d3a5aaad0f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879407"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Vytvoření klienta Služby Azure Active Directory pro připojení protokolu P2S OpenVPN

Při připojování k virtuální síti můžete použít ověřování na základě certifikátu nebo ověřování RADIUS. Pokud však používáte protokol Open VPN, můžete také použít ověřování služby Azure Active Directory. Tento článek vám pomůže nastavit klienta Azure AD pro ověřování P2S Open VPN.

> [!NOTE]
> Ověřování Azure AD je podporované jenom pro připojení protokolu OpenVPN®.
>


## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. Ověření klienta Azure AD

Ověřte, že máte klienta Azure AD. Pokud nemáte klienta Azure AD, můžete ho vytvořit pomocí kroků v článku [Vytvořit nový klient:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Název organizace
* Počáteční název domény

Příklad:

   ![Nový klient Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Vytvoření uživatelů klienta Azure AD

Váš klient Azure AD potřebuje následující účty: účet globálního správce a hlavní uživatelský účet. Hlavní uživatelský účet se používá jako hlavní vložený účet (účet služby). Když vytvoříte uživatelský účet klienta Azure AD, upravíte roli adresáře pro typ uživatele, který chcete vytvořit.

Pomocí kroků v [tomto článku](../active-directory/fundamentals/add-users-azure-active-directory.md) vytvořte alespoň dva uživatele pro klienta Azure AD. Nezapomeňte změnit **roli adresáře** a vytvořit tak typy účtů:

* Globální správce
* Uživatel

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. Povolení ověřování Azure AD na bráně VPN

1. Vyhledejte ID adresáře adresáře, který chcete použít pro ověřování. Je uveden v části vlastnosti na stránce služby Active Directory.

    ![ID adresáře](./media/openvpn-create-azure-ad-tenant/directory-id.png)

2. Zkopírujte ID adresáře.

3. Přihlaste se k portálu Azure jako uživatel, kterému je přiřazena role **globálního správce.**

4. Dále uveďte souhlas správce. Zkopírujte a vložte adresu URL, která se týkají umístění nasazení, do adresního řádku prohlížeče:

    Public

    ```
    https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
    ````

    Azure Government

    ```
    https://login-us.microsoftonline.com/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
    ````

    Microsoft Cloud Německo

    ```
    https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
    ````

    Azure China 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Pokud se zobrazí výzva, vyberte účet **globálního správce.**

    ![ID adresáře](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Po zobrazení výzvy vyberte **Přijmout.**

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Ve službě Azure AD se v **podnikových aplikacích**zobrazí **azure vpn.**

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)
    
8. Pokud ještě nemáte funkční prostředí point-to-site, postupujte podle pokynů k jeho vytvoření. Viz [Vytvoření vpn typu point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) k vytvoření a konfiguraci brány VPN typu point-to-site s nativním ověřováním certifikátů Azure. 

    > [!IMPORTANT]
    > Základní skladová položka není podporována pro OpenVPN.

9. Povolte ověřování Azure AD na bráně VPN spuštěním následujících příkazů a ujistěte se, že chcete upravit příkaz tak, aby odrážel vaše vlastní prostředí:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>/" -AadAudienceId "41b23e61-6c1e-4545-b367-cd054e0ed4b4" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24 -VpnClientProtocol OpenVPN
    ```

   > [!NOTE]
   > Ujistěte se, že na konec `AadIssuerUri` hodnoty zahrnete koncové lomítko. V opačném případě se příkaz nezdaří.

10. Vytvořte a stáhněte profil spuštěním následujících příkazů. Změňte hodnoty -ResourceGroupName a -Name tak, aby odpovídaly vašim vlastním.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

11. Po spuštění příkazů se zobrazí výsledek podobný tomu níže. Zkopírujte adresu URL výsledků do prohlížeče a stáhněte si soubor zip profilu.

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/profile.png)

12. Extrahujte stažený soubor zip.

13. Přejděte do rozbalené složky "AzureVPN".

14. Poznamenejte si umístění souboru "azurevpnconfig.xml". Soubor azurevpnconfig.xml obsahuje nastavení pro připojení VPN a lze jej importovat přímo do aplikace klienta Azure VPN. Tento soubor můžete také distribuovat všem uživatelům, kteří se potřebují připojit prostřednictvím e-mailu nebo jiným způsobem. Uživatel bude k úspěšnému připojení potřebovat platná pověření Azure AD.

## <a name="next-steps"></a>Další kroky

Chcete-li se připojit k virtuální síti, musíte vytvořit a nakonfigurovat profil klienta VPN. Viz [Konfigurace klienta VPN pro připojení P2S VPN](openvpn-azure-ad-client.md).

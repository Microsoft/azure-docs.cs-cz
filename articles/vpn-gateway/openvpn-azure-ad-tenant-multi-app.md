---
title: 'VPN Gateway: tenant Azure AD pro různé skupiny uživatelů: ověřování Azure AD'
description: P2S VPN můžete použít pro připojení k virtuální síti pomocí ověřování Azure AD.
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: alzam
ms.openlocfilehash: 73a47b20010be0b4eadec790dd8fb254f912ff2a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477171"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Vytvoření tenanta Azure Active Directory pro připojení protokolu P2S OpenVPN

Při připojování k virtuální síti můžete použít ověřování založené na certifikátech nebo ověřování pomocí protokolu RADIUS. Když ale použijete otevřený protokol sítě VPN, můžete použít i Azure Active Directory ověřování. Pokud chcete, aby se různé skupiny uživatelů připojovaly k různým branám VPN, můžete v AD zaregistrovat několik aplikací a propojit je s různými bránami VPN. Tento článek vám pomůže nastavit tenanta Azure AD pro ověřování P2S OpenVPN a vytvořit a zaregistrovat víc aplikací ve službě Azure AD, aby se povolil jiný přístup pro různé uživatele a skupiny.

> [!NOTE]
> Ověřování Azure AD se podporuje jenom pro připojení OpenVPN® protokolu.
>

## <a name="tenant"></a>1. vytvoření tenanta Azure AD

Vytvořte tenanta Azure AD pomocí kroků v článku [Vytvoření nového tenanta](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Název organizace
* Počáteční název domény

Příklad:

   ![Nový tenant Azure AD](./media/openvpn-azure-ad-tenant-multi-app/newtenant.png)

## <a name="users"></a>2. vytvoření uživatelů klienta Azure AD

Dále vytvořte dva uživatelské účty. Vytvořte jeden účet globálního správce a jeden hlavní uživatelský účet. Hlavní uživatelský účet se používá jako účet pro vložení hlavního serveru (účet služby). Když vytvoříte uživatelský účet tenanta Azure AD, upravíte roli adresáře pro typ uživatele, který chcete vytvořit.

Postup v [tomto článku](../active-directory/fundamentals/add-users-azure-active-directory.md) použijte k vytvoření alespoň dvou uživatelů pro vašeho TENANTA Azure AD. Nezapomeňte změnit **roli adresáře** , aby se vytvořily typy účtů:

* Globální správce
* Uživatel

## <a name="enable-authentication"></a>3. registrace klienta VPN Azure v tenantovi Azure AD

1. Vyhledejte ID adresáře adresáře, který chcete použít pro ověřování. Je uveden v části Properties (vlastnosti) stránky Active Directory.

    ![ID adresáře](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Zkopírujte ID adresáře.

3. Přihlaste se k Azure Portal jako uživatel, kterému je přiřazena role **globálního správce** .

4. Pak poskytněte souhlas správce. Zkopírujte a vložte adresu URL, která se vztahuje k umístění vašeho nasazení, do adresního řádku v prohlížeči:

    Veřejné

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Pokud se zobrazí výzva, vyberte **globální účet správce** .

    ![ID adresáře](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Po zobrazení výzvy vyberte **přijmout** .

    ![Přijmout](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. V rámci Azure AD se v **podnikových aplikacích**zobrazí uvedená služba **Azure VPN** .

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azurevpn.png)

## <a name="enable-authentication"></a>4. registrace dalších aplikací pro různé uživatele nebo skupiny

1. V části Azure Active Directory klikněte na **Registrace aplikací** a pak **+ Nová registrace** .

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. V okně **zaregistrovat aplikaci** zadejte **název** a vyberte požadované **podporované typy účtů** a klikněte na **zaregistrovat** .

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Po registraci nové aplikace klikněte na **vystavení rozhraní API** v okně aplikace.

4. Klikněte na **+ Přidat obor** .
5. Ponechte výchozí identifikátor **URI ID aplikace** a klikněte na **Uložit a pokračovat** .

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)
6. Vyplňte požadovaná pole a zajistěte, aby byl **stav** **povolen**. Klikněte na **Přidat obor** .

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)
7. Klikněte na **Zobrazit rozhraní API** a potom na **+ Přidat klientskou aplikaci**.  V poli **ID klienta**zadejte v závislosti na cloudu následující hodnoty:
    -   Zadejte **41b23e61-6c1e-4545-B367-cd054e0ed4b4** pro Azure **Public** .
    -   Zadejte **51bb15d4-3a4f-4ebf-9dca-40096fe32426** pro **vládu** Azure.
    -   Zadejte **538ee9e6-310A-468d-afef-ea97365856a9** pro Azure **Německo** .
    -   Zadejte **49f817b6-84ae-4CC0-928c-73f27289b3aa** pro Azure **Čína 21Vianet**


8. Klikněte na **Přidat aplikaci** .

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)
9. Zkopírujte **ID aplikace (klienta)** ze stránky **Přehled** . Budete ho potřebovat ke konfiguraci vašich bran VPN.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Zopakováním kroků v této části (4) vytvořte tolik aplikací, které jsou potřeba pro váš požadavek na zabezpečení. Každá aplikace bude přidružena k bráně VPN a může mít jinou skupinu uživatelů. K bráně může být přidružená jenom jedna aplikace.

## <a name="enable-authentication"></a>5. přiřazení uživatelů k aplikacím

1. V části Azure AD, **podnikové aplikace**vyberte nově registrovanou aplikaci a klikněte na **vlastnosti**. Zajistěte, aby bylo **přiřazení uživatele vyžadováno?** je nastaveno na **Ano**. Klikněte na **Uložit**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na stránce aplikace klikněte na **Uživatelé a skupiny** a pak na **Přidat uživatele** .

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)
3. V části **Přidat přiřazení**klikněte na **Uživatelé a skupiny**. Vyberte uživatele, kterým chcete mít přístup k této aplikaci VPN. Klikněte na **Vybrat**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

## <a name="enable-authentication"></a>6. povolení ověřování Azure AD na bráně VPN

1. Povolte ověřování Azure AD v bráně VPN spuštěním následujících příkazů a nezapomeňte upravit příkaz tak, aby odrážel vaše vlastní prostředí:

    ```azurepowershell-interactive
    $gw = Get-AzVirtualNetworkGateway -Name <name of VPN gateway> -ResourceGroupName <Resource group>
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -VpnClientRootCertificates @()
    Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -AadTenantUri "https://login.microsoftonline.com/<your Directory ID>" -AadAudienceId "application ID from previous section" -AadIssuerUri "https://sts.windows.net/<your Directory ID>/" -VpnClientAddressPool 192.168.0.0/24
    ```
> [!NOTE]
> V předchozích příkazech nepoužívejte ID aplikace klienta Azure VPN. tím se udělí všem uživatelům přístup k bráně VPN. Použijte ID registrovaných aplikací.

2. Vytvořte a Stáhněte si profil spuštěním následujících příkazů. Změňte hodnoty-ResourcGroupName a-Name tak, aby odpovídaly vašemu vlastním.

    ```azurepowershell-interactive
    $profile = New-AzVpnClientConfiguration -Name <name of VPN gateway> -ResourceGroupName <Resource group> -AuthenticationMethod "EapTls"
    $PROFILE.VpnProfileSASUrl
    ```

3. Po spuštění příkazů vidíte výsledek podobný tomu níže. Zkopírujte výslednou adresu URL do prohlížeče a Stáhněte si soubor. zip profilu.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/profile.png)

4. Extrahujte stažený soubor zip.

5. Přejděte do složky unzip "AzureVPN".

6. Poznamenejte si umístění souboru azurevpnconfig. XML. Azurevpnconfig. XML obsahuje nastavení pro připojení VPN a dá se importovat přímo do klientské aplikace Azure VPN. Tento soubor můžete také distribuovat všem uživatelům, kteří se potřebují připojit prostřednictvím e-mailu nebo jiným způsobem. Uživatel bude potřebovat platné přihlašovací údaje Azure AD pro úspěšné připojení.

## <a name="next-steps"></a>Další kroky

Aby bylo možné se připojit k virtuální síti, je nutné vytvořit a nakonfigurovat profil klienta VPN. Viz téma [Konfigurace klienta VPN pro připojení P2S VPN](openvpn-azure-ad-client.md).

---
title: 'VPN Gateway: tenant Azure AD pro připojení VPN P2S: ověřování Azure AD'
description: Přečtěte si, jak nastavit tenanta Azure AD pro P2S Open VPN.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: bff1eec0152ab0f57edd212adf6b14f7b588fb51
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390160"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Vytvoření tenanta Azure Active Directory pro připojení typu point-to-site přes protokol OpenVPN

Při připojování k virtuální síti můžete použít ověřování založené na certifikátech nebo ověřování pomocí protokolu RADIUS. Když ale použijete otevřený protokol sítě VPN, můžete použít i Azure Active Directory ověřování. Tento článek vám pomůže nastavit tenanta Azure AD pro P2S otevřené ověřování VPN.

[!INCLUDE [Windows 10 and OpenVPN note](../../includes/vpn-gateway-openvpn-auth-include.md)]

## <a name="1-verify-azure-ad-tenant"></a><a name="tenant"></a>1. ověření tenanta Azure AD

Ověřte, že máte tenanta Azure AD. Pokud nemáte klienta služby Azure AD, můžete ho vytvořit pomocí postupu v článku [Vytvoření nového tenanta](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Název organizace
* Počáteční název domény

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/newtenant.png" alt-text="Nový tenant Azure AD" border="false":::

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. vytvoření uživatelů klienta Azure AD

Váš tenant Azure AD potřebuje tyto účty: účet globálního správce a hlavní uživatelský účet. Hlavní uživatelský účet se používá jako účet pro vložení hlavního serveru (účet služby). Když vytvoříte uživatelský účet tenanta Azure AD, upravíte roli adresáře pro typ uživatele, který chcete vytvořit.

Použijte postup v části [Přidání nebo odstranění uživatelů – Azure Active Directory](../active-directory/fundamentals/add-users-azure-active-directory.md) k vytvoření alespoň dvou uživatelů pro vašeho TENANTA Azure AD. Nezapomeňte změnit **roli adresáře** , aby se vytvořily typy účtů:

* Globální správce
* User

## <a name="3-enable-azure-ad-authentication-on-the-vpn-gateway"></a><a name="enable-authentication"></a>3. povolení ověřování Azure AD na bráně VPN

1. Vyhledejte ID adresáře adresáře, který chcete použít pro ověřování. Je uvedený v části Properties (vlastnosti) stránky Active Directory.

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/directory-id.png" alt-text="Snímek obrazovky, který zobrazuje vlastnosti adresáře" lightbox="./media/openvpn-create-azure-ad-tenant/directory-id.png":::

1. Zkopírujte ID adresáře.

1. Přihlaste se k Azure Portal jako uživatel, kterému je přiřazena role **globálního správce** .

1. Pak poskytněte souhlas správce. Zkopírujte a vložte adresu URL, která se vztahuje k umístění vašeho nasazení, do adresního řádku v prohlížeči:

   Veřejná

   ```
   https://login.microsoftonline.com/common/oauth2/authorize?client_id=41b23e61-6c1e-4545-b367-cd054e0ed4b4&response_type=code&redirect_uri=https://portal.azure.com&nonce=1234&prompt=admin_consent
   ````

   Azure Government

   ```
   https://login.microsoftonline.us/common/oauth2/authorize?client_id=51bb15d4-3a4f-4ebf-9dca-40096fe32426&response_type=code&redirect_uri=https://portal.azure.us&nonce=1234&prompt=admin_consent
   ````

   Microsoft Cloud Německo

   ```
   https://login-us.microsoftonline.de/common/oauth2/authorize?client_id=538ee9e6-310a-468d-afef-ea97365856a9&response_type=code&redirect_uri=https://portal.microsoftazure.de&nonce=1234&prompt=admin_consent
   ````

    Azure (Čína) 21Vianet

    ```
    https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

   > [!NOTE]
   > Pokud k poskytnutí souhlasu používáte účet globálního správce, který není nativní pro tenanta Azure AD, nahraďte "Common" ID adresáře Azure AD v adrese URL. V některých jiných případech může být také nutné nahradit "Common" ID vašeho adresáře.
   >

1. Pokud se zobrazí výzva, vyberte **globální účet správce** .

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/pick.png" alt-text="Výběr účtu" border="false":::
1. Po zobrazení výzvy vyberte **přijmout** .

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/accept.jpg" alt-text="Snímek obrazovky se zobrazenými oprávněními pro zprávy, která požádala o přijetí ve vaší organizaci s podrobnostmi a možnost přijmout." border="false":::
1. V rámci Azure AD se v **podnikových aplikacích** zobrazí uvedená služba **Azure VPN** .

   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azurevpn.png" alt-text="Snímek obrazovky zobrazující stránku všechny aplikace" lightbox="./media/openvpn-create-azure-ad-tenant/azurevpn.png" :::
1. Pokud ještě nemáte funkční prostředí Point-to-site, postupujte podle pokynů a vytvořte si ho. Pokud chcete vytvořit a nakonfigurovat bránu VPN typu Point-to-site, přečtěte si téma [vytvoření sítě VPN typu Point-to-site](vpn-gateway-howto-point-to-site-resource-manager-portal.md) .

    > [!IMPORTANT]
    > Základní skladová položka není pro OpenVPN podporovaná.

1. Povolte ověřování Azure AD v bráně VPN tak, že přejdete na **položku konfigurace Point-to-site** a jako **Typ tunelu zadáte** **OpenVPN (výběr protokolu SSL)** . Jako **typ ověřování** vyberte **Azure Active Directory** a potom zadejte informace v části **Azure Active Directory** .

   * **Tenant:** TenantID pro tenanta Azure AD ```https://login.microsoftonline.com/{AzureAD TenantID}/```

   * **Cílová skupina:** ApplicationID pro Azure AD podnikovou aplikaci Azure VPN ```{AppID of the "Azure VPN" AD Enterprise app}```

   * **Vystavitel**: adresa URL služby tokenu zabezpečení ```https://sts.windows.net/{AzureAD TenantID}/```


   :::image type="content" source="./media/openvpn-create-azure-ad-tenant/azure-ad-auth-portal.png" alt-text="SAzure VPN" border="false":::

   > [!NOTE]
   > Nezapomeňte na konec hodnoty zahrnout koncové lomítko `AadIssuerUri` . V opačném případě se může stát, že připojení selže.
   >

1. Vytvořte a Stáhněte si profil Kliknutím na odkaz **stáhnout klienta VPN** .

1. Extrahujte stažený soubor zip.

1. Přejděte do složky unzip "AzureVPN".

1. Poznamenejte si umístění souboru azurevpnconfig.xml. azurevpnconfig.xml obsahuje nastavení pro připojení VPN a dá se importovat přímo do klientské aplikace VPN Azure. Tento soubor můžete také distribuovat všem uživatelům, kteří se potřebují připojit prostřednictvím e-mailu nebo jiným způsobem. Uživatel bude potřebovat platné přihlašovací údaje Azure AD pro úspěšné připojení.

## <a name="next-steps"></a>Další kroky

Vytvořte a nakonfigurujte profil klienta VPN. Viz téma [Konfigurace klienta VPN pro připojení P2S VPN](openvpn-azure-ad-client.md).

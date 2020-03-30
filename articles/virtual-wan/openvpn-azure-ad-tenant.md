---
title: 'Tenant Azure AD pro připojení VPN uživatelů: Ověřování Azure AD'
description: K připojení k virtuální síti pomocí ověřování Azure AD můžete použít azure virtuální síť WAN (point-to-site).
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: 74347ce969b6a5ffd57f5ca8396517e78590f3f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80059445"
---
# <a name="create-an-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Vytvoření klienta Služby Azure Active Directory pro připojení protokolu OPENVPN uživatelské sítě VPN

Při připojování k virtuální síti můžete použít ověřování na základě certifikátu nebo ověřování RADIUS. Pokud však používáte protokol Open VPN, můžete také použít ověřování služby Azure Active Directory. Tento článek vám pomůže nastavit klienta Azure AD pro ověřování open vpn uživatele virtuální sítě WAN (point-to-site).

> [!NOTE]
> Ověřování Azure AD je podporované&reg; jenom pro připojení protokolu OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Vytvoření klienta Azure AD

Vytvořte klienta Azure AD pomocí kroků v článku [Vytvořit nový klient:](../active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Název organizace
* Počáteční název domény

Příklad:

   ![Nový klient Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. Vytvoření uživatelů klienta Azure AD

Dále vytvořte dva uživatelské účty. Vytvořte jeden účet globálního správce a jeden hlavní uživatelský účet. Hlavní uživatelský účet se používá jako hlavní vložený účet (účet služby). Když vytvoříte uživatelský účet klienta Azure AD, upravíte roli adresáře pro typ uživatele, který chcete vytvořit.

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Pokud se zobrazí výzva, vyberte účet **globálního správce.**

    ![ID adresáře](./media/openvpn-create-azure-ad-tenant/pick.png)

6. Po zobrazení výzvy vyberte **Přijmout.**

    ![Accept](./media/openvpn-create-azure-ad-tenant/accept.jpg)

7. Ve službě Azure AD se v **podnikových aplikacích**zobrazí **azure vpn.**

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

8. Konfigurace ověřování Azure AD pro uživatele VPN a jeho přiřazení k virtuálnímu rozbočovači podle kroků v [části Konfigurace ověřování Azure aD pro připojení point-to-site k Azure](virtual-wan-point-to-site-azure-ad.md)

## <a name="next-steps"></a>Další kroky

Chcete-li se připojit k virtuální síti, musíte vytvořit a nakonfigurovat profil klienta VPN a přidružit jej k virtuálnímu rozbočovači. Viz [Konfigurace ověřování Azure AD pro připojení point-to-site k Azure](virtual-wan-point-to-site-azure-ad.md).

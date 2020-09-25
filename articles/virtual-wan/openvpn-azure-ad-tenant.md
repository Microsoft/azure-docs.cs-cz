---
title: 'Tenant Azure AD pro uživatelská připojení VPN: ověřování Azure AD'
description: K připojení k virtuální síti pomocí ověřování Azure AD můžete použít Azure Virtual WAN User VPN (Point-to-site).
titleSuffix: Azure Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: f77d17881054440b01e30e738a8c38407c38b4a4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91306544"
---
# <a name="prepare-azure-active-directory-tenant-for-user-vpn-openvpn-protocol-connections"></a>Příprava Azure Active Directory tenanta pro připojení VPN OpenVPN protokolu uživatelů

Při připojování k virtuálnímu centru přes protokol IKEv2 můžete použít ověřování založené na certifikátech nebo ověřování pomocí protokolu RADIUS. Pokud však používáte protokol OpenVPN, můžete také použít ověřování Azure Active Directory. Tento článek vám pomůže nastavit tenanta Azure AD pro virtuální síť VPN uživatele sítě WAN (Point-to-site) pomocí ověřování OpenVPN.

> [!NOTE]
> Ověřování Azure AD se podporuje jenom pro &reg; připojení protokolu OpenVPN.
>

## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. vytvoření tenanta Azure AD

Ověřte, že máte tenanta Azure AD. Pokud nemáte klienta služby Azure AD, můžete ho vytvořit pomocí postupu v článku [Vytvoření nového tenanta](../active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Název organizace
* Počáteční název domény

Příklad:

   ![Nový tenant Azure AD](./media/openvpn-create-azure-ad-tenant/newtenant.png)

## <a name="2-create-azure-ad-tenant-users"></a><a name="users"></a>2. vytvoření uživatelů klienta Azure AD

V dalším kroku vytvořte v nově vytvořeném tenantovi služby Azure AD dva uživatelské účty, jeden globální účet správce a jeden uživatelský účet. Uživatelský účet se dá použít k otestování ověřování OpenVPN a účet globálního správce se použije k udělení souhlasu registraci aplikace Azure VPN. Po vytvoření uživatelského účtu Azure AD se uživateli přiřadí **role adresáře** , aby mohl delegovat oprávnění správce.

Pomocí kroků v [tomto článku](../active-directory/fundamentals/add-users-azure-active-directory.md) vytvoříte dva uživatele pro vašeho TENANTA Azure AD. Nezapomeňte změnit **roli adresáře** na jednom z vytvořených účtů na **globální správce**.

## <a name="3-grant-consent-to-the-azure-vpn-app-registration"></a><a name="enable-authentication"></a>3. udělení souhlasu k registraci aplikace Azure VPN

1. Přihlaste se k webu Azure Portal jako uživatel, kterému je přiřazena role **globálního správce** .

2. Potom pro vaši organizaci udělte souhlas správce, což umožňuje aplikacím Azure VPN přihlašovat se a číst profily uživatelů. Zkopírujte a vložte adresu URL, která se vztahuje k umístění vašeho nasazení, do adresního řádku v prohlížeči:

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

    Azure (Čína) 21Vianet

    ```
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

3. Pokud se zobrazí výzva, vyberte **globální účet správce** .

    ![ID adresáře](./media/openvpn-create-azure-ad-tenant/pick.png)

4. Po zobrazení výzvy vyberte **přijmout** .

    ![Přijmout](./media/openvpn-create-azure-ad-tenant/accept.jpg)

5. V rámci Azure AD by se teď v **podnikových aplikacích**měla zobrazit uvedená služba **Azure VPN** .

    ![Azure VPN](./media/openvpn-create-azure-ad-tenant/azurevpn.png)

## <a name="next-steps"></a>Další kroky

Aby bylo možné se připojit k virtuálním sítím pomocí ověřování Azure AD, musíte vytvořit konfiguraci sítě VPN uživatele a přidružit ji k virtuálnímu rozbočovači. Viz [Konfigurace ověřování Azure AD pro připojení Point-to-site k Azure](virtual-wan-point-to-site-azure-ad.md).

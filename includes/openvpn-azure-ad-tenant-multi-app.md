---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/18/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e950d194ab48cec1a70c7bd17617332cb858a55d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77485600"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. Vytvoření klienta Azure AD

Vytvořte klienta Azure AD pomocí kroků v článku [Vytvořit nový klient:](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md)

* Název organizace
* Počáteční název domény

  Příklad:

   ![Nový klient Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. Vytvoření uživatelů klienta

V tomto kroku vytvoříte dva uživatele klienta Azure AD: jeden účet globálního správce a jeden hlavní uživatelský účet. Hlavní uživatelský účet se používá jako hlavní vložený účet (účet služby). Když vytvoříte uživatelský účet klienta Azure AD, upravíte roli adresáře pro typ uživatele, který chcete vytvořit. Pomocí kroků v [tomto článku](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) vytvořte alespoň dva uživatele pro klienta Azure AD. Nezapomeňte změnit **roli adresáře** a vytvořit tak typy účtů:

* Globální správce
* Uživatel

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Registrace klienta VPN

Zaregistrujte klienta VPN v tenantovi Azure AD.

1. Vyhledejte ID adresáře adresáře, který chcete použít pro ověřování. Je uveden v části vlastnosti na stránce služby Active Directory.

    ![ID adresáře](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

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

    ![ID adresáře](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Po zobrazení výzvy vyberte **Přijmout.**

    ![Accept](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. Ve svém Azure AD se v **podnikových aplikacích**zobrazí **azure vpn** v seznamu.

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. Registrace dalších žádostí

V tomto kroku zaregistrujete další aplikace pro různé uživatele a skupiny.

1. Ve službě Azure Active Directory klikněte na **Registrace aplikací** a potom na + **Nová registrace**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Na stránce **Registrovat aplikaci** zadejte **název**. Vyberte požadované **typy podporovaných účtů**a klepněte na tlačítko **Registrovat**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Po registraci nové aplikace klikněte na **vystavit rozhraní API** pod rozhraním aplikace.

4. Klepněte na tlačítko **+ Přidat obor**.

5. Ponechte výchozí **identifikátor URI ID aplikace**. Klepněte na tlačítko **Uložit a pokračovat .**

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Vyplňte požadovaná pole a ujistěte se, že **je povolen** **stav**. Klepněte na tlačítko **Přidat obor**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Klepněte na tlačítko **Vystavit rozhraní API** a potom + Přidat **klientskou aplikaci**.  Pro **ID klienta**zadejte v závislosti na cloudu následující hodnoty:

    - Zadejte **41b23e61-6c1e-4545-b367-cd054e0ed4b4** pro Azure **Public**
    - Zadejte **51bb15d4-3a4f-4ebf-9dca-40096fe32426** pro Azure **Government**
    - Zadejte **538ee9e6-310a-468d-afef-ea97365856a9** pro Azure **Germany**
    - Zadejte **49f817b6-84ae-4cc0-928c-73f27289b3aa** pro Azure **China 21Vianet**

8. Klepněte na tlačítko **Přidat aplikaci**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Zkopírujte **ID Aplikace (klienta)** ze stránky **Přehled.** Tyto informace budete potřebovat ke konfiguraci brány VPN.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Opakujte kroky v této části [zaregistrovat další aplikace](#register-apps) vytvořit tolik aplikací, které jsou potřebné pro vaše požadavky na zabezpečení. Každá aplikace bude přidružena k bráně VPN a může mít jinou sadu uživatelů. K bráně může být přidružena pouze jedna aplikace.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. Přiřazení uživatelů k aplikacím

Přiřaďte uživatele k vašim aplikacím.

1. V části **Aplikace Azure AD -> Enterprise**vyberte nově registrovanou aplikaci a klikněte na **Vlastnosti**. Ujistěte se, že je **yes** **vyžadováno přiřazení uživatele?** Klikněte na **Uložit**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na stránce aplikace klikněte na **Uživatelé a skupiny**a potom klikněte na **+Přidat uživatele**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. V části **Přidat přiřazení**klikněte na **Položku Uživatelé a skupiny**. Vyberte uživatele, kterým chcete mít přístup k této aplikaci VPN. Klepněte na **tlačítko Vybrat**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)
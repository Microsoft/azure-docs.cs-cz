---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/21/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8172e0e208d9a780c7676d7cb0e77dbc1c16f493
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606673"
---
## <a name="1-create-the-azure-ad-tenant"></a><a name="tenant"></a>1. vytvoření tenanta Azure AD

Vytvořte tenanta Azure AD pomocí kroků v článku [Vytvoření nového tenanta](../articles/active-directory/fundamentals/active-directory-access-create-new-tenant.md) :

* Název organizace
* Počáteční název domény

  Příklad:

   ![Nový tenant Azure AD](./media/openvpn-azure-ad-tenant-multi-app/new-tenant.png)

## <a name="2-create-tenant-users"></a><a name="users"></a>2. vytvoření uživatelů tenanta

V tomto kroku vytvoříte dva uživatele klienta Azure AD: jeden globální účet správce a jeden hlavní uživatelský účet. Hlavní uživatelský účet se používá jako účet pro vložení hlavního serveru (účet služby). Když vytvoříte uživatelský účet tenanta Azure AD, upravíte roli adresáře pro typ uživatele, který chcete vytvořit. Postup v [tomto článku](../articles/active-directory/fundamentals/add-users-azure-active-directory.md) použijte k vytvoření alespoň dvou uživatelů pro vašeho TENANTA Azure AD. Nezapomeňte změnit **roli adresáře** , aby se vytvořily typy účtů:

* Globální správce
* Uživatel

## <a name="3-register-the-vpn-client"></a><a name="register-client"></a>3. Zaregistrujte klienta VPN.

Zaregistrujte klienta VPN v tenantovi Azure AD.

1. Vyhledejte ID adresáře adresáře, který chcete použít pro ověřování. Je uveden v části Properties (vlastnosti) stránky Active Directory.

    ![ID adresáře](./media/openvpn-azure-ad-tenant-multi-app/directory-id.png)

2. Zkopírujte ID adresáře.

3. Přihlaste se k Azure Portal jako uživatel, kterému je přiřazena role **globálního správce** .

4. Pak poskytněte souhlas správce. Zkopírujte a vložte adresu URL, která se vztahuje k umístění vašeho nasazení, do adresního řádku v prohlížeči:

    Public

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
    https://https://login.chinacloudapi.cn/common/oauth2/authorize?client_id=49f817b6-84ae-4cc0-928c-73f27289b3aa&response_type=code&redirect_uri=https://portal.azure.cn&nonce=1234&prompt=admin_consent
    ```

5. Pokud se zobrazí výzva, vyberte **globální účet správce** .

    ![ID adresáře](./media/openvpn-azure-ad-tenant-multi-app/pick.png)

6. Po zobrazení výzvy vyberte **přijmout** .

    ![Snímek obrazovky se zobrazeným oknem s oprávněním pro zprávy, které se požaduje, přijměte vaší organizaci a informace o žádosti.](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

7. V rámci Azure AD se v **podnikových aplikacích**zobrazí uvedená služba **Azure VPN** .

     ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/azure-vpn.png)

## <a name="4-register-additional-applications"></a><a name="register-apps"></a>4. registrace dalších aplikací

V tomto kroku zaregistrujete další aplikace pro různé uživatele a skupiny.

1. V Azure Active Directory klikněte na **Registrace aplikací** a pak na **+ Nová registrace**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app1.png)

2. Na stránce **zaregistrovat aplikaci** zadejte **název**. Vyberte požadované **typy účtů**a pak klikněte na **zaregistrovat**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app2.png)

3. Po registraci nové aplikace klikněte v okně aplikace na **vystavení rozhraní API** .

4. Klikněte na **+ Přidat obor**.

5. Ponechte výchozí **identifikátor URI ID aplikace**. Klikněte na **Uložit a pokračujte**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app3.png)

6. Vyplňte požadovaná pole a zajistěte, aby byl **stav** **povolen**. Klikněte na **Přidat obor**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app4.png)

7. Klikněte na **zveřejnit rozhraní API** a potom na **+ Přidat klientskou aplikaci**.  V poli **ID klienta**zadejte v závislosti na cloudu následující hodnoty:

    - Zadejte **41b23e61-6c1e-4545-B367-cd054e0ed4b4** pro Azure **Public** .
    - Zadejte **51bb15d4-3a4f-4ebf-9dca-40096fe32426** pro **vládu** Azure.
    - Zadejte **538ee9e6-310A-468d-afef-ea97365856a9** pro Azure **Německo** .
    - Zadejte **49f817b6-84ae-4CC0-928c-73f27289b3aa** pro Azure **Čína 21Vianet**

8. Klikněte na **Přidat aplikaci**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app5.png)

9. Zkopírujte **ID aplikace (klienta)** ze stránky **Přehled** . Tyto informace budete potřebovat ke konfiguraci bran sítě VPN.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/app6.png)

10. Opakujte postup v části [registrace dalších aplikací](#register-apps) a vytvořte tolik aplikací, které jsou potřeba pro váš požadavek na zabezpečení. Každá aplikace bude přidružena k bráně VPN a může mít jinou skupinu uživatelů. K bráně může být přidružená jenom jedna aplikace.

## <a name="5-assign-users-to-applications"></a><a name="assign-users"></a>5. přiřazení uživatelů k aplikacím

Přiřaďte uživatele k vašim aplikacím.

1. V části **Azure AD – > podnikové aplikace**vyberte nově registrovanou aplikaci a klikněte na **vlastnosti**. Zajistěte, aby bylo **přiřazení uživatele vyžadováno?** je nastaveno na **Ano**. Klikněte na **Uložit**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user2.png)

2. Na stránce aplikace klikněte na **Uživatelé a skupiny**a pak klikněte na **+ Přidat uživatele**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user3.png)

3. V části **Přidat přiřazení**klikněte na **Uživatelé a skupiny**. Vyberte uživatele, kterým chcete mít přístup k této aplikaci VPN. Klikněte na **Vybrat**.

    ![Azure VPN](./media/openvpn-azure-ad-tenant-multi-app/user4.png)

---
title: 'VPN Gateway: tenant Azure AD pro různé skupiny uživatelů: ověřování Azure AD'
description: P2S VPN můžete použít pro připojení k virtuální síti pomocí ověřování Azure AD.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: b390b5f8b00f61994db820a3af7bce26a3e0a30d
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75934970"
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

## <a name="site"></a>6. vytvoření nové konfigurace P2S

Konfigurace P2S definuje parametry pro připojení vzdálených klientů.

1. Nastavte následující proměnné a nahraďte hodnoty podle potřeby vašeho prostředí.

   ```powershell
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Spuštěním následujících příkazů vytvořte konfiguraci:

   ```powershell
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
> [!NOTE]
> Do not use the Azure VPN client's application ID in the commands above as it will grant all users access to the VPN gateway. Use the ID of the application(s) you registered.

## <a name="hub"></a>7. Edit hub assignment

1. Navigate to the **Hubs** blade under the virtual WAN.
2. Select the hub that you want to associate the vpn server configuration to and click the ellipsis (...).

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Click **Edit virtual hub**.
4. Check the **Include point-to-site gateway** check box and pick the **Gateway scale unit** that you want.

   ![new site](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Enter the **Address pool** from which the VPN clients will be assigned IP addresses.
6. Click **Confirm**.
7. The operation will can take up to 30 minutes to complete.

## <a name="device"></a>8. Download VPN profile

Use the VPN profile to configure your clients.

1. On the page for your virtual WAN, click **User VPN configurations**.
2. At the top of the  page, click **Download user VPN config**.
3. Once the file has finished creating, you can click the link to download it.
4. Use the profile file to configure the VPN clients.

4. Extract the downloaded zip file.

5. Browse to the unzipped “AzureVPN” folder.

6. Make a note of the location of the “azurevpnconfig.xml” file. The azurevpnconfig.xml contains the setting for the VPN connection and can be imported directly into the Azure VPN Client application. You can also distribute this file to all the users that need to connect via e-mail or other means. The user will need valid Azure AD credentials to connect successfully.
## Configure user VPN clients

To connect, you need to download the Azure VPN Client (Preview) and import the VPN client profile that was downloaded in the previous steps on every computer that wants to connect to the VNet.

> [!NOTE]
> Azure AD authentication is supported only for OpenVPN® protocol connections.
>

#### To download the Azure VPN client

Use this [link](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) to download the Azure VPN Client (Preview).

#### <a name="import"></a>To import a client profile

1. On the page, select **Import**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Browse to the profile xml file and select it. With the file selected, select **Open**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Specify the name of the profile and select **Save**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Select **Connect** to connect to the VPN.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Once connected, the icon will turn green and say **Connected**.

    ![import](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>To delete a client profile

1. Select the ellipsis (...) next to the client profile that you want to delete. Then, select **Remove**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Select **Remove** to delete.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnose connection issues

1. To diagnose connection issues, you can use the **Diagnose** tool. Select the ellipsis (...) next to the VPN connection that you want to diagnose to reveal the menu. Then select **Diagnose**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. On the **Connection Properties** page, select **Run Diagnosis**.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Sign in with your credentials.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. View the diagnosis results.

    ![diagnose](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>View your virtual WAN

1. Navigate to the virtual WAN.
2. On the Overview page, each point on the map represents a hub. Hover over any point to view the hub health summary.
3. In the Hubs and connections section, you can view hub status, site, region, VPN connection status, and bytes in and out.

## <a name="viewhealth"></a>View your resource health

1. Navigate to your WAN.
2. On your WAN page, in the **SUPPORT + Troubleshooting** section, click **Health** and view your resource.


## <a name="cleanup"></a>Clean up resources

When you no longer need these resources, you can use [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) to remove the resource group and all of the resources it contains. Replace "myResourceGroup" with the name of your resource group and run the following PowerShell command:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).
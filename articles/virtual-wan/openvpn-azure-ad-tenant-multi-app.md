---
title: 'VPN Gateway: tenant Azure AD pro různé skupiny uživatelů: ověřování Azure AD'
description: P2S VPN můžete použít pro připojení k virtuální síti pomocí ověřování Azure AD.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 1/10/2020
ms.author: alzam
ms.openlocfilehash: 90244b9dcf30c2ef01d4e57c9d8e35fa1d71f434
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985641"
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

    ![Vyjádřit](./media/openvpn-azure-ad-tenant-multi-app/accept.jpg)

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
   ```

> [!NOTE]
> V předchozích příkazech nepoužívejte ID aplikace klienta Azure VPN. tím se udělí všem uživatelům přístup k bráně VPN. Použijte ID registrovaných aplikací.

## <a name="hub"></a>7. Úprava přiřazení centra

1. Přejděte do okna **centra** v rámci virtuální sítě WAN.
2. Vyberte centrum, ke kterému chcete přiřadit konfiguraci serveru VPN, a klikněte na tlačítko se třemi tečkami (...).

   ![nová lokalita](media/virtual-wan-point-to-site-azure-ad/p2s4.jpg)
3. Klikněte na **Upravit virtuální rozbočovač**.
4. Zaškrtněte políčko **Zahrnout bránu Point-to-site** a vyberte **jednotku škálování brány** , kterou chcete použít.

   ![nová lokalita](media/virtual-wan-point-to-site-azure-ad/p2s2.jpg)
5. Zadejte **fond adres** , ze kterého budou klienti VPN přiřazeni IP adresy.
6. Klikněte na **Confirm** (Potvrdit).
7. Dokončení operace může trvat až 30 minut.

## <a name="device"></a>8. stáhnout profil sítě VPN

Pomocí profilu sítě VPN nakonfigurujte své klienty.

1. Na stránce pro virtuální síť WAN klikněte na **Konfigurace sítě VPN uživatele**.
2. V horní části stránky klikněte na **Stáhnout uživatelskou konfiguraci VPN**.
3. Jakmile se dokončí vytváření souboru, můžete ho kliknutím na odkaz stáhnout.
4. K nakonfigurování klientů VPN použijte profilový soubor.

4. Extrahujte stažený soubor zip.

5. Přejděte do složky unzip "AzureVPN".

6. Poznamenejte si umístění souboru azurevpnconfig. XML. Azurevpnconfig. XML obsahuje nastavení pro připojení VPN a dá se importovat přímo do klientské aplikace Azure VPN. Tento soubor můžete také distribuovat všem uživatelům, kteří se potřebují připojit prostřednictvím e-mailu nebo jiným způsobem. Uživatel bude potřebovat platné přihlašovací údaje Azure AD pro úspěšné připojení.
## <a name="configure-user-vpn-clients"></a>Konfigurace klientů VPN uživatelů

Abyste se mohli připojit, je potřeba stáhnout klienta Azure VPN (Preview) a importovat profil klienta VPN, který jste stáhli v předchozích krocích v každém počítači, který se chce připojit k virtuální síti.

> [!NOTE]
> Ověřování Azure AD se podporuje jenom pro připojení OpenVPN® protokolu.
>

#### <a name="to-download-the-azure-vpn-client"></a>Stažení klienta Azure VPN

Pomocí tohoto [odkazu](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab) si stáhněte klienta Azure VPN (Preview).

#### <a name="import"></a>Import profilu klienta

1. Na stránce vyberte **importovat**.

    ![importovat](./media/virtual-wan-point-to-site-azure-ad/import/import1.jpg)

2. Přejděte k souboru XML profilu a vyberte ho. Když je vybraný soubor, vyberte **otevřít**.

    ![importovat](./media/virtual-wan-point-to-site-azure-ad/import/import2.jpg)

3. Zadejte název profilu a vyberte **Uložit**.

    ![importovat](./media/virtual-wan-point-to-site-azure-ad/import/import3.jpg)

4. Vyberte **připojit** a připojte se k síti VPN.

    ![importovat](./media/virtual-wan-point-to-site-azure-ad/import/import4.jpg)

5. Po připojení se ikona změní na zelenou a znamená se **připojit**.

    ![importovat](./media/virtual-wan-point-to-site-azure-ad/import/import5.jpg)

#### <a name="delete"></a>Odstranění profilu klienta

1. Vyberte tři tečky (...) vedle profilu klienta, který chcete odstranit. Pak vyberte **Odebrat**.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete1.jpg)

2. Vyberte **Odebrat** a odstraňte.

    ![delete](./media/virtual-wan-point-to-site-azure-ad/delete/delete2.jpg)

#### <a name="diagnose"></a>Diagnostika problémů s připojením

1. K diagnostice problémů s připojením můžete použít nástroj pro **diagnostiku** . Vyberte tři tečky (...) vedle připojení VPN, které chcete diagnostikovat, aby se nabídka zobrazila. Pak vyberte **Diagnostika**.

    ![diagnóz](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose1.jpg)

2. Na stránce **Vlastnosti připojení** vyberte **Spustit diagnostiku**.

    ![diagnóz](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose2.jpg)

3. Přihlaste se pomocí svých přihlašovacích údajů.

    ![diagnóz](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose3.jpg)

4. Zobrazení výsledků diagnostiky.

    ![diagnóz](./media/virtual-wan-point-to-site-azure-ad/diagnose/diagnose4.jpg)

## <a name="viewwan"></a>Zobrazení virtuální sítě WAN

1. Přejděte na virtuální síť WAN.
2. Na stránce Overview (Přehled) každý bod na mapě představuje jeden rozbočovač. Podržením ukazatele na některém z těchto bodů zobrazíte souhrn stavu rozbočovače.
3. V části Hubs and connections (Rozbočovače a připojení) můžete zjistit stav rozbočovače, lokalitu, oblast, stav připojení VPN a přijaté a odeslané bajty.

## <a name="viewhealth"></a>Zobrazení stavu prostředku

1. Přejděte na svoji síť WAN.
2. Na stránce sítě WAN v části **SUPPORT + Troubleshooting** (Podpora a řešení potíží) klikněte na **Health** (Stav) a prohlédněte si stav svého prostředku.


## <a name="cleanup"></a>Vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Položku myResourceGroup nahraďte názvem vaší skupiny prostředků a spusťte následující příkaz PowerShellu:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).

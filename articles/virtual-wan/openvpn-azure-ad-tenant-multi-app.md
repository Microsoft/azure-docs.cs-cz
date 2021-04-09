---
title: 'Virtuální síť WAN: tenant Azure AD pro různé skupiny uživatelů: ověřování Azure AD'
description: Nastavte tenanta Azure AD pro ověřování OpenVPN P2S a vytvořte a zaregistrujte více aplikací v Azure AD, abyste povolili různé přístupy pro různé uživatele a skupiny.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: alzam
ms.openlocfilehash: e25ef7f55492be4ee491b9ebbbef4aa1eb03c80b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98898083"
---
# <a name="create-an-azure-active-directory-ad-tenant-for-p2s-openvpn-protocol-connections"></a>Vytvoření tenanta Azure Active Directory (AD) pro připojení protokolu P2S OpenVPN

Při připojování k virtuální síti můžete použít ověřování založené na certifikátech nebo ověřování pomocí protokolu RADIUS. Když ale použijete otevřený protokol sítě VPN, můžete použít i Azure Active Directory ověřování. Pokud chcete, aby se různé skupiny uživatelů připojovaly k různým branám, můžete zaregistrovat několik aplikací ve službě AD a propojit je s různými bránami.

Tento článek vám pomůže nastavit tenanta Azure AD pro ověřování OpenVPN P2S a vytvořit a zaregistrovat víc aplikací v Azure AD, aby se povolil jiný přístup pro různé uživatele a skupiny.

> [!NOTE]
> Ověřování Azure AD se podporuje jenom pro &reg; připojení protokolu OpenVPN.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. vytvoření nové konfigurace P2S

Konfigurace P2S definuje parametry pro připojení vzdálených klientů.

1. Nastavte následující proměnné a nahraďte hodnoty podle potřeby vašeho prostředí.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Spuštěním následujících příkazů vytvořte konfiguraci:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Ve výše uvedených příkazech nepoužívejte ID aplikace klienta Azure VPN. Tato akce udělí všem uživatelům přístup k bráně. Použijte ID registrovaných aplikací.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Úprava přiřazení centra

1. Přejděte do okna **centra** v rámci virtuální sítě WAN.

2. Vyberte centrum, ke kterému chcete přiřadit konfiguraci serveru VPN, a klikněte na tlačítko se třemi tečkami (...).

    ![Snímek obrazovky se zobrazí v nabídce Upravit virtuální rozbočovač vybraný.](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Klikněte na **Upravit virtuální rozbočovač**.

4. Zaškrtněte políčko **Zahrnout bránu Point-to-site** a vyberte **jednotku škálování brány** , kterou chcete použít.

    ![Snímek obrazovky se zobrazí dialogové okno Upravit virtuální centrum, kde můžete vybrat jednotku škálování brány.](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Zadejte **fond adres** , ze kterého budou klienti VPN přiřazeni IP adresy.

6. Klikněte na **Confirm** (Potvrdit).

7. Dokončení operace může trvat až 30 minut.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. stáhnout profil sítě VPN

Pomocí profilu sítě VPN nakonfigurujte své klienty.

1. Na stránce pro virtuální síť WAN klikněte na **Konfigurace sítě VPN uživatele**.

2. V horní části stránky klikněte na **Stáhnout uživatelskou konfiguraci VPN**.

3. Jakmile se dokončí vytváření souboru, můžete ho kliknutím na odkaz stáhnout.

4. K nakonfigurování klientů VPN použijte profilový soubor.

5. Extrahujte stažený soubor zip.

6. Přejděte do složky unzip "AzureVPN".

7. Poznamenejte si umístění souboru azurevpnconfig.xml. azurevpnconfig.xml obsahuje nastavení pro připojení VPN a dá se importovat přímo do klientské aplikace VPN Azure. Tento soubor můžete také distribuovat všem uživatelům, kteří se potřebují připojit prostřednictvím e-mailu nebo jiným způsobem. Uživatel bude potřebovat platné přihlašovací údaje Azure AD pro úspěšné připojení.

## <a name="9-configure-user-vpn-clients"></a>9. konfigurace klientů VPN uživatelů

Abyste se mohli připojit, je potřeba stáhnout klienta Azure VPN a importovat profil klienta VPN, který jste stáhli v předchozích krocích v každém počítači, který se chce připojit k virtuální síti.

> [!NOTE]
> Ověřování Azure AD se podporuje jenom pro &reg; připojení protokolu OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Stažení klienta Azure VPN

Pomocí tohoto [odkazu](https://go.microsoft.com/fwlink/?linkid=2117554) si stáhněte klienta Azure VPN.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Import profilu klienta

1. Na stránce vyberte **importovat**.

    ![Snímek obrazovky zobrazující import vybraný z nabídky plus](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Přejděte k souboru XML profilu a vyberte ho. Když je vybraný soubor, vyberte **otevřít**.

    ![Snímek obrazovky zobrazuje otevřené dialogové okno, ve kterém můžete vybrat soubor.](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Zadejte název profilu a vyberte **Uložit**.

    ![Snímek obrazovky zobrazuje název připojení, který jste přidali, a tlačítko Uložit.](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Vyberte **připojit** a připojte se k síti VPN.

    ![Snímek obrazovky se zobrazí tlačítko připojit pro připojení, které jste právě vytvořili.](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Po připojení se ikona změní na zelenou a znamená se **připojit**.

    ![Snímek obrazovky zobrazuje připojení v připojeném stavu s možností odpojení.](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Odstranění profilu klienta

1. Vyberte tři tečky (...) vedle profilu klienta, který chcete odstranit. Pak vyberte **Odebrat**.

    ![Snímek obrazovky se zobrazí z nabídky odebrat vybrané.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Vyberte **Odebrat** a odstraňte.

    ![Snímek obrazovky se zobrazí potvrzovací dialogové okno s možností odebrat nebo zrušit.](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Postup diagnostiky problémů s připojením

1. K diagnostice problémů s připojením můžete použít nástroj pro **diagnostiku** . Vyberte tři tečky (...) vedle připojení VPN, které chcete diagnostikovat, aby se nabídka zobrazila. Pak vyberte **Diagnostika**.

    ![Snímek obrazovky zobrazuje diagnostiku vybranou z nabídky.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Na stránce **Vlastnosti připojení** vyberte **Spustit diagnostiku**.

    ![Snímek obrazovky ukazuje tlačítko spustit diagnostiku pro připojení.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Přihlaste se pomocí svých přihlašovacích údajů.

    ![Diagnostika 3](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Zobrazení výsledků diagnostiky.

    ![Snímek obrazovky ukazuje tlačítko spustit diagnostiku pro připojení.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Přihlaste se pomocí svých přihlašovacích údajů.

    ![Snímek obrazovky se zobrazí dialogové okno přihlášení pro tuto akci.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Zobrazení výsledků diagnostiky.

    ![Snímek obrazovky zobrazuje výsledky diagnostiky.](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Podívejte se na virtuální síť WAN

1. Přejděte na virtuální síť WAN.

2. Na stránce Overview (Přehled) každý bod na mapě představuje jeden rozbočovač.

3. V části Hubs and connections (Rozbočovače a připojení) můžete zjistit stav rozbočovače, lokalitu, oblast, stav připojení VPN a přijaté a odeslané bajty.

## <a name="clean-up-resources"></a><a name="cleanup"></a>Vyčištění prostředků

Pokud už tyto prostředky nepotřebujete, můžete k odebrání skupiny prostředků a všech prostředků, které obsahuje, použít rutinu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Položku myResourceGroup nahraďte názvem vaší skupiny prostředků a spusťte následující příkaz PowerShellu:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Další kroky

Další informace o službě Virtual WAN najdete v článku [Přehled služby Virtual WAN](virtual-wan-about.md).

---
title: 'Virtuální WAN: Klient Azure AD pro různé skupiny uživatelů: Ověřování Azure AD'
description: K připojení k virtuální síti pomocí ověřování Azure AD můžete použít P2S VPN.
services: virtual-wan
author: anzaman
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: alzam
ms.openlocfilehash: af5ff5817ee9ae7e6d7432fe281ecb440bf25b9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060714"
---
# <a name="create-an-azure-active-directory-tenant-for-p2s-openvpn-protocol-connections"></a>Vytvoření klienta Služby Azure Active Directory pro připojení protokolu P2S OpenVPN

Při připojování k virtuální síti můžete použít ověřování na základě certifikátu nebo ověřování RADIUS. Pokud však používáte protokol Open VPN, můžete také použít ověřování služby Azure Active Directory. Pokud chcete, aby se jiná skupina uživatelů mohla připojit k různým bránám, můžete ve službě AD zaregistrovat více aplikací a propojit je s různými bránami.

Tento článek vám pomůže nastavit klienta Azure AD pro ověřování P2S OpenVPN a vytvořit a zaregistrovat více aplikací ve službě Azure AD, abyste povolili jiný přístup pro různé uživatele a skupiny.

> [!NOTE]
> Ověřování Azure AD je podporované&reg; jenom pro připojení protokolu OpenVPN.
>

[!INCLUDE [create](../../includes/openvpn-azure-ad-tenant-multi-app.md)]

## <a name="6-create-a-new-p2s-configuration"></a><a name="site"></a>6. Vytvořte novou konfiguraci P2S

Konfigurace P2S definuje parametry pro připojení vzdálených klientů.

1. Nastavte následující proměnné a nahraďte hodnoty podle potřeby pro vaše prostředí.

   ```azurepowershell-interactive
   $aadAudience = "00000000-abcd-abcd-abcd-999999999999"
   $aadIssuer = "https://sts.windows.net/00000000-abcd-abcd-abcd-999999999999/"
   $aadTenant = "https://login.microsoftonline.com/00000000-abcd-abcd-abcd-999999999999"    
   ```

2. Chcete-li vytvořit konfiguraci, spusťte následující příkazy:

   ```azurepowershell-interactive
   $aadConfig = New-AzVpnServerConfiguration -ResourceGroupName <ResourceGroup> -Name newAADConfig -VpnProtocol OpenVPN -VpnAuthenticationType AAD -AadTenant $aadTenant -AadIssuer $aadIssuer -AadAudience $aadAudience -Location westcentralus
   ```

   > [!NOTE]
   > Nepoužívejte ID aplikace klienta Azure VPN ve výše uvedených příkazech: Udělí všem uživatelům přístup k bráně. Použijte ID aplikací, které jste zaregistrovali.

## <a name="7-edit-hub-assignment"></a><a name="hub"></a>7. Úprava přiřazení centra

1. Přejděte do okna **Rozbočovače** pod virtuální sítě WAN.

2. Vyberte rozbočovač, ke kterému chcete přidružit konfiguraci serveru VPN, a klikněte na tři tečky (...).

    ![nová lokalita](media/openvpn-azure-ad-tenant-multi-app/p2s4.jpg)

3. Klikněte na **Upravit virtuální rozbočovač**.

4. Zaškrtněte políčko **Zahrnout bránu bod k webu** a vyberte **požadovanou jednotku škálování brány.**

    ![nová lokalita](media/openvpn-azure-ad-tenant-multi-app/p2s2.jpg)

5. Zadejte **fond adres,** ze kterého budou klientům VPN přiřazeny ip adresy.

6. Klikněte na **Confirm** (Potvrdit).

7. Operace může trvat až 30 minut.

## <a name="8-download-vpn-profile"></a><a name="device"></a>8. Stáhněte si profil VPN

Pomocí profilu sítě VPN nakonfigurujte své klienty.

1. Na stránce virtuální sítě WAN klikněte na **Konfigurace uživatelské sítě VPN**.

2. V horní části stránky klikněte na **Stáhnout uživatelskou konfiguraci VPN**.

3. Jakmile se dokončí vytváření souboru, můžete ho kliknutím na odkaz stáhnout.

4. Pomocí souboru profilu nakonfigurujte klienty VPN.

5. Extrahujte stažený soubor zip.

6. Přejděte do rozbalené složky "AzureVPN".

7. Poznamenejte si umístění souboru "azurevpnconfig.xml". Soubor azurevpnconfig.xml obsahuje nastavení pro připojení VPN a lze jej importovat přímo do aplikace klienta Azure VPN. Tento soubor můžete také distribuovat všem uživatelům, kteří se potřebují připojit prostřednictvím e-mailu nebo jiným způsobem. Uživatel bude k úspěšnému připojení potřebovat platná pověření Azure AD.

## <a name="9-configure-user-vpn-clients"></a>9. Konfigurace klienty VPN uživatelů

Chcete-li se připojit, musíte stáhnout klienta VPN Azure a importovat profil klienta VPN, který byl stažen v předchozích krocích v každém počítači, který se chce připojit k virtuální síti.

> [!NOTE]
> Ověřování Azure AD je podporované&reg; jenom pro připojení protokolu OpenVPN.
>

#### <a name="to-download-the-azure-vpn-client"></a>Stažení klienta Azure VPN

Tento [odkaz](https://go.microsoft.com/fwlink/?linkid=2117554) použijte ke stažení klienta Azure VPN.

#### <a name="to-import-a-client-profile"></a><a name="import"></a>Import profilu klienta

1. Na stránce vyberte **Importovat**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import1.jpg)

2. Přejděte do souboru XML profilu a vyberte ho. S vybraným souborem vyberte **Otevřít**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import2.jpg)

3. Zadejte název profilu a vyberte **Uložit**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import3.jpg)

4. Chcete-li se připojit k síti VPN, vyberte **možnost Připojit.**

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import4.jpg)

5. Po připojení se ikona změní na zelenou a řekne **Připojeno**.

    ![import](./media/openvpn-azure-ad-tenant-multi-app/import/import5.jpg)

#### <a name="to-delete-a-client-profile"></a><a name="delete"></a>Odstranění profilu klienta

1. Vyberte tři tečky (...) vedle profilu klienta, který chcete odstranit. Potom vyberte **Odebrat**.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete1.jpg)

2. Vyberte **Odebrat,** chcete-li odstranit.

    ![delete](./media/openvpn-azure-ad-tenant-multi-app/delete/delete2.jpg)

#### <a name="to-diagnose-connection-issues"></a><a name="diagnose"></a>Diagnostika problémů s připojením

1. Chcete-li diagnostikovat problémy s připojením, můžete použít nástroj **diagnostika.** Vyberte tři tečky (...) vedle připojení VPN, které chcete diagnostikovat, abyste odhalili nabídku. Pak vyberte **Diagnostikovat**.

    ![Diagnostikovat](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose1.jpg)

2. Na stránce **Vlastnosti připojení** vyberte **Spustit diagnózu**.

    ![Diagnostikovat](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose2.jpg)

3. Přihlaste se pomocí svých přihlašovacích údajů.

    ![Diagnostikovat](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose3.jpg)

4. Prohlédněte si výsledky diagnostiky.

    ![Diagnostikovat](./media/openvpn-azure-ad-tenant-multi-app/diagnose/diagnose4.jpg)

## <a name="10-view-your-virtual-wan"></a><a name="viewwan"></a>10. Zobrazení virtuální sítě WAN

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

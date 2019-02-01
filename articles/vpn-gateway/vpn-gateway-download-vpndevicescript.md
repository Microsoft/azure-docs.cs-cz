---
title: 'Stažení skriptů konfigurace zařízení VPN pro připojení S2S VPN: Azure Resource Manageru | Dokumentace Microsoftu'
description: Tento článek vás provede stažením konfiguračních skriptů zařízení VPN pro připojení k síti VPN S2S s bránami Azure VPN Gateway pomocí Azure Resource Manageru.
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: 0b0a7ce63fa2d0154300dd2e8f9cf88d985a8a0a
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55507686"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Stažení skriptů konfigurace zařízení VPN pro připojení k síti VPN S2S

Tento článek vás provede stažením konfiguračních skriptů zařízení VPN pro připojení k síti VPN S2S s bránami Azure VPN Gateway pomocí Azure Resource Manageru. Následující diagram znázorňuje pracovní postup vysoké úrovně.

![stažení skriptu](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Tato zařízení mají k dispozici skriptů:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>Informace o konfiguračních skriptů zařízení VPN

Připojení k síti VPN mezi různými místy se skládá z Azure VPN gateway, místním zařízením VPN a tunel IPsec S2S VPN spojuje. Typický pracovní postup zahrnuje následující kroky:

1. Vytvoření a konfigurace služby Azure VPN gateway (Brána virtuální sítě)
2. Vytvoření a konfigurace brány místní sítě Azure, který představuje vaše místní sítě a zařízením VPN
3. Vytvoření a konfigurace připojení k Azure VPN mezi Azure VPN gateway a bránu místní sítě
4. Konfigurace místního zařízení VPN reprezentována bránu místní sítě pro vytvoření skutečné tunelu S2S VPN pomocí služby Azure VPN gateway

Můžete provést kroky 1 až 3 pomocí Azure [portál](vpn-gateway-howto-site-to-site-resource-manager-portal.md), [PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md), nebo [CLI](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Poslední krok zahrnuje konfiguraci místních zařízení VPN mimo Azure. Tato funkce umožňuje stáhnout konfigurační skript zařízení VPN s odpovídajícími hodnotami vaše brána Azure VPN gateway, virtuální sítě a předpony adres místní sítě a vlastnosti připojení VPN, už vytvořilo atd. Můžete použít skript jako výchozí bod, nebo použijte skript přímo pro vaše místní zařízení VPN přes konzolu configuration.

> [!IMPORTANT]
> * Syntaxe pro každý konfigurační skript zařízení VPN je jiné a silně závisí na modely a verzemi firmwaru. Věnujte zvláštní pozornost váš model a verze informací o zařízení proti dostupných šablon.
> * Chybí některé hodnoty parametrů musí být jedinečný na zařízení a nemůže dá určit bez přístupu k zařízení. Azure generovaných konfiguračních skriptů předem zadejte tyto hodnoty, ale je potřeba zajistit, že zadané hodnoty jsou platné ve vašem zařízení. Příklady:
>    * Čísla rozhraní
>    * Čísla v seznamu řízení přístupu
>    * Názvy zásad nebo čísla, atd.
> * Vyhledejte klíčové slovo "**nahradit**" vložený v skript, který chcete najít parametry, je třeba ověřit před použitím skriptu.
> * Některé šablony zahrnují "**VYČIŠTĚNÍ**" části můžete použít k odebrání konfigurace. Vyčištění oddíly jsou označené jako komentář ve výchozím nastavení.

## <a name="download-the-configuration-script-from-azure-portal"></a>Stáhnout konfigurační skript z webu Azure portal

Vytvoření služby Azure VPN gateway, bránu místní sítě a prostředek připojení spojuje. Na následující stránce provede vás těmito kroky:

* [Vytvoření připojení Site-to-Site na webu Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Jakmile se vytvoří prostředek připojení, postupujte podle níže uvedených pokynů ke stažení konfiguračních skriptů zařízení VPN:

1. V prohlížeči přejděte [webu Azure portal](http://portal.azure.com) a v případě potřeby, přihlaste se pomocí svého účtu Azure
2. Přejdete k prostředku připojení, které jste vytvořili. Seznam všech prostředků připojení můžete najít kliknutím na "Všechny služby" a "Sítě" a "Připojení".

    ![seznam připojení](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Klikněte na připojení, které chcete konfigurovat.

    ![Přehled připojení](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Klikněte na odkaz "Stáhnout konfiguraci" jako zvýrazněné červeně na stránce Přehled připojení; Otevře se stránka "Download configuration".

    ![stažení skriptu-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Vybrat verzi modelu řady a firmware pro vaše zařízení VPN a potom klikněte na tlačítko "Stáhnout konfigurace".

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Zobrazí se výzva k uložení staženého skriptu (textového souboru) z prohlížeče.
7. Po stažení skriptů konfigurace otevřete jej pomocí textového editoru a vyhledejte – klíčové slovo "Nahradit" k identifikaci a zkontrolujte parametry, které možná bude nutné vyměnit.

    ![edit-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Stáhnout konfigurační skript pomocí prostředí Azure PowerShell

Můžete také stáhnout konfigurační skript pomocí prostředí Azure PowerShell, jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzureRmVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzureRmVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Použít konfigurační skript zařízení VPN

Po stažení a ověří konfigurační skript, dalším krokem je použít skript zařízení VPN. Skutečné postup se liší v závislosti na modely a využívá zařízení sítě VPN. Vaše zařízení VPN najdete příručky pro operace nebo na stránkách instrukce.

## <a name="next-steps"></a>Další postup

Pokračovat v konfiguraci vaší [připojení Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

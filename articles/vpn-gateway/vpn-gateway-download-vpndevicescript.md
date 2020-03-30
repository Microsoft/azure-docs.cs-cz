---
title: Stažení konfiguračních skriptů zařízení VPN pro připojení S2S VPN
description: Tento článek vás provede stažením konfiguračních skriptů zařízení VPN pro připojení Vpn S2S pomocí Azure Resource Gateways.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
manager: rossort
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2019
ms.author: yushwang
ms.openlocfilehash: f905e27f48a0bf9181625bbba07549a13d9420cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77162132"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Stažení konfiguračních skriptů zařízení VPN pro připojení S2S VPN

Tento článek vás provede stažením konfiguračních skriptů zařízení VPN pro připojení Vpn S2S pomocí Azure Resource Gateways. Následující diagram znázorňuje pracovní postup vysoké úrovně.

![download-skript](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Následující zařízení mají k dispozici skripty:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>O konfiguračních skriptech zařízení VPN

Připojení VPN mezi místními se skládá z brány Azure VPN, místního zařízení VPN a tunelového propojení VPN IPsec S2S, které je spojuje. Typický pracovní tok zahrnuje následující kroky:

1. Vytvoření a konfigurace brány Azure VPN (brána virtuální sítě)
2. Vytvoření a konfigurace brány místní sítě Azure, která představuje vaši místní síť a zařízení VPN
3. Vytvoření a konfigurace připojení Azure VPN mezi bránou Azure VPN a bránou místní sítě
4. Konfigurace místního zařízení VPN reprezentovaného bránou místní sítě za účelem vytvoření skutečného tunelu S2S VPN s bránou Azure VPN

Kroky 1 až 3 můžete provést pomocí [portálu](vpn-gateway-howto-site-to-site-resource-manager-portal.md)Azure , [PowerShellu](vpn-gateway-create-site-to-site-rm-powershell.md)nebo [cli](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Poslední krok zahrnuje konfiguraci místních zařízení VPN mimo Azure. Tato funkce umožňuje stáhnout konfigurační skript pro vaše zařízení VPN s odpovídajícími hodnotami brány Azure VPN, virtuální sítě a předpon místní síťové adresy a vlastností připojení VPN atd. Skript můžete použít jako výchozí bod nebo ho použít přímo na místní zařízení VPN prostřednictvím konfigurační konzoly.

> [!IMPORTANT]
> * Syntaxe pro každý konfigurační skript zařízení VPN se liší a silně závisí na modelech a verzích firmwaru. Věnujte zvláštní pozornost modelu zařízení a informacím o verzi podle dostupných šablon.
> * Některé hodnoty parametrů musí být v zařízení jedinečné a nelze je určit bez přístupu k zařízení. Konfigurační skripty generované Azure předem vyplnit tyto hodnoty, ale je třeba zajistit, že zadaný hodnoty jsou platné na vašem zařízení. Příklady:
>    * Čísla rozhraní
>    * Čísla seznamu řízení přístupu
>    * Názvy nebo čísla zásad atd.
> * Vyhledejte klíčové slovo "**REPLACE**", vložené do skriptu, abyste našli parametry, které je třeba ověřit před použitím skriptu.
> * Některé šablony obsahují oddíl **"CLEANUP",** který můžete použít k odebrání konfigurací. Oddíly čištění jsou ve výchozím nastavení zakomentovány.

## <a name="download-the-configuration-script-from-azure-portal"></a>Stažení konfiguračního skriptu z webu Azure Portal

Vytvořte bránu Azure VPN, bránu místní sítě a prostředek připojení, který tyto dvě položky propojuje. Následující stránka vás provede kroky:

* [Vytvoření připojení typu Site-to-Site na webu Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Po vytvoření prostředku připojení stáhněte konfigurační skripty zařízení VPN podle následujících pokynů:

1. Z prohlížeče přejděte na [portál Azure](https://portal.azure.com) a v případě potřeby se přihlaste pomocí svého účtu Azure.
2. Přejděte na prostředek připojení, který jste vytvořili. Seznam všech prostředků připojení můžete najít kliknutím na "Všechny služby", potom na "NETWORKING" a "Připojení".

    ![seznam připojení](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Klikněte na připojení, které chcete konfigurovat.

    ![přehled připojení](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Klikněte na odkaz "Stáhnout konfiguraci", jak je zvýrazněnčerveně na stránce Přehled připojení; otevře se stránka "Stáhnout konfiguraci".

    ![download-skript-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Vyberte rodinu modelů a verzi firmwaru pro vaše zařízení VPN a klikněte na tlačítko "Stáhnout konfiguraci".

    ![download66-skript-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Budete vyzváni k uložení staženého skriptu (textového souboru) z prohlížeče.
7. Po stažení konfiguračního skriptu jej otevřete textovým editorem a vyhledejte klíčové slovo "REPLACE", abyste identifikovali a prozkoumali parametry, které může být nutné nahradit.

    ![upravit skript](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Stažení konfiguračního skriptu pomocí Azure PowerShellu



Konfigurační skript můžete taky stáhnout pomocí Azure PowerShellu, jak je znázorněno v následujícím příkladu:

```azurepowershell-interactive
$RG          = "TestRG1"
$GWName      = "VNet1GW"
$Connection  = "VNet1toSite1"

# List the available VPN device models and versions
Get-AzVirtualNetworkGatewaySupportedVpnDevice -Name $GWName -ResourceGroupName $RG

# Download the configuration script for the connection
Get-AzVirtualNetworkGatewayConnectionVpnDeviceConfigScript -Name $Connection -ResourceGroupName $RG -DeviceVendor Juniper -DeviceFamily Juniper_SRX_GA -FirmwareVersion Juniper_SRX_12.x_GA
```

## <a name="apply-the-configuration-script-to-your-vpn-device"></a>Použití konfiguračního skriptu na zařízení VPN

Po stažení a ověření konfiguračního skriptu je dalším krokem použití skriptu na zařízení VPN. Skutečný postup se liší v závislosti na vašich zařízeních VPN a modelech. Informace o obslužných příručkách nebo na stránkách s pokyny pro vaše zařízení VPN naleznete.

## <a name="next-steps"></a>Další kroky

Pokračovat v konfiguraci [připojení site-to-site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

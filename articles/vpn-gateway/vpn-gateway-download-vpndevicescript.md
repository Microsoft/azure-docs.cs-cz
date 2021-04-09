---
title: Stáhnout skripty pro konfiguraci zařízení VPN pro připojení S2S VPN
description: Tento článek vás provede stažením skriptů pro konfiguraci zařízení VPN pro připojení S2S VPN se službou Azure VPN Gateway pomocí Azure Resource Manager.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: d2593c656e17310c5afb4a897b94cc8c12a618f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98879490"
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Stáhnout skripty pro konfiguraci zařízení VPN pro připojení S2S VPN

Tento článek vás provede stažením skriptů pro konfiguraci zařízení VPN pro připojení S2S VPN se službou Azure VPN Gateway pomocí Azure Resource Manager. Následující diagram znázorňuje pracovní postup vysoké úrovně.

![stáhnout skript](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Následující zařízení mají dostupné skripty:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about-vpn-device-configuration-scripts"></a><a name="about"></a>Informace o konfiguračních skriptech zařízení VPN

Připojení VPN mezi různými místy se skládá z brány Azure VPN Gateway, místního zařízení VPN a tunelového připojení VPN S2S IPsec spojujících tyto dvě. Typický pracovní postup zahrnuje následující kroky:

1. Vytvoření a konfigurace brány Azure VPN Gateway (Brána virtuální sítě)
2. Vytvořte a nakonfigurujte bránu místní sítě Azure, která představuje vaši místní síť a zařízení VPN.
3. Vytvoření a konfigurace připojení Azure VPN mezi bránou Azure VPN a bránou místní sítě
4. Nakonfigurujte místní zařízení VPN reprezentované bránou místní sítě, aby se navázalo skutečné tunelové propojení VPN S2S s bránou Azure VPN.

Kroky 1 až 3 můžete provést pomocí webu Azure [Portal](./tutorial-site-to-site-portal.md), [PowerShellu](vpn-gateway-create-site-to-site-rm-powershell.md)nebo rozhraní příkazového [řádku](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Poslední krok zahrnuje konfiguraci místních zařízení VPN mimo Azure. Tato funkce umožňuje stáhnout konfigurační skript pro vaše zařízení VPN s odpovídajícími hodnotami vaší brány Azure VPN Gateway, virtuální sítě a místních předpon adres sítě a vlastností připojení VPN, a to i v případě, že jsou už vyplněné. Skript můžete použít jako výchozí bod, nebo můžete skript použít přímo na místní zařízení VPN prostřednictvím konzoly Konfigurace.

> [!IMPORTANT]
> * Syntaxe pro každý konfigurační skript zařízení VPN je odlišná a silně závislá na modelech a verzích firmwaru. Věnujte zvláštní pozornost vašemu modelu zařízení a informacím o verzi na dostupných šablonách.
> * Některé hodnoty parametrů musí být v zařízení jedinečné a nelze je určit bez přístupu k zařízení. Konfigurační skripty generované službou Azure tyto hodnoty předem vyplní, ale je potřeba zajistit, aby zadané hodnoty byly na vašem zařízení platné. Příklady:
>    * Čísla rozhraní
>    * Čísla seznamů řízení přístupu
>    * Názvy nebo čísla zásad atd.
> * Před použitím skriptu vyhledejte klíčové slovo "**Replace**", vložené ve skriptu, abyste našli parametry, které je potřeba ověřit.
> * Některé šablony obsahují oddíl "**Vyčištění**", který můžete použít pro odebrání konfigurací. Oddíly čištění jsou ve výchozím nastavení zakomentovány.

## <a name="download-the-configuration-script-from-azure-portal"></a>Stažení konfiguračního skriptu z Azure Portal

Vytvořte bránu Azure VPN Gateway, bránu místní sítě a prostředek připojení s připojením obou. Následující stránka vás provede kroky:

* [Vytvoření připojení typu Site-to-Site na webu Azure Portal](./tutorial-site-to-site-portal.md)

Po vytvoření prostředku připojení postupujte podle pokynů níže a Stáhněte si konfigurační skripty pro zařízení VPN:

1. V prohlížeči přejděte na [Azure Portal](https://portal.azure.com) a v případě potřeby se přihlaste pomocí účtu Azure.
2. Přejít na prostředek připojení, který jste vytvořili. Seznam všech prostředků připojení můžete najít kliknutím na všechny služby, pak na síť a připojení.

    ![seznam připojení](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Klikněte na připojení, které chcete nakonfigurovat.

    ![připojení – přehled](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Klikněte na odkaz "Stáhnout konfiguraci", který je na stránce Přehled připojení zvýrazněný červeně. Tím se otevře stránka pro stažení konfigurace.

    ![stáhnout – skript-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Vyberte modelovou rodinu a verzi firmwaru pro vaše zařízení VPN a pak klikněte na tlačítko Stáhnout konfiguraci.

    ![download66-Script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Zobrazí se výzva k uložení staženého skriptu (textový soubor) z prohlížeče.
7. Po stažení konfiguračního skriptu ho otevřete pomocí textového editoru a vyhledejte klíčové slovo "nahradit", abyste identifikovali a prozkoumali parametry, které je potřeba nahradit.

    ![upravit skript](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Stažení konfiguračního skriptu pomocí Azure PowerShell



Konfigurační skript můžete také stáhnout pomocí Azure PowerShell, jak je znázorněno v následujícím příkladu:

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

Po stažení a ověření konfiguračního skriptu je dalším krokem použití skriptu na vaše zařízení VPN. Skutečný postup se liší v závislosti na tom, jak vaše zařízení VPN vytváří a modelují. Projděte si příručky operace nebo pokyny pro vaše zařízení VPN.

## <a name="next-steps"></a>Další kroky

Pokračujte v konfiguraci [připojení Site-to-site](./tutorial-site-to-site-portal.md).
---
title: 'Stáhněte si skripty konfigurace zařízení VPN pro připojení k síti VPN S2S: Azure Resource Manager | Microsoft Docs'
description: Tento článek vás provede stahování skripty konfigurace zařízení VPN pro připojení k síti VPN S2S s Azure VPN Gateway pomocí Azure Resource Manager.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: yushwang
ms.openlocfilehash: 254f5012bfbf827aebc20d90405636dcb204193c
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="download-vpn-device-configuration-scripts-for-s2s-vpn-connections"></a>Stáhněte si skripty konfigurace zařízení VPN pro připojení k síti VPN S2S

Tento článek vás provede stahování skripty konfigurace zařízení VPN pro připojení k síti VPN S2S s Azure VPN Gateway pomocí Azure Resource Manager. Následující diagram znázorňuje pracovní postup vysoké úrovně.

![skript stažení](./media/vpn-gateway-download-vpndevicescript/downloaddevicescript.png)

Tato zařízení mají k dispozici skriptů:

[!INCLUDE [scripts](../../includes/vpn-gateway-device-configuration-scripts.md)]

## <a name="about"></a>O skriptech konfigurace zařízení VPN

Připojení VPN mezi různými místy se skládá z bránu VPN Azure VPN, místní zařízení VPN a připojení dvě tunelové propojení IPsec S2S VPN. Typické pracovní postup obsahuje následující kroky:

1. Vytvoření a konfigurace služby Azure VPN gateway (Brána virtuální sítě)
2. Vytvořte a nakonfigurujte bránu Azure místní sítě, který představuje místní sítí a zařízení VPN
3. Vytvořit a nakonfigurovat připojení k Azure VPN mezi Azure VPN gateway a bránu místní sítě
4. Nakonfigurujte místní zařízení VPN reprezentována bránu místní sítě k vytvoření skutečné tunelu S2S VPN s Azure VPN gateway

Můžete provést kroky 1 až 3 pomocí Azure [portál](vpn-gateway-howto-site-to-site-resource-manager-portal.md), [prostředí PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md), nebo [rozhraní příkazového řádku](vpn-gateway-howto-site-to-site-resource-manager-cli.md). Poslední krok zahrnuje konfiguraci místní zařízení VPN mimo Azure. Tato funkce umožňuje stáhnout konfigurační skript pro vaše zařízení VPN s odpovídajícími hodnotami vaší brány Azure VPN, virtuální sítě a předpony adres místní sítě a vlastnosti připojení VPN, již vyplněno atd. Můžete použít skript jako počáteční bod, nebo použít skript přímo pro vaše místní zařízení VPN prostřednictvím konzoly configuration.

> [!IMPORTANT]
> * Syntaxe pro každý skript pro konfiguraci zařízení VPN je jiné a silně závislá na modely a verzí firmwaru. Věnujte zvláštní pozornost vaše zařízení modelu a informace o verzi proti dostupné šablony.
> * Některé hodnoty parametru musí být jedinečný v zařízení a nelze určit bez přístupu k zařízení. Generované Azure konfigurační skripty předem vyplnit tyto hodnoty, ale je potřeba zajistit, že zadané hodnoty jsou platné ve vašem zařízení. Příklady:
>    * Čísla rozhraní
>    * Čísla v seznamu řízení přístupu
>    * Názvy zásad nebo čísla, atd.
> * Vyhledejte – klíčové slovo, "**nahradit**" vložený v skript, který chcete najít parametry, je třeba ověřit před použitím skriptu.
> * Některé šablony zahrnují "**čištění**" části můžete použít k odebrání konfigurace. Ve výchozím nastavení jsou komentované části čištění.

## <a name="download-the-configuration-script-from-azure-portal"></a>Stáhnout konfigurační skript z portálu Azure

Vytvořte bránu VPN Azure VPN, brána místní sítě a prostředek připojení dvě připojení. Na následující stránce vás provede kroky:

* [Na portálu Azure vytvořit připojení Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md)

Po vytvoření prostředek připojení postupujte podle pokynů ke stažení skripty konfigurace zařízení VPN:

1. V prohlížeči přejděte na [portál Azure](http://portal.azure.com) a v případě potřeby, přihlaste se pomocí účtu Azure
2. Přejděte na prostředek připojení, kterou jste vytvořili. Seznam všech prostředků připojení můžete najít tak, že kliknete "Všechny služby" a "Sítě", "Připojení".

    ![seznam připojení](./media/vpn-gateway-download-vpndevicescript/connectionlist.png)

3. Klikněte na připojení, které chcete nakonfigurovat.

    ![připojení – přehled](./media/vpn-gateway-download-vpndevicescript/connectionoverview.png)

4. Klikněte na odkaz "Stáhnout konfigurace" jako zvýrazněné červeně na stránce Přehled připojení; Otevře se stránka "Stahování konfigurace".

    ![stažení skriptu-1](./media/vpn-gateway-download-vpndevicescript/downloadscript-1.png)

5. Vyberte verzi modelu rodiny a firmware pro vaše zařízení VPN a potom klikněte na tlačítko "Stáhnout konfigurace".

    ![download66-script-2](./media/vpn-gateway-download-vpndevicescript/downloadscript-2.PNG)

6. Zobrazí se výzva k uložení stažených skriptu (textový soubor) z prohlížeče.
7. Jakmile jste stáhli konfigurační skript, otevřete jej pomocí textového editoru a vyhledejte – klíčové slovo "Nahradit" k identifikaci a zkontrolujte parametry, které může být nutné vyměnit.

    ![edit-script](./media/vpn-gateway-download-vpndevicescript/editscript.png)

## <a name="download-the-configuration-script-using-azure-powershell"></a>Stáhnout konfigurační skript pomocí Azure PowerShell

Můžete také stáhnout konfigurační skript pomocí Azure PowerShell, jak je znázorněno v následujícím příkladu:

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

Po stáhnout a ověřit konfigurační skript, dalším krokem je použít skript zařízení VPN. Skutečný postup se liší podle díky zařízení VPN a modely. Poraďte příručky pro operaci nebo na stránkách instrukce pro vaše zařízení VPN.

## <a name="next-steps"></a>Další postup

Pokračujte v konfiguraci vaší [připojení Site-to-Site](vpn-gateway-howto-site-to-site-resource-manager-portal.md).
---
title: Resetování služby Azure VPN gateway k opětovnému zavedení tunelových propojení IPsec | Microsoft Docs
description: Tento článek vás provede procesem resetování služby Azure VPN Gateway k opětovnému zavedení tunelových propojení IPsec. Článek se týká bran VPN jak classic a modelem nasazení Resource Manager.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 79d77cb8-d175-4273-93ac-712d7d45b1fe
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/24/2017
ms.author: cherylmc
ms.openlocfilehash: 604af54d034b6b51a96ff098827e2352a163be18
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884549"
---
# <a name="reset-a-vpn-gateway"></a>Resetování brány VPN Gateway

Resetování brány Azure VPN je užitečné v případě ztráty připojení VPN mezi lokalitami na jednom nebo více tunelech VPN typu Site-to-Site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN. Tento článek vám umožňuje resetování služby VPN gateway.

### <a name="what-happens-during-a-reset"></a>Co se stane během vynulování?

Brána sítě VPN se skládá ze dvou instancí virtuálního počítače spuštěných v konfiguraci aktivní pohotovostní. Při resetování brány, brána se restartuje a pak znovu použije konfigurace mezi různými místy k němu. Brána si ponechá stejnou veřejnou IP adresu. To znamená, že nebudete muset aktualizovat konfiguraci směrovače VPN na novou veřejnou IP adresu brány Azure VPN.

Pokud vydáte příkaz k resetování brány, se okamžitě resetuje právě aktivní instance brány Azure VPN. Při selhání pohotovostní instancí od aktivní instance (která se resetuje), bude ke krátké prodlevě. Prodleva by neměla být delší než jedna minuta.

Pokud po prvním resetování nedojde k obnovení připojení, znovu vydejte stejný příkaz pro resetování druhé instance virtuálního počítače (nové aktivní brány). Pokud se vyžadují dvě restartování za sebou, bude doba, po kterou se obě instance virtuálního počítače (aktivní a pohotovostní) restartují, trochu delší. To způsobí delší prodlevu propojování VPN – 2 až 4 minuty, než se dokončí resetování virtuálních počítačů.

Po dvou resetováních Pokud stále dochází k potížím připojení mezi různými místy, otevřete prosím žádost o podporu na portálu Azure.

## <a name="before"></a>Než začnete

Před restartováním brány ověřte pro každé tunelové propojení VPN typu Site-to-Site (S2S) s protokolem IPsec klíčové body následujícího seznamu. Jakákoli neshoda v těchto bodech způsobí odpojení tunelových propojení VPN typu S2S. Ověřením a opravením konfigurace pro místní a brány Azure VPN uloží můžete z zbytečnému resetování a přerušení ostatních fungujících připojení na brány.

Před resetováním brány ověřte následující položky:

* Internetové IP adresy (virtuální IP adresy) pro bránu Azure VPN a bránu místní VPN jsou správně nakonfigurovány v zásadách Azure VPN i v zásadách místní VPN.
* Předsdílený klíč musí být stejný v bráně Azure VPN i v bráně místní VPN.
* Pokud použijete určitou konfiguraci protokolu IPsec/IKE, jako například šifrování, algoritmy hash nebo metodu Perfect Forward Secrecy (PFS), ujistěte se, že je stejně nakonfigurovaná brána Azure VPN i brána místní sítě.

## <a name="portal"></a>Portál Azure

Můžete resetovat bránu VPN Resource Manageru pomocí portálu Azure. Pokud chcete resetovat bránu classic, přečtěte si téma [prostředí PowerShell](#resetclassic) kroky.

### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

1. Otevřete [portál Azure](https://portal.azure.com) a přejděte na bránu virtuální sítě Resource Manager, který chcete obnovit.
2. V okně pro bránu virtuální sítě klikněte na 'Resetovat'.

  ![Okno resetování brány VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. V okně resetování klikněte na **resetovat** tlačítko.

## <a name="ps"></a>Prostředí PowerShell

### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

Rutina pro resetování brány je **resetování AzureRmVirtualNetworkGateway**. Před provedením provést obnovení, ujistěte se, abyste měli nejnovější verzi [rutiny prostředí PowerShell Resource Manager](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0). Následující příklad resetuje bránu virtuální sítě s názvem VNet1GW ve skupině prostředků TestRG1:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroup TestRG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Výsledek:

Jakmile se zobrazí návratový výsledek, můžete předpokládat resetování brány byla úspěšná. Však není nic v návratové výsledek, který explicitně označuje, že obnovení bylo úspěšné. Pokud chcete podrobněji prohlížet historii, přesně při resetování brány došlo k chybě, můžete zobrazit tyto informace v [portál Azure](https://portal.azure.com). Na portálu, přejděte na **'GatewayName} -> Stav prostředku**.

### <a name="resetclassic"></a>Model nasazení Classic

Rutina pro resetování brány je **Reset-AzureVNetGateway**. Před provedením provést obnovení, ujistěte se, abyste měli nejnovější verzi [rutiny prostředí PowerShell služby správy (SM)](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0). Následující příklad resetuje bránu pro virtuální síť s názvem "ContosoVNet":

```powershell
Reset-AzureVNetGateway –VnetName “ContosoVNet”
```

Výsledek:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="cli"></a>Rozhraní příkazového řádku Azure

Chcete-li obnovit bránu, použijte [resetovat az brány virtuální sítě-](https://docs.microsoft.com/cli/azure/network/vnet-gateway#az_network_vnet_gateway_reset) příkaz. Následující příklad resetuje bránu virtuální sítě s názvem VNet5GW ve skupině prostředků TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Výsledek:

Jakmile se zobrazí návratový výsledek, můžete předpokládat resetování brány byla úspěšná. Však není nic v návratové výsledek, který explicitně označuje, že obnovení bylo úspěšné. Pokud chcete podrobněji prohlížet historii, přesně při resetování brány došlo k chybě, můžete zobrazit tyto informace v [portál Azure](https://portal.azure.com). Na portálu, přejděte na **'GatewayName} -> Stav prostředku**.
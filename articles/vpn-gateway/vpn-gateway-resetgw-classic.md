---
title: Resetování služby Azure VPN gateway k opětovnému zavedení tunelových propojení IPsec | Dokumentace Microsoftu
description: Tento článek vás provede resetování služby Azure VPN Gateway k opětovnému zavedení tunelových propojení IPsec. Článek se týká bran VPN classic, a modely nasazení Resource Manager.
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
ms.openlocfilehash: d23ed383bee3346cfbb20c6935dd3630e137c179
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45573892"
---
# <a name="reset-a-vpn-gateway"></a>Resetování brány VPN Gateway

Resetování brány Azure VPN je užitečné v případě ztráty připojení VPN mezi lokalitami na jednom nebo více tunelech VPN typu Site-to-Site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN. Tento článek pomáhá resetovat bránu VPN.

### <a name="what-happens-during-a-reset"></a>Co se stane při obnovení nastavení?

Brány VPN se skládá ze dvou instancí virtuálních počítačů, které jsou spuštěné v konfiguraci aktivní pohotovostní. Když resetujete zařízení brány, restartuje brány a potom znovu použije konfigurace mezi různými místy, které ho. Brána si ponechá stejnou veřejnou IP adresu. To znamená, že nebudete muset aktualizovat konfiguraci směrovače VPN na novou veřejnou IP adresu brány Azure VPN.

Když vydáte příkaz k resetování brány, se okamžitě resetuje právě aktivní instance brány Azure VPN. Během převzetí služeb můžete pohotovostní instancí od aktivní instance (která se resetuje), bude ke krátké prodlevě. Prodleva by neměla být delší než jedna minuta.

Pokud po prvním resetování nedojde k obnovení připojení, znovu vydejte stejný příkaz pro resetování druhé instance virtuálního počítače (nové aktivní brány). Pokud se vyžadují dvě restartování za sebou, bude doba, po kterou se obě instance virtuálního počítače (aktivní a pohotovostní) restartují, trochu delší. To způsobí delší prodlevu propojování VPN – 2 až 4 minuty, než se dokončí resetování virtuálních počítačů.

Po dvou resetováních Pokud stále máte problémy s připojením mezi různými místy, otevřete prosím prostřednictvím žádosti o podporu z portálu Azure portal.

## <a name="before"></a>Než začnete

Před restartováním brány ověřte pro každé tunelové propojení VPN typu Site-to-Site (S2S) s protokolem IPsec klíčové body následujícího seznamu. Jakákoli neshoda v těchto bodech způsobí odpojení tunelových propojení VPN typu S2S. Ověřením a opravením konfigurace pro místní a bránami Azure VPN vám ušetří zbytečnému resetování a přerušení ostatních fungujících připojení na brány.

Před resetováním brány ověřte následující body:

* Internetové IP adresy (virtuální IP adresy) pro bránu Azure VPN a bránu místní VPN jsou správně nakonfigurovány v zásadách Azure VPN i v zásadách místní VPN.
* Předsdílený klíč musí být stejný v bráně Azure VPN i v bráně místní VPN.
* Pokud použijete určitou konfiguraci protokolu IPsec/IKE, jako například šifrování, algoritmy hash nebo metodu Perfect Forward Secrecy (PFS), ujistěte se, že je stejně nakonfigurovaná brána Azure VPN i brána místní sítě.

## <a name="portal"></a>Azure portal

Můžete resetovat bránu VPN Resource Manageru pomocí webu Azure portal. Pokud chcete resetovat bránu classic, přečtěte si článek [Powershellu](#resetclassic) kroky.

### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

1. Otevřít [webu Azure portal](https://portal.azure.com) a přejděte k bráně virtuální sítě Resource Manageru, kterou chcete obnovit.
2. V okně pro bránu virtuální sítě klikněte na tlačítko "Obnovit".

  ![Resetování brány VPN Gateway okno](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. V okně obnovit klikněte na tlačítko **resetování** tlačítko.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

Rutina pro obnovení brány je **Reset-AzureRmVirtualNetworkGateway**. Před provedením obnovení, ujistěte se, že máte nejnovější verzi [rutin Powershellu pro Resource Manager](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.0.0). Následující příklad resetuje bránu virtuální sítě ve skupině prostředků TestRG1 s názvem brány VNet1GW:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Výsledek:

Když obdržíte výsledku vrácení, můžete předpokládat resetování brány byla úspěšná. Ale nic ve výsledku vrácené explicitně označuje, že obnovení bylo úspěšné. Pokud chcete prohlédněte si blíže Historie zobrazíte přesně při resetování brány došlo k chybě, můžete zobrazit tyto informace v [webu Azure portal](https://portal.azure.com). Na portálu přejděte na **"Název brány" -> Resource Health**.

### <a name="resetclassic"></a>Model nasazení Classic

Rutina pro obnovení brány je **Reset-AzureVNetGateway**. Před provedením obnovení, ujistěte se, že máte nejnovější verzi [rutiny Powershellu služby správy (SM)](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets). Následující příklad resetuje bránu pro virtuální síť s názvem "ContosoVNet":

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

Chcete-li obnovit brány, použijte [az network vnet-gateway reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway#az_network_vnet_gateway_reset) příkazu. Následující příklad resetuje bránu virtuální sítě s názvem VNet5GW ve skupině prostředků TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Výsledek:

Když obdržíte výsledku vrácení, můžete předpokládat resetování brány byla úspěšná. Ale nic ve výsledku vrácené explicitně označuje, že obnovení bylo úspěšné. Pokud chcete prohlédněte si blíže Historie zobrazíte přesně při resetování brány došlo k chybě, můžete zobrazit tyto informace v [webu Azure portal](https://portal.azure.com). Na portálu přejděte na **"Název brány" -> Resource Health**.

---
title: Resetování služby Azure VPN gateway k opětovnému zavedení tunelových propojení IPsec | Dokumentace Microsoftu
description: Tento článek vás provede resetování služby Azure VPN Gateway k opětovnému zavedení tunelových propojení IPsec. Článek se týká bran VPN classic, a modely nasazení Resource Manager.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/14/2019
ms.author: cherylmc
ms.openlocfilehash: 6e57979e2c43cc47504495cce23947b93abb4020
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414929"
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

## <a name="portal"></a>Azure Portal

Můžete resetovat bránu VPN Resource Manageru pomocí webu Azure portal. Pokud chcete resetovat bránu classic, přečtěte si článek [Powershellu](#resetclassic) kroky.

### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

1. Otevřít [webu Azure portal](https://portal.azure.com) a přejděte k bráně virtuální sítě Resource Manageru, kterou chcete obnovit.
2. V okně pro bránu virtuální sítě klikněte na tlačítko "Obnovit".

  ![Resetování brány VPN Gateway okno](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. V okně obnovit klikněte na tlačítko **resetování** tlačítko.

## <a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Rutina pro obnovení brány je **resetování AzVirtualNetworkGateway**. Před provedením obnovení, ujistěte se, že máte nejnovější verzi [rutin Powershellu pro Resource Manager](https://docs.microsoft.com/powershell/azure/azurerm/install-Az-ps?view=azurermps-4.0.0). Následující příklad resetuje bránu virtuální sítě ve skupině prostředků TestRG1 s názvem brány VNet1GW:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
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

## <a name="cli"></a>Azure CLI

Chcete-li obnovit brány, použijte [az network vnet-gateway reset](https://docs.microsoft.com/cli/azure/network/vnet-gateway) příkazu. Následující příklad resetuje bránu virtuální sítě s názvem VNet5GW ve skupině prostředků TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Výsledek:

Když obdržíte výsledku vrácení, můžete předpokládat resetování brány byla úspěšná. Ale nic ve výsledku vrácené explicitně označuje, že obnovení bylo úspěšné. Pokud chcete prohlédněte si blíže Historie zobrazíte přesně při resetování brány došlo k chybě, můžete zobrazit tyto informace v [webu Azure portal](https://portal.azure.com). Na portálu přejděte na **"Název brány" -> Resource Health**.

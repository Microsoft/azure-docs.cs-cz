---
title: Obnovení brány Azure VPN pro obnovení tunelového propojení IPsec
description: Tento článek vás provede obnovením brány Azure VPN gateway a obnovením tunelových propojení IPsec. Článek se vztahuje na brány VPN v klasických i v modelech nasazení Správce prostředků.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e3a5807a0ccfa39cc80acacedaa5fb4d3afaaed3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244417"
---
# <a name="reset-a-vpn-gateway"></a>Resetování brány VPN Gateway

Resetování brány Azure VPN je užitečné v případě ztráty připojení VPN mezi lokalitami na jednom nebo více tunelech VPN typu Site-to-Site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN. Tento článek vám pomůže obnovit bránu VPN.

### <a name="what-happens-during-a-reset"></a>Co se stane během resetu?

Brána VPN se skládá ze dvou instancí virtuálních počítačů spuštěných v aktivní pohotovostní konfiguraci. Při obnovení brány restartuje bránu a potom znovu použije konfigurace mezi místními. Brána si ponechá stejnou veřejnou IP adresu. To znamená, že nebudete muset aktualizovat konfiguraci směrovače VPN na novou veřejnou IP adresu brány Azure VPN.

Když vydáte příkaz k obnovení brány, aktuální aktivní instance brány Azure VPN se restartuje okamžitě. Během převzetí služeb při selhání z aktivní instance (restartování) bude krátká mezera do instance v pohotovostním režimu. Prodleva by neměla být delší než jedna minuta.

Pokud po prvním resetování nedojde k obnovení připojení, znovu vydejte stejný příkaz pro resetování druhé instance virtuálního počítače (nové aktivní brány). Pokud se vyžadují dvě restartování za sebou, bude doba, po kterou se obě instance virtuálního počítače (aktivní a pohotovostní) restartují, trochu delší. To způsobí delší mezeru v připojení VPN, až 30 až 45 minut pro virtuální počítače k dokončení restartování.

Po dvou restartování, pokud stále dochází k problémům s připojením mezi místními oblastmi, otevřete žádost o podporu z portálu Azure.

## <a name="before-you-begin"></a><a name="before"></a>Než začnete

Před restartováním brány ověřte pro každé tunelové propojení VPN typu Site-to-Site (S2S) s protokolem IPsec klíčové body následujícího seznamu. Jakákoli neshoda v těchto bodech způsobí odpojení tunelových propojení VPN typu S2S. Ověření a oprava konfigurace pro místní brány a brány Azure VPN vám ušetří zbytečné restartování a přerušení pro ostatní pracovní připojení na brány.

Před resetováním brány ověřte následující položky:

* Internetové IP adresy (virtuální IP adresy) pro bránu Azure VPN a bránu místní VPN jsou správně nakonfigurovány v zásadách Azure VPN i v zásadách místní VPN.
* Předsdílený klíč musí být stejný v bráně Azure VPN i v bráně místní VPN.
* Pokud použijete určitou konfiguraci protokolu IPsec/IKE, jako například šifrování, algoritmy hash nebo metodu Perfect Forward Secrecy (PFS), ujistěte se, že je stejně nakonfigurovaná brána Azure VPN i brána místní sítě.

## <a name="azure-portal"></a><a name="portal"></a>Portál Azure

Bránu VPN Správce prostředků můžete obnovit pomocí portálu Azure. Pokud chcete obnovit klasickou bránu, přečtěte si postup [PowerShellu.](#resetclassic)

### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

1. Otevřete [portál Azure](https://portal.azure.com) a přejděte na bránu virtuální sítě Správce prostředků, kterou chcete resetovat.
2. V okně brány virtuální sítě klikněte na tlačítko Obnovit.

   ![Obnovit okno brány VPN](./media/vpn-gateway-howto-reset-gateway/reset-vpn-gateway-portal.png)
3. V okně Reset klepněte na tlačítko **Obnovit.**

## <a name="powershell"></a><a name="ps"></a>PowerShell

### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Rutina pro resetování brány je **Reset-AzVirtualNetworkGateway**. Před obnovením se ujistěte, že máte nejnovější verzi [rutin PowerShell Az](https://docs.microsoft.com/powershell/module/az.network). Následující příklad obnoví bránu virtuální sítě s názvem VNet1GW ve skupině prostředků TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Result:

Když obdržíte výsledek vrácení, můžete předpokládat, že obnovení brány bylo úspěšné. Však není nic v návratvýsledek, který označuje explicitně, že obnovení bylo úspěšné. Pokud se chcete podrobně podívat na historii, abyste přesně zjistili, kdy došlo k obnovení brány, můžete tyto informace zobrazit na [webu Azure Portal](https://portal.azure.com). Na portálu přejděte na **"GatewayName" -> Resource Health**.

### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Model nasazení Classic

Rutina pro resetování brány je **Reset-AzureVNetGateway**. Rutiny Prostředí Azure PowerShell pro správu služeb musí být nainstalovány místně na ploše. Azure Cloud Shell používat nedá. Před obnovením se ujistěte, že máte nejnovější verzi [rutin prostředí PowerShell pro správu služeb (SM).](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) Při použití tohoto příkazu se ujistěte, že používáte úplný název virtuální sítě. Klasické virtuální sítě, které byly vytvořeny pomocí portálu mají dlouhý název, který je vyžadován pro Prostředí PowerShell. Dlouhý název můžete zobrazit pomocí příkazu Get-AzureVNetConfig -ExportToFile C:\Myfoldername\NetworkConfig.xml.

Následující příklad obnoví bránu pro virtuální síť s názvem "Skupina TestRG1 TestVNet1" (který ukazuje jako jednoduše "TestVNet1" na portálu):

```powershell
Reset-AzureVNetGateway –VnetName 'Group TestRG1 TestVNet1'
```

Result:

```powershell
Error          :
HttpStatusCode : OK
Id             : f1600632-c819-4b2f-ac0e-f4126bec1ff8
Status         : Successful
RequestId      : 9ca273de2c4d01e986480ce1ffa4d6d9
StatusCode     : OK
```

## <a name="azure-cli"></a><a name="cli"></a>Azure CLI

Chcete-li bránu obnovit, použijte příkaz [resetovat bránu virtuální sítě AZ.](https://docs.microsoft.com/cli/azure/network/vnet-gateway) Následující příklad obnoví bránu virtuální sítě s názvem VNet5GW ve skupině prostředků TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Result:

Když obdržíte výsledek vrácení, můžete předpokládat, že obnovení brány bylo úspěšné. Však není nic v návratvýsledek, který označuje explicitně, že obnovení bylo úspěšné. Pokud se chcete podrobně podívat na historii, abyste přesně zjistili, kdy došlo k obnovení brány, můžete tyto informace zobrazit na [webu Azure Portal](https://portal.azure.com). Na portálu přejděte na **"GatewayName" -> Resource Health**.

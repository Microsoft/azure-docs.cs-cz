---
title: Resetování brány VPN nebo připojení pro opětovné vytvoření tunelu IPsec
titleSuffix: Azure VPN Gateway
description: Resetujte připojení nebo bránu VPN, aby se znovu navázaly tunely IPsec.
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: adc2ffd63d73baaddce00324787df61061ea69dc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101726631"
---
# <a name="reset-a-vpn-gateway-or-a-connection"></a>Resetování brány VPN nebo připojení

Resetování služby Azure VPN Gateway nebo připojení brány je užitečné, pokud ztratíte připojení VPN mezi různými místy na jednom nebo více tunelech VPN typu Site-to-site. V této situaci vaše místní zařízení VPN fungují správně, ale nejsou schopná vytvořit tunelová propojení prostřednictvím protokolu IPsec s branami Azure VPN. Tento článek vám pomůže resetovat bránu VPN nebo připojení brány.

## <a name="what-happens-during-a-reset"></a>Co se stane při resetování

### <a name="gateway-reset"></a>Resetování brány

Brána sítě VPN se skládá ze dvou instancí virtuálních počítačů spuštěných v konfiguraci s aktivním pohotovostním režimem. Když resetujete bránu, restartuje bránu a pak na ni znovu použije konfigurace pro více míst. Brána si ponechá stejnou veřejnou IP adresu. To znamená, že nebudete muset aktualizovat konfiguraci směrovače VPN na novou veřejnou IP adresu brány Azure VPN.

Při vystavení příkazu k resetování brány se okamžitě spustí aktuální aktivní instance služby Azure VPN Gateway. V případě převzetí služeb při selhání z aktivní instance (restartování) bude v pohotovostním stavu k dispozici krátká mezera. Prodleva by neměla být delší než jedna minuta.

Pokud po prvním resetování nedojde k obnovení připojení, znovu vydejte stejný příkaz pro resetování druhé instance virtuálního počítače (nové aktivní brány). Pokud se vyžadují dvě restartování za sebou, bude doba, po kterou se obě instance virtuálního počítače (aktivní a pohotovostní) restartují, trochu delší. To způsobí delší dobu připojení VPN, maximálně 30 až 45 minut, než virtuální počítače dokončí restartování.

Pokud stále dochází k problémům s připojením mezi různými místy, otevřete prosím žádost o podporu z Azure Portal po dvou restartováních.

### <a name="connection-reset"></a>Resetování připojení

Když vyberete resetování připojení, brána se nerestartuje. Resetuje se a obnoví se jenom vybrané připojení.

## <a name="reset-a-connection"></a>Resetování připojení

Připojení můžete snadno obnovit pomocí Azure Portal.

1. Přejděte k **připojení** , které chcete obnovit. Prostředek připojení můžete najít buď tak, že ho vyhledáte ve **všech prostředcích**, nebo přejdete na **název brány-> připojení-> název připojení.**
1. Na stránce **připojení** vyberte v nabídce vlevo možnost **resetovat** .
1. Na stránce **reset** obnovte připojení kliknutím na **obnovit** .

   :::image type="content" source="./media/reset-gateway/reset-connection.png" alt-text="Snímek obrazovky s resetováním":::

## <a name="reset-a-vpn-gateway"></a>Resetování brány VPN

Před restartováním brány ověřte pro každé tunelové propojení VPN typu Site-to-Site (S2S) s protokolem IPsec klíčové body následujícího seznamu. Jakákoli neshoda v těchto bodech způsobí odpojení tunelových propojení VPN typu S2S. Ověření a opravování konfigurací místních a Azure VPN Gateway vám ušetříte z zbytečných restartování a přerušení pro ostatní funkční připojení v bráně.

Před resetováním brány ověřte následující položky:

* Internetové IP adresy (virtuální IP adresy) pro bránu Azure VPN a bránu místní VPN jsou správně nakonfigurovány v zásadách Azure VPN i v zásadách místní VPN.
* Předsdílený klíč musí být stejný v bráně Azure VPN i v bráně místní VPN.
* Pokud použijete určitou konfiguraci protokolu IPsec/IKE, jako například šifrování, algoritmy hash nebo metodu Perfect Forward Secrecy (PFS), ujistěte se, že je stejně nakonfigurovaná brána Azure VPN i brána místní sítě.

### <a name="azure-portal"></a><a name="portal"></a>Azure Portal

Správce prostředků VPN Gateway můžete resetovat pomocí Azure Portal. Pokud chcete resetovat klasickou bránu, přečtěte si postup PowerShellu pro [model nasazení Classic](#resetclassic).

[!INCLUDE [portal steps](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="powershell"></a><a name="ps"></a>Prostředí

#### <a name="resource-manager-deployment-model"></a>Model nasazení Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Rutina pro **resetování brány je resetována – AzVirtualNetworkGateway**. Před provedením resetu se ujistěte, že máte nejnovější verzi [PowerShellu AZ rutins](/powershell/module/az.network). Následující příklad obnoví bránu virtuální sítě s názvem VNet1GW ve skupině prostředků TestRG1:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gw
```

Result:

Když obdržíte výsledek vrácení, můžete předpokládat, že resetování brány bylo úspěšné. Nejedná se však o výsledek návratového výsledku, který indikuje, že bylo resetování úspěšné. Pokud se chcete podívat na historii, abyste viděli přesně, kdy došlo k obnovení brány, můžete tyto informace zobrazit v [Azure Portal](https://portal.azure.com). Na portálu přejděte na **"Gateway"-> Resource Health**.

#### <a name="classic-deployment-model"></a><a name="resetclassic"></a>Model nasazení Classic

Rutina pro **resetování brány je resetována – AzureVNetGateway**. Rutiny Azure PowerShell pro správu služeb musí být nainstalované místně na vašem počítači. Nemůžete použít Azure Cloud Shell. Před provedením resetu se ujistěte, že máte nejnovější verzi [rutin PowerShellu pro správu služeb (SM)](/powershell/azure/servicemanagement/install-azure-ps#azure-service-management-cmdlets). Při použití tohoto příkazu se ujistěte, že používáte úplný název virtuální sítě. Klasické virtuální sítě vytvořené pomocí portálu mají dlouhý název, který se vyžaduje pro PowerShell. Dlouhý název můžete zobrazit pomocí příkazu Get-AzureVNetConfig-ExportToFile C:\Myfoldername\NetworkConfig.xml.

Následující příklad obnoví bránu pro virtuální síť s názvem "Group TestRG1 virtuální sítě testvnet1" (která na portálu představuje jednoduše "virtuální sítě testvnet1"):

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

### <a name="azure-cli"></a><a name="cli"></a>Rozhraní příkazového řádku Azure

Bránu resetujete tak, že použijete příkaz [AZ Network VNet-Gateway Reset](/cli/azure/network/vnet-gateway) . Následující příklad obnoví bránu virtuální sítě s názvem VNet5GW ve skupině prostředků TestRG5:

```azurecli
az network vnet-gateway reset -n VNet5GW -g TestRG5
```

Result:

Když obdržíte výsledek vrácení, můžete předpokládat, že resetování brány bylo úspěšné. Nejedná se však o výsledek návratového výsledku, který indikuje, že bylo resetování úspěšné. Pokud se chcete podívat na historii, abyste viděli přesně, kdy došlo k obnovení brány, můžete tyto informace zobrazit v [Azure Portal](https://portal.azure.com). Na portálu přejděte na **"Gateway"-> Resource Health**.

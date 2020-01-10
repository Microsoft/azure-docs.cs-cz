---
title: 'Azure VPN Gateway: ověření připojení brány'
description: V tomto článku se dozvíte, jak ověřit připojení k virtuální síti VPN Gateway.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 40417b078577424b1ba13ce60f4f773a1c8fd4dc
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75780143"
---
# <a name="verify-a-vpn-gateway-connection"></a>Ověření VPN Gatewayho připojení

V tomto článku se dozvíte, jak ověřit připojení brány VPN pro model nasazení Classic i Správce prostředků.

## <a name="azure-portal"></a>Portál Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Pokud chcete ověřit připojení brány VPN pro model nasazení Správce prostředků pomocí PowerShellu, nainstalujte nejnovější verzi [rutin Azure Resource Manager PowerShellu](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Pokud chcete ověřit připojení brány VPN pro model nasazení Správce prostředků pomocí rozhraní příkazového řádku Azure, nainstalujte nejnovější verzi [příkazů rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2,0 nebo novější).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Azure Portal (klasický)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (Classic)

Pokud chcete ověřit připojení brány VPN pro model nasazení Classic pomocí PowerShellu, nainstalujte nejnovější verze rutin Azure PowerShell. Nezapomeňte si stáhnout a nainstalovat modul [Service Management](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) . Pomocí příkazu Add-AzureAccount se přihlaste k modelu nasazení Classic.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Další kroky

* Do virtuálních sítí můžete přidat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

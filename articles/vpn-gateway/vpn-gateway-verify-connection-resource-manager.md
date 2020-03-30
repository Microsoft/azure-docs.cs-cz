---
title: 'Brána Azure VPN: Ověření připojení brány'
description: Tento článek ukazuje, jak ověřit připojení brány VPN virtuální sítě.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: 40417b078577424b1ba13ce60f4f773a1c8fd4dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780143"
---
# <a name="verify-a-vpn-gateway-connection"></a>Ověření připojení brány VPN

Tento článek ukazuje, jak ověřit připojení brány VPN pro klasické modely nasazení i pro správce prostředků.

## <a name="azure-portal"></a>portál Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Chcete-li ověřit připojení brány VPN pro model nasazení Správce prostředků pomocí prostředí PowerShell, nainstalujte nejnovější verzi [rutin prostředí PowerShell azure správce prostředků](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Chcete-li ověřit připojení brány VPN pro model nasazení Správce prostředků pomocí rozhraní příkazového příkazu Azure, nainstalujte nejnovější verzi [příkazů rozhraní příkazového příkazu](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 nebo novější).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Azure Portal (Classic)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (Classic)

Chcete-li ověřit připojení brány VPN pro klasický model nasazení pomocí prostředí PowerShell, nainstalujte nejnovější verze rutin Azure PowerShell. Nezapomeňte stáhnout a nainstalovat modul [Správa služeb.](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) Pomocí doplňku AzureAccount se přihlaste ke klasickému modelu nasazení.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Další kroky

* Do virtuálních sítí můžete přidat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

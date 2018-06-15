---
title: Ověření připojení VPN Gateway | Microsoft Docs
description: Tento článek ukazuje, jak ověřit připojení VPN brány virtuální sítě.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 7e3d1043-caa9-4472-96d3-832f4e2c91ee
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2017
ms.author: cherylmc
ms.openlocfilehash: b2d702ecdd5e1fca342e7c84c6e75339097f0bcd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884129"
---
# <a name="verify-a-vpn-gateway-connection"></a>Ověření připojení brány sítě VPN

Tento článek ukazuje, jak ověřit připojení k bráně VPN pro classic a modelech nasazení Resource Manager.

## <a name="azure-portal"></a>portál Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Chcete-li ověřit připojení k bráně VPN pro model nasazení Resource Manager pomocí prostředí PowerShell, nainstalujte nejnovější verzi [rutiny Powershellu pro Azure Resource Manager](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Chcete-li ověřit připojení k bráně VPN pro model nasazení Resource Manager pomocí rozhraní příkazového řádku Azure, nainstalujte nejnovější verzi [rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 nebo novější).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Portál Azure (klasický)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (Classic)

Chcete-li ověřit připojení brány VPN pro model nasazení classic pomocí prostředí PowerShell, nainstalujte nejnovější verzi rutin prostředí Azure PowerShell. Nezapomeňte si stáhněte a nainstalujte [Service Management](https://docs.microsoft.com/powershell/azure/install-azure-ps?view=azuresmps-3.7.0) modulu. 'Add-AzureAccount, použijte k přihlášení do modelu nasazení classic.

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Další kroky

* Do virtuálních sítí můžete přidat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
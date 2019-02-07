---
title: Ověření připojení brány sítě VPN | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak ověřit připojení VPN brány virtuální sítě.
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
ms.openlocfilehash: bcece64a85a69c705b0f7f915dafe29123a2cd4a
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765751"
---
# <a name="verify-a-vpn-gateway-connection"></a>Ověření připojení brány VPN

Tento článek popisuje, jak k ověření připojení brány VPN classic a modely nasazení Resource Manager.

## <a name="azure-portal"></a>portál Azure

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="powershell"></a>PowerShell

Pokud chcete ověřit připojení k bráně VPN pro model nasazení Resource Manageru pomocí prostředí PowerShell, nainstalujte nejnovější verzi [rutin Powershellu pro Azure Resource Manageru](/powershell/azure/overview).

[!INCLUDE [PowerShell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="azure-cli"></a>Azure CLI

Pokud chcete ověřit připojení k bráně VPN pro model nasazení Resource Manageru pomocí rozhraní příkazového řádku Azure, nainstalujte nejnovější verzi [příkazy rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0 nebo novější).

[!INCLUDE [CLI](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]


## <a name="azure-portal-classic"></a>Azure portal (classic)

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

## <a name="powershell-classic"></a>PowerShell (Classic)

K ověření připojení brány sítě VPN pro model nasazení classic pomocí prostředí PowerShell, nainstalujte nejnovější verzi rutin Azure Powershellu. Je potřeba stáhnout a nainstalovat [Service Management](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0#azure-service-management-cmdlets) modulu. Přihlaste se k modelu nasazení classic pomocí "Add-AzureAccount".

[!INCLUDE [Classic PowerShell](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

## <a name="next-steps"></a>Další postup

* Do virtuálních sítí můžete přidat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

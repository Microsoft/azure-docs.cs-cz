---
title: Nastavení koncových bodů na klasickém virtuálním počítači s Windows | Dokumentace Microsoftu
description: Zjistěte, jak nastavit koncové body pro klasické virtuální počítače s Windows na webu Azure Portal umožňující navázat komunikaci s virtuálním počítačem Windows v Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: cca9adb40557cf7bf9e1d4129fc6bd61cbf0df4f
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38618235"
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Jak nastavit koncové body na klasickém virtuálním počítači Windows v Azure
Všechny Windows, které virtuální počítače, které vytvoříte v Azure s využitím modelu nasazení classic můžete automaticky komunikují přes privátní síťový kanál s jinými virtuálními počítači ve stejné cloudové službě nebo virtuální síti. Počítače v Internetu nebo jiným virtuálním sítím ale vyžadují koncové body pro přesměrování příchozí síťový provoz do virtuálního počítače. Tento článek je také k dispozici pro [virtuální počítače s Linuxem](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

V **Resource Manageru** model nasazení, koncové body se konfigurují pomocí **skupiny zabezpečení sítě (Nsg)**. Další informace najdete v tématu [povolení externího přístupu k vašemu virtuálnímu počítači pomocí webu Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Když vytvoříte virtuální počítač s Windows na webu Azure Portal, běžné koncové body, například pro vzdálenou plochu a vzdálenou komunikaci Windows Powershellu se obvykle vytvářejí za vás automaticky. Další koncové body můžete nakonfigurovat při vytváření virtuálního počítače nebo později podle potřeby.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Další postup
* Používání rutiny prostředí Azure PowerShell k nastavení koncového bodu virtuálního počítače, naleznete v tématu [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Použití rutiny Azure Powershellu ke správě seznamu ACL v koncovém bodě, naleznete v tématu [seznamy Správa řízení přístupu (ACL) pro koncové body pomocí prostředí PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Pokud jste vytvořili virtuální počítač v modelu nasazení Resource Manager, můžete použít prostředí Azure PowerShell potřeba [vytvoření skupin zabezpečení sítě](../../../virtual-network/tutorial-filter-network-traffic.md) řídit provoz směřující do virtuálního počítače.

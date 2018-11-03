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
ms.date: 10/23/2018
ms.author: cynthn
ms.openlocfilehash: 373003eb8be0482ed96d7d11ecd879237f69b47a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957161"
---
# <a name="set-up-endpoints-on-a-windows-virtual-machine-by-using-the-classic-deployment-model"></a>Nastavení koncových bodů na virtuálním počítači s Windows pomocí modelu nasazení classic
Windows virtuálních počítačů (VM), které vytvoříte v Azure s použitím modelu nasazení classic můžou automaticky komunikovat prostřednictvím kanálu privátní sítě s jiným virtuálním počítačům ve stejné cloudové službě nebo virtuální síti. Počítače v Internetu nebo jiným virtuálním sítím ale vyžadují koncové body pro přesměrování příchozí síťový provoz do virtuálního počítače. 

Koncové body můžete také nastavit na [virtuální počítače s Linuxem](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure nabízí dva různé modely nasazení pro vytváření a práci s prostředky: [nástroj Resource Manager a klasický režim](../../../resource-manager-deployment-model.md). Tento článek se týká modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.  
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

V **Resource Manageru** model nasazení, koncové body se konfigurují pomocí **skupiny zabezpečení sítě (Nsg)**. Další informace najdete v tématu [povolení externího přístupu k vašemu virtuálnímu počítači pomocí webu Azure portal](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Při vytváření virtuálního počítače s Windows na webu Azure Portal, běžné koncové body, jako jsou koncové body pro vzdálenou plochu a vzdálenou komunikaci Windows Powershellu, jsou obvykle vytvoří za vás automaticky. Další koncové body můžete nakonfigurovat později podle potřeby.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Další postup
* Používání rutiny prostředí Azure PowerShell k nastavení koncového bodu virtuálního počítače, naleznete v tématu [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Použití rutiny Azure Powershellu ke správě seznamu ACL v koncovém bodě, naleznete v tématu [seznamy Správa řízení přístupu (ACL) pro koncové body pomocí prostředí PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Pokud jste vytvořili virtuální počítač v modelu nasazení Resource Manager, můžete použít prostředí Azure PowerShell potřeba [vytvoření skupin zabezpečení sítě](../../../virtual-network/tutorial-filter-network-traffic.md) řídit provoz směřující do virtuálního počítače.

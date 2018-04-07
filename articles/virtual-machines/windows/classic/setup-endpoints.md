---
title: Nastavení koncových bodů na klasické virtuální počítač s Windows | Microsoft Docs
description: Naučte se nastavit koncové body pro klasické virtuální počítač s Windows v portálu Azure a povolit komunikaci s virtuálního počítače s Windows v Azure.
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
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="how-to-set-up-endpoints-on-a-classic-windows-virtual-machine-in-azure"></a>Jak nastavit koncové body na klasické virtuální počítač Windows v Azure
Všechny systémy Windows, který můžou virtuální počítače, které vytvoříte v Azure pomocí modelu nasazení classic automaticky komunikují přes privátního síťový kanál s jinými virtuálními počítači ve stejné cloudové služby nebo virtuální sítě. Počítače v Internetu nebo k jiným virtuálním sítím však vyžadují koncové body směrovat příchozí síťový provoz do virtuálního počítače. Tento článek je také k dispozici pro [virtuální počítače s Linuxem](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

V **Resource Manager** modelu nasazení koncové body jsou konfigurováni pomocí **skupiny zabezpečení sítě (Nsg)**. Další informace najdete v tématu [povolit externí přístup k virtuálnímu počítači pomocí portálu Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Při vytváření virtuálního počítače s Windows v portálu Azure, běžné koncové body, jako jsou ty, které pro vzdálenou plochu a vzdálenou komunikaci Windows PowerShell jsou obvykle vám vytvoří automaticky. Při vytváření virtuálního počítače nebo později podle potřeby můžete nakonfigurovat další koncové body.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Další postup
* Nastavit koncový bod virtuálního počítače pomocí rutiny Azure Powershellu naleznete v části [přidat AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Používat rutiny Azure Powershellu ke správě ACL na koncový bod, najdete v části [seznamy Správa řízení přístupu (ACL) pro koncové body pomocí prostředí PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Pokud jste vytvořili virtuální počítač v modelu nasazení Resource Manager, můžete použít prostředí Azure PowerShell [vytvoření skupin zabezpečení sítě](../../../virtual-network/tutorial-filter-network-traffic.md) pro řízení přenosu do virtuálního počítače.

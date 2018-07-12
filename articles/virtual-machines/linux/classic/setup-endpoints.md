---
title: Nastavení koncových bodů na klasickém virtuálním počítači Linux | Dokumentace Microsoftu
description: Zjistěte, jak nastavit koncové body pro virtuální počítač s Linuxem na webu Azure Portal umožňující navázat komunikaci s virtuálním počítači s Linuxem v Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: f3749738-1109-4a1d-8635-40e4bd220e91
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: cynthn
ms.openlocfilehash: 03cb90dcdcc7a7407898ddd8cbc30f1af0833676
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38295683"
---
# <a name="how-to-set-up-endpoints-on-a-linux-classic-virtual-machine-in-azure"></a>Jak nastavit koncové body na klasický virtuální počítač s Linuxem v Azure
Všechny virtuální počítače s Linuxem, které vytvoříte v Azure s využitím modelu nasazení classic můžou automaticky komunikovat prostřednictvím kanálu privátní sítě s jinými virtuálními počítači ve stejné cloudové službě nebo virtuální síti. Počítače v Internetu nebo jiným virtuálním sítím ale vyžadují koncové body pro přesměrování příchozí síťový provoz do virtuálního počítače. Tento článek je také k dispozici pro [Windows virtual machines](../../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

V **Resource Manageru** model nasazení, koncové body se konfigurují pomocí **skupiny zabezpečení sítě (Nsg)**. Další informace najdete v tématu [otevření portů a koncových bodů](../nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Když vytvoříte virtuální počítač s Linuxem na webu Azure Portal, koncový bod pro Secure Shell (SSH) se obvykle vytvoří za vás automaticky. Další koncové body můžete nakonfigurovat při vytváření virtuálního počítače nebo později podle potřeby.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Další postup
* Můžete také vytvořit koncový bod virtuálního počítače pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2). Spustit **vytvoření koncového bodu virtuálního počítače azure** příkazu.
* Pokud jste vytvořili virtuální počítač v modelu nasazení Resource Manager, můžete použít rozhraní příkazového řádku Azure v režimu Resource Manageru k [vytvoření skupin zabezpečení sítě](../../../virtual-network/tutorial-filter-network-traffic-cli.md) řídit provoz směřující do virtuálního počítače.

---
title: Vytvoření klasického virtuálního počítače s Linuxem pomocí Azure CLI 1.0 | Microsoft Docs
description: Naučte se vytvořit virtuální počítač s Linuxem pomocí Azure CLI 1.0 pomocí modelu nasazení Classic
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: f8071a2e-ed91-4f77-87d9-519a37e5364f
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
ms.openlocfilehash: 6d3f0dd0c82ad32df4d6e17058d9b1bea57c301f
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Postup vytvoření klasické virtuální počítač s Linuxem pomocí rozhraní příkazového řádku Azure 1.0
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Resource Manager verze, najdete v části [zde](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Toto téma popisuje, jak vytvořit virtuální počítač s Linuxem (VM) s Azure CLI 1.0 pomocí modelu nasazení Classic. Můžeme použít bitovou kopii systému Linux z dostupných **bitové kopie** v Azure. Příkazy Azure CLI 1.0 poskytují následující možnosti konfigurace, mimo jiné:

* Připojení virtuálního počítače k virtuální síti.
* Přidání virtuálního počítače do existující cloudové služby
* Přidání virtuálního počítače do stávající účet úložiště
* Přidání virtuálního počítače do skupiny dostupnosti nebo umístění

> [!IMPORTANT]
> Pokud chcete virtuální počítač používat virtuální síť, takže k nim mohla připojit přímo podle názvu hostitele nebo nastavte připojení mezi různými místy, ujistěte se, že zadáte virtuální sítě, při vytváření virtuálního počítače. Virtuální počítač se dá nakonfigurovat pro připojení k virtuální síti, pouze když vytváříte virtuální počítač. Podrobnosti virtuální sítě najdete v tématu [Přehled virtuálních sítí Azure](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Postup vytvoření virtuálního počítače s Linuxem pomocí modelu nasazení Classic
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]


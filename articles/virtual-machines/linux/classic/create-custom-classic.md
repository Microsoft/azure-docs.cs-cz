---
title: Vytvoření klasického virtuálního počítače s Linuxem pomocí Azure classic CLI | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač s Linuxem pomocí Azure CLI classic pomocí modelu nasazení Classic
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: d8e469289f72fe892ea7c3da99972e6326c75eb9
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242532"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-classic-cli"></a>Vytvoření klasického virtuálního počítače s Linuxem pomocí Azure classic CLI
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Resource Manager, najdete v článku [tady](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Toto téma popisuje, jak vytvořit virtuální počítač s Linuxem (VM) pomocí Azure CLI classic pomocí modelu nasazení Classic. Používáme image systému Linux z dostupných **IMAGÍ** v Azure. Příkazy rozhraní příkazového řádku Azure classic nabízejí následující možnosti konfigurace, mimo jiné:

* Připojení virtuálního počítače k virtuální síti
* Přidání virtuálního počítače do existující cloudové služby
* Přidání virtuálního počítače do existující účet úložiště
* Přidání virtuálního počítače na skupinu dostupnosti nebo umístění

> [!IMPORTANT]
> Pokud chcete virtuální počítač používat virtuální síť, abyste se mohli připojit k němu přímo podle názvu hostitele a nastavení připojení mezi různými místy, ujistěte se, že virtuální síť určena při vytváření virtuálního počítače. Virtuální počítač je možné nakonfigurovat pro připojení virtuální sítě pouze při vytváření virtuálního počítače. Podrobnosti ve virtuálních sítích najdete v tématu [Přehled služby Azure Virtual Network](https://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Jak vytvořit virtuální počítač s Linuxem pomocí modelu nasazení Classic
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]


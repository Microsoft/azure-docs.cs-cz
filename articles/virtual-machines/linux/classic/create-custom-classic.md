---
title: Vytvoření klasického virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure CLI 1.0 | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač s Linuxem pomocí Azure CLI 1.0 pomocí modelu nasazení Classic
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
ms.openlocfilehash: 13d0ef93c3828c514e46e37494a66f7003eac827
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37931610"
---
# <a name="how-to-create-a-classic-linux-vm-with-the-azure-cli-10"></a>Vytvoření klasického virtuálního počítače s Linuxem pomocí Azure CLI 1.0
> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Resource Manager, najdete v článku [tady](../create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Toto téma popisuje, jak vytvořit virtuální počítač s Linuxem (VM) pomocí Azure CLI 1.0 pomocí modelu nasazení Classic. Používáme image systému Linux z dostupných **IMAGÍ** v Azure. Příkazy příkazového řádku Azure CLI 1.0 nabízejí následující možnosti konfigurace, mimo jiné:

* Připojení virtuálního počítače k virtuální síti
* Přidání virtuálního počítače do existující cloudové služby
* Přidání virtuálního počítače do existující účet úložiště
* Přidání virtuálního počítače na skupinu dostupnosti nebo umístění

> [!IMPORTANT]
> Pokud chcete virtuální počítač používat virtuální síť, abyste se mohli připojit k němu přímo podle názvu hostitele a nastavení připojení mezi různými místy, ujistěte se, že virtuální síť určena při vytváření virtuálního počítače. Virtuální počítač je možné nakonfigurovat pro připojení virtuální sítě pouze při vytváření virtuálního počítače. Podrobnosti ve virtuálních sítích najdete v tématu [Přehled služby Azure Virtual Network](http://go.microsoft.com/fwlink/p/?LinkID=294063).
> 
> 

## <a name="how-to-create-a-linux-vm-using-the-classic-deployment-model"></a>Jak vytvořit virtuální počítač s Linuxem pomocí modelu nasazení Classic
[!INCLUDE [virtual-machines-create-LinuxVM](../../../../includes/virtual-machines-create-linuxvm.md)]


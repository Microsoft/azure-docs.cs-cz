---
title: Vytvoření virtuálního počítače na webu Azure Portal | Dokumentace Microsoftu
description: Vytvoření virtuálního počítače Windows na webu Azure Portal.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ROBOTS: NOINDEX
ms.assetid: 1871f823-ebd7-4eff-9a22-8e2411555595
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/30/2017
ms.author: cynthn
ms.openlocfilehash: 5fd2128ff436d3211f41c7dfdcc4c2b8aabd0eb0
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38232322"
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Vytvoření virtuálního počítače s Windows na webu Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](tutorial.md)
> * [Powershellu: Klasické nasazení pomocí](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu nasazení Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pomocí **webu Azure portal**.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

V tomto kurzu se dozvíte, jak vytvořit virtuální počítač Azure (VM) s Windows na webu Azure Portal. Jako příklad použijeme image Windows serveru, ale to je jenom jeden z mnoha imagí, které Azure nabízí. Všimněte si, že vaše volba image závisí na vašem předplatném. Například může být desktopových imagí Windows dostupných pro předplatitele MSDN.

V této části se dozvíte, jak používat **řídicí panel** na webu Azure Portal vyberte a pak vytvořte virtuální počítač.

Můžete také vytvářet virtuální počítače pomocí [vlastních imagí](createupload-vhd.md). Další informace o tomto a dalších metodách, naleznete v tématu [různé způsoby vytvoření virtuálního počítače s Windows](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a id="createvirtualmachine"> </a>Vytvoření virtuálního počítače
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [vytvoření virtuálního počítače pomocí modelu nasazení Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) na webu Azure Portal.
* Přihlaste se k virtuálnímu počítači. Pokyny najdete v tématu [Přihlaste se k virtuálnímu počítači s Windows serverem](connect-logon.md).
* Připojení disku k uložení dat. Můžete připojit prázdné disky a disky, které obsahují data. Pokyny najdete v tématu [připojení datového disku k virtuálnímu počítači Windows vytvořeného pomocí modelu nasazení classic](attach-disk.md).

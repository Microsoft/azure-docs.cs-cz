---
title: Vytvoření virtuálního počítače na portálu Azure | Microsoft Docs
description: Vytvoření virtuálního počítače s Windows v portálu Azure.
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
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="create-a-virtual-machine-running-windows-in-the-azure-portal"></a>Vytvoření virtuálního počítače se systémem Windows na portálu Azure
> [!div class="op_single_selector"]
> * [Azure Portal](tutorial.md)
> * [PowerShell: Nasazení Classic](create-powershell.md)
>
>

<br>

> [!IMPORTANT]
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a klasický](../../../resource-manager-deployment-model.md). Tento článek se zabývá pomocí modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [proveďte tyto kroky, pomocí modelu nasazení Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) pomocí **portál Azure**.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

V tomto kurzu se dozvíte, jak vytvořit virtuální počítač Azure (VM) s Windows v portálu Azure. Jako příklad použijeme bitovou kopii systému Windows Server, ale který je jen jednou z mnoha imagí, které Azure nabízí. Všimněte si, že tato volba image závisí na vaše předplatné. Například může být plochy bitových kopií systému Windows k dispozici předplatitelům služby MSDN.

V této části se dozvíte, jak používat **řídicí panel** na portálu Azure vyberte a poté vytvořit virtuální počítač.

Můžete také vytvořit virtuální počítače pomocí [vlastní image](createupload-vhd.md). Informace o tomto a dalších metod najdete v tématu [různé způsoby vytvoření virtuálního počítače s Windows](../../virtual-machines-windows-creation-choices.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a id="createvirtualmachine"> </a>Vytvoření virtuálního počítače
[!INCLUDE [virtual-machines-create-WindowsVM](../../../../includes/virtual-machines-create-windowsvm.md)]

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [vytvoření virtuálního počítače pomocí modelu nasazení Resource Manager](../../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) na portálu Azure.
* Přihlaste se k virtuálnímu počítači. Pokyny najdete v tématu [Přihlaste se k virtuálního počítače se systémem Windows Server](connect-logon.md).
* Připojte disk k uložení dat. Můžete připojit prázdné disky a disky, které obsahují data. Pokyny najdete v tématu [připojit datový disk k virtuálnímu počítači Windows vytvořené pomocí modelu nasazení classic](attach-disk.md).

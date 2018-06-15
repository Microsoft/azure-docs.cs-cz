---
title: Odpojit datový disk z virtuálního počítače s Linuxem – Azure | Microsoft Docs
description: Naučte se odpojit datový disk z virtuálního počítače v Azure pomocí rozhraní příkazového řádku 2.0 nebo portálu Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 572fe5bd4d6d79bb9dd94353732e273282e2a0af
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
ms.locfileid: "30903675"
---
# <a name="how-to-detach-a-data-disk-from-a-linux-virtual-machine"></a>Postup odpojit datový disk z virtuálního počítače systému Linux

Když už nepotřebujete datový disk připojený k virtuálnímu počítači, můžete ho jednoduše odpojit. Odebere disk z virtuálního počítače, ale neodstraní z úložiště. 

> [!WARNING]
> Pokud se odpojit disk není automaticky odstraněn. Pokud jste přihlášení k odběru služby storage úrovně Premium, můžete nadále toho vám být účtovány poplatky za úložiště pro disk. Další informace najdete v části [ceny a fakturace při použití služby Premium Storage](../windows/premium-storage.md#pricing-and-billing). 
> 
> 

Pokud znovu chcete použít stávající data na disku, můžete ho znovu připojit ke stejnému nebo jinému virtuálnímu počítači.  

## <a name="detach-a-data-disk-using-cli-20"></a>Odpojit datový disk pomocí rozhraní příkazového řádku 2.0

```azurecli
az vm disk detach \
    -g myResourceGroup \
    --vm-name myVm \
    -n myDataDisk
```

Disk zůstává v úložišti, ale už není připojený k virtuálnímu počítači.


## <a name="detach-a-data-disk-using-the-portal"></a>Odpojení datového disku pomocí portálu
1. V nabídce vlevo vyberte **virtuální počítače**.
2. Vyberte virtuální počítač, který má datový disk, kterou chcete odpojit a klikněte na tlačítko **Zastavit** se zrušit přidělení virtuálního počítače.
3. V podokně virtuální počítač vyberte **disky**.
4. V horní části **disky** podokně, vyberte **upravit**.
5. V **disky** podokně na pravé straně datový disk, který chcete odpojit, klikněte na tlačítko ![obrázek tlačítka odpojení](./media/detach-disk/detach.png) odpojit tlačítko.
5. Po odebrání disku nahoře v podokně klikněte na tlačítko Uložit.
6. V podokně virtuální počítač, klikněte na tlačítko **přehled** a pak klikněte na tlačítko **spustit** tlačítka v horní části podokna restartování virtuálního počítače.

Disk zůstává v úložišti, ale už není připojený k virtuálnímu počítači.


## <a name="next-steps"></a>Další postup
Pokud chcete použít datový disk, jste právě [připojte ji k jiným virtuálním Počítačem](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


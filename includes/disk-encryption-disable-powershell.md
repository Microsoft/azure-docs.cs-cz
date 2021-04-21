---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/19/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 82318a94a6a095016fe1177ee486f035d101589c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776777"
---
Šifrování můžete zakázat pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo pomocí Správce prostředků šablony. Zakázání šifrování datových disků u virtuálních počítačů Windows, když byly zašifrovány disky s operačním systémem a datové disky, nefungujte podle očekávání. Místo toho zakažte šifrování na všech discích.

- **Zakázat šifrování disku pomocí Azure PowerShell:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Zakázat šifrování pomocí Azure CLI:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az_vm_encryption_disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Zakázat šifrování pomocí šablony Správce prostředků:** 

    1. Kliknutím na **nasadit do Azure** z části [zakázat šifrování disku při spuštění šablony virtuálního počítače s Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad) .
    2. Vyberte předplatné, skupinu prostředků, umístění, virtuální počítač, typ svazku, právní výrazy a smlouvu.
    3.  Kliknutím na **koupit** zakažte šifrování disku na běžícím virtuálním počítači s Windows. 

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
ms.openlocfilehash: ed2b13077ff5809899097254dde0fa8406e1db3b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85601376"
---
Šifrování můžete zakázat pomocí Azure PowerShell, rozhraní příkazového řádku Azure nebo pomocí Správce prostředků šablony. 

>[!IMPORTANT]
>Zakázání šifrování pomocí Azure Disk Encryption na virtuálních počítačích se systémem Linux je podporováno pouze pro datové svazky. Pokud je svazek s operačním systémem zašifrovaný, není podpora na svazcích dat nebo operačních systémů podporována.  

- **Zakázat šifrování disku pomocí Azure PowerShell:** Pokud chcete šifrování zakázat, použijte rutinu [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) . 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [-VolumeType DATA]
     ```

- **Zakázat šifrování pomocí Azure CLI:** Pokud chcete šifrování zakázat, použijte příkaz [AZ VM Encryption Disable](/cli/azure/vm/encryption#az-vm-encryption-disable) . 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```
- **Zakázat šifrování pomocí šablony Správce prostředků:** Zakáže šifrování pomocí [zákazu šifrování na spuštěné šabloně virtuálního počítače se systémem Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-linux-vm-without-aad) .
     1. Klikněte na **Nasadit do Azure**.
     2. Vyberte předplatné, skupinu prostředků, umístění, virtuální počítač, právní podmínku a smlouvu.

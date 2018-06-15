---
title: Azure ukázkový skript prostředí PowerShell - odstranění kontejnerů podle předpony | Microsoft Docs
description: Odstranění kontejnerů objektů blob Azure Storage na základě předpony názvu kontejneru.
services: storage
documentationcenter: na
author: tamram
manager: timlt
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.openlocfilehash: 629189b9dbe2327763d364abc95f49539a312c53
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/29/2017
ms.locfileid: "25983894"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Odstranění kontejnerů na základě předpony názvu kontejneru

Tento skript odstraní kontejnerů v úložišti objektů Blob v Azure na základě předpony v názvu kontejneru.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Pomocí následujícího příkazu odeberte skupinu zdrojů, zbývající kontejnery, a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy k odstranění kontejnerů na základě předpony názvu kontejneru. Každou položku v tabulce odkazy na dokumentaci specifické pro příkaz.

| Příkaz | Poznámky |
|---|---|
| [Get AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Získá zadaný účet úložiště nebo všechny účty úložiště ve skupině prostředků nebo předplatného. |
| [Get-AzureStorageContainer](/powershell/module/azure.storage/get-azurestoragecontainer) | Uvádí kontejnery úložiště přidružené k účtu úložiště. |
| [Odebrat AzureStorageContainer](/powershell/module/azure.storage/remove-azurestoragecontainer) | Odebere zadané úložiště kontejneru. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/overview).

Ukázky skriptu PowerShell další úložiště naleznete v [ukázky pro úložiště objektů Blob v Azure PowerShell](../blobs/storage-samples-blobs-powershell.md).

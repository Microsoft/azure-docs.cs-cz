---
title: Ukázkový skript Azure Powershellu – přístupový klíč účtu úložiště otočit | Dokumentace Microsoftu
description: Vytvoření účtu služby Azure Storage a načtení a obměně jeden z jeho přístupových klíčů účtu.
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
ms.openlocfilehash: 0aae733f94ab4a217ca41ca3de6df2e5da47df50
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244347"
---
# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Vytvoření účtu úložiště a obměna přístupových klíčů tohoto účtu

Tento skript vytvoří účet služby Azure Storage, zobrazuje nový účet úložiště primární přístupový klíč a pak klíče obnoví (obmění) klíč.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spuštěním následujícího příkazu odeberte skupinu prostředků, účet úložiště a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k vytvoření účtu úložiště a načtení a obměně jeden z jeho přístupových klíčů používá následující příkazy. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Získá všechny umístění a podporovaných poskytovatelích prostředků pro každé umístění. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků Azure. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Vytvoří účet úložiště. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Získá seznam přístupových klíčů pro účet služby Azure Storage. |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Obnovuje se přístupový klíč pro účet služby Azure Storage. |

## <a name="next-steps"></a>Další postup

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázkové skripty PowerShellu pro úložiště najdete v tématu [Ukázky PowerShellu pro úložiště objektů blob v Azure](../blobs/storage-samples-blobs-powershell.md).

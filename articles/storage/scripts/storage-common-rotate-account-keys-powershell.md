---
title: Otočení přístupových klíčů k účtu úložiště pomocí PowerShellu
titleSuffix: Azure Storage
description: Vytvořte účet Azure Storage a pak načtěte a otáčejte jeden z jeho přístupových klíčů k účtu.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7d8585b5d05012ab2aff2580d41fecf6423b509c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89070424"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>Otočení přístupových klíčů k účtu úložiště pomocí PowerShellu

Tento skript vytvoří účet Azure Storage, zobrazí primární přístupový klíč nového účtu úložiště a pak ho znovu nanews (otočí).

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

Tento skript pomocí následujících příkazů vytvoří účet úložiště a načte a natočí jeden z jeho přístupových klíčů. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Načte všechna umístění a podporované poskytovatele prostředků pro každé umístění. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Vytvoří skupinu prostředků Azure. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Vytvoří účet úložiště. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Získá seznam přístupových klíčů pro účet služby Azure Storage. |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Znovu vygeneruje přístupový klíč pro účet Azure Storage. |

## <a name="next-steps"></a>Další kroky

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázkové skripty PowerShellu pro úložiště najdete v tématu [Ukázky PowerShellu pro úložiště objektů blob v Azure](../blobs/storage-samples-blobs-powershell.md).

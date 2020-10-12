---
title: Výpočet velikosti kontejneru objektů BLOB pomocí prostředí PowerShell
titleSuffix: Azure Storage
description: Vypočítejte velikost kontejneru v úložišti objektů BLOB v Azure tím, že se naplní celková velikost všech objektů BLOB.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: de275bcca1644750532809b35ae85d954d3cac6d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89076901"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Výpočet velikosti kontejneru objektů BLOB pomocí prostředí PowerShell

Tento skript vypočítá velikost kontejneru v úložišti objektů blob v Azure součtem velikostí jednotlivých objektů blob v kontejneru.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Tento skript PowerShellu poskytuje odhadovanou velikost kontejneru a neměl by se používat k výpočtům fakturace. Skript, který počítá velikost kontejneru pro účely fakturace, najdete v tématu [Výpočet velikosti kontejneru úložiště objektů BLOB pro účely fakturace](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Spuštěním následujícího příkazu odeberte skupinu prostředků, kontejner a všechny související prostředky.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript k výpočtu velikosti kontejneru úložiště objektů blob používá následující příkazy. Každá položka v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Načte zadaný účet úložiště nebo všechny účty úložiště v rámci skupiny prostředků nebo předplatného. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Zobrazí seznam objektů BLOB v kontejneru. |

## <a name="next-steps"></a>Další kroky

Skript, který počítá velikost kontejneru pro účely fakturace, najdete v tématu [Výpočet velikosti kontejneru úložiště objektů BLOB pro účely fakturace](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/).

Další ukázkové skripty PowerShellu pro úložiště najdete v [ukázkách PowerShellu pro Azure Storage](../blobs/storage-samples-blobs-powershell.md).

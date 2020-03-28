---
title: Výpočet velikosti kontejneru objektů blob pomocí Prostředí PowerShell
titleSuffix: Azure Storage
description: Vypočítejte velikost kontejneru v úložišti objektů Blob Azure tak, že sesečtevelikost každého z jeho objektů BLOB.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: de51ed7d91ba1102f5a9cd376ab95f49dd54d9f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80067073"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Výpočet velikosti kontejneru objektů blob pomocí prostředí PowerShell

Tento skript vypočítá velikost kontejneru v úložišti objektů blob v Azure součtem velikostí jednotlivých objektů blob v kontejneru.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Tento skript prostředí PowerShell poskytuje odhadovanou velikost kontejneru a neměl by se používat pro výpočty fakturace. Skript, který vypočítá velikost kontejneru pro účely fakturace, [najdete v tématu Výpočet velikosti kontejneru úložiště objektů Blob pro účely fakturace](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

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
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Získá zadaný účet úložiště nebo všechny účty úložiště ve skupině prostředků nebo předplatné. |
| [Získat-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Zobrazí seznam objektů BLOB v kontejneru. |

## <a name="next-steps"></a>Další kroky

Skript, který vypočítá velikost kontejneru pro účely fakturace, [najdete v tématu Výpočet velikosti kontejneru úložiště objektů Blob pro účely fakturace](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Další ukázky skriptů PowerShellu úložiště najdete ve [vzorcích PowerShellu pro Azure Storage](../blobs/storage-samples-blobs-powershell.md).

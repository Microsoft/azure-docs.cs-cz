---
title: Skript Azure Powershellu ukázkový – výpočet velikosti kontejneru objektů blob | Dokumentace Microsoftu
description: Vypočítá velikost kontejneru v úložišti objektů Blob v Azure součtem velikostí jednotlivých jeho objekty BLOB.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: ''
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: 03a0f8885d51e6d45fabda86714afdeefc01f992
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2019
ms.locfileid: "54213480"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Výpočet velikosti kontejneru úložiště objektů blob

Tento skript vypočítá velikost kontejneru v úložišti objektů blob v Azure součtem velikostí jednotlivých objektů blob v kontejneru.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Tento skript Powershellu, poskytuje odhadovanou velikost kontejneru a neměl by se používat k výpočtům fakturace. Skript, který vypočítá velikost kontejneru pro účely fakturace, najdete v článku [výpočet velikosti kontejneru úložiště objektů Blob pro účely fakturace](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

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
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Získá zadaný účet úložiště nebo všechny účty úložišť ve skupině prostředků nebo předplatného. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Vypíše objekty BLOB v kontejneru. ||

## <a name="next-steps"></a>Další postup

Skript, který vypočítá velikost kontejneru pro účely fakturace, najdete v článku [výpočet velikosti kontejneru úložiště objektů Blob pro účely fakturace](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Další informace o modulu Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](/powershell/azure/overview).

Ukázkové skripty Powershellu pro úložiště najdete v [ukázky Powershellu pro Azure Storage](../blobs/storage-samples-blobs-powershell.md).

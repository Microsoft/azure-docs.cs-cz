---
title: Azure skript prostředí PowerShell ukázkový – vypočítat velikost kontejneru objektů blob | Microsoft Docs
description: Vypočítejte velikost kontejneru v úložiště objektů Blob v Azure pomocí součtem velikost každého z jeho objekty BLOB.
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
ms.openlocfilehash: f6f421e780bfbb7922a4b11f758330f2a9a0b84b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2017
ms.locfileid: "24814571"
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Vypočítat velikost kontejner úložiště objektů Blob

Tento skript se vypočítá velikost kontejner v úložišti objektů Blob Azure součtem velikost objektů BLOB v kontejneru.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Tento skript prostředí PowerShell poskytuje odhadovanou velikost kontejneru a by se neměla používat pro výpočty fakturace. Skript, který vypočítá velikost kontejneru pro účely fakturace, najdete v části [vypočítat velikost kontejner úložiště objektů Blob pro účely fakturace](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení 

Spusťte následující příkaz pro odebrání skupiny prostředků, kontejneru a všechny související prostředky.

```powershell
Remove-AzureRmResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy vypočítat velikost kontejner úložiště objektů Blob. Každou položku v tabulce odkazy na dokumentaci specifické pro příkaz.

| Příkaz | Poznámky |
|---|---|
| [Get AzureRmStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Získá zadaný účet úložiště nebo všechny účty úložiště ve skupině prostředků nebo předplatného. |
| [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) | Zobrazí seznam objektů BLOB v kontejneru. ||

## <a name="next-steps"></a>Další kroky

Skript, který vypočítá velikost kontejneru pro účely fakturace, najdete v části [vypočítat velikost kontejner úložiště objektů Blob pro účely fakturace](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Další informace o modulu Azure PowerShell najdete v tématu [dokumentace Azure PowerShell](/powershell/azure/overview).

Ukázky skriptu PowerShell další úložiště naleznete v [ukázky prostředí PowerShell pro Azure Storage](../blobs/storage-samples-blobs-powershell.md).

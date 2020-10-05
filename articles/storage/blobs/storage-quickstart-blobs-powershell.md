---
title: Rychlý Start – vytvoření objektu BLOB s využitím PowerShellu
titleSuffix: Azure Storage
description: V tomto rychlém startu použijete v úložišti objektů (blob) Azure PowerShell. Pak použijete PowerShell k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.date: 03/31/2020
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b051a4ffe4d24e1ef0e69ab7c18a8ed3388b57e5
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "89078278"
---
# <a name="quickstart-upload-download-and-list-blobs-with-powershell"></a>Rychlý Start: nahrání, stažení a výpis objektů BLOB pomocí PowerShellu

Pomocí modulu Azure PowerShell můžete vytvářet a spravovat prostředky Azure. Prostředky Azure je možné vytvářet nebo spravovat z příkazového řádku PowerShellu nebo ve skriptech. Tato příručka popisuje použití PowerShellu k přenosu souborů mezi místním diskem a úložištěm objektů blob v Azure.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>Předpoklady

Pokud chcete získat přístup k Azure Storage, budete potřebovat předplatné Azure. Pokud ještě nemáte předplatné, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Tento rychlý Start vyžaduje modul Azure PowerShell AZ verze 0,7 nebo novější. Verzi zjistíte spuštěním příkazu `Get-InstalledModule -Name Az -AllVersions | select Name,Version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Objekty blob se vždy nahrávají do kontejneru. Skupiny objektů blob můžete organizovat podobně, jako organizujete soubory do složek na svém počítači.

Nastavte název kontejneru a pak vytvořte kontejner pomocí [New-AzStorageContainer](/powershell/module/az.storage/new-azstoragecontainer). Nastavte oprávnění na `blob`, abyste umožnili veřejný přístup k souborům. Název kontejneru v tomto příkladu je *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Soubory VHD využívané virtuálními počítači IaaS jsou objekty blob stránky. Doplňovací objekty blob můžete použít k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Většina souborů uložených v úložišti objektů blob je objekty blob bloku. 

Pokud chcete nahrát soubor do objektu blob bloku, získejte odkaz na kontejner a pak získejte odkaz na objekt blob bloku v tomto kontejneru. Jakmile budete mít odkaz na objekt blob, můžete do něj nahrát data pomocí [set-AzStorageBlobContent](/powershell/module/az.storage/set-azstorageblobcontent). Tato operace vytvoří objekt blob, pokud ještě neexistuje, nebo objekt blob přepíše, pokud už existuje.

Následující příklady nahrají soubory *Image001.jpg* a *Image002.png* ze složky *D:\\_TestImages* na místním disku do kontejneru, který jste vytvořili.

```powershell
# upload a file to the default account (inferred) access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image000.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload a file to the Hot access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 
  -StandardBlobTier Hot

# upload another file to the Cool access tier
Set-AzStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
  -StandardBlobTier Cool

# upload a file to a folder to the Archive access tier
Set-AzStorageBlobContent -File "D:\_TestImages\foldername\Image003.jpg" `
  -Container $containerName `
  -Blob "Foldername/Image003.jpg" `
  -Context $ctx 
  -StandardBlobTier Archive
```

Než budete pokračovat, můžete nahrát libovolné množství souborů.

## <a name="list-the-blobs-in-a-container"></a>Seznam objektů blob v kontejneru

Získat seznam objektů BLOB v kontejneru pomocí [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob). Tento příklad zobrazí pouze názvy nahraných objektů blob.

```powershell
Get-AzStorageBlob -Container $ContainerName -Context $ctx | select Name
```

## <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte objekty blob na svůj místní disk. Pro každý objekt blob, který chcete stáhnout, nastavte název a zavolejte [Get-AzStorageBlobContent](/powershell/module/az.storage/get-azstorageblobcontent) ke stažení objektu BLOB.

Tento příklad stáhne objekty blob do složky D:*D:\\_TestImages\Downloads* na místním disku. 

```powershell
# download first blob
Get-AzStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx
```

## <a name="data-transfer-with-azcopy"></a>Přenos dat pomocí AzCopy

Nástroj příkazového řádku AzCopy nabízí vysoce výkonný a skriptový přenos dat pro Azure Storage. AzCopy můžete použít k přenosu dat do a ze služby Blob Storage a souborů Azure. Další informace o AzCopy v10 za účelem, nejnovější verzi AzCopy, najdete v tématu [Začínáme s AzCopy](../common/storage-use-azcopy-v10.md). Další informace o použití AzCopy v10 za účelem s úložištěm objektů BLOB najdete v tématu [přenos dat pomocí AzCopy a BLOB Storage](../common/storage-use-azcopy-blobs.md).

Následující příklad používá AzCopy k nahrání místního souboru do objektu BLOB. Nezapomeňte nahradit vzorové hodnoty vlastními hodnotami:

```powershell
azcopy login
azcopy copy 'C:\myDirectory\myTextFile.txt' 'https://mystorageaccount.blob.core.windows.net/mycontainer/myTextFile.txt'
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odeberte všechny prostředky, které jste vytvořili. Nejjednodušším způsobem, jak odebrat prostředky, je odstranit skupinu prostředků. Odebráním skupiny prostředků se odstraní také všechny prostředky, které tato skupina obsahuje. V následujícím příkladu se odebráním skupiny prostředků odebere účet úložiště a samotná skupiny prostředků.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přenesli soubory mezi místním systémem souborů a úložištěm objektů BLOB v Azure. Další informace o práci s úložištěm objektů BLOB pomocí PowerShellu najdete v Azure PowerShell ukázek pro úložiště objektů BLOB.

> [!div class="nextstepaction"]
> [Ukázky Azure PowerShellu pro službu Azure Blob Storage](storage-samples-blobs-powershell.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Rutiny Microsoft Azure PowerShellu pro úložiště – referenční informace

* [Rutiny PowerShellu pro úložiště](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.

---
title: Rychlý start Azure – Nahrávání, stahování a výpis objektů blob v Azure Storage pomocí Azure PowerShellu | Microsoft Docs
description: V tomto rychlém startu pomocí Azure PowerShellu vytvoříte účet úložiště a kontejner. Pak použijete PowerShell k nahrání objektu blob do služby Azure Storage, stažení objektu blob a výpisu objektů blob v kontejneru.
services: storage
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.topic: quickstart
ms.date: 02/22/2018
ms.author: rogarana
ms.openlocfilehash: 29eb69f95680babfc1004d483a6a1c2bc44edda8
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="quickstart-upload-download-and-list-blobs-using-azure-powershell"></a>Rychlý start: Nahrávání, stahování a výpis objektů blob pomocí Azure PowerShellu

Modul Azure PowerShell slouží k vytváření a správě prostředků Azure z příkazového řádku PowerShellu nebo ve skriptech. Tato příručka podrobně popisuje použití PowerShellu k přenosu souborů mezi místním diskem a úložištěm objektů blob v Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

Tento rychlý start vyžaduje modul Azure PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Objekty blob se vždy nahrávají do kontejneru. Skupiny objektů blob můžete organizovat podobně, jako organizujete soubory do složek na svém počítači.

Nastavte název kontejneru a pak kontejner vytvořte pomocí rutiny [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer), přičemž nastavte oprávnění na „blob“, abyste umožnili veřejný přístup k souborům. Název kontejneru v tomto příkladu je *quickstartblobs*.

```powershell
$containerName = "quickstartblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-to-the-container"></a>Nahrání objektů blob do kontejneru

Úložiště objektů blob podporuje objekty blob bloku, doplňovací objekty blob a objekty blob stránky. Soubory VHD, které se používají pro virtuální počítače IaaS, jsou objekty blob stránky. Doplňovací objekty blob se používají k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Většina souborů uložených v úložišti objektů blob je objekty blob bloku. 

Pokud chcete nahrát soubor do objektu blob bloku, získejte odkaz na kontejner a pak získejte odkaz na objekt blob bloku v tomto kontejneru. Jakmile budete mít tento odkaz na objekt blob, můžete do něj nahrát data pomocí rutiny [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Tato operace vytvoří objekt blob, pokud ještě neexistuje, nebo ho přepíše, pokud už existuje.

Následující příklady nahrají soubory Image001.jpg a Image002.png ze složky D:\\_TestImages na místním disku do kontejneru, který jste vytvořili.

```powershell
# upload a file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image001.jpg" `
  -Container $containerName `
  -Blob "Image001.jpg" `
  -Context $ctx 

# upload another file
Set-AzureStorageBlobContent -File "D:\_TestImages\Image002.png" `
  -Container $containerName `
  -Blob "Image002.png" `
  -Context $ctx
```

Než budete pokračovat, můžete nahrát libovolné množství souborů.

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Získejte seznam objektů blob v kontejneru pomocí rutiny [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob). Tento příklad zobrazí pouze názvy nahraných objektů blob.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte objekty blob na svůj místní disk. Pro každý objekt blob, který se má stáhnout, nastavte název a zavoláním rutiny [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) objekt blob stáhněte.

Tento příklad stáhne objekty blob do složky D:\\_TestImages\Downloads na místním disku. 

```powershell
# download first blob
Get-AzureStorageBlobContent -Blob "Image001.jpg" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 

# download another blob
Get-AzureStorageBlobContent -Blob "Image002.png" `
  -Container $containerName `
  -Destination "D:\_TestImages\Downloads\" `
  -Context $ctx 
```

## <a name="data-transfer-with-azcopy"></a>Přenos dat pomocí AzCopy

Nástroj [AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) představuje další možnost vysoce výkonného a skriptovatelného přenosu dat pro službu Azure Storage. Pomocí AzCopy můžete přenášet data do a ze služeb Blob, File a Table Storage.

Tady je jednoduchý příklad příkazu AzCopy pro nahrání souboru *myfile.txt* do kontejneru *mystoragecontainer* z okna PowerShellu.

```PowerShell
./AzCopy `
    /Source:C:\myfolder `
    /Dest:https://mystorageaccount.blob.core.windows.net/mystoragecontainer `
    /DestKey:<storage-account-access-key> `
    /Pattern:"myfile.txt"
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odeberte všechny prostředky, které jste vytvořili. Nejjednodušším způsobem, jak to provést, je odstranit skupinu prostředků. Tím se odstraní také všechny prostředky, které skupina obsahuje. V tomto případě se odebere účet úložiště i samotná skupina prostředků.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak přenášet soubory mezi místním diskem a úložištěm objektů blob v Azure. Další informace o práci s úložištěm objektů blob najdete v postupech pro úložiště objektů blob.

> [!div class="nextstepaction"]
> [Operace s úložištěm objektů blob – postupy](storage-how-to-use-blobs-powershell.md)

### <a name="microsoft-azure-powershell-storage-cmdlets-reference"></a>Rutiny Microsoft Azure PowerShellu pro úložiště – referenční informace
* [Rutiny PowerShellu pro úložiště](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.
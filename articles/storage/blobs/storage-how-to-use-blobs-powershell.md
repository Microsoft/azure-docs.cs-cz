---
title: "Provádění operací na Azure Blob storage (úložiště objektů) pomocí prostředí PowerShell | Microsoft Docs"
description: "Kurz – provádění operací na Azure Blob storage (úložiště objektů) pomocí prostředí PowerShell"
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 09/14/2017
ms.author: tamram
ms.openlocfilehash: 8b87ecc3fefe5338269a6ad584e9a097ff3fb633
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>Provedení operace úložiště objektů Blob v Azure pomocí prostředí Azure PowerShell

Azure Blob Storage je služba, která slouží k ukládání velkých objemů nestrukturovaných dat objektů, například textu nebo binárních dat, která jsou přístupná odkudkoli na světě prostřednictvím protokolů HTTP nebo HTTPS. Tento článek se týká základních operací v úložišti objektů Blob v Azure, jako je například odesílání, stahování a odstraňování objektů BLOB. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření kontejneru 
> * Nahrát objektů BLOB
> * Zobrazí seznam objektů blob v kontejneru 
> * Stáhnout objekty blob
> * Kopírování objektů BLOB
> * Odstranění objektů blob
> * Zobrazení a nastavte vlastnosti a metadata do objektu blob
> * Správa zabezpečení pomocí sdílené přístupové podpisy

Tento kurz vyžaduje modul Azure PowerShell verze 3.6 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Vytvoření kontejneru

Objekty blob se vždy nahrávají do kontejneru. Kontejnery jsou podobná adresářů v počítači umožňuje uspořádat skupiny objektů BLOB v kontejnerech jako uspořádání souborů ve složkách ve vašem počítači. Účet úložiště může mít libovolný počet kontejnerů; jenom je omezen velikostí místa pro v účtu úložiště (až 500TB). 

Když vytvoříte kontejner, můžete nastavit úroveň přístupu, která pomáhá definovat, kdo má přístup k objektům BLOB v tomto kontejneru. Například může být privátní (úroveň přístupu = `Off`), což znamená nikdo k nim mají přístup bez sdílený přístupový podpis nebo přístupové klíče pro účet úložiště. Pokud nezadáte úroveň přístupu, když vytvoříte kontejner, bude výchozí privátní.

Můžete chtít bitové kopie v vašeho kontejneru přístupná veřejně. Pokud chcete uložit bitové kopie, který se má zobrazit na webu, můžete například chtít mají být veřejné. V takovém případě nastavíte kontejner přístupu na úrovni `blob`, a přístup každý, kdo má URL ukazující na objekt blob v tomto kontejneru objektu blob.

Pokud chcete vytvořit kontejner, nastavte název kontejneru a pak vytvořit kontejner, nastavení oprávnění k 'blob'. Názvy kontejnerů musí začínat písmenem nebo číslicí a může obsahovat pouze písmena, číslice a znak pomlčka (-). Další pravidla o pojmenovávání objekty BLOB a kontejnerů, najdete v tématu [pojmenování a odkazování na kontejnerů, objektů BLOB a metadat](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Vytvořit nový kontejner s [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer). Nastavte úroveň přístupu k veřejné. Název kontejneru v tomto příkladu je *howtoblobs*.

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>Nahrajte do kontejneru objektů BLOB

Úložiště objektů Blob Azure podporuje objekty BLOB bloku, doplňovací objekty BLOB a objekty BLOB stránky.  Soubory VHD, které se používají pro virtuální počítače IaaS, jsou objekty blob stránky. Doplňovací objekty blob se používají k protokolování, například když chcete zapisovat do souboru a pak přidávat další informace. Většina souborů uložených v úložišti objektů blob je objekty blob bloku. 

Pokud chcete nahrát soubor do objektu blob bloku, získejte odkaz na kontejner a pak získejte odkaz na objekt blob bloku v tomto kontejneru. Jakmile budete mít tento odkaz na objekt blob, můžete do něj nahrát data pomocí rutiny [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Tato operace vytvoří objekt blob, pokud neexistuje, nebo ho přepíše, pokud již existuje.

Následující ukazuje, jak nahrát objekt blob do kontejneru. Nastavte nejprve, proměnné, které přejděte do adresáře v místním počítači, kde jsou umístěny soubory a nastavte proměnnou pro název souboru k odeslání. To je užitečné, pokud chcete provádět stejné operace opakovaně. Nahrajte několik souborů, abyste viděli více položek, při výpisu objektů BLOB v kontejneru.

Následující příklady zatížení Image001.jpg a Image002.png od D:\\_TestImages na místním disku ke kontejneru, který jste právě vytvořili.

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

Nahrajte jiný soubor. 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

Než budete pokračovat, můžete nahrát libovolné množství souborů.

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru

Získání seznamu objektů BLOB v kontejneru pomocí [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) a vyberete název objektu blob, který se má zobrazit.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Stáhnout objekty blob

Stáhněte objekty blob na svůj místní disk. Nastavte nejprve, proměnné, která odkazuje na místní složku, do které chcete stáhnout objekty BLOB. Pro každý objekt blob ke stažení, nastavte název a volání [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent) ke stažení objektu blob.

Tento příklad zkopíruje objektů BLOB do D:\\_TestImages\Downloads na místním disku. 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>Kopírování objektů BLOB

Existuje několik věcí, které je třeba zvážit při kopírování objektů BLOB. Vám může být právě zkopírování objektu blob do nový název ve stejném umístění. Může být zkopírování objektu blob na účet samostatného úložiště. Může být kopírování velkého objektu blob (například soubor virtuálního pevného disku) na jiný účet úložiště. Tyto každý provádějí jinak.

### <a name="simple-blob-copy"></a>Kopírování jednoduché objektů blob

Můžete vytvořit kopii jeden z objektů BLOB v kontejneru zkopírováním do nového objektu blob. V tomto příkladu ho zkopírujte do kontejneru s jiným názvem, ale můžete stejně snadno vytvořit jiný kontejner a zkopírujte jej existuje místo. Tento příklad ukazuje, jak používat [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) ke zkopírování objektu blob. Budete muset určit zdrojový a cílový objekt blob název i název kontejneru.

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>Kopírovat objekt blob jiný účet úložiště 

Můžete kopírovat objekt blob účet samostatného úložiště. Jedním z příkladů tohoto postupu je zkopírovat soubor virtuálního pevného disku, který zálohuje jeden z virtuálních počítačů na jiný účet úložiště, aby bylo možné ho zálohovat. 

Nastavit druhého účtu úložiště, načtení kontextu, nastavit kontejner v daném účtu úložiště a pro kopírování. Tato část skriptu je téměř identický s výjimkou pomocí druhého účtu úložiště místo první skript výše.

```powershell
#create new storage account, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>Asynchronně kopírování velmi rozsáhlých objektů BLOB

Pokud kopírujete velký objekt blob, který je několik GB, můžete využít asynchronní kopie spuštěním a pak vracející se zpět a kontrole stavu, dokud se nedokončí jeho. Tímto způsobem můžete nejsou svázané při provádí operace kopírování.

Pokud kopírujete v rámci účtu úložiště, je velmi rychlé vytvoření kopie. Pokud kopírujete mezi dva účty úložiště ve stejné oblasti, kopie je poměrně krátkou dobu. Ale pokud zdrojové a cílové v oblastech, může trvat několik hodin, kopírování, v závislosti na vzdálenost a velikost souboru. 

Tento skript používá stejné proměnné definované v posledním příkladu kopírování. Rozdíl je, že tento jeden zachytí stav kopie a se dotazuje opakovaně každých 10 sekund, dokud se nedokončí jeho. Možná budete muset odeslat velmi rozsáhlých objektů blob do svého účtu úložiště zobrazení tohoto trvat víc než jeden iterace k dokončení.

Použití [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate) dotaz na stav kopie. 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>Odstranění objektů blob

Chcete-li odebrat objekty BLOB z účtu úložiště, použijte [odebrat AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob). 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>Číst a Zapisovat vlastnosti objekt blob a metadat

Pro přístup k sadu vlastností a metod vrácené **IListBlobItem**, musíte vysílat (nebo ji převést) k **CloudBlockBlob**, **CloudPageBlob**, nebo **CloudBlobDirectory** objektu. Pokud je typ neznámý, můžete použít kontrolu typu a zjistit, na který typ vysílat. Následující kód ukazuje, jak načíst a získat výstup vlastnosti jednoho objektů BLOB načten pomocí dříve [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) a výsledek, který má objekt CloudBlockBlob přetypování.

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

Naplnění vlastností systému voláním FetchAttributes a potom zobrazit tyto vlastnosti. Některé vlastnosti nejsou s možností zápisu a můžete změnit jejich hodnot. Tento příklad ukazuje, jak změnit typ obsahu, také známé jako typ MIME.

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

Každý objekt blob má svou vlastní metadata, která můžete nastavit. Může například uložit jméno uživatele, kteří nahrát objekt blob nebo datum a čas, který byl nejprve nahrát. Metadata se skládá z dvojice klíč/hodnota. To lze upravit pomocí prostředí PowerShell následujícím způsobem.

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>Správa zabezpečení pro objekty blob 

Ve výchozím nastavení Azure Storage zajišťuje ochranu dat omezením přístupu k majiteli účtu, který je vlastníkem přístupové klíče účtu úložiště. Když budete chtít sdílet data objektu blob na svém účtu úložiště, je důležité zajistit, aby nedošlo k ohrožení zabezpečení vašich klíčů pro přístup k účtu. K tomuto účelu můžete použít sdílených podpis adresu URL pro přístup, což je adresa URL pro daný prostředek, který obsahuje parametry dotazu a token zabezpečení, který umožňuje konkrétní úroveň oprávnění pro konkrétní množství času. Chcete třeba udělit přístup pro čtení do objektu blob privátní 5 minut, někdo ji zobrazit. 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>Nastavení úrovně přístupu a jeho objekty BLOB kontejneru do privátního

Nejprve nastavte úroveň přístupu tohoto kontejner, aby `Off`, která označuje, že není k dispozici přístup bez sdílený přístupový podpis nebo klíč účtu. Použití [Set-AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl).

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>Test přístupu k privátní

Pokud chcete ověřit, že nemáte přístup k objektům BLOB v tomto kontejneru, vytvořit adresu URL na jeden z objektů BLOB bez sdíleného přístupového podpisu a zkuste zobrazení objektu blob. Pomocí protokolu HTTPS, adresa URL bude v následujícím formátu:

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

To ukazuje postup vytvoření adresy URL objektu blob.

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

Zkopírujte adresu URL objektu blob a vložte ho do okna prohlížeče privátní – zobrazí chybu autorizace, protože objekt blob je privátní a nepoužijete sdílený přístupový podpis. 

### <a name="create-the-sas-uri"></a>Vytvořit identifikátor URI SAS

Vytvořit identifikátor URI SAS – Toto je odkaz na objekt blob, včetně parametrů dotazu a tokenu zabezpečení, který tvoří SAS. Vložte tento identifikátor URI do privátní okno prohlížeče – zobrazí bitovou kopii. 

Nejprve vytvořte počáteční datum a čas a datum a čas vypršení platnosti pro přístup. Tato služba využívá okno 2 minut. 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

Vytvořit identifikátor URI SAS pomocí [New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken). Je třeba název kontejneru, název objektu blob, kontext účtu úložiště, oprávnění, která má být poskytnuta (v tento případ, čtení, zápisu a odstraňování) a počáteční a koncový čas pro přístup. 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

Zkopírovat identifikátor URI SAS a umístit ho v okně prohlížeče privátní; Zobrazí bitovou kopii.

Počkejte dostatečně dlouhé adresy URL do vypršení platnosti (2 minuty v tomto příkladu) a potom vložte adresu URL do jiné v anonymním okně prohlížeče. Tento čas se dojde k chybě autorizace a nezobrazí na obrázku, protože identifikátor URI SAS vypršela platnost.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odeberte všechny prostředky, které jste vytvořili. K tomuto účelu můžete odebrat skupinu prostředků, která se také odstraní všechny prostředky obsažené v rámci skupiny. V takovém případě se odeberou všechny účty úložiště a skupině prostředků, sám sebe.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili o základní správu úložiště objektů Blob například jak:

> [!div class="checklist"]
> * Vytvoření kontejneru 
> * Nahrát objektů BLOB
> * Zobrazí seznam objektů blob v kontejneru 
> * Stáhnout objekty blob
> * Kopírování objektů BLOB
> * Odstranění objektů blob
> * Číst a zapisovat do objektu blob metadata a vlastnosti
> * Správa zabezpečení pomocí sdílené přístupové podpisy

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Rutiny prostředí PowerShell služby Microsoft Azure Storage
* [Rutiny PowerShellu pro úložiště](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) je bezplatná samostatná aplikace od Microsoftu, která umožňuje vizuálně pracovat s daty Azure Storage ve Windows, macOS a Linuxu.

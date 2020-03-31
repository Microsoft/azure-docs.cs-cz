---
title: Rehydrate dat objektu blob z archivní vrstvy
description: Rehydratujte své objekty BLOB z úložiště archivu, abyste měli přístup k datům.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2019
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: 0a7012d9daa808933a51ac05862a8a9aa4cfcf77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77614804"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Rehydrate dat objektu blob z archivní vrstvy

Zatímco objekt blob je v úrovni přístupu archivu, je považován za offline a nelze číst ani upravovat. Metadata objektu blob zůstanou online a dostupná, což umožňuje vypsat objekt blob a jeho vlastnosti. Čtení a úpravy dat objektů blob je k dispozici pouze s online úrovněmi, jako je horké nebo cool. Existují dvě možnosti pro načtení a přístup k datům uloženým v vrstvě přístupu archivu.

1. [Rehydratujte archivovaný objekt blob na online úroveň](#rehydrate-an-archived-blob-to-an-online-tier) – rehydratujte objekt blob archivu na horký nebo ochlazovaný změnou úrovně pomocí operace [Nastavit úroveň blob.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier)
2. [Zkopírujte archivovaný objekt blob do online vrstvy](#copy-an-archived-blob-to-an-online-tier) – vytvořte novou kopii objektu blob archivu pomocí operace [Kopírování objektů blob.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Zadejte jiný název objektu blob a cílovou úroveň horké nebo studené.

 Další informace o úrovních najdete v [tématu úložiště objektů blob Azure: horké, studené a archivní úrovně přístupu](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Rehydratujte archivovaný objekt blob na online úroveň

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Zkopírování archivovaného objektu blob na online úroveň

Pokud nechcete znovu hydratovat objekt blob archivu, můžete provést operaci [objektu blob copy.](https://docs.microsoft.com/rest/api/storageservices/copy-blob) Původní objekt blob zůstane nezměněn v archivu, zatímco nový objekt blob se vytvoří v online horké nebo chladné vrstvě, na které můžete pracovat. V operaci Kopírování objektů blob můžete také nastavit volitelnou vlastnost *x-ms-rehydrate-priority* na Standardní nebo Vysoká (náhled) k určení priority, při které chcete vytvořit kopii objektu blob.

Objekty BLOB archivu lze zkopírovat pouze do cílových úrovní online v rámci stejného účtu úložiště. Kopírování objektu blob archivu do jiného objektu blob archivu není podporováno.

Kopírování objektu blob z archivu může trvat hodiny v závislosti na vybrané prioritě rehydratace. Operace **Kopírování objektů blob** na pozadí přečte objekt blob zdroje archivu a vytvoří nový objekt blob online ve vybrané cílové vrstvě. Nový objekt blob může být viditelný při seznamu objektů BLOB, ale data nejsou k dispozici, dokud není čtení z objektu blob zdrojového archivu dokončeno a data se zapisují do nového cílového objektu online. Nový objekt blob je jako nezávislá kopie a žádné změny nebo odstranění na něj nemá vliv na objekt blob zdrojového archivu.

## <a name="pricing-and-billing"></a>Ceny a fakturace

Rehydrating objektů BLOB z archivu do horké nebo studené úrovně se účtují jako operace čtení a načítání dat. Použití vysoké priority (náhled) má vyšší provozní a náklady na načítání dat ve srovnání se standardní prioritou. Rehydratace s vysokou prioritou se na faktuře zobrazí jako samostatná řádková položka. Pokud požadavek na vrácení objektu BLOB archivu s několika gigabajty trvá déle než 5 hodin, nebude vám účtována rychlost načítání s vysokou prioritou. Standardní rychlosti načítání však stále platí jako rehydratace byla upřednostněna před jinými požadavky.

Kopírování objektů BLOB z archivu do horkých nebo chladných úrovní se účtuje jako operace čtení a načítání dat. Operace zápisu se účtuje pro vytvoření nové kopie objektu blob. Poplatky za předčasné odstranění se nevztahují při kopírování do objektu blob online, protože zdrojový objekt blob zůstane v archivní vrstvě nezměněn. Pokud je vybrána, platí se poplatky za vyhledávání s vysokou prioritou.

Objekty BLOB v archivní vrstvě by měly být uloženy po dobu minimálně 180 dnů. Odstranění nebo rehydratace archivovaných objektů BLOB před 180 dny bude mít za následek poplatky za předčasné odstranění.

> [!NOTE]
> Další informace o cenách pro objekty BLOB bloku a rehydrataci dat najdete v [tématu Ceny za úložiště Azure](https://azure.microsoft.com/pricing/details/storage/blobs/). Další informace o poplatcích za odchozí přenos dat naleznete v [tématu Podrobnosti o cenách přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Scénáře Rychlý start

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Rehydratujte objekt blob archivu na online úroveň
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Na webu Azure Portal vyhledejte a vyberte **Všechny prostředky**.

1. Vyberte svůj účet úložiště.

1. Vyberte kontejner a pak vyberte objekt blob.

1. Ve **vlastnostech objektu blob**vyberte **Změnit úroveň**.

1. Vyberte úroveň **přístupu za tepla** nebo **pohodě.** 

1. Vyberte prioritu rehydratace **standardní** nebo **vysoké**.

1. V dolní části vyberte **Uložit.**

![Změna úrovně účtu úložiště](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)
Následující skript prostředí PowerShell lze použít ke změně vrstvy objektu blob objektu blob archivu. Proměnná `$rgName` musí být inicializována s názvem skupiny prostředků. Proměnná `$accountName` musí být inicializována s názvem účtu úložiště. Proměnná `$containerName` musí být inicializována s názvem kontejneru. Proměnná `$blobName` musí být inicializována s názvem objektu blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blobs = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $context

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", “Standard”)
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Kopírování objektu blob archivu do nového objektu blob s online vrstvou
Následující skript prostředí PowerShell lze použít ke kopírování objektu blob archivu do nového objektu blob v rámci stejného účtu úložiště. Proměnná `$rgName` musí být inicializována s názvem skupiny prostředků. Proměnná `$accountName` musí být inicializována s názvem účtu úložiště. Proměnné `$srcContainerName` `$destContainerName` a musí být inicializovány s názvy kontejnerů. Proměnné `$srcBlobName` `$destBlobName` a musí být inicializovány s názvy objektů blob. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Další kroky

* [Další informace o vrstvách úložiště objektů Blob](storage-blob-storage-tiers.md)
* [Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)
* [Správa životního cyklu úložiště objektů blob v Azure](storage-lifecycle-management-concepts.md)
* [Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)

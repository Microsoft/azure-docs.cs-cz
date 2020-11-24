---
title: Dehydratované data objektů BLOB z archivní úrovně
description: Pokud chcete získat přístup k datům objektu blob, můžete znovu vymezit své objekty BLOB z archivního úložiště. Zkopírování archivovaného objektu blob do online úrovně.
services: storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: f74d4ffdd724039354a311234317dac889cd7cfe
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95545924"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>Dehydratované data objektů BLOB z archivní úrovně

Když je objekt BLOB v archivní úrovni, považuje se za offline a nedá se číst ani upravovat. Metadata objektu BLOB zůstávají v online a k dispozici, což vám umožní zobrazit seznam objektů BLOB a jejích vlastností. Čtení a úpravy dat objektů BLOB jsou k dispozici pouze u online vrstev, jako je horká nebo studená. K dispozici jsou dvě možnosti načtení a přístup k datům uloženým v archivní úrovni přístupu.

1. [Dehydratované archivovaný objekt blob do online úrovně](#rehydrate-an-archived-blob-to-an-online-tier) – dehydratované objekt BLOB archivu na horkou nebo studenou změnou jeho úrovně pomocí operace [nastavit vrstvu objektu BLOB](/rest/api/storageservices/set-blob-tier) .
2. [Zkopírování archivovaného objektu blob do online úrovně](#copy-an-archived-blob-to-an-online-tier) – vytvoří novou kopii objektu BLOB archivu pomocí operace [kopírování objektu BLOB](/rest/api/storageservices/copy-blob) . Zadejte jiný název objektu BLOB a cílovou úroveň horké nebo studené.

 Další informace o úrovních najdete v tématu [Azure Blob Storage: horká, studená a archivní úroveň přístupu](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Dehydratované archivovaný objekt blob do online úrovně

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Zkopírování archivovaného objektu blob na online úroveň

Pokud nechcete znovu vyměnit svůj archivní objekt blob, můžete zvolit operaci [kopírování objektu BLOB](/rest/api/storageservices/copy-blob) . Původní objekt BLOB zůstane v archivu beze změny, zatímco nový objekt BLOB se vytvoří v online horké nebo studené vrstvě, kde můžete pracovat. V operaci kopírování objektu blob můžete také nastavit volitelnou vlastnost *x-MS-rehydratované priority* na hodnotu Standard nebo high a zadat prioritu, na které chcete vytvořit kopii objektu BLOB.

Kopírování objektu BLOB z archivu může trvat hodiny na dokončení v závislosti na vybrané prioritě rehydratovaného. Na pozadí operace **kopírování objektu BLOB** přečte váš zdrojový objekt BLOB archivu a vytvoří nový objekt BLOB online ve vybrané cílové vrstvě. Nový objekt BLOB může být viditelný při výpisu objektů blob, ale data nejsou dostupná, dokud se nedokončí čtení ze zdrojového objektu BLOB archivu a data se zapisují do nového online cílového objektu BLOB. Nový objekt BLOB je jako nezávislá kopie a jakákoli změna nebo odstranění do něj nemá vliv na zdrojový objekt BLOB archivu.

> [!IMPORTANT]
> Neodstraňujte zdrojový objekt blob, dokud není kopie úspěšně dokončena v cílovém umístění. Pokud se zdrojový objekt BLOB odstraní, cílový objekt BLOB nemusí dokončit kopírování a bude prázdný. Můžete zjistit stav operace kopírování v *x-MS-Copy-status* .

Archivní objekty BLOB se dají zkopírovat jenom do online cílových vrstev v rámci stejného účtu úložiště. Kopírování objektu BLOB archivu do jiného archivní objektu BLOB se nepodporuje. Následující tabulka uvádí možnosti CopyBlob.

|                                           | **Zdroj vrstvy Hot**   | **Zdroj studené vrstvy** | **Zdroj vrstvy archivu**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Cíl vrstvy Hot**                  | Podporováno             | Podporováno            | Podporováno v rámci stejného účtu; čeká na redehydrataci               |
| **Cíl na studené úrovni**                 | Podporováno             | Podporováno            | Podporováno v rámci stejného účtu; čeká na redehydrataci               |
| **Cíl vrstvy archivu**              | Podporováno             | Podporováno            | Nepodporované         |

## <a name="pricing-and-billing"></a>Ceny a fakturace

Dosazování objektů BLOB z archivu do horké nebo studené úrovně se účtuje jako operace čtení a načítání dat. Použití vysoké priority má vyšší náklady na operace a načítání dat ve srovnání se standardní prioritou. Dosazování s vysokou prioritou se ve vašem vyúčtování zobrazí jako samostatná položka řádku. Pokud žádost s vysokou prioritou vrátí objekt BLOB pro archivaci o několika gigabajtech, bude trvat víc než 5 hodin, bude se vám účtovat frekvence načítání s vysokou prioritou. Nicméně standardní míry načítání se ještě použijí, protože pro vyvýšení se nacházelo upřednostnění přes jiné požadavky.

Kopírování objektů BLOB z archivu na horké nebo studené úrovně se účtuje jako operace čtení a načítání dat. Pro vytvoření nové kopie objektu BLOB se účtuje operace zápisu. Poplatky za předčasné odstranění se nepoužívají při kopírování do online objektu blob, protože zdrojový objekt BLOB zůstane v archivní úrovni nezměněný. Poplatky za načtení s vysokou prioritou se použijí, pokud je vybraná možnost.

Objekty BLOB v archivní úrovni by měly být uložené minimálně 180 dnů. Odstranění nebo dehydratovanéní archivovaných objektů BLOB před 180 dny bude platit poplatky za předčasné odstranění.

> [!NOTE]
> Další informace o cenách pro objekty blob bloku a data pro dehydrataci najdete v tématu [Azure Storage ceny](https://azure.microsoft.com/pricing/details/storage/blobs/). Další informace o poplatcích za odchozí přenosy dat najdete v [podrobnostech o cenách](https://azure.microsoft.com/pricing/details/data-transfers/)přenosů dat.

## <a name="quickstart-scenarios"></a>Scénáře Rychlý start

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Dehydratované objekt BLOB archivu do online úrovně
# <a name="portal"></a>[Azure Portal](#tab/azure-portal)
1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. V Azure Portal vyhledejte a vyberte **všechny prostředky**.

1. Vyberte svůj účet úložiště.

1. Vyberte svůj kontejner a pak vyberte objekt BLOB.

1. Ve **vlastnostech objektu BLOB** vyberte **změnit úroveň**.

1. Vyberte **horkou** nebo **studenou** úroveň přístupu. 

1. Vyberte rehydratované prioritu **Standard** nebo **High**.

1. V dolní části vyberte **Uložit** .

![Změnit ](media/storage-tiers/blob-access-tier.png)
 ![ stav dehydratované kontroly úrovně účtu úložiště](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Pomocí následujícího skriptu PowerShellu můžete změnit úroveň objektu BLOB archivu. `$rgName`Proměnná musí být inicializována s názvem vaší skupiny prostředků. `$accountName`Proměnná musí být inicializována s názvem vašeho účtu úložiště. `$containerName`Proměnná musí být inicializována s názvem kontejneru. `$blobName`Proměnná musí být inicializována s vaším názvem objektu BLOB. 
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
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Kopírování objektu BLOB archivu do nového objektu BLOB s online vrstvou
K zkopírování objektu BLOB archivu do nového objektu BLOB v rámci stejného účtu úložiště můžete použít následující skript PowerShellu. `$rgName`Proměnná musí být inicializována s názvem vaší skupiny prostředků. `$accountName`Proměnná musí být inicializována s názvem vašeho účtu úložiště. `$srcContainerName`Proměnné a `$destContainerName` musí být inicializovány s názvy kontejnerů. `$srcBlobName`Proměnné a `$destBlobName` musí být inicializovány s názvy objektů BLOB. 
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

* [Další informace o Blob Storagech vrstvách](storage-blob-storage-tiers.md)
* [Ověření cen horké, studené a archivní úrovně v účtech Blob Storage a GPv2 v jednotlivých oblastech](https://azure.microsoft.com/pricing/details/storage/)
* [Správa životního cyklu úložiště objektů blob v Azure](storage-lifecycle-management-concepts.md)
* [Posouzení cen přenosu dat](https://azure.microsoft.com/pricing/details/data-transfers/)
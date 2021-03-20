---
title: Správa úrovně přístupu objektu BLOB v účtu Azure Storage
description: Přečtěte si, jak změnit úroveň objektu BLOB v účtu GPv2 nebo Blob Storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 183593022c934eaf52ffe18649c23e8deced34d8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98166491"
---
# <a name="manage-the-access-tier-of-a-blob-in-an-azure-storage-account"></a>Správa úrovně přístupu objektu BLOB v účtu Azure Storage

Každý objekt BLOB má výchozí úroveň přístupu, horkou, studenou nebo archivní. Objekt BLOB přebírá výchozí úroveň přístupu účtu Azure Storage, na kterém je vytvořený. Účty Blob Storage a GPv2 zpřístupňují atribut **úroveň přístupu** na úrovni účtu. Tento atribut určuje výchozí úroveň přístupu pro libovolný objekt blob, který není explicitně nastaven na úrovni objektu. Pro objekty s úrovní nastavenou na úrovni objektu se úroveň účtu nepoužije. Archivní úroveň je možné použít pouze na úrovni objektu. Mezi úrovněmi přístupu můžete kdykoli přepínat pomocí následujících kroků.

## <a name="change-the-tier-of-a-blob-in-a-gpv2-or-blob-storage-account"></a>Změna úrovně objektu BLOB v účtu GPv2 nebo Blob Storage

Následující scénáře používají Azure Portal nebo PowerShell:

# <a name="portal"></a>[Azure Portal](#tab/portal)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V Azure Portal vyhledejte a vyberte **všechny prostředky**.

1. Vyberte svůj účet úložiště.

1. Vyberte svůj kontejner a pak vyberte objekt BLOB.

1. Ve **vlastnostech objektu BLOB** vyberte **změnit úroveň**.

1. Vyberte úroveň přístupu **horká**, **studená** nebo **archivní** . Pokud je objekt BLOB v současné době v archivu a chcete ho znovu vyhodnotit do online úrovně, můžete také vybrat možnost rehydratované prioritu **Standard** nebo **High**.

1. V dolní části vyberte **Uložit** .

![Změna úrovně objektu BLOB v Azure Portal](media/storage-tiers/blob-access-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ke změně úrovně objektu BLOB se dá použít následující skript PowerShellu. `$rgName`Proměnná musí být inicializována s názvem vaší skupiny prostředků. `$accountName`Proměnná musí být inicializována s názvem vašeho účtu úložiště. `$containerName`Proměnná musí být inicializována s názvem kontejneru. `$blobName`Proměnná musí být inicializována s vaším názvem objektu BLOB.

```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName == ""

#Select the storage account and get the context
$storageAccount = Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to archive
$blob.ICloudBlob.SetStandardBlobTier("Archive")
```

---

## <a name="next-steps"></a>Další kroky

- [Správa výchozí úrovně přístupu účtu Azure Storage účtu](../common/manage-account-default-access-tier.md)
- [Přečtěte si informace o prodehydratacích dat objektů BLOB z archivní úrovně.](storage-blob-rehydration.md)
- [Podívejte se na horké, studené a archivní ceny v účtech Blob Storage a GPv2 podle oblasti.](https://azure.microsoft.com/pricing/details/storage/)

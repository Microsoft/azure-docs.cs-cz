---
title: Správa výchozí úrovně přístupu účtu Azure Storage
description: Naučte se, jak změnit výchozí úroveň přístupu účtu GPv2 nebo Blob Storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 637f748882b3ac84127c8b71761a06629e1e0957
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100653828"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Správa výchozí úrovně přístupu účtu Azure Storage

Každý Azure Storage účet má výchozí úroveň přístupu, ať už horkou, nebo studenou. Úroveň přístupu přiřadíte při vytváření účtu úložiště. Výchozí úroveň přístupu je horká.

Výchozí úroveň účtu můžete změnit nastavením atributu **úroveň přístupu** v účtu úložiště. Změna úrovně účtu se vztahuje na všechny objekty uložené v účtu, které nemají explicitně nastavenou úroveň. Přepínání úrovně účtu z horké na studenou povede k operacím zápisu (za 10 000) pro všechny objekty blob bez nastavené úrovně v účtech GPv2 a přepínání ze studené na horkou se účtují poplatky za operace čtení (za 10 000) a načítání dat (za GB) pro všechny objekty BLOB v účtech Blob Storage a GPv2.

U objektů BLOB s úrovní nastavenou na úrovni objektu se úroveň účtu nepoužívá. Archivní úroveň je možné použít pouze na úrovni objektu. Můžete kdykoli přepínat mezi úrovněmi přístupu.

Výchozí úroveň přístupu můžete po vytvoření účtu úložiště změnit pomocí následujících kroků.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Změna výchozí úrovně přístupu účtu GPv2 nebo Blob Storage

Následující scénáře používají Azure Portal nebo PowerShell:

# <a name="portal"></a>[Azure Portal](#tab/portal)

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. V Azure Portal vyhledejte a vyberte **všechny prostředky**.

1. Vyberte svůj účet úložiště.

1. V **Nastavení** vyberte **Konfigurace** , aby se zobrazila a změnila konfigurace účtu.

1. Vyberte správnou úroveň přístupu podle vašich potřeb: nastavte **úroveň přístupu** buď na **studenou** , nebo **za horkou**.

1. V horní části klikněte na **Uložit** .

![Změna výchozí úrovně účtu v Azure Portal](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Ke změně úrovně účtu se dá použít následující skript PowerShellu. `$rgName`Proměnná musí být inicializována s názvem vaší skupiny prostředků. `$accountName`Proměnná musí být inicializována s názvem vašeho účtu úložiště.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>Další kroky

- [Správa úrovně objektu BLOB v účtu Azure Storage](../blobs/manage-access-tier.md)
- [Určení, jestli má vaše aplikace nárok na zvýšení úrovně Premium](../blobs/storage-blob-performance-tiers.md)
- [Zapnutí metrik Azure Storage a vyhodnocení používání aktuálních účtů úložiště](../blobs/monitor-blob-storage.md)

---
title: Účty Batch a účty Azure Storage
description: Přečtěte si o Azure Batch účtech a způsobu jejich použití z hlediska vývoje.
ms.topic: conceptual
ms.date: 05/12/2020
ms.openlocfilehash: 20a2a28d0eaa2c7997ea93e66d07ecb99bf297a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83791145"
---
# <a name="batch-accounts-and-azure-storage-accounts"></a>Účty Batch a účty Azure Storage

Účet Azure Batch je jedinečnou identifikovanou entitou v rámci služby Batch. Většina řešení Batch používá [Azure Storage](../storage/index.yml) pro ukládání souborů prostředků a výstupních souborů, takže každý účet Batch je obvykle přidružen k odpovídajícímu účtu úložiště.

## <a name="batch-accounts"></a>Účty služby Batch

Veškeré zpracování a prostředky jsou přidruženy k účtu Batch. Pokud vaše aplikace odešle požadavek na službu Batch, ověří se tato žádost pomocí názvu účtu Azure Batch, adresy URL účtu a přístupové klávesy, nebo tokenu Azure Active Directory.

V jednom účtu Batch můžete spustit několik úloh Batch. Můžete také distribuovat úlohy mezi účty Batch, které jsou ve stejném předplatném, ale nacházejí se v různých oblastech Azure.

[!INCLUDE [batch-account-mode-include](../../includes/batch-account-mode-include.md)]

Účet Batch můžete vytvořit pomocí [Azure Portal](batch-account-create-portal.md) nebo programově, například pomocí [knihovny Batch Management .NET](batch-management-dotnet.md). Při vytváření účtu můžete přidružit účet úložiště Azure pro ukládání aplikací nebo vstupních a výstupních dat souvisejících s úlohami.

## <a name="azure-storage-accounts"></a>Účty úložiště Azure

Většina řešení Batch pro ukládání souborů prostředků a výstupních souborů používá službu Azure Storage. Například úkoly služby Batch (včetně standardních úkolů, spouštěcích úkolů, úkolů přípravy úloh a úkolů uvolnění úloh) obvykle určují soubory prostředků, které jsou umístěné v účtu úložiště. Účty úložiště také ukládá data, která jsou zpracována, a veškerá vygenerovaná výstupní data.

Batch podporuje následující typy účtů Azure Storage:

- Účty pro obecné účely verze 2 (GPv2)
- Účty pro obecné účely verze 1 (GPv1)
- Účty úložiště Blob (v současnosti podporuje fondy v konfiguraci virtuálního počítače)

Další informace o účtech úložiště najdete v tématu [Přehled účtu Azure Storage](../storage/common/storage-account-overview.md).

Účet úložiště můžete ke svému účtu Batch přidružit při vytváření účtu Batch nebo později. Při výběru účtu úložiště zvažte své požadavky na náklady a výkon. Například možnosti účtu úložiště GPv2 a účtu úložiště objektů blob podporují ve srovnání s účty GPv1 vyšší [limity kapacity a škálovatelnosti](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/). (Kontaktujte podporu Azure a požádejte o zvýšení limitu úložiště.) Tyto možnosti účtu můžou zlepšit výkon řešení Batch, který obsahuje velký počet paralelních úloh, které čtou nebo zapisují do účtu úložiště.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [uzlech a fondech](nodes-and-pools.md).
- Naučte se vytvořit účet Batch pomocí [Azure Portal](batch-account-create-portal.md).

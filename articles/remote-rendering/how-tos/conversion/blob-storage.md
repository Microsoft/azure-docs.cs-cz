---
title: Použití služby Azure Blob Storage pro převod modelů
description: Popisuje běžné kroky pro nastavení a používání úložiště objektů BLOB pro převod modelu.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 889c25fd1402c4438ff648afce64905de112c8db
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215540"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Použití služby Azure Blob Storage pro převod modelů

Služba [konverze modelu](model-conversion.md) vyžaduje přístup k Azure Blob Storage tak, aby mohla načíst vstupní data a uložit výstupní data. Tento článek popisuje, jak provést nejběžnější kroky.

## <a name="prepare-azure-storage-accounts"></a>Příprava účtů Azure Storage

- Vytvoření účtu úložiště (StorageV2)
- Vytvořte v účtu úložiště vstupní kontejner objektů BLOB (například s názvem "arrinput").
- Vytvořte výstupní kontejner objektů BLOB v účtu úložiště (například s názvem "arroutput").

> [!TIP]
> Podrobné pokyny, jak nastavit účet úložiště, najdete v tématu [rychlý Start: převod modelu pro vykreslování.](../../quickstarts/convert-model.md)

Vytváření účtů úložiště a kontejnerů objektů BLOB se dá udělat jedním z následujících nástrojů:

- [Azure Portal](https://portal.azure.com)
- [AZ Command line](/cli/azure/install-azure-cli)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- Sady SDK (C#, Python...)

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Zajistěte, aby vzdálené vykreslování Azure mohlo mít přístup k vašemu účtu úložiště.

Služba Azure Remote Rending potřebuje načíst modelová data z vašeho účtu úložiště a zapisovat do ní data zpátky.

Přístup ke vzdálenému vykreslování Azure můžete udělit účtu úložiště následujícími dvěma způsoby:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Připojte svůj účet Azure Storage k účtu vzdáleného vykreslování Azure.

Postupujte podle kroků uvedených v části [Vytvoření účtu](../create-an-account.md#link-storage-accounts) .

### <a name="retrieve-sas-for-the-storage-containers"></a>Načtení SAS pro kontejnery úložiště

Uložené přístupové podpisy (SAS) slouží k udělení přístupu pro čtení vstupu a přístupu pro zápis do výstupu. Doporučujeme, abyste při každém převodu modelu vygenerovali nové identifikátory URI. Vzhledem k tomu, že identifikátory URI vyprší po určité době, jejich uchování po delší dobu může neočekávaně dojít k narušení vaší aplikace.

Podrobnosti o SAS najdete v [dokumentaci k SAS](../../../storage/common/storage-sas-overview.md).

Identifikátor URI SAS může být vygenerován pomocí jednoho z těchto:

- AZ PowerShell Module
  - Podívejte se na [ukázkové skripty PowerShellu](../../samples/powershell-example-scripts.md)
- [AZ Command line](/cli/azure/install-azure-cli)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
  - Klikněte pravým tlačítkem na kontejner získat sdílený přístupový podpis (číst, vypsat přístup pro vstupní kontejner, oprávnění k zápisu pro kontejner výstupu).
- Sady SDK (C#, Python...)

Příklad použití sdílených přístupových podpisů v převodu assetu se zobrazuje v Conversion.ps1 [ukázkových skriptech PowerShellu](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Nahrát vstupní model

Chcete-li začít s převodem modelu, je nutné jej nahrát pomocí jedné z následujících možností:

- [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) – pohodlné uživatelské rozhraní pro nahrávání, stahování a správu souborů v Azure Blob Storage
- [Příkazový řádek Azure](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Modul Azure PowerShellu](/powershell/azure/install-az-ps?view=azps-2.2.0)
  - Podívejte se na [ukázkové skripty PowerShellu](../../samples/powershell-example-scripts.md)
- [Používání sady SDK pro úložiště (Python, C#...)](../../../storage/index.yml)
- [Použití rozhraní REST API pro Azure Storage](/rest/api/storageservices/blob-service-rest-api)

Příklad, jak nahrát data pro převod, najdete v tématu Conversion.ps1 [ukázkových skriptů PowerShellu](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Získat identifikátor URI SAS pro převedený model

Tento krok je podobný jako při [načítání SAS pro kontejnery úložiště](#retrieve-sas-for-the-storage-containers). Tentokrát ale potřebujete načíst identifikátor URI SAS pro soubor modelu, který se zapsal do výstupního kontejneru.

Pokud například chcete načíst identifikátor URI SAS prostřednictvím [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/), klikněte pravým tlačítkem na soubor modelu a vyberte načíst sdílený přístupový podpis.

Sdílený přístupový podpis (SAS) pro načtení modelů je nutný, pokud jste svůj účet úložiště nepřipojili k účtu vzdáleného vykreslování Azure. Informace o tom, jak připojit svůj účet, najdete v tématu [Vytvoření účtu](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Další kroky

- [Konfigurace převodu modelu](configure-model-conversion.md)
- [REST API převodu modelu](conversion-rest-api.md)

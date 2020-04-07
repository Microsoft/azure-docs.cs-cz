---
title: Použití azure blob storage pro převod modelu
description: Popisuje běžné kroky pro nastavení a použití úložiště objektů blob pro převod modelu.
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 6f0605077bd131c54f27e3bf46240331557fd92e
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681646"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>Použití azure blob storage pro převod modelu

Služba [převodu modelu](model-conversion.md) vyžaduje přístup k úložišti objektů blob Azure, aby mohla načítat vstupní data a ukládat výstupní data. Tento článek popisuje, jak provést nejběžnější kroky.

## <a name="prepare-azure-storage-accounts"></a>Příprava účtů Azure Storage

- Vytvoření účtu úložiště (StorageV2)
- Vytvoření vstupního kontejneru objektů blob v účtu úložiště (například s názvem "arrinput")
- Vytvoření výstupního kontejneru objektů blob v účtu úložiště (například s názvem "arroutput")

> [!TIP]
> Podrobné pokyny k nastavení účtu úložiště najdete v úvodním [panelu: Převést model pro vykreslování](../../quickstarts/convert-model.md)

Vytvoření účtu úložiště a kontejnerů objektů blob lze provést pomocí jednoho z následujících nástrojů:

- [portál Azure](https://portal.azure.com)
- [příkazový řádek az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
- Sady SDK (C#, Python ... )

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>Zajistěte vzdálený vykreslování Azure přístup k vašemu účtu úložiště

Azure Remote Rending potřebuje načíst data modelu z vašeho účtu úložiště a zapsat data zpět do něj.

Přístup vzdáleného vykreslování Azure ke svému účtu úložiště můžete udělit dvěma způsoby:

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>Připojení účtu Azure Storage k účtu vzdáleného vykreslování Azure

Postupujte podle kroků uvedených v části [Vytvořit účet.](../create-an-account.md#link-storage-accounts)

### <a name="retrieve-sas-for-the-storage-containers"></a>Načtení SAS pro kontejnery úložiště

Uložené přístupové podpisy (SAS) se používají k udělení přístupu pro čtení pro vstup a přístup u zápisu pro výstup. Doporučujeme generovat nové identifikátory URI při každém převodu modelu. Vzhledem k tomu, že vyprší po určité době platnost identifikátorů URI, může jejich trvalé po delší dobu dojít k neočekávaně přeložení aplikace.

Podrobnosti o SAS naleznete v [dokumentaci SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1).

Identifikátor URI SAS lze vygenerovat pomocí jednoho z těchto:

- modul az PowerShell
  - Podívejte se na [ukázkové skripty prostředí PowerShell](../../samples/powershell-example-scripts.md)
- [příkazový řádek az](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)
  - klikněte pravým tlačítkem myši na kontejner "Get Shared Access Signature" (čtení, přístup k seznamu pro vstupní kontejner, přístup pro zápis pro výstupní kontejner)
- Sady SDK (C#, Python ... )

Příklad použití sdílených přístupových podpisů při převodu datových zdrojů je uveden v souboru Conversion.ps1 [v ukázkových skriptech aplikace Powershell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="upload-an-input-model"></a>Nahrání vstupního modelu

Chcete-li začít s převodem modelu, musíte jej nahrát pomocí jedné z následujících možností:

- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) – pohodlné rozhraní pro nahrávání a stahování a správu souborů v úložišti objektů blob azure
- [Příkazový řádek Azure](https://docs.microsoft.com/azure/storage/common/storage-azure-cli)
- [Modul Azure PowerShellu](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.2.0)
  - Podívejte se na [ukázkové skripty prostředí PowerShell](../../samples/powershell-example-scripts.md)
- [Použití sady SDK úložiště (Python, C# ... )](https://docs.microsoft.com/azure/storage/)
- [Použití api REST úložiště Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api)

Příklad, jak nahrát data pro konverzi, najdete v části Conversion.ps1 v [ukázkových skriptech aplikace Powershell](../../samples/powershell-example-scripts.md#script-conversionps1).

## <a name="get-a-sas-uri-for-the-converted-model"></a>Získání identifikátoru URI SAS pro převedený model

Tento krok je podobný [načítání SAS pro kontejnery úložiště](#retrieve-sas-for-the-storage-containers). Tentokrát však budete muset načíst Identifikátor URI SAS pro soubor modelu, který byl zapsán do výstupního kontejneru.

Chcete-li například načíst identifikátor URI SAS prostřednictvím [Průzkumníka úložiště Azure](https://azure.microsoft.com/features/storage-explorer/), klepněte pravým tlačítkem myši na soubor modelu a vyberte možnost Získat sdílený přístupový podpis.

Sdílený přístupový podpis (SAS) k načtení modelů je potřeba, pokud jste nepřipojili účet úložiště k účtu vzdáleného vykreslování Azure. Jak propojit svůj účet, najdete v [aplikaci Vytvořit účet](../create-an-account.md#link-storage-accounts).

## <a name="next-steps"></a>Další kroky

- [Konfigurace převodu modelu](configure-model-conversion.md)
- [Rozhraní REST API převodu modelu](conversion-rest-api.md)

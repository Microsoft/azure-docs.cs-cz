---
title: Načíst tokeny sdíleného přístupového podpisu v kódu | Azure Key Vault
description: Funkce spravovaného účtu úložiště poskytuje bezproblémovou integraci mezi Azure Key Vault a účtem služby Azure Storage. Tato ukázka používá sadu Azure SDK pro .NET ke správě tokenů SAS.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: afe7d5ce3dd1756ddb9e33fe402fb2eb699ce8f7
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007413"
---
# <a name="fetch-shared-access-signature-tokens-in-code"></a>Načtení tokenů sdíleného přístupového podpisu v kódu

Svůj účet úložiště můžete spravovat pomocí tokenů sdíleného přístupového podpisu (SAS) uložených v trezoru klíčů. Další informace najdete v tématu [udělení omezeného přístupu k prostředkům Azure Storage pomocí SAS](../../storage/common/storage-sas-overview.md).

> [!NOTE]
> Pro zabezpečení účtu úložiště doporučujeme použít [Access Control na základě rolí (RBAC)](../../storage/common/storage-auth-aad.md) a zajistit tak lepší zabezpečení a snadné použití při autorizaci pomocí sdíleného klíče.

Tento článek poskytuje ukázky kódu .NET, který vytváří definici SAS a načítá tokeny SAS. Úplné podrobnosti najdete v našem příkladu [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) , včetně vygenerovaného klienta pro účty úložiště spravované Key Vault. Informace o tom, jak vytvářet a ukládat tokeny SAS, najdete v tématech [Správa klíčů účtu úložiště pomocí Key Vault a Azure CLI](overview-storage-keys.md) nebo [Správa klíčů účtu úložiště pomocí Key Vault a Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Ukázky kódů

V následujícím příkladu vytvoříme šablonu SAS:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Pomocí této šablony můžeme vytvořit definici SAS pomocí 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Po vytvoření definice SAS můžete načíst tokeny SAS jako tajné klíče pomocí `SecretClient` . Název tajného klíče musí být název účtu úložiště následovaný pomlčkou:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Pokud se brzo vyprší platnost tokenu sdíleného přístupového podpisu, můžete znovu načíst stejný tajný klíč, abyste vygenerovali nový.

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [udělit omezený přístup k prostředkům Azure Storage pomocí SAS](../../storage/common/storage-sas-overview.md).
- Naučte se [spravovat klíče účtu úložiště pomocí Key Vault a Azure CLI](overview-storage-keys.md) nebo [Azure PowerShell](overview-storage-keys-powershell.md).
- Podívejte se na kompletní ukázku [ShareLink](https://docs.microsoft.com/samples/azure/azure-sdk-for-net/share-link/) .
- Další [ukázky Key Vault](https://docs.microsoft.com/samples/browse/?expanded=azure&products=azure-key-vault)
- Viz [ukázky klíčů spravovaného účtu úložiště](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) .

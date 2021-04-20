---
title: Načíst tokeny sdíleného přístupového podpisu v kódu | Azure Key Vault
description: Funkce spravovaného účtu úložiště poskytuje bezproblémovou integraci mezi Azure Key Vault a účtem služby Azure Storage. Tato ukázka používá sadu Azure SDK pro .NET ke správě tokenů SAS.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c8a7b357efc2e30d1ca93d0b45fd2563c980045
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752745"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>Vytvoření definice SAS a načtení tokenů sdíleného přístupového podpisu v kódu

Svůj účet úložiště můžete spravovat pomocí tokenů sdíleného přístupového podpisu (SAS) uložených v trezoru klíčů. Další informace najdete v tématu [udělení omezeného přístupu k prostředkům Azure Storage pomocí SAS](../../storage/common/storage-sas-overview.md).

> [!NOTE]
> Doporučujeme použít [řízení přístupu na základě role Azure (Azure RBAC)](../../storage/common/storage-auth-aad.md) k zabezpečení vašeho účtu úložiště za účelem zajištění vysokého zabezpečení a snadného použití při autorizaci pomocí sdíleného klíče.

Tento článek poskytuje ukázky kódu .NET, který vytváří definici SAS a načítá tokeny SAS. Úplné podrobnosti najdete v našem příkladu [ShareLink](/samples/azure/azure-sdk-for-net/share-link/) , včetně vygenerovaného klienta pro účty úložiště spravované Key Vault. Informace o tom, jak vytvářet a ukládat tokeny SAS, najdete v tématech [Správa klíčů účtu úložiště pomocí Key Vault a Azure CLI](overview-storage-keys.md) nebo [Správa klíčů účtu úložiště pomocí Key Vault a Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Ukázky kódů

V následujícím příkladu vytvoříme šablonu SAS:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Pomocí této šablony můžeme vytvořit definici SAS pomocí 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

Po vytvoření definice SAS můžete načíst tokeny SAS jako tajné klíče pomocí `SecretClient` . Název tajného klíče musí být název účtu úložiště následovaný pomlčkou:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Pokud se brzo vyprší platnost tokenu sdíleného přístupového podpisu, můžete znovu načíst stejný tajný klíč, abyste vygenerovali nový.

Návod, jak použít načtený z Key Vault token SAS pro přístup ke službám Azure Storage, najdete v tématu [použití SAS účtu pro přístup k BLOB Service](../../storage/common/storage-account-sas-create-dotnet.md#use-an-account-sas-from-a-client)

> [!NOTE]
> Vaše aplikace musí být připravená na aktualizaci SAS, pokud získá 403 z úložiště, abyste mohli zpracovat případ, kdy byl klíč napadený, a potřebujete ho otočit rychleji než normální období rotace. 

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [udělit omezený přístup k prostředkům Azure Storage pomocí SAS](../../storage/common/storage-sas-overview.md).
- Naučte se [spravovat klíče účtu úložiště pomocí Key Vault a Azure CLI](overview-storage-keys.md) nebo [Azure PowerShell](overview-storage-keys-powershell.md).
- Viz [ukázky klíčů spravovaného účtu úložiště](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=) .
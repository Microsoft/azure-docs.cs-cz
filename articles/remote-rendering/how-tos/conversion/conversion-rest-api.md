---
title: REST API převodu assetu
description: Popisuje, jak převést Asset prostřednictvím REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 341d24e73c9e07bb3155535d98a88145643c1692
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92201779"
---
# <a name="use-the-model-conversion-rest-api"></a>Použití rozhraní REST API pro převod modelů

Služba [převodu modelů](model-conversion.md) je řízena prostřednictvím [REST API](https://en.wikipedia.org/wiki/Representational_state_transfer). Tento článek popisuje podrobnosti rozhraní API služby převod.

## <a name="regions"></a>Oblasti

Přečtěte si [seznam dostupných oblastí](../../reference/regions.md) pro základní adresy URL, na které se mají požadavky odesílat.

## <a name="common-headers"></a>Společné hlavičky

### <a name="common-request-headers"></a>Běžné hlavičky požadavků

Tato záhlaví musí být zadána pro všechny požadavky:

- **Autorizační** hlavička musí mít hodnotu "nosiče [*token*]", kde [*token*] je [přístupový token služby](../tokens.md).

### <a name="common-response-headers"></a>Běžné hlavičky odpovědí

Všechny odpovědi obsahují tyto hlavičky:

- Hlavička **MS-CV** obsahuje jedinečný řetězec, který lze použít k trasování volání v rámci služby.

## <a name="endpoints"></a>Koncové body

Služba konverze poskytuje tři REST API koncové body:

- Spusťte převod modelu pomocí účtu úložiště propojeného s vaším účtem Azure Remote rendering. 
- Spusťte převod modelu pomocí zadaných *sdílených přístupových podpisů (SAS)*.
- dotaz na stav převodu

### <a name="start-conversion-using-a-linked-storage-account"></a>Spustit převod pomocí propojeného účtu úložiště
Váš účet vzdáleného vykreslování Azure musí mít přístup k zadanému účtu úložiště pomocí postupu, jak [propojit účty úložiště](../create-an-account.md#link-storage-accounts).

| Koncový bod | Metoda |
|-----------|:-----------|
| /V1/Accounts/**accountid**/Conversions/Create | POST |

Vrátí ID průběžného převodu zabaleného v dokumentu JSON. Název pole je "conversionId".

#### <a name="request-body"></a>Text požadavku

> [!NOTE]
> Všechno `input.folderPath` , co se načte, se načte a provede převod na Azure. Pokud `input.folderPath` parametr není zadán, bude načten celý obsah kontejneru. Všechny objekty BLOB a složky, které získají načíst, musí mít [platné názvy souborů Windows](/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Spustit převod pomocí zadaných podpisů sdíleného přístupu
Pokud váš účet ARR není propojený s vaším účtem úložiště, umožňuje toto rozhraní REST poskytovat přístup pomocí *sdílených přístupových podpisů (SAS)*.

| Koncový bod | Metoda |
|-----------|:-----------|
| /V1/Accounts/**accountid**/Conversions/createWithSharedAccessSignature | POST |

Vrátí ID průběžného převodu zabaleného v dokumentu JSON. Název pole je `conversionId` .

#### <a name="request-body"></a>Text požadavku

Text žádosti je stejný jako ve výše uvedeném volání metody Create REST, ale vstup a výstup obsahuje *tokeny sdíleného přístupového podpisu (SAS)*. Tyto tokeny poskytují přístup k účtu úložiště pro čtení vstupu a zápis výsledku převodu.

> [!NOTE]
> Tyto tokeny identifikátoru URI SAS jsou řetězce dotazu, nikoli úplný identifikátor URI. 

> [!NOTE]
> Všechno `input.folderPath` , co se načte, se načte a provede převod na Azure. Pokud `input.folderPath` parametr není zadán, bude načten celý obsah kontejneru. Všechny objekty BLOB a složky, které získají načíst, musí mít [platné názvy souborů Windows](/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Stav převodu cyklického dotazování
Stav průběžného převodu zahájeného pomocí jednoho z výše uvedených volání REST lze dotazovat pomocí následujícího rozhraní:


| Koncový bod | Metoda |
|-----------|:-----------|
| /V1/Accounts/**accountid**/Conversions/**conversionId** | GET |

Vrátí dokument JSON s polem "status", které může obsahovat následující hodnoty:

- Vytvářejí
- Instalovanou
- Nástup
- Poruše

Pokud je stav "Chyba", bude k dispozici další pole "Chyba" s podpolem "zpráva" obsahující informace o chybě. Další protokoly se nahrají do vašeho výstupního kontejneru.

## <a name="next-steps"></a>Další kroky

- [Použití služby Azure Blob Storage pro převod modelů](blob-storage.md)
- [Převod modelu](model-conversion.md)
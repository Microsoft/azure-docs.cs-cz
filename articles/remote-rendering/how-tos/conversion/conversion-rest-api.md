---
title: Rozhraní REST API konverze majetku
description: Popisuje, jak převést datový zdroj prostřednictvím rozhraní REST API
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 38116efc9e87eca8e2514a0a84045a69b8d42326
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80887040"
---
# <a name="use-the-model-conversion-rest-api"></a>Použití rozhraní REST API pro převod modelů

Služba [převodu modelu](model-conversion.md) je řízena prostřednictvím [rozhraní REST API](https://en.wikipedia.org/wiki/Representational_state_transfer). Tento článek popisuje podrobnosti rozhraní API služby převodu.

## <a name="regions"></a>Oblasti

Podívejte se na [seznam dostupných oblastí](../../reference/regions.md) pro základní adresy URL, na které chcete odeslat požadavky.

## <a name="common-headers"></a>Běžná záhlaví

### <a name="common-request-headers"></a>Běžné hlavičky požadavků

Tyto hlavičky musí být určeny pro všechny požadavky:

- Hlavička **Autorizace** musí mít hodnotu "Nosič [*TOKEN*]", kde [*TOKEN*] je [přístupový token služby](../tokens.md).

### <a name="common-response-headers"></a>Běžné hlavičky odpovědí

Všechny odpovědi obsahují tato záhlaví:

- Hlavička **MS-CV** obsahuje jedinečný řetězec, který lze použít ke sledování volání v rámci služby.

## <a name="endpoints"></a>Koncové body

Služba převodu poskytuje tři koncové body rozhraní REST API pro:

- Spusťte převod modelu pomocí účtu úložiště propojeného s účtem vzdáleného vykreslování Azure. 
- Zahájit převod modelu pomocí zadaných *sdílených přístupových podpisů (SAS).*
- dotaz na stav převodu

### <a name="start-conversion-using-a-linked-storage-account"></a>Zahájení převodu pomocí propojeného účtu úložiště
Váš účet vzdáleného vykreslování Azure musí mít přístup k účtu poskytovaného úložiště podle pokynů k [propojení účtů úložiště](../create-an-account.md#link-storage-accounts).

| Koncový bod | Metoda |
|-----------|:-----------|
| /v1/účty/**accountID**/převody/vytvořit | POST |

Vrátí ID probíhajícího převodu zabalené v dokumentu JSON. Název pole je "conversionId".

#### <a name="request-body"></a>Text požadavku


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
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Zahájení převodu pomocí zadaných sdílených přístupových podpisů
Pokud váš účet ARR není propojen s vaším účtem úložiště, toto rozhraní REST umožňuje poskytovat přístup pomocí *sdílených přístupových podpisů (SAS).*

| Koncový bod | Metoda |
|-----------|:-----------|
| /v1/accounts/**accountID**/conversions/createWithSharedAccessSignature | POST |

Vrátí ID probíhajícího převodu zabalené v dokumentu JSON. Název pole je "conversionId".

#### <a name="request-body"></a>Text požadavku

Tělo požadavku je stejné jako ve výše uvedeném volání create REST, ale vstupní a výstupní obsahují *tokeny sdílených přístupových podpisů (SAS).* Tyto tokeny poskytují přístup k účtu úložiště pro čtení vstupu a zápisu výsledek převodu.

> [!NOTE]
> Tyto tokeny identifikátoru URI sas jsou řetězce dotazu a nikoli úplné identifikátory URI. 


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

### <a name="poll-conversion-status"></a>Stav konverze ankety
Stav probíhajícího převodu zahájeného jedním z výše uvedených volání REST lze dotazovat pomocí následujícího rozhraní:


| Koncový bod | Metoda |
|-----------|:-----------|
| /v1/účty/**accountID**/konverze/**conversionId** | GET |

Vrátí dokument JSON s polem "status", které může mít následující hodnoty:

- "Běh"
- "Úspěch"
- "Selhání"

Pokud je stav "Selhání", bude existovat další "chyba" pole s dílčím polem "zpráva" obsahující informace o chybě. Další protokoly budou odeslány do výstupního kontejneru.

## <a name="next-steps"></a>Další kroky

- [Použití služby Azure Blob Storage pro převod modelů](blob-storage.md)
- [Převod modelu](model-conversion.md)

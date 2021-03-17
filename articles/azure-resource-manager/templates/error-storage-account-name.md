---
title: Chyby názvu účtu úložiště
description: Popisuje chyby, se kterými se můžete setkat při zadávání názvu účtu úložiště.
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.openlocfilehash: 5b2706d8540ea38ef08bf7ca0f804e6811a93085
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76153968"
---
# <a name="resolve-errors-for-storage-account-names"></a>Řešení chyb pro názvy účtů úložiště

Tento článek popisuje chyby pojmenování, ke kterým může dojít při nasazování účtu úložiště.

## <a name="symptom"></a>Příznak

Pokud název vašeho účtu úložiště obsahuje zakázané znaky, zobrazí se chyba, například:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Pro účty úložiště musíte zadat název prostředku, který je v Azure jedinečný. Pokud nezadáte jedinečný zámek, zobrazí se chyba typu:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Pokud nasadíte účet úložiště se stejným názvem jako existující účet úložiště v předplatném, ale zadáte jiné umístění, zobrazí se chyba oznamující, že účet úložiště již existuje v jiném umístění. Buď odstraňte existující účet úložiště, nebo zadejte stejné umístění jako stávající účet úložiště.

## <a name="cause"></a>Příčina

Názvy účtů úložiště musí mít délku 3 až 24 znaků a musí obsahovat jenom číslice a malá písmena. Název musí být jedinečný.

## <a name="solution"></a>Řešení

Ujistěte se, že je název účtu úložiště jedinečný. Jedinečný název můžete vytvořit zřetězením konvence pojmenování s výsledkem funkce [uniqueString](template-functions-string.md#uniquestring) .

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Ujistěte se, že název účtu úložiště není delší než 24 znaků. Funkce [uniqueString](template-functions-string.md#uniquestring) vrátí 13 znaků. Pokud zřetězete předponu nebo příponu do výsledku **uniqueString** , zadejte hodnotu, která je 11 znaků nebo méně.

```json
"parameters": {
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
    "description": "The value to use for starting the storage account name."
    }
  }
}
```

Ujistěte se, že název účtu úložiště neobsahuje žádná velká písmena ani speciální znaky.
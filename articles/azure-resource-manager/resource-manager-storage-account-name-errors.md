---
title: Chyby název účtu úložiště Azure | Dokumentace Microsoftu
description: Popisuje chyby, které můžete narazit při zadávání názvu účtu úložiště.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: c3d4d764b1076c8705cfa64d6c0b38e3b8c1a801
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60390093"
---
# <a name="resolve-errors-for-storage-account-names"></a>Řešení chyb pro názvy účtů úložiště

Tento článek popisuje pojmenování chyby, na které můžete narazit při nasazení účtu úložiště.

## <a name="symptom"></a>Příznak

Pokud název svého účtu úložiště obsahuje zakázané znaky, obdržíte chybu, jako jsou:

```
Code=AccountNameInvalid
Message=S!torageckrexph7isnoc is not a valid storage account name. Storage account name must be 
between 3 and 24 characters in length and use numbers and lower-case letters only.
```

Pro účty úložiště je nutné zadat název pro prostředek, který je jedinečný v rámci Azure. Pokud nezadáte jedinečný název, zobrazí chyba typu:

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

Pokud nasazení účtu úložiště se stejným názvem jako stávající účet úložiště ve vašem předplatném, ale zadejte jiné umístění, zobrazí se chybová zpráva oznamující, že účet úložiště už existuje v jiném umístění. Odstraňte existující účet úložiště, nebo zadejte stejné umístění jako existující účet úložiště.

## <a name="cause"></a>Příčina

Názvy účtů úložiště musí být dlouhý 3 až 24 znaků a použít pouze číslice a malá písmena. Název musí být jedinečný.

## <a name="solution"></a>Řešení

Ujistěte se, že název účtu úložiště není jedinečný. Můžete vytvořit jedinečný název zřetězením svých zásad vytváření názvů výsledkem [uniqueString](resource-group-template-functions-string.md#uniquestring) funkce.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

Ujistěte se, že váš název účtu úložiště není delší než 24 znaků. [UniqueString](resource-group-template-functions-string.md#uniquestring) funkce vrací 13 znaků. Pokud zřetězit předpony nebo přípony k **uniqueString** výsledek, zadejte hodnotu, která je 11 znaků nebo méně.

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

Ujistěte se, že váš název účtu úložiště neobsahuje žádné znaky velké písmeno nebo speciální znaky.
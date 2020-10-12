---
title: Zaškrtávací políčko – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. CheckBox pro Azure Portal. Umožňuje uživatelům vybrat možnost zaškrtnutí nebo zrušit zaškrtnutí možnosti.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tomfitz
ms.openlocfilehash: 9f40f223cca34df58d2af7373d8f60fd7f383162
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87097105"
---
# <a name="microsoftcommoncheckbox-ui-element"></a>Microsoft. Common. CheckBox – element uživatelského rozhraní

Ovládací prvek CheckBox umožňuje uživatelům zaškrtnout nebo zrušit zaškrtnutí možnosti. Ovládací prvek vrátí **hodnotu true** , pokud je ovládací prvek zaškrtnuto nebo **false** , pokud není zaškrtnuto.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

:::image type="content" source="./media/managed-application-elements/microsoft-common-checkbox.png" alt-text="Microsoft. Common. CheckBox":::

## <a name="schema"></a>Schéma

```json
{
    "name": "legalAccept",
    "type": "Microsoft.Common.CheckBox",
    "label": "I agree to the terms and conditions.",
    "constraints": {
        "required": true,
        "validationMessage": "Please acknowledge the legal conditions."
    }
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
true
```

## <a name="remarks"></a>Poznámky

Pokud nastavíte možnost **požadováno** na **hodnotu true**, musí uživatel zaškrtnout políčko. Pokud uživatel nevybere zaškrtávací políčko, zobrazí se zpráva ověření.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).

---
title: Vytvoření definice prvku uživatelského rozhraní Azure | Dokumentace Microsoftu
description: Popisuje prvky, které mají používat při vytváření definice uživatelského rozhraní pro Azure portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: tomfitz
ms.openlocfilehash: 895b5cfb121347f9400d1aa315fef05d9088c55c
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46366467"
---
# <a name="createuidefinition-elements"></a>Prvky CreateUiDefinition
Tento článek popisuje vlastnosti schématu a pro všechny podporované elementy tohoto CreateUiDefinition. 

## <a name="schema"></a>Schéma

Schéma pro většinu prvků vypadá takto:

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Some text box",
  "defaultValue": "my value",
  "toolTip": "Provide a descriptive name.",
  "constraints": {},
  "options": {},
  "visible": true
}
```

| Vlastnost | Požaduje se | Popis |
| -------- | -------- | ----------- |
| jméno | Ano | Interní identifikátor pro odkazování na konkrétní instanci elementu. Nejběžnější použití názvu elementu je v `outputs`, kde výstupní hodnoty zadané elementy jsou namapovány na parametry šablony. Můžete ho můžete použít k vytvoření vazby výstupní hodnota element, který má `defaultValue` jiného elementu. |
| type | Ano | Ovládací prvek uživatelského rozhraní pro vykreslení elementu. Seznam podporovaných typů najdete v tématu [prvky](#elements). |
| label | Ano | Zobrazený text elementu. Některé typy prvků obsahovat více popisků, takže hodnota může být objekt, který obsahuje více řetězců. |
| Výchozí hodnota | Ne | Výchozí hodnota elementu. Některé typy prvků podporu komplexní výchozí hodnoty, takže hodnota může být objekt. |
| Popis tlačítka | Ne | Text zobrazený v popisu tlačítka prvku. Podobně jako `label`, některé prvky podporovat více řetězců tip nástroj. Vložených odkazů můžete vložit pomocí syntaxe Markdownu.
| Omezení | Ne | Jednu nebo více vlastností, které se používají k přizpůsobení chování ověřování elementu. Podporované vlastnosti pro omezení se liší podle typu elementu. Některé typy prvků nepodporuje přizpůsobení chování ověřování a tedy mít žádnou vlastnost omezení. |
| Možnosti | Ne | Další vlastnosti, které přizpůsobit chování element. Podobně jako `constraints`, podporovaných vlastností se liší podle typu elementu. |
| Viditelné | Ne | Určuje, zda je zobrazen elementu. Pokud `true`, se zobrazí element a příslušných podřízených elementů. Výchozí hodnota je `true`. Použití [logické funkce](create-uidefinition-functions.md#logical-functions) dynamicky řídit hodnota této vlastnosti.

## <a name="elements"></a>Elementy

V dokumentaci pro každý prvek obsahuje ukázkové uživatelské rozhraní, schéma a poznámky na chování prvku (obvykle o ověření a podporované přizpůsobení) a ukázkový výstup.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Další postup
Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).

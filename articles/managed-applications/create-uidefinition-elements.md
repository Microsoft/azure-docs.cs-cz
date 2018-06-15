---
title: Azure vytvořit element definice uživatelského rozhraní | Microsoft Docs
description: Popisuje elementy pro použití při vytváření definice uživatelského rozhraní pro portál Azure.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: 0a69f46294fc370b1eb403440af5bb3c25ef995d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
ms.locfileid: "34305238"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition elementy
Tento článek popisuje vlastnosti schématu a pro všechny podporované elementy CreateUiDefinition. 

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
| jméno | Ano | Interní identifikátor tak, aby odkazovaly konkrétní instanci elementu. Nejběžnější využití název elementu je v `outputs`, kde jsou hodnoty výstup zadaných elementů namapované na parametry šablony. Můžete ji použít i k vytvoření vazby výstupní hodnotu elementu na `defaultValue` jiného elementu. |
| type | Ano | Kontrolní mechanismus uživatelského rozhraní pro vykreslení elementu. Seznam podporovaných typů najdete v tématu [elementy](#elements). |
| Popisek | Ano | Zobrazovaný text elementu. Některé typy element obsahovat více štítků, takže hodnotou může být objekt obsahující více řetězců. |
| Výchozí hodnota | Ne | Výchozí hodnota elementu. Některé typy element podporují komplexní výchozí hodnoty, takže hodnotou může být objekt. |
| Popisek | Ne | Text, který zobrazit jako popis elementu. Podobně jako `label`, některé prvky podporovat více nástroj tip řetězců. Vložené odkazy lze jej vkládat pomocí syntaxe Markdownu.
| Omezení | Ne | Jednu nebo více vlastností, které slouží k přizpůsobení chování ověřování elementu. Podporované vlastnosti pro omezení se liší podle typu elementu. Některé typy element nepodporuje přizpůsobení chování ověření a proto mít vlastnost žádné omezení. |
| Možnosti | Ne | Další vlastnosti, které přizpůsobují chování elementu. Podobně jako `constraints`, podporovaných vlastností se liší podle typu elementu. |
| Viditelné | Ne | Označuje, zda se zobrazí elementu. Pokud `true`, se zobrazí elementu a příslušné podřízené elementy. Výchozí hodnota je `true`. Použití [logické funkce](create-uidefinition-functions.md#logical-functions) dynamicky řídit hodnota této vlastnosti.

## <a name="elements"></a>Elementy

V dokumentaci pro každý prvek obsahuje ukázku uživatelského rozhraní, schéma, remarks na chování elementu (obvykle týkají se ověření a podporované přizpůsobení) a ukázkový výstup.

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
- [Microsoft.Network.AvailabilityZoneDropDown](microsoft-network-availabilityzonedropdown.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Další postup
Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).

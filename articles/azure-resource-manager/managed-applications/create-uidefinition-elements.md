---
title: Vytvoření prvků definice uživatelského rozhraní
description: Popisuje prvky, které se mají použít při vytváření definic uživatelského rozhraní pro portál Azure.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 12bf5d131001d95a3f4327f95c24125dbf3fb510
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086705"
---
# <a name="createuidefinition-elements"></a>Elementy CreateUiDefinition

Tento článek popisuje schéma a vlastnosti pro všechny podporované prvky CreateUiDefinition. 

## <a name="schema"></a>Schéma

Schéma pro většinu prvků je následující:

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
| jméno | Ano | Vnitřní identifikátor odkazovat na konkrétní instanci prvku. Nejběžnější použití názvu prvku je `outputs`v , kde jsou výstupní hodnoty zadaných prvků mapovány na parametry šablony. Můžete také použít k vazbě výstupní hodnotu `defaultValue` prvku na jiný prvek. |
| type | Ano | Ovládací prvek ui k vykreslení pro prvek. Seznam podporovaných typů naleznete v tématu [Elements](#elements). |
| label | Ano | Zobrazit text prvku. Některé typy prvků obsahují více popisků, takže hodnota může být objekt obsahující více řetězců. |
| Defaultvalue | Ne | Výchozí hodnota prvku. Některé typy prvků podporují složité výchozí hodnoty, takže hodnota může být objekt. |
| Popis | Ne | Text, který se má zobrazit v špičce nástroje prvku. Podobně `label`jako některé prvky podporují více řetězců tip nástroje. Vložené odkazy lze vložit pomocí syntaxe Markdown.
| Omezení | Ne | Jedna nebo více vlastností, které se používají k přizpůsobení chování ověření prvku. Podporované vlastnosti omezení se liší podle typu prvku. Některé typy prvků nepodporují vlastní nastavení chování ověření a proto nemají vlastnost omezení. |
| možnosti | Ne | Další vlastnosti, které přizpůsobují chování prvku. Podobně `constraints`jako podporované vlastnosti se liší podle typu prvku. |
| Viditelné | Ne | Označuje, zda je prvek zobrazen. Pokud `true`se zobrazí prvek a příslušné podřízené prvky. Výchozí hodnota je `true`. Pomocí [logických funkcí](create-uidefinition-functions.md#logical-functions) můžete dynamicky řídit hodnotu této vlastnosti.

## <a name="elements"></a>Elementy

Dokumentace pro každý prvek obsahuje ukázku uživatelského rozhraní, schéma, poznámky k chování prvku (obvykle týkající se ověření a podporované přizpůsobení) a ukázkový výstup.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft.Common.InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft.Common.TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft.Common.TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft.ManagedIdentity.IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Další kroky

Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).

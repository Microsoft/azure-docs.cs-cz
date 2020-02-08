---
title: Vytvořit prvky definice uživatelského rozhraní
description: Popisuje prvky, které mají být použity při vytváření definic uživatelského rozhraní pro Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: 12bf5d131001d95a3f4327f95c24125dbf3fb510
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2020
ms.locfileid: "77086705"
---
# <a name="createuidefinition-elements"></a>CreateUiDefinition elementy

Tento článek popisuje schéma a vlastnosti všech podporovaných prvků CreateUiDefinition. 

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

| Vlastnost | Požadováno | Popis |
| -------- | -------- | ----------- |
| jméno | Ano | Vnitřní identifikátor, který odkazuje na konkrétní instanci elementu. Nejběžnější použití názvu elementu je v `outputs`, kde jsou výstupní hodnoty určených prvků mapovány na parametry šablony. Můžete ji také použít k navázání výstupní hodnoty prvku na `defaultValue` jiného prvku. |
| type | Ano | Ovládací prvek uživatelského rozhraní pro vykreslení elementu. Seznam podporovaných typů naleznete v tématu [elementy](#elements). |
| label | Ano | Zobrazený text elementu Některé typy prvků obsahují více popisků, takže hodnota může být objekt obsahující více řetězců. |
| defaultValue | Ne | Výchozí hodnota elementu. Některé typy prvků podporují komplexní výchozí hodnoty, takže hodnota může být objekt. |
| Okna | Ne | Text, který se má zobrazit v popisku nástroje elementu. Podobně jako u `label`některé prvky podporují více řetězců tipů nástroje. Vložené odkazy mohou být vloženy pomocí syntaxe Markdownu.
| constraints | Ne | Jedna nebo více vlastností, které jsou použity k přizpůsobení chování ověřování prvku. Podporované vlastnosti pro omezení se liší podle typu elementu. Některé typy prvků nepodporují přizpůsobení chování ověřování, a proto nemají žádnou vlastnost omezení. |
| options | Ne | Další vlastnosti, které přizpůsobují chování prvku. Podobně jako u `constraints`se podporované vlastnosti liší podle typu prvku. |
| visible | Ne | Určuje, zda je prvek zobrazen. Pokud `true`, zobrazí se element a příslušné podřízené prvky. Výchozí hodnota je `true`. K dynamickému řízení hodnoty této vlastnosti použijte [logické funkce](create-uidefinition-functions.md#logical-functions) .

## <a name="elements"></a>Elementy

Dokumentace pro každý prvek obsahuje ukázku uživatelského rozhraní, schéma, poznámky k chování elementu (obvykle týkající se ověřování a podporovaného přizpůsobení) a ukázkový výstup.

- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft. Common. InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft. Common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft. Common. TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft. ManagedIdentity. IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)

## <a name="next-steps"></a>Další kroky

Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).

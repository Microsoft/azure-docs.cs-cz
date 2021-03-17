---
title: Vytvořit prvky definice uživatelského rozhraní
description: Popisuje prvky, které mají být použity při vytváření definic uživatelského rozhraní pro Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 3e8bdd85c112d90fd5e10f6e65e48ca6b3f71b6e
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94410738"
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

| Vlastnost | Požaduje se | Popis |
| -------- | -------- | ----------- |
| name | Ano | Vnitřní identifikátor, který odkazuje na konkrétní instanci elementu. Nejběžnější použití názvu elementu je v `outputs` , kde jsou výstupní hodnoty určených prvků namapovány na parametry šablony. Můžete ji také použít k vytvoření vazby výstupní hodnoty prvku na `defaultValue` jiný prvek. |
| typ | Ano | Ovládací prvek uživatelského rozhraní pro vykreslení elementu. Seznam podporovaných typů naleznete v tématu [elementy](#elements). |
| label | Ano | Zobrazený text elementu Některé typy prvků obsahují více popisků, takže hodnota může být objekt obsahující více řetězců. |
| Hodnot | Ne | Výchozí hodnota elementu. Některé typy prvků podporují komplexní výchozí hodnoty, takže hodnota může být objekt. |
| Okna | Ne | Text, který se má zobrazit v popisku nástroje elementu. Podobně jako `label` některé prvky podporují více řetězců tipů nástroje. Vložené odkazy mohou být vloženy pomocí syntaxe Markdownu.
| jednotlivým | Ne | Jedna nebo více vlastností, které jsou použity k přizpůsobení chování ověřování prvku. Podporované vlastnosti pro omezení se liší podle typu elementu. Některé typy prvků nepodporují přizpůsobení chování ověřování, a proto nemají žádnou vlastnost omezení. |
| možnosti | Ne | Další vlastnosti, které přizpůsobují chování prvku. Podobně jako `constraints` se podporované vlastnosti liší podle typu prvku. |
| zobrazeny | Ne | Určuje, zda je prvek zobrazen. Pokud se `true` zobrazí element a příslušné podřízené prvky. Výchozí hodnota je `true`. K dynamickému řízení hodnoty této vlastnosti použijte [logické funkce](create-uidefinition-functions.md#logical-functions) .

## <a name="elements"></a>Elementy

Dokumentace pro každý prvek obsahuje ukázku uživatelského rozhraní, schéma, poznámky k chování elementu (obvykle týkající se ověřování a podporovaného přizpůsobení) a ukázkový výstup.

- [Microsoft. Common. CheckBox](microsoft-common-checkbox.md)
- [Microsoft.Common.DropDown](microsoft-common-dropdown.md)
- [Microsoft. Common. EditableGrid](microsoft-common-editablegrid.md)
- [Microsoft.Common.FileUpload](microsoft-common-fileupload.md)
- [Microsoft. Common. InfoBox](microsoft-common-infobox.md)
- [Microsoft.Common.OptionsGroup](microsoft-common-optionsgroup.md)
- [Microsoft.Common.PasswordBox](microsoft-common-passwordbox.md)
- [Microsoft.Common.Section](microsoft-common-section.md)
- [Microsoft. Common. ServicePrincipalSelector](microsoft-common-serviceprincipalselector.md)
- [Microsoft. Common. Slider](microsoft-common-slider.md)
- [Microsoft. Common. TagsByResource](microsoft-common-tagsbyresource.md)
- [Microsoft. Common. TextBlock](microsoft-common-textblock.md)
- [Microsoft.Common.TextBox](microsoft-common-textbox.md)
- [Microsoft.Compute.CredentialsCombo](microsoft-compute-credentialscombo.md)
- [Microsoft.Compute.SizeSelector](microsoft-compute-sizeselector.md)
- [Microsoft.Compute.UserNameTextBox](microsoft-compute-usernametextbox.md)
- [Microsoft. klíčů trezor. KeyVaultCertificateSelector](microsoft-keyvault-keyvaultcertificateselector.md)
- [Microsoft. ManagedIdentity. IdentitySelector](microsoft-managedidentity-identityselector.md)
- [Microsoft.Network.PublicIpAddressCombo](microsoft-network-publicipaddresscombo.md)
- [Microsoft.Network.VirtualNetworkCombo](microsoft-network-virtualnetworkcombo.md)
- [Microsoft. Solutions. ArmApiControl](microsoft-solutions-armapicontrol.md)
- [Microsoft. Solutions. ResourceSelector](microsoft-solutions-resourceselector.md)
- [Microsoft.Storage.MultiStorageAccountCombo](microsoft-storage-multistorageaccountcombo.md)
- [Microsoft.Storage.StorageAccountSelector](microsoft-storage-storageaccountselector.md)
- [Microsoft. Storage. StorageBlobSelector](microsoft-storage-storageblobselector.md)

## <a name="next-steps"></a>Další kroky

Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).

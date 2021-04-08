---
title: StorageBlobSelector – element uživatelského rozhraní
description: Popisuje prvek uživatelského rozhraní Microsoft. Storage. StorageBlobSelector pro Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754420"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Microsoft. Storage. StorageBlobSelector – element uživatelského rozhraní

Ovládací prvek pro výběr objektu BLOB z účtu služby Azure Storage.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

Uživatel se zobrazí s možností vyhledat dostupné objekty blob úložiště.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft. Storage. StorageBlobSelector – Procházet":::

Po výběru možnosti **Procházet** může uživatel vybrat účet úložiště.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft. Storage. StorageBlobSelector – výběr úložiště":::

Uživatel uvidí kontejnery v účtu úložiště a může je vybrat.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft. Storage. StorageBlobSelector – výběr kontejneru":::

V kontejneru může uživatel vybrat soubor.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft. Storage. StorageBlobSelector – soubor":::

Ovládací prvek se aktualizuje tak, aby zobrazoval vybraný název souboru.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft. Storage. StorageBlobSelector – zobrazit výběrový soubor":::

## <a name="schema"></a>Schéma

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>Ukázkový výstup

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>Poznámky

Vlastnost **omezení. allowedFileExtensions** určuje povolené typy souborů.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).

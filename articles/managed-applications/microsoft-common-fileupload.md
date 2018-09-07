---
title: Azure prvek uživatelského rozhraní FileUpload | Dokumentace Microsoftu
description: Popisuje element Microsoft.Common.FileUpload uživatelského rozhraní pro Azure portal.
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
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 2886dbafe6bf20718f4e3cd2976764fc432dbb04
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2018
ms.locfileid: "44021748"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Prvek uživatelského rozhraní Microsoft.Common.FileUpload
Ovládací prvek, který umožňuje uživateli zadat jeden nebo více souborů k nahrání.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>Schéma
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>Poznámky
- `constraints.accept` Určuje typy souborů, které se zobrazí v dialogovém okně soubor prohlížeče. Zobrazit [specifikace HTML5](http://www.w3.org/TR/html5/forms.html#attr-input-accept) pro povolené hodnoty. Výchozí hodnota je **null**.
- Pokud `options.multiple` je nastavena na **true**, uživatel může vybrat více než jeden soubor v dialogovém okně soubor prohlížeče. Výchozí hodnota je **false**.
- Tento prvek podporuje nahrávání souborů ve dvou režimech na základě hodnoty z `options.uploadMode`. Pokud **souboru** není zadána, výstup obsahuje obsah souboru jako objekt blob. Pokud **url** není zadán, pak se soubor nahraje do dočasného umístění a výstup obsahuje adresu URL objektu blob. Dočasné objekty BLOB bude vyprázdněno po 24 hodinách. Výchozí hodnota je **souboru**.
- Nahraný soubor je chráněný. Zahrnuje výstupní adresy URL [SAS token](../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pro přístup k souboru během nasazení.
- Hodnota `options.openMode` Určuje, jak číst soubor. Pokud soubor má být prostý text, zadejte **text**; v opačném, zadejte **binární**. Výchozí hodnota je **text**.
- Pokud `options.uploadMode` je nastavena na **souboru** a `options.openMode` je nastavena na **binární**, výstup je s kódováním base64.
- `options.encoding` Určuje kódování určené k použití při čtení souboru. Výchozí hodnota je **UTF-8**a používá se pouze tehdy, když `options.openMode` je nastavena na **text**.

## <a name="sample-output"></a>Ukázkový výstup
Pokud má hodnotu false options.multiple a options.uploadMode je soubor, výstup obsahuje obsah souboru jako řetězec formátu JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Pokud platí options.multiple and'options.uploadMode je soubor, pak výstup má obsah souborů jako pole JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Pokud má hodnotu false options.multiple a options.uploadMode je adresa url, výstup má adresu URL jako řetězec formátu JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Pokud je true options.multiple a options.uploadMode je adresa url, výstup obsahuje seznam adres URL jako pole JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Při testování CreateUiDefinition, zkrátit některé prohlížeče (například Google Chrome) adresy URL vygenerovaném daným elementem Microsoft.Common.FileUpload v konzole prohlížeče. Budete muset klikněte pravým tlačítkem na jednotlivé odkazy pro kopírování úplných adres URL.


## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, naleznete v tématu [Začínáme s funkcí CreateUiDefinition](create-uidefinition-overview.md).
* Popis společné vlastnosti v prvcích uživatelského rozhraní, naleznete v tématu [CreateUiDefinition prvky](create-uidefinition-elements.md).

---
title: Azure elementu odesílání souborů při odpovědích uživatelského rozhraní | Microsoft Docs
description: Popisuje element Microsoft.Common.FileUpload uživatelského rozhraní pro portál Azure.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2018
ms.author: tomfitz
ms.openlocfilehash: bfde11a189348472942248a6b90fd5ef1b5a1c89
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="microsoftcommonfileupload-ui-element"></a>Element Microsoft.Common.FileUpload uživatelského rozhraní
Ovládací prvek, který umožňuje uživateli zadat jeden nebo více souborů, které chcete nahrát.

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
- `constraints.accept` Určuje typy souborů, které se zobrazují v dialogovém okně prohlížeče souboru. Najdete v článku [specifikace HTML5](http://www.w3.org/TR/html5/forms.html#attr-input-accept) pro povolené hodnoty. Výchozí hodnota je **null**.
- Pokud `options.multiple` je nastaven na **true**, uživatel může vybrat více než jeden soubor v dialogovém okně prohlížeče souboru. Výchozí hodnota je **false**.
- Tento element podporuje odesílání souborů ve dvou režimech na základě hodnoty z `options.uploadMode`. Pokud **soubor** není zadaný, výstup obsahuje obsah souboru jako objekt blob. Pokud **url** je určeno, soubor je odesílán do dočasného umístění a výstup obsahuje adresu URL objektu blob. Dočasné objekty BLOB se vyprázdní po 24 hodinách. Výchozí hodnota je **soubor**.
- Hodnota `options.openMode` Určuje, jak je soubor pro čtení. Pokud soubor je očekáván prostý text, zadejte **text**; v opačném, zadejte **binární**. Výchozí hodnota je **text**.
- Pokud `options.uploadMode` je nastaven na **soubor** a `options.openMode` je nastaven na **binární**, výstup se kódování base64.
- `options.encoding` Určuje kódování určené k použití při čtení souboru. Výchozí hodnota je **UTF-8**a používá se pouze tehdy, když `options.openMode` je nastaven na **text**.

## <a name="sample-output"></a>Ukázkový výstup
Pokud je hodnota false options.multiple a options.uploadMode je soubor, výstup obsahuje obsah souboru jako řetězec formátu JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Pokud platí options.multiple and'options.uploadMode je soubor, pak výstup obsahuje obsah souborů jako pole JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Pokud je hodnota false options.multiple a options.uploadMode je adresa url, výstup obsahuje adresu URL jako řetězec formátu JSON:

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

Při testování CreateUiDefinition, zkrátit některé prohlížeče (například Google Chrome) adresy URL vygenerovaných elementem Microsoft.Common.FileUpload v konzole prohlížeče. Budete muset klikněte pravým tlačítkem na jednotlivé odkazy na kopírování úplné adresy URL.


## <a name="next-steps"></a>Další postup
* Úvod do vytváření definic uživatelského rozhraní, najdete v části [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis společných vlastností v prvky uživatelského rozhraní najdete v tématu [CreateUiDefinition elementy](create-uidefinition-elements.md).

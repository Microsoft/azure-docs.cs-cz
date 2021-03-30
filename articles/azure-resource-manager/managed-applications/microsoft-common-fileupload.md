---
title: Element uživatelského rozhraní pro nahrání souborů
description: Popisuje prvek uživatelského rozhraní Microsoft. Common. upload pro Azure Portal. Umožňuje uživatelům při nasazování spravované aplikace nahrávat soubory.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: c4eb3972be85ac7e291380f64127c20424b0d0f0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87004272"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Microsoft. Common. upload – element uživatelského rozhraní

Ovládací prvek, který umožňuje uživateli určit jeden nebo více souborů k nahrání.

## <a name="ui-sample"></a>Ukázka uživatelského rozhraní

![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft-common-fileupload.png)

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

## <a name="sample-output"></a>Ukázkový výstup

Pokud options. Multiple je false a Options. uploadMode je soubor, pak výstup obsahuje obsah souboru jako řetězec JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Pokud options. Multiple je true and'options. uploadMode je soubor, pak výstup obsahuje obsah souborů jako pole JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Pokud options. Multiple je false a Options. uploadMode je adresa URL, pak výstup má adresu URL jako řetězec JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Pokud options. Multiple je true a Options. uploadMode je adresa URL, pak výstup obsahuje seznam adres URL jako pole JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Při testování CreateUiDefinition některé prohlížeče (jako Google Chrome) zkrátí adresy URL generované elementem Microsoft. Common. upload v konzole prohlížeče. Je možné, že budete muset kliknout pravým tlačítkem na jednotlivé odkazy a zkopírovat tak úplné adresy URL.

## <a name="remarks"></a>Poznámky

- `constraints.accept` Určuje typy souborů, které se zobrazí v dialogovém okně souboru prohlížeče. Povolené hodnoty najdete v tématu [specifikace HTML5](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) . Výchozí hodnota je **null**.
- Pokud `options.multiple` je nastaveno na **hodnotu true**, může uživatel vybrat více než jeden soubor v dialogovém okně souboru prohlížeče. Výchozí hodnota je **false** (nepravda).
- Tento prvek podporuje nahrávání souborů ve dvou režimech na základě hodnoty `options.uploadMode` . Je-li zadán **soubor** , výstup obsahuje obsah souboru jako objekt BLOB. Pokud zadáte **adresu URL** , soubor se nahraje do dočasného umístění a výstup má adresu URL objektu BLOB. Dočasné objekty blob budou vymazány po 24 hodinách. Výchozí hodnota je **File (soubor**).
- Nahraný soubor je chráněný. Výstupní adresa URL obsahuje [token SAS](../../storage/common/storage-sas-overview.md?toc=/azure/storage/blobs/toc.json) pro přístup k souboru během nasazování.
- Hodnota určuje, `options.openMode` jak je soubor čten. Pokud by měl být soubor prostým textem, zadejte **text**; v opačném případě zadejte **Binary**. Výchozí hodnota je **text**.
- Pokud `options.uploadMode` je nastavená na **soubor** a `options.openMode` je nastavená na **Binary**, výstup je kódovaný v kódování Base64.
- `options.encoding` Určuje kódování, které má být použito při čtení souboru. Výchozí hodnota je **UTF-8** a používá se jenom v případě, že `options.openMode` je nastavená na **text**.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic uživatelského rozhraní najdete v tématu [Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v tématu [CreateUiDefinition Elements](create-uidefinition-elements.md).

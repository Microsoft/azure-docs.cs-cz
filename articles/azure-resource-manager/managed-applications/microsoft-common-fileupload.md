---
title: Element ui fileupload
description: Popisuje prvek ui rozhraní Microsoft.Common.FileUpload pro portál Azure. Umožňuje uživatelům odesílat soubory při nasazování spravované aplikace.
author: tfitzmac
ms.topic: conceptual
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 61e1c9fe07fdd29ebc00e7e3491472d073bc4e5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652486"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Element ui rozhraní Microsoft.Common.FileUpload

Ovládací prvek, který umožňuje uživateli zadat jeden nebo více souborů k nahrání.

## <a name="ui-sample"></a>Ukázka ui

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

## <a name="sample-output"></a>Ukázkový výstup

Pokud options.multiple je false a options.uploadMode je soubor, pak výstup má obsah souboru jako řetězec JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Pokud options.multiple je true and'options.uploadMode je soubor, pak výstup má obsah souborů jako pole JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Pokud options.multiple je false a options.uploadMode je url, pak výstup má adresu URL jako řetězec JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Pokud options.multiple je true a options.uploadMode je url, pak výstup má seznam adres URL jako pole JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Při testování createuidefinition některé prohlížeče (například Google Chrome) zkrátit adresy URL generované Microsoft.Common.FileUpload prvek v konzole prohlížeče. Chcete-li zkopírovat úplné adresy URL, může být nutné klepnout pravým tlačítkem myši na jednotlivé odkazy.

## <a name="remarks"></a>Poznámky

- `constraints.accept`určuje typy souborů, které jsou zobrazeny v dialogovém okně souboru prohlížeče. Povolené hodnoty naleznete ve [specifikaci HTML5.](https://html.spec.whatwg.org/multipage/input.html#attr-input-accept) Výchozí hodnota je **null**.
- Pokud `options.multiple` je nastavena hodnota **true**, uživatel může vybrat více než jeden soubor v dialogovém okně souboru prohlížeče. Výchozí hodnota je **false** (nepravda).
- Tento prvek podporuje nahrávání souborů ve dvou `options.uploadMode`režimech na základě hodnoty aplikace . Pokud je **zadán soubor,** výstup má obsah souboru jako objekt blob. Pokud je **zadána adresa URL,** soubor se nahraje do dočasného umístění a výstup má adresu URL objektu blob. Dočasné objekty BLOB budou po 24 hodinách vymazány. Výchozí hodnota je **soubor**.
- Nahraný soubor je chráněn. Výstupní adresa URL obsahuje [token SAS](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) pro přístup k souboru během nasazení.
- Hodnota určuje `options.openMode` způsob čtení souboru. Pokud se očekává, že soubor bude prostý montovna, zadejte **text**; jinak, zadejte **binární**. Výchozí hodnota je **text**.
- Pokud `options.uploadMode` je **file** nastavena `options.openMode` na soubor a je nastavena na **binární**, výstup je base64-kódované.
- `options.encoding`určuje kódování, které se má použít při čtení souboru. Výchozí hodnota je **UTF-8**a používá `options.openMode` se pouze v případě, že je nastavena na **text**.

## <a name="next-steps"></a>Další kroky

* Úvod k vytváření definic ui naleznete [v tématu Začínáme s CreateUiDefinition](create-uidefinition-overview.md).
* Popis běžných vlastností v prvcích uživatelského rozhraní naleznete v [tématu CreateUiDefinition elements](create-uidefinition-elements.md).

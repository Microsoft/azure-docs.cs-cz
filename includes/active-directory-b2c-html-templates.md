---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189007"
---
## <a name="sample-templates"></a>Ukázkové šablony
Ukázkové šablony pro přizpůsobení uživatelského rozhraní najdete zde:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Tento projekt obsahuje následující šablony:
- [Oceán modrá](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Břidlice šedá](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Použití vzorku:

1. Naklonujte repo na místní počítač. Zvolte složku `/ocean_blue` `/slate_gray`šablony nebo .
1. Nahrajte všechny soubory ve složce šablony a `/assets` do složky blob do úložiště objektů Blob, jak je popsáno v předchozích částech.
1. Dále otevřete `\*.html` každý soubor v `/ocean_blue` `/slate_gray`kořenovém adresáři buď nebo , nahraďte všechny instance relativních adres URL adresurl souborů css, obrázků a písem, které jste nahráli v kroku 2. Například:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Akce
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Uložte `\*.html` soubory a nahrajte je do úložiště objektů Blob.
1. Nyní upravte zásadu a přesměrujte na soubor HTML, jak již bylo zmíněno dříve.
1. Pokud se vám zobrazují chybějící písma, obrázky nebo css, zkontrolujte odkazy v zásadách rozšíření a v souborech \*HTML.

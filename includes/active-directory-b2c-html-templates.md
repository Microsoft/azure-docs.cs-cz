---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: mimart
ms.openlocfilehash: d43b879057001d62ea72bd2e011ad52957d47470
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78189007"
---
## <a name="sample-templates"></a>Ukázkové šablony
Ukázkové šablony pro přizpůsobení uživatelského rozhraní můžete najít tady:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

Tento projekt obsahuje následující šablony:
- [Mořská modrá](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Břidlicová šedá](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Postup použití ukázky:

1. Naklonujte úložiště na místním počítači. Vyberte složku šablony `/ocean_blue` nebo `/slate_gray` .
1. Nahrajte všechny soubory ve složce šablony a `/assets` složce do úložiště objektů blob, jak je popsáno v předchozích částech.
1. Potom otevřete každý `\*.html` soubor v kořenovém adresáři `/ocean_blue` nebo `/slate_gray` nahraďte všechny výskyty relativních adres URL adresami URL souborů CSS, obrázků a písem, které jste nahráli v kroku 2. Například:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    Záměr
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Uložte `\*.html` soubory a nahrajte je do úložiště objektů BLOB.
1. Nyní upravte zásady tak, aby odkazovaly na soubor HTML, jak bylo uvedeno dříve.
1. Pokud se zobrazí chybějící písma, obrázky nebo šablony stylů CSS, zkontrolujte odkazy v zásadách rozšíření a v \* souborech. html.

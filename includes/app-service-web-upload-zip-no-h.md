---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 10/24/2018
ms.author: cephalin
ms.openlocfilehash: e4bc749047bbf0d55fc60a699ac956421775d5b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60710013"
---
V [webu Azure portal](https://portal.azure.com), klikněte na tlačítko **skupiny prostředků** > **cloud-shell-úložiště –\<your_region >**  >   **\<název_účtu_úložiště >**.

![Najít účet úložiště Cloud Shell](../articles/app-service/media/app-service-deploy-zip/upload-choose-storage-account.png)

V **přehled** stránce účtu úložiště vyberte **soubory**.

Vyberte sdílenou složku automaticky vygenerovaný soubor a vyberte **nahrát**. V Cloud Shellu, jako je připojený tuto sdílenou složku souborového `clouddrive`.

![Najít tlačítko Nahrát](../articles/app-service/media/app-service-deploy-zip/upload-select-button.png)

Klepněte na volič soubor a vyberte soubor ZIP a potom klikněte na tlačítko **nahrát**. 

Ve službě Cloud Shell pomocí `ls` k ověření, že vidíte ve výchozích nahraný soubor ZIP `clouddrive` sdílet.

```azurecli-interactive
ls clouddrive
```

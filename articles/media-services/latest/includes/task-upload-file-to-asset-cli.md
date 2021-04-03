---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: affd55d65ed8454ebfcdf14f697849badf8e5d3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88658404"
---
<!-- ### Upload files with the CLI -->

Následující příkaz nahraje jeden soubor.  

Změňte následující hodnoty:

1. Přejděte `/path/to/file` na místní cestu k souboru.  
1. Přejděte `MyContainer` na prostředek, který jste dříve vytvořili pomocí ID assetu (nikoli názvu).
1. Změňte `MyBlob` název, který chcete použít pro nahraný soubor.
1. Přejděte `MyStorageAccountName` na název účtu úložiště, který používáte.
1. Přejděte `MyStorageAccountKey` do přístupového klíče pro váš účet úložiště.

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob --acount-name MyStorageAccountName --account-key MyStorageAccountKey
    ```

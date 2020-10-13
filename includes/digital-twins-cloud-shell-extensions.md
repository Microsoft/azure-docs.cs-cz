---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – nastavení nejnovějšího rozšíření IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 7e43ab6d419d6a08973864838c66aef0941d74f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90606524"
---
Nejdřív spuštěním tohoto příkazu zobrazíte seznam všech rozšíření, která jste už nainstalovali.

```azurecli
az extension list
```

Výstupem je pole všech rozšíření, která máte aktuálně k dispozici. Vyhledejte `"name"` pole pro každou položku seznamu, abyste viděli názvy rozšíření.

Pomocí výstupu určete, které z následujících příkazů se má spustit pro instalaci rozšíření (můžete spustit více než jeden).
* Pokud seznam obsahuje `azure-iot` : rozšíření již máte. Spusťte tento příkaz, abyste se ujistili, že máte nejnovější aktualizaci a že nejsou k dispozici žádné další aktualizace:

   ```azurecli
   az extension update --name azure-iot
   ```

* Pokud **seznam neobsahuje** `azure-iot` : bude nutné nainstalovat rozšíření. Použijte tento příkaz:

    ```azurecli
    az extension add --name azure-iot
    ```

* Pokud seznam obsahuje `azure-iot-cli-ext` : Jedná se o starší verzi rozšíření. Současně by měla být nainstalována pouze jedna verze rozšíření, proto byste měli odinstalovat starší verzi rozšíření. Použijte tento příkaz:

   ```azurecli
   az extension remove --name azure-cli-iot-ext
   ```
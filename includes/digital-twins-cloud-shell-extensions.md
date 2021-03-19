---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – nastavení nejnovějšího rozšíření IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 984739a728f6ac5e28eeb561e0d7b6ec0485ca13
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90606524"
---
Nejdřív spuštěním tohoto příkazu zobrazíte seznam všech rozšíření, která jste už nainstalovali.

```azurecli-interactive
az extension list
```

Výstupem je pole všech rozšíření, která máte aktuálně k dispozici. Vyhledejte `"name"` pole pro každou položku seznamu, abyste viděli názvy rozšíření.

Pomocí výstupu určete, které z následujících příkazů se má spustit pro instalaci rozšíření (můžete spustit více než jeden).
* Pokud seznam obsahuje `azure-iot` : rozšíření již máte. Spusťte tento příkaz, abyste se ujistili, že máte nejnovější aktualizaci a že nejsou k dispozici žádné další aktualizace:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Pokud **seznam neobsahuje** `azure-iot` : bude nutné nainstalovat rozšíření. Použijte tento příkaz:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Pokud seznam obsahuje `azure-iot-cli-ext` : Jedná se o starší verzi rozšíření. Současně by měla být nainstalována pouze jedna verze rozšíření, proto byste měli odinstalovat starší verzi rozšíření. Použijte tento příkaz:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```
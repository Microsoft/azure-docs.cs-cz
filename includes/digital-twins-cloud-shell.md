---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – nastavení Cloud Shell a rozšíření IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b7c91d648c06970d53799c6ff505919dea17b3c0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87032203"
---
Aby bylo možné začít pracovat s digitálními událostmi Azure v otevřeném [Azure Cloud Shell](https://shell.azure.com) , první věc, kterou je třeba provést, se přihlaste a nastavte kontext prostředí na vaše předplatné pro tuto relaci. Spusťte tyto příkazy v Cloud Shell:

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Místo ID ve výše uvedeném příkazu můžete také použít svůj název vašeho předplatného. 

Pokud jste toto předplatné použili u digitálních vláken Azure jako první, spusťte tento příkaz a zaregistrujte se do oboru názvů digitálních vláken Azure. (Pokud si nejste jistí, můžete ji znovu spustit i v případě, že jste to udělali za nějakou dobu v minulosti.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Dále přidáte [**Microsoft Azure rozšíření IoT pro Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) do vašeho Cloud Shell, abyste povolili příkazy pro interakci s digitálními interakcemi Azure a dalšími službami IoT. 

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

Nyní jste připraveni pracovat s digitálními úkoly Azure v Cloud Shell.

Můžete to ověřit tak, že je spustíte kdykoli `az dt -h` a zobrazí se seznam dostupných příkazů digitálního vlákna Azure na nejvyšší úrovni.
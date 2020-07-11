---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – nastavení Cloud Shell a rozšíření IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 6f472865c131b873f1ae0a21fa9ec55865fb2b29
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277729"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Nastavit relaci Cloud Shell

Po otevření okna Cloud Shell se první věc přihlaste a nastavte kontext prostředí pro tuto relaci na své předplatné. Spusťte tyto příkazy v Cloud Shell:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```
> [!TIP]
> Předplatné můžete také nastavit pomocí názvu svého předplatného. Použijte tento příkaz: 
> ```azurecli
> az account set --subscription "your-Azure-subscription-name"
> 
Pokud jste toto předplatné použili u digitálních vláken Azure jako první, spusťte tento příkaz a zaregistrujte se do oboru názvů digitálních vláken Azure. (Pokud si nejste jistí, můžete ji znovu spustit i v případě, že jste to udělali za nějakou dobu v minulosti.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Dále přidáte [**Microsoft Azure rozšíření IoT pro Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) do vašeho Cloud Shell, abyste povolili příkazy pro interakci s digitálními interakcemi Azure a dalšími službami IoT. 

Nejdřív spuštěním tohoto příkazu zobrazíte seznam všech rozšíření, která jste už nainstalovali.

```azurecli-interactive
az extension list
```

Ve výstupu vyhledejte `"name"` pole pro každou položku seznamu, abyste viděli názvy rozšíření.

Pomocí výstupu určete, které z následujících příkazů se má spustit pro instalaci rozšíření (můžete spustit více než jeden).
* Pokud seznam obsahuje `azure-iot` : rozšíření již máte. Spusťte tento příkaz, abyste se ujistili, že máte nejnovější aktualizaci:

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
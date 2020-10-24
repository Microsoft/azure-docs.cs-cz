---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – nastavení Cloud Shell a rozšíření IoT
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 8a3efc9ba8fc8ffd8c0eca4340e1948c388c0a13
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494960"
---
Aby bylo možné začít pracovat s digitálními událostmi Azure v otevřeném [Azure Cloud Shell](https://shell.azure.com) , první věc, kterou je třeba provést, se přihlaste a nastavte kontext prostředí na vaše předplatné pro tuto relaci. Spusťte tyto příkazy v Cloud Shell:

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Místo ID ve výše uvedeném příkazu můžete také použít svůj název vašeho předplatného. 

Pokud jste toto předplatné použili u digitálních vláken Azure jako první, spusťte tento příkaz a zaregistrujte se do oboru názvů digitálních vláken Azure. (Pokud si nejste jistí, můžete ji znovu spustit i v případě, že jste to udělali za nějakou dobu v minulosti.)

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

Dále přidáte [**Microsoft Azure rozšíření IoT pro Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest&preserve-view=true) do vašeho Cloud Shell, abyste povolili příkazy pro interakci s digitálními interakcemi Azure a dalšími službami IoT. 

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

Nyní jste připraveni pracovat s digitálními úkoly Azure v Cloud Shell.

Můžete to ověřit tak, že je spustíte kdykoli `az dt -h` a zobrazí se seznam dostupných příkazů digitálního vlákna Azure na nejvyšší úrovni.
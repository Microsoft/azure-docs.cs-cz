---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – nastavení Cloud Shell a rozšíření IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 76480959d94b9e2a2e04857c85b7359105e73e4f
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612891"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Nastavit relaci Cloud Shell

Po otevření okna Cloud Shell se první věc přihlaste a nastavte kontext prostředí pro tuto relaci na své předplatné. 

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Pokud jste toto předplatné použili u digitálních vláken Azure jako první, spusťte tento příkaz a zaregistrujte se do oboru názvů digitálních vláken Azure. (Pokud si nejste jistí, můžete ji znovu spustit i v případě, že jste to udělali za nějakou dobu v minulosti.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Potom spuštěním následujícího příkazu v instanci Cloud Shell přidejte rozšíření IoT Microsoft Azure pro rozhraní příkazového řádku Azure CLI.

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

> [!NOTE]
> Tento článek používá nejnovější verzi rozšíření Azure IoT, která se nazývá `azure-iot` . Je volána starší verze `azure-iot-cli-ext` . V jednom okamžiku byste měli mít nainstalovanou jenom jednu verzi. `az extension list`K ověření aktuálně nainstalovaných rozšíření můžete použít příkaz.
> Použijte `az extension remove --name azure-cli-iot-ext` k odebrání starší verze rozšíření.
> Použijte `az extension add --name azure-iot` k přidání nové verze rozšíření. Pokud chcete zjistit, jaká rozšíření jste nainstalovali, použijte `az extension list` .

> [!TIP]
> Můžete spustit, `az dt -h` aby se zobrazily příkazy digitálních vláken Azure na nejvyšší úrovni.
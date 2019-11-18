---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: e6ca830a7224326c17003924b76192b95ba6bcb2
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152009"
---
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v dalších článcích technologie Plug and Play IoT, můžete všechny prostředky, které jste použili v rámci tohoto rychlého startu, zachovat a znovu použít. Jinak můžete odstranit prostředky, které jste vytvořili v rámci tohoto rychlého startu, abyste se vyhnuli dalším poplatkům.

Pomocí tohoto příkazu pro rozhraní příkazového řádku Azure můžete odstranit jak centrum, tak i registrované zařízení najednou, a to tak, že odstraníte celou skupinu prostředků. (Nepoužívejte to ale v případě, že tyto prostředky sdílí skupinu prostředků s jinými prostředky, které máte k různým účelům.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Pokud chcete odstranit jenom službu IoT Hub, spusťte následující příkaz pomocí Azure CLI:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Pokud chcete odstranit jenom identitu zařízení, kterou jste zaregistrovali ve službě IoT Hub, spusťte následující příkaz pomocí Azure CLI:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Můžete taky chtít odebrat klonované soubory sady SDK z vývojového počítače.
---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453702"
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

Můžete taky chtít odebrat klonované ukázkové soubory z vývojového počítače.
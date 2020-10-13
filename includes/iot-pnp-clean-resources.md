---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 6cd983b6421fc821853aa22d4dc2c297f672d292
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87336887"
---
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v úmyslu pokračovat v dalších článcích technologie Plug and Play IoT, můžete všechny prostředky, které jste použili v tomto článku, zachovat a znovu použít. V opačném případě můžete odstranit prostředky, které jste vytvořili v tomto článku, abyste se vyhnuli dalším poplatkům.

Pomocí následujícího příkazu rozhraní příkazového řádku Azure můžete odstranit jak centrum, tak i registrované zařízení najednou. Tento příkaz nepoužívejte, pokud tyto prostředky sdílí skupinu prostředků s jinými prostředky, které chcete zachovat.

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

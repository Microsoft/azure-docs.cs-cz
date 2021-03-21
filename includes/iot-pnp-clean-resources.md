---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 647226091c3e15a19bf8262c23e62d95018488b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831195"
---
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

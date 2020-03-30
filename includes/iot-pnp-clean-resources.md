---
author: baanders
ms.author: baanders
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: d88d166e001e71cabdabc3a3d344adc4da19aa8c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "75453702"
---
## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat s dalšími články ioT Plug and Play, můžete zachovat a znovu použít prostředky použité v tomto rychlém startu. V opačném případě můžete odstranit prostředky, které jste vytvořili v tomto rychlém startu, abyste se vyhnuli dalším poplatkům.

Centrum i registrované zařízení můžete odstranit najednou odstraněním celé skupiny prostředků pomocí následujícího příkazu pro rozhraní příkazového příkazu Azure pro rozhraní příkazového příkazu Azure. (Nepoužívejte to však, pokud tyto prostředky sdílejí skupinu prostředků s jinými prostředky, které máte pro různé účely.)

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```
Chcete-li odstranit pouze centrum IoT, spusťte pomocí azure cli následující příkaz:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Pokud chcete odstranit jenom identitu zařízení, kterou jste zaregistrovali ve službě IoT hub, spusťte pomocí rozhraní příkazového příkazu Azure:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Můžete také odebrat klonované ukázkové soubory z vývojového počítače.
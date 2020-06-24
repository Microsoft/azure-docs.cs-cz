---
author: baanders
description: zahrnutí souboru pro digitální vlákna Azure – nastavení Cloud Shell a rozšíření IoT
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 4aa016294f0ef3bd26f7f3ef6fa374e9367b672d
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296963"
---
[!INCLUDE [cloud-shell-try-it.md](cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Nastavit relaci Cloud Shell

Po otevření okna Cloud Shell se první věc přihlaste a nastavte kontext prostředí pro tuto relaci na své předplatné. Spusťte tyto příkazy v Cloud Shell:

```azurecli
az login
az account set --subscription <your-Azure-subscription-ID>
```

Pokud jste toto předplatné použili u digitálních vláken Azure jako první, spusťte tento příkaz a zaregistrujte se do oboru názvů digitálních vláken Azure. (Pokud si nejste jistí, můžete ji znovu spustit i v případě, že jste to udělali za nějakou dobu v minulosti.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Dále přidáte [**Microsoft Azure rozšíření IoT pro Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) do vašeho Cloud Shell, abyste povolili příkazy pro interakci s digitálními interakcemi Azure a dalšími službami IoT. Pomocí tohoto příkazu přidejte rozšíření:

   ```azurecli-interactive
   az extension add --name azure-iot
   ```

Pokud jste rozšíření nainstalovali v minulosti, může výstup vyslovit "rozšíření" Azure-IoT "už je nainstalované." Pokud k tomu dojde, spusťte následující příkaz, abyste měli jistotu, že máte nejnovější aktualizaci: 

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

Nyní jste připraveni pracovat s digitálními úkoly Azure v Cloud Shell.

Můžete to ověřit tak, že je spustíte kdykoli `az dt -h` a zobrazí se seznam dostupných příkazů digitálního vlákna Azure na nejvyšší úrovni.
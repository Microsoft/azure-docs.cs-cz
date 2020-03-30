---
title: zahrnout soubor
description: zahrnout soubor
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a58e408feadd10e6dbc9d6878b82a4d045918ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68781439"
---
## <a name="access-the-virtual-machine"></a>Přístup k virtuálnímu počítači

Následující kroky používají Azure CLI v Azure Cloud Shell. Pokud chcete, můžete [nainstalovat azure cli](/cli/azure/install-azure-cli) na vývojovém počítači a spustit příkazy místně.

Následující kroky ukazují, jak nakonfigurovat virtuální počítač Azure tak, aby umožňoval přístup **SSH.** Uvedené kroky předpokládají, že název, který jste zvolili pro akcelerátor řešení, je **contoso-simulation** – nahraďte tuto hodnotu názvem nasazení:

1. Uveďte obsah skupiny prostředků, která obsahuje prostředky akcelerátoru řešení:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Poznamenejte si název virtuálního počítače, veřejnou IP adresu a skupinu zabezpečení sítě – tyto hodnoty budete potřebovat později.

1. Aktualizujte skupinu zabezpečení sítě, aby byl povolen přístup SSH. Následující příkaz předpokládá, že název skupiny zabezpečení sítě je **contoso-simulation-nsg** -- nahraďte tuto hodnotu názvem skupiny zabezpečení sítě:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Povolte přístup SSH pouze během testování a vývoje. Pokud povolíte SSH, [měli byste jej co nejdříve znovu zakázat](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Aktualizujte heslo pro **účet azureuser** ve virtuálním počítači na heslo, které znáte. Při spuštění následujícího příkazu zvolte vlastní heslo:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Najděte veřejnou IP adresu vašeho virtuálního počítače. Následující příkaz předpokládá, že název virtuálního počítače je **vm-vikxv** -- nahraďte tuto hodnotu názvem virtuálního počítače, který jste si dříve poznamenali:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Poznamenejte si veřejnou IP adresu vašeho virtuálního počítače.

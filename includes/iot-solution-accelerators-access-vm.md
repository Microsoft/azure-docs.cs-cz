---
title: zahrnout soubor
description: zahrnout soubor
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 817c41a969f03ad04d372c516a16ef6b770f3e18
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92755855"
---
## <a name="access-the-virtual-machine"></a>Přístup k virtuálnímu počítači

Následující kroky používají rozhraní příkazového řádku Azure v Azure Cloud Shell. Pokud chcete, můžete rozhraní příkazového [řádku Azure nainstalovat](/cli/azure/install-azure-cli) na svém vývojovém počítači a spustit příkazy místně.

Následující kroky ukazují, jak nakonfigurovat virtuální počítač Azure tak, aby povoloval přístup přes **SSH** . Uvedený postup předpokládá, že název, který jste zvolili pro akcelerátor řešení, je **Contoso-simulace** – nahraďte tuto hodnotu názvem vašeho nasazení:

1. Vypíše obsah skupiny prostředků, která obsahuje prostředky akcelerátoru řešení:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Poznamenejte si název virtuálního počítače, veřejnou IP adresu a skupinu zabezpečení sítě – tyto hodnoty budete potřebovat později.

1. Aktualizujte skupinu zabezpečení sítě tak, aby povolovala přístup přes SSH. Následující příkaz předpokládá, že název skupiny zabezpečení sítě je **Contoso-simulace-NSG** --nahraďte tuto hodnotu názvem vaší skupiny zabezpečení sítě:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Povolit přístup přes SSH jenom během testu a vývoje. Pokud povolíte SSH, [měli byste ho znovu deaktivovat, jakmile to bude možné](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Aktualizujte heslo účtu **azureuser** ve virtuálním počítači na heslo, které znáte. Když spustíte následující příkaz, vyberte vlastní heslo:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Najděte veřejnou IP adresu vašeho virtuálního počítače. Následující příkaz předpokládá, že název virtuálního počítače je **VM-vikxv** --nahradit tuto hodnotu názvem virtuálního počítače, na který jste si poznamenali předchozí poznámku:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Poznamenejte si veřejnou IP adresu vašeho virtuálního počítače.

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
ms.openlocfilehash: db1af4f046bd8849fddee299e949d6edbdaae86a
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53609085"
---
## <a name="access-the-virtual-machine"></a>Přístup k virtuálnímu počítači

Následující kroky použijte `az` příkazu ve službě Azure Cloud Shell. Pokud dáváte přednost, můžete si [instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) na vašem vývojovém počítači a spusťte příkazy místně.

Následující kroky ukazují, jak nakonfigurovat virtuální počítač Azure umožňující **SSH** přístup. Uvedené kroky předpokládají název, který jste zvolili pro akcelerátor řešení je **contoso simulace** --tuto hodnotu nahraďte názvem vašeho nasazení:

1. Zobrazit obsah skupiny prostředků obsahující prostředky akcelerátoru řešení:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Poznamenejte si název virtuálního počítače, veřejná IP adresa a skupiny zabezpečení sítě – tyto hodnoty budete potřebovat později.

1. Aktualizujte skupinu zabezpečení sítě umožňující přístup SSH. Následující příkaz předpokládá, že je název skupiny zabezpečení sítě **contoso simulace nsg** --tuto hodnotu nahraďte název skupiny zabezpečení sítě:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Povolte přístup přes SSH pouze během vývoj a testování. Pokud povolíte SSH, [byste měli znovu co nejdříve zakázat](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Aktualizovat heslo pro **azureuser** účet na virtuálním počítači na heslo, budete vědět. Zvolte vlastní heslo, když spustíte následující příkaz:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Zjistit veřejnou IP adresu vašeho virtuálního počítače. Následující příkaz předpokládá, že název virtuálního počítače je **vm vikxv** --tuto hodnotu nahraďte názvem virtuálního počítače, které jste si poznamenali dříve:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Poznamenejte si veřejnou IP adresu vašeho virtuálního počítače.

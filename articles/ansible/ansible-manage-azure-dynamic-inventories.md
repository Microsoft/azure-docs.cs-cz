---
title: Použít Ansible ke správě vašeho Azure dynamické inventáře
description: Další informace o použití Ansible ke správě vašeho Azure dynamické inventáře
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, dynamické inventáře
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/14/2018
ms.topic: article
ms.openlocfilehash: f29f4ec64b79738cae2ad684610f4817739825a9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32153105"
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Použít Ansible ke správě vašeho Azure dynamické inventáře
Ansible lze načítat informace o inventáři z různých zdrojů (včetně cloudové zdroje, jako je Azure) do *dynamické inventáře*. V tomto článku, můžete použít [prostředí cloudu Azure](./ansible-run-playbook-in-cloudshell.md) konfigurace dynamické inventáře Ansible Azure, ve kterém vytvoříte dva virtuální počítače, označit jeden z těchto virtuálních počítačů a nainstalujte Nginx s příznakem virtuálního počítače.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure** – Pokud nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před zahájením.

- **Přihlašovací údaje Azure** - [Azure vytvořit přihlašovací údaje a nakonfigurujte Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Vytvořit testovací virtuální počítače

1. Přihlaste se k webu [Azure Portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete [cloudové prostředí](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Vytvořte skupinu prostředků Azure pro uložení virtuálních počítačů pro účely tohoto kurzu.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Vytvořte dva virtuální počítače s Linuxem v Azure pomocí jednoho z následujících postupů:

    - **Ansible playbook** – článek, [vytvořit základní virtuální počítač v Azure s Ansible](/azure/virtual-machines/linux/ansible-create-vm) znázorňuje, jak vytvořit virtuální počítač z Ansible scénářem. Používáte-li zadat jeden nebo oba virtuální počítače scénářem, ujistěte se, že se připojení SSH je používat místo hesla.

    - **Rozhraní příkazového řádku Azure** -problém pro následující příkazy v prostředí cloudu vytvořit dva virtuální počítače:

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm1 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

        ```azurecli-interactive
        az vm create --resource-group ansible-inventory-test-rg \
                     --name ansible-inventory-test-vm2 \
                     --image UbuntuLTS --generate-ssh-keys
        ```

## <a name="tag-a-virtual-machine"></a>Značka virtuálního počítače
Můžete [použití značek k uspořádání prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) podle uživatelem definované kategorie. 

Zadejte následující [značky prostředku az](/cli/azure/resource?view=azure-cli-latest.md#az_resource_tag) příkaz k označení virtuální počítač `ansible-inventory-test-vm1` klíčem `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generování dynamických inventáře
Jakmile máte virtuální počítače určené (a s příznakem), je čas ke generování dynamické inventáře. Ansible poskytuje skript v jazyce Python názvem [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) který generuje dynamické inventáře vašich prostředků Azure tak, že žádostí o rozhraní API pro Azure Resource Manager. Následující kroky vás provedou pomocí `azure_rm.py` skript pro připojení k vaší dva testovací virtuální počítače Azure:

1. Použít GNU `wget` příkaz k načtení `azure_rm.py` skriptu:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Použití `chmod` příkaz a změňte oprávnění k přístupu k `azure_rm.py` skriptu. Následující příkaz používá `+x` parametr umožňující spouštění (spouštění) určeného souboru (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Použití [ansible příkaz](https://docs.ansible.com/ansible/2.4/ansible.html) pro připojení k vaší skupiny prostředků: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Po připojení se zobrazí výsledky podobné výstupu v následujícím:

    ```Output
    ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ansible-inventory-test-vm2 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

## <a name="enable-the-virtual-machine-tag"></a>Povolit značce virtuálního počítače
Jakmile nastavíte požadované značky, budete muset "Povolit" značky. Jeden způsob, jak povolit značku exportováním značky proměnné prostředí se nazývá `AZURE_TAGS` prostřednictvím **exportovat** příkaz:

```azurecli-interactive
export AZURE_TAGS=nginx
```

Jakmile byla exportována značky, můžete zkusit `ansible` znovu příkaz:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Zobrazí jenom jeden virtuální počítač (jeden, jejichž značka odpovídá hodnotě exportovali do **AZURE_TAGS** proměnnou prostředí):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Nastavit Nginx ve virtuálním počítači s příznakem
Účelem značky je povolit možnost rychle a snadno pracovat s podskupiny virtuálních počítačů. Předpokládejme například, že chcete nainstalovat pouze na virtuálních počítačích, k nimž jste přiřazeni značky Nginx `nginx`. Následující kroky popisují, jak snadno se dá provést:

1. Vytvořte soubor (tak, aby obsahovala vaše playbook) s názvem `nginx.yml` následujícím způsobem:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Vložte následující kód do nově vytvořený `nginx.yml` souboru:

    ```yml
    - name: Install and start Nginx on an Azure virtual machine
    hosts: azure
    become: yes
    tasks:
    - name: install nginx
        apt: pkg=nginx state=installed
        notify:
        - start nginx

    handlers:
    - name: start nginx
        service: name=nginx state=started
    ```

1. Spustit `nginx.yml` playbook:

  ```azurecli-interactive
  ansible-playbook -i azure_rm.py nginx.yml
  ```

1. Jakmile spustíte scénářem, zobrazí výsledky podobné výstupu v následujícím:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] **********

    TASK [Gathering Facts] **********
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] **********
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] **********
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP **********
    ansible-inventory-test-vm1 : ok=3    changed=1    unreachable=0    failed=0
    ```

## <a name="test-nginx-installation"></a>Test Nginx instalace
Tato část ukazuje jeden způsob testování Nginx je nainstalovaný na virtuálním počítači.

1. Použití [az virtuálních počítačů seznamu ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az_vm_list_ip_addresses) příkaz, který získá IP adresu `ansible-inventory-test-vm1` virtuálního počítače. Vrácená hodnota (IP adresu virtuálního počítače) se pak používá jako parametr k příkazu SSH k připojení k virtuálnímu počítači.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. [Nginx - v](https://nginx.org/en/docs/switches.html) příkaz se obvykle používá k vytištění Nginx verze. Ale ho mohou sloužit také k určení, zda je nainstalován Nginx. Zadejte ji během připojení k `ansible-inventory-test-vm1` virtuálního počítače.

    ```azurecli-interactive
    nginx -v
    ```

1. Po spuštění `nginx -v` příkaz, se nezobrazí verze Nginx (druhý řádek) určující, zda je nainstalován Nginx.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Stiskněte  **&lt;Ctrl > D** klávesové kombinace k odpojení relace SSH.

1. Provádění podle předchozích kroků pro `ansible-inventory-test-vm2` virtuální počítač vypočítá pouze informativní zpráva, která určuje, kde můžete získat Nginx (což znamená, že nemáte nainstalováno v tomto okamžiku):

    ```Output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Vytvoření základního virtuálního počítače v Azure pomocí Ansible](/azure/virtual-machines/linux/ansible-create-vm)

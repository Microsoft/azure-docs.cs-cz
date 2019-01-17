---
title: Správa dynamického inventáře vaše Azure pomocí Ansible
description: Zjistěte, jak použít Ansible ke správě vašeho Azure dynamického inventáře
ms.service: ansible
keywords: ansible, azure, devops, bash, cloud shell, dynamického inventáře
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 08/09/2018
ms.topic: tutorial
ms.openlocfilehash: cf404b84377a37f9a97f7e7c9c9f86402fe999e0
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359659"
---
# <a name="use-ansible-to-manage-your-azure-dynamic-inventories"></a>Správa dynamického inventáře vaše Azure pomocí Ansible
Ansible je možné načítat informace o inventáři z různých zdrojů (včetně cloudových zdrojů, jako je Azure) do *dynamický inventář*. V tomto článku budete používat [Azure Cloud Shell](./ansible-run-playbook-in-cloudshell.md) ke konfiguraci dynamického inventáře Ansible Azure, ve kterém vytvoříte dva virtuální počítače, označit jednu z těchto virtuálních počítačů a na označené virtuální počítače nainstalujete server Nginx.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Přihlašovací údaje Azure** - [Azure vytvořit přihlašovací údaje a konfigurace Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

## <a name="create-the-test-virtual-machines"></a>Vytvoření testovacích virtuálních počítačů

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Vytvořte skupinu prostředků Azure pro uložení virtuálních počítačů pro účely tohoto kurzu.

    > [!IMPORTANT]  
    > Skupina prostředků Azure, které vytvoříte v tomto kroku musí mít název, který je zcela malá. V opačném případě se nezdaří generování dynamického inventáře.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Vytvoření dvou virtuálních počítačů s Linuxem v Azure pomocí jedné z následujících postupů:

    - **Ansible playbook** – článek, [vytvoření základního virtuálního počítače v Azure pomocí Ansible](/azure/virtual-machines/linux/ansible-create-vm) ukazuje, jak vytvořit virtuální počítač z Ansible playbook. Pokud používáte playbooku definovat jeden nebo oba virtuální počítače, ujistěte se, že připojení SSH je používat místo hesla.

    - **Azure CLI** – problém každé z následujících příkazů ve službě Cloud Shell vytvořte dva virtuální počítače:

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

## <a name="tag-a-virtual-machine"></a>Označit virtuální počítač
Je možné [používání značek k uspořádání prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) pomocí uživatelem definované kategorie. 

Zadejte následující [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) příkazu označit virtuální počítač `ansible-inventory-test-vm1` klíčem `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generovat dynamického inventáře
Jakmile budete mít virtuální počítače definované (a označené), je čas ke generování dynamického inventáře. Ansible obsahuje skript Pythonu volá [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) dynamický inventář vašich prostředků Azure, který generuje tím, že žádosti rozhraní API pro Azure Resource Manageru. Následující postup vás provede použitím `azure_rm.py` skript pro připojení k vaší dvě testovat virtuální počítače Azure:

1. Použít GNU `wget` příkaz, který načte `azure_rm.py` skriptu:

    ```azurecli-interactive
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Použití `chmod` příkaz, který změní oprávnění k přístupu k `azure_rm.py` skriptu. Následující příkaz používá `+x` parametru spuštění (spouštění) určeného souboru (`azure_rm.py`):

    ```azurecli-interactive
    chmod +x azure_rm.py
    ```

1. Použití [ansible příkaz](https://docs.ansible.com/ansible/2.4/ansible.html) pro připojení k vaší skupiny prostředků: 

    ```azurecli-interactive
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Po připojení uvidíte výsledky podobné následující výstup:

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

## <a name="enable-the-virtual-machine-tag"></a>Povolit značky virtuálního počítače
Jakmile jednou nastavíte požadované značky, je potřeba "Povolit" značky. Jeden způsob, jak povolit značky se exportováním značky do proměnné prostředí nazývá `AZURE_TAGS` prostřednictvím **exportovat** příkaz:

```azurecli-interactive
export AZURE_TAGS=nginx
```

Jakmile byl exportován značku, můžete vyzkoušet `ansible` znovu příkaz:

```azurecli-interactive
ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
```

Nyní uvidíte pouze jeden virtuální počítač (ten, jehož značky se shoduje s hodnotou exportovat do **AZURE_TAGS** proměnnou prostředí):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Nastavení serveru Nginx na virtuálním počítači příznakem
Účelem značky je chcete povolit možnost rychle a snadno spolupracovat s podskupiny vašich virtuálních počítačů. Například Řekněme, že chcete nainstalovat server Nginx pouze na virtuálních počítačích, ke kterým přiřadíte značkou `nginx`. Následující postup ukazuje, jak je to snadné provést:

1. Vytvoření souboru (obsahovat vaše playbook) s názvem `nginx.yml` následujícím způsobem:

  ```azurecli-interactive
  vi nginx.yml
  ```

1. Vložte následující kód do nově vytvořeného `nginx.yml` souboru:

    ```yml
    ---
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

1. Po spuštění playbooku uvidíte výsledky podobné následující výstup:

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

## <a name="test-nginx-installation"></a>Otestujte instalaci serveru Nginx
Tato část ukazuje jeden způsob testování, že je nainstalovaný Nginx na virtuálním počítači.

1. Použití [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) příkaz, který načte IP adresu `ansible-inventory-test-vm1` virtuálního počítače. Vrácené hodnoty (IP adresa virtuálního počítače) se použije jako parametr k příkazu SSH k připojení k virtuálnímu počítači.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Připojeny k `ansible-inventory-test-vm1` virtuálního počítače, spusťte [nginx - v](https://nginx.org/en/docs/switches.html) příkaz zjistit, jestli je nainstalovaný Nginx.

    ```azurecli-interactive
    nginx -v
    ```

1. Po spuštění `nginx -v` příkaz, naleznete v části verze serveru Nginx (druhý řádek), která označuje, že je nainstalovaný Nginx.

    ```Output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)
    
    tom@ansible-inventory-test-vm1:~$
    ```

1. Stisknutím klávesy  **&lt;Ctrl > D** klávesové kombinace k odpojení relace SSH.

1. Provedením předchozích kroků pro `ansible-inventory-test-vm2` virtuálního počítače provede informační zpráva označující, kde můžete získat Nginx (což znamená, že není nutné je nainstalována v tomto okamžiku):

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

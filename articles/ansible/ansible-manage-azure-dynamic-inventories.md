---
title: Kurz – konfigurace dynamického inventáře vašich prostředků Azure pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible ke správě vašeho Azure dynamického inventáře
keywords: ansible, azure, devops, bash, cloud shell, dynamického inventáře
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: bdd78747505664c0824fffbd41a692818000193f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64728784"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Kurz: Konfigurace dynamického inventáře vašich prostředků Azure pomocí Ansible

Ansible je možné načítat informace o inventáři z různých zdrojů (včetně cloudových zdrojů, jako je Azure) do *dynamický inventář*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Nakonfigurujte dvě testovací virtuální počítače. 
> * Značka, jeden z virtuálních počítačů
> * Na označené virtuální počítače nainstalujete server Nginx
> * Konfigurace dynamického inventáře, která zahrnuje nakonfigurovaných prostředků Azure

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Vytvořit testovací virtuální počítače

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

## <a name="tag-a-vm"></a>Označení virtuálního počítače

Je možné [používání značek k uspořádání prostředků Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) pomocí uživatelem definované kategorie. 

Zadejte následující [az resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) příkazu označit virtuální počítač `ansible-inventory-test-vm1` klíčem `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```
## <a name="generate-a-dynamic-inventory"></a>Generovat dynamického inventáře

Jakmile budete mít virtuální počítače definované (a označené), je čas ke generování dynamického inventáře.

### <a name="using-ansible-version--28"></a>Pomocí Ansible verze < 2.8

Ansible obsahuje skript Pythonu s názvem [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) , který generuje dynamický inventář vašich prostředků Azure. Následující postup vás provede použitím `azure_rm.py` skript pro připojení k vaší dvě testovat virtuální počítače Azure:

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

### <a name="ansible-version--28"></a>Ansible verze > = 2.8

Od verze Ansible 2.8, poskytuje Ansible [modul plug-in Azure dynamický inventář](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py). Následující kroky vás provedou použitím modulu plug-in:

1. Modul plug-in inventáře vyžaduje konfigurační soubor. Konfigurační soubor musí končit `azure_rm` a mít příponu buď `yml` nebo `yaml`. V tomto kurzu příkladu uložit následující playbook jako `myazure_rm.yml`:

    ```yml
    plugin: azure_rm
    include_vm_resource_groups:
    - ansible-inventory-test-rg
    auth_source: auto
    ```

1. Spusťte následující příkaz pro odeslání příkazu ping virtuálních počítačů ve skupině prostředků:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Při spuštění ve výstupu předchozího příkazu se zobrazí následující chybová zpráva:

    ```Output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Pokud se zobrazí chyba "ověřovací klíč hostitele", přidejte následující řádek do souboru konfigurace Ansible. Ansible konfigurační soubor je umístěn v `/etc/ansible/ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Při spuštění playbooku, zobrazí se výsledky podobné následující výstup:
  
    ```Output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Povolit značka virtuálního počítače
Jakmile jednou nastavíte značku, je potřeba "Povolit" dané klíčové slovo. Jeden způsob, jak povolit značky se exportováním značky do proměnné prostředí `AZURE_TAGS` prostřednictvím `export` příkaz:

```azurecli-interactive
export AZURE_TAGS=nginx
```

- Pokud používáte Ansible < 2.8, spusťte následující příkaz:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```

- Pokud používáte Ansible > = 2.8, spusťte následující příkaz:
  
    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

Nyní uvidíte pouze jeden virtuální počítač (ten, jehož značky se shoduje s hodnotou exportovat do `AZURE_TAGS` proměnnou prostředí):

```Output
ansible-inventory-test-vm1 | SUCCESS => {
    "changed": false,
    "failed": false,
    "ping": "pong"
}
```

## <a name="set-up-nginx-on-the-tagged-vm"></a>Nastavení serveru Nginx na virtuálním počítači příznakem

Účelem značky je chcete povolit možnost rychle a snadno spolupracovat s podskupiny vašich virtuálních počítačů. Například Řekněme, že chcete nainstalovat server Nginx pouze na virtuálních počítačích, ke kterým přiřadíte značkou `nginx`. Následující postup ukazuje, jak je to snadné provést:

1. Vytvořte soubor s názvem `nginx.yml`:

   ```azurecli-interactive
   code nginx.yml
   ```

1. Vložte do editoru následující vzorový kód:

    ```yml
    ---
    - name: Install and start Nginx on an Azure virtual machine
      hosts: all
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

1. Uložte soubor a ukončete editor.

1. Spuštění playbooku pomocí `ansible-playbook` příkaz:

   - Ansible < 2.8:

    ```bash
    ansible-playbook -i azure_rm.py nginx.yml
    ```

   - Ansible > = 2.8:

    ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml
    ```

1. Po spuštění playbooku, se zobrazí výstup podobný následující výsledky:

    ```Output
    PLAY [Install and start Nginx on an Azure virtual machine] 

    TASK [Gathering Facts] 
    ok: [ansible-inventory-test-vm1]

    TASK [install nginx] 
    changed: [ansible-inventory-test-vm1]

    RUNNING HANDLER [start nginx] 
    ok: [ansible-inventory-test-vm1]

    PLAY RECAP 
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

1. Klikněte na tlačítko `<Ctrl>D` klávesové kombinace k odpojení relace SSH.

1. To předchozí kroky pro `ansible-inventory-test-vm2` virtuálního počítače provede informační zpráva označující, kde můžete získat Nginx (což znamená, že není nutné je nainstalována v tomto okamžiku):

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
> [Rychlé zprovoznění: Konfigurace virtuálních počítačů s Linuxem v Azure pomocí Ansible](/azure/virtual-machines/linux/ansible-create-vm)
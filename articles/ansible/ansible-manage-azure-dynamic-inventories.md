---
title: Kurz – konfigurace dynamického inventáře vašich prostředků Azure pomocí Ansible
description: Naučte se používat Ansible ke správě dynamických inventářů Azure.
keywords: Ansible, Azure, DevOps, bash, cloudshellu, dynamický soupis
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: cd225dcf8a0c307d49e985817b71c491559edb14
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78247855"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Kurz: Konfigurace dynamického inventáře vašich prostředků Azure pomocí Ansible

Ansible se dají použít k získání informací o inventáři z různých zdrojů (včetně cloudových zdrojů, jako je Azure), do *dynamického inventáře*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurace dvou testovacích virtuálních počítačů. 
> * Označení jednoho z virtuálních počítačů
> * Instalace Nginx na označené virtuální počítače
> * Konfigurace dynamického inventáře, který obsahuje nakonfigurované prostředky Azure

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Vytvoření testovacích virtuálních počítačů

1. Přihlaste se k webu [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Vytvořte skupinu prostředků Azure, která bude obsahovat virtuální počítače pro tento kurz.

    > [!IMPORTANT]  
    > Skupina prostředků Azure, kterou v tomto kroku vytvoříte, musí mít název, který je úplně malý. V opačném případě se generování dynamického soupisu nezdaří.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Pomocí některého z následujících postupů vytvořte dva virtuální počítače se systémem Linux v Azure:

    - **Ansible PlayBook** – článek [Vytvoření základního virtuálního počítače v Azure pomocí Ansible](./ansible-create-vm.md) ukazuje, jak vytvořit virtuální počítač z Ansible PlayBook. Pokud k definování jednoho nebo obou virtuálních počítačů použijete PlayBook, ujistěte se, že se místo hesla používá připojení SSH.

    - **Azure CLI** – vydejte každý z následujících příkazů v Cloud Shell k vytvoření těchto dvou virtuálních počítačů:

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

Pomocí [značek můžete uspořádat prostředky Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) podle uživatelem definovaných kategorií. 

### <a name="using-ansible-version--28"></a>Použití Ansible verze < 2,8
Zadejte následující příkaz [AZ Resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) – k označení `ansible-inventory-test-vm1` virtuálního počítače pomocí klíčového `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Použití Ansible verze > = 2,8
Zadejte následující příkaz [AZ Resource tag](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) – k označení `ansible-inventory-test-vm1` virtuálního počítače pomocí klíčového `Ansible=nginx`:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generovat dynamický soupis

Po definování virtuálních počítačů (a označení) je čas vytvořit dynamický soupis.

### <a name="using-ansible-version--28"></a>Použití Ansible verze < 2,8

Ansible poskytuje skript Pythonu s názvem [azure_rm. py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) , který vygeneruje dynamický inventář vašich prostředků Azure. Následující kroky vás provedou použitím skriptu `azure_rm.py` pro připojení ke dvěma testovacím virtuálním počítačům Azure:

1. K načtení skriptu `azure_rm.py` použijte příkaz `wget` GNU:

    ```python
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Pomocí příkazu `chmod` Změňte přístupová oprávnění ke skriptu `azure_rm.py`. Následující příkaz používá parametr `+x`, který umožňuje spuštění (spuštění) zadaného souboru (`azure_rm.py`):

    ```python
    chmod +x azure_rm.py
    ```

1. Pomocí [příkazu Ansible](https://docs.ansible.com/ansible/2.4/ansible.html) se připojte ke své skupině prostředků: 

    ```python
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Po připojení uvidíte výsledky podobné následujícímu výstupu:

    ```output
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

### <a name="ansible-version--28"></a>Ansible verze > = 2,8

Od Ansible 2,8 poskytuje Ansible [modul plug-in Azure Dynamic-Inventory](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py). Následující kroky vás provedou použitím modulu plug-in:

1. Modul plug-in pro inventarizace vyžaduje konfigurační soubor. Konfigurační soubor musí končit `azure_rm` a mít rozšíření buď `yml` nebo `yaml`. Pro tento příklad kurzu uložte následující PlayBook jako `myazure_rm.yml`:

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Spusťte následující příkaz, který otestuje virtuální počítače příkazem Command v této skupině prostředků:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Při spuštění předchozího příkazu se může zobrazit následující chyba:

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Pokud se zobrazí chyba "ověření typu Host-klíč", přidejte následující řádek do konfiguračního souboru Ansible. Konfigurační soubor Ansible se nachází na `/etc/ansible/ansible.cfg` nebo `~/.ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Při spuštění PlayBook se zobrazí výsledky podobné následujícímu výstupu:
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Povolení značky VM

### <a name="if-youre-using-ansible--28"></a>Pokud používáte Ansible < 2,8,

- Po nastavení značky musíte tuto značku "Povolit". Jedním ze způsobů, jak povolit značku, je exportovat značku do proměnné prostředí `AZURE_TAGS` prostřednictvím příkazu `export`:

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Spusťte následující příkaz:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Teď vidíte jenom jeden virtuální počítač (jehož značka odpovídá hodnotě exportované do proměnné prostředí `AZURE_TAGS`):

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Pokud používáte Ansible > = 2,8

- Spusťte `ansible-inventory -i myazure_rm.yml --graph` příkazu, abyste získali následující výstup:

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- K otestování připojení k virtuálnímu počítači s Nginx můžete taky spustit následující příkaz:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Nastavení Nginx na označeném virtuálním počítači

Účelem značek je umožnit možnost rychle a snadno pracovat s podskupinami virtuálních počítačů. Řekněme například, že chcete nainstalovat Nginx jenom na virtuální počítače, ke kterým jste přiřadili značku `nginx`. Následující kroky ukazují, jak snadné je dosáhnout:

1. Vytvořte soubor s názvem `nginx.yml`:

   ```console
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
            apt: pkg=nginx state=present
            notify:
            - start nginx
    
          handlers:
            - name: start nginx
              service: name=nginx state=started
    ```

1. Uložte soubor a ukončete Editor.

1. Spusťte PlayBook pomocí příkazu `ansible-playbook`:

   - Ansible < 2,8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible > = 2,8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. Po spuštění PlayBook se zobrazí výstup podobný následujícímu výsledku:

    ```output
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

## <a name="test-nginx-installation"></a>Test instalace Nginx

Tato část ukazuje jednu z postupů testování, že Nginx je na vašem virtuálním počítači nainstalovaný.

1. Pomocí příkazu [AZ VM list-IP-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) načtěte ip adresu `ansible-inventory-test-vm1`ho virtuálního počítače. Vrácená hodnota (IP adresa virtuálního počítače) se pak použije jako parametr příkazu SSH pro připojení k virtuálnímu počítači.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Když jste připojení k virtuálnímu počítači `ansible-inventory-test-vm1`, spusťte příkaz [Nginx-v](https://nginx.org/en/docs/switches.html) , abyste zjistili, jestli je nainstalovaná Nginx.

    ```console
    nginx -v
    ```

1. Po spuštění příkazu `nginx -v` se zobrazí verze Nginx (druhý řádek), která indikuje, že je Nginx nainstalovaný.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Kliknutím na kombinaci kláves `<Ctrl>D` odpojíte relaci SSH.

1. Provedením předchozích kroků pro `ansible-inventory-test-vm2` virtuální počítač se zobrazí informační zpráva s oznámením, kde můžete získat Nginx (což znamená, že v tomto okamžiku není nainstalován):

    ```output
    tom@ansible-inventory-test-vm2:~$ nginx -v
    The program 'nginx' can be found in the following packages:
    * nginx-core
    * nginx-extras
    * nginx-full
    * nginx-lightTry: sudo apt install <selected package>
    tom@ansible-inventory-test-vm2:~$
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Rychlý Start: Konfigurace virtuálních počítačů se systémem Linux v Azure pomocí Ansible](./ansible-create-vm.md)
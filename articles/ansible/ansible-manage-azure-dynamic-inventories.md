---
title: Kurz – konfigurace dynamických soupisů prostředků Azure pomocí ansible
description: Přečtěte si, jak používat Ansible ke správě dynamických inventářů Azure.
keywords: ansible, azure, devops, bash, cloudshell, dynamický inventář
ms.topic: tutorial
ms.date: 10/23/2019
ms.openlocfilehash: cd225dcf8a0c307d49e985817b71c491559edb14
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78247855"
---
# <a name="tutorial-configure-dynamic-inventories-of-your-azure-resources-using-ansible"></a>Kurz: Konfigurace dynamických soupisů prostředků Azure pomocí ansible

Ansible lze použít k vyžádat informace o inventáři z různých zdrojů (včetně cloudových zdrojů, jako je Azure) do *dynamického inventáře*. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurace dvou testovacích virtuálních počítačů. 
> * Označení jednoho z virtuálních počítačů
> * Instalace Nginx na označených virtuálních počítačích
> * Konfigurace dynamického inventáře, který zahrnuje nakonfigurované prostředky Azure

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-the-test-vms"></a>Vytvoření testovacích virtuálních měn

1. Přihlaste se k [portálu Azure](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Otevřete [prostředí Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).

1. Vytvořte skupinu prostředků Azure pro uložení virtuálních počítačů pro tento kurz.

    > [!IMPORTANT]  
    > Skupina prostředků Azure, kterou vytvoříte v tomto kroku, musí mít název, který je zcela malá písmena. V opačném případě se generování dynamického inventáře nezdaří.

    ```azurecli-interactive
    az group create --resource-group ansible-inventory-test-rg --location eastus
    ```

1. Vytvořte dva virtuální počítače Linuxu v Azure pomocí jedné z následujících technik:

    - **Ansible playbook** – článek [Vytvoření základního virtuálního počítače v Azure s Ansible](./ansible-create-vm.md) ukazuje, jak vytvořit virtuální počítač z ansible playbook. Pokud používáte playbook k definování jednoho nebo obou virtuálních počítačů, ujistěte se, že připojení SSH se používá místo hesla.

    - **Azure CLI** – problém každý z následujících příkazů v prostředí Cloud vytvořit dva virtuální počítače:

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

[Značky](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#azure-cli) můžete použít k uspořádání prostředků Azure podle kategorií definovaných uživatelem. 

### <a name="using-ansible-version--28"></a>Použití ansible verze < 2.8
Zadáním následujícího [příkazu značky prostředku az](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) označte virtuální počítač `ansible-inventory-test-vm1` pomocí klíče `nginx`:

```azurecli-interactive
az resource tag --tags nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

### <a name="using-ansible-version--28"></a>Použití ansible verze >= 2,8
Zadáním následujícího [příkazu značky prostředku az](/cli/azure/resource?view=azure-cli-latest.md#az-resource-tag) označte virtuální počítač `ansible-inventory-test-vm1` pomocí klíče `Ansible=nginx`:

```azurecli-interactive
az resource tag --tags Ansible=nginx --id /subscriptions/<YourAzureSubscriptionID>/resourceGroups/ansible-inventory-test-rg/providers/Microsoft.Compute/virtualMachines/ansible-inventory-test-vm1
```

## <a name="generate-a-dynamic-inventory"></a>Generování dynamického inventáře

Jakmile máte své virtuální počítače definované (a označené), je čas generovat dynamický inventář.

### <a name="using-ansible-version--28"></a>Použití ansible verze < 2.8

Ansible poskytuje skript Pythonu s názvem [azure_rm.py,](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) který generuje dynamický inventář vašich prostředků Azure. Následující kroky vás provedou `azure_rm.py` pomocí skriptu pro připojení ke dvěma testovacím virtuálním počítačům Azure:

1. K načtení `wget` `azure_rm.py` skriptu použijte příkaz GNU:

    ```python
    wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
    ```

1. Pomocí `chmod` příkazu změňte přístupová `azure_rm.py` oprávnění ke skriptu. Následující příkaz používá `+x` parametr k povolení spuštění (spuštění)`azure_rm.py`zadaného souboru ( ):

    ```python
    chmod +x azure_rm.py
    ```

1. Pomocí [ansible příkazu](https://docs.ansible.com/ansible/2.4/ansible.html) pro připojení ke skupině prostředků: 

    ```python
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping 
    ```

1. Po připojení se zobrazí výsledky podobné následujícímu výstupu:

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

### <a name="ansible-version--28"></a>Ansible verze >= 2,8

Počínaje Ansible 2.8, Ansible poskytuje [modul plug-in dynamického inventáře Azure](https://github.com/ansible/ansible/blob/devel/lib/ansible/plugins/inventory/azure_rm.py). Pomocí modulu plug-in vás provedou následující kroky:

1. Modul plug-in zásob vyžaduje konfigurační soubor. Konfigurační soubor `azure_rm` musí končit a `yml` `yaml`mít příponu buď nebo . V tomto příkladu kurzu uložte `myazure_rm.yml`následující playbook jako :

    ```yml
        plugin: azure_rm
        include_vm_resource_groups:
        - ansible-inventory-test-rg
        auth_source: auto
    
        keyed_groups:
        - prefix: tag
          key: tags
    ```

1. Spusťte následující příkaz pro příkaz ping pro virtuální chodvek ve skupině prostředků:

    ```bash
    ansible all -m ping -i ./myazure_rm.yml
    ```

1. Při spuštění předchozího příkazu se může zobrazit následující chyba:

    ```output
    Failed to connect to the host via ssh: Host key verification failed.
    ```
    
    Pokud se zobrazí chyba ověření klíče hostitele, přidejte do konfiguračního souboru Ansible následující řádek. Konfigurační soubor Ansible je umístěn na adrese `/etc/ansible/ansible.cfg` nebo `~/.ansible.cfg`.

    ```bash
    host_key_checking = False
    ```

1. Při spuštění playbooku se zobrazí výsledky podobné následujícímu výstupu:
  
    ```output
    ansible-inventory-test-vm1_0324 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ansible-inventory-test-vm2_8971 : ok=1    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
    ```

## <a name="enable-the-vm-tag"></a>Povolení značky Virtuální ho virtuálního mě

### <a name="if-youre-using-ansible--28"></a>Pokud používáte ansible < 2.8,

- Jakmile značku nastavíte, musíte ji "povolit". Jedním ze způsobů, jak značku povolit, je `AZURE_TAGS` export `export` značky do proměnné prostředí pomocí příkazu:

    ```console
    export AZURE_TAGS=nginx
    ```
    
- Spusťte následující příkaz:

    ```bash
    ansible -i azure_rm.py ansible-inventory-test-rg -m ping
    ```
    
    Nyní se zobrazí pouze jeden virtuální počítač (ten, jehož značka odpovídá hodnotě exportované do proměnné `AZURE_TAGS` prostředí):

    ```output
       ansible-inventory-test-vm1 | SUCCESS => {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
    ```

### <a name="if-youre-using-ansible---28"></a>Pokud používáte Ansible >= 2,8

- Spuštěním `ansible-inventory -i myazure_rm.yml --graph` příkazu získáte následující výstup:

    ```output
        @all:
          |--@tag_Ansible_nginx:
          |  |--ansible-inventory-test-vm1_9e2f
          |--@ungrouped:
          |  |--ansible-inventory-test-vm2_7ba9
    ```

- Můžete také spustit následující příkaz pro testování připojení k virtuálnímu mísu Nginx:
  
    ```bash
    ansible -i ./myazure_rm.yml -m ping tag_Ansible_nginx
    ```


## <a name="set-up-nginx-on-the-tagged-vm"></a>Nastavení Nginx u označeného virtuálního počítači

Účelem značek je umožnit rychlou a snadnou práci s podskupinami virtuálních počítačů. Řekněme například, že chcete nginx nainstalovat jenom na virtuálních počítačích, ke kterým jste přiřadili značku `nginx`. Následující kroky ilustrují, jak snadné je to dosáhnout:

1. Vytvořte soubor `nginx.yml`s názvem :

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

1. Uložte tento soubor a ukončete editor.

1. Spusťte playbook `ansible-playbook` pomocí příkazu:

   - Ansible < 2.8:

     ```bash
     ansible-playbook -i azure_rm.py nginx.yml
     ```

   - Ansible >= 2,8:

     ```bash
     ansible-playbook  -i ./myazure_rm.yml  nginx.yml --limit=tag_Ansible_nginx
     ```

1. Po spuštění playbooku se zobrazí výstup podobný následujícím výsledkům:

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

## <a name="test-nginx-installation"></a>Testovací instalace Nginx

Tato část ilustruje jednu techniku testování, že Nginx je nainstalován na vašem virtuálním počítači.

1. Pomocí příkazu [az vm list-ip-addresses](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-list-ip-addresses) načtěte IP adresu virtuálního `ansible-inventory-test-vm1` počítače. Vrácená hodnota (IP adresa virtuálního počítače) se pak použije jako parametr příkazu SSH pro připojení k virtuálnímu počítači.

    ```azurecli-interactive
    ssh `az vm list-ip-addresses \
    -n ansible-inventory-test-vm1 \
    --query [0].virtualMachine.network.publicIpAddresses[0].ipAddress -o tsv`
    ```

1. Při připojení `ansible-inventory-test-vm1` k virtuálnímu počítači spusťte příkaz [nginx -v](https://nginx.org/en/docs/switches.html) a zjistěte, zda je nainstalován Nginx.

    ```console
    nginx -v
    ```

1. Po spuštění `nginx -v` příkazu se zobrazí verze Nginx (druhý řádek), která označuje, že nginx je nainstalován.

    ```output
    tom@ansible-inventory-test-vm1:~$ nginx -v

    nginx version: nginx/1.10.3 (Ubuntu)

    tom@ansible-inventory-test-vm1:~$
    ```

1. Klepnutím `<Ctrl>D` na kombinaci klávesnice odpojte relaci SSH.

1. Provedení předchozích kroků `ansible-inventory-test-vm2` pro virtuální počítač dává informační zprávu označující, kde můžete získat Nginx (což znamená, že nemáte nainstalován v tomto bodě):

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
> [Úvodní příručka: Konfigurace virtuálních počítačů Linuxu v Azure pomocí Ansible](./ansible-create-vm.md)
---
title: Spusťte Ansible s Bash v prostředí cloudu Azure
description: Zjistěte, jak provádět různé úlohy Ansible s Bash v prostředí cloudu Azure
ms.service: ansible
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 02/01/2018
ms.topic: article
ms.openlocfilehash: 9fe65f4cf10119002bcb7a3855d112d850e20f1a
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32150375"
---
# <a name="run-ansible-with-bash-in-azure-cloud-shell"></a>Spusťte Ansible s Bash v prostředí cloudu Azure

V tomto kurzu zjistěte, jak provádět různé úlohy Ansible z Bash v prostředí cloudu. Tyto úlohy patří připojení k virtuálnímu počítači a vytvoření Ansible playbooks vytvářet a odstraňovat skupiny prostředků Azure.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure** – Pokud nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před zahájením.

- **Přihlašovací údaje Azure** - [Azure vytvořit přihlašovací údaje a nakonfigurujte Ansible](/azure/virtual-machines/linux/ansible-install-configure#create-azure-credentials)

- **Konfigurace prostředí Azure Cloud** – Pokud začínáte prostředí cloudu Azure a v článku [rychlý start pro Bash v prostředí cloudu Azure](https://docs.microsoft.com/azure/cloud-shell/quickstart) znázorňuje, jak spustit a nakonfigurovat prostředí cloudu. Spusťte na vyhrazené webové stránce pro cloudové prostředí tady:

[![Spusťte prostředí cloudu](https://shell.azure.com/images/launchcloudshell.png "spusťte cloudové prostředí Shell")](https://shell.azure.com)

## <a name="use-ansible-to-connect-to-a-vm"></a>Použít Ansible pro připojení k virtuálnímu počítači
Ansible vytvořil skript v jazyce Python názvem [azure_rm.py](https://github.com/ansible/ansible/blob/devel/contrib/inventory/azure_rm.py) který generuje dynamické inventáře vašich prostředků Azure tak, že žádostí o rozhraní API pro Azure Resource Manager. Následující kroky vás provedou pomocí `azure_rm.py` skript pro připojení k virtuální počítač Azure:

1. Otevřete Bash v prostředí cloudu. Typ prostředí je označené na levé straně okna cloudové prostředí.

1. Pokud jste virtuální počítač použít, zadejte následující příkazy do prostředí cloudu k vytvoření virtuálního počítače, ke které má být testování:

  ```azurecli-interactive
  az group create --resource-group ansible-test-rg --location eastus
  ```

  ```azurecli-interactive
  az vm create --resource-group ansible-test-rg --name ansible-test-vm --image UbuntuLTS --generate-ssh-keys
  ```

1. Použít GNU `wget` příkaz k načtení `azure_rm.py` skriptu:

  ```azurecli-interactive
  wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/azure_rm.py
  ```

1. Použití `chmod` příkaz a změňte oprávnění k přístupu k `azure_rm.py` skriptu. Následující příkaz používá `+x` parametr umožňující spouštění (spouštění) určeného souboru (`azure_rm.py`):

  ```azurecli-interactive
  chmod +x azure_rm.py
  ```

1. Použití [ansible příkaz](https://docs.ansible.com/ansible/2.4/ansible.html) pro připojení k virtuálnímu počítači: 

  ```azurecli-interactive
  ansible -i azure_rm.py ansible-test-vm -m ping
  ```

  Po připojení, měli byste vidět výsledky, které jsou podobné výstupu v:

  ```Output
  The authenticity of host 'nn.nnn.nn.nn (nn.nnn.nn.nn)' can't be established.
  ECDSA key fingerprint is SHA256:&lt;some value>.
  Are you sure you want to continue connecting (yes/no)? yes
  test-ansible-vm | SUCCESS => {
      "changed": false,
      "failed": false,
      "ping": "pong"
  }
  ```

1. Pokud jste vytvořili skupinu prostředků a virtuálního počítače v této části

  ```azurecli-interactive
  az group delete -n <resourceGroup>
  ```

## <a name="run-a-playbook-in-cloud-shell"></a>Spuštění playbooku v Cloud Shellu
[Ansible playbook](https://docs.ansible.com/ansible/2.4/ansible-playbook.html) příkaz provede Ansible playbooks, spuštění úlohy na cílovém hostiteli. Tato část vás provede pomocí prostředí cloudu vytvářet a spouštět dva playbooks – jednu pro vytvoření skupiny prostředků a druhý se odstranit skupinu prostředků. 

1. Vytvořte soubor s názvem `rg.yml` následujícím způsobem:

  ```azurecli-interactive
  vi rg.yml
  ```

1. Zkopírujte následující obsah do okna prostředí cloudu (který je nyní hostitelem instance editoru VI):

  ```yml
  - name: My first Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        location: eastus
  ```

1. Uložte soubor a ukončete VI editor zadáním `:wq` a stisknutím klávesy &lt;Enter >.

1. Použití `ansible-playbook` příkaz ke spuštění `rg.yml` playbook:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Měli byste vidět výsledky, které jsou podobné výstupu v následujícím:

  ```Output
  PLAY [My first Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Create a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

1. Ověření nasazení:

  ```azurecli-interactive
  az group show -n demoresourcegroup
  ```

1. Teď, když jste vytvořili skupinu prostředků, vytvořte druhý playbook Ansible se odstranit skupinu prostředků:

  ```azurecli-interactive
  vi rg2.yml
  ```

1. Zkopírujte následující obsah do okna prostředí cloudu (který je nyní hostitelem instance editoru VI):

  ```yml
  - name: My second Ansible Playbook
    hosts: localhost
    connection: local
    tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: demoresourcegroup
        state: absent
  ```

1. Uložte soubor a ukončete VI editor zadáním `:wq` a stisknutím klávesy &lt;Enter >.

1. Použití `ansible-playbook` příkaz ke spuštění `rg2.yml` playbook:

  ```azurecli-interactive
  ansible-playbook rg.yml
  ```

1. Měli byste vidět výsledky, které jsou podobné výstupu v následujícím:

  ```Output
  The output is as following. 
  PLAY [My second Ansible Playbook] **********

  TASK [Gathering Facts] **********
  ok: [localhost]

  TASK [Delete a resource group] **********
  changed: [localhost]

  PLAY RECAP **********
  localhost : ok=2 changed=1 unreachable=0 failed=0
  ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Vytvoření základního virtuálního počítače v Azure pomocí Ansible](/azure/virtual-machines/linux/ansible-create-vm)

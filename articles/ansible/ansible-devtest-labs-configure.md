---
title: Kurz – konfigurace testovacích prostředí ve službě Azure DevTest Labs pomocí Ansible | Dokumentace Microsoftu
description: Další informace o konfiguraci testovacího prostředí ve službě Azure DevTest Labs pomocí Ansible
ms.service: ansible
keywords: ansible, azure, devops, bash, playbooku, devtest labs
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 04/22/2019
ms.openlocfilehash: 2b1a3f614baae7dd5dc03c2e1e5c6324ac36a22e
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63767156"
---
# <a name="tutorial-configure-labs-in-azure-devtest-labs-using-ansible"></a>Kurz: Konfigurace testovacích prostředí ve službě Azure DevTest Labs pomocí Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Azure DevTest Labs](/azure/lab-services/devtest-lab-overview) umožňuje vývojářům k automatizaci vytváření prostředí virtuálních počítačů pro své aplikace. Tato prostředí můžete konfigurovat pro aplikace, vývoj, testování a školení. 

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření testovacího prostředí
> * Nastavte zásady testovacího prostředí
> * Nastavte plány testovacího prostředí
> * Vytvořte virtuální síť testovacího prostředí
> * Definování zdroje artefaktů pro testovací prostředí
> * Vytvoření virtuálního počítače v testovacím prostředí
> * Seznam zdrojů artefaktů a artefakty testovacího prostředí
> * Získat informace o Azure Resource Manageru pro zdroje artefaktů
> * Vytvoření testovacího prostředí
> * Vytvoření bitové kopie testovacího prostředí
> * Odstranění testovacího prostředí

## <a name="prerequisites"></a>Požadavky

- [!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
- [!INCLUDE [open-source-devops-prereqs-create-service-principal.md](../../includes/open-source-devops-prereqs-create-service-principal.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Playbook ukázka vytvoří skupinu prostředků Azure. Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.

```yml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
```

## <a name="create-the-lab"></a>Vytvoření testovacího prostředí

Dalším úkolem vytvoří ukázka testovacího prostředí.

```yml
- name: Create the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    location: "{{ location }}"
    storage_type: standard
    premium_data_disks: no
  register: output_lab
```

## <a name="set-the-lab-policies"></a>Nastavte zásady testovacího prostředí

Můžete nastavit nastavení zásad testovacího prostředí. Můžete nastavit následující hodnoty:

- `user_owned_lab_vm_count` je počet virtuálních počítačů, které uživatel může vlastnit.
- `user_owned_lab_premium_vm_count` počet virtuálních počítačů služby premium můžete vlastnit uživatele
- `lab_vm_count` je maximální počet virtuálních počítačů testovacího prostředí
- `lab_premium_vm_count` je maximální počet testovacího prostředí virtuálních počítačů služby premium
- `lab_vm_size` je velikost(i) virtuálních počítačů povolených testovacího prostředí
- `gallery_image` je povolené Galerie Image
- `user_owned_lab_vm_count_in_subnet` je maximální počet virtuálních počítačů uživatele v podsíti
- `lab_target_cost` Cílové náklady testovacího prostředí

```yml
- name: Set the lab policies
  azure_rm_devtestlabpolicy:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    policy_set_name: myDtlPolicySet
    name: myDtlPolicy
    fact_name: user_owned_lab_vm_count
    threshold: 5
```

## <a name="set-the-lab-schedules"></a>Nastavte plány testovacího prostředí

Ukázkový úkol v této části nakonfiguruje v testovacím plánu. 

V následujícím fragmentu kódu `lab_vms_startup` hodnota se používá k určení doby spuštění virtuálního počítače. Podobně, nastavení `lab_vms_shutdown` hodnota vytváří čas vypnutí virtuálního počítače testovacího prostředí.

```yml
- name: Set the lab schedule
  azure_rm_devtestlabschedule:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: lab_vms_shutdown
    time: "1030"
    time_zone_id: "UTC+12"
  register: output
```

## <a name="create-the-lab-virtual-network"></a>Vytvořte virtuální síť testovacího prostředí

Tato úloha následující vytvoří výchozí virtuální síť testovacího prostředí.

```yml
- name: Create the lab virtual network
  azure_rm_devtestlabvirtualnetwork:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vn_name }}"
    location: "{{ location }}"
    description: My lab virtual network
  register: output
```

## <a name="define-an-artifact-source-for-the-lab"></a>Definování zdroje artefaktů pro testovací prostředí

Zdroj artefaktů je správně strukturované úložiště GitHub obsahující definice artefaktů a šablony Azure Resource Manageru. Každé prostředí obsahuje předdefinované veřejné artefakty. Následující úlohy se dozvíte, jak vytvořit zdroj artefaktu pro testovací prostředí.

```yml
- name: Define the lab artifacts source
  azure_rm_devtestlabartifactsource:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ artifacts_name }}"
    uri: https://github.com/Azure/azure_preview_modules.git
    source_type: github
    folder_path: /tasks
    security_token: "{{ github_token }}"
```

## <a name="create-a-vm-within-the-lab"></a>Vytvoření virtuálního počítače v testovacím prostředí

Vytvoření virtuálního počítače v testovacím prostředí.

```yml
- name: Create a VM within the lab
  azure_rm_devtestlabvirtualmachine:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: "{{ vm_name }}"
    notes: Virtual machine notes, just something....
    os_type: linux
    vm_size: Standard_A2_v2
    user_name: dtladmin
    password: ZSasfovobocu$$21!
    lab_subnet:
      virtual_network_name: "{{ vn_name }}"
      name: "{{ vn_name }}Subnet"
    disallow_public_ip_address: no
    image:
      offer: UbuntuServer
      publisher: Canonical
      sku: 16.04-LTS
      os_type: Linux
      version: latest
    artifacts:
      - source_name: "{{ artifacts_name }}"
        source_path: "/Artifacts/linux-install-mongodb"
    allow_claim: no
    expiration_date: "2029-02-22T01:49:12.117974Z"
```

## <a name="list-the-labs-artifact-sources-and-artifacts"></a>Seznam zdrojů artefaktů a artefakty testovacího prostředí

K zobrazení seznamu všech výchozí a zdroji vlastních artefaktů v testovacím prostředí použijte následující úlohy:

```yml
- name: List the artifact sources
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

Následující úlohu uvádí všechny artefakty:

```yml
- name: List the artifact facts
  azure_rm_devtestlabartifact_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: public repo
  register: output
- debug:
    var: output
```

## <a name="get-azure-resource-manager-information-for-the-artifact-sources"></a>Získat informace o Azure Resource Manageru pro zdroje artefaktů

Chcete-li vypsat všechny šablony Azure Resource Manageru v `public environment repository`, předdefinované úložiště s využitím šablon:

```yml
- name: List the Azure Resource Manager template facts
  azure_rm_devtestlabartifactsource_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
  register: output
- debug:
    var: output
```

A následující úloha načítá podrobnosti o konkrétní šablony Azure Resource Manageru z úložiště:

```yml
- name: Get Azure Resource Manager template facts
  azure_rm_devtestlabarmtemplate_facts:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    artifact_source_name: "public environment repo"
    name: ServiceFabric-LabCluster
  register: output
- debug:
    var: output
```

## <a name="create-the-lab-environment"></a>Vytvoření testovacího prostředí

Následující úloha vytvoří testovací prostředí podle šablon z prostředí veřejného úložiště.

```yml
- name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"
      register: output
```

## <a name="create-the-lab-image"></a>Vytvoření bitové kopie testovacího prostředí

Následující úloha vytvoří image z virtuálního počítače. Image můžete vytvořit identických virtuálních počítačů.

```yml
- name: Create the lab image
  azure_rm_devtestlabcustomimage:
    resource_group: "{{ resource_group }}"
    lab_name: "{{ lab_name }}"
    name: myImage
    source_vm: "{{ output_vm.virtualmachines[0]['name'] }}"
    linux_os_state: non_deprovisioned
```

## <a name="delete-the-lab"></a>Odstranění testovacího prostředí

Pokud chcete odstranit testovací prostředí, použijte následující úlohy:

```yml
- name: Delete the lab
  azure_rm_devtestlab:
    resource_group: "{{ resource_group }}"
    name: "{{ lab_name }}"
    state: absent
  register: output
- name: Assert the change was correctly reported
  assert:
    that:
      - output.changed
```

## <a name="get-the-sample-playbook"></a>Získat ukázky playbook

Existují dva způsoby, jak získat playbook úplnou ukázku:
- [Stáhnout příručku](https://github.com/Azure-Samples/ansible-playbooks/blob/master/devtestlab-create.yml) a uložit ho. tím `devtestlab-create.yml`.
- Vytvořte nový soubor s názvem `devtestlab-create.yml` a zkopírujte do něj následující obsah:

```yml
---
- hosts: localhost
  #roles:
  #  - azure.azure_preview_modules
  vars:
    resource_group: "{{ resource_group_name }}"
    lab_name: myLab
    vn_name: myLabVirtualNetwork
    vm_name: myLabVm
    artifacts_name: myArtifacts
    github_token: "{{ lookup('env','GITHUB_ACCESS_TOKEN') }}"
    location: eastus
  tasks:
    - name: Create a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"

    - name: Create the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        location: eastus
        storage_type: standard
        premium_data_disks: no
      register: output_lab

    - name: Set the lab policies
      azure_rm_devtestlabpolicy:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        policy_set_name: myDtlPolicySet
        name: myDtlPolicy
        fact_name: user_owned_lab_vm_count
        threshold: 5

    - name: Set the lab schedule
      azure_rm_devtestlabschedule:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: lab_vms_shutdown
        time: "1030"
        time_zone_id: "UTC+12"
      register: output

    - name: Create the lab virtual network
      azure_rm_devtestlabvirtualnetwork:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vn_name }}"
        location: eastus
        description: My lab virtual network
      register: output

    - name: Define the lab artifacts source
      azure_rm_devtestlabartifactsource:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ artifacts_name }}"
        uri: https://github.com/Azure/azure_preview_modules.git
        source_type: github
        folder_path: /tasks
        security_token: "{{ github_token }}"

    - name:
      set_fact:
        artifact_source:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
      when: "github_token | length > 0"

    - name:
      set_fact:
        artifact_source: null
      when: "github_token | length == 0"

    - name: Create a VM within the lab
      azure_rm_devtestlabvirtualmachine:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: "{{ vm_name }}"
        notes: Virtual machine notes, just something....
        os_type: linux
        vm_size: Standard_A2_v2
        user_name: dtladmin
        password: ZSasfovobocu$$21!
        lab_subnet:
          virtual_network_name: "{{ vn_name }}"
          name: "{{ vn_name }}Subnet"
        disallow_public_ip_address: no
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          os_type: Linux
          version: latest
        artifacts:
          - source_name: "{{ artifacts_name }}"
            source_path: "/Artifacts/linux-install-mongodb"
        allow_claim: no
        expiration_date: "2029-02-22T01:49:12.117974Z"

    - name: List the artifact sources
      azure_rm_devtestlabartifactsource_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
      register: output
    - debug:
        var: output

    - name: List the artifact facts
      azure_rm_devtestlabartifact_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: public repo
      register: output
    - debug:
        var: output

    - name: List the Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
      register: output
    - debug:
        var: output

    - name: Get Azure Resource Manager template facts
      azure_rm_devtestlabarmtemplate_facts:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        artifact_source_name: "public environment repo"
        name: ServiceFabric-LabCluster
      register: output
    - debug:
        var: output

    - name: Create the lab environment
      azure_rm_devtestlabenvironment:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        user_name: "@me"
        name: myEnvironment
        location: eastus
        deployment_template: "{{ output_lab.id }}/artifactSources/public environment repo/armTemplates/WebApp"

    - name: Create the lab image
      azure_rm_devtestlabcustomimage:
        resource_group: "{{ resource_group }}"
        lab_name: "{{ lab_name }}"
        name: myImage
        source_vm: "{{ vm_name }}"
        linux_os_state: non_deprovisioned

    - name: Delete the lab
      azure_rm_devtestlab:
        resource_group: "{{ resource_group }}"
        name: "{{ lab_name }}"
        state: absent
```

## <a name="run-the-playbook"></a>Spuštění playbooku

V této části se spusťte playbook pro testování různých funkcí uvedených v tomto článku.

Před spuštěním playbooku, proveďte následující změny:
- V `vars` části, nahraďte `{{ resource_group_name }}` zástupný symbol s názvem vaší skupiny prostředků.
- Store token Githubu jako proměnnou prostředí s názvem `GITHUB_ACCESS_TOKEN`.

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook devtestlab-create.yml
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující kód jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myResourceGroup
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Spuštění playbooku pomocí `ansible-playbook` příkaz:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)
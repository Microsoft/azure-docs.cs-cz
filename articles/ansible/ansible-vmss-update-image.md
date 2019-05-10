---
title: Kurz – aktualizace vlastní image virtuálního počítače Azure škálování nastaví pomocí Ansible | Dokumentace Microsoftu
description: Zjistěte, jak použít Ansible k aktualizaci škálovací sady virtuálních počítačů v Azure s použitím vlastní image
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, vmss
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: d3eedc5b83190af46669b9b5df8643f3c80e9bb1
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230849"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Kurz: Nastaví aktualizace vlastní image škálování virtuálních počítačů Azure pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Po nasazení virtuálního počítače nakonfigurujete virtuální počítač se softwarem požadavkům vašich aplikací. Místo provádění této úlohy konfigurace pro každý virtuální počítač, můžete vytvořit vlastní image. Vlastní image je snímek existujícího virtuálního počítače, který zahrnuje veškerý software nainstalovaný. Když jste [konfigurace škálovací sady](./ansible-create-configure-vmss.md), určete bitovou kopii pro tuto škálovací sadu virtuálních počítačů. S použitím vlastní image, je stejně jako jednotlivých instancí virtuálních počítačů nakonfigurované pro vaši aplikaci. V některých případech budete muset aktualizovat vlastní image pro škálovací sady. Tento úkol je hlavním cílem tohoto kurzu.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurovat dva virtuální počítače s HTTPD
> * Vytvoření vlastní image z existujícího virtuálního počítače
> * Vytvoření škálovací sady z image
> * Aktualizovat vlastní image

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Konfigurovat dva virtuální počítače

Playbook kódu v této části vytvoří dva virtuální počítače s HTTPD nainstalovaná na obojí. 

`index.html` Stránky pro každý virtuální počítač zobrazí testovací řetězec:

* První virtuální počítač zobrazuje hodnotu `Image A`
* Druhý virtuální počítač zobrazuje hodnotu `Image B`

Tento řetězec je určená k napodobení konfiguraci každého virtuálního počítače pomocí různých softwarových.

Existují dva způsoby, jak získat ukázkové scénáře:

* [Stáhnout příručku](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) a uložit ho. tím `create_vms.yml`.
* Vytvořte nový soubor s názvem `create_vms.yml` a zkopírujte do něj následující obsah:

```yml
- name: Create two VMs (A and B) with HTTPS
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
    admin_username: testuser
    admin_password: Pass123$$$abx!
    location: eastus
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"

  - name: Create virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      address_prefixes: "10.0.0.0/16"

  - name: Create subnets for VM A and B
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      virtual_network: "{{ vm_name }}"
      name: "{{ vm_name }}"
      address_prefix: "10.0.1.0/24"

  - name: Create Network Security Group that allows HTTP
    azure_rm_securitygroup:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      rules:
        - name: HTTP
          protocol: Tcp
          destination_port_range: 80
          access: Allow
          priority: 1002
          direction: Inbound

  - name: Create public IP addresses for VM A and B
    azure_rm_publicipaddress:
      resource_group: "{{ resource_group }}"
      allocation_method: Static
      name: "{{ vm_name }}_{{ item }}"
    loop:
      - A
      - B
    register: pip_output

  - name: Create virtual network inteface cards for VM A and B
    azure_rm_networkinterface:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}_{{ item }}"
      virtual_network: "{{ vm_name }}"
      subnet: "{{ vm_name }}"
      public_ip_name: "{{ vm_name }}_{{ item }}"
      security_group: "{{ vm_name }}"
    loop:
      - A
      - B

  - name: Create VM A and B
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      vm_size: Standard_B1ms
      network_interfaces: "{{ vm_name }}_{{ item }}"
      image:
        offer: UbuntuServer
        publisher: Canonical
        sku: 16.04-LTS
        version: latest
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "sudo apt-get -y install apache2"}
    loop:
      - A
      - B

  - name: Create VM Extension
    azure_rm_virtualmachineextension:
      resource_group: "{{ resource_group }}"
      name: testVMExtension
      virtual_machine_name: "{{ vm_name }}{{ item }}"
      publisher: Microsoft.Azure.Extensions
      virtual_machine_extension_type: CustomScript
      type_handler_version: 2.0
      auto_upgrade_minor_version: true
      settings: {"commandToExecute": "printf '<html><body><h1>Image {{ item }}</h1></body></html>' >> index.html; sudo cp index.html /var/www/html/"}
    loop:
      - A
      - B

  - debug:
      msg: "Public IP Address A: {{ pip_output.results[0].state.ip_address }}"

  - debug:
      msg: "Public IP Address B: {{ pip_output.results[1].state.ip_address }}"
```

Spuštění playbooku pomocí `ansible-playbook` příkaz a nahraďte `myrg` názvem skupiny prostředků:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Z důvodu `debug` oddíly playbooku, `ansible-playbook` příkaz vytiskne IP adresu každého virtuálního počítače. Zkopírujte tyto IP adresy pro pozdější použití.

![IP adresy virtuálních počítačů](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Připojte se k dva virtuální počítače

V této části se připojíte ke všem virtuálním počítačům. Jak je uvedeno v předchozí části, řetězce `Image A` a `Image B` napodobují s dva různé virtuální počítače s různými konfiguracemi.

Pomocí IP adresy z předchozí části se připojte k oba virtuální počítače:

![Snímek obrazovky z virtuálního počítače A](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Snímek obrazovky z virtuálních počítačů B](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Vytvoření Image z každého virtuálního počítače

V tomto okamžiku budete mít dva virtuální počítače s různými konfiguracemi (jejich `index.html` soubory).

Playbook kód v této části se vytvoří vlastní image pro každý virtuální počítač:

* `image_vmforimageA` – Vlastní image vytvořené pro virtuální počítač, který zobrazuje `Image A` na své domovské stránce.
* `image_vmforimageB` – Vlastní image vytvořené pro virtuální počítač, který zobrazuje `Image B` na své domovské stránce.

Existují dva způsoby, jak získat ukázkové scénáře:

* [Stáhnout příručku](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) a uložit ho. tím `capture-images.yml`.
* Vytvořte nový soubor s názvem `capture-images.yml` a zkopírujte do něj následující obsah:

```yml
- name: Capture VM Images
  hosts: localhost
  connection: local
  vars:
    vm_name: vmforimage
  tasks:

  - name: Stop and generalize VMs
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}{{ item }}"
      generalized: yes
    loop:
      - A
      - B

  - name: Create an images from a VMs
    azure_rm_image:
      resource_group: "{{ resource_group }}"
      name: "image_{{ vm_name }}{{ item }}"
      source: "{{ vm_name }}{{ item }}"
    loop:
      - A
      - B
```

Spuštění playbooku pomocí `ansible-playbook` příkaz a nahraďte `myrg` názvem skupiny prostředků:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Vytvoření škálovací sady pomocí bitové kopie A

V této části playbooku lze konfigurovat následující prostředky Azure:

* Veřejná IP adresa
* Nástroj pro vyrovnávání zatížení
* Škálovací sada této odkazy `image_vmforimageA`

Existují dva způsoby, jak získat ukázkové scénáře:

* [Stáhnout příručku](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) a uložit ho. tím `create-vmss.yml`.
* Vytvořte nový soubor s názvem `create-vmss.yml` a zkopírujte do něj následující obsah: "

```yml
---
- hosts: localhost
  vars:
    vmss_name: vmsstest
    location: eastus
    admin_username: vmssadmin
    admin_password: User123!!!abc
    vm_name: vmforimage
    image_name: "image_vmforimageA"

  tasks:

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ vmss_name }}"
      register: pip_output

    - name: Create a load balancer
      azure_rm_loadbalancer:
        name: "{{ vmss_name }}lb"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        public_ip: "{{ vmss_name }}"
        probe_protocol: Tcp
        probe_port: 80
        probe_interval: 10
        probe_fail_count: 3
        protocol: Tcp
        load_distribution: Default
        frontend_port: 80
        backend_port: 80
        idle_timeout: 4
        natpool_frontend_port_start: 50000
        natpool_frontend_port_end: 50040
        natpool_backend_port: 22
        natpool_protocol: Tcp

    - name: Create a scale set
      azure_rm_virtualmachinescaleset:
        resource_group: "{{ resource_group }}"
        name: "{{ vmss_name }}"
        vm_size: Standard_DS1_v2
        admin_username: "{{ admin_username }}"
        admin_password: "{{ admin_password }}"
        ssh_password_enabled: true
        capacity: 2
        virtual_network_name: "{{ vm_name }}"
        subnet_name: "{{ vm_name }}"
        upgrade_policy: Manual
        tier: Standard
        managed_disk_type: Standard_LRS
        os_disk_caching: ReadWrite
        image:
          name: "{{ image_name }}"
          resource_group: "{{ resource_group }}"
        load_balancer: "{{ vmss_name }}lb"

    - debug:
        msg: "Scale set public IP address: {{ pip_output.state.ip_address }}"
```

Spuštění playbooku pomocí `ansible-playbook` příkaz a nahraďte `myrg` názvem skupiny prostředků:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Z důvodu `debug` část playbooku, `ansible-playbook` příkaz vytiskne IP adresy škálovací sady. Zkopírujte tuto IP adresu pro pozdější použití.

![Veřejná IP adresa](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Připojte se ke škálovací sadě

V této části se připojíte ke škálovací sadě. 

Pomocí IP adresy z předchozí části, připojte se ke škálovací sadě.

Jak je uvedeno v předchozí části, řetězce `Image A` a `Image B` napodobují s dva různé virtuální počítače s různými konfiguracemi.

Škálovací sada odkazů na vlastní image s názvem `image_vmforimageA`. Vlastní image `image_vmforimageA` byl vytvořen z virtuálního počítače, jejichž Domovská stránka zobrazí `Image A`.

V důsledku toho se zobrazí domovská stránka zobrazení `Image A`:

![Škálovací sada je spojené s prvním virtuálním počítači.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Ponechte okno prohlížeče, otevřené a pokračujte v další části.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Nastavit vlastní obrázek změny ve škálovací a upgradovat instance

Škálovací sady image - playbook kódu v této části se změní z `image_vmforimageA` k `image_vmforimageB`. Aktualizují se také všechny aktuální virtuální počítače nasazené ve škálovací sadě.

Existují dva způsoby, jak získat ukázkové scénáře:

* [Stáhnout příručku](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) a uložit ho. tím `update-vmss-image.yml`.
* Vytvořte nový soubor s názvem `update-vmss-image.yml` a zkopírujte do něj následující obsah:

```yml
- name: Update scale set image reference
  hosts: localhost
  connection: local
  vars:
    vmss_name: vmsstest
    image_name: image_vmforimageB
    admin_username: vmssadmin
    admin_password: User123!!!abc
  tasks:

  - name: Update scale set - second image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: "{{ admin_username }}"
      admin_password: "{{ admin_password }}"
      ssh_password_enabled: true
      capacity: 3
      virtual_network_name: "{{ vmss_name }}"
      subnet_name: "{{ vmss_name }}"
      upgrade_policy: Manual
      tier: Standard
      managed_disk_type: Standard_LRS
      os_disk_caching: ReadWrite
      image:
        name: "{{ image_name }}"
        resource_group: "{{ resource_group }}"
      load_balancer: "{{ vmss_name }}lb"

  - name: List all of the instances
    azure_rm_virtualmachinescalesetinstance_facts:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
    register: instances

  - debug:
      var: instances

  - name: manually upgrade all the instances 
    azure_rm_virtualmachinescalesetinstance:
      resource_group: "{{ resource_group }}"
      vmss_name: "{{ vmss_name }}"
      instance_id: "{{ item.instance_id }}"
      latest_model: yes
    with_items: "{{ instances.instances }}"
```

Spuštění playbooku pomocí `ansible-playbook` příkaz a nahraďte `myrg` názvem skupiny prostředků:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Vraťte se do prohlížeče a aktualizujte stránku. 

Uvidíte, že vlastní image základního virtuálního počítače je aktualizována.

![Škálovací sada je přidružen druhý virtuální počítač](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Uložte následující kód jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: myrg
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
> [Ansible v Azure](/azure/ansible)
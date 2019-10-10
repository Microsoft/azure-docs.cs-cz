---
title: Kurz – aktualizace vlastní image služby Azure Virtual Machine Scale Sets pomocí Ansible
description: Naučte se používat Ansible k aktualizaci služby Virtual Machine Scale Sets v Azure s využitím vlastní image.
keywords: Ansible, Azure, DevOps, bash, PlayBook, virtuální počítač, sada škálování virtuálních počítačů, VMSS
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 3b7baffe6ce0fadbac2dd56b9c8296c80546fa72
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241333"
---
# <a name="tutorial-update-the-custom-image-of-azure-virtual-machine-scale-sets-using-ansible"></a>Kurz: aktualizace vlastní image služby Azure Virtual Machine Scale Sets pomocí Ansible

[!INCLUDE [ansible-27-note.md](../../includes/ansible-28-note.md)]

[!INCLUDE [open-source-devops-intro-vmss.md](../../includes/open-source-devops-intro-vmss.md)]

Po nasazení virtuálního počítače nakonfigurujete virtuální počítač na software, který vaše aplikace potřebuje. Místo provádění této úlohy konfigurace pro každý virtuální počítač můžete vytvořit vlastní image. Vlastní image je snímek existujícího virtuálního počítače, který obsahuje nainstalovaný software. Při [konfiguraci sady škálování](./ansible-create-configure-vmss.md)určíte image, která se má použít pro virtuální počítače sady škálování. Když použijete vlastní image, každá instance virtuálního počítače je u vaší aplikace nakonfigurované identicke. V některých případech možná budete muset aktualizovat vlastní image sady škálování. Tento úkol se zaměřuje na tento kurz.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Konfigurace dvou virtuálních počítačů pomocí HTTPD
> * Vytvoření vlastní image z existujícího virtuálního počítače
> * Vytvoření sady škálování z obrázku
> * Aktualizace vlastní image

## <a name="prerequisites"></a>Požadované součásti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="configure-two-vms"></a>Konfigurace dvou virtuálních počítačů

Kód PlayBook v této části vytvoří dva virtuální počítače s nainstalovanou HTTPD na obě. 

Stránka `index.html` pro každý virtuální počítač zobrazuje testovací řetězec:

* První virtuální počítač zobrazí hodnotu `Image A`.
* Druhý virtuální počítač zobrazí hodnotu `Image B`.

Tento řetězec má napodobovat konfiguraci každého virtuálního počítače pomocí jiného softwaru.

Existují dva způsoby, jak získat ukázkovou PlayBook:

* [Stáhněte si PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/01-create-vms.yml) a uložte ho do `create_vms.yml`.
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

Spusťte PlayBook pomocí příkazu `ansible-playbook` a nahraďte `myrg` názvem vaší skupiny prostředků:

```bash
ansible-playbook create-vms.yml --extra-vars "resource_group=myrg"
```

Z důvodu `debug` v PlayBook se v příkazu `ansible-playbook` vytiskne IP adresa každého virtuálního počítače. Zkopírujte tyto IP adresy pro pozdější použití.

![IP adresy virtuálních počítačů](media/ansible-vmss-update-image/vmss-update-vms-ip-addresses.png)

## <a name="connect-to-the-two-vms"></a>Připojení ke dvěma virtuálním počítačům

V této části se připojíte ke každému virtuálnímu počítači. Jak je uvedeno v předchozí části, řetězce `Image A` a `Image B` napodobují dva odlišné virtuální počítače s různými konfiguracemi.

Pomocí IP adres z předchozí části se připojte k oběma virtuálním počítačům:

![Snímek obrazovky z virtuálního počítače A](media/ansible-vmss-update-image/vmss-update-browser-vma.png)

![Snímek obrazovky s virtuálním počítačem B](media/ansible-vmss-update-image/vmss-update-browser-vmb.png)

## <a name="create-images-from-each-vm"></a>Vytváření imagí z každého virtuálního počítače

V tomto okamžiku máte dva virtuální počítače s mírně odlišnými konfiguracemi (soubory `index.html`).

Kód PlayBook v této části vytvoří vlastní image pro každý virtuální počítač:

* `image_vmforimageA` – vlastní image vytvořená pro virtuální počítač, která na domovské stránce zobrazuje `Image A`.
* `image_vmforimageB` – vlastní image vytvořená pro virtuální počítač, která na domovské stránce zobrazuje `Image B`.

Existují dva způsoby, jak získat ukázkovou PlayBook:

* [Stáhněte si PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/02-capture-images.yml) a uložte ho do `capture-images.yml`.
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

Spusťte PlayBook pomocí příkazu `ansible-playbook` a nahraďte `myrg` názvem vaší skupiny prostředků:

```bash
ansible-playbook capture-images.yml --extra-vars "resource_group=myrg"
```

## <a name="create-scale-set-using-image-a"></a>Vytvoření sady škálování pomocí Image A

V této části se ke konfiguraci následujících prostředků Azure používá PlayBook:

* Veřejná IP adresa
* Load Balancer
* Sada škálování, která odkazuje na `image_vmforimageA`

Existují dva způsoby, jak získat ukázkovou PlayBook:

* [Stáhněte si PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/03-create-vmss.yml) a uložte ho do `create-vmss.yml`.
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

Spusťte PlayBook pomocí příkazu `ansible-playbook` a nahraďte `myrg` názvem vaší skupiny prostředků:

```bash
ansible-playbook create-vmss.yml --extra-vars "resource_group=myrg"
```

Vzhledem k tomu, že část `debug` v PlayBook, příkaz `ansible-playbook` vytiskne IP adresu sady škálování. Tuto IP adresu zkopírujte pro pozdější použití.

![Veřejná IP adresa](media/ansible-vmss-update-image/vmss-update-vmss-public-ip.png)

## <a name="connect-to-the-scale-set"></a>Připojit se k sadě škálování

V této části se připojíte k sadě škálování. 

Pomocí IP adresy z předchozí části se připojte k sadě škálování.

Jak je uvedeno v předchozí části, řetězce `Image A` a `Image B` napodobují dva odlišné virtuální počítače s různými konfiguracemi.

Sada škálování odkazuje na vlastní image s názvem `image_vmforimageA`. Vlastní image `image_vmforimageA` se vytvořila z virtuálního počítače, na kterém se na domovské stránce zobrazuje `Image A`.

V důsledku toho se zobrazí domovská stránka zobrazující `Image A`:

![Sada škálování je přidružená k prvnímu virtuálnímu počítači.](media/ansible-vmss-update-image/vmss-update-browser-initial-vmss.png)

Nechte okno prohlížeče otevřené, když budete pokračovat k další části.

## <a name="change-custom-image-in-scale-set-and-upgrade-instances"></a>Změna vlastní image v sadě škálování a instancích upgradu

PlayBook kód v této části změní obrázek sady škálování z `image_vmforimageA` na `image_vmforimageB`. Všechny aktuální virtuální počítače nasazené pomocí sady škálování jsou taky aktualizované.

Existují dva způsoby, jak získat ukázkovou PlayBook:

* [Stáhněte si PlayBook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vmss_images/04-update-vmss-image.yml) a uložte ho do `update-vmss-image.yml`.
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

Spusťte PlayBook pomocí příkazu `ansible-playbook` a nahraďte `myrg` názvem vaší skupiny prostředků:

```bash
ansible-playbook update-vmss-image.yml --extra-vars "resource_group=myrg"
```

Vraťte se do prohlížeče a aktualizujte stránku. 

Vidíte, že je upravena základní image virtuálního počítače.

![Sada škálování je přidružená k druhému virtuálnímu počítači.](media/ansible-vmss-update-image/vmss-update-browser-updated-vmss.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Následující kód uložte jako `cleanup.yml`:

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

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible)
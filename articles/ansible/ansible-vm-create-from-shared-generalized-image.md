---
title: Kurz – vytvoření virtuálního počítače nebo sady škálování virtuálních počítačů z Galerie sdílených imagí Azure pomocí Ansible
description: Naučte se používat Ansible k vytvoření virtuálního počítače nebo sady škálování virtuálních počítačů na základě generalizované image v galerii sdílených imagí.
keywords: Ansible, Azure, DevOps, bash, PlayBook, virtuální počítač, sada škálování virtuálních počítačů, Galerie sdílených imagí
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155840"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Kurz: Vytvoření virtuálního počítače nebo sady škálování virtuálních počítačů z Galerie sdílených imagí Azure pomocí Ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[Galerie sdílených imagí](/azure/virtual-machines/windows/shared-image-galleries) je služba, která umožňuje snadnou správu, sdílení a organizování vlastních imagí spravovaných uživatelem. Tato funkce je výhodná pro scénáře, ve kterých se udržuje a sdílí mnoho imagí. Vlastní image je možné sdílet mezi předplatnými a mezi Azure Active Directorymi klienty. Obrázky je také možné replikovat do více oblastí pro rychlejší škálování nasazení.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření zobecněného virtuálního počítače a vlastní image
> * Vytvoření galerie sdílených imagí
> * Vytvořit sdílenou image a verzi image
> * Vytvoření virtuálního počítače pomocí generalizované image
> * Vytvoření sady škálování virtuálních počítačů pomocí generalizované image
> * Získat informace o galerii sdílených imagí, image a verzi

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Získat ukázkovou playbooky

Existují dva způsoby, jak získat úplnou sadu vzorových playbooky:

- [Stáhněte si SLOŽKU SIG](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) a uložte ji do místního počítače.
- Vytvořte nový soubor pro každý oddíl a zkopírujte do něj ukázkovou PlayBook.

`vars.yml` soubor obsahuje proměnné používané všemi vzorovými playbooky pro tento kurz. Soubor můžete upravit tak, aby poskytoval jedinečné názvy a hodnoty.

První ukázka PlayBook `00-prerequisites.yml` vytvoří, co je potřeba k dokončení tohoto kurzu:
- Skupina prostředků, což je logický kontejner, ve kterém se nasazují a spravují prostředky Azure.
- Virtuální síť; podsíť Veřejná IP adresa a síťová karta pro virtuální počítač.
- Zdrojový virtuální počítač, který se používá k vytvoření generalizované image.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create resource group if doesn't exist
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        location: "{{ location }}"
    
    - name: Create virtual network
      azure_rm_virtualnetwork:
        resource_group: "{{ resource_group }}"
        name: "{{ virtual_network_name }}"
        address_prefixes: "10.0.0.0/16"

    - name: Add subnet
      azure_rm_subnet:
        resource_group: "{{ resource_group }}"
        name: "{{ subnet_name }}"
        address_prefix: "10.0.1.0/24"
        virtual_network: "{{ virtual_network_name }}"

    - name: Create public IP address
      azure_rm_publicipaddress:
        resource_group: "{{ resource_group }}"
        allocation_method: Static
        name: "{{ ip_name }}"

    - name: Create virtual network inteface cards for VM A and B
      azure_rm_networkinterface:
        resource_group: "{{ resource_group }}"
        name: "{{ network_interface_name }}"
        virtual_network: "{{ virtual_network_name }}"
        subnet: "{{ subnet_name }}"

    - name: Create VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        admin_username: testuser
        admin_password: "Password1234!"
        vm_size: Standard_B1ms
        network_interfaces: "{{ network_interface_name }}"
        image:
          offer: UbuntuServer
          publisher: Canonical
          sku: 16.04-LTS
          version: latest
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook 00-prerequisites.yml
```

V [Azure Portal](https://portal.azure.com)zkontrolujte skupinu prostředků, kterou jste zadali v části `vars.yml`, abyste viděli nový virtuální počítač a různé prostředky, které jste vytvořili.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Generalizace virtuálního počítače a vytvoření vlastní image

Následující PlayBook `01a-create-generalized-image.yml`generalizuje zdrojový virtuální počítač vytvořený v předchozím kroku a pak na něm vytvoří vlastní image.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Generalize VM
      azure_rm_virtualmachine:
        resource_group: "{{ resource_group }}"
        name: "{{ source_vm_name }}"
        generalized: yes

    - name: Create custom image
      azure_rm_image:
        resource_group: "{{ resource_group }}"
        name: "{{ image_name }}"
        source: "{{ source_vm_name }}"
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Zkontrolujte skupinu prostředků a ujistěte se, že se `testimagea` zobrazí.

## <a name="create-the-shared-image-gallery"></a>Vytvoření galerie sdílených imagí

Galerie imagí je úložiště pro sdílení a správu imagí. Vzorový PlayBook kód v `02-create-shared-image-gallery.yml` vytvoří galerii sdílených imagí ve vaší skupině prostředků.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image gallery
      azure_rm_gallery:
        resource_group: "{{ resource_group }}"
        name: "{{ shared_gallery_name }}"
        location: "{{ location }}"
        description: This is the gallery description.
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Ve vaší skupině prostředků teď uvidíte novou galerii, `myGallery`.

## <a name="create-a-shared-image-and-image-version"></a>Vytvořit sdílenou image a verzi image

Další PlayBook `03a-create-shared-image-generalized.yml` vytvoří definici obrázku a verzi image.

Definice obrázků zahrnují typ obrázku (Windows nebo Linux), poznámky k verzi a minimální a maximální požadavky na paměť. Verze Image je verze image. Galerie, definice obrázků a verze image usnadňují uspořádání imagí v logických skupinách. 

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
    - name: Create shared image
      azure_rm_galleryimage:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        name: "{{ shared_image_name }}"
        location: "{{ location }}"
        os_type: linux
        os_state: generalized
        identifier:
          publisher: myPublisherName
          offer: myOfferName
          sku: mySkuName
        description: Image description
    
    - name: Create or update a simple gallery image version.
      azure_rm_galleryimageversion:
        resource_group: "{{ resource_group }}"
        gallery_name: "{{ shared_gallery_name }}"
        gallery_image_name: "{{ shared_image_name }}"
        name: "{{ shared_image_version }}"
        location: "{{ location }}"
        publishing_profile:
          end_of_life_date: "2020-10-01t00:00:00+00:00"
          exclude_from_latest: yes
          replica_count: 3
          storage_account_type: Standard_LRS
          target_regions:
            - name: West US
              regional_replica_count: 1
            - name: East US
              regional_replica_count: 2
              storage_account_type: Standard_ZRS
          managed_image:
            name: "{{ image_name }}"
            resource_group: "{{ resource_group }}"
      register: output

    - debug:
        var: output
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Vaše skupina prostředků teď má definici image a verzi image pro vaši galerii.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Vytvoření virtuálního počítače založeného na generalizované imagi

Nakonec spuštěním `04a-create-vm-using-generalized-image.yml` vytvořte virtuální počítač založený na generalizované imagi, kterou jste vytvořili v předchozím kroku.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create VM using shared image
    azure_rm_virtualmachine:
      resource_group: "{{ resource_group }}"
      name: "{{ vm_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Vytvoření sady škálování virtuálních počítačů na základě generalizované image

Můžete také vytvořit sadu škálování virtuálního počítače založenou na generalizované imagi. Pokud to chcete udělat, spusťte `05a-create-vmss-using-generalized-image.yml`.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Create a virtual machine scale set using a shared image
    azure_rm_virtualmachinescaleset:
      resource_group: "{{ resource_group }}"
      name: "{{ vmss_name }}"
      vm_size: Standard_DS1_v2
      admin_username: adminUser
      admin_password: PassWord01
      capacity: 2
      virtual_network_name: "{{ virtual_network_name }}"
      upgrade_policy: Manual
      subnet_name: "{{ subnet_name }}"
      managed_disk_type: Standard_LRS
      image:
        id: "/subscriptions/{{ lookup('env', 'AZURE_SUBSCRIPTION_ID') }}/resourceGroups/{{ resource_group }}/providers/Microsoft.Compute/galleries/{{ shared_gallery_name }}/images/{{ shared_image_name }}/versions/{{ shared_image_version }}"
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Získání informací o galerii

Spuštěním `06-get-info.yml`můžete získat informace o galerii, definici bitové kopie a verzi.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Get Shared Image Gallery information
    azure_rm_gallery_info:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
  - name: Get shared image information
    azure_rm_galleryimage_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
  - name: Get Shared Image Gallery image version information
    azure_rm_galleryimageversion_info:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Odstranit sdílenou bitovou kopii

Informace o odstranění prostředků Galerie najdete v ukázce PlayBook `07-delete-gallery.yml`. Odstraní prostředky v opačném pořadí. Začněte tím, že odstraníte verzi image. Po odstranění všech verzí imagí můžete definici image odstranit. Po odstranění všech definicí imagí můžete galerii odstranit.

```yml
- hosts: localhost
  connection: local
  vars_files:
    - ./vars.yml

  tasks:
  - name: Delete gallery image version.
    azure_rm_galleryimageversion:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      gallery_image_name: "{{ shared_image_name }}"
      name: "{{ shared_image_version }}"
      state: absent

  - name: Delete gallery image
    azure_rm_galleryimage:
      resource_group: "{{ resource_group }}"
      gallery_name: "{{ shared_gallery_name }}"
      name: "{{ shared_image_name }}"
      state: absent

  - name: Delete a simple gallery.
    azure_rm_gallery:
      resource_group: "{{ resource_group }}"
      name: "{{ shared_gallery_name }}"
      state: absent
```

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku. 

Vzorový kód PlayBook v této části se používá pro:

- Odstranit dvě skupiny prostředků vytvořené dříve

Uložte následující ukázkový playbook jako `cleanup.yml`:

```yml
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent
```

Tady jsou některé klíčové poznámky, které je potřeba vzít v úvahu při práci s ukázkovým PlayBook:

- Zástupný symbol `{{ resource_group_name }}` nahraďte názvem vaší skupiny prostředků.
- Všechny prostředky v rámci dvou zadaných skupin prostředků se odstraní.

Spusťte PlayBook pomocí příkazu `ansible-playbook`:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)
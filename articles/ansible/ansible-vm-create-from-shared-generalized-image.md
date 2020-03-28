---
title: Kurz – vytvoření škálovací sady virtuálního počítače nebo virtuálního počítače z Galerie sdílených bitových obrázků Azure pomocí ansible
description: Naučte se používat Ansible k vytvoření škálovací sady virtuálních počítačů nebo virtuálních počítačů na základě zobecněné image v Galerii sdílených obrázků.
keywords: ansible, azure, devops, bash, playbook, virtual machine, virtual machine scale set, shared image gallery
ms.topic: tutorial
ms.date: 10/14/2019
ms.openlocfilehash: f784419736854095cc1bc5da14f3867ac3f7eb12
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74155840"
---
# <a name="tutorial-create-a-vm-or-virtual-machine-scale-set-from-the-azure-shared-image-gallery-using-ansible"></a>Kurz: Vytvoření škálovací sady virtuálního počítače nebo virtuálního počítače z Galerie sdílených bitových obrázků Azure pomocí Ansible

[!INCLUDE [ansible-29-note.md](../../includes/ansible-29-note.md)]

[Galerie sdílených obrázků](/azure/virtual-machines/windows/shared-image-galleries) je služba, která umožňuje snadnospravovat, sdílet a organizovat vlastní spravované obrázky. Tato funkce je výhodná pro scénáře, kde je udržováno a sdíleno mnoho bitových kopií. Vlastní image lze sdílet mezi předplatnými a mezi klienty Služby Azure Active Directory. Bitové kopie lze také replikovat do více oblastí pro rychlejší škálování nasazení.

[!INCLUDE [ansible-tutorial-goals.md](../../includes/ansible-tutorial-goals.md)]

> [!div class="checklist"]
>
> * Vytvoření generalizovaného virtuálního virtuálního montovaa a vlastní image
> * Vytvoření galerie sdílených obrázků
> * Vytvoření sdílené ho obrázku a verze obrázku
> * Vytvoření virtuálního virtuálního virtuálního ms pomocí generalizované bitové kopie
> * Vytvoření škálovací sady virtuálních strojů pomocí generalizované bitové kopie
> * Získejte informace o galerii sdílených obrázků, obrázek a verzi.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]
[!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## <a name="get-the-sample-playbooks"></a>Získejte ukázkové playbooky

Existují dva způsoby, jak získat kompletní sadu ukázkových playbooků:

- [Stáhněte si složku SIG](https://github.com/Azure-Samples/ansible-playbooks/tree/master/SIG_generalized_image) a uložte ji do místního počítače.
- Vytvořte nový soubor pro každý oddíl a zkopírujte v něm ukázkový playbook.

Soubor `vars.yml` obsahuje proměnné používané všemi ukázkovými playbooky pro tento kurz. Soubor můžete upravit a poskytnout tak jedinečné názvy a hodnoty.

První ukázkový `00-prerequisites.yml` playbook vytvoří to, co je nezbytné k dokončení tohoto kurzu:
- Skupina prostředků, což je logický kontejner, ve kterém jsou nasazené a spravované prostředky Azure.
- Virtuální síť; podsíť; veřejná IP adresa a karta síťového rozhraní pro virtuální ho.
- Zdrojový virtuální počítač, který se používá k vytvoření generalizovaného obrazu.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook 00-prerequisites.yml
```

Na [webu Azure Portal](https://portal.azure.com)zkontrolujte skupinu prostředků, ve které jste `vars.yml` zadali, abyste viděli nový virtuální počítač a různé prostředky, které jste vytvořili.

## <a name="generalize-the-vm-and-create-a-custom-image"></a>Generalize virtuálního virtuálního mísy a vytvoření vlastní image

Další playbook `01a-create-generalized-image.yml`, zobecnizuje zdrojový virtuální počítač vytvořený v předchozím kroku a pak na něm vytvoří vlastní image.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook 01a-create-generalized-image.yml
```

Zkontrolujte skupinu prostředků `testimagea` a ujistěte se, že se zobrazí.

## <a name="create-the-shared-image-gallery"></a>Vytvoření galerie sdílených obrázků

Galerie obrázků je úložištěpro sdílení a správu obrázků. Ukázkový kód playbooku v aplikaci `02-create-shared-image-gallery.yml` vytvoří galerii sdílených obrázků ve skupině prostředků.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook 02-create-shared-image-gallery.yml
```

Nyní se zobrazí nová `myGallery`galerie , ve skupině prostředků.

## <a name="create-a-shared-image-and-image-version"></a>Vytvoření sdílené ho obrázku a verze obrázku

Další playbook `03a-create-shared-image-generalized.yml` vytvoří definici obrazu a verzi obrázku.

Definice bitových obrázků zahrnují typ bitové kopie (Windows nebo Linux), poznámky k verzi a minimální a maximální požadavky na paměť. Verze obrázku je verze obrázku. Galerie, definice obrázků a verze obrázku vám pomohou uspořádat obrázky v logických skupinách. 

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook 03a-create-shared-image-generalized.yml
```

Vaše skupina prostředků má nyní definici obrázku a verzi obrázku pro galerii.

## <a name="create-a-vm-based-on-the-generalized-image"></a>Vytvoření virtuálního počítače na základě zobecněné bitové kopie

Nakonec spusťte `04a-create-vm-using-generalized-image.yml` a vytvořte virtuální počítač na základě zobecněné image, kterou jste vytvořili v předchozím kroku.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook 04a-create-vm-using-generalized-image.yml
```

## <a name="create-a-virtual-machine-scale-sets-based-on-the-generalized-image"></a>Vytvoření škálovacích sad virtuálních strojů založených na zobecněné bitové kopii

Můžete také vytvořit škálovací sadu virtuálního počítače na základě generalizované bitové kopie. Utíkejte, `05a-create-vmss-using-generalized-image.yml` abyste tak učinili.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook 05a-create-vmss-using-generalized-image.yml
```

## <a name="get-information-about-the-gallery"></a>Získání informací o galerii

Informace o galerii, definici obrázku a `06-get-info.yml`verzi můžete získat spuštěním .

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook 06-get-info.yml
```

## <a name="delete-the-shared-image"></a>Odstranění sdíleného obrázku

Chcete-li odstranit prostředky galerie, `07-delete-gallery.yml`podívejte se na ukázkový playbook . Odstranit prostředky v opačném pořadí. Začněte odstraněním verze bitové kopie. Po odstranění všech verzí obrázku můžete odstranit definici obrázku. Po odstranění všech definic obrázků můžete galerii odstranit.

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

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook 07-delete-gallery.yml
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte prostředky vytvořené v tomto článku. 

Ukázkový kód playbooku v této části slouží k:

- Odstranění dvou skupin prostředků vytvořených dříve

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

Zde jsou některé klíčové poznámky, které je třeba zvážit při práci s ukázkovým playbookem:

- Nahraďte `{{ resource_group_name }}` zástupný symbol názvem skupiny prostředků.
- Všechny prostředky v rámci dvou určených skupin prostředků budou odstraněny.

Spusťte playbook `ansible-playbook` pomocí příkazu:

```bash
ansible-playbook cleanup.yml
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Ansible v Azure](/azure/ansible/)
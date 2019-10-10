---
title: Rychlý Start – nasazení šablony řešení Ansible pro Azure do CentOS
description: V tomto rychlém startu se dozvíte, jak nasadit šablonu řešení Ansible na virtuálním počítači s CentOS hostovaným v Azure spolu s nástroji nakonfigurovanými pro práci s Azure.
keywords: Ansible, Azure, DevOps, šablona řešení, virtuální počítač, spravované identity pro prostředky Azure, CentOS, Red Hat
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 5ababe9eb1f680378e882970df2d0b008287a7c4
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241715"
---
# <a name="quickstart-deploy-the-ansible-solution-template-for-azure-to-centos"></a>Rychlý Start: nasazení šablony řešení Ansible pro Azure do CentOS

Šablona řešení Ansible pro Azure je navržená tak, aby nakonfigurovala instanci Ansible na virtuálním počítači s CentOS společně s Ansible a sadou nástrojů nakonfigurovaných pro práci s Azure. Mezi tyto nástroje patří:

- **Moduly Ansible pro Azure** – [moduly Ansible pro Azure](./ansible-matrix.md) představují sadu modulů, které vám umožní vytvořit a spravovat infrastrukturu v Azure. Ve výchozím nastavení je nasazena nejnovější verze těchto modulů. Během procesu nasazení šablony řešení ale můžete zadat číslo verze, které je vhodné pro vaše prostředí.
- **Rozhraní příkazového řádku Azure (CLI) 2,0** – [Azure CLI 2,0](/cli/azure/?view=azure-cli-latest) je prostředí příkazového řádku pro různé platformy pro správu prostředků Azure. 
- **spravované identity pro prostředky Azure** – funkce [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) řeší potíže se zabezpečením přihlašovacích údajů ke cloudovým aplikacím.

## <a name="prerequisites"></a>Požadované součásti

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="deploy-the-ansible-solution-template"></a>Nasazení šablony řešení Ansible

1. Přejděte do [šablony řešení Ansible v Azure Marketplace](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Vyberte **získat hned**.

1. Zobrazí se okno s informacemi o podmínek použití, zásadách ochrany osobních údajů a použití podmínek Azure Marketplace. Vyberte **pokračovat**.

1. Zobrazí se Azure Portal a zobrazí stránku Ansible, která popisuje šablonu řešení. Vyberte **Vytvořit**.

1. Na stránce **vytvořit Ansible** se zobrazí několik karet. Na kartě **základy** zadejte požadované informace:

   - **Název** – zadejte název vaší instance Ansible. Pro účely ukázky je použit název `ansiblehost`.
   - **Uživatelské jméno:** – zadejte uživatelské jméno, které bude mít přístup k instanci Ansible. Pro účely ukázky je použit název `ansibleuser`.
   - **Typ ověřování:** – vyberte možnost **heslo** nebo **veřejný klíč SSH**. Pro demonstrační účely je vybrán **veřejný klíč SSH** .
   - **Heslo** a **potvrzení hesla** – Pokud pro **typ ověřování**vyberete **heslo** , zadejte pro tyto hodnoty heslo.
   - **Veřejný klíč SSH** – Pokud vyberete možnost **veřejný klíč SSH** pro **typ ověřování**, zadejte svůj veřejný klíč RSA v jednořádkovém formátu – počínaje `ssh-rsa`.
   - **Předplatné** – v rozevíracím seznamu vyberte své předplatné Azure.
   - **Skupina prostředků** – z rozevíracího seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit novou** a zadejte název nové skupiny prostředků. Pro demonstrační účely se používá nová skupina prostředků s názvem `ansiblerg`.
   - **Umístění** – v rozevíracím seznamu vyberte umístění, které je vhodné pro váš scénář.

     ![Karta Azure Portal pro základní nastavení Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Vyberte **OK**.

1. Na kartě **Další nastavení** zadejte požadované informace:

   - **Size** – Azure Portal standardně standardní velikost. Pokud chcete zadat jinou velikost, která bude vyhovovat vašemu konkrétnímu scénáři, vyberte šipku pro zobrazení seznamu různých velikostí.
   - **Typ disku virtuálního počítače** – vyberte buď **SSD** (Solid-State disk), nebo **HDD** (pevný disk). Pro účely ukázky se pro své výhody výkonu vybere jednotka **SSD** . Další informace o jednotlivých typech diskového úložiště najdete v následujících článcích:
       - [Vysoce výkonné Premium Storage a spravované disky pro virtuální počítače](/azure/virtual-machines/windows/premium-storage)
       - [Managed Disks SSD úrovně Standard pro úlohy virtuálních počítačů Azure](/azure/virtual-machines/windows/disks-standard-ssd)
   - **Veřejná IP adresa** – toto nastavení zadejte, pokud chcete s virtuálním počítačem komunikovat mimo virtuální počítač. Výchozí je nová veřejná IP adresa, která má název `ansible-pip`. Pokud chcete zadat jinou IP adresu, vyberte šipku zadat atributy, jako je název, SKU a přiřazení této IP adresy. 
   - **Popisek názvu domény** – zadejte název veřejné domény virtuálního počítače. Název musí být jedinečný a splňovat požadavky na pojmenování. Další informace o tom, jak zadat název pro virtuální počítač, najdete v tématu zásady [vytváření názvů pro prostředky Azure](/azure/architecture/best-practices/naming-conventions).
   - **Ansible verze** – zadejte buď číslo verze, nebo hodnotu `latest` pro nasazení nejnovější verze. Kliknutím na informační ikonu vedle **Ansible verze** zobrazíte další informace o dostupných verzích.

     ![Karta Azure Portal pro další nastavení Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Vyberte **OK**.

1. Na kartě **Nastavení integrace Ansible** zadejte typ ověřování. Další informace o zabezpečení prostředků Azure najdete v tématu [co jsou spravované identity pro prostředky Azure?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Karta Azure Portal pro nastavení integrace Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Vyberte **OK**.

1. Na stránce **Souhrn** se zobrazí proces ověření a výpis zadaných kritérií pro nasazení Ansible. Odkaz v dolní části karty vám umožní **Stáhnout šablonu a parametry** pro použití s podporovanými jazyky a platformami Azure. 

     ![Karta Azure Portal pro souhrn Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Vyberte **OK**.

1. Po zobrazení karty **vytvořit** vyberte **OK** a nasaďte Ansible.

1. Výběrem ikony **oznámení** v horní části stránky portálu Sledujte nasazení Ansible. Po dokončení nasazení vyberte **Přejít do skupiny prostředků**. 

     ![Karta Azure Portal pro souhrn Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-complete.png)

1. Na stránce skupina prostředků Získejte IP adresu hostitele Ansible a přihlaste se, abyste mohli spravovat prostředky Azure pomocí Ansible.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Rychlý Start: konfigurace virtuálního počítače se systémem Linux v Azure pomocí Ansible](/azure/virtual-machines/linux/ansible-create-vm)
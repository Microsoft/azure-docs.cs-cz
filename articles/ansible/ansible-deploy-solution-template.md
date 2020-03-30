---
title: Úvodní příručka – nasazení šablony řešení Ansible pro Azure do CentOS
description: V tomto rychlém startu se dozvíte, jak nasadit šablonu řešení Ansible na virtuální medailon CentOS hostovaný v Azure, spolu s nástroji nakonfigurovanými pro práci s Azure.
keywords: ansible, azure, devops, šablona řešení, virtuální počítač, spravované identity pro azure prostředky, centos, red hat
ms.topic: quickstart
ms.date: 04/30/2019
ms.openlocfilehash: cc3a6c1f0ef36b51b62e6aa58f317aee13149589
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77193782"
---
# <a name="quickstart-deploy-the-ansible-solution-template-for-azure-to-centos"></a>Úvodní příručka: Nasazení šablony řešení Ansible pro Azure do CentOS

Šablona řešení Ansible pro Azure je navržena tak, aby nakonfigurovala instanci Ansible na virtuálním počítači CentOS spolu s Ansible a sadou nástrojů nakonfigurovaných pro práci s Azure. Mezi tyto nástroje patří:

- **Ansible moduly pro Azure** – [Ansible moduly pro Azure](./ansible-matrix.md) jsou sada modulů, které vám umožní vytvářet a spravovat infrastrukturu v Azure. Nejnovější verze těchto modulů je nasazena ve výchozím nastavení. Během procesu nasazení šablony řešení však můžete zadat číslo verze, která je vhodná pro vaše prostředí.
- **Azure Command-Line Interface (CLI) 2.0** – [Azure CLI 2.0](/cli/azure/?view=azure-cli-latest) je prostředí příkazového řádku pro různé platformy pro správu prostředků Azure. 
- **spravované identity pro prostředky Azure** – funkce [spravované identity pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) řeší problém zabezpečení přihlašovacích údajů cloudových aplikací.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [open-source-devops-prereqs-azure-subscription.md](../../includes/open-source-devops-prereqs-azure-subscription.md)]

## <a name="deploy-the-ansible-solution-template"></a>Nasazení šablony řešení Ansible

1. Přejděte k [šabloně řešení Ansible na Webu Azure Marketplace](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Vyberte **MOŽNOST ZÍSKAT NYNÍ**.

1. Zobrazí se okno, které podrobně popisuje podmínky použití, zásady ochrany osobních údajů a podmínky používání podmínek Azure Marketplace. Vyberte **Pokračovat**.

1. Zobrazí se portál Azure a zobrazí stránku Ansible, která popisuje šablonu řešení. Vyberte **Vytvořit**.

1. Na stránce **Vytvořit ansible** se zobrazí několik karet. Na kartě **Základy** zadejte požadované informace:

   - **Název** – zadejte název instance Ansible. Pro ukázkové účely `ansiblehost` se používá název.
   - **Uživatelské jméno:** - Zadejte uživatelské jméno, které bude mít přístup k instanci Ansible. Pro ukázkové účely `ansibleuser` se používá název.
   - **Typ ověřování:** - Vyberte veřejný klíč **Heslo** nebo **SSH**. Pro účely ukázky je vybrán **veřejný klíč SSH.**
   - **Heslo** a **potvrdit heslo** – Pokud vyberete **heslo** pro **typ ověřování**, zadejte heslo pro tyto hodnoty.
   - **Veřejný klíč SSH** – pokud vyberete **veřejný klíč SSH** pro **typ ověřování**, zadejte `ssh-rsa`svůj veřejný klíč RSA v jednořádkovém formátu – počínaje .
   - **Předplatné** – v rozevíracím seznamu vyberte předplatné Azure.
   - **Skupina prostředků** – Vrozenou existující skupinu prostředků z rozevíracího seznamu nebo vyberte **Vytvořit nový** a zadejte název nové skupiny prostředků. Pro ukázkové účely se `ansiblerg` používá nová skupina prostředků s názvem.
   - **Umístění** – vyberte umístění z rozevíracího seznamu, který je vhodný pro váš scénář.

     ![Karta Portál Azure pro základní nastavení Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Vyberte **OK**.

1. Na kartě **Další nastavení** zadejte požadované informace:

   - **Velikost** – portál Azure výchozí standardní velikost. Chcete-li určit jinou velikost, která vyhovuje konkrétnímu scénáři, vyberte šipku pro zobrazení seznamu různých velikostí.
   - **Typ disku virtuálního počítače** – vyberte buď **SSD** (Premium SSD) nebo **HDD** (pevný disk). Pro ukázkové účely je **ssd disfunkční** systém vybrán pro své výhody výkonu. Další informace o jednotlivých typech diskového úložiště naleznete v následujících článcích:
       - [Vysoce výkonná služba Premium Storage a spravované disky pro virtuální počítače](/azure/virtual-machines/windows/premium-storage)
       - [Standardní spravované disky SSD pro úlohy virtuálních počítačů Azure](/azure/virtual-machines/windows/disks-standard-ssd)
   - **Veřejná IP adresa** – toto nastavení zadejte, pokud chcete komunikovat s virtuálním počítačem mimo virtuální počítač. Výchozí hodnota je nová veřejná IP `ansible-pip`adresa, která má název . Chcete-li zadat jinou adresu IP, vyberte šipku, určete atributy – například název, skladovou položku a přiřazení této adresy IP. 
   - **Popisek názvu domény** – Zadejte název domény virtuálního počítače směřující k veřejné straně. Název musí být jedinečný a musí splňovat požadavky na pojmenování. Další informace o určení názvu virtuálního počítače najdete v [tématu Konvence pojmenování prostředků Azure](/azure/architecture/best-practices/resource-naming).
   - **Ansible verze** - Zadejte číslo `latest` verze nebo hodnotu pro nasazení nejnovější verze. Kliknutím na informační ikonu vedle **položky Ansible verze** zobrazíte další informace o dostupných verzích.

     ![Karta Azure portal pro další nastavení Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Vyberte **OK**.

1. Na kartě **Ansible Integration Settings** zadejte typ ověřování. Další informace o zabezpečení prostředků Azure najdete v tématu [Co je spravované identity pro prostředky Azure?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Karta Portál Azure pro nastavení integrace Ansible](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Vyberte **OK**.

1. Stránka **Souhrn** zobrazuje proces ověření a uvádí zadaná kritéria pro nasazení Ansible. Odkaz v dolní části karty umožňuje **stáhnout šablonu a parametry** pro použití s podporovanými jazyky a platformami Azure. 

     ![Karta Azure portal pro kartu Ansible Summary](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Vyberte **OK**.

1. Když se zobrazí karta **Vytvořit,** vyberte **OK,** chcete-li nasadit Ansible.

1. V horní části stránky portálu vyberte ikonu **Oznámení** a sledujte nasazení Ansible. Po dokončení nasazení vyberte **Přejít na skupinu prostředků**. 

     ![Karta Azure portal pro kartu Ansible Summary](./media/ansible-quick-deploy-solution-template/portal-ansible-setup-complete.png)

1. Na stránce skupiny prostředků získejte IP adresu svého hostitele Ansible a přihlaste se ke správě prostředků Azure pomocí Ansible.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Úvodní příručka: Konfigurace virtuálního počítače s Linuxem v Azure pomocí Ansible](./ansible-create-vm.md)
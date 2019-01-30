---
title: Nasazení šablony Ansible řešení pro Azure a CentOS
description: Zjistěte, jak nasadit šablonu řešení Ansible na virtuálním počítači s CentOS hostovaný v Azure, společně s nástroji, které jsou nakonfigurovány pro práci s Azure.
ms.service: ansible
keywords: ansible, azure, devops, šablona řešení, virtuálních počítačů spravovaných identit pro prostředky azure, centos, red hat
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 01/28/2019
ms.openlocfilehash: 705d23a93b25c513d413f2115c47d52ff6ef2bac
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55229500"
---
# <a name="deploy-the-ansible-solution-template-for-azure-to-centos"></a>Nasazení šablony Ansible řešení pro Azure a CentOS
Ansible šablona řešení pro Azure slouží ke konfiguraci Ansible instance virtuálního počítače CentOS spolu s Ansible a sada nástrojů, které jsou nakonfigurovány pro práci s Azure. Mezi tyto nástroje patří:

- **Ansible modulů pro službu Azure** – [Ansible modulů pro službu Azure](./ansible-matrix.md) jsou sada modulů, které vám umožní vytvářet a spravovat vaši infrastrukturu v Azure. Nejnovější verzi tyto moduly se nasadí ve výchozím nastavení. Během procesu nasazení šablony řešení ale můžete zadat číslo verze, která je vhodná pro vaše prostředí.
- **Rozhraní příkazového řádku Azure (CLI) 2.0** – [příkazového řádku Azure CLI 2.0](/cli/azure/?view=azure-cli-latest) je prostředí příkazového řádku napříč platformami pro správu prostředků Azure. 
- **spravované identity pro prostředky Azure** – [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) funkce řeší problém ze zabezpečení cloudu přihlašovací údaje aplikací.

## <a name="prerequisites"></a>Požadavky
- **Předplatné Azure** – Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

## <a name="deploy-the-ansible-solution-template-from-the-azure-marketplace"></a>Nasazení šablony řešení Ansible v Azure Marketplace

1. Přejděte [šablonu řešení Ansible v Azure Marketplace](https://azuremarketplace.microsoft.com/en-%20%20us/marketplace/apps/azure-oss.ansible?tab=Overview).

1. Vyberte **získat teď**.

1. Zobrazí se okno s podrobnostmi o podmínkách použití, zásady ochrany osobních údajů a podmínky použití z Azure Marketplace. Vyberte **Pokračovat**.

1. Na webu Azure portal se zobrazí a zobrazí stránku Ansible, který popisuje šablonu řešení. Vyberte **Vytvořit**.

1. V **vytvořit Ansible** stránku, uvidíte několik karet. Na **Základy** kartu, zadejte požadované informace:

    - **Název** – zadejte název vaší instance Ansible. Pro účely ukázky, název `ansiblehost` se používá.
    - **Uživatelské jméno:** – zadejte uživatelské jméno, které budou mít přístup k instanci Ansible. Pro účely ukázky, název `ansibleuser` se používá.
    - **Typ ověřování:** – vyberte buď **heslo** nebo **veřejný klíč SSH**. Pro účely ukázky **veřejný klíč SSH** zaškrtnuto.
    - **Heslo** a **potvrzení hesla** – Pokud vyberete **heslo** pro **typ ověřování**, zadejte heslo pro tyto hodnoty.
    - **Veřejný klíč SSH** – Pokud vyberete **veřejný klíč SSH** pro **typ ověřování**, zadejte veřejný klíč RSA v jednořádkovém formátu – od verze `ssh-rsa`.
    - **Předplatné** – z rozevíracího seznamu vyberte své předplatné Azure.
    - **Skupina prostředků** – z rozevíracího seznamu vyberte existující skupinu prostředků nebo vyberte **vytvořit nový** a zadejte název pro novou skupinu prostředků. Pro účely ukázky novou skupinu prostředků s názvem `ansiblerg` se používá.
    - **Umístění** – vyberte umístění, z rozevíracího seznamu, která je vhodná pro váš scénář.

    ![Azure portal kartu pro základní nastavení Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-1.png)

1. Vyberte **OK**.

1. V **další nastavení** kartu, zadejte požadované informace:

    - **Velikost** – Azure portal výchozí hodnota je standardní velikosti. Pokud chcete zadat jinou velikost, který přizpůsobuje váš konkrétní scénář, vyberte šipku a zobrazte seznam různých velikostí.
    - **Velikost disku virtuálního počítače** – vyberte buď **SSD** (Premium Solid-State Drive) nebo **HDD** (pevný disk). Pro účely ukázky **SSD** je vybrán pro jeho výhody výkonu. Další informace o všech těchto typů diskové úložiště, naleznete v tématu v následujících článcích:
        - [Vysoce výkonná služba Premium Storage a spravované disky pro virtuální počítače](/azure/virtual-machines/windows/premium-storage)
        - [Standardní spravované disky SSD pro úlohy Azure virtuálních počítačů](/azure/virtual-machines/windows/disks-standard-ssd)
    - **Veřejná IP adresa** – toto nastavení zadat, pokud chcete ke komunikaci s virtuálním počítačem mimo virtuální počítač. Výchozí hodnota je novou veřejnou IP adresu, která má název `ansible-pip`. Pokud chcete zadat jinou IP adresu, vyberte šipku určit atributy – například název, skladovou Položku a přiřazení této IP adresy. 
    - **Popisek názvu domény** – zadejte název domény veřejně přístupných virtuálního počítače. Název musí být jedinečný a musí splňovat požadavky na pojmenování. Další informace o zadávání názvu virtuálního počítače najdete v tématu [zásady vytváření názvů pro prostředky Azure](/azure/architecture/best-practices/naming-conventions).
    - **Verze Ansible** – zadejte číslo verze nebo hodnotu `latest` k nasazení nejnovější verze. Vyberte ikonu informace vedle **Ansible verze** zobrazíte další informace o dostupných verzí.

    ![Azure portal kartu pro další nastavení Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-2.png)

1. Vyberte **OK**.

1. V **nastavení integrace Ansible** kartu, zadejte typ ověřování. Další informace o zabezpečení prostředků Azure najdete v tématu [co je spravované identity pro prostředky Azure?](/azure/active-directory/managed-identities-azure-resources/overview).

    ![Azure portal kartu pro nastavení Ansible integrace](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-3.png)

1. Vyberte **OK**.

1. **Souhrn** znázorňující postup ověření a výpis zadaná kritéria pro nastavení Ansible, zobrazí se stránka. Odkaz v dolní části karty umožňuje **stáhnout šablonu a parametry** pro použití se službou Azure podporované jazyky a platformy. 

    ![Azure portal kartu karta se souhrnem Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-tab-4.png)

1. Vyberte **OK**.

1. Když **vytvořit** kartě se zobrazí, vyberte **OK** nasazení Ansible.

1. Vyberte **oznámení** ikonu v horní části stránky portálu ke sledování nasazení Ansible. Po dokončení nasazení vyberte **přejít ke skupině prostředků**. 

    ![Azure portal kartu karta se souhrnem Ansible](./media/ansible-deploy-solution-template/portal-ansible-setup-complete.png)

1. Na stránce skupiny prostředků získejte IP adresu hostitele Ansible a přihlaste se ke správě prostředků Azure pomocí Ansible.

## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"] 
> [Použití Ansible k vytvoření virtuálního počítače s Linuxem v Azure](/azure/virtual-machines/linux/ansible-create-vm)

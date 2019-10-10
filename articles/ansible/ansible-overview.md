---
title: Použití Ansible s Azure
description: Seznámení s používáním Ansible automatizuje zřizování cloudu, správu konfigurací a nasazení aplikací.
keywords: Ansible, Azure, DevOps, přehled, zřizování cloudu, Správa konfigurace, nasazení aplikací, Ansible moduly, Ansible playbooky
ms.topic: overview
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: b6b9656edc43f38a4cb005be53c33bb98781679c
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241510"
---
# <a name="using-ansible-with-azure"></a>Použití Ansible s Azure

[Ansible](https://www.ansible.com) je open source produkt, který automatizuje zřizování cloudu, správu konfigurací a nasazení aplikací. Pomocí Ansible můžete zřídit virtuální počítače, kontejnery a sítě a dokončit cloudové infrastruktury. Ansible také umožňuje automatizovat nasazení a konfiguraci prostředků ve vašem prostředí.

Tento článek obsahuje základní přehled některých výhod používání Ansible s Azure.

## <a name="ansible-playbooks"></a>Ansible playbooky

[Ansible playbooky](https://docs.ansible.com/ansible/latest/playbooks.html) vám umožní nasměrovat Ansible ke konfiguraci vašeho prostředí. Playbooky se kódují pomocí YAML tak, aby bylo možné je číst lidmi. Část kurzy obsahuje mnoho příkladů použití playbooky k instalaci a konfiguraci prostředků Azure. 

## <a name="ansible-modules"></a>Moduly Ansible

Ansible zahrnuje sadu [Ansible modulů](https://docs.ansible.com/ansible/latest/modules_by_category.html) , které se spouštějí přímo na vzdálených hostitelích nebo prostřednictvím [playbooky](https://docs.ansible.com/ansible/latest/playbooks.html). Uživatelé mohou vytvořit své vlastní moduly. Moduly se používají k řízení systémových prostředků, jako jsou například služby, balíčky nebo soubory, nebo příkazy pro spuštění systému.

Pro interakci se službami Azure Ansible zahrnuje sadu [cloudových modulů Ansible](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Tyto moduly vám umožní vytvořit a orchestrovat infrastrukturu v Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrace stávající úlohy do Azure

Po použití Ansible k definování infrastruktury můžete použít PlayBook vaší aplikace, aby Azure v případě potřeby automaticky škálovat vaše prostředí. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizace cloudové nativní aplikace v Azure

Ansible umožňuje automatizovat nativní cloudové aplikace v Azure pomocí mikroslužeb Azure, jako jsou [Azure Functions](https://azure.microsoft.com//services/functions/) a [Kubernetes v Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Správa nasazení s dynamickým inventářem

Ansible poskytuje možnost vyžádat si inventář z prostředků Azure prostřednictvím funkce [dynamického inventarizace](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) . Pak můžete označit existující nasazení Azure a spravovat tato přidaná nasazení prostřednictvím Ansible.

## <a name="additional-azure-marketplace-options"></a>Další možnosti Azure Marketplace

[Ansible věž](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) je Azure Marketplace image Red Hat. 

Ansible Tower je webové uživatelské rozhraní a řídicí panel pro Ansible, který má následující funkce:

* Umožňuje definovat řízení přístupu na základě rolí, plánování úloh a správu grafického inventáře. 
* Zahrnuje REST API a rozhraní příkazového řádku, abyste mohli vložit věž do existujících nástrojů a procesů. 
* Podporuje výstup spuštění PlayBook v reálném čase. 
* Šifruje přihlašovací údaje, například Azure a klíče SSH – můžete tak delegovat úlohy bez odhalení přihlašovacích údajů.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Modul Ansible a matice verzí pro Azure

Ansible zahrnuje sadu modulů pro použití při zřizování a konfiguraci prostředků Azure. Mezi tyto prostředky patří virtuální počítače, sady škálování, síťové služby a služby kontejneru. V tabulce [Ansible](./ansible-matrix.md) jsou uvedené moduly Ansible pro Azure a verze Ansible, ve kterých se dodávají.

## <a name="next-steps"></a>Další kroky

- [Rychlý Start: nasazení šablony řešení Ansible pro Azure do CentOS](./ansible-deploy-solution-template.md)
- [Rychlý Start: Konfigurace virtuálních počítačů se systémem Linux v Azure pomocí Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)
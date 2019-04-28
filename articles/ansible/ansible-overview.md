---
title: Použití Ansible v Azure | Dokumentace Microsoftu
description: Úvod do automatizuje zřizování cloudů, správu konfigurací a nasazení aplikací pomocí Ansible.
keywords: ansible, azure, devops, přehled, zřizování cloudu, správu konfigurací, nasazení aplikace, ansible moduly, runbooků ansible
ms.topic: overview
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 54d66a0ac425a9a0a63c91317ead0e02ecf9452c
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764154"
---
# <a name="using-ansible-with-azure"></a>Azure pomocí Ansible

[Ansible](https://www.ansible.com) je opensourcový produkt, který automatizuje zřizování cloudů, správu konfigurací a nasazení aplikací. Pomocí Ansible můžete zřizovat virtuální počítače, kontejnery a sítě a provést cloudových infrastruktur. Ansible taky umožňuje automatizaci nasazení a konfigurace prostředků ve vašem prostředí.

Tento článek poskytuje základní přehled o některé z výhod použití Ansible v Azure.

## <a name="ansible-playbooks"></a>Runbooků Ansible

[Runbooků Ansible](https://docs.ansible.com/ansible/latest/playbooks.html) umožňují přímé Ansible ke konfiguraci prostředí. Playbooky se kódují pomocí YAML tak, aby byly čitelné. Kurzy část poskytuje mnoho příkladů použití playbooky k instalaci a konfiguraci prostředků Azure. 

## <a name="ansible-modules"></a>Moduly Ansible

Ansible zahrnuje sadu [Ansible moduly](https://docs.ansible.com/ansible/latest/modules_by_category.html) , které jsou spuštěny na vzdáleného hostitele přímo nebo prostřednictvím [playbooky](https://docs.ansible.com/ansible/latest/playbooks.html). Uživatelé můžou vytvářet vlastní moduly. Moduly se používají k řízení systémových prostředků – například služby, balíčky nebo soubory, nebo spuštěním příkazů systému.

Pro interakci se službami Azure, Ansible zahrnuje sadu [Ansible cloudové moduly](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Tyto moduly vám umožňují vytvářet a orchestrovat vaši infrastrukturu v Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrace existující úlohy do Azure

Jakmile Ansible můžete definovat infrastrukturu, můžete použít playbook vaší aplikace umožňuje podle potřeby automaticky vertikálně navyšovat prostředí Azure. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizace aplikace nativní pro cloud v Azure

Ansible umožňuje automatizovat aplikací nativních pro cloud v Azure s využitím mikroslužeb Azure, jako [Azure Functions](https://azure.microsoft.com//services/functions/) a [Kubernetes v Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Správa nasazení pomocí dynamického inventáře

Prostřednictvím jeho [dynamický inventář](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) funkce, Ansible poskytuje možnost inventáře o přijetí změn z prostředků Azure. Můžete označit existující nasazení v Azure a spravujte těmito klíčovými slovy nasazení Ansible.

## <a name="additional-azure-marketplace-options"></a>Další možnosti Azure Marketplace

[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) je image Azure Marketplace by Red Hat. 

Ansible Tower je webové uživatelské rozhraní a řídicí panel pro Ansible, který má následující funkce:

* Umožňuje definovat řízení přístupu na základě rolí, plánování úloh a správu grafické inventáře. 
* Obsahuje rozhraní REST API a rozhraní příkazového řádku, takže Tower můžete vložit do existujících nástrojů a procesů. 
* Podporuje v reálném čase výstup spuštění playbooků. 
* Šifruje přihlašovací údaje – jako jsou klíče Azure a SSH - tak mohou delegovat úlohy bez vystavení přihlašovací údaje.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Ansible modul a verzi matice pro Azure

Ansible obsahuje sadu modulů pro použití v zřizování a konfiguraci prostředků Azure. Tyto prostředky zahrnují virtuální počítače, škálovací sady, síťové služby a služby kontejneru. [Ansible matice](./ansible-matrix.md) seznam modulů Ansible pro Azure a verze Ansible, ve kterých dodání.

## <a name="next-steps"></a>Další postup

- [Rychlé zprovoznění: Nasazení šablony Ansible řešení pro Azure a CentOS](./ansible-deploy-solution-template.md)
- [Rychlé zprovoznění: Konfigurace virtuálních počítačů s Linuxem v Azure pomocí Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fazure%2Fansible%2Ftoc.json&bc=%2Fazure%2Fbread%2Ftoc.json)
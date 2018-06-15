---
title: Ansible pomocí Azure
description: Úvod do používání Ansible k automatizuje zřizování cloudu, správu konfigurace a nasazení aplikací.
ms.service: ansible
keywords: ansible, azure, devops, přehled, cloudu zřizování, Správa konfigurace, nasazení aplikace, ansible moduly, ansible playbooks
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 01/19/2018
ms.topic: article
ms.openlocfilehash: a7ce3c239a50462a9af137eb958268f72dbf79d1
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2018
ms.locfileid: "28212103"
---
# <a name="ansible-with-azure"></a>Ansible s Azure

[Ansible](http://www.ansible.com) je produkt open source, který automatizuje zřizování cloudu, správu konfigurace a nasazení aplikací. Pomocí Ansible můžete zřizovat virtuální počítače, kontejnery a sítě a dokončení cloudových infrastruktur. Kromě toho Ansible umožňuje automatizovat nasazení a konfigurace prostředků ve vašem prostředí.

Tento článek poskytuje základní přehled některé z výhod použití Ansible s Azure.

## <a name="ansible-playbooks"></a>Ansible playbooks

[Ansible playbooks](http://docs.ansible.com/ansible/latest/playbooks.html) jsou Ansible na konfiguraci, nasazení a orchestraci jazyk. Můžete popisují chcete, aby vaše vzdálené systémy k vynucení zásad, nebo u sady kroků v obecné IT procesů. Když vytvoříte playbook provedete použitím YAML, který definuje model konfigurace nebo proces.

## <a name="ansible-modules"></a>Ansible moduly

Sada obsahuje Ansible [Ansible moduly](http://docs.ansible.com/ansible/latest/modules_by_category.html) , mohou být provedeny přímo na vzdáleným hostitelům nebo prostřednictvím [playbooks](http://docs.ansible.com/ansible/latest/playbooks.html). Uživatelé mohou také vytvářet vlastní moduly. Moduly lze použít k řízení prostředky systému – například služby, balíčky nebo soubory – nebo spuštěním příkazů systému.

Pro interakci se službami Azure, Ansible zahrnuje sadu [Ansible cloudu moduly](http://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) , který poskytuje nástroje ke snadné vytváření a orchestraci infrastruktury v Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrovat existující úlohy do Azure

Jakmile Ansible jste použili k definování infrastrukturu, můžete použít playbook vaší aplikace, když necháte Azure automaticky škálovat prostředí podle potřeby. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizovat cloudu nativní aplikaci v Azure

Ansible umožňuje automatizovat cloudu nativních aplikací v Azure pomocí Azure mikroslužeb, jako třeba [Azure Functions](https://azure.microsoft.com//services/functions/) a [Kubernetes v Azure](https://azure.microsoft.com/services/container-service/kubernetes/)).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Správa nasazení pomocí dynamických inventáře
Přes jeho [dynamické inventáře](http://docs.ansible.com/ansible/intro_dynamic_inventory.html) funkce Ansible poskytuje možnost pro vyžádání obsahu inventáře z prostředků Azure. Pak můžete označení vaší existující nasazení Azure a spravovat tyto s příznakem nasazení prostřednictvím Ansible.

## <a name="additional-azure-marketplace-options"></a>Další možnosti Azure Marketplace
[Ansible věž](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) image Azure Marketplace pomocí Red Hat pomáhá organizacím škálování IT automatizace a správa komplexní nasazení napříč fyzických, virtuálních a cloudových infrastruktur. Ansible věž obsahuje funkce, které poskytují další úroveň viditelnosti, řízení, zabezpečení a efektivitu, které jsou nezbytné pro dnešní podniky. Ansible věž šifruje přihlašovací údaje, jako jsou Azure a SSH klíče tak, aby úlohy můžete delegovat na méně zkušení zaměstnanci bez riziko úniku přihlašovacích údajů.

## <a name="next-steps"></a>Další postup
- [Konfigurace Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Vytvoření virtuálního počítače s Linuxem](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
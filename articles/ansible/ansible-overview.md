---
title: Azure pomocí Ansible
description: Úvod do automatizuje zřizování cloudů, správu konfigurací a nasazení aplikací pomocí Ansible.
ms.service: ansible
keywords: ansible, azure, devops, přehled, zřizování cloudu, správu konfigurací, nasazení aplikace, ansible moduly, runbooků ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 09/02/2018
ms.topic: overview
ms.openlocfilehash: 55dcda953454ce3eb4e19dabbf198f886d028180
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54357926"
---
# <a name="ansible-with-azure"></a>Ansible v Azure

[Ansible](https://www.ansible.com) je opensourcový produkt, který automatizuje zřizování cloudů, správu konfigurací a nasazení aplikací. Pomocí Ansible můžete zřizovat virtuální počítače, kontejnery a sítě a provést cloudových infrastruktur. Kromě toho Ansible umožňuje automatizovat nasazení a konfiguraci prostředků ve vašem prostředí.

Tento článek poskytuje základní přehled o některé z výhod použití Ansible v Azure.

## <a name="ansible-playbooks"></a>Runbooků Ansible

[Runbooků Ansible](https://docs.ansible.com/ansible/latest/playbooks.html) jsou Ansible v konfiguraci, nasazení a orchestraci jazyka. Můžete popsat, chcete, aby vaše vzdálené systémy k vynucení zásad, nebo sadu kroků v obecné IT procesů. Při vytváření playbooku provedete použitím YAML, který definuje model konfigurace nebo proces.

## <a name="ansible-modules"></a>Moduly Ansible

Ansible zahrnuje sadu [Ansible moduly](https://docs.ansible.com/ansible/latest/modules_by_category.html) , které mohou být provedeny přímo na vzdáleného hostitele nebo prostřednictvím [playbooky](https://docs.ansible.com/ansible/latest/playbooks.html). Uživatelé mohou také vytvářet vlastní moduly. Moduly je možné řídit systémových prostředků – například služby, balíčky nebo soubory, nebo spuštěním příkazů systému.

Pro interakci se službami Azure, Ansible zahrnuje sadu [Ansible cloudové moduly](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure) , který poskytuje nástroje, které umožní snadno vytvářet a organizovat vaši infrastrukturu v Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrace existující úlohy do Azure

Jakmile použijete Ansible definovat infrastrukturu, můžete použít playbook vaší aplikace umožňuje podle potřeby automaticky vertikálně navyšovat prostředí Azure. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizace aplikace nativní pro cloud v Azure

Ansible umožňuje automatizovat aplikací nativních pro cloud v Azure s využitím mikroslužeb Azure, jako [Azure Functions](https://azure.microsoft.com//services/functions/) a [Kubernetes v Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Správa nasazení pomocí dynamického inventáře
Prostřednictvím jeho [dynamický inventář](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) funkce, Ansible poskytuje možnost inventáře o přijetí změn z prostředků Azure. Můžete označit existující nasazení v Azure a spravujte těmito klíčovými slovy nasazení Ansible.

## <a name="additional-azure-marketplace-options"></a>Další možnosti Azure Marketplace
[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) image na Azure Marketplace by Red Hat pomáhá organizacím škálovat automatizaci IT a spravujte složitá nasazení napříč fyzickými, virtuálními a cloudovými infrastrukturami. Ansible Tower obsahuje funkce, které poskytovaly vyšší úroveň viditelnosti, řízení, zabezpečení a efektivitu nezbytné pro dnešní podniky. Ansible Tower šifruje přihlašovací údaje, jako jsou klíče Azure a SSH, tak, aby úlohy může delegovat na méně zkušených zaměstnance bez riziku úniku pověření.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Ansible modul a verzi matice pro Azure
Ansible se dodává se počet modulů, které mohou být provedeny přímo na vzdáleného hostitele nebo prostřednictvím playbooky.
[Ansible modul a verzi matice](./ansible-matrix.md) seznam modulů Ansible pro Azure, která můžete zřizovat prostředky cloudu Azure, jako jsou virtuální počítače, sítě a služby kontejneru. 

## <a name="next-steps"></a>Další postup
- [Konfigurace Ansible](/azure/virtual-machines/linux/ansible-install-configure?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
- [Vytvoření virtuálního počítače s Linuxem](/azure/virtual-machines/linux/ansible-create-vm?toc=%2Fen-us%2Fazure%2Fansible%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

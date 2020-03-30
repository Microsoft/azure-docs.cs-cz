---
title: Použití ansible s Azure
description: Úvod k použití Ansible k automatizaci zřizování cloudu, správy konfigurace a nasazení aplikací.
keywords: ansible, azure, devops, přehled, cloud provision, configuration management, application deployment, ansible modules, ansible playbooks
ms.topic: overview
ms.date: 04/30/2019
ms.openlocfilehash: e9d5b8858f052083b157c7d4809fe21018518bcd
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77193561"
---
# <a name="using-ansible-with-azure"></a>Použití ansible s Azure

[Ansible](https://www.ansible.com) je open source produkt, který automatizuje zřizování cloudu, správu konfigurace a nasazení aplikací. Pomocí Ansible můžete zřídit virtuální počítače, kontejnery a sítě a kompletní cloudové infrastruktury. Ansible také umožňuje automatizovat nasazení a konfiguraci prostředků ve vašem prostředí.

Tento článek poskytuje základní přehled o některé výhody používání Ansible s Azure.

## <a name="ansible-playbooks"></a>Ansible playbooky

[Ansible playbooky](https://docs.ansible.com/ansible/latest/playbooks.html) vám umožní nasměrovat Ansible ke konfiguraci vašeho prostředí. Playbooky jsou kódovány pomocí YAML tak, aby byly čitelné pro člověka. V části Kurzy uvádí mnoho příkladů použití playbooků k instalaci a konfiguraci prostředků Azure. 

## <a name="ansible-modules"></a>Ansible moduly

Ansible obsahuje sadu [modulů Ansible,](https://docs.ansible.com/ansible/latest/modules_by_category.html) které jsou spuštěny přímo na vzdálených hostitelích nebo prostřednictvím [playbooků](https://docs.ansible.com/ansible/latest/playbooks.html). Uživatelé mohou vytvářet své vlastní moduly. Moduly se používají k řízení systémových prostředků – například služeb, balíčků nebo souborů – nebo ke spouštění systémových příkazů.

Pro interakci se službami Azure ansible obsahuje sadu [modulů Ansible cloud](https://docs.ansible.com/ansible/list_of_cloud_modules.html#azure). Tyto moduly umožňují vytvářet a organizovat infrastrukturu v Azure. 

## <a name="migrate-existing-workload-to-azure"></a>Migrace existujícího pracovního vytížení do Azure

Jakmile použijete Ansible k definování infrastruktury, můžete použít playbook vaší aplikace, který umožňuje Azure automaticky škálovat vaše prostředí podle potřeby. 

## <a name="automate-cloud-native-application-in-azure"></a>Automatizace aplikací nativních pro cloud v Azure

Ansible umožňuje automatizovat nativní cloudové aplikace v Azure pomocí mikroslužeb Azure, jako jsou [Azure Functions](https://azure.microsoft.com//services/functions/) a [Kubernetes v Azure](https://azure.microsoft.com/services/container-service/kubernetes/).  

## <a name="manage-deployments-with-dynamic-inventory"></a>Správa nasazení pomocí dynamického inventáře

Prostřednictvím funkce [dynamického inventáře](https://docs.ansible.com/ansible/intro_dynamic_inventory.html) ansible poskytuje možnost vyžádat inventář z prostředků Azure. Potom můžete označit stávající nasazení Azure a spravovat tato označená nasazení prostřednictvím Ansible.

## <a name="additional-azure-marketplace-options"></a>Další možnosti Azure Marketplace

[Ansible Tower](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.ansible-tower) je image Azure Marketplace od Red Hatu. 

Ansible Tower je webové uživatelské tlačítko a řídicí panel pro ansible, který má následující funkce:

* Umožňuje definovat řízení přístupu na základě rolí, plánování úloh a grafickou správu zásob. 
* Obsahuje rozhraní REST API a rozhraní API, takže můžete vložit Věž do existujících nástrojů a procesů. 
* Podporuje výstup playbookv reálném čase. 
* Šifruje přihlašovací údaje – například klíče Azure a SSH – takže můžete delegovat úkoly bez vystavení přihlašovacích údajů.

## <a name="ansible-module-and-version-matrix-for-azure"></a>Ansible modul a verze matice pro Azure

Ansible obsahuje sadu modulů pro použití při zřizování a konfiguraci prostředků Azure. Mezi tyto prostředky patří virtuální počítače, škálovací sady, síťové služby a kontejnerové služby. [Ansible matice](./ansible-matrix.md) uvádí ansible moduly pro Azure a Ansible verze, ve kterých dodávají.

## <a name="next-steps"></a>Další kroky

- [Úvodní příručka: Nasazení šablony řešení Ansible pro Azure do CentOS](./ansible-deploy-solution-template.md)
- [Úvodní příručka: Konfigurace virtuálních počítačů Linuxu v Azure pomocí Ansible](./ansible-install-configure.md)
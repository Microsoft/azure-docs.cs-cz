---
title: Ansible modul a verzi matice pro Azure
description: Ansible modul a verzi matice pro Azure
ms.service: ansible
keywords: ansible, role, matice, verze, azure, devops
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 07/02/2018
ms.topic: article
ms.openlocfilehash: c9be94d1ea77b3609f146a373574e10b7f4d4355
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37859914"
---
# <a name="ansible-module-and-version-matrix"></a>Modul a verzi matice Ansible

## <a name="ansible-modules-for-azure"></a>Ansible moduly pro Azure
Ansible se dodává se počet modulů, které mohou být provedeny přímo na vzdáleného hostitele nebo prostřednictvím playbooky.
Tento článek uvádí Ansible moduly pro Azure, která můžete zřizovat prostředky cloudu Azure, jako jsou virtuální počítače, sítě a služby kontejneru. Tyto moduly můžete získat z oficiálním vydáním Ansible nebo z následující role playbooku vydávaný microsoftem.

| Ansible modul pro Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 |  Playbook Role [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------| 
| **Compute**                    |           |                          |                          |                                  | 
| azure_rm_availabilityset                    | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_availabilityset_facts              | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_deployment                         | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_resource                           | -            | -                           | Ano          | Ano                                 | 
| azure_rm_resource_facts                     | -            | -                           | Ano          | Ano                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_virtualmachineimage_facts          | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_resourcegroup                      | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_resourcegroup_facts                | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_virtualmachine                     | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_virtualmachine_extension           | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_virtualmachine_scaleset            | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_image                              |              | Ano                         | Ano          | Ano                                 | 
| **Sítě**                    |           |                          |                          |                                  | 
| azure_rm_virtualnetwork                     | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_virtualnetwork_facts               | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_subnet                             | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_networkinterface                   | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_networkinterface_facts             | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_publicipaddress                    | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_publicipaddress_facts              | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_dnsrecordset                       | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_dnsrecordset_facts                 | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_dnszone                            | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_dnszone_facts                      | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_loadbalancer                       | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_loadbalancer_facts                 | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_appgw                              | -            | -                           | -            | Ano                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | Ano                                 | 
| azure_rm_appgwroute                         | -            | -                           | -            | Ano                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | Ano                                 |
| azure_rm_appgwroutetable                    | -            | -                           | -            | Ano                                 |
| azure_rm_securitygroup                      | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | Ano                                 | 
| **Úložiště**                    |           |                          |                          |                                  | 
| azure_rm_storageaccount                     | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_storageaccount_facts               | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_storageblob                        | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_managed_disk                       | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_managed_disk_facts                 | Ano          | Ano                         | Ano          | Ano                                 | 
| **Containers**                    |           |                          |                          |                                  | 
| azure_rm_aks                                | -            | -                           | Ano          | Ano                                 | 
| azure_rm_aks_facts                          | -            | -                           | Ano          | Ano                                 | 
| azure_rm_acs                                | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_containerinstance                  | -            | Ano                         | Ano          | Ano                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | -            | Ano                                 | 
| azure_rm_containerregistry                  | -            | Ano                         | Ano          | Ano                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | -            | Ano                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | -            | Ano                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | Ano                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | -            | Ano                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | Ano                                 | 
| **Azure Functions**                    |           |                          |                          |                                  | 
| azure_rm_functionapp                        | Ano          | Ano                         | Ano          | Ano                                 | 
| azure_rm_functionapp_facts                  | Ano          | Ano                         | Ano          | Ano                                 | 
| **Databáze**                    |           |                          |                          |                                  | 
| azure_rm_sqlserver                          | -            | Ano                         | Ano          | Ano                                 | 
| azure_rm_sqlserver_facts                    | -            | Ano                         | Ano          | Ano                                 | 
| azure_rm_sqldatabase                        | -            | Ano                         | Ano          | Ano                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | Ano                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | -            | Ano                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | Ano                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Ano                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | Ano                                 | 
| azure_rm_mysqlserver                        | -            | Ano                         | Ano          | Ano                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Ano                                 | 
| azure_rm_mysqldatabase                      | -            | Ano                         | Ano          | Ano                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Ano                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | Ano                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | Ano                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | Ano                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | Ano                                 | 
| azure_rm_postgresqlserver                   | -            | Ano                         | Ano          | Ano                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Ano                                 | 
| azure_rm_postgresqldatabase                 | -            | Ano                         | Ano          | Ano                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Ano                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | Ano                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | Ano                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | Ano                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | Ano                                 | 
| **Key Vault**                    |           |                          |                          |                                  | 
| azure_rm_keyvault                           | -            | Ano                         | Ano          | Ano                                 |
| azure_rm_keyvault_facts                     | -            | -                           | -            | Ano                                 |
| azure_rm_keyvaultkey                        | -            | Ano                         | Ano          | Ano                                 |
| azure_rm_keyvaultsecret                     | -            | Ano                         | Ano          | Ano                                 |


## <a name="introduction-to-playbook-role-for-azure"></a>Úvod do playbook role pro Azure
[Role playbooku azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) je role, nejúplnější a zahrnuje všechny nejnovější moduly Azure. Aktualizace a opravy chyb dokončení včas více než oficiálním vydáním Ansible. Pokud pro účely zřizování prostředků Azure pomocí Ansible, jste ukončena. doporučujeme nainstalovat roli azure_preview_module.

Role playbooku azure_preview_module vydání každé tři týdny.

## <a name="next-steps"></a>Další postup
Další informace týkající se role playbooku naleznete [vytvoření opakovaně použitelného Playbooky](http://docs.ansible.com/ansible/latest/playbooks_reuse.html). 

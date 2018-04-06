---
title: Matice Ansible modul a verze pro Azure.
description: Matice Ansible modul a verze pro Azure.
ms.service: ansible
keywords: ansible, role, matice, verze, azure, devops
author: tomarcher
manager: routlaw
ms.author: tarcher
ms.date: 03/25/2018
ms.topic: article
ms.openlocfilehash: 011cb173ffdecc7a22c2e470209719ccaf6bda58
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="ansible-module-and-version-matrix"></a>Matice Ansible modul a verze

## <a name="ansible-modules-for-azure"></a>Moduly Ansible pro Azure.
Ansible se dodává s číslem modulů, které mohou být provedeny přímo na vzdáleným hostitelům nebo prostřednictvím playbooks.
V tomto článku jsou uvedené moduly Ansible pro Azure, který můžete zřizovat Azure cloudové prostředky, jako je například virtuální počítač, sítě a služby kontejneru. Tyto moduly můžete získat z oficiálního vydání Ansible nebo z následujících rolí playbook publikovaný microsoftem.

| Modul Ansible pro Azure.                   |  Ansible 2.4 |  Ansible 2.5 |  Playbook Role [azure_preview_module](#introduction-to-azurepreviewmodule) | 
|---------------------------------------------|--------------|-----------------------------|-------------------------------------| 
| **Compute**                    |           |                          |                                  | 
| azure_rm_availabilityset                    | Ano          | Ano                         | Ano                                 | 
| azure_rm_availabilityset_facts              | Ano          | Ano                         | Ano                                 | 
| azure_rm_deployment                         | Ano          | Ano                         | Ano                                 | 
| azure_rm_virtualmachine_scaleset_facts      | Ano          | Ano                         | Ano                                 | 
| azure_rm_virtualmachineimage_facts          | Ano          | Ano                         | Ano                                 | 
| azure_rm_resourcegroup                      | Ano          | Ano                         | Ano                                 | 
| azure_rm_resourcegroup_facts                | Ano          | Ano                         | Ano                                 | 
| azure_rm_virtualmachine                     | Ano          | Ano                         | Ano                                 | 
| azure_rm_virtualmachine_extension           | Ano          | Ano                         | Ano                                 | 
| azure_rm_virtualmachine_scaleset            | Ano          | Ano                         | Ano                                 | 
| azure_rm_image                              |              | Ano                         | Ano                                 | 
| **Sítě**                    |           |                          |                                  | 
| azure_rm_virtualnetwork                     | Ano          | Ano                         | Ano                                 | 
| azure_rm_virtualnetwork_facts               | Ano          | Ano                         | Ano                                 | 
| azure_rm_subnet                             | Ano          | Ano                         | Ano                                 | 
| azure_rm_networkinterface                   | Ano          | Ano                         | Ano                                 | 
| azure_rm_networkinterface_facts             | Ano          | Ano                         | Ano                                 | 
| azure_rm_publicipaddress                    | Ano          | Ano                         | Ano                                 | 
| azure_rm_publicipaddress_facts              | Ano          | Ano                         | Ano                                 | 
| azure_rm_dnsrecordset                       | Ano          | Ano                         | Ano                                 | 
| azure_rm_dnsrecordset_facts                 | Ano          | Ano                         | Ano                                 | 
| azure_rm_dnszone                            | Ano          | Ano                         | Ano                                 | 
| azure_rm_dnszone_facts                      | Ano          | Ano                         | Ano                                 | 
| azure_rm_loadbalancer                       | Ano          | Ano                         | Ano                                 | 
| azure_rm_loadbalancer_facts                 | Ano          | Ano                         | Ano                                 | 
| azure_rm_appgw                              | -            | -                           | Ano                                 | 
| azure_rm_appgwroute                         | -            | -                           | Ano                                 | 
| azure_rm_appgwroute                         | -            | -                           | Ano                                 |
| azure_rm_appgwroute_facts                   | -            | -                           | Ano                                 |
| azure_rm_appgwroutetable                    | -            | -                           | Ano                                 |
| azure_rm_securitygroup                      | Ano          | Ano                         | Ano                                 | 
| azure_rm_appgwroutetable_facts              | -            | -                           | Ano                                 | 
| **Úložiště**                    |           |                          |                                  | 
| azure_rm_storageaccount                     | Ano          | Ano                         | Ano                                 | 
| azure_rm_storageaccount_facts               | Ano          | Ano                         | Ano                                 | 
| azure_rm_storageblob                        | Ano          | Ano                         | Ano                                 | 
| azure_rm_managed_disk                       | Ano          | Ano                         | Ano                                 | 
| azure_rm_managed_disk_facts                 | Ano          | Ano                         | Ano                                 | 
| **Kontejnery**                    |           |                          |                                  | 
| azure_rm_acs                                | Ano          | Ano                         | Ano                                 | 
| azure_rm_containerinstance                  | -            | Ano                         | Ano                                 | 
| azure_rm_containerinstance_facts            | -            | -                           | Ano                                 | 
| azure_rm_containerregistry                  | -            | Ano                         | Ano                                 | 
| azure_rm_containerregistry_facts            | -            | -                           | Ano                                 | 
| azure_rm_containerregistryreplication       | -            | -                           | Ano                                 | 
| azure_rm_containerregistryreplication_facts | -            | -                           | Ano                                 | 
| azure_rm_containerregistrywebhook           | -            | -                           | Ano                                 | 
| azure_rm_containerregistrywebhook_facts     | -            | -                           | Ano                                 | 
| **Azure Functions**                    |           |                          |                                  | 
| azure_rm_functionapp                        | Ano          | Ano                         | Ano                                 | 
| azure_rm_functionapp_facts                  | Ano          | Ano                         | Ano                                 | 
| **Databáze**                    |           |                          |                                  | 
| azure_rm_sqlserver                          | -            | Ano                         | Ano                                 | 
| azure_rm_sqlserver_facts                    | -            | Ano                         | Ano                                 | 
| azure_rm_sqldatabase                        | -            | Ano                         | Ano                                 | 
| azure_rm_sqldatabase_facts                  | -            | -                           | Ano                                 | 
| azure_rm_sqlelasticpool                     | -            | -                           | Ano                                 | 
| azure_rm_sqlelasticpool_facts               | -            | -                           | Ano                                 | 
| azure_rm_sqlfirewallrule                    | -            | -                           | Ano                                 | 
| azure_rm_sqlfirewallrule_facts              | -            | -                           | Ano                                 | 
| azure_rm_mysqlserver                        | -            | Ano                         | Ano                                 | 
| azure_rm_mysqlserver_facts                  | -            | -                           | Ano                                 | 
| azure_rm_mysqldatabase                      | -            | Ano                         | Ano                                 | 
| azure_rm_mysqldatabase_facts                | -            | -                           | Ano                                 | 
| azure_rm_mysqlfirewallrule                  | -            | -                           | Ano                                 | 
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | Ano                                 | 
| azure_rm_mysqlconfiguration                 | -            | -                           | Ano                                 | 
| azure_rm_mysqlconfiguration_facts           | -            | -                           | Ano                                 | 
| azure_rm_postgresqlserver                   | -            | Ano                         | Ano                                 | 
| azure_rm_postgresqlserver_facts             | -            | -                           | Ano                                 | 
| azure_rm_postgresqldatabase                 | -            | Ano                         | Ano                                 | 
| azure_rm_postgresqldatabase_facts           | -            | -                           | Ano                                 | 
| azure_rm_postgresqlfirewallrule             | -            | -                           | Ano                                 | 
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | Ano                                 | 
| azure_rm_postgresqlconfiguration            | -            | -                           | Ano                                 | 
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | Ano                                 | 
| **Key Vault**                    |           |                          |                                  | 
| azure_rm_keyvault                           | -            | Ano                         | Ano                                 |
| azure_rm_keyvault_facts                     | -            | -                           | Ano                                 |
| azure_rm_keyvaultkey                        | -            | Ano                         | Ano                                 |
| azure_rm_keyvaultsecret                     | -            | Ano                         | Ano                                 |


## <a name="introduction-to-playbook-role-for-azure"></a>Úvod do role scénářem pro Azure.
[Azure_preview_module playbook role](https://galaxy.ansible.com/Azure/azure_preview_modules/) je nejúplnější role a obsahuje nejnovější Azure modulů. Aktualizace a opravy chyb hotovi včas více než oficiální Ansible verze. Pokud používáte Ansible pro účely zřizování prostředků Azure, můžete se doporučujeme nainstalovat roli azure_preview_module.

Azure_preview_module playbook role vydání každé tři týdny.

## <a name="next-steps"></a>Další postup
Další informace související se scénářem rolí, najdete na [vytváření opakovaně použitelného Playbooks](http://docs.ansible.com/ansible/latest/playbooks_reuse.html). 

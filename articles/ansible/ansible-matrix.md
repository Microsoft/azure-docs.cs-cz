---
title: Ansible modul a verzi matice pro Azure | Dokumentace Microsoftu
description: Ansible modul a verzi matice pro Azure
keywords: ansible, role, matice, verze, azure, devops
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/22/2019
ms.openlocfilehash: 721179e12ed7f21312fe848a6bef1a8e19bc8083
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64866052"
---
# <a name="ansible-module-and-version-matrix"></a>Modul a verzi matice Ansible

Ansible obsahuje sadu modulů pro použití v zřizování a konfiguraci prostředků Azure. Tyto prostředky zahrnují virtuální počítače, škálovací sady, síťové služby a služby kontejneru. Tento článek uvádí různé Ansible moduly pro Azure a verze Ansible, ve kterých dodání.

## <a name="ansible-modules-for-azure"></a>Ansible moduly pro Azure

Následující moduly mohou být provedeny přímo na vzdáleného hostitele nebo prostřednictvím playbooky.

Tyto moduly jsou dostupné z oficiálním vydáním Ansible a následující role playbooku Microsoft.

| Ansible modul pro Azure                   |  Ansible 2.4 |  Ansible 2.5 |  Ansible 2.6 | Ansible 2.7 | Ansible 2.8 | Ansible Role | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Compute**                    |           |                          |                          |                            |           |           |
| azure_rm_availabilityset                    | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_availabilityset_facts              | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_deployment                         | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_deployment_facts                   | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_functionapp                        | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_functionapp_facts                  | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_image                              | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_image_facts                        | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_resource                           | -            | -                           | Ano          | Ano          | Ano          | Ano          |
| azure_rm_resource_facts                     | -            | -                           | Ano          | Ano          | Ano          | Ano          |
| azure_rm_resourcegroup                      | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_resourcegroup_facts                | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachine                     | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachine_facts               | -            | -                           | -            | Ano          | Ano          | Ano          |
| azure_rm_virtualmachineextension           | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachineextension_facts      | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_virtualmachineimage_facts          | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachinescaleset            | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachinescaleset_facts      | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_virtualmachinescalesetextension_facts | -            | -                        | -            | -            | Ano          | Ano          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_virtualmachinescalesetinstance_facts | -            | -                         | -            | -            | Ano          | Ano          |
| **Sítě**                              |              |                             |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Ano          | Ano          | Ano          |
| azure_rm_appgwroute                         | -            | -                           | -            | -            | -          | Ano          |
| azure_rm_appgwroute_facts                   | -            | -                           | -            | -            | -          | Ano          |
| azure_rm_appgwroutetable                    | -            | -                           | -            | -            | -          | Ano          |
| azure_rm_appgwroutetable_facts              | -            | -                           | -            | -            | -          | Ano          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_applicationsecuritygroup_facts     | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Ano          | Ano          |
| azure_rm_cdnendpoint_facts                  | -            | -                         | -          | -            | Ano          | Ano          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Ano          | Ano          |
| azure_rm_cdnprofile_facts                   | -            | -                         | -          | -            | Ano          | Ano          |
| azure_rm_dnsrecordset                       | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_dnsrecordset_facts                 | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_dnszone                            | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_dnszone_facts                      | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_loadbalancer                       | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_loadbalancer_facts                 | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_networkinterface                   | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_networkinterface_facts             | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_publicipaddress                    | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_publicipaddress_facts              | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_route                              | -            | -                           | -            | Ano          | Ano          | Ano          |
| azure_rm_routetable                         | -            | -                           | -            | Ano          | Ano          | Ano          |
| azure_rm_routetable_facts                   | -            | -                           | -            | Ano          | Ano          | Ano          |
| azure_rm_securitygroup                      | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_subnet                             | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_subnet_facts                       | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_trafficmanagerendpoint_facts       | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_trafficmanagerprofile_facts        | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_virtualnetwork                     | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualnetwork_facts               | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Ano          | Ano          |
| **Storage**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_manageddisk_facts                  | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_storageaccount                     | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_storageaccount_facts               | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_storageblob                        | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| **Web**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_webapp                             | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Ano          | Ano          |
| **Containers**                    |           |                          |                          |                            |           |           |
| azure_rm_acs                                | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_aks                                | -            | -                           | Ano          | Ano          | Ano          | Ano          |
| azure_rm_aks_facts                          | -            | -                           | Ano          | Ano          | Ano          | Ano          |
| azure_rm_aksversion_facts                   | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_containerinstance                  | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_containerinstance_facts            | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_containerregistry                  | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_containerregistry_facts            | -            | -                           | -            | Ano          | Ano          | Ano          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_containerregistryreplication_facts | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_containerregistrywebhook_facts     | -            | -                           | -            | -            | Ano          | Ano          |
| **Databáze**                    |           |                          |                          |                             |           |           |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_cosmosdbaccount_facts              | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mariadbconfiguration_facts         | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mariadbdatabase_facts              | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mariadbfirewallrule_facts          | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mariadbserver_facts                | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mysqlconfiguration_facts           | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mysqldatabase                      | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_mysqldatabase_facts                | -            | -                           | -            | Ano          | Ano          | Ano          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mysqlfirewallrule_facts            | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_mysqlserver                        | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_mysqlserver_facts                  | -            | -                           | -            | Ano          | Ano          | Ano          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_postgresqlconfiguration_facts      | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_postgresqldatabase                 | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_postgresqldatabase_facts           | -            | -                           | -            | Ano          | Ano          | Ano          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_postgresqlfirewallrule_facts       | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_postgresqlserver                   | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_postgresqlserver_facts             | -            | -                           | -            | Ano          | Ano          | Ano          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_rediscache_facts                   | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_sqldatabase                        | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_sqldatabase_facts                  | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_sqlelasticpool                     | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_sqlelasticpool_facts               | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Ano          | Ano          | Ano          |
| azure_rm_sqlfirewallrule_facts              | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_sqlserver                          | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_sqlserver_facts                    | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| **Analýzy**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Ano          | Ano          |
| **Integrace**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Ano          | Ano          |
| **Zabezpečení**                    |           |                          |                          |                             |           |           |
| azure_rm_keyvault                           | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_keyvault_facts                     | -            | -                           | -              | -          | Ano          | Ano          |
| azure_rm_keyvaultkey                        | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_keyvaultsecret                     | -            | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_roleassignment_facts               | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_roledefinition_facts               | -            | -                           | -            | -            | Ano          | Ano          |
| **DevOps**               |           |                          |                          |                             |           |           |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlab_facts                   | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabarmtemplate_facts        | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabartifact_facts           | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabartifactsource_facts     | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabvirtualmachine_facts | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabvirtualnetwork_facts     | -            | -                           | -            | -            | Ano          | Ano          |
| **Azure Monitor**          |           |                          |                          |                             |           |           |
| azure_rm_autoscale                  | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_autoscale_facts            | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_loganalyticsworkspace_facts        | -            | -                           | -            | -            | Ano          | Ano          |

## <a name="introduction-to-playbook-role-for-azure"></a>Úvod do playbook role pro Azure

[Role playbooku azure_preview_module](https://galaxy.ansible.com/Azure/azure_preview_modules/) zahrnuje všechny nejnovější moduly Azure. Aktualizace a opravy chyb dokončení včas více než oficiálním vydáním Ansible. Pokud pro účely zřizování prostředků Azure pomocí Ansible, jste ukončena. doporučujeme nainstalovat `azure_preview_module` playbook role.

`azure_preview_module` Role playbooku vydání každé tři týdny.

## <a name="next-steps"></a>Další postup

Další informace o rolích playbooku, naleznete v tématu [vytváření opakovaně použitelných playbooky](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 

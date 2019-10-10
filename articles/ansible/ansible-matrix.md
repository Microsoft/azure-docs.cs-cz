---
title: Modul Ansible a matice verzí pro Azure
description: Modul Ansible a matice verzí pro Azure
keywords: Ansible, role, matice, verze, Azure, DevOps
ms.topic: reference
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/30/2019
ms.openlocfilehash: 18654500a78178c46e72f9f6cd01e8507fa179f0
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72241548"
---
# <a name="ansible-module-and-version-matrix"></a>Ansible modul a verze matice

Ansible zahrnuje sadu modulů pro použití při zřizování a konfiguraci prostředků Azure. Mezi tyto prostředky patří virtuální počítače, sady škálování, síťové služby a služby kontejneru. V tomto článku jsou uvedené různé moduly Ansible pro Azure a verze Ansible, ve kterých se dodávají.

## <a name="ansible-modules-for-azure"></a>Moduly Ansible pro Azure

Následující moduly lze spustit přímo na vzdálených hostitelích nebo prostřednictvím playbooky.

Tyto moduly jsou k dispozici v oficiální verzi Ansible a od následujících rolí Microsoft PlayBook.

| Modul Ansible pro Azure                   |  Ansible 2,4 |  Ansible 2,5 |  Ansible 2,6 | Ansible 2,7 | Ansible 2,8 | Role Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------| 
| **Výpočetní**                    |           |                          |                          |                            |           |           |
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
| **Pamì**                    |           |                          |                          |                            |           |           |
| azure_rm_manageddisk                        | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_manageddisk_facts                  | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_storageaccount                     | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_storageaccount_facts               | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| azure_rm_storageblob                        | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          |
| **Webovém**                    |           |                          |                          |                             |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_appserviceplan_facts               | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_webapp                             | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_webapp_facts                       | -            | -                         | -          | Ano          | Ano          | Ano          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Ano          | Ano          |
| **Kontejnery**                    |           |                          |                          |                            |           |           |
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
| **Analytics**                    |           |                          |                          |                             |           |           |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Ano          | Ano          |
| **Spolupráci**                    |           |                          |                          |                             |           |           |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_servicebus_facts                   | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Ano          | Ano          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Ano          | Ano          |
| **Bezpečnost**                    |           |                          |                          |                             |           |           |
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

## <a name="introduction-to-playbook-role-for-azure"></a>Seznámení s rolí PlayBook pro Azure

[Role azure_preview_module PlayBook](https://galaxy.ansible.com/Azure/azure_preview_modules/) zahrnuje všechny nejnovější moduly Azure. Aktualizace a opravy chyb jsou prováděny efektivněji než oficiální verze Ansible. Pokud pro účely zřizování prostředků Azure používáte Ansible, doporučujeme @no__t nainstalovat roli PlayBook-0.

Role PlayBook-0 je vydaná každé tři týdny. @no__t

## <a name="next-steps"></a>Další kroky

Další informace o rolích PlayBook najdete v tématu [vytvoření opakovaně použitelné playbooky](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 

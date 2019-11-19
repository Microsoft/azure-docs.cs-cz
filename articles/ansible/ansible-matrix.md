---
title: Modul Ansible a matice verzí pro Azure | Microsoft Docs
description: Modul Ansible a matice verzí pro Azure
keywords: Ansible, role, matice, verze, Azure, DevOps
ms.topic: reference
ms.date: 10/14/2019
ms.openlocfilehash: 54e27c7570ba1cdbce7355740181d68a2f3efbac
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74155988"
---
# <a name="ansible-module-and-version-matrix"></a>Modul Ansible a matice verzí

Ansible zahrnuje sadu modulů pro použití při zřizování a konfiguraci prostředků Azure. Mezi tyto prostředky patří virtuální počítače, sady škálování, síťové služby a služby kontejneru. V tomto článku jsou uvedené různé moduly Ansible pro Azure a verze Ansible, ve kterých se dodávají.

## <a name="ansible-modules-for-azure"></a>Moduly Ansible pro Azure

Následující moduly lze spustit přímo na vzdálených hostitelích nebo prostřednictvím playbooky.  

Tyto moduly jsou k dispozici v oficiální verzi Ansible a od následujících rolí Microsoft PlayBook.

> [!NOTE]
> Od Ansible 2,9 a dalších jsme přejmenovali všechny * _facts moduly na * _info, aby odpovídaly konvenci pojmenování Ansible. Staré a přejmenované moduly jsou propojené, takže nezávisle na zobrazení upozornění na zastaralost budou všechny moduly fungovat stejně jako dřív.

| Modul Ansible pro Azure                   |  Ansible 2,4 |  Ansible 2,5 |  Ansible 2,6 | Ansible 2,7 | Ansible 2,8 | Ansible 2,9 | Role Ansible | 
|---------------------------------------------|--------------|--------------|-----------------------------|-------------------------------------|--------------|--------------|--------------|  
| **Compute**                    |           |                          |                          |                            |           |           |           |
| azure_rm_availabilityset                   | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_availabilityset_info              | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_batchaccount                       | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_deployment                         | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_deployment_info                   | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_functionapp                        | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_functionapp_info                  | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_gallery                            | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_gallery_info                       | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_galleryimage                       | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_galleryimage_info                  | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_galleryimageversion                | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_galleryimageversion_info           | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_image                              | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_image_info                        | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_resource                           | -            | -                           | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_resource_info                     | -            | -                           | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_resourcegroup                      | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_resourcegroup_info                | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_snapshot                           | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_virtualmachine                     | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachine_info               | -            | -                           | -            | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachineextension            | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachineextension_info      | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_virtualmachineimage_info          | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachinescaleset             | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachinescaleset_info       | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualmachinescalesetextension    | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_virtualmachinescalesetextension_info | -            | -                        | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_virtualmachinescalesetinstance     | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_virtualmachinescalesetinstance_info | -            | -                         | -            | -            | Ano          | Ano          | Ano          |
| **Sítě**                              |              |                             |              |              |              |              |              |
| azure_rm_appgateway                         | -            | -                           | -            | Ano          | Ano          | Ano          | Ano          |
| azure_rm_applicationsecuritygroup           | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_applicationsecuritygroup_info     | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_cdnendpoint                        | -            | -                         | -          | -            | Ano          | Ano          | Ano          |
| azure_rm_cdnendpoint_info                  | -            | -                         | -          | -            | Ano          | Ano          | Ano          |
| azure_rm_cdnprofile                         | -            | -                         | -          | -            | Ano          | Ano          | Ano          |
| azure_rm_cdnprofile_info                   | -            | -                         | -          | -            | Ano          | Ano          | Ano          |
| azure_rm_dnsrecordset                       | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_dnsrecordset_info                 | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_dnszone                            | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_dnszone_info                      | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_firewall                           | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_firewall_info                      | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_loadbalancer                       | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_loadbalancer_info                 | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_networkinterface                   | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_networkinterface_info             | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_publicipaddress                    | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_publicipaddress_info              | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_route                              | -            | -                           | -            | Ano          | Ano          | Ano          | Ano          |
| azure_rm_routetable                         | -            | -                           | -            | Ano          | Ano          | Ano          | Ano          |
| azure_rm_routetable_info                   | -            | -                           | -            | Ano          | Ano          | Ano          | Ano          |
| azure_rm_securitygroup                      | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_securitygroup_info                 | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_subnet                             | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_subnet_info                       | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_trafficmanagerendpoint             | -            | -                         | -          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_trafficmanagerendpoint_info       | -            | -                         | -          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_trafficmanagerprofile              | -            | -                         | -          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_trafficmanagerprofile_info        | -            | -                         | -          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualnetwork                     | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualnetwork_info               | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_virtualnetworkgateway              | -            | -                         | -          | -            | Ano          | Ano          | Ano          |
| azure_rm_virtualnetworkpeering              | -            | -                         | -          | -            | Ano          | Ano          | Ano          |
| azure_rm_virtualnetworkpeering_info         | -            | -                         | -          | -            | -            | Ano          | Ano          |
| **Storage**                    |           |                          |                          |                            |           |           |         |
| azure_rm_manageddisk                        | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_manageddisk_info                  | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_storageaccount                     | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_storageaccount_info               | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_storageblob                        | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| **Web**                    |           |                          |                          |                             |           |           |           |
| azure_rm_appserviceplan                     | -            | -                         | -          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_appserviceplan_info               | -            | -                         | -          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_webapp                             | -            | -                         | -          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_webapp_info                       | -            | -                         | -          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_webappslot                         | -            | -                         | -          | -            | Ano          | Ano          | Ano          |
| **Kontejnery**                    |           |                          |                          |                            |           |           |          |
| azure_rm_acs                                | Ano          | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_aks                                | -            | -                           | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_aks_info                          | -            | -                           | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_aksversion_info                   | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_containerinstance                  | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_containerinstance_info            | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_containerregistry                  | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_containerregistry_info            | -            | -                           | -            | Ano          | Ano          | Ano          | Ano          |
| azure_rm_containerregistryreplication       | -            | -                           | -            | -            | -          | -          | Ano          |
| azure_rm_containerregistryreplication_info  | -            | -                           | -            | -            | -          | -          | Ano          |
| azure_rm_containerregistrywebhook           | -            | -                           | -            | -            | -          | -          | Ano          |
| azure_rm_containerregistrywebhook_info      | -            | -                           | -            | -            | -          | -          | Ano          |
| **Databáze**                    |           |                          |                          |                             |           |           |          |
| azure_rm_cosmosdbaccount                    | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_cosmosdbaccount_info              | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mariadbconfiguration               | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mariadbconfiguration_info         | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mariadbdatabase                    | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mariadbdatabase_info              | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mariadbfirewallrule                | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mariadbfirewallrule_info          | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mariadbserver                      | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mariadbserver_info                | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mysqlconfiguration                 | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mysqlconfiguration_info           | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mysqldatabase                      | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_mysqldatabase_info                | -            | -                           | -            | Ano          | Ano          | Ano          | Ano          |
| azure_rm_mysqlfirewallrule                  | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mysqlfirewallrule_info            | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_mysqlserver                        | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_mysqlserver_info                  | -            | -                           | -            | Ano          | Ano          | Ano          | Ano          |
| azure_rm_postgresqlconfiguration            | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_postgresqlconfiguration_info      | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_postgresqldatabase                 | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_postgresqldatabase_info           | -            | -                           | -            | Ano          | Ano          | Ano          | Ano          |
| azure_rm_postgresqlfirewallrule             | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_postgresqlfirewallrule_info       | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_postgresqlserver                   | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_postgresqlserver_info             | -            | -                           | -            | Ano          | Ano          | Ano          | Ano          |
| azure_rm_rediscache                         | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_rediscache_info                   | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_rediscachefirewallrule             | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_sqldatabase                        | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_sqldatabase_info                  | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_sqlelasticpool                    | -            | -                           | -            | -            | -          | -          | Ano          |
| azure_rm_sqlelasticpool_info               | -            | -                           | -            | -            | -          | -          | Ano          |
| azure_rm_sqlfirewallrule                    | -            | -                           | -            | Ano          | Ano          | Ano          | Ano          |
| azure_rm_sqlfirewallrule_info              | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_sqlserver                          | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_sqlserver_info                    | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| **Analýzy**                    |           |                          |                          |                             |           |           |          |
| azure_rm_hdinsightcluster                   | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_hdinsightcluster_info              | -            | -                           | -            | -            | -            | Ano          | Ano          |
| **Spolupráci**                    |           |                          |                          |                             |           |           |          |
| azure_rm_servicebus                         | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_servicebus_info                   | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_servicebusqueue                    | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_servicebussaspolicy                | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_servicebustopic                    | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_servicebustopicsubscription        | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| **Zabezpečení**                    |           |                          |                          |                             |           |           |           |
| azure_rm_keyvault                           | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_keyvault_info                     | -            | -                           | -              | -          | Ano          | Ano          | Ano          |
| azure_rm_keyvaultkey                        | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_keyvaultkey_info                   | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_keyvaultsecret                     | -            | Ano                         | Ano          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_roleassignment                     | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_roleassignment_info               | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_roledefinition                     | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_roledefinition_info               | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| **DevOps**               |           |                          |                          |                             |           |           |                  |
| azure_rm_devtestlab                         | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlab_info                   | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabarmtemplate_info        | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabartifact_info           | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabartifactsource           | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabartifactsource_info     | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabcustomimage              | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabcustomimage_info         | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabenvironment              | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabenvironment_info         | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabpolicy                   | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabpolicy_info              | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabschedule                 | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabschedule_info            | -            | -                           | -            | -            | -            | Ano          | Ano          |
| azure_rm_devtestlabvirtualmachine           | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabvirtualmachine_info | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabvirtualnetwork           | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_devtestlabvirtualnetwork_info     | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| **Azure Monitor**                           |              |                           |            |              |                 |           |              |
| azure_rm_autoscale                          | -            | -                         | -          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_autoscale_info                    | -            | -                         | -          | Ano          | Ano          | Ano          | Ano          |
| azure_rm_loganalyticsworkspace              | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_loganalyticsworkspace_info        | -            | -                           | -            | -            | Ano          | Ano          | Ano          |
| azure_rm_monitorlogprofile                  | -            | -                           | -            | -            | -            | Ano          | Ano          |
| **Správa a zásady správného řízení**     |              |                           |            |            |            |            |              |
| azure_rm_automationaccount        | -            | -                         | -          | -          | -          | Ano        | Ano          |
| azure_rm_automationaccount_info   | -            | -                         | -          | -          | -          | Ano        | Ano          |
| azure_rm_lock                     | -            | -                         | -          | -          | -          | Ano        | Ano          |
| azure_rm_lock_info                | -            | -                         | -          | -          | -          | Ano        | Ano          |
| **Internet věcí**            |              |                           |            |            |            |            |              |
| azure_rm_iotdevice                | -            | -                         | -          | -          | -          | Ano        | Ano          |
| azure_rm_iotdevice_info           | -            | -                         | -          | -          | -          | Ano        | Ano          |
| azure_rm_iotdevicemodule          | -            | -                         | -          | -          | -          | Ano        | Ano          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Ano        | Ano          |
| azure_rm_iothub_info              | -            | -                         | -          | -          | -          | Ano        | Ano          |
| azure_rm_iothubconsumergroup      | -            | -                         | -          | -          | -          | Ano        | Ano          |

## <a name="introduction-to-playbook-role-for-azure"></a>Seznámení s rolí PlayBook pro Azure

[Role azure_preview_module PlayBook](https://galaxy.ansible.com/Azure/azure_preview_modules/) zahrnuje všechny nejnovější moduly Azure. Aktualizace a opravy chyb jsou prováděny efektivněji než oficiální verze Ansible. Pokud pro účely zřizování prostředků Azure používáte Ansible, doporučujeme nainstalovat roli `azure_preview_module` PlayBook.

Role `azure_preview_module` PlayBook je vydaná každé tři týdny.

## <a name="next-steps"></a>Další kroky

Další informace o rolích PlayBook najdete v tématu [vytvoření opakovaně použitelné playbooky](https://docs.ansible.com/ansible/latest/playbooks_reuse.html). 
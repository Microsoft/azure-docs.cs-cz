---
title: Zdroje bez limitu počtu 800
description: Uvádí typy prostředků Azure, které mohou mít více než 800 instancí ve skupině prostředků.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 8644bec1a68acebff18cf83d17acb014784dc964
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804771"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Prostředky nejsou omezeny na 800 instancí na skupinu prostředků

Ve výchozím nastavení můžete v každé skupině prostředků nasadit až 800 instancí typu prostředku. Některé typy prostředků jsou však osvobozeny od limitu instance 800. Tento článek uvádí typy prostředků Azure, které mohou mít více než 800 instancí ve skupině prostředků. Všechny ostatní typy prostředků jsou omezeny na 800 instancí.

U některých typů prostředků je třeba kontaktovat podporu, aby byl odebrán limit instance 800. Tyto typy prostředků jsou uvedeny v tomto článku.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* Registrace
* registrace/odběry zákazníků
* registrace/produkty

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices - Ve výchozím nastavení, omezena na 800 instancí. Tento limit lze zvýšit kontaktováním podpory.

## <a name="microsoftcompute"></a>Microsoft.Compute

* Disky
* Galerie
* galerie/obrázky
* galerie/obrázky/verze
* images
* snímky
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registry/buildTasks
* registry/buildTasks/listSourceRepositoryProperties
* registry/buildÚkoly/kroky
* registry/buildTasks/steps/listBuildArguments
* registry/eventGridFilters
* registry/replikace
* rejstříky/úkoly
* registry/webhooky

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* Servery

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* Servery

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverové skupiny
* Servery
* serveryv2
* singleServers

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* plány - Ve výchozím nastavení, omezena na 800 instancí. Tento limit lze zvýšit kontaktováním podpory.

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* Clustery
* Obory názvů

## <a name="microsoftexperimentation"></a>Microsoft.Experimentování

* experimentworkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* konfiguracePřiřazení profilů
* guestConfigurationAssignments
* Software
* softwareUpdateProfile
* softwareAktualizace

## <a name="microsoftinsights"></a>microsoft.insights

* upozornění na metriky

## <a name="microsoftlogic"></a>Microsoft.Logic

* integraceÚčty
* Pracovní postupy

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPool/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots netAppAccounts/capacityPools/volumes/snapshots netAppAccounts/capacityPools/volumes/snapshots netApp

## <a name="microsoftnetwork"></a>Microsoft.Network

* aplikaceGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszóny
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/vše
* dnszones/sady záznamů
* networkIntentPolicies
* síťová rozhraní
* privátnídnszóny
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/vše
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses - Ve výchozím nastavení je omezen na 800 instancí. Tento limit lze zvýšit kontaktováním podpory.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkKohouty

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections – ve výchozím nastavení je omezen na 800 instancí. Tento limit lze zvýšit kontaktováním podpory.

## <a name="microsoftrelay"></a>Microsoft.Relay

* Obory názvů

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* Obory názvů

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

* aplikace
* containerGroups
* Brány
* Sítí
* Tajemství
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* Stránky

## <a name="next-steps"></a>Další kroky

Úplný seznam kvót a omezení najdete v tématu [Limity předplatného azure a služby, kvóty a omezení](azure-subscription-service-limits.md).

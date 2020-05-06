---
title: Prostředky bez omezení počtu 800
description: Obsahuje seznam typů prostředků Azure, které můžou mít víc než 800 instancí ve skupině prostředků.
ms.topic: conceptual
author: davidsmatlak
ms.author: v-dasmat
ms.date: 05/04/2020
ms.openlocfilehash: 892b59b3d3e980abfcdb9cd692c2598ceb1284ad
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780925"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Prostředky, které nejsou omezeny na 800 instancí na skupinu prostředků

Ve výchozím nastavení můžete do každé skupiny prostředků nasadit až 800 instancí typu prostředku. Některé typy prostředků jsou ale z limitu instance 800 vyloučené. V tomto článku jsou uvedené typy prostředků Azure, které můžou mít víc než 800 instancí ve skupině prostředků. Všechny ostatní typy prostředků jsou omezeny na 800 instancí.

U některých typů prostředků musíte kontaktovat podporu, aby byl limit instance 800 odebraný. Tyto typy prostředků jsou uvedeny v tomto článku.

## <a name="microsoftautomation"></a>Microsoft. Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

* rozpoznávání
* registrace/customerSubscriptions
* registrace/produkty

## <a name="microsoftbotservice"></a>Microsoft. BotService

* botServices – ve výchozím nastavení omezen na 800 instancí. Toto omezení se dá zvýšit kontaktováním podpory.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disků
* Galerie
* Galerie/image
* Galerie/image/verze
* images
* snímky
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

* Registry/buildTasks
* Registry/buildTasks/listSourceRepositoryProperties
* Registry/buildTasks/kroky
* Registry/buildTasks/Steps/listBuildArguments
* Registry/eventGridFilters
* Registry/replikace
* Registry/úlohy
* Registry a Webhooky

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

* servery

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

* servery

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

* serverGroups
* servery
* serversv2
* singleServers

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

* plány – ve výchozím nastavení omezeny na 800 instancí. Toto omezení se dá zvýšit kontaktováním podpory.

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft. EventHub

* existující
* obsažené

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* Vybavení
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftinsights"></a>Microsoft. Insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft. Logic

* integrationAccounts
* Zpracovávaný

## <a name="microsoftnetapp"></a>Microsoft. NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/svazky
* netAppAccounts/capacityPools/svazky/mountTargets
* netAppAccounts/capacityPools/svazky/snímky

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
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
* networkInterfaces
* privateDnsZones
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
* publicIPAddresses – ve výchozím nastavení omezen na 800 instancí. Toto omezení se dá zvýšit kontaktováním podpory.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

* workspaceCollections – ve výchozím nastavení omezen na 800 instancí. Toto omezení se dá zvýšit kontaktováním podpory.

## <a name="microsoftrelay"></a>Microsoft. Relay

* obsažené

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* obsažené

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

* aplikace
* containerGroups
* brány
* sítí
* záleží
* volumes

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft. Web

* apiManagementAccounts/rozhraní API
* místa

## <a name="next-steps"></a>Další kroky

Úplný seznam kvót a omezení najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](azure-subscription-service-limits.md).

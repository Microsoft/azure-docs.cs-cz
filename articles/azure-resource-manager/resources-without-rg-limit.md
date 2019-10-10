---
title: Prostředky Azure bez omezení počtu 800
description: Obsahuje seznam typů prostředků Azure, které můžou mít víc než 800 instancí ve skupině prostředků.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: tomfitz
ms.openlocfilehash: 0f9db618b69ff692350f333b8a4a4354f4980169
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249126"
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

## <a name="microsoftcompute"></a>Microsoft. COMPUTE

* disků
* fotografií
* snímky
* VirtualMachines

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

* Servery

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

* Servery

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

* serverGroups
* Servery
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

* Orgány

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

* guestConfigurationAssignments
* Vybavení
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft. Logic

* integrationAccounts
* Zpracovávaný

## <a name="microsoftnetapp"></a>Microsoft. NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/svazky
* netAppAccounts/capacityPools/svazky/mountTargets
* netAppAccounts/capacityPools/svazky/snímky

## <a name="microsoftnetwork"></a>Microsoft. Network

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

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

* jobcollections

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

* vyrovnání
* containerGroups
* brány
* sítí
* záleží
* svazků

## <a name="microsoftstorage"></a>Microsoft. Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft. Web

* apiManagementAccounts/rozhraní API
* místa

## <a name="next-steps"></a>Další kroky

Úplný seznam kvót a omezení najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md).

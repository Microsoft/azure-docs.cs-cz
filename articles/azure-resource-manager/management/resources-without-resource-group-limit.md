---
title: Prostředky bez omezení počtu 800
description: Obsahuje seznam typů prostředků Azure, které můžou mít víc než 800 instancí ve skupině prostředků.
ms.topic: conceptual
ms.date: 01/08/2021
ms.openlocfilehash: 6736118f0713a27a91618173e06423530e6f10cb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103563109"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Prostředky, které nejsou omezeny na 800 instancí na skupinu prostředků

Ve výchozím nastavení můžete do každé skupiny prostředků nasadit až 800 instancí typu prostředku. Některé typy prostředků jsou ale z limitu instance 800 vyloučené. V tomto článku jsou uvedené typy prostředků Azure, které můžou mít víc než 800 instancí ve skupině prostředků. Všechny ostatní typy prostředků jsou omezeny na 800 instancí.

U některých typů prostředků musíte kontaktovat podporu, aby byl limit instance 800 odebraný. Tyto typy prostředků jsou uvedeny v tomto článku.

## <a name="microsoftautomation"></a>Microsoft. Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

* edgeSubscriptions
* linkedSubscriptions
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
* virtualMachineScaleSets – ve výchozím nastavení omezen na 800 instancí. Toto omezení se dá zvýšit kontaktováním podpory.
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

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

* instance

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

* servery

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

* flexibleServers
* servery

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

* flexibleServers
* serverGroups
* servery
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

* časových

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

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

* počítače – podporuje až 5 000 instancí.
* rozšíření – podporuje neomezený počet instancí rozšíření virtuálních počítačů.

## <a name="microsoftinsights"></a>Microsoft. Insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft. Logic

* integrationAccounts
* Zpracovávaný

## <a name="microsoftmedia"></a>Microsoft. Media

* MediaServices/liveEvents

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

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

* kapacity – ve výchozím nastavení omezen na 800 instancí. Toto omezení se dá zvýšit kontaktováním podpory.

## <a name="microsoftrelay"></a>Microsoft. Relay

* obsažené

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* obsažené

## <a name="microsoftsingularity"></a>Microsoft. jednotného

* accounts
* účty/accountQuotaPolicies
* účty/groupPolicies
* účty/úlohy
* účty/storageContainers

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftsql"></a>Microsoft.Sql

* servery/databáze

## <a name="microsoftweb"></a>Microsoft. Web

* apiManagementAccounts/rozhraní API
* místa

## <a name="next-steps"></a>Další kroky

Úplný seznam kvót a omezení najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](azure-subscription-service-limits.md).

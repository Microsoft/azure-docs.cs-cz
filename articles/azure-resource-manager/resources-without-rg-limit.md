---
title: Prostředky Azure bez omezení počtu 800
description: Obsahuje seznam typů prostředků Azure, které můžou mít víc než 800 instancí ve skupině prostředků.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 7/30/2019
ms.author: tomfitz
ms.openlocfilehash: eec448dcbf1c550b6b0a0ee0f17505fd6ddd3c74
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70194734"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Prostředky, které nejsou omezeny na 800 instancí na skupinu prostředků

Ve výchozím nastavení můžete do každé skupiny prostředků nasadit až 800 instancí typu prostředku. Některé typy prostředků jsou ale z limitu instance 800 vyloučené. V tomto článku jsou uvedené typy prostředků Azure, které můžou mít víc než 800 instancí ve skupině prostředků. Všechny ostatní typy prostředků jsou omezeny na 800 instancí.

U některých typů prostředků musíte kontaktovat podporu, aby byl limit instance 800 odebraný. Tyto typy prostředků jsou uvedeny v tomto článku.


## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* registrations
* registrace/customerSubscriptions
* registrace/produkty

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices – obraťte se na podporu a prodlužte limit.

## <a name="microsoftcompute"></a>Microsoft.Compute

* disks
* image
* snapshots
* virtualMachines

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* Registry/buildTasks
* Registry/buildTasks/listSourceRepositoryProperties
* Registry/buildTasks/kroky
* Registry/buildTasks/Steps/listBuildArguments
* registries/eventGridFilters
* registries/replications
* registries/tasks
* registries/webhooks

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* serverGroups
* servers
* serversv2

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* guestConfigurationAssignments
* software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* workflows

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
* publicIPAddresses – obraťte se na podporu a prodlužte limit.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections – obraťte se na podporu a prodlužte limit.

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/rozhraní API
* sites

## <a name="next-steps"></a>Další postup

Úplný seznam kvót a omezení najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-subscription-service-limits.md).

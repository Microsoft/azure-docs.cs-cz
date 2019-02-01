---
title: Přesunout operace podpory podle typu prostředku Azure | Dokumentace Microsoftu
description: Seznam typů prostředků Azure, které lze přesunout do nové skupiny prostředků nebo předplatného.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: tomfitz
ms.openlocfilehash: dfed8ce93a97b31380b44f0ac7057c0cff60901e
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55497277"
---
# <a name="move-operation-support-for-resources"></a>Přesunout operace podporu pro prostředky

Tento článek uvádí, zda typ prostředku Azure podporuje operace přesunu. I když typ prostředku podporuje operace přesunutí, může být podmínky, které brání přesunutí prostředku. Podrobnosti o podmínkách, které mají vliv operací přesunu najdete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="find-resource-provider-and-resource-type"></a>Najít poskytovatele prostředků a typ prostředku

Pokud chcete zjistit, zda lze přesunout prostředek, musí najít jeho poskytovatele prostředků a typ prostředku.

Pokud používáte PowerShell, použijte:

```azurepowershell-interactive
Get-AzResource -ResourceGroupName demogroup | Select Name, ResourceType | Format-table
```

Pokud používáte Azure CLI, použijte:

```azurecli-interactive
az resource list -g demogroup --query '[].{name:name, resourceType:type}' --output table
```

Typ prostředku se vrátí ve formátu `<resource-provider>/<resource-type-name>`. Tak, hodnota `Microsoft.OperationalInsights/workspaces` má poskytovatel prostředků **Microsoft.OperationalInsights** a název typu prostředku **pracovní prostory**.

Po nalezení poskytovatele prostředků a typ prostředku, použijte k určení, zda typ prostředku podporuje operace přesunu tabulky v tomto článku.

## <a name="microsoftaad"></a>Microsoft.AAD

| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | --------------- | ----------- |
| domainservices | Ne | Ne |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| servers | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | --------------- | ----------- |
| service | Ano | Ano |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | --------------- | ----------- |
| policyassignments | Ne | Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| automationaccounts | Ano | Ano |
| automationaccounts/configurations | Ano | Ano |
| automationaccounts/runbooks | Ano | Ano |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| b2cdirectories | Ano | Ano |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| registrations | Ano | Ano |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| backupvault | Ne | Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| batchaccounts | Ano | Ano |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| mapapis | Ne | Ne |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| biztalk | Ano | Ano |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| blueprintassignments | Ne | Ne |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| botservices | Ano | Ano |

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| redis | Ano | Ano |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| profiles | Ano | Ano |
| profiles/endpoints | Ano | Ano |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| certificateorders | Ano | Ano |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| domainnames | Ano | Ne |
| virtualmachines | Ano | Ne |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| networksecuritygroups | Ne | Ne |
| reservedips | Ne | Ne |
| virtualnetworks | Ne | Ne |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| storageaccounts | Ano | Ne |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| accounts | Ano | Ano |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| availabilitysets | Ano | Ano |
| disks | Ano | Ano |
| galleries | Ne | Ne |
| galleries/images | Ne | Ne |
| galleries/images/versions | Ne | Ne |
| images | Ano | Ano |
| restorepointcollections | Ne | Ne |
| sharedvmimages | Ne | Ne |
| sharedvmimages/verze | Ne | Ne |
| snapshots | Ano | Ano |
| virtualmachines | Ano | Ano |
| virtualmachines/extensions | Ano | Ano |
| virtualmachinescalesets | Ano | Ano |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| containergroups | Ne | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| containergroups | Ne | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| registries | Ano | Ano |
| registries/buildtasks | Ano | Ano |
| registries/replications | Ne | Ne |
| registries/tasks | Ano | Ano |
| registries/webhooks | Ano | Ano |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| containerservices | Ne | Ne |
| managedclusters | Ne | Ne |
| openshiftmanagedclusters | Ne | Ne |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| applications | Ano | Ano |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| connectors | Ano | Ano |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| hubs | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| jobs | Ne | Ne |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| databoxedgedevices | Ne | Ne |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| workspaces | Ne | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| catalogs | Ano | Ano |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| datafactories | Ano | Ano |
| factories | Ano | Ano |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| datalakeaccounts | Ne | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| accounts | Ano | Ano |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| accounts | Ano | Ano |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| services | Ne | Ne |
| services/projects | Ne | Ne |
| slots | Ne | Ne |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| servers | Ne | Ne |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| servers | Ano | Ano |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| servergroups | Ne | Ne |
| servers | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| artifactsources | Ne | Ne |
| rollouts | Ne | Ne |
| servicetopologies | Ne | Ne |
| servicetopologies/services | Ne | Ne |
| servicetopologies/services/serviceunits | Ne | Ne |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| iothubs | Ano | Ano |
| provisioningservices | Ano | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| labcenters | Ne | Ne |
| labs | Ano | Ne |
| labs/servicerunners | Ano | Ano |
| labs/virtualmachines | Ano | Ne |
| schedules | Ne | Ne |

## <a name="microsoftdns"></a>microsoft.dns
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| dnszones | Ne | Ne |
| dnszones/a | Ne | Ne |
| dnszones/aaaa | Ne | Ne |
| dnszones/cname | Ne | Ne |
| dnszones/mx | Ne | Ne |
| dnszones/ptr | Ne | Ne |
| dnszones/srv | Ne | Ne |
| dnszones/txt | Ne | Ne |
| trafficmanagerprofiles | Ne | Ne |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| databaseaccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| domains | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| topics | Ano | Ano |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| clusters | Ano | Ano |
| namespaces | Ano | Ano |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| hanainstances | Ano | Ano |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| dedicatedhsms | Ne | Ne |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| clusters | Ano | Ano |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| jobs | Ano | Ano |

## <a name="microsoftinsights"></a>microsoft.insights
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| actiongroups | Ano | Ano |
| activitylogalerts | Ne | Ne |
| alertrules | Ano | Ano |
| autoscalesettings | Ano | Ano |
| components | Ano | Ano |
| metricalerts | Ne | Ne |
| scheduledqueryrules | Ano | Ano |
| webtests | Ano | Ano |
| workbooks | Ano | Ano |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| iotapps | Ano | Ano |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| vaults | Ano | Ano |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| clusters | Ano | Ano |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| labaccounts | Ano | Ano |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| accounts | Ano | Ano |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| accounts | Ano | Ano |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| integrationaccounts | Ano | Ano |
| workflows | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| commitmentplans | Ano | Ano |
| webservices | Ano | Ne |
| workspaces | Ano | Ano |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| operationalizationclusters | Ano | Ano |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| accounts | Ano | Ano |
| accounts/workspaces | Ano | Ano |
| accounts/workspaces/projects | Ano | Ano |
| teamaccounts | Ano | Ano |
| teamaccounts/workspaces | Ano | Ano |
| teamaccounts/workspaces/projects | Ano | Ano |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| accounts | Ano | Ano |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| workspaces | Ano | Ano |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| userassignedidentities | Ano | Ano |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| accounts | Ano | Ano |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| classicdevservices | Ne | Ne |

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| mediaservices | Ano | Ano |
| mediaservices/liveevents | Ano | Ano |
| mediaservices/streamingendpoints | Ano | Ano |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| projects | Ne | Ne |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| applicationgateways | Ne | Ne |
| applicationsecuritygroups | Ano | Ano |
| azurefirewalls | Ne | Ne |
| connections | Ano | Ano |
| ddosprotectionplans | Ne | Ne |
| dnszones | Ano | Ano |
| expressroutecircuits | Ne | Ne |
| expressroutecrossconnections | Ne | Ne |
| expressroutegateways | Ne | Ne |
| expressrouteports | Ne | Ne |
| frontdoors | Ano | Ano |
| frontdoorwebapplicationfirewallpolicies | Ano | Ano |
| interfaceendpoints | Ne | Ne |
| loadbalancers | Ano | Ano |
| localnetworkgateways | Ano | Ano |
| networkintentpolicies | Ano | Ano |
| networkinterfaces | Ano | Ano |
| networkprofiles | Ne | Ne |
| networksecuritygroups | Ano | Ano |
| networkwatchers | Ano | Ano |
| networkwatchers/connectionmonitors | Ano | Ano |
| networkwatchers/přehledů | Ano | Ano |
| networkwatchers/pingmeshes | Ano | Ano |
| publicipaddresses | Ano | Ano |
| publicipprefixes | Ano | Ano |
| routefilters | Ne | Ne |
| routetables | Ano | Ano |
| serviceendpointpolicies | Ano | Ano |
| trafficmanagerprofiles | Ano | Ano |
| virtualhubs | Ano | Ano |
| virtualnetworkgateways | Ano | Ano |
| virtualnetworks | Ano | Ano |
| virtualnetworktaps | Ne | Ne |
| virtualwans | Ano | Ano |
| vpngateways | Ano | Ano |
| vpnsites | Ano | Ano |
| webapplicationfirewallpolicies | Ano | Ano |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| namespaces | Ano | Ano |
| namespaces/notificationhubs | Ano | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| workspaces | Ano | Ano |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| managementconfigurations | Ano | Ano |
| solutions | Ano | Ano |
| views | Ano | Ano |

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| dashboards | Ano | Ano |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| workspacecollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| capacities | Ano | Ano |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| vaults | Ano | Ano |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| namespaces | Ano | Ano |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| applications | Ano | Ne |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| flows | Ano | Ano |
| jobcollections | Ano | Ano |

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| searchservices | Ano | Ano |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| namespaces | Ano | Ano |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| clusters | Ano | Ano |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| applications | Ano | Ano |
| networks | Ano | Ano |
| volumes | Ano | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| signalr | Ano | Ano |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| siterecoveryvault | Ne | Ne |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| appliancedefinitions | Ne | Ne |
| appliances | Ne | Ne |
| applicationdefinitions | Ne | Ne |
| applications | Ne | Ne |

## <a name="microsoftsql"></a>Microsoft.Sql
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| managedinstances | Ano | Ano |
| managedinstances/databáze | Ano | Ano |
| servers | Ano | Ano |
| servers/databases | Ano | Ano |
| servers/elasticpools | Ano | Ano |
| virtualclusters | Ano | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| storageaccounts | Ano | Ano |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| storagesyncservices | Ano | Ano |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| managers | Ne | Ne |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| streamingjobs | Ano | Ano |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| environments | Ano | Ano |
| environments/eventsources | Ano | Ano |
| environments/referencedatasets | Ano | Ano |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| account | Ano | Ano |
| account/extension | Ano | Ano |
| account/project | Ano | Ano |

## <a name="microsoftweb"></a>Microsoft.Web
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| certificates | Ne | Ano |
| classicmobileservices | Ne | Ne |
| connectiongateways | Ano | Ano |
| connections | Ano | Ano |
| customapis | Ano | Ano |
| hostingenvironments | Ne | Ne |
| serverfarms | Ano | Ano |
| sites | Ano | Ano |
| sites/premieraddons | Ano | Ano |
| sites/slots | Ano | Ano |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | -------------- | ------------ |
| deviceservices | Ano | Ano |


## <a name="third-party-services"></a>Třeba služby třetích stran

Třeba služby třetích stran aktuálně nepodporují operace přesunu. Tyto poskytovatele prostředků patří:

* 84codes.CloudAMQP
* AppDynamics.APM
* Aspera.Transfers
* Auth0.Cloud
* Citrix.Cloud
* Citrix.Services
* CloudSimple.PrivateCloudIaaS
* Cloudyn.Analytics
* Conexlink.MyCloudIT
* Crypteron.DataSecurity
* Dynatrace.DynatraceSaaS
* Dynatrace.Ruxit
* LiveArena.Broadcast
* Lombiq.DotNest
* Mailjet.Email
* Myget.PackageManagement
* NewRelic.APM
* nuubit.nextgencdn
* Paraleap.CloudMonix
* Pokitdok.Platform
* RavenHq.Db
* Raygun.CrashReporting
* RevAPM.MobileCDN
* Sendgrid.Email
* Sparkpost.Basic
* stackify.retrace
* SuccessBricks.ClearDB
* TrendMicro.DeepSecurity
* U2uconsult.TheIdentityHub


## <a name="next-steps"></a>Další postup

* Příkazy pro přesun prostředky, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

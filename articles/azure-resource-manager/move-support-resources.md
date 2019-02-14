---
title: Přesunout operace podpory podle typu prostředku Azure
description: Seznam typů prostředků Azure, které lze přesunout do nové skupiny prostředků nebo předplatného.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 2/13/2019
ms.author: tomfitz
ms.openlocfilehash: f82c3adc0049c7386206fb4be10d3e634627a25a
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236885"
---
# <a name="move-operation-support-for-resources"></a>Přesunout operace podporu pro prostředky
Tento článek uvádí, zda typ prostředku Azure podporuje operace přesunu. I když typ prostředku podporuje operace přesunutí, může být podmínky, které brání přesunutí prostředku. Podrobnosti o podmínkách, které mají vliv operací přesunu najdete v tématu [přesunutí prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte si [přesunout. podpora resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| domainservices | Ne | Ne |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| Tenanti | Ne | Ne |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| servers | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| service | Ano | Ano |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| apiapps | Ne | Ne |
| appidentities | Ne | Ne |
| gateways | Ne | Ne |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| policyassignments | Ne | Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| automationaccounts | Ano | Ano |
| automationaccounts/configurations | Ano | Ano |
| automationaccounts/runbooks | Ano | Ano |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| b2cdirectories | Ano | Ano |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| registrations | Ano | Ano |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| backupvault | Ne | Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| batchaccounts | Ano | Ano |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| clusters | Ne | Ne |
| fileservers | Ne | Ne |
| jobs | Ne | Ne |
| workspaces | Ne | Ne |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| mapapis | Ne | Ne |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| biztalk | Ano | Ano |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| blockchainmembers | Ne | Ne |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| blueprintassignments | Ne | Ne |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| botservices | Ano | Ano |

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| redis | Ano | Ano |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| profiles | Ano | Ano |
| profiles/endpoints | Ano | Ano |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| certificateorders | Ano | Ano |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| domainnames | Ano | Ne |
| virtualmachines | Ano | Ne |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Ne | Ne |
| reservedips | Ne | Ne |
| virtualnetworks | Ne | Ne |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| storageaccounts | Ano | Ne |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| availabilitysets | Ano | Ano |
| disks | Ano | Ano |
| galleries | Ne | Ne |
| galleries/images | Ne | Ne |
| galleries/images/versions | Ne | Ne |
| images | Ano | Ano |
| proximityplacementgroups | Ne | Ne |
| restorepointcollections | Ne | Ne |
| sharedvmimages | Ne | Ne |
| sharedvmimages/verze | Ne | Ne |
| snapshots | Ano | Ano |
| virtualmachines | Ano | Ano |
| virtualmachines/extensions | Ano | Ano |
| virtualmachinescalesets | Ano | Ano |

## <a name="microsoftcontainer"></a>Microsoft.Container
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| containergroups | Ne | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| containergroups | Ne | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| registries | Ano | Ano |
| registries/buildtasks | Ano | Ano |
| registries/replications | Ne | Ne |
| registries/tasks | Ano | Ano |
| registries/webhooks | Ano | Ano |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| containerservices | Ne | Ne |
| managedclusters | Ne | Ne |
| openshiftmanagedclusters | Ne | Ne |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| applications | Ano | Ano |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ne | Ne |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| connectors | Ano | Ano |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| hubs | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| jobs | Ne | Ne |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Ne | Ne |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| workspaces | Ne | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| catalogs | Ano | Ano |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| connectionmanagers | Ne | Ne |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| Balíčky | Ne | Ne |
| Plány | Ne | Ne |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| datafactories | Ano | Ano |
| factories | Ano | Ano |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Ne | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| services | Ne | Ne |
| services/projects | Ne | Ne |
| slots | Ne | Ne |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| servers | Ano | Ano |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| servers | Ano | Ano |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| servergroups | Ne | Ne |
| servers | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| artifactsources | Ne | Ne |
| rollouts | Ne | Ne |
| servicetopologies | Ne | Ne |
| servicetopologies/services | Ne | Ne |
| servicetopologies/services/serviceunits | Ne | Ne |
| kroky | Ne | Ne |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| elasticpools | Ne | Ne |
| elasticpools/iothubtenants | Ne | Ne |
| iothubs | Ano | Ano |
| provisioningservices | Ano | Ano |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| Kontrolery | Ne | Ne |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| labcenters | Ne | Ne |
| labs | Ano | Ne |
| labs/servicerunners | Ano | Ano |
| labs/virtualmachines | Ano | Ne |
| schedules | Ne | Ne |

## <a name="microsoftdns"></a>microsoft.dns
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
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
| ------------- | ----------- | ---------- |
| databaseaccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| domains | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| domains | Ano | Ano |
| topics | Ano | Ano |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| clusters | Ano | Ano |
| namespaces | Ano | Ano |

## <a name="microsoftgenomics"></a>Microsoft.Genomics
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ne | Ne |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| hanainstances | Ano | Ano |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| clusters | Ano | Ano |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| datamanagers | Ano | Ano |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| jobs | Ano | Ano |

## <a name="microsoftinsights"></a>microsoft.insights
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ne | Ne |
| actiongroups | Ano | Ano |
| activitylogalerts | Ne | Ne |
| alertrules | Ano | Ano |
| autoscalesettings | Ano | Ano |
| components | Ano | Ano |
| guestdiagnosticsettings | Ne | Ne |
| metricalerts | Ne | Ne |
| notificationgroups | Ne | Ne |
| notificationrules | Ne | Ne |
| scheduledqueryrules | Ne | Ne |
| webtests | Ano | Ano |
| workbooks | Ano | Ano |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| iotapps | Ano | Ano |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| checknameavailability | Ano | Ano |
| graf | Ano | Ano |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| hsmpools | Ne | Ne |
| vaults | Ano | Ano |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| clusters | Ano | Ano |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| labaccounts | Ano | Ano |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| hostingenvironments | Ne | Ne |
| integrationaccounts | Ano | Ano |
| integrationserviceenvironments | Ne | Ne |
| isolatedenvironments | Ne | Ne |
| workflows | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| commitmentplans | Ano | Ano |
| webservices | Ano | Ne |
| workspaces | Ano | Ano |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Ano | Ano |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |
| accounts/workspaces | Ano | Ano |
| accounts/workspaces/projects | Ano | Ano |
| teamaccounts | Ano | Ano |
| teamaccounts/workspaces | Ano | Ano |
| teamaccounts/workspaces/projects | Ano | Ano |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| hostingaccounts | Ne | Ne |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| workspaces | Ne | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| userassignedidentities | Ano | Ano |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| classicdevservices | Ne | Ne |

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| mediaservices | Ano | Ano |
| mediaservices/liveevents | Ano | Ano |
| mediaservices/streamingendpoints | Ano | Ano |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| assessmentprojects | Ne | Ne |
| migrateprojects | Ne | Ne |
| projects | Ne | Ne |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| netappaccounts | Ne | Ne |
| netappaccounts/capacitypools | Ne | Ne |
| netappaccounts/capacitypools/svazky | Ne | Ne |
| netappaccounts/capacitypools/svazky/mounttargets | Ne | Ne |
| netappaccounts/capacitypools/svazky/snímky | Ne | Ne |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| applicationgateways | Ne | Ne |
| applicationsecuritygroups | Ano | Ano |
| azurefirewalls | Ano | Ano |
| bastionhosts | Ne | Ne |
| connections | Ano | Ano |
| ddoscustompolicies | Ano | Ano |
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
| natgateways | Ano | Ano |
| networkintentpolicies | Ano | Ano |
| networkinterfaces | Ano | Ano |
| networkprofiles | Ne | Ne |
| networksecuritygroups | Ano | Ano |
| networkwatchers | Ano | Ano |
| networkwatchers/connectionmonitors | Ano | Ano |
| networkwatchers/přehledů | Ano | Ano |
| networkwatchers/pingmeshes | Ano | Ano |
| p2svpngateways | Ne | Ne |
| privatelinkservices | Ne | Ne |
| publicipaddresses | Ano | Ano |
| publicipprefixes | Ano | Ano |
| routefilters | Ne | Ne |
| routetables | Ano | Ano |
| securegateways | Ne | Ne |
| serviceendpointpolicies | Ano | Ano |
| trafficmanagerprofiles | Ano | Ano |
| virtualhubs | Ne | Ne |
| virtualnetworkgateways | Ano | Ano |
| virtualnetworks | Ano | Ano |
| virtualnetworktaps | Ne | Ne |
| virtualwans | Ne | Ne |
| vpngateways | Ne | Ne |
| vpnsites | Ano | Ano |
| webapplicationfirewallpolicies | Ano | Ano |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| namespaces | Ano | Ano |
| namespaces/notificationhubs | Ano | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| workspaces | Ano | Ano |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| managementconfigurations | Ano | Ano |
| solutions | Ano | Ano |
| views | Ano | Ano |

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| dashboards | Ano | Ano |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| rootresources | Ne | Ne |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| workspacecollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| capacities | Ano | Ano |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| accounts | Ne | Ne |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| vaults | Ano | Ano |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| namespaces | Ano | Ano |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| applications | Ano | Ne |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| flows | Ano | Ano |
| jobcollections | Ano | Ano |

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| searchservices | Ano | Ano |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| gateways | Ne | Ne |
| uzlů | Ne | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| namespaces | Ano | Ano |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| applications | Ne | Ne |
| clusters | Ano | Ano |
| containergroups | Ne | Ne |
| containergroupsets | Ne | Ne |
| edgeclusters | Ne | Ne |
| networks | Ne | Ne |
| secretstores | Ne | Ne |
| volumes | Ne | Ne |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| applications | Ano | Ano |
| containergroups | Ne | Ne |
| gateways | Ano | Ano |
| networks | Ano | Ano |
| tajné kódy | Ano | Ano |
| volumes | Ano | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| signalr | Ano | Ano |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Ne | Ne |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Ne | Ne |
| appliances | Ne | Ne |
| applicationdefinitions | Ne | Ne |
| applications | Ne | Ne |
| jitrequests | Ne | Ne |

## <a name="microsoftsql"></a>Microsoft.Sql
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| managedinstances | Ano | Ano |
| managedinstances/databáze | Ano | Ano |
| servers | Ano | Ano |
| servers/databases | Ano | Ano |
| servers/elasticpools | Ano | Ano |
| servery pro/jobaccounts | Ne | Ne |
| servery pro/jobagents | Ne | Ne |
| virtualclusters | Ano | Ano |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Ano | Ano |
| sqlvirtualmachines | Ano | Ano |

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| dwvm | Ne | Ne |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| storageaccounts | Ano | Ano |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| storagesyncservices | Ano | Ano |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| storagesyncservices | Ne | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| storagesyncservices | Ne | Ne |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| managers | Ne | Ne |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| streamingjobs | Ano | Ano |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| environments | Ne | Ne |
| environments/eventsources | Ne | Ne |
| instance | Ne | Ne |
| instance nebo prostředími | Ne | Ne |
| instance/prostředí/eventsources | Ne | Ne |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| providerregistrations | Ne | Ne |
| zdroje | Ne | Ne |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| environments | Ano | Ano |
| environments/eventsources | Ano | Ano |
| environments/referencedatasets | Ano | Ano |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| imagetemplates | Ne | Ne |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| account | Ano | Ano |
| account/extension | Ano | Ano |
| account/project | Ano | Ano |

## <a name="microsoftweb"></a>Microsoft.Web
| Typ prostředku | Skupina prostředků | Předplatné |
| ------------- | ----------- | ---------- |
| certificates | Ne | Ano |
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
| ------------- | ----------- | ---------- |
| deviceservices | Ano | Ano |

## <a name="third-party-services"></a>Třeba služby třetích stran

Třeba služby třetích stran aktuálně nepodporují operace přesunu.

## <a name="next-steps"></a>Další postup
Příkazy pro přesun prostředky, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](resource-group-move-resources.md).

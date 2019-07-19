---
title: Přesunout podporu operace podle typu prostředku Azure
description: Zobrazuje seznam typů prostředků Azure, které se dají přesunout do nové skupiny prostředků nebo předplatného.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 7/9/2019
ms.author: tomfitz
ms.openlocfilehash: 22493ad7998e9014c88c79e6ac5eee3bf1216119
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226805"
---
# <a name="move-operation-support-for-resources"></a>Přesunout podporu operací pro prostředky
Tento článek uvádí, jestli typ prostředku Azure podporuje operaci přesunu. Poskytuje taky informace o zvláštních podmínkách, které je potřeba vzít v úvahu při přesunu prostředku.

Přejít na obor názvů poskytovatele prostředků:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft. Backup](#microsoftbackup)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [microsoft.dns](#microsoftdns)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainservices | Ne | Ne |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| tenantů | Ne | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| actionrules | Ano | Ano |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| service | Ano | Ano |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| configurationstores | Ano | Ano |

## <a name="microsoftappservice"></a>Microsoft.AppService
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| apiapps | Ne | Ne |
| appidentities | Ne | Ne |
| brány | Ne | Ne |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| policyassignments | Ne | Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| automationaccounts | Ano | Ano |
| automationaccounts/konfigurace | Ano | Ano |
| automationaccounts/Runbooky | Ano | Ano |

> [!IMPORTANT]
> Runbooky musí existovat ve stejné skupině prostředků jako účet Automation.

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| b2cdirectories | Ano | Ano |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registrations | Ano | Ano |

## <a name="microsoftbackup"></a>Microsoft.Backup
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| backupvault | Ne | Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| batchaccounts | Ano | Ano |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Ne | Ne |
| servery | Ne | Ne |
| jobs | Ne | Ne |
| workspaces | Ne | Ne |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mapapis | Ne | Ne |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| biztalk | Ano | Ano |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blockchainmembers | Ano | Ano |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| blueprintassignments | Ne | Ne |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| botservices | Ano | Ano |

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Redis | Ano | Ano |

> [!IMPORTANT]
> Pokud je instance Azure cache for Redis nakonfigurovaná pomocí virtuální sítě, nedá se tato instance přesunout do jiného předplatného. Viz [omezení přesunu virtuálních sítí](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| profiles | Ano | Ano |
| profiles/endpoints | Ano | Ano |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| certificateorders | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domainnames | Ano | Ne |
| virtualmachines | Ano | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| networksecuritygroups | Ne | Ne |
| reservedips | Ne | Ne |
| virtualnetworks | Ne | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Ano | Ne |

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| availabilitysets | Ano | Ano |
| disks | Ano | Ano |
| galleries | Ne | Ne |
| galleries/images | Ne | Ne |
| galleries/images/versions | Ne | Ne |
| hostgroups | Ne | Ne |
| hostgroups/hostitelé | Ne | Ne |
| images | Ano | Ano |
| proximityplacementgroups | Ne | Ne |
| restorepointcollections | Ne | Ne |
| sharedvmimages | Ne | Ne |
| sharedvmimages/verze | Ne | Ne |
| snapshots | Ano | Ano |
| virtualmachines | Ano | Ano |
| VirtualMachines/rozšíření | Ano | Ano |
| virtualmachinescalesets | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Virtual Machines](./move-limitations/virtual-machines-move-limitations.md).

## <a name="microsoftcontainer"></a>Microsoft.Container
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Ne | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containergroups | Ne | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| registries | Ano | Ano |
| registries/buildtasks | Ano | Ano |
| registries/replications | Ano | Ano |
| registries/tasks | Ano | Ano |
| registries/webhooks | Ano | Ano |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| containerservices | Ne | Ne |
| managedclusters | Ne | Ne |
| openshiftmanagedclusters | Ne | Ne |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applications | Ano | Ano |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ne | Ne |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connectors | Ano | Ano |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hubs | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| jobs | Ne | Ne |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databoxedgedevices | Ne | Ne |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Ne | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| catalogs | Ano | Ano |
| datacatalogs | Ne | Ne |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| connectionmanagers | Ne | Ne |

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Zásilk | Ne | Ne |
| plánují | Ne | Ne |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| objekty DataFactory | Ano | Ano |
| factories | Ano | Ano |

## <a name="microsoftdatalake"></a>Microsoft.DataLake
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datalakeaccounts | Ne | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Ne | Ne |
| services/projects | Ne | Ne |
| slots | Ne | Ne |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Ano | Ano |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servers | Ano | Ano |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| servergroups | Ne | Ne |
| servers | Ano | Ano |
| serversv2 | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| artifactsources | Ano | Ano |
| rollouts | Ano | Ano |
| servicetopologies | Ano | Ano |
| servicetopologies/services | Ano | Ano |
| servicetopologies/služby/serviceunits | Ano | Ano |
| kroky | Ano | Ano |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| elasticpools | Ne | Ne |
| elasticpools/iothubtenants | Ne | Ne |
| iothubs | Ano | Ano |
| provisioningservices | Ano | Ano |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Kontrolou | Ne | Ne |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labcenters | Ne | Ne |
| labs | Ano | Ne |
| laboratoře/prostředí | Ano | Ano |
| labs/servicerunners | Ano | Ano |
| labs/virtualmachines | Ano | Ne |
| schedules | Ano | Ano |

## <a name="microsoftdns"></a>microsoft.dns
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dnszones | Ne | Ne |
| dnszones/a | Ne | Ne |
| dnszones/AAAA | Ne | Ne |
| dnszones/CNAME | Ne | Ne |
| dnszones/MX | Ne | Ne |
| dnszones/PTR | Ne | Ne |
| dnszones/SRV | Ne | Ne |
| dnszones/txt | Ne | Ne |
| trafficmanagerprofiles | Ne | Ne |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| databaseaccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domains | Ano | Ano |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| domains | Ano | Ano |
| topics | Ano | Ano |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Ano | Ano |
| namespaces | Ano | Ano |

## <a name="microsoftgenomics"></a>Microsoft. genomika
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ne | Ne |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hanainstances | Ano | Ano |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Ano | Ano |

> [!IMPORTANT]
> Clustery HDInsight můžete přesunout do nové předplatné nebo skupinu prostředků. Však nelze přesouvat mezi předplatných přidružených síťových prostředků do clusteru HDInsight (například virtuální sítě, síťové karty nebo nástroje pro vyrovnávání zatížení). Kromě toho nelze přesunout do nové skupiny prostředků síťové rozhraní, který je připojen k virtuálnímu počítači pro cluster.
>
> Při přesunu do nového předplatného clusteru služby HDInsight, nejprve přesuňte dalších prostředků (např. účet úložiště). Přesuňte clusteru HDInsight samostatně.

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| services | Ano | Ano |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| virtuální | Ne | Ne |

## <a name="microsofthybriddata"></a>Microsoft.HybridData
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| datamanagery | Ano | Ano |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| jobs | Ano | Ano |

## <a name="microsoftinsights"></a>microsoft.insights
| Typ prostředku | Resource group | Subscription |
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
| scheduledqueryrules | Ano | Ano |
| webtests | Ano | Ano |
| workbooks | Ano | Ano |

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty](../azure-subscription-service-limits.md#azure-monitor-limits)předplatného.

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotapps | Ano | Ano |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| checknameavailability | Ano | Ano |
| graph | Ano | Ano |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hsmpools | Ne | Ne |
| vaults | Ano | Ano |

> [!IMPORTANT]
> Trezory klíčů použité pro šifrování disku nejde přesunout do skupiny prostředků v rámci stejného předplatného nebo napříč předplatnými.

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| clusters | Ano | Ano |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| labaccounts | Ne | Ne |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ne | Ne |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingenvironments | Ne | Ne |
| integrationaccounts | Ano | Ano |
| integrationserviceenvironments | Ne | Ne |
| isolatedenvironments | Ne | Ne |
| workflows | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| commitmentplans | Ano | Ano |
| webservices | Ano | Ne |
| workspaces | Ano | Ano |

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| operationalizationclusters | Ano | Ano |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ne | Ne |
| accounts/workspaces | Ne | Ne |
| accounts/workspaces/projects | Ne | Ne |
| teamaccounts | Ne | Ne |
| teamaccounts/workspaces | Ne | Ne |
| teamaccounts/workspaces/projects | Ne | Ne |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| hostingaccounts | Ne | Ne |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Ne | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| userassignedidentities | Ne | Ne |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ano | Ano |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| classicdevservices | Ne | Ne |

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| MediaServices | Ano | Ano |
| MediaServices/liveevents | Ano | Ano |
| mediaservices/streamingendpoints | Ano | Ano |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| assessmentprojects | Ne | Ne |
| migrateprojects | Ne | Ne |
| projects | Ne | Ne |

## <a name="microsoftnetapp"></a>Microsoft.NetApp
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| netappaccounts | Ne | Ne |
| netappaccounts/capacitypools | Ne | Ne |
| netappaccounts/capacitypools/svazky | Ne | Ne |
| netappaccounts/capacitypools/svazky/mounttargets | Ne | Ne |
| netappaccounts/capacitypools/svazky/snímky | Ne | Ne |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgateways | Ne | Ne |
| applicationgatewaywebapplicationfirewallpolicies | Ne | Ne |
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
| frontdoors | Ne | Ne |
| frontdoorwebapplicationfirewallpolicies | Ne | Ne |
| loadbalancers | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
| localnetworkgateways | Ano | Ano |
| natgateways | Ano | Ano |
| networkintentpolicies | Ano | Ano |
| networkinterfaces | Ano | Ano |
| networkprofiles | Ne | Ne |
| networksecuritygroups | Ano | Ano |
| networkwatchers | Ano | Ano |
| networkwatchers/connectionmonitors | Ano | Ano |
| networkwatchers/čočky | Ano | Ano |
| networkwatchers/pingmeshes | Ano | Ano |
| p2svpngateways | Ne | Ne |
| privatednszones | Ano | Ano |
| privatednszones/virtualnetworklinks | Ano | Ano |
| privateendpoints | Ne | Ne |
| privatelinkservices | Ne | Ne |
| publicipaddresses | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
| publicipprefixes | Ano | Ano |
| routefilters | Ne | Ne |
| routetables | Ano | Ano |
| securegateways | Ano | Ano |
| serviceendpointpolicies | Ano | Ano |
| trafficmanagerprofiles | Ano | Ano |
| virtualhubs | Ne | Ne |
| virtualnetworkgateways | Ano | Ano |
| virtualnetworks | Ano | Ano |
| virtualnetworktaps | Ne | Ne |
| virtualwans | Ne | Ne |
| vpngateways | Ne | Ne |
| vpnsites | Ne | Ne |
| webapplicationfirewallpolicies | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si doprovodné materiály k [přesunu virtuálních sítí](./move-limitations/virtual-network-move-limitations.md).

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Ano | Ano |
| namespaces/notificationhubs | Ano | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspaces | Ano | Ano |

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty](../azure-subscription-service-limits.md#azure-monitor-limits)předplatného.

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managementconfigurations | Ano | Ano |
| solutions | Ano | Ano |
| views | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft.Peering
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Partnerské vztahy | Ne | Ne |

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dashboards | Ano | Ano |

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| rootresources | Ne | Ne |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| workspacecollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| capacities | Ano | Ano |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| accounts | Ne | Ne |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| vaults | Ano | Ano |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Ano | Ano |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applications | Ano | Ne |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| flows | Ano | Ano |
| jobcollections | Ano | Ano |

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| searchservices | Ano | Ano |

> [!IMPORTANT]
> V jedné operaci nemůžete přesouvat několik prostředků vyhledávání v různých oblastech. Místo toho je přesuňte do samostatných operací.

## <a name="microsoftsecurity"></a>Microsoft.Security
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| iotsecuritysolutions | Ano | Ano |

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| brány | Ne | Ne |
| uzlů | Ne | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| namespaces | Ano | Ano |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ prostředku | Resource group | Subscription |
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
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applications | Ano | Ano |
| containergroups | Ne | Ne |
| brány | Ano | Ano |
| networks | Ano | Ano |
| záleží | Ano | Ano |
| volumes | Ano | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| signalr | Ano | Ano |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| siterecoveryvault | Ne | Ne |

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Recovery Services](../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| appliancedefinitions | Ne | Ne |
| appliances | Ne | Ne |
| applicationdefinitions | Ne | Ne |
| applications | Ne | Ne |
| jitrequests | Ne | Ne |

## <a name="microsoftsql"></a>Microsoft.Sql
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| instancepools | Ne | Ne |
| managedinstances | Ne | Ne |
| managedinstances/databáze | Ne | Ne |
| servers | Ano | Ano |
| servers/databases | Ano | Ano |
| servers/elasticpools | Ano | Ano |
| virtualclusters | Ano | Ano |

> [!IMPORTANT]
> Databáze a server musí být ve stejné skupině prostředků. Přesunete-li SQL server, přesunou také všechny jeho databáze. Toto chování platí pro databáze Azure SQL Database a Azure SQL Data Warehouse.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| sqlvirtualmachinegroups | Ano | Ano |
| sqlvirtualmachines | Ano | Ano |

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dwvm | Ne | Ne |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storageaccounts | Ano | Ano |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| mezipaměti | Ne | Ne |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Ano | Ano |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Ne | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| storagesyncservices | Ne | Ne |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| managers | Ne | Ne |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| streamingjobs | Ano | Ano |

> [!IMPORTANT]
> Stream Analytics úlohy nelze přesunout, pokud je ve spuštěném stavu.

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| environments | Ne | Ne |
| environments/eventsources | Ne | Ne |
| instance | Ne | Ne |
| instance/prostředí | Ne | Ne |
| instance/prostředí/EventSources | Ne | Ne |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| providerregistrations | Ne | Ne |
| Prostředky | Ne | Ne |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| environments | Ano | Ano |
| environments/eventsources | Ano | Ano |
| environments/referencedatasets | Ano | Ano |

## <a name="microsofttoken"></a>Microsoft.Token
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| Store | Ne | Ne |

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| imagetemplates | Ne | Ne |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| account | Ano | Ano |
| account/extension | Ano | Ano |
| account/project | Ano | Ano |

> [!IMPORTANT]
> Pokud chcete změnit předplatné služby Azure DevOps, přečtěte si téma [Změna předplatného Azure používaného pro fakturaci](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| dedicatedcloudnodes | Ano | Ano |
| dedicatedcloudservices | Ano | Ano |
| virtualmachines | Ano | Ano |

## <a name="microsoftweb"></a>Microsoft.Web
| Typ prostředku | Resource group | Subscription |
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

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| deviceservices | Ne | Ne |

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop
| Typ prostředku | Resource group | Subscription |
| ------------- | ----------- | ---------- |
| applicationgroups | Ne | Ne |
| hostpools | Ne | Ne |
| workspaces | Ne | Ne |

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

## <a name="next-steps"></a>Další postup
Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo](resource-group-move-resources.md)předplatného.

Pokud chcete získat stejná data jako soubor hodnot oddělených čárkami, Stáhněte si [Move-support-Resources. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

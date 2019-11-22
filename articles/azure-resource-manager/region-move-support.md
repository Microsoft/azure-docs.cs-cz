---
title: Podpora přesunu prostředků Azure napříč oblastmi
description: Zobrazí seznam typů prostředků Azure, které se dají přesunout napříč oblastmi Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 60b8708458e081d66514e092edc9ef9af7c33494
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308099"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Podpora přesunu prostředků Azure napříč oblastmi

Tento článek potvrzuje, jestli je typ prostředku Azure podporovaný pro přesun do jiné oblasti Azure. 

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
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
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
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
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
> - [Microsoft. datashare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
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
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
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
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
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

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | domainservices | Ne | 
> | domainservices / replicasets | Ne | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Tenantů | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | actionrules | Ne | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | servery | Ne |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | služba | Ne |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | configurationstores | Ne | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | apiapps | Ne | 
> | appidentities | Ne | 
> | brány | Ne | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | policyassignments | Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | automationaccounts | Ne | 
> | automationaccounts/konfigurace | Ne | 
> | automationaccounts/Runbooky | Ne | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | b2cdirectories | Ne | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | sqlserverregistrations | Ne |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | registrations | Ne | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | batchaccounts | Ne |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | clusters | Ne | 
> | servery | Ne | 
> | jobs | Ne | 
> | workspaces | Ne | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | mapapis | Ne | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | biztalk | Ne | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | blockchainmembers | Ne |
> | diváků | Ne | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | blueprintassignments | Ne | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | botservices | Ne | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Redis | Ne | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Ne |
> | profiles | Ne | 
> | profily/koncové body | Ne | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | certificateorders | Ne | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | domainnames | Ne |  
> | virtualmachines | Ne | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | networksecuritygroups | Ne |
> | reservedips | Ne | 
> | virtualnetworks | Ne | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storageaccounts | Ano |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | availabilitysets | Ne | 
> | diskencryptionsets | Ne | 
> | disks | Ne | 
> | galleries | Ne | 
> | Galerie/image | Ne | 
> | Galerie/image/verze | Ne | 
> | hostgroups | Ne | 
> | hostgroups/hostitelé | Ne | 
> | images | Ne | 
> | proximityplacementgroups | Ne | 
> | restorepointcollections | Ne | 
> | sharedvmimages | Ne | 
> | sharedvmimages/verze | Ne | 
> | snímky | Ne | 
> | virtualmachines | Ano | 
> | VirtualMachines/rozšíření | Ne | 
> | virtualmachinescalesets | Ne | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containergroups | Ne | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containergroups | Ne | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | registries | Ne |  
> | Registry/BuildTasks | Ne |  
> | Registry/replikace | Ne | 
> | Registry/úlohy | Ne |  
> | Registry a Webhooky | Ne | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containerservices | Ne | 
> | managedclusters | Ne | 
> | openshiftmanagedclusters | Ne | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace | Ne | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | konektory | Ne |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hubs | Ne |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | resourceproviders | Ne | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | jobs | Ne | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | databoxedgedevices | Ne | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | workspaces | Ne | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | catalogs | Ne | 
> | datacatalogs | Ne | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | connectionmanagers | Ne | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | zásilk | Ne | 
> | plánují | Ne | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | objekty DataFactory | Ne | 
> | factories | Ne |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | datalakeaccounts | Ne | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | services | Ne | 
> | služby a projekty | Ne | 
> | slots | Ne | 

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | servery | Ne |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | servery | Ne |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | servergroups | Ne | 
> | servery | Ne |  
> | serversv2 | Ne | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | artifactsources | Ne | 
> | rollouts | Ne |  
> | servicetopologies | Ne | 
> | servicetopologies/služby | Ne |  
> | servicetopologies/služby/serviceunits | Ne | 
> | kroky | Ne | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | elasticpools | Ne | 
> | elasticpools / iothubtenants | Ne | 
> | iothubs | Ano | 
> | provisioningservices | Ne | 

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | kontrolou | Ne | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | labcenters | Ne | 
> | labs | Ne | 
> | laboratoře/prostředí | Ne |  
> | Labs/servicerunners | Ne | 
> | Labs/VirtualMachines | Ne |  
> | schedules | Ne |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | databaseaccounts | Ne | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | domains | Ne | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | services | Ne |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | domains | Ne |  
> | topics | Ne | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | clusters | Ne |  
> | namespaces | Ne | 

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hanainstances | Ne | 
> | sapmonitors | Ne |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | clusters | Ne | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | services | Ne |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | virtuální | Ne | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | datamanagery |  Ne | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | jobs |  Ne | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 
> | actiongroups |  Ne | 
> | activitylogalerts | Ne | 
> | alertrules |  Ne | 
> | autoscalesettings |  Ne | 
> | components |  Ne |  
> | guestdiagnosticsettings | Ne | 
> | metricalerts | Ne | 
> | notificationgroups | Ne | 
> | notificationrules | Ne | 
> | scheduledqueryrules |  Ne | 
> | webtests |  Ne | 
> | workbooks |  Ne |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | iotapps |  Ne |  

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | checknameavailability |  Ne |  
> | graph |  Ne | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hsmpools | Ne | 
> | vaults |  Ne | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | clusters |  Ne |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | labaccounts | Ne | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hostingenvironments | Ne | 
> | integrationaccounts |  Ne |  
> | integrationserviceenvironments | Ne | 
> | isolatedenvironments | Ne | 
> | workflows |  Ne |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | commitmentplans |  Ne | 
> | webservices |  Ne | 
> | workspaces |  Ne | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | operationalizationclusters |  Ne | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 
> | účty/pracovní prostory | Ne | 
> | účty/pracovní prostory/projekty | Ne | 
> | teamaccounts | Ne | 
> | teamaccounts/pracovní prostory | Ne | 
> | teamaccounts/pracovní prostory/projekty | Ne | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hostingaccounts | Ne | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | workspaces | Ne | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | userassignedidentities | Ne | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts |  Ne |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | classicdevservices | Ne | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | MediaServices |  Ne | 
> | MediaServices/liveevents |  Ne | 
> | MediaServices/starají |  Ne | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | appclusters | Ne | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | assessmentprojects | Ne | 
> | migrateprojects | Ne | 
> | projects | Ne | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | netappaccounts | Ne | 
> | netappaccounts / capacitypools | Ne | 
> | netappaccounts/capacitypools/svazky | Ne | 
> | netappaccounts/capacitypools/svazky/mounttargets | Ne | 
> | netappaccounts/capacitypools/svazky/snímky | Ne | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | applicationgateways | Ne | 
> | applicationgatewaywebapplicationfirewallpolicies | Ne | 
> | applicationsecuritygroups |  Ne |  
> | azurefirewalls |  Ne |  
> | bastionhosts | Ne | 
> | connections |  Ne | 
> | ddoscustompolicies |  Ne | 
> | ddosprotectionplans | Ne | 
> | dnszones |  Ne | 
> | expressroutecircuits | Ne | 
> | expressroutecrossconnections | Ne | 
> | expressroutegateways | Ne | 
> | expressrouteports | Ne | 
> | frontdoors | Ne | 
> | frontdoorwebapplicationfirewallpolicies | Ne | 
> | loadbalancers | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br> -Ano standardní SKU |
> | localnetworkgateways |  Ne | 
> | natgateways |  Ne | 
> | networkintentpolicies |  Ne | 
> | networkinterfaces | Ano | 
> | networkprofiles | Ne | 
> | networksecuritygroups | Ano | 
> | networkwatchers |  Ne |  
> | networkwatchers / connectionmonitors |  Ne | 
> | networkwatchers/čočky |  Ne | 
> | networkwatchers / pingmeshes |  Ne | 
> | p2svpngateways | Ne | 
> | privatednszones |  Ne |  
> | privatednszones / virtualnetworklinks |  Ne |  
> | privateendpoints | Ne | 
> | privatelinkservices | Ne | 
> | publicipaddresses | Ano – základní SKU<br>No – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | publicipprefixes | Ne | 
> | routefilters | Ne | 
> | routetables |  Ne | 
> | serviceendpointpolicies |  Ne | 
> | trafficmanagerprofiles |  Ne | 
> | virtualhubs | Ne | 
> | virtualnetworkgateways |  Ne |  
> | virtualnetworks |  Ne | 
> | virtualnetworktaps | Ne | 
> | virtualwans | Ne | 
> | vpngateways (virtuální síť WAN) | Ne | 
> | vpnsites (virtuální síť WAN) | Ne | 
> | webapplicationfirewallpolicies |  Ne | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | namespaces |  Ne | 
> | obory názvů/notificationhubs |  Ne |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | workspaces |  Ne | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | managementconfigurations |  Ne | 
> | views |  Ne | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | partnerské vztahy | Ne | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | dashboards | Ne | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | rootresources | Ne | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | workspacecollections |  Ne | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | capacities |  Ne | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | vaults | Ano (pro trezory služby Backup, které si myslím? | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | namespaces |  Ne | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | dotazy |  Ne |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace |  Ne | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | flows |  Ne |  
> | jobcollections |  Ne | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | searchservices |  Ne | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  Ne | 
> | playbookconfigurations | Ne | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | brány | Ne | 
> | uzlů | Ne | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | namespaces |  Ne | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace | Ne | 
> | clusters |  Ne | 
> | clustery/aplikace | Ne | 
> | containergroups | Ne | 
> | containergroupsets | Ne | 
> | edgeclusters | Ne | 
> | networks | Ne | 
> | secretstores | Ne | 
> | volumes | Ne | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace |  Ne | 
> | containergroups | Ne | 
> | brány |  Ne | 
> | networks |  Ne | 
> | záleží |  Ne | 
> | volumes |  Ne |  

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | signalr |  Ne |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | appliancedefinitions | Ne | 
> | appliances | Ne | 
> | applicationdefinitions | Ne | 
> | aplikace | Ne | 
> | jitrequests | Ne | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | instancepools | Ne | 
> | managedinstances | Ano | 
> | managedinstances/databáze | Ano | 
> | servery | Ano | 
> | servery/databáze | Ano | 
> | servery/elasticpools | Ano | 
> | virtualclusters | Ano | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Ne |  
> | sqlvirtualmachines |  Ne |  

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | dwvm | Ne | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storageaccounts | Ano?? | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | mezipaměti | Ne | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storagesyncservices |  Ne | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storagesyncservices | Ne | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storagesyncservices | Ne | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | managers | Ne | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | streamingjobs |  Ne |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | environments | Ne | 
> | prostředí/EventSources | Ne | 
> | instance | Ne | 
> | instance/prostředí | Ne | 
> | instance/prostředí/EventSources | Ne | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | providerregistrations | Ne | 
> | prostředky | Ne | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | environments |  Ne | 
> | prostředí/EventSources |  Ne |  
> | prostředí/referencedatasets |  Ne | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Store | Ne | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | imagetemplates | Ne | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | account |  Ne | 
> | účet/rozšíření |  Ne | 
> | účet/projekt |  Ne | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Ne | 
> | dedicatedcloudservices | Ne | 
> | virtualmachines | Ne | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | certificates | Ne | 
> | connectiongateways |  Ne |  
> | connections |  Ne |  
> | customapis |  Ne | 
> | hostingenvironments | Ne | 
> | serverfarms |  Ne |  
> | sites |  Ne | 
> | lokality/premieraddons |  Ne |  
> | lokality/sloty |  Ne |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | deviceservices | Ne | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | applicationgroups | Ne | 
> | hostpools | Ne | 
> | workspaces | Ne | 

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

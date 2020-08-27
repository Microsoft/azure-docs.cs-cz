---
title: Podpora přesunu prostředků Azure napříč oblastmi
description: Zobrazí seznam typů prostředků Azure, které se dají přesunout napříč oblastmi Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 8c18a4d2fa6e5bdb211b77d4d7bb28af7e5b1c1a
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "88948109"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Podpora přesunu prostředků Azure napříč oblastmi

Tento článek potvrzuje, jestli je typ prostředku Azure podporovaný pro přesun do jiné oblasti Azure. 

Přejít na obor názvů poskytovatele prostředků:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. Azureactivedirectory selhala](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. detail](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Cognitiveservices Account](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. Container](#microsoftcontainer)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Cihly Microsoft.](#microsoftdatabricks)
> - [Microsoft. datacatalog](#microsoftdatacatalog)
> - [Microsoft. DataConnect](#microsoftdataconnect)
> - [Microsoft. dataexchange](#microsoftdataexchange)
> - [Microsoft. DataFactory](#microsoftdatafactory)
> - [Microsoft. datalake](#microsoftdatalake)
> - [Microsoft. DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft. DataLakeStore](#microsoftdatalakestore)
> - [Migrace Microsoft.](#microsoftdatamigration)
> - [Microsoft. datashare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Trezor Microsoft.](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft. MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. migruje](#microsoftmigrate)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PortalSdk](#microsoftportalsdk)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectOxford](#microsoftprojectoxford)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. TerraformOSS](#microsoftterraformoss)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | domainservices | Ne | 
> | domainservices / replicasets | Ne | 

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | tenantů | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | actionrules | Ne | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | servery | Ne |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | service |  Ano (pomocí šablony) <br/><br/> [Přesunutí API Management napříč oblastmi](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | configurationstores | Ne | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | apiapps | Ano (pomocí šablony)<br/><br/> [Přesunutí aplikace App Service do jiné oblasti](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Ne | 
> | brány | Ne | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | policyassignments | Ne |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | automationaccounts | Ano (pomocí šablony) <br/><br/> [Použití geografické replikace](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfigurace | Ne | 
> | automationaccounts/Runbooky | Ne | 



## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | b2cdirectories | Ne | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | sqlserverregistrations | Ne |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | rozpoznávání | Ne | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | batchaccounts |  Účty Batch nejde přesunout přímo z jedné oblasti do druhé, ale šablonu můžete použít k exportu šablony, její úpravě a nasazení šablony do nové oblasti. <br/><br/> Další informace o [přesunu účtu Batch napříč oblastmi](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující | Ne <br/><br/> Služba Azure Batch AI je [vyřazena](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai).
> | servery | Ne | 
> | úlohy | Ne | 
> | pracovní prostory | Ne | 

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | mapapis | Ne | 

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | biztalk | Ne | 

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | blockchainmembers | Ne <br/><br/> Síť blockchain nemůže mít uzly v různých oblastech. 
> | sledovacích procesů | Ne | 

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | blueprintassignments | Ne | 

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | botservices | Ne | 

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Redis | Ne | 


## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Ne |
> | uživatelů | Ne | 
> | profily/koncové body | Ne | 

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | certificateorders | Ne | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | domainnames | Pro klasické služby se neplánuje žádná práce.
> | virtualmachines | Ne | 



## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | networksecuritygroups | Pro klasické služby se neplánuje žádná práce.
> | reservedips | Ne | 
> | virtualnetworks | Ne | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storageaccounts | Ano |  


## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 
> | Cognitive Search | Podporováno v ručních krocích.<br/><br/> Přečtěte si o [přesunu služby Azure kognitivní hledání do jiné oblasti](../../search/search-howto-move-across-regions.md) .

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | availabilitysets | Ne | 
> | diskencryptionsets | Ne | 
> | disků | Ne | 
> | Galerie | Ne | 
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

## <a name="microsoftcontainer"></a>Microsoft. Container

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containergroups | Ne | 

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containergroups | Ne | 

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | registr | Ne |  
> | Registry/BuildTasks | Ne |  
> | Registry/replikace | Ne | 
> | Registry/úlohy | Ne |  
> | Registry a Webhooky | Ne | 

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containerservices | No.<br/><br/> Služba je [Vyřazená](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/).
> | managedclusters | Ne | 
> | openshiftmanagedclusters | Ne | 

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace | Ne | 

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | konektory | Ne |  

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Hubs | Ne |  

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | resourceproviders | Ne | 

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | úlohy | Ne | 

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | databoxedgedevices | Ne | 

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | pracovní prostory | Ne | 

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | spustí | Ne | 
> | datacatalogs | Ne | 

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | connectionmanagers | Ne | 

## <a name="microsoftdataexchange"></a>Microsoft. dataexchange

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | zásilk | Ne | 
> | plánují | Ne | 

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | objekty DataFactory | Ne | 
> | továrny | Ne |  

## <a name="microsoftdatalake"></a>Microsoft. datalake

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | datalakeaccounts | Ne | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | services | Ne | 
> | služby a projekty | Ne | 
> | otvory | Ne | 

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | servery | K přesunutí stávajícího serveru můžete použít repliku čtení mezi oblastmi. [Přečtěte si další informace](../../postgresql/howto-move-regions-portal.md).<br/><br/> Pokud je služba zřízená s geograficky redundantním úložištěm zálohování, můžete k obnovení v jiných oblastech použít geografické obnovení. [Přečtěte si další informace](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | servery | K přesunutí stávajícího serveru můžete použít repliku čtení mezi oblastmi. [Přečtěte si další informace](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | servergroups | Ne | 
> | servery | K přesunutí stávajícího serveru můžete použít repliku čtení mezi oblastmi. Další [informace](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Ne | 

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | artifactsources | Ne | 
> | uvádění | Ne |  
> | servicetopologies | Ne | 
> | servicetopologies/služby | Ne |  
> | servicetopologies/služby/serviceunits | Ne | 
> | kroky | Ne | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | elasticpools | No. Prostředek není vystavený.
> | elasticpools / iothubtenants | No. Prostředek není vystavený.
> | iothubs | Yes. [Další informace](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Ne | 

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | kontrolery | Ne | 
> | Cluster AKS | Ne<br/><br/> [Přečtěte si další informace](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) o přesunu do jiné oblasti.

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | labcenters | Ne | 
> | Labs | Ne | 
> | laboratoře/prostředí | Ne |  
> | Labs/servicerunners | Ne | 
> | Labs/VirtualMachines | Ne |  
> | časových | Ne |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | databaseaccounts | Ne | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | doménu | Ne | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | services | Ne |  

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | doménu | Ne |  
> | popisující | Ne | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující | Ne |  
> | obsažené | Ano (se šablonou)<br/><br/> [Přesunutí oboru názvů centra událostí do jiné oblasti](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hanainstances | Ne | 
> | sapmonitors | Ne |  

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující | Ne | 

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | services | Ne |  

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | virtuální | Ne | 

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | datamanagery |  Ne | 

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | úlohy |  Ne | 

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 
> | actiongroups |  Ne | 
> | upozorněníprotokoluaktivit | Ne | 
> | alertrules |  Ne | 
> | autoscalesettings |  Ne | 
> | konstrukční |  Ne |  
> | guestdiagnosticsettings | Ne | 
> | metricalerts | Ne | 
> | notificationgroups | Ne | 
> | notificationrules | Ne | 
> | scheduledqueryrules |  Ne | 
> | webové testy |  Ne | 
> | sešity |  Ne |  


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | checknameavailability |  No.<br/><br/> IoT Central pracuje s geografickými oblastmi a ne oblastmi.
> | zapisovací | Ne

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> |  iothub |  Ano (klonovat centrum) <br/><br/> [Klonování služby IoT Hub do jiné oblasti](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | checknameavailability |  Ne |  
> | zapisovací |  Ne | 

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hsmpools | Ne | 
> | trezory |  Ne | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující |  Ne |  

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | labaccounts | Ne | 

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne, jedná se o globální službu.

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hostingenvironments | Ne | 
> | integrationaccounts |  Ne |  
> | integrationserviceenvironments | Ne | 
> | isolatedenvironments | Ne | 
> | Zpracovávaný |  Ne |  

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | commitmentplans |  Ne | 
> | WebServices |  Ne | 
> | pracovní prostory |  Ne | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

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

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hostingaccounts | Ne | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | pracovní prostory | Ne | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | userassignedidentities | Ne | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts |  Ne, Azure Maps je geoprostorové služba. 

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | classicdevservices | Pro klasické služby se neplánuje žádná práce. 

## <a name="microsoftmedia"></a>Microsoft. Media

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

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | assessmentprojects | Ne | 
> | migrateprojects | Ne | 
> | projekty | Ne | 

## <a name="microsoftnetapp"></a>Microsoft. NetApp

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
> | připojení |  Ne | 
> | ddoscustompolicies |  Ne | 
> | ddosprotectionplans | Ne | 
> | dnszones |  Ne | 
> | expressroutecircuits | Ne | 
> | expressroutecrossconnections | Ne | 
> | expressroutegateways | Ne | 
> | expressrouteports | Ne | 
> | frontdoors | Ne | 
> | frontdoorwebapplicationfirewallpolicies | Ne | 
> | loadbalancers | Ano <br/><br/> Existující konfiguraci můžete exportovat jako šablonu a šablonu nasadit do nové oblasti. Přečtěte si, jak přesunout [externí](../..//load-balancer/move-across-regions-external-load-balancer-portal.md) nebo [interní](../../load-balancer/move-across-regions-internal-load-balancer-portal.md) Nástroj pro vyrovnávání zatížení. |
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
> | publicipaddresses | Ano<br/><br/> Existující konfiguraci veřejné IP adresy můžete exportovat jako šablonu a nasadit šablonu do nové oblasti. [Přečtěte si další informace](../../virtual-network/move-across-regions-publicip-portal.md) o přesunu veřejné IP adresy. |
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


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | obsažené |  Ne | 
> | obory názvů/notificationhubs |  Ne |  

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | pracovní prostory |  Ne | 



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | managementconfigurations |  Ne | 
> | zobrazení |  Ne | 

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | partnerské vztahy | Ne | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | řídicí panely | Ne | 

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | rootresources | Ne | 

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | workspacecollections |  Ne | 

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | schopností |  Ne | 

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | trezory | No.<br/><br/> Přesunutí Recovery Services trezorů pro Azure Backup napříč oblastmi Azure se nepodporuje.<br/><br/> V Recovery Services trezory pro Azure Site Recovery můžete [trezor zakázat a znovu vytvořit](../../site-recovery/move-vaults-across-regions.md) v cílové oblasti. | 


## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | obsažené |  Ne | 

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | odešle |  Ne |  

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti |
> | ------------- | ----------- |
> | deploymentScripts |  Ano<br/><br/>[Přesunout prostředky Microsoft. Resources do nové oblasti](microsoft-resources-move-regions.md) |
> | templateSpecs |  Ano<br/><br/>[Přesunout prostředky Microsoft. Resources do nové oblasti](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace |  Ne | 

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | flows |  Ne |  
> | jobcollections |  Ne | 

## <a name="microsoftsearch"></a>Microsoft. Search

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

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | brány | Ne | 
> | sortiment | Ne | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | obsažené |  Ne | 

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace | Ne | 
> | existující |  Ne | 
> | clustery/aplikace | Ne | 
> | containergroups | Ne | 
> | containergroupsets | Ne | 
> | edgeclusters | Ne | 
> | sítí | Ne | 
> | secretstores | Ne | 
> | volumes | Ne | 

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace |  Ne | 
> | containergroups | Ne | 
> | brány |  Ne | 
> | sítí |  Ne | 
> | záleží |  Ne | 
> | volumes |  Ne |  

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | SignalR |  Ne |  

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | appliancedefinitions | Ne | 
> | zařízení | Ne | 
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

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

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
> | storageaccounts | Ano<br/><br/> [Přesunutí účtu Azure Storage do jiné oblasti](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | mezipaměti | Ne | 

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storagesyncservices |  Ne | 

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storagesyncservices | Ne | 

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storagesyncservices | Ne | 

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | jednatel | Ne | 

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | streamingjobs |  Ne |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Environment | Ne | 
> | prostředí/EventSources | Ne | 
> | instance | Ne | 
> | instance/prostředí | Ne | 
> | instance/prostředí/EventSources | Ne | 

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | providerregistrations | Ne | 
> | resources | Ne | 

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Environment |  Ne | 
> | prostředí/EventSources |  Ne |  
> | prostředí/referencedatasets |  Ne | 

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Store | Ne | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | imagetemplates | Ne | 

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | account |  Ne | 
> | účet/rozšíření |  Ne | 
> | účet/projekt |  Ne | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Ne | 
> | dedicatedcloudservices | Ne | 
> | virtualmachines | Ne | 

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | certifikáty | Ne | 
> | connectiongateways |  Ne |  
> | připojení |  Ne |  
> | customapis |  Ne | 
> | hostingenvironments | Ne | 
> | serverových farem |  Ne |  
> | místa |  Ne | 
> | lokality/premieraddons |  Ne |  
> | lokality/sloty |  Ne |  


## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | deviceservices | Ne | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | applicationgroups | Ne | 
> | hostpools | Ne | 
> | pracovní prostory | Ne | 

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

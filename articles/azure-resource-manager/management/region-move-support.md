---
title: Podpora přesunu prostředků Azure napříč oblastmi
description: Zobrazí seznam typů prostředků Azure, které se dají přesunout napříč oblastmi Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 608d9511b14ef8dd3815d6f9b45cda31e6b38b94
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004302"
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
> | domainservices | No | 
> | domainservices / replicasets | No | 

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | tenantů | No |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | actionrules | No | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | servery | No |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | service |  Ano (pomocí šablony) <br/><br/> [Přesunutí API Management napříč oblastmi](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | configurationstores | No | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | apiapps | Ano (pomocí šablony)<br/><br/> [Přesunutí aplikace App Service do jiné oblasti](../../app-service/manage-move-across-regions.md) | 
> | appidentities | No | 
> | brány | No | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | policyassignments | No |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | automationaccounts | Ano (pomocí šablony) <br/><br/> [Použití geografické replikace](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfigurace | No | 
> | automationaccounts/Runbooky | No | 



## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | b2cdirectories | No | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | sqlserverregistrations | No |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | rozpoznávání | No | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | batchaccounts |  Účty Batch nejde přesunout přímo z jedné oblasti do druhé, ale šablonu můžete použít k exportu šablony, její úpravě a nasazení šablony do nové oblasti. <br/><br/> Další informace o [přesunu účtu Batch napříč oblastmi](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující | No <br/><br/> Služba Azure Batch AI je [vyřazena](/previous-versions/azure/batch-ai/overview-what-happened-batch-ai).
> | servery | No | 
> | úlohy | No | 
> | pracovní prostory | No | 

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | mapapis | No | 

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | biztalk | No | 

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | blockchainmembers | No <br/><br/> Síť blockchain nemůže mít uzly v různých oblastech. 
> | sledovacích procesů | No | 

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | blueprintassignments | No | 

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | botservices | No | 

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Redis | No | 


## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | No |
> | uživatelů | No | 
> | profily/koncové body | No | 

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | certificateorders | No | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | domainnames | Pro klasické služby se neplánuje žádná práce.
> | virtualmachines | No | 



## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | networksecuritygroups | Pro klasické služby se neplánuje žádná práce.
> | reservedips | No | 
> | virtualnetworks | No | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storageaccounts | Yes |  


## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 
> | Cognitive Search | Podporováno v ručních krocích.<br/><br/> Přečtěte si o [přesunu služby Azure kognitivní hledání do jiné oblasti](../../search/search-howto-move-across-regions.md) .

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | availabilitysets | Yes <br/><br/> K přesunutí skupin dostupnosti použijte [prostředek Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | diskencryptionsets | No | 
> | disků | Yes <br/><br/> K přesunutí virtuálních počítačů Azure a souvisejících disků použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | Galerie | No | 
> | Galerie/image | No | 
> | Galerie/image/verze | No | 
> | hostgroups | No | 
> | hostgroups/hostitelé | No | 
> | images | No | 
> | proximityplacementgroups | No | 
> | restorepointcollections | No | 
> | sharedvmimages | No | 
> | sharedvmimages/verze | No | 
> | snímky | No | 
> | virtualmachines | Yes <br/><br/> K přesunu virtuálních počítačů Azure použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | VirtualMachines/rozšíření | No | 
> | virtualmachinescalesets | No | 

## <a name="microsoftcontainer"></a>Microsoft. Container

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containergroups | No | 

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containergroups | No | 

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | registr | No |  
> | Registry/BuildTasks | No |  
> | Registry/replikace | No | 
> | Registry/úlohy | No |  
> | Registry a Webhooky | No | 

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containerservices | No.<br/><br/> Služba je [Vyřazená](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/).
> | managedclusters | No | 
> | openshiftmanagedclusters | No | 

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace | No | 

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | konektory | No |  

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Hubs | No |  

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | resourceproviders | No | 

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | úlohy | No | 

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | databoxedgedevices | No | 

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | pracovní prostory | No | 

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | spustí | No | 
> | datacatalogs | No | 

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | connectionmanagers | No | 

## <a name="microsoftdataexchange"></a>Microsoft. dataexchange

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | zásilk | No | 
> | plánují | No | 

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | objekty DataFactory | No | 
> | továrny | No |  

## <a name="microsoftdatalake"></a>Microsoft. datalake

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | datalakeaccounts | No | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | services | No | 
> | služby a projekty | No | 
> | otvory | No | 

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 

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
> | servergroups | No | 
> | servery | K přesunutí stávajícího serveru můžete použít repliku čtení mezi oblastmi. Další [informace](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | No | 

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | artifactsources | No | 
> | uvádění | No |  
> | servicetopologies | No | 
> | servicetopologies/služby | No |  
> | servicetopologies/služby/serviceunits | No | 
> | kroky | No | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | elasticpools | No. Prostředek není vystavený.
> | elasticpools / iothubtenants | No. Prostředek není vystavený.
> | iothubs | Yes. [Další informace](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | No | 

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | kontrolery | No | 
> | Cluster AKS | No<br/><br/> [Přečtěte si další informace](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) o přesunu do jiné oblasti.

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | labcenters | No | 
> | Labs | No | 
> | laboratoře/prostředí | No |  
> | Labs/servicerunners | No | 
> | Labs/VirtualMachines | No |  
> | časových | No |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | databaseaccounts | No | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | doménu | No | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | doménu | No |  
> | popisující | No | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující | No |  
> | obsažené | Ano (se šablonou)<br/><br/> [Přesunutí oboru názvů centra událostí do jiné oblasti](../../event-hubs/move-across-regions.md) | 

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hanainstances | No | 
> | sapmonitors | No |  

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující | No | 

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | virtuální | No | 

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | datamanagery |  No | 

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | úlohy |  No | 

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 
> | actiongroups |  No | 
> | upozorněníprotokoluaktivit | No | 
> | alertrules |  No | 
> | autoscalesettings |  No | 
> | konstrukční |  No |  
> | guestdiagnosticsettings | No | 
> | metricalerts | No | 
> | notificationgroups | No | 
> | notificationrules | No | 
> | scheduledqueryrules |  No | 
> | webové testy |  No | 
> | sešity |  No |  


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | checknameavailability |  No.<br/><br/> IoT Central pracuje s geografickými oblastmi a ne oblastmi.
> | zapisovací | No

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> |  iothub |  Ano (klonovat centrum) <br/><br/> [Klonování služby IoT Hub do jiné oblasti](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | checknameavailability |  No |  
> | zapisovací |  No | 

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hsmpools | No | 
> | trezory |  No | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující |  No |  

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | labaccounts | No | 

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne, jedná se o globální službu.

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hostingenvironments | No | 
> | integrationaccounts |  No |  
> | integrationserviceenvironments | No | 
> | isolatedenvironments | No | 
> | Zpracovávaný |  No |  

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | commitmentplans |  No | 
> | WebServices |  No | 
> | pracovní prostory |  No | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | operationalizationclusters |  No | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 
> | účty/pracovní prostory | No | 
> | účty/pracovní prostory/projekty | No | 
> | teamaccounts | No | 
> | teamaccounts/pracovní prostory | No | 
> | teamaccounts/pracovní prostory/projekty | No | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft. MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hostingaccounts | No | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | pracovní prostory | No | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | userassignedidentities | No | 

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
> | MediaServices |  No | 
> | MediaServices/liveevents |  No | 
> | MediaServices/starají |  No | 

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | appclusters | No | 

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | assessmentprojects | No | 
> | migrateprojects | No | 
> | projekty | No | 

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | netappaccounts | No | 
> | netappaccounts / capacitypools | No | 
> | netappaccounts/capacitypools/svazky | No | 
> | netappaccounts/capacitypools/svazky/mounttargets | No | 
> | netappaccounts/capacitypools/svazky/snímky | No | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | applicationgateways | No | 
> | applicationgatewaywebapplicationfirewallpolicies | No | 
> | applicationsecuritygroups |  No |  
> | azurefirewalls |  No |  
> | bastionhosts | No | 
> | připojení |  No | 
> | ddoscustompolicies |  No | 
> | ddosprotectionplans | No | 
> | dnszones |  No | 
> | expressroutecircuits | No | 
> | expressroutecrossconnections | No | 
> | expressroutegateways | No | 
> | expressrouteports | No | 
> | frontdoors | No | 
> | frontdoorwebapplicationfirewallpolicies | No | 
> | loadbalancers | Yes <br/><br/> K přesunutí interních a externích nástrojů pro vyrovnávání zatížení použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | localnetworkgateways |  No | 
> | natgateways |  No | 
> | networkintentpolicies |  No | 
> | networkinterfaces | Yes <br/><br/> K přesunutí síťových adaptérů použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkprofiles | No | 
> | networksecuritygroups | Yes <br/><br/> K přesunutí skupin zabezpečení sítě (NGSs) použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkwatchers |  No |  
> | networkwatchers / connectionmonitors |  No | 
> | networkwatchers/čočky |  No | 
> | networkwatchers / pingmeshes |  No | 
> | p2svpngateways | No | 
> | privatednszones |  No |  
> | privatednszones / virtualnetworklinks |  No |  
> | privateendpoints | No | 
> | privatelinkservices | No | 
> | publicipaddresses | Yes<br/><br/> Pomocí služby [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) PŘESUŇTE veřejné IP adresy. |
> | publicipprefixes | No | 
> | routefilters | No | 
> | routetables |  No | 
> | serviceendpointpolicies |  No | 
> | trafficmanagerprofiles |  No | 
> | virtualhubs | No | 
> | virtualnetworkgateways |  No |  
> | virtualnetworks |  No | 
> | virtualnetworktaps | No | 
> | virtualwans | No | 
> | vpngateways (virtuální síť WAN) | No | 
> | vpnsites (virtuální síť WAN) | No | 
> | webapplicationfirewallpolicies |  No | 


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | obsažené |  No | 
> | obory názvů/notificationhubs |  No |  

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | pracovní prostory |  No | 



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | managementconfigurations |  No | 
> | zobrazení |  No | 

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | partnerské vztahy | No | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | řídicí panely | No | 

## <a name="microsoftportalsdk"></a>Microsoft. PortalSdk

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | rootresources | No | 

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | workspacecollections |  No | 

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | schopností |  No | 

## <a name="microsoftprojectoxford"></a>Microsoft. ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | trezory | No.<br/><br/> Přesunutí Recovery Services trezorů pro Azure Backup napříč oblastmi Azure se nepodporuje.<br/><br/> V Recovery Services trezory pro Azure Site Recovery můžete [trezor zakázat a znovu vytvořit](../../site-recovery/move-vaults-across-regions.md) v cílové oblasti. | 


## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | obsažené |  No | 

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | odešle |  No |  

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti |
> | ------------- | ----------- |
> | deploymentScripts |  Yes<br/><br/>[Přesunout prostředky Microsoft. Resources do nové oblasti](microsoft-resources-move-regions.md) |
> | templateSpecs |  Yes<br/><br/>[Přesunout prostředky Microsoft. Resources do nové oblasti](microsoft-resources-move-regions.md) |  

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace |  No | 

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | flows |  No |  
> | jobcollections |  No | 

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | searchservices |  No | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  No | 
> | playbookconfigurations | No | 

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | brány | No | 
> | sortiment | No | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | obsažené |  No | 

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace | No | 
> | existující |  No | 
> | clustery/aplikace | No | 
> | containergroups | No | 
> | containergroupsets | No | 
> | edgeclusters | No | 
> | sítí | No | 
> | secretstores | No | 
> | volumes | No | 

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace |  No | 
> | containergroups | No | 
> | brány |  No | 
> | sítí |  No | 
> | záleží |  No | 
> | volumes |  No |  

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | SignalR |  No |  

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | appliancedefinitions | No | 
> | zařízení | No | 
> | applicationdefinitions | No | 
> | aplikace | No | 
> | jitrequests | No | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | instancepools | No | 
> | managedinstances | Yes <br/><br/> [Přečtěte si další informace](/azure/azure-sql/database/move-resources-across-regions) o přesouvání spravovaných instancí napříč oblastmi. | 
> | managedinstances/databáze | Yes | 
> | servery | Yes | 
> | servery/databáze | Yes <br/><br/> [Přečtěte si další informace](/azure/azure-sql/database/move-resources-across-regions) o přesouvání databází napříč oblastmi.<br/><br/> [Přečtěte si další informace](../../resource-mover/tutorial-move-region-sql.md) o použití Azure Resource stěhovací k přesunu databází SQL Azure.  | 
> | servery/elasticpools | Yes <br/><br/> [Přečtěte si další informace](/azure/azure-sql/database/move-resources-across-regions) o přesouvání elastických fondů napříč oblastmi.<br/><br/> [Přečtěte si další informace](../../resource-mover/tutorial-move-region-sql.md) o použití Azure Resource stěhovací k přesunu elastických fondů Azure SQL.  | 
> | virtualclusters | Yes | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  No |  
> | sqlvirtualmachines |  No |  

## <a name="microsoftsqlvm"></a>Microsoft. SqlVM

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | dwvm | No | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storageaccounts | Yes<br/><br/> [Přesunutí účtu Azure Storage do jiné oblasti](../../storage/common/storage-account-move.md) | 

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | mezipaměti | No | 

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storagesyncservices |  No | 

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | jednatel | No | 

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | streamingjobs |  No |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Environment | No | 
> | prostředí/EventSources | No | 
> | instance | No | 
> | instance/prostředí | No | 
> | instance/prostředí/EventSources | No | 

## <a name="microsoftterraformoss"></a>Microsoft. TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | providerregistrations | No | 
> | resources | No | 

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Environment |  No | 
> | prostředí/EventSources |  No |  
> | prostředí/referencedatasets |  No | 

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Store | No | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | imagetemplates | No | 

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | account |  No | 
> | účet/rozšíření |  No | 
> | účet/projekt |  No | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | No | 
> | dedicatedcloudservices | No | 
> | virtualmachines | No | 

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | certifikáty | No | 
> | connectiongateways |  No |  
> | připojení |  No |  
> | customapis |  No | 
> | hostingenvironments | No | 
> | serverových farem |  No |  
> | místa |  No | 
> | lokality/premieraddons |  No |  
> | lokality/sloty |  No |  


## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | deviceservices | No | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft. WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | applicationgroups | No | 
> | hostpools | No | 
> | pracovní prostory | No | 

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

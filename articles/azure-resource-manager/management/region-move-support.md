---
title: Podpora přesunu prostředků Azure napříč oblastmi
description: Zobrazí seznam typů prostředků Azure, které se dají přesunout napříč oblastmi Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 49c5828e02bf96a536ff14f6b84e81f7adbe3090
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98806905"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Podpora přesunu prostředků Azure napříč oblastmi

Tento článek potvrzuje, jestli je typ prostředku Azure podporovaný pro přesun do jiné oblasti Azure. 

Přejít na obor názvů poskytovatele prostředků:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. aadiam](#microsoftaadiam)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. AppService](#microsoftappservice)
> - [Microsoft. Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. Azureactivedirectory selhala](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. fakturace](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. detail](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. ClassicSubscription](#microsoftclassicsubscription)
> - [Microsoft. Cognitiveservices Account](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. spotřeba](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. ContentModerator](#microsoftcontentmoderator)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
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
> - [Microsoft. DataProtection](#microsoftdataprotection)
> - [Microsoft. datashare](#microsoftdatashare)
> - [Microsoft. DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft. DBforMySQL](#microsoftdbformysql)
> - [Microsoft. DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft. DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft. DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. experimentování](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. genomika](#microsoftgenomics)
> - [Microsoft. GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft. HanaOnAzure](#microsofthanaonazure)
> - [Microsoft. HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft. HDInsight](#microsofthdinsight)
> - [Microsoft. HealthcareApis](#microsofthealthcareapis)
> - [Microsoft. HybridCompute](#microsofthybridcompute)
> - [Microsoft. HybridData](#microsofthybriddata)
> - [Microsoft. HybridNetwork](#microsofthybridnetwork)
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Insights](#microsoftinsights)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Trezor Microsoft.](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft. LocationServices](#microsoftlocationservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft. MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft. MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. Maintenance](#microsoftmaintenance)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft. ManagedNetwork](#microsoftmanagednetwork)
> - [Microsoft. ManagedServices](#microsoftmanagedservices)
> - [Microsoft. Management](#microsoftmanagement)
> - [Microsoft. Maps](#microsoftmaps)
> - [Microsoft. Marketplace](#microsoftmarketplace)
> - [Microsoft. MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft. MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft. Media](#microsoftmedia)
> - [Microsoft. Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft. migruje](#microsoftmigrate)
> - [Microsoft. MixedReality](#microsoftmixedreality)
> - [Microsoft. NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft. NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft. ObjectStore](#microsoftobjectstore)
> - [Microsoft. OffAzure](#microsoftoffazure)
> - [Microsoft. OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft. OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft. peering](#microsoftpeering)
> - [Microsoft. PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft. Portal](#microsoftportal)
> - [Microsoft. PowerBI](#microsoftpowerbi)
> - [Microsoft. PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft. ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft. ProviderHub](#microsoftproviderhub)
> - [Microsoft.](#microsoftquantum)
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. RedHatOpenShift](#microsoftredhatopenshift)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft. ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. support](#microsoftsupport)
> - [Microsoft. synapse](#microsoftsynapse)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VisualStudio](#microsoftvisualstudio)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. monitor zátěže byl](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | domainservices | No | 


## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | diagnosticsettings | No |
> | diagnosticsettingscategories | No |
> | privatelinkforazuread | No |
> | tenantů |  No |

## <a name="microsoftaddons"></a>Microsoft. Doplňků

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | supportproviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | aadsupportcases | No |
> | addsservices | No | 
> | technici | No | 
> | anonymousapiusers | No |
> | konfigurace | No | 
> | Protokoly | No | 
> | sestavy | No | 
> | servicehealthmetrics | No | 
> | services | No | 

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | konfiguračních | No | 
> | generaterecommendations | No |
> | zprostředkovatele identity | No |
> | doporučit | No |
> | potlačení | No | 

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | actionrules | No | 
> | výstrahy | No | 
> | alertslist | No | 
> | alertsmetadata | No | 
> | alertssummary | No | 
> | alertssummarylist | No | 
> | smartdetectoralertrules | No | 
> | smartgroups | No | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | servery | No |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | reportfeedback | No |
> | service |  Ano (pomocí šablony) <br/><br/> [Přesunutí API Management napříč oblastmi](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | configurationstores | No | 
> | configurationstores / eventgridfilters | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | návratu | No | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | apiapps | Ano (pomocí šablony)<br/><br/> [Přesunutí aplikace App Service do jiné oblasti](../../app-service/manage-move-across-regions.md) | 
> | appidentities | No | 
> | brány | No | 

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | attestationproviders | No | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | classicadministrators | No | 
> | dataaliasy | No | 
> | denyassignments | No | 
> | elevateaccess | No | 
> | findorphanroleassignments | No | 
> | počtu | No | 
> | oprávnění | No | 
> | policyassignments | No | 
> | policydefinitions | No | 
> | policysetdefinitions | No | 
> | privatelinkassociations | No | 
> | resourcemanagementprivatelinks | No | 
> | RoleAssignments | No | 
> | roleassignmentsusagemetrics | No | 
> | roledefinitions | No | 

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | automationaccounts | Ano (pomocí šablony) <br/><br/> [Použití geografické replikace](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfigurace | No | 
> | automationaccounts/Runbooky | No | 

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | Předplatné |
> | ------------- | ----------- | 
> | privateclouds | No | 


## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | b2cdirectories | No | 
> | b2ctenants | No | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | datacontrollery | No | 
> | hybriddatamanagers | No | 
> | postgresinstances | No | 
> | sqlinstances | No | 
> | sqlmanagedinstances | No |
> | sqlserverinstances | No | 
> | sqlserverregistrations | No |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | cloudmanifestfiles | No |
> | rozpoznávání | No | 

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | existující | No | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | batchaccounts |  Účty Batch nejde přesunout přímo z jedné oblasti do druhé, ale šablonu můžete použít k exportu šablony, její úpravě a nasazení šablony do nové oblasti. <br/><br/> Další informace o [přesunu účtu Batch napříč oblastmi](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | billingaccounts | No | 
> | billingperiods | No | 
> | billingpermissions | No | 
> | billingproperty | No | 
> | billingroleassignments | No | 
> | billingroledefinitions | No | 
> | oddělení | No | 
> | enrollmentaccounts | No | 
> | faktury | No | 
> | Převede | No | 

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
> | cordamembers | No |
> | sledovacích procesů | No | 

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | tokenservices | No |


## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | blueprintassignments | No | 
> | podrobné plány | No |

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
> | redisenterprise | No | 

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | appliedreservations | No | 
> | calculateexchange | No | 
> | calculateprice | No | 
> | calculatepurchaseprice | No | 
> | spustí | No | 
> | commercialreservationorders | No | 
> | výměn | No |
> | reservationorders | No | 
> | rezervace | No | 
> | resources | No | 
> | validatereservationorder | No | 

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | No |
> | edgenodes | No
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
> | možnosti | No | 
> | domainnames | Yes | No |
> | quotas | No | 
> | typ prostředků | No |
> | validatesubscriptionmoveavailability | No | 
> | virtualmachines | No 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | No | 

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | možnosti | No | 
> | expressroutecrossconnections | No | 
> | expressroutecrossconnections/partnerské vztahy | No | 
> | gatewaysupporteddevices | No | 
> | networksecuritygroups | No |
> | quotas | No |
> | reservedips | No | 
> | virtualnetworks | No | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | disků | No | 
> | images | No | 
> | OSImage | No | 
> | osplatformimages | No | 
> | publicimages | No | 
> | quotas | No | 
> | storageaccounts | Yes |  
> | vmimages | No |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | Operace | No | 

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 
> | Cognitive Search | Podporováno v ručních krocích.<br/><br/> Přečtěte si o [přesunu služby Azure kognitivní hledání do jiné oblasti](../../search/search-howto-move-across-regions.md) .

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | ratecard | No | 
> | usageaggregates | No | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | availabilitysets | Yes <br/><br/> K přesunutí skupin dostupnosti použijte [prostředek Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | diskaccesses | No |
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
> | sshpublickeys | No |
> | virtualmachines | Yes <br/><br/> K přesunu virtuálních počítačů Azure použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | VirtualMachines/rozšíření | No | 
> | virtualmachinescalesets | No | 

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | aggregatedcost | No | 
> | Přesun | No | 
> | projektů | No | 
> | poplatky za | No | 
> | costtags | No | 
> | dobropis | No | 
> | stránka events | No | 
> | prognózy | No | 
> | ŠARŽ | No | 
> | tržišť | No | 
> | pricesheets | No | 
> | produktech | No | 
> | reservationdetails | No | 
> | reservationrecommendationdetails | No | 
> | reservationrecommendations | No | 
> | reservationsummaries | No | 
> | reservationtransactions | No | 
> | tags | No | 
> | tenantů | No | 
> | uvedenými | No | 
> | usagedetails | No | 


## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containergroups | No | 
> | serviceassociationlinks | No |


## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | registr | No |  
> | Registry/agentpools | No | 
> | Registry/BuildTasks | No |  
> | Registry/replikace | No | 
> | Registry/úlohy | No |  
> | Registry a Webhooky | No | 

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containerservices | No |
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
> | výstrahy | No | 
> | billingaccounts | No | 
> | projektů | No | 
> | cloudconnectors | No | 
> | konektory | No | 
> | oddělení | No | 
> | použijí | No | 
> | enrollmentaccounts | No | 
> | vývozních | No | 
> | externalbillingaccounts | No | 
> | forecast | No | 
> | query | No | 
> | register | No | 
> | reportconfigs | No | 
> | sestavy | No | 
> | nastavení | No | 
> | showbackrules | No | 
> | zobrazení | No | 

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Hubs | No |  

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | požádal | No | 

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | přidružení | No |
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
> | availableskus | No |
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

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | ---------- |
> | backupvaults | No | 

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


## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | applicationgroups | No | 
> | pracovní prostory | No | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | elasticpools | Ne. Prostředek není vystavený.
> | elasticpools / iothubtenants | Ne. Prostředek není vystavený.
> | iothubs | Ano. [Další informace](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | No | 

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | kontrolery | No | 


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

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Ano, opětovným vytvořením prostředků v nové oblasti. [Další informace](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | databaseaccounts | No | 
> | databaseaccounts | No | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | doménu | No | 
> | generatessorequest | No | 
> | topleveldomains | No | 
> | validatedomainregistrationinformation | No |

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
> | eventsubscriptions | No |
> | extensiontopics | No | 
> | partnernamespaces | No | 
> | partnerregistrations | No | 
> | partnertopics | No | 
> | systemtopics | No | 
> | popisující | No | 
> | topictypes | No | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující | No |  
> | obsažené | Ano (se šablonou)<br/><br/> [Přesunutí oboru názvů centra událostí do jiné oblasti](../../event-hubs/move-across-regions.md) | 
> | skladové | No |  

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | experimentworkspaces | No | 

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | obsažené | No | 

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | featureproviders | No | 
> | funkce | No | 
> | dodavateli | No | 
> | subscriptionfeatureregistrations | No | 

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | automanagedaccounts | No | 
> | automanagedvmconfigurationprofiles | No | 
> | guestconfigurationassignments | No | 
> | Vybavení | No | 
> | softwareupdateprofile | No | 
> | softwareupdates | No | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hanainstances | No | 
> | sapmonitors | No |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | dedicatedhsms | No | 


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
> | počítače/rozšíření | No |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | datamanagery |  No | 

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | zařízení | No | 
> | vnfs | No | 

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | konstrukční | No | 
> | networkscopes | No | 

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | úlohy |  No | 

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne. [Přečtěte si další informace](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | actiongroups |  No | 
> | upozorněníprotokoluaktivit | No | 
> | alertrules |  No | 
> | autoscalesettings |  No | 
> | standardní hodnoty | No |
> | konstrukční |  No |  
> | datacollectionrules | No | 
> | diagnosticsettings | No | 
> | diagnosticsettingscategories | No | 
> | eventcategories | No | 
> | EventType | No | 
> | extendeddiagnosticsettings | No | |
> | guestdiagnosticsettings | No | 
> | listmigrationdate | No | 
> | logdefinitions | No | 
> | logprofiles | No | 
> | Protokoly | No | No |
> | metricalerts | No | 
> | metricbaselines | No | 
> | metricbatch | No | 
> | metricdefinitions | No | 
> | metricnamespaces | No | 
> | metriky | No | 
> | migratealertrules | No |
> | migratetonewpricingmodel | No | 
> | myworkbooks | No |
> | notificationgroups | No | 
> | privatelinkscopes | No |
> | rollbacktolegacypricingmodel | No |
> | scheduledqueryrules |  No | 
> | topologie | No |
> | transactions | No |
> | vminsightsonboardingstatuses | No |
> | webové testy |  No | 
> | webtests/gettestresultfile | No |
> | sešity |  No |  
> | workbooktemplates | No |


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | apptemplates | No | 
> | iotapps | No | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> |  iothub |  Ano (klonovat centrum) <br/><br/> [Klonování služby IoT Hub do jiné oblasti](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti |
> | ------------- | ----------- | 
> | zapisovací | No | 

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | deletedvaults | No |
> | hsmpools | No | 
> | managedhsms | No |
> | trezory |  No | 

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | connectedclusters | No | 
> | registeredsubscriptions | No | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | No | 

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
> | uživatelé | No | 

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
> | integrationserviceenvironments/Inspirujte | No |
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
> | teamaccounts | No | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | pracovní prostory | No | 

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | configurationassignments | Ano. [Další informace](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Ano. [Další informace](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | aktualizovány | No | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | nebyly | No | 
> | userassignedidentities | No | 

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | managednetworks | No | 
> | managednetworks / managednetworkgroups | No |
> | managednetworks / managednetworkpeeringpolicies | No | 
> | oznámení | No | 

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | No | 
> | registrationassignments | No |
> | registrationdefinitions | No | 

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | getentities | No | 
> | managementgroups | No | 
> | managementgroups/nastavení | No | 
> | resources | No | 
> | starttenantbackfill | No | 
> | tenantbackfillstatus | No | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts |  Ne, Azure Maps je geoprostorové služba. 
> | účty/privateatlases | No

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Rozsah | No | 
> | offertypes | No | 
> | privategalleryitems | No | 
> | privatestoreclient | No | 
> | privatestores | No | 
> | produktech | No | 
> | zdrojů | No | 
> | register | No | 

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | classicdevservices | No | 

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | o | No | 
> | offertypes | No | 

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
> | movecollections | No
> | projekty | No | 

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | No | 
> | objectunderstandingaccounts | No | 
> | remoterenderingaccounts | No | 
> | spatialanchorsaccounts | No | 

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
> | bgpservicecommunities | No |
> | připojení |  No | 
> | ddoscustompolicies |  No | 
> | ddosprotectionplans | No | 
> | dnszones |  No | 
> | expressroutecircuits | No | 
> | expressroutegateways | No | 
> | expressrouteserviceproviders | No | 
> | firewallpolicies | No |
> | frontdoors | No | 
> | ipallocations | No |
> | ipgroups | No |
> | loadbalancers | Yes <br/><br/> K přesunutí interních a externích nástrojů pro vyrovnávání zatížení použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | localnetworkgateways |  No | 
> | natgateways |  No | 
> | networkexperimentprofiles | No |
> | networkintentpolicies |  No | 
> | networkinterfaces | Yes <br/><br/> K přesunutí síťových adaptérů použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkprofiles | No | 
> | networksecuritygroups | Yes <br/><br/> K přesunutí skupin zabezpečení sítě (NGSs) použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkwatchers |  No |  
> | networkwatchers / connectionmonitors |  No | 
> | networkwatchers / flowlogs |  No | 
> | networkwatchers / pingmeshes |  No | 
> | p2svpngateways | No | 
> | privatednszones |  No |  
> | privatednszones / virtualnetworklinks | No |> | privatednszonesinternal | No |
> | privateendpointredirectmaps | No |
> | privateendpoints | No | 
> | privatelinkservices | No | 
> | publicipaddresses | Yes<br/><br/> Pomocí služby [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) PŘESUŇTE veřejné IP adresy. |
> | publicipprefixes | No | 
> | routefilters | No | 
> | routetables |  No | 
> | securitypartnerproviders | No |
> | serviceendpointpolicies |  No | 
> | trafficmanagergeographichierarchies | No | 
> | trafficmanagerprofiles |  No | 
> | trafficmanagerusermetricskeys | No |
> | virtualhubs | No | 
> | virtualnetworkgateways |  No |  
> | virtualnetworks |  No | 
> | virtualnetworktaps | No | 
> | virtualwans | No | 
> | vpngateways (virtuální síť WAN) | No | 
> | vpnsites (virtuální síť WAN) | No | 
> | vpnsites (virtuální síť WAN) | No |


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | obsažené |  No | 
> | obory názvů/notificationhubs |  No |  

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | osnamespaces | No | 

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | hypervsites | No | 
> | importsites | No | 
> | serversites | No | 
> | vmwaresites | No | 

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující | No | 
> | deletedworkspaces | No | 
> | linktargets | No | 
> | storageinsightconfigs | No |
> | pracovní prostory | No |



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations |  No | 
> | Řešení | No |
> | zobrazení |  No | 

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | legacypeerings | No | 
> | peerasns | No | 
> | peeringlocations | No | 
> | partnerské vztahy | No | 
> | peeringservicecountries | No | 
> | peeringservicelocations | No | 
> | peeringserviceproviders | No | 
> | peeringservices | No | 

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | policyevents | No | 
> | policystates | No | 
> | policytrackedresources | No | 
> | nápravy | No | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Konzoly nástroje | No |
> | řídicí panely | No | 
> | usersettings | No | 


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

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | availableaccounts | No | 
> | providerregistrations | No | 
> | uvádění | No | 

## <a name="microsoftquantum"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | pracovní prostory | No | 

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | replicationeligibilityresults | No |
> | trezory | Ne.<br/><br/> Přesunutí Recovery Services trezorů pro Azure Backup napříč oblastmi Azure se nepodporuje.<br/><br/> V Recovery Services trezory pro Azure Site Recovery můžete [trezor zakázat a znovu vytvořit](../../site-recovery/move-vaults-across-regions.md) v cílové oblasti. | 

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | openshiftclusters | No | 

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
> | resourcechangedetails | No | 
> | resourcechanges | No | 
> | resources | No | 
> | resourceshistory | No | 
> | subscriptionsstatus | No | 

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | childresources | No | 
> | emergingissues | No | 
> | stránka events | No | 
> | zprostředkovatele identity | No | 
> | připomenutí | No | 

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
> | saasresources | No | 


## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | resourcehealthmetadata | No |
> | searchservices |  No | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | No | 
> | advancedthreatprotectionsettings | No | 
> | výstrahy | No | 
> | allowedconnections | No | 
> | applicationwhitelistings | No | 
> | assessmentmetadata | No | 
> | hodnocení | No | 
> | autodismissalertsrules | No | 
> | automatizace | No | 
> | autoprovisioningsettings | No |
> | complianceresults | No | 
> | předpisů | No | 
> | datacollectionagents | No | 
> | devicesecuritygroups | No | 
> | discoveredsecuritysolutions | No | 
> | externalsecuritysolutions | No | 
> | informationprotectionpolicies | No | 
> | iotsecuritysolutions | No | 
> | iotsecuritysolutions / analyticsmodels | No | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | No | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | No | 
> | jitnetworkaccesspolicies | No | 
> | Zásady | No | 
> | ceny | No | 
> | regulatorycompliancestandards | No | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | No | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | No | 
> | securitycontacts | No | 
> | securitysolutions | No | 
> | securitysolutionsreferencedata | No | 
> | securitystatuses | No | 
> | securitystatusessummaries | No | 
> | servervulnerabilityassessments | No | 
> | nastavení | No | 
> | podhodnocení | No |
> | úlohy | No | 
> | topologie | No | 
> | workspacesettings | No | 

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | agregace | No | 
> | alertrules | No | 
> | alertruletemplates | No | 
> | automationrules | No |
> | věcech | No | 
> | dataconnects | No | 
> | podnikům | No | 
> | entityqueries | No |
> | Incidenty | No | 
> | officeconsents | No | 
> | nastavení | No | 
> | threatintelligence | No | 

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | consoleservices | No | 

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
> | premiummessagingregions | No | 
> | skladové | No | 

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace | No | 
> | existující |  No |  
> | containergroups | No | 
> | containergroupsets | No | 
> | edgeclusters | No | 
> | managedclusters | No |
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

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | uvádění | No | 

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | SignalR |  No |  

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | hybridusebenefits | No | 

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | appliancedefinitions | No | 
> | zařízení | No | 
> | jitrequests | No | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | instancepools | No | 
> | polohy | No |
> | managedinstances | Yes <br/><br/> [Přečtěte si další informace](../../azure-sql/database/move-resources-across-regions.md) o přesouvání spravovaných instancí napříč oblastmi. | 
> | managedinstances/databáze | Yes | 
> | servery | Yes | 
> | servery/databáze | Yes <br/><br/> [Přečtěte si další informace](../../azure-sql/database/move-resources-across-regions.md) o přesouvání databází napříč oblastmi.<br/><br/> [Přečtěte si další informace](../../resource-mover/tutorial-move-region-sql.md) o použití Azure Resource stěhovací k přesunu databází SQL Azure.  | 
> | servery/elasticpools | Yes <br/><br/> [Přečtěte si další informace](../../azure-sql/database/move-resources-across-regions.md) o přesouvání elastických fondů napříč oblastmi.<br/><br/> [Přečtěte si další informace](../../resource-mover/tutorial-move-region-sql.md) o použití Azure Resource stěhovací k přesunu elastických fondů Azure SQL.  | 
> | virtualclusters | Yes | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  No |  
> | sqlvirtualmachines |  No |  


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
> | existující | No |
> | streamingjobs |  No |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Environment | No | 
> | instance | No | 

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | odběru | No | 

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | services | No | 
> | supporttickets | No | 

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | pracovní prostory | No | 
> | pracovní prostory/bigdatapools | No | 
> | pracovní prostory/sqlpools | No | 


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

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | arczones | No | 
> | resourcepools | No | 
> | servery vCenter | No | 
> | virtualmachines | No | 
> | virtualmachinetemplates | No | 
> | virtualnetworks | No | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | No | 
> | dedicatedcloudservices | No | 
> | virtualmachines | No | 

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | zařízení | No | 
> | vnfs | No | 

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | accounts | No | 
> | plánují | No | 
> | registeredsubscriptions | No |


## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | availablestacks | No | 
> | billingmeters | No | 
> | certifikáty | No | 
> | connectiongateways |  No |  
> | připojení |  No |  
> | customapis |  No | 
> | deletedsites | No | 
> | deploymentlocations | No | 
> | geografická umístění | No | 
> | hostingenvironments | No | 
> | kubeenvironments | No | 
> | publishingusers | No |
> | doporučit | No | 
> | resourcehealthmetadata | No | 
> | moduly runtime | No | 
> | serverových farem | No |  
> | serverových farem/eventgridfilters | N
> | místa |  No | 
> | lokality/premieraddons |  No |  
> | lokality/sloty |  No |  
> | sourcecontrols | No |
> | staticsites | No | 

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | multipleactivationkeys | No |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | deviceservices | No | 

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | úlohy | No | 

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | konstrukční | No |
> | componentssummary | No | 
> | monitorinstances | No | 
> | monitorinstancessummary | No | 
> | Monitor | No | 
## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](../../resource-mover/overview.md) o službě Resource stěhovací.


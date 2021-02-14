---
title: Podpora přesunu prostředků Azure napříč oblastmi
description: Zobrazí seznam typů prostředků Azure, které se dají přesunout napříč oblastmi Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 08/25/2020
ms.author: raynew
ms.openlocfilehash: 18d4d84462d528b718d784ff6a16ecf990ed0d20
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100094011"
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
> - [Microsoft. dosah](#microsoftpurview)
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
> | domainservices | Ne | 


## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | diagnosticsettings | Ne |
> | diagnosticsettingscategories | Ne |
> | privatelinkforazuread | Ne |
> | tenantů |  Ne |

## <a name="microsoftaddons"></a>Microsoft. Doplňků

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | supportproviders | Ne |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | aadsupportcases | Ne |
> | addsservices | Ne | 
> | technici | Ne | 
> | anonymousapiusers | Ne |
> | konfigurace | Ne | 
> | Protokoly | Ne | 
> | sestavy | Ne | 
> | servicehealthmetrics | Ne | 
> | services | Ne | 

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | konfiguračních | Ne | 
> | generaterecommendations | Ne |
> | zprostředkovatele identity | Ne |
> | doporučit | Ne |
> | potlačení | Ne | 

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | actionrules | Ne | 
> | výstrahy | Ne | 
> | alertslist | Ne | 
> | alertsmetadata | Ne | 
> | alertssummary | Ne | 
> | alertssummarylist | Ne | 
> | smartdetectoralertrules | Ne | 
> | smartgroups | Ne | 

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | servery | Ne |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | reportfeedback | Ne |
> | service |  Ano (pomocí šablony) <br/><br/> [Přesunutí API Management napříč oblastmi](../../api-management/api-management-howto-migrate.md). | 

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | configurationstores | Ne | 
> | configurationstores / eventgridfilters | Ne |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | návratu | Ne | 

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | apiapps | Ano (pomocí šablony)<br/><br/> [Přesunutí aplikace App Service do jiné oblasti](../../app-service/manage-move-across-regions.md) | 
> | appidentities | Ne | 
> | brány | Ne | 

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | attestationproviders | Ne | 

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | classicadministrators | Ne | 
> | dataaliasy | Ne | 
> | denyassignments | Ne | 
> | elevateaccess | Ne | 
> | findorphanroleassignments | Ne | 
> | počtu | Ne | 
> | oprávnění | Ne | 
> | policyassignments | Ne | 
> | policydefinitions | Ne | 
> | policysetdefinitions | Ne | 
> | privatelinkassociations | Ne | 
> | resourcemanagementprivatelinks | Ne | 
> | RoleAssignments | Ne | 
> | roleassignmentsusagemetrics | Ne | 
> | roledefinitions | Ne | 

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | automationaccounts | Ano (pomocí šablony) <br/><br/> [Použití geografické replikace](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |  
> | automationaccounts/konfigurace | Ne | 
> | automationaccounts/Runbooky | Ne | 

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | Předplatné |
> | ------------- | ----------- | 
> | privateclouds | Ne | 


## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | b2cdirectories | Ne | 
> | b2ctenants | Ne | 

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | datacontrollery | Ne | 
> | hybriddatamanagers | Ne | 
> | postgresinstances | Ne | 
> | sqlinstances | Ne | 
> | sqlmanagedinstances | Ne |
> | sqlserverinstances | Ne | 
> | sqlserverregistrations | Ne |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | cloudmanifestfiles | Ne |
> | rozpoznávání | Ne | 

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | existující | Ne | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | batchaccounts |  Účty Batch nejde přesunout přímo z jedné oblasti do druhé, ale šablonu můžete použít k exportu šablony, její úpravě a nasazení šablony do nové oblasti. <br/><br/> Další informace o [přesunu účtu Batch napříč oblastmi](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | billingaccounts | Ne | 
> | billingperiods | Ne | 
> | billingpermissions | Ne | 
> | billingproperty | Ne | 
> | billingroleassignments | Ne | 
> | billingroledefinitions | Ne | 
> | oddělení | Ne | 
> | enrollmentaccounts | Ne | 
> | faktury | Ne | 
> | Převede | Ne | 

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
> | cordamembers | Ne |
> | sledovacích procesů | Ne | 

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | tokenservices | Ne |


## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | blueprintassignments | Ne | 
> | podrobné plány | Ne |

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
> | redisenterprise | Ne | 

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | appliedreservations | Ne | 
> | calculateexchange | Ne | 
> | calculateprice | Ne | 
> | calculatepurchaseprice | Ne | 
> | spustí | Ne | 
> | commercialreservationorders | Ne | 
> | výměn | Ne |
> | reservationorders | Ne | 
> | rezervace | Ne | 
> | resources | Ne | 
> | validatereservationorder | Ne | 

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | Ne |
> | edgenodes | Ne
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
> | možnosti | Ne | 
> | domainnames | Ano | Ne |
> | quotas | Ne | 
> | typ prostředků | Ne |
> | validatesubscriptionmoveavailability | Ne | 
> | virtualmachines | Ne 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | classicinfrastructureresources | Ne | 

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | možnosti | Ne | 
> | expressroutecrossconnections | Ne | 
> | expressroutecrossconnections/partnerské vztahy | Ne | 
> | gatewaysupporteddevices | Ne | 
> | networksecuritygroups | Ne |
> | quotas | Ne |
> | reservedips | Ne | 
> | virtualnetworks | Ne | 

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | disků | Ne | 
> | images | Ne | 
> | OSImage | Ne | 
> | osplatformimages | Ne | 
> | publicimages | Ne | 
> | quotas | Ne | 
> | storageaccounts | Ano |  
> | vmimages | Ne |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | Operace | Ne | 

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 
> | Cognitive Search | Podporováno v ručních krocích.<br/><br/> Přečtěte si o [přesunu služby Azure kognitivní hledání do jiné oblasti](../../search/search-howto-move-across-regions.md) .

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | ratecard | Ne | 
> | usageaggregates | Ne | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | availabilitysets | Ano <br/><br/> K přesunutí skupin dostupnosti použijte [prostředek Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | diskaccesses | Ne |
> | diskencryptionsets | Ne | 
> | disků | Ano <br/><br/> K přesunutí virtuálních počítačů Azure a souvisejících disků použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
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
> | sshpublickeys | Ne |
> | virtualmachines | Ano <br/><br/> K přesunu virtuálních počítačů Azure použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | VirtualMachines/rozšíření | Ne | 
> | virtualmachinescalesets | Ne | 

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | aggregatedcost | Ne | 
> | Přesun | Ne | 
> | projektů | Ne | 
> | poplatky za | Ne | 
> | costtags | Ne | 
> | dobropis | Ne | 
> | stránka events | Ne | 
> | prognózy | Ne | 
> | ŠARŽ | Ne | 
> | tržišť | Ne | 
> | pricesheets | Ne | 
> | produktech | Ne | 
> | reservationdetails | Ne | 
> | reservationrecommendationdetails | Ne | 
> | reservationrecommendations | Ne | 
> | reservationsummaries | Ne | 
> | reservationtransactions | Ne | 
> | tags | Ne | 
> | tenantů | Ne | 
> | uvedenými | Ne | 
> | usagedetails | Ne | 


## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containergroups | Ne | 
> | serviceassociationlinks | Ne |


## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | registr | Ne |  
> | Registry/agentpools | Ne | 
> | Registry/BuildTasks | Ne |  
> | Registry/replikace | Ne | 
> | Registry/úlohy | Ne |  
> | Registry a Webhooky | Ne | 

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | containerservices | Ne |
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
> | výstrahy | Ne | 
> | billingaccounts | Ne | 
> | projektů | Ne | 
> | cloudconnectors | Ne | 
> | konektory | Ne | 
> | oddělení | Ne | 
> | použijí | Ne | 
> | enrollmentaccounts | Ne | 
> | vývozních | Ne | 
> | externalbillingaccounts | Ne | 
> | forecast | Ne | 
> | query | Ne | 
> | register | Ne | 
> | reportconfigs | Ne | 
> | sestavy | Ne | 
> | nastavení | Ne | 
> | showbackrules | Ne | 
> | zobrazení | Ne | 

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Hubs | Ne |  

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | požádal | Ne | 

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | přidružení | Ne |
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
> | availableskus | Ne |
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

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | ---------- |
> | backupvaults | Ne | 

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


## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | applicationgroups | Ne | 
> | pracovní prostory | Ne | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | elasticpools | No. Prostředek není vystavený.
> | elasticpools / iothubtenants | No. Prostředek není vystavený.
> | iothubs | Ano. [Další informace](../../iot-hub/iot-hub-how-to-clone.md)
> | provisioningservices | Ne | 

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | kontrolery | Ne | 


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

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | digitaltwinsinstances | Ano, opětovným vytvořením prostředků v nové oblasti. [Další informace](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | databaseaccounts | Ne | 
> | databaseaccounts | Ne | 

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | doménu | Ne | 
> | generatessorequest | Ne | 
> | topleveldomains | Ne | 
> | validatedomainregistrationinformation | Ne |

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
> | eventsubscriptions | Ne |
> | extensiontopics | Ne | 
> | partnernamespaces | Ne | 
> | partnerregistrations | Ne | 
> | partnertopics | Ne | 
> | systemtopics | Ne | 
> | popisující | Ne | 
> | topictypes | Ne | 

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující | Ne |  
> | obsažené | Ano (se šablonou)<br/><br/> [Přesunutí oboru názvů centra událostí do jiné oblasti](../../event-hubs/move-across-regions.md) | 
> | skladové | Ne |  

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | experimentworkspaces | Ne | 

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | obsažené | Ne | 

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | featureproviders | Ne | 
> | funkce | Ne | 
> | dodavateli | Ne | 
> | subscriptionfeatureregistrations | Ne | 

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | automanagedaccounts | Ne | 
> | automanagedvmconfigurationprofiles | Ne | 
> | guestconfigurationassignments | Ne | 
> | Vybavení | Ne | 
> | softwareupdateprofile | Ne | 
> | softwareupdates | Ne | 

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | hanainstances | Ne | 
> | sapmonitors | Ne |  

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | dedicatedhsms | Ne | 


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
> | počítače/rozšíření | Ne |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | datamanagery |  Ne | 

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | zařízení | Ne | 
> | vnfs | Ne | 

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | konstrukční | Ne | 
> | networkscopes | Ne | 

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | úlohy |  Ne | 

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | No. [Přečtěte si další informace](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region).
> | actiongroups |  Ne | 
> | upozorněníprotokoluaktivit | Ne | 
> | alertrules |  Ne | 
> | autoscalesettings |  Ne | 
> | standardní hodnoty | Ne |
> | konstrukční |  Ne |  
> | datacollectionrules | Ne | 
> | diagnosticsettings | Ne | 
> | diagnosticsettingscategories | Ne | 
> | eventcategories | Ne | 
> | EventType | Ne | 
> | extendeddiagnosticsettings | Ne | |
> | guestdiagnosticsettings | Ne | 
> | listmigrationdate | Ne | 
> | logdefinitions | Ne | 
> | logprofiles | Ne | 
> | Protokoly | Ne | Ne |
> | metricalerts | Ne | 
> | metricbaselines | Ne | 
> | metricbatch | Ne | 
> | metricdefinitions | Ne | 
> | metricnamespaces | Ne | 
> | metriky | Ne | 
> | migratealertrules | Ne |
> | migratetonewpricingmodel | Ne | 
> | myworkbooks | Ne |
> | notificationgroups | Ne | 
> | privatelinkscopes | Ne |
> | rollbacktolegacypricingmodel | Ne |
> | scheduledqueryrules |  Ne | 
> | topologie | Ne |
> | transactions | Ne |
> | vminsightsonboardingstatuses | Ne |
> | webové testy |  Ne | 
> | webtests/gettestresultfile | Ne |
> | sešity |  Ne |  
> | workbooktemplates | Ne |


## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | apptemplates | Ne | 
> | iotapps | Ne | 



## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> |  iothub |  Ano (klonovat centrum) <br/><br/> [Klonování služby IoT Hub do jiné oblasti](../../iot-hub/iot-hub-how-to-clone.md)

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti |
> | ------------- | ----------- | 
> | zapisovací | Ne | 

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | deletedvaults | Ne |
> | hsmpools | Ne | 
> | managedhsms | Ne |
> | trezory |  Ne | 

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | connectedclusters | Ne | 
> | registeredsubscriptions | Ne | 

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | sourcecontrolconfigurations | Ne | 

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
> | uživatelé | Ne | 

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
> | integrationserviceenvironments/Inspirujte | Ne |
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
> | teamaccounts | Ne | 


## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 


## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | pracovní prostory | Ne | 

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | configurationassignments | Ano. [Další informace](../../virtual-machines/move-region-maintenance-configuration.md) | 
> | maintenanceconfigurations | Ano. [Další informace](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | aktualizovány | Ne | 

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | nebyly | Ne | 
> | userassignedidentities | Ne | 

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | managednetworks | Ne | 
> | managednetworks / managednetworkgroups | Ne |
> | managednetworks / managednetworkpeeringpolicies | Ne | 
> | oznámení | Ne | 

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | marketplaceregistrationdefinitions | Ne | 
> | registrationassignments | Ne |
> | registrationdefinitions | Ne | 

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | getentities | Ne | 
> | managementgroups | Ne | 
> | managementgroups/nastavení | Ne | 
> | resources | Ne | 
> | starttenantbackfill | Ne | 
> | tenantbackfillstatus | Ne | 

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts |  Ne, Azure Maps je geoprostorové služba. 
> | účty/privateatlases | Ne

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Rozsah | Ne | 
> | offertypes | Ne | 
> | privategalleryitems | Ne | 
> | privatestoreclient | Ne | 
> | privatestores | Ne | 
> | produktech | Ne | 
> | zdrojů | Ne | 
> | register | Ne | 

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | classicdevservices | Ne | 

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | o | Ne | 
> | offertypes | Ne | 

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
> | movecollections | Ne
> | projekty | Ne | 

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Ne | 
> | objectunderstandingaccounts | Ne | 
> | remoterenderingaccounts | Ne | 
> | spatialanchorsaccounts | Ne | 

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
> | bgpservicecommunities | Ne |
> | připojení |  Ne | 
> | ddoscustompolicies |  Ne | 
> | ddosprotectionplans | Ne | 
> | dnszones |  Ne | 
> | expressroutecircuits | Ne | 
> | expressroutegateways | Ne | 
> | expressrouteserviceproviders | Ne | 
> | firewallpolicies | Ne |
> | frontdoors | Ne | 
> | ipallocations | Ne |
> | ipgroups | Ne |
> | loadbalancers | Ano <br/><br/> K přesunutí interních a externích nástrojů pro vyrovnávání zatížení použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | localnetworkgateways |  Ne | 
> | natgateways |  Ne | 
> | networkexperimentprofiles | Ne |
> | networkintentpolicies |  Ne | 
> | networkinterfaces | Ano <br/><br/> K přesunutí síťových adaptérů použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkprofiles | Ne | 
> | networksecuritygroups | Ano <br/><br/> K přesunutí skupin zabezpečení sítě (NGSs) použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . | 
> | networkwatchers |  Ne |  
> | networkwatchers / connectionmonitors |  Ne | 
> | networkwatchers / flowlogs |  Ne | 
> | networkwatchers / pingmeshes |  Ne | 
> | p2svpngateways | Ne | 
> | privatednszones |  Ne |  
> | privatednszones / virtualnetworklinks | Ne |> | privatednszonesinternal | Ne |
> | privateendpointredirectmaps | Ne |
> | privateendpoints | Ne | 
> | privatelinkservices | Ne | 
> | publicipaddresses | Ano<br/><br/> Pomocí služby [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) PŘESUŇTE veřejné IP adresy. |
> | publicipprefixes | Ne | 
> | routefilters | Ne | 
> | routetables |  Ne | 
> | securitypartnerproviders | Ne |
> | serviceendpointpolicies |  Ne | 
> | trafficmanagergeographichierarchies | Ne | 
> | trafficmanagerprofiles |  Ne | 
> | trafficmanagerusermetricskeys | Ne |
> | virtualhubs | Ne | 
> | virtualnetworkgateways |  Ne |  
> | virtualnetworks |  Ne | 
> | virtualnetworktaps | Ne | 
> | virtualwans | Ne | 
> | vpngateways (virtuální síť WAN) | Ne | 
> | vpnsites (virtuální síť WAN) | Ne | 
> | vpnsites (virtuální síť WAN) | Ne |


## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | obsažené |  Ne | 
> | obory názvů/notificationhubs |  Ne |  

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | osnamespaces | Ne | 

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | hypervsites | Ne | 
> | importsites | Ne | 
> | serversites | Ne | 
> | vmwaresites | Ne | 

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | existující | Ne | 
> | deletedworkspaces | Ne | 
> | linktargets | Ne | 
> | storageinsightconfigs | Ne |
> | pracovní prostory | Ne |



## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | managementassociations | Ne |
> | managementconfigurations |  Ne | 
> | Řešení | Ne |
> | zobrazení |  Ne | 

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | legacypeerings | Ne | 
> | peerasns | Ne | 
> | peeringlocations | Ne | 
> | partnerské vztahy | Ne | 
> | peeringservicecountries | Ne | 
> | peeringservicelocations | Ne | 
> | peeringserviceproviders | Ne | 
> | peeringservices | Ne | 

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | policyevents | Ne | 
> | policystates | Ne | 
> | policytrackedresources | Ne | 
> | nápravy | Ne | 

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Konzoly nástroje | Ne |
> | řídicí panely | Ne | 
> | usersettings | Ne | 


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

## <a name="microsoftpurview"></a>Microsoft. dosah

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | accounts | Ne | 

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | availableaccounts | Ne | 
> | providerregistrations | Ne | 
> | uvádění | Ne | 

## <a name="microsoftquantum"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | pracovní prostory | Ne | 

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | replicationeligibilityresults | Ne |
> | trezory | No.<br/><br/> Přesunutí Recovery Services trezorů pro Azure Backup napříč oblastmi Azure se nepodporuje.<br/><br/> V Recovery Services trezory pro Azure Site Recovery můžete [trezor zakázat a znovu vytvořit](../../site-recovery/move-vaults-across-regions.md) v cílové oblasti. | 

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | openshiftclusters | Ne | 

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
> | resourcechangedetails | Ne | 
> | resourcechanges | Ne | 
> | resources | Ne | 
> | resourceshistory | Ne | 
> | subscriptionsstatus | Ne | 

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | childresources | Ne | 
> | emergingissues | Ne | 
> | stránka events | Ne | 
> | zprostředkovatele identity | Ne | 
> | připomenutí | Ne | 

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
> | saasresources | Ne | 


## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | resourcehealthmetadata | Ne |
> | searchservices |  Ne | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | adaptivenetworkhardenings | Ne | 
> | advancedthreatprotectionsettings | Ne | 
> | výstrahy | Ne | 
> | allowedconnections | Ne | 
> | applicationwhitelistings | Ne | 
> | assessmentmetadata | Ne | 
> | hodnocení | Ne | 
> | autodismissalertsrules | Ne | 
> | automatizace | Ne | 
> | autoprovisioningsettings | Ne |
> | complianceresults | Ne | 
> | předpisů | Ne | 
> | datacollectionagents | Ne | 
> | devicesecuritygroups | Ne | 
> | discoveredsecuritysolutions | Ne | 
> | externalsecuritysolutions | Ne | 
> | informationprotectionpolicies | Ne | 
> | iotsecuritysolutions | Ne | 
> | iotsecuritysolutions / analyticsmodels | Ne | 
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Ne | 
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Ne | 
> | jitnetworkaccesspolicies | Ne | 
> | Zásady | Ne | 
> | ceny | Ne | 
> | regulatorycompliancestandards | Ne | 
> | regulatorycompliancestandards / regulatorycompliancecontrols | Ne | 
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Ne | 
> | securitycontacts | Ne | 
> | securitysolutions | Ne | 
> | securitysolutionsreferencedata | Ne | 
> | securitystatuses | Ne | 
> | securitystatusessummaries | Ne | 
> | servervulnerabilityassessments | Ne | 
> | nastavení | Ne | 
> | podhodnocení | Ne |
> | úlohy | Ne | 
> | topologie | Ne | 
> | workspacesettings | Ne | 

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | agregace | Ne | 
> | alertrules | Ne | 
> | alertruletemplates | Ne | 
> | automationrules | Ne |
> | věcech | Ne | 
> | dataconnects | Ne | 
> | podnikům | Ne | 
> | entityqueries | Ne |
> | Incidenty | Ne | 
> | officeconsents | Ne | 
> | nastavení | Ne | 
> | threatintelligence | Ne | 

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | consoleservices | Ne | 

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
> | premiummessagingregions | Ne | 
> | skladové | Ne | 

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | aplikace | Ne | 
> | existující |  Ne |  
> | containergroups | Ne | 
> | containergroupsets | Ne | 
> | edgeclusters | Ne | 
> | managedclusters | Ne |
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

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | uvádění | Ne | 

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | SignalR |  Ne |  

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | hybridusebenefits | Ne | 

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | appliancedefinitions | Ne | 
> | zařízení | Ne | 
> | jitrequests | Ne | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | instancepools | Ne | 
> | polohy | Ne |
> | managedinstances | Ano <br/><br/> [Přečtěte si další informace](../../azure-sql/database/move-resources-across-regions.md) o přesouvání spravovaných instancí napříč oblastmi. | 
> | managedinstances/databáze | Ano | 
> | servery | Ano | 
> | servery/databáze | Ano <br/><br/> [Přečtěte si další informace](../../azure-sql/database/move-resources-across-regions.md) o přesouvání databází napříč oblastmi.<br/><br/> [Přečtěte si další informace](../../resource-mover/tutorial-move-region-sql.md) o použití Azure Resource stěhovací k přesunu databází SQL Azure.  | 
> | servery/elasticpools | Ano <br/><br/> [Přečtěte si další informace](../../azure-sql/database/move-resources-across-regions.md) o přesouvání elastických fondů napříč oblastmi.<br/><br/> [Přečtěte si další informace](../../resource-mover/tutorial-move-region-sql.md) o použití Azure Resource stěhovací k přesunu elastických fondů Azure SQL.  | 
> | virtualclusters | Ano | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  Ne |  
> | sqlvirtualmachines |  Ne |  


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
> | existující | Ne |
> | streamingjobs |  Ne |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | Environment | Ne | 
> | instance | Ne | 

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | odběru | Ne | 

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | services | Ne | 
> | supporttickets | Ne | 

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | pracovní prostory | Ne | 
> | pracovní prostory/bigdatapools | Ne | 
> | pracovní prostory/sqlpools | Ne | 


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

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | arczones | Ne | 
> | resourcepools | Ne | 
> | servery vCenter | Ne | 
> | virtualmachines | Ne | 
> | virtualmachinetemplates | Ne | 
> | virtualnetworks | Ne | 

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | Ne | 
> | dedicatedcloudservices | Ne | 
> | virtualmachines | Ne | 

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | zařízení | Ne | 
> | vnfs | Ne | 

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | accounts | Ne | 
> | plánují | Ne | 
> | registeredsubscriptions | Ne |


## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | availablestacks | Ne | 
> | billingmeters | Ne | 
> | certifikáty | Ne | 
> | connectiongateways |  Ne |  
> | připojení |  Ne |  
> | customapis |  Ne | 
> | deletedsites | Ne | 
> | deploymentlocations | Ne | 
> | geografická umístění | Ne | 
> | hostingenvironments | Ne | 
> | kubeenvironments | Ne | 
> | publishingusers | Ne |
> | doporučit | Ne | 
> | resourcehealthmetadata | Ne | 
> | moduly runtime | Ne | 
> | serverových farem | Ne |  
> | serverových farem/eventgridfilters | N
> | místa |  Ne | 
> | lokality/premieraddons |  Ne |  
> | lokality/sloty |  Ne |  
> | sourcecontrols | Ne |
> | staticsites | Ne | 

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | multipleactivationkeys | Ne |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- |
> | deviceservices | Ne | 

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | úlohy | Ne | 

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Přesunutí oblasti | 
> | ------------- | ----------- | 
> | konstrukční | Ne |
> | componentssummary | Ne | 
> | monitorinstances | Ne | 
> | monitorinstancessummary | Ne | 
> | Monitor | Ne | 
## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

## <a name="next-steps"></a>Další kroky

[Přečtěte si další informace](../../resource-mover/overview.md) o službě Resource stěhovací.


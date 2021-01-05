---
title: Přesunout podporu operace podle typu prostředku
description: Zobrazuje seznam typů prostředků Azure, které se dají přesunout do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 72cc2bd1b391acd2065cc4b775c50af94510a840
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97795897"
---
# <a name="move-operation-support-for-resources"></a>Přesunout podporu operací pro prostředky

Tento článek uvádí, jestli typ prostředku Azure podporuje operaci přesunu. Poskytuje taky informace o zvláštních podmínkách, které je potřeba vzít v úvahu při přesunu prostředku.

> [!IMPORTANT]
> Ve většině případů nelze podřízený prostředek přesunout nezávisle na svém nadřazeném prostředku. Podřízené prostředky mají typ prostředku ve formátu `<resource-provider-namespace>/<parent-resource>/<child-resource>` . Například `Microsoft.ServiceBus/namespaces/queues` je podřízeným prostředkem `Microsoft.ServiceBus/namespaces` . Když přesunete nadřazený prostředek, automaticky se s ním přesune podřízený prostředek. Pokud se v tomto článku nezobrazuje podřízený prostředek, můžete předpokládat, že je přesunutý s nadřazeným prostředkem. Pokud nadřazený prostředek nepodporuje přesun, nelze podřízený prostředek přesunout.

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
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | domainservices | Ne | Ne |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | Ne | Ne |
> | diagnosticsettingscategories | Ne | Ne |
> | privatelinkforazuread | Ano | Ano |
> | tenantů | Ano | Ano |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | supportproviders | Ne | Ne |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | Ne | Ne |
> | addsservices | Ne | Ne |
> | technici | Ne | Ne |
> | anonymousapiusers | Ne | Ne |
> | konfigurace | Ne | Ne |
> | Protokoly | Ne | Ne |
> | reports | Ne | Ne |
> | servicehealthmetrics | Ne | Ne |
> | services | Ne | Ne |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konfiguračních | Ne | Ne |
> | generaterecommendations | Ne | Ne |
> | zprostředkovatele identity | Ne | Ne |
> | doporučit | Ne | Ne |
> | potlačení | Ne | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | actionrules | Ano | Ano |
> | výstrahy | Ne | Ne |
> | alertslist | Ne | Ne |
> | alertsmetadata | Ne | Ne |
> | alertssummary | Ne | Ne |
> | alertssummarylist | Ne | Ne |
> | smartdetectoralertrules | Ano | Ano |
> | smartgroups | Ne | Ne |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | servery | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!IMPORTANT]
> Službu API Management, která je nastavená na SKU spotřeby, se nedá přesunout.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | reportfeedback | Ne | Ne |
> | service | Ano | Ano |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ano | Ano |
> | configurationstores / eventgridfilters | Ne | Ne |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | návratu | Ano | Ano |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | apiapps | Ne | Ne |
> | appidentities | Ne | Ne |
> | brány | Ne | Ne |

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Ano | Ano |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicadministrators | Ne | Ne |
> | dataaliasy | Ne | Ne |
> | denyassignments | Ne | Ne |
> | elevateaccess | Ne | Ne |
> | findorphanroleassignments | Ne | Ne |
> | počtu | Ne | Ne |
> | oprávnění | Ne | Ne |
> | policyassignments | Ne | Ne |
> | policydefinitions | Ne | Ne |
> | policysetdefinitions | Ne | Ne |
> | privatelinkassociations | Ne | Ne |
> | resourcemanagementprivatelinks | Ne | Ne |
> | RoleAssignments | Ne | Ne |
> | roleassignmentsusagemetrics | Ne | Ne |
> | roledefinitions | Ne | Ne |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!IMPORTANT]
> Runbooky musí existovat ve stejné skupině prostředků jako účet Automation.
>
> Informace najdete v tématu [přesunutí účtu Azure Automation do jiného předplatného](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | automationaccounts | Ano | Ano |
> | automationaccounts/konfigurace | Ano | Ano |
> | automationaccounts/Runbooky | Ano | Ano |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | privateclouds | Ano | Ano |

## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | b2cdirectories | Ano | Ano |
> | b2ctenants | Ne | Ne |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datacontrollery | Ne | Ne |
> | hybriddatamanagers | Ne | Ne |
> | postgresinstances | Ne | Ne |
> | sqlinstances | Ne | Ne |
> | sqlmanagedinstances | Ne | Ne |
> | sqlserverinstances | Ne | Ne |
> | sqlserverregistrations | Ano | Ano |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | Ne | Ne |
> | rozpoznávání | Ano | Ano |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ne | Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ano | Ano |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | billingaccounts | Ne | Ne |
> | billingperiods | Ne | Ne |
> | billingpermissions | Ne | Ne |
> | billingproperty | Ne | Ne |
> | billingroleassignments | Ne | Ne |
> | billingroledefinitions | Ne | Ne |
> | oddělení | Ne | Ne |
> | enrollmentaccounts | Ne | Ne |
> | faktury | Ne | Ne |
> | Převede | Ne | Ne |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | mapapis | Ne | Ne |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | biztalk | Ne | Ne |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | Ne | Ne |
> | cordamembers | Ne | Ne |
> | sledovacích procesů | Ne | Ne |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | tokenservices | Ne | Ne |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | Ne | Ne |
> | podrobné plány | Ne | Ne |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | botservices | Ano | Ano |

## <a name="microsoftcache"></a>Microsoft. cache

> [!IMPORTANT]
> Pokud je instance Azure cache for Redis nakonfigurovaná pomocí virtuální sítě, nedá se tato instance přesunout do jiného předplatného. Viz [omezení přesunu sítě](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Redis | Ano | Ano |
> | redisenterprise | Ne | Ne |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | appliedreservations | Ne | Ne |
> | calculateexchange | Ne | Ne |
> | calculateprice | Ne | Ne |
> | calculatepurchaseprice | Ne | Ne |
> | spustí | Ne | Ne |
> | commercialreservationorders | Ne | Ne |
> | výměn | Ne | Ne |
> | reservationorders | Ne | Ne |
> | rezervace | Ne | Ne |
> | resources | Ne | Ne |
> | validatereservationorder | Ne | Ne |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | Ne | Ne |
> | cdnwebapplicationfirewallpolicies | Ano | Ano |
> | edgenodes | Ne | Ne |
> | uživatelů | Ano | Ano |
> | profily/koncové body | Ano | Ano |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | certificateorders | Ano | Ano |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | možnosti | Ne | Ne |
> | domainnames | Ano | Ne |
> | quotas | Ne | Ne |
> | typ prostředků | Ne | Ne |
> | validatesubscriptionmoveavailability | Ne | Ne |
> | virtualmachines | Ano | Ano |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | Ne | Ne |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | možnosti | Ne | Ne |
> | expressroutecrossconnections | Ne | Ne |
> | expressroutecrossconnections/partnerské vztahy | Ne | Ne |
> | gatewaysupporteddevices | Ne | Ne |
> | networksecuritygroups | Ne | Ne |
> | quotas | Ne | Ne |
> | reservedips | Ne | Ne |
> | virtualnetworks | Ne | Ne |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | disků | Ne | Ne |
> | images | Ne | Ne |
> | OSImage | Ne | Ne |
> | osplatformimages | Ne | Ne |
> | publicimages | Ne | Ne |
> | quotas | Ne | Ne |
> | storageaccounts | Ano | Ne |
> | vmimages | Ne | Ne |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | Ne | Ne |

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | ratecard | Ne | Ne |
> | usageaggregates | Ne | Ne |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Virtual Machines](./move-limitations/virtual-machines-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Ano | Ano |
> | diskaccesses | Ne | Ne |
> | diskencryptionsets | Ne | Ne |
> | disků | Ano | Ano |
> | Galerie | Ne | Ne |
> | Galerie/image | Ne | Ne |
> | Galerie/image/verze | Ne | Ne |
> | hostgroups | Ne | Ne |
> | hostgroups/hostitelé | Ne | Ne |
> | images | Ano | Ano |
> | proximityplacementgroups | Ano | Ano |
> | restorepointcollections | Ne | Ne |
> | restorepointcollections / restorepoints | Ne | Ne |
> | sharedvmextensions | Ne | Ne |
> | sharedvmimages | Ne | Ne |
> | sharedvmimages/verze | Ne | Ne |
> | snímky | Ano | Ano |
> | sshpublickeys | Ne | Ne |
> | virtualmachines | Ano | Ano |
> | VirtualMachines/rozšíření | Ano | Ano |
> | virtualmachinescalesets | Ano | Ano |

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | Ne | Ne |
> | Přesun | Ne | Ne |
> | projektů | Ne | Ne |
> | poplatky za | Ne | Ne |
> | costtags | Ne | Ne |
> | dobropis | Ne | Ne |
> | stránka events | Ne | Ne |
> | prognózy | Ne | Ne |
> | ŠARŽ | Ne | Ne |
> | tržišť | Ne | Ne |
> | pricesheets | Ne | Ne |
> | produktech | Ne | Ne |
> | reservationdetails | Ne | Ne |
> | reservationrecommendationdetails | Ne | Ne |
> | reservationrecommendations | Ne | Ne |
> | reservationsummaries | Ne | Ne |
> | reservationtransactions | Ne | Ne |
> | tags | Ne | Ne |
> | tenantů | Ne | Ne |
> | uvedenými | Ne | Ne |
> | usagedetails | Ne | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | containergroups | Ne | Ne |
> | serviceassociationlinks | Ne | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | registr | Ano | Ano |
> | Registry/agentpools | Ano | Ano |
> | Registry/BuildTasks | Ano | Ano |
> | Registry/replikace | Ano | Ano |
> | Registry/úlohy | Ano | Ano |
> | Registry a Webhooky | Ano | Ano |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | containerservices | Ne | Ne |
> | managedclusters | Ne | Ne |
> | openshiftmanagedclusters | Ne | Ne |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ne | Ne |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | výstrahy | Ne | Ne |
> | billingaccounts | Ne | Ne |
> | projektů | Ne | Ne |
> | cloudconnectors | Ne | Ne |
> | konektory | Ano | Ano |
> | oddělení | Ne | Ne |
> | použijí | Ne | Ne |
> | enrollmentaccounts | Ne | Ne |
> | vývozních | Ne | Ne |
> | externalbillingaccounts | Ne | Ne |
> | forecast | Ne | Ne |
> | query | Ne | Ne |
> | register | Ne | Ne |
> | reportconfigs | Ne | Ne |
> | reports | Ne | Ne |
> | nastavení | Ne | Ne |
> | showbackrules | Ne | Ne |
> | zobrazení | Ne | Ne |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Hubs | Ne | Ne |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | požádal | Ne | Ne |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | přidružení | Ne | Ne |
> | resourceproviders | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | úlohy | Ne | Ne |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availableskus | Ne | Ne |
> | databoxedgedevices | Ano | Ano |

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | spustí | Ano | Ano |
> | datacatalogs | Ne | Ne |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | Ne | Ne |

## <a name="microsoftdataexchange"></a>Microsoft. dataexchange

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zásilk | Ne | Ne |
> | plánují | Ne | Ne |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | objekty DataFactory | Ano | Ano |
> | továrny | Ano | Ano |

## <a name="microsoftdatalake"></a>Microsoft. datalake

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datalakeaccounts | Ne | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | services | Ne | Ne |
> | služby a projekty | Ne | Ne |
> | otvory | Ne | Ne |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | backupvaults | Ne | Ne |

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | servery | Ano | Ano |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Ano | Ano |
> | servery | Ano | Ano |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | flexibleServers | Ano | Ano |
> | servergroups | Ne | Ne |
> | servery | Ano | Ano |
> | serversv2 | Ano | Ano |
> | singleservers | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | artifactsources | Ano | Ano |
> | uvádění | Ano | Ano |
> | servicetopologies | Ano | Ano |
> | servicetopologies/služby | Ano | Ano |
> | servicetopologies/služby/serviceunits | Ano | Ano |
> | kroky | Ano | Ano |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationgroups | Ano | Ano |
> | hostpools | Ano | Ano |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | elasticpools | Ne | Ne |
> | elasticpools / iothubtenants | Ne | Ne |
> | iothubs | Ano | Ano |
> | provisioningservices | Ano | Ano |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | kanály | Ano | Ano |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | kontrolery | Ano | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | labcenters | Ne | Ne |
> | Labs | Ano | Ne |
> | laboratoře/prostředí | Ano | Ano |
> | Labs/servicerunners | Ano | Ano |
> | Labs/VirtualMachines | Ano | Ne |
> | časových | Ano | Ano |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | Ne | Ne |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | Ne | Ne |
> | databaseaccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | doménu | Ano | Ano |
> | generatessorequest | Ne | Ne |
> | topleveldomains | Ne | Ne |
> | validatedomainregistrationinformation | Ne | Ne |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | services | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | doménu | Ano | Ano |
> | eventsubscriptions | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. |
> | extensiontopics | Ne | Ne |
> | partnernamespaces | Ano | Ano |
> | partnerregistrations | Ne | Ne |
> | partnertopics | Ano | Ano |
> | systemtopics | Ano | Ano |
> | popisující | Ano | Ano |
> | topictypes | Ne | Ne |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |
> | obsažené | Ano | Ano |
> | skladové | Ne | Ne |

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | Ne | Ne |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | featureproviders | Ne | Ne |
> | funkce | Ne | Ne |
> | dodavateli | Ne | Ne |
> | subscriptionfeatureregistrations | Ne | Ne |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | Ne | Ne |
> | automanagedvmconfigurationprofiles | Ne | Ne |
> | guestconfigurationassignments | Ne | Ne |
> | Vybavení | Ne | Ne |
> | softwareupdateprofile | Ne | Ne |
> | softwareupdates | Ne | Ne |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hanainstances | Ne | Ne |
> | sapmonitors | Ne | Ne |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | Ne | Ne |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!IMPORTANT]
> Clustery HDInsight můžete přesunout do nového předplatného nebo skupiny prostředků. Nemůžete se ale pohybovat mezi předplatnými síťových prostředků propojených s clusterem HDInsight (například virtuální síť, síťová karta nebo nástroj pro vyrovnávání zatížení). Kromě toho nemůžete přesunout do nové skupiny prostředků síťovou kartu, která je připojená k virtuálnímu počítači pro daný cluster.
>
> Když přesunete cluster HDInsight do nového předplatného, napřed přesuňte další prostředky (třeba účet úložiště). Pak můžete cluster HDInsight přesunout sám o sobě.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | services | Ano | Ano |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | virtuální | Ano | Ano |
> | počítače/rozšíření | Ano | Ano |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datamanagery | Ano | Ano |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zařízení | Ne | Ne |
> | vnfs | Ne | Ne |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konstrukční | Ne | Ne |
> | networkscopes | Ne | Ne |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | úlohy | Ano | Ano |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | actiongroups | Ano | Ano |
> | upozorněníprotokoluaktivit | Ne | Ne |
> | alertrules | Ano | Ano |
> | autoscalesettings | Ano | Ano |
> | standardní hodnoty | Ne | Ne |
> | konstrukční | Ano | Ano |
> | datacollectionrules | Ne | Ne |
> | diagnosticsettings | Ne | Ne |
> | diagnosticsettingscategories | Ne | Ne |
> | eventcategories | Ne | Ne |
> | EventType | Ne | Ne |
> | extendeddiagnosticsettings | Ne | Ne |
> | guestdiagnosticsettings | Ne | Ne |
> | listmigrationdate | Ne | Ne |
> | logdefinitions | Ne | Ne |
> | logprofiles | Ne | Ne |
> | Protokoly | Ne | Ne |
> | metricalerts | Ne | Ne |
> | metricbaselines | Ne | Ne |
> | metricbatch | Ne | Ne |
> | metricdefinitions | Ne | Ne |
> | metricnamespaces | Ne | Ne |
> | metriky | Ne | Ne |
> | migratealertrules | Ne | Ne |
> | migratetonewpricingmodel | Ne | Ne |
> | myworkbooks | Ne | Ne |
> | notificationgroups | Ne | Ne |
> | privatelinkscopes | Ne | Ne |
> | rollbacktolegacypricingmodel | Ne | Ne |
> | scheduledqueryrules | Ano | Ano |
> | topologie | Ne | Ne |
> | transactions | Ne | Ne |
> | vminsightsonboardingstatuses | Ne | Ne |
> | webové testy | Ano | Ano |
> | webtests/gettestresultfile | Ne | Ne |
> | sešity | Ano | Ano |
> | workbooktemplates | Ano | Ano |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | apptemplates | Ne | Ne |
> | iotapps | Ano | Ano |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zapisovací | Ano | Ano |

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!IMPORTANT]
> Trezory klíčů použité pro šifrování disku nejde přesunout do skupiny prostředků v rámci stejného předplatného nebo napříč předplatnými.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | deletedvaults | Ne | Ne |
> | hsmpools | Ne | Ne |
> | managedhsms | Ne | Ne |
> | trezory | Ano | Ano |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Ano | Ano |
> | registeredsubscriptions | Ne | Ne |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | Ne | Ne |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | labaccounts | Ne | Ne |
> | uživatelé | Ne | Ne |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | Ne | Ne |
> | integrationaccounts | Ano | Ano |
> | integrationserviceenvironments | Ano | Ne |
> | integrationserviceenvironments/Inspirujte | Ano | Ne |
> | isolatedenvironments | Ne | Ne |
> | Zpracovávaný | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | commitmentplans | Ne | Ne |
> | WebServices | Ano | Ne |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | Ne | Ne |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |
> | teamaccounts | Ne | Ne |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | configurationassignments | Ne | Ne |
> | maintenanceconfigurations | Ano | Ano |
> | aktualizovány | Ne | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | nebyly | Ne | Ne |
> | userassignedidentities | Ne | Ne |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | managednetworks | Ne | Ne |
> | managednetworks / managednetworkgroups | Ne | Ne |
> | managednetworks / managednetworkpeeringpolicies | Ne | Ne |
> | oznámení | Ne | Ne |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | Ne | Ne |
> | registrationassignments | Ne | Ne |
> | registrationdefinitions | Ne | Ne |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | getentities | Ne | Ne |
> | managementgroups | Ne | Ne |
> | managementgroups/nastavení | Ne | Ne |
> | resources | Ne | Ne |
> | starttenantbackfill | Ne | Ne |
> | tenantbackfillstatus | Ne | Ne |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |
> | účty/privateatlases | Ano | Ano |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Rozsah | Ne | Ne |
> | offertypes | Ne | Ne |
> | privategalleryitems | Ne | Ne |
> | privatestoreclient | Ne | Ne |
> | privatestores | Ne | Ne |
> | produktech | Ne | Ne |
> | zdrojů | Ne | Ne |
> | register | Ne | Ne |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicdevservices | Ne | Ne |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | o | Ne | Ne |
> | offertypes | Ne | Ne |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | MediaServices | Ano | Ano |
> | MediaServices/liveevents | Ano | Ano |
> | MediaServices/starají | Ano | Ano |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | appclusters | Ne | Ne |

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | Ne | Ne |
> | migrateprojects | Ne | Ne |
> | movecollections | Ne | Ne |
> | projekty | Ne | Ne |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | Ne | Ne |
> | objectunderstandingaccounts | Ne | Ne |
> | remoterenderingaccounts | Ano | Ano |
> | spatialanchorsaccounts | Ano | Ano |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | netappaccounts | Ne | Ne |
> | netappaccounts / capacitypools | Ne | Ne |
> | netappaccounts/capacitypools/svazky | Ne | Ne |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Viz [pokyny k přesunu sítě](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationgateways | Ne | Ne |
> | applicationgatewaywebapplicationfirewallpolicies | Ne | Ne |
> | applicationsecuritygroups | Ano | Ano |
> | azurefirewalls | Ne | Ne |
> | bastionhosts | Ne | Ne |
> | bgpservicecommunities | Ne | Ne |
> | připojení | Ano | Ano |
> | ddoscustompolicies | Ano | Ano |
> | ddosprotectionplans | Ne | Ne |
> | dnszones | Ano | Ano |
> | expressroutecircuits | Ne | Ne |
> | expressroutegateways | Ne | Ne |
> | expressrouteserviceproviders | Ne | Ne |
> | firewallpolicies | Ano | Ano |
> | frontdoors | Ne | Ne |
> | ipallocations | Ano | Ano |
> | ipgroups | Ano | Ano |
> | loadbalancers | Ano – základní SKU<br> Ano – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | localnetworkgateways | Ano | Ano |
> | natgateways | Ne | Ne |
> | networkexperimentprofiles | Ne | Ne |
> | networkintentpolicies | Ano | Ano |
> | networkinterfaces | Ano | Ano |
> | networkprofiles | Ne | Ne |
> | networksecuritygroups | Ano | Ano |
> | networkwatchers | Ne | Ne |
> | networkwatchers / connectionmonitors | Ano | Ne |
> | networkwatchers / flowlogs | Ano | Ne |
> | networkwatchers / pingmeshes | Ano | Ne |
> | p2svpngateways | Ne | Ne |
> | privatednszones | Ano | Ano |
> | privatednszones / virtualnetworklinks | Ano | Ano |
> | privatednszonesinternal | Ne | Ne |
> | privateendpointredirectmaps | Ne | Ne |
> | privateendpoints | Ne | Ne |
> | privatelinkservices | Ne | Ne |
> | publicipaddresses | Ano – základní SKU<br>Ano – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | publicipprefixes | Ano | Ano |
> | routefilters | Ne | Ne |
> | routetables | Ano | Ano |
> | securitypartnerproviders | Ano | Ano |
> | serviceendpointpolicies | Ano | Ano |
> | trafficmanagergeographichierarchies | Ne | Ne |
> | trafficmanagerprofiles | Ano | Ano |
> | trafficmanagerprofiles/Heat mapy | Ne | Ne |
> | trafficmanagerusermetricskeys | Ne | Ne |
> | virtualhubs | Ne | Ne |
> | virtualnetworkgateways | Ano | Ano |
> | virtualnetworks | Ano | Ano |
> | virtualnetworktaps | Ne | Ne |
> | virtualrouters | Ano | Ano |
> | virtualwans | Ne | Ne |
> | vpngateways (virtuální síť WAN) | Ne | Ne |
> | vpnserverconfigurations | Ne | Ne |
> | vpnsites (virtuální síť WAN) | Ne | Ne |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |
> | obory názvů/notificationhubs | Ano | Ano |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | osnamespaces | Ano | Ano |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hypervsites | Ne | Ne |
> | importsites | Ne | Ne |
> | serversites | Ne | Ne |
> | vmwaresites | Ne | Ne |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Pracovní prostory, které mají propojený účet Automation, nejde přesunout. Než začnete s přesunem, nezapomeňte zrušit propojení jakýchkoli účtů Automation.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ne | Ne |
> | deletedworkspaces | Ne | Ne |
> | linktargets | Ne | Ne |
> | storageinsightconfigs | Ne | Ne |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | managementassociations | Ne | Ne |
> | managementconfigurations | Ano | Ano |
> | Řešení | Ano | Ano |
> | zobrazení | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | legacypeerings | Ne | Ne |
> | peerasns | Ne | Ne |
> | peeringlocations | Ne | Ne |
> | partnerské vztahy | Ne | Ne |
> | peeringservicecountries | Ne | Ne |
> | peeringservicelocations | Ne | Ne |
> | peeringserviceproviders | Ne | Ne |
> | peeringservices | Ne | Ne |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | policyevents | Ne | Ne |
> | policystates | Ne | Ne |
> | policytrackedresources | Ne | Ne |
> | nápravy | Ne | Ne |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Konzoly nástroje | Ne | Ne |
> | řídicí panely | Ano | Ano |
> | usersettings | Ne | Ne |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | workspacecollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | schopností | Ano | Ano |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availableaccounts | Ne | Ne |
> | providerregistrations | Ne | Ne |
> | uvádění | Ne | Ne |

## <a name="microsoftquantum"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityresults | Ne | Ne |
> | trezory | Ano | Ano |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | Ne | Ne |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | odešle | Ano | Ano |
> | resourcechangedetails | Ne | Ne |
> | resourcechanges | Ne | Ne |
> | resources | Ne | Ne |
> | resourceshistory | Ne | Ne |
> | subscriptionsstatus | Ne | Ne |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | childresources | Ne | Ne |
> | emergingissues | Ne | Ne |
> | stránka events | Ne | Ne |
> | zprostředkovatele identity | Ne | Ne |
> | připomenutí | Ne | Ne |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | nasazení | Ne | Ne |
> | deploymentscripts | Ne | Ne |
> | deploymentscripts/protokoly | Ne | Ne |
> | odkazy | Ne | Ne |
> | dodavateli | Ne | Ne |
> | ResourceGroups | Ne | Ne |
> | resources | Ne | Ne |
> | odběru | Ne | Ne |
> | tags | Ne | Ne |
> | templatespecs | Ne | Ne |
> | templatespecs/verze | Ne | Ne |
> | tenantů | Ne | Ne |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ne |
> | saasresources | Ne | Ne |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!IMPORTANT]
> V jedné operaci nemůžete přesouvat několik prostředků vyhledávání v různých oblastech. Místo toho je přesuňte do samostatných operací.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | resourcehealthmetadata | Ne | Ne |
> | searchservices | Ano | Ano |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | Ne | Ne |
> | advancedthreatprotectionsettings | Ne | Ne |
> | výstrahy | Ne | Ne |
> | allowedconnections | Ne | Ne |
> | applicationwhitelistings | Ne | Ne |
> | assessmentmetadata | Ne | Ne |
> | hodnocení | Ne | Ne |
> | autodismissalertsrules | Ne | Ne |
> | automatizace | Ano | Ano |
> | autoprovisioningsettings | Ne | Ne |
> | complianceresults | Ne | Ne |
> | předpisů | Ne | Ne |
> | datacollectionagents | Ne | Ne |
> | devicesecuritygroups | Ne | Ne |
> | discoveredsecuritysolutions | Ne | Ne |
> | externalsecuritysolutions | Ne | Ne |
> | informationprotectionpolicies | Ne | Ne |
> | iotsecuritysolutions | Ano | Ano |
> | iotsecuritysolutions / analyticsmodels | Ne | Ne |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | Ne | Ne |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | Ne | Ne |
> | jitnetworkaccesspolicies | Ne | Ne |
> | Zásady | Ne | Ne |
> | ceny | Ne | Ne |
> | regulatorycompliancestandards | Ne | Ne |
> | regulatorycompliancestandards / regulatorycompliancecontrols | Ne | Ne |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | Ne | Ne |
> | securitycontacts | Ne | Ne |
> | securitysolutions | Ne | Ne |
> | securitysolutionsreferencedata | Ne | Ne |
> | securitystatuses | Ne | Ne |
> | securitystatusessummaries | Ne | Ne |
> | servervulnerabilityassessments | Ne | Ne |
> | nastavení | Ne | Ne |
> | podhodnocení | Ne | Ne |
> | úlohy | Ne | Ne |
> | topologie | Ne | Ne |
> | workspacesettings | Ne | Ne |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | agregace | Ne | Ne |
> | alertrules | Ne | Ne |
> | alertruletemplates | Ne | Ne |
> | automationrules | Ne | Ne |
> | záložky | Ne | Ne |
> | věcech | Ne | Ne |
> | dataconnects | Ne | Ne |
> | podnikům | Ne | Ne |
> | entityqueries | Ne | Ne |
> | Incidenty | Ne | Ne |
> | officeconsents | Ne | Ne |
> | nastavení | Ne | Ne |
> | threatintelligence | Ne | Ne |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | consoleservices | Ne | Ne |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | brány | Ne | Ne |
> | sortiment | Ne | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |
> | premiummessagingregions | Ne | Ne |
> | skladové | Ne | Ne |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ne | Ne |
> | existující | Ano | Ano |
> | containergroups | Ne | Ne |
> | containergroupsets | Ne | Ne |
> | edgeclusters | Ne | Ne |
> | managedclusters | Ne | Ne |
> | sítí | Ne | Ne |
> | secretstores | Ne | Ne |
> | volumes | Ne | Ne |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ano |
> | containergroups | Ne | Ne |
> | brány | Ano | Ano |
> | sítí | Ano | Ano |
> | záleží | Ano | Ano |
> | volumes | Ano | Ano |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | uvádění | Ne | Ne |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | SignalR | Ano | Ano |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | Ne | Ne |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | Ne | Ne |
> | aplikace | Ne | Ne |
> | jitrequests | Ne | Ne |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Databáze a server musí být ve stejné skupině prostředků. Když přesunete SQL Server, přesunou se také všechny jeho databáze. Toto chování platí pro databáze Azure SQL Database a Azure synapse Analytics.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | instancepools | Ne | Ne |
> | polohy | Ano | Ano |
> | managedinstances | Ne | Ne |
> | servery | Ano | Ano |
> | servery/databáze | Ano | Ano |
> | servery/databáze/backuplongtermretentionpolicies | Ano | Ano |
> | servery/elasticpools | Ano | Ano |
> | servery/jobaccounts | Ano | Ano |
> | servery/jobagents | Ano | Ano |
> | virtualclusters | Ano | Ano |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | sqlvirtualmachinegroups | Ano | Ano |
> | sqlvirtualmachines | Ano | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storageaccounts | Ano | Ano |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | mezipaměti | Ne | Ne |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ano | Ano |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ne | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ne | Ne |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | jednatel | Ne | Ne |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!IMPORTANT]
> Stream Analytics úlohy nelze přesunout, pokud je ve spuštěném stavu.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ne | Ne |
> | streamingjobs | Ano | Ano |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Environment | Ne | Ne |
> | instance | Ne | Ne |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | odběru | Ne | Ne |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | services | Ne | Ne |
> | supporttickets | Ne | Ne |

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | Ne | Ne |
> | pracovní prostory/bigdatapools | Ne | Ne |
> | pracovní prostory/sqlpools | Ne | Ne |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Environment | Ano | Ano |
> | prostředí/EventSources | Ano | Ano |
> | prostředí/referencedatasets | Ano | Ano |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Store | Ano | Ano |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | imagetemplates | Ne | Ne |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!IMPORTANT]
> Pokud chcete změnit předplatné služby Azure DevOps, přečtěte si téma [Změna předplatného Azure používaného pro fakturaci](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | account | Ne | Ne |
> | účet/rozšíření | Ne | Ne |
> | účet/projekt | Ne | Ne |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | arczones | Ne | Ne |
> | resourcepools | Ne | Ne |
> | servery vCenter | Ne | Ne |
> | virtualmachines | Ne | Ne |
> | virtualmachinetemplates | Ne | Ne |
> | virtualnetworks | Ne | Ne |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | Ne | Ne |
> | dedicatedcloudservices | Ne | Ne |
> | virtualmachines | Ne | Ne |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zařízení | Ne | Ne |
> | vnfs | Ne | Ne |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ne | Ne |
> | plánují | Ne | Ne |
> | registeredsubscriptions | Ne | Ne |

## <a name="microsoftweb"></a>Microsoft. Web

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availablestacks | Ne | Ne |
> | billingmeters | Ne | Ne |
> | certifikáty | Ne | Ano |
> | connectiongateways | Ano | Ano |
> | připojení | Ano | Ano |
> | customapis | Ano | Ano |
> | deletedsites | Ne | Ne |
> | deploymentlocations | Ne | Ne |
> | geografická umístění | Ne | Ne |
> | hostingenvironments | Ne | Ne |
> | kubeenvironments | Ano | Ano |
> | publishingusers | Ne | Ne |
> | doporučit | Ne | Ne |
> | resourcehealthmetadata | Ne | Ne |
> | moduly runtime | Ne | Ne |
> | serverových farem | Ano | Ano |
> | serverových farem/eventgridfilters | Ne | Ne |
> | místa | Ano | Ano |
> | lokality/premieraddons | Ano | Ano |
> | lokality/sloty | Ano | Ano |
> | sourcecontrols | Ne | Ne |
> | staticsites | Ne | Ne |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | Ne | Ne |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | deviceservices | Ne | Ne |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | úlohy | Ne | Ne |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konstrukční | Ne | Ne |
> | componentssummary | Ne | Ne |
> | monitorinstances | Ne | Ne |
> | monitorinstancessummary | Ne | Ne |
> | Monitor | Ne | Ne |

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

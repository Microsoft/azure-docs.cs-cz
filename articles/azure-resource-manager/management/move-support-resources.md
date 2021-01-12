---
title: Přesunout podporu operace podle typu prostředku
description: Zobrazuje seznam typů prostředků Azure, které se dají přesunout do nové skupiny prostředků nebo předplatného.
ms.topic: conceptual
ms.date: 01/11/2021
ms.openlocfilehash: fe8f6705fdce1a692dd8a969cfc4b3b21d2d2a11
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98072101"
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
> | domainservices | No | No |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | diagnosticsettings | No | No |
> | diagnosticsettingscategories | No | No |
> | privatelinkforazuread | Ano | Ano |
> | tenantů | Ano | Ano |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | supportproviders | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aadsupportcases | No | No |
> | addsservices | No | No |
> | technici | No | No |
> | anonymousapiusers | No | No |
> | konfigurace | No | No |
> | Protokoly | No | No |
> | reports | No | No |
> | servicehealthmetrics | No | No |
> | services | No | No |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konfiguračních | No | No |
> | generaterecommendations | No | No |
> | zprostředkovatele identity | No | No |
> | doporučit | No | No |
> | potlačení | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | actionrules | Ano | Ano |
> | výstrahy | No | No |
> | alertslist | No | No |
> | alertsmetadata | No | No |
> | alertssummary | No | No |
> | alertssummarylist | No | No |
> | smartdetectoralertrules | Ano | Ano |
> | smartgroups | No | No |

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
> | reportfeedback | No | No |
> | service | Ano | Ano |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | configurationstores | Ano | Ano |
> | configurationstores / eventgridfilters | No | No |

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
> | apiapps | No | No |
> | appidentities | No | No |
> | brány | No | No |

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | attestationproviders | Ano | Ano |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicadministrators | No | No |
> | dataaliasy | No | No |
> | denyassignments | No | No |
> | elevateaccess | No | No |
> | findorphanroleassignments | No | No |
> | počtu | No | No |
> | oprávnění | No | No |
> | policyassignments | No | No |
> | policydefinitions | No | No |
> | policysetdefinitions | No | No |
> | privatelinkassociations | No | No |
> | resourcemanagementprivatelinks | No | No |
> | RoleAssignments | No | No |
> | roleassignmentsusagemetrics | No | No |
> | roledefinitions | No | No |

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
> | b2ctenants | No | No |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | datacontrollery | No | No |
> | hybriddatamanagers | No | No |
> | postgresinstances | No | No |
> | sqlinstances | No | No |
> | sqlmanagedinstances | No | No |
> | sqlserverinstances | No | No |
> | sqlserverregistrations | Ano | Ano |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | cloudmanifestfiles | No | No |
> | rozpoznávání | Ano | Ano |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | batchaccounts | Ano | Ano |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | billingaccounts | No | No |
> | billingperiods | No | No |
> | billingpermissions | No | No |
> | billingproperty | No | No |
> | billingroleassignments | No | No |
> | billingroledefinitions | No | No |
> | oddělení | No | No |
> | enrollmentaccounts | No | No |
> | faktury | No | No |
> | Převede | No | No |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | mapapis | No | No |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | biztalk | No | No |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | blockchainmembers | No | No |
> | cordamembers | No | No |
> | sledovacích procesů | No | No |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | tokenservices | No | No |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | blueprintassignments | No | No |
> | podrobné plány | No | No |

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
> | redisenterprise | No | No |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | appliedreservations | No | No |
> | calculateexchange | No | No |
> | calculateprice | No | No |
> | calculatepurchaseprice | No | No |
> | spustí | No | No |
> | commercialreservationorders | No | No |
> | výměn | No | No |
> | reservationorders | No | No |
> | rezervace | No | No |
> | resources | No | No |
> | validatereservationorder | No | No |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | cdnwebapplicationfirewallmanagedrulesets | No | No |
> | cdnwebapplicationfirewallpolicies | Ano | Ano |
> | edgenodes | No | No |
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
> | možnosti | No | No |
> | domainnames | Ano | No |
> | quotas | No | No |
> | typ prostředků | No | No |
> | validatesubscriptionmoveavailability | No | No |
> | virtualmachines | Ano | Ano |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicinfrastructureresources | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | možnosti | No | No |
> | expressroutecrossconnections | No | No |
> | expressroutecrossconnections/partnerské vztahy | No | No |
> | gatewaysupporteddevices | No | No |
> | networksecuritygroups | No | No |
> | quotas | No | No |
> | reservedips | No | No |
> | virtualnetworks | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | disků | No | No |
> | images | No | No |
> | OSImage | No | No |
> | osplatformimages | No | No |
> | publicimages | No | No |
> | quotas | No | No |
> | storageaccounts | Ano | No |
> | vmimages | No | No |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Operace | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | Ano | Ano |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | ratecard | No | No |
> | usageaggregates | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Virtual Machines](./move-limitations/virtual-machines-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availabilitysets | Ano | Ano |
> | diskaccesses | No | No |
> | diskencryptionsets | No | No |
> | disků | Ano | Ano |
> | Galerie | No | No |
> | Galerie/image | No | No |
> | Galerie/image/verze | No | No |
> | hostgroups | No | No |
> | hostgroups/hostitelé | No | No |
> | images | Ano | Ano |
> | proximityplacementgroups | Ano | Ano |
> | restorepointcollections | No | No |
> | restorepointcollections / restorepoints | No | No |
> | sharedvmextensions | No | No |
> | sharedvmimages | No | No |
> | sharedvmimages/verze | No | No |
> | snímky | Ano | Ano |
> | sshpublickeys | No | No |
> | virtualmachines | Ano | Ano |
> | VirtualMachines/rozšíření | Ano | Ano |
> | virtualmachinescalesets | Ano | Ano |

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aggregatedcost | No | No |
> | Přesun | No | No |
> | projektů | No | No |
> | poplatky za | No | No |
> | costtags | No | No |
> | dobropis | No | No |
> | stránka events | No | No |
> | prognózy | No | No |
> | ŠARŽ | No | No |
> | tržišť | No | No |
> | pricesheets | No | No |
> | produktech | No | No |
> | reservationdetails | No | No |
> | reservationrecommendationdetails | No | No |
> | reservationrecommendations | No | No |
> | reservationsummaries | No | No |
> | reservationtransactions | No | No |
> | tags | No | No |
> | tenantů | No | No |
> | uvedenými | No | No |
> | usagedetails | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | containergroups | No | No |
> | serviceassociationlinks | No | No |

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
> | containerservices | No | No |
> | managedclusters | No | No |
> | openshiftmanagedclusters | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | No | No |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | výstrahy | No | No |
> | billingaccounts | No | No |
> | projektů | No | No |
> | cloudconnectors | No | No |
> | konektory | Ano | Ano |
> | oddělení | No | No |
> | použijí | No | No |
> | enrollmentaccounts | No | No |
> | vývozních | No | No |
> | externalbillingaccounts | No | No |
> | forecast | No | No |
> | query | No | No |
> | register | No | No |
> | reportconfigs | No | No |
> | reports | No | No |
> | nastavení | No | No |
> | showbackrules | No | No |
> | zobrazení | No | No |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Hubs | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | požádal | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | přidružení | No | No |
> | resourceproviders | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | úlohy | No | No |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availableskus | No | No |
> | databoxedgedevices | No | No |

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | spustí | Ano | Ano |
> | datacatalogs | No | No |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | connectionmanagers | No | No |

## <a name="microsoftdataexchange"></a>Microsoft. dataexchange

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zásilk | No | No |
> | plánují | No | No |

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
> | datalakeaccounts | No | No |

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
> | services | No | No |
> | služby a projekty | No | No |
> | otvory | No | No |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | backupvaults | No | No |

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
> | servergroups | No | No |
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
> | elasticpools | No | No |
> | elasticpools / iothubtenants | No | No |
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
> | labcenters | No | No |
> | Labs | Ano | No |
> | laboratoře/prostředí | Ano | Ano |
> | Labs/servicerunners | Ano | Ano |
> | Labs/VirtualMachines | Ano | No |
> | časových | Ano | Ano |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | digitaltwinsinstances | No | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | databaseaccountnames | No | No |
> | databaseaccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | doménu | Ano | Ano |
> | generatessorequest | No | No |
> | topleveldomains | No | No |
> | validatedomainregistrationinformation | No | No |

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
> | extensiontopics | No | No |
> | partnernamespaces | Ano | Ano |
> | partnerregistrations | No | No |
> | partnertopics | Ano | Ano |
> | systemtopics | Ano | Ano |
> | popisující | Ano | Ano |
> | topictypes | No | No |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |
> | obsažené | Ano | Ano |
> | skladové | No | No |

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | experimentworkspaces | No | No |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | featureproviders | No | No |
> | funkce | No | No |
> | dodavateli | No | No |
> | subscriptionfeatureregistrations | No | No |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | automanagedaccounts | No | No |
> | automanagedvmconfigurationprofiles | No | No |
> | guestconfigurationassignments | No | No |
> | Vybavení | No | No |
> | softwareupdateprofile | No | No |
> | softwareupdates | No | No |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hanainstances | No | No |
> | sapmonitors | No | No |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dedicatedhsms | No | No |

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
> | zařízení | No | No |
> | vnfs | No | No |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konstrukční | No | No |
> | networkscopes | No | No |

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
> | upozorněníprotokoluaktivit | No | No |
> | alertrules | Ano | Ano |
> | autoscalesettings | Ano | Ano |
> | standardní hodnoty | No | No |
> | konstrukční | Ano | Ano |
> | datacollectionrules | No | No |
> | diagnosticsettings | No | No |
> | diagnosticsettingscategories | No | No |
> | eventcategories | No | No |
> | EventType | No | No |
> | extendeddiagnosticsettings | No | No |
> | guestdiagnosticsettings | No | No |
> | listmigrationdate | No | No |
> | logdefinitions | No | No |
> | logprofiles | No | No |
> | Protokoly | No | No |
> | metricalerts | No | No |
> | metricbaselines | No | No |
> | metricbatch | No | No |
> | metricdefinitions | No | No |
> | metricnamespaces | No | No |
> | metriky | No | No |
> | migratealertrules | No | No |
> | migratetonewpricingmodel | No | No |
> | myworkbooks | No | No |
> | notificationgroups | No | No |
> | privatelinkscopes | No | No |
> | rollbacktolegacypricingmodel | No | No |
> | scheduledqueryrules | Ano | Ano |
> | topologie | No | No |
> | transactions | No | No |
> | vminsightsonboardingstatuses | No | No |
> | webové testy | Ano | Ano |
> | webtests/gettestresultfile | No | No |
> | sešity | Ano | Ano |
> | workbooktemplates | Ano | Ano |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | apptemplates | No | No |
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
> | deletedvaults | No | No |
> | hsmpools | No | No |
> | managedhsms | No | No |
> | trezory | Ano | Ano |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | connectedclusters | Ano | Ano |
> | registeredsubscriptions | No | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | sourcecontrolconfigurations | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | Ano | Ano |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | labaccounts | No | No |
> | uživatelé | No | No |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hostingenvironments | No | No |
> | integrationaccounts | Ano | Ano |
> | integrationserviceenvironments | Ano | No |
> | integrationserviceenvironments/Inspirujte | Ano | No |
> | isolatedenvironments | No | No |
> | Zpracovávaný | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | commitmentplans | No | No |
> | WebServices | Ano | No |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | operationalizationclusters | No | No |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |
> | teamaccounts | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | No | No |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | configurationassignments | No | No |
> | maintenanceconfigurations | Ano | Ano |
> | aktualizovány | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | nebyly | No | No |
> | userassignedidentities | No | No |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | managednetworks | No | No |
> | managednetworks / managednetworkgroups | No | No |
> | managednetworks / managednetworkpeeringpolicies | No | No |
> | oznámení | No | No |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | marketplaceregistrationdefinitions | No | No |
> | registrationassignments | No | No |
> | registrationdefinitions | No | No |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | getentities | No | No |
> | managementgroups | No | No |
> | managementgroups/nastavení | No | No |
> | resources | No | No |
> | starttenantbackfill | No | No |
> | tenantbackfillstatus | No | No |

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
> | Rozsah | No | No |
> | offertypes | No | No |
> | privategalleryitems | No | No |
> | privatestoreclient | No | No |
> | privatestores | No | No |
> | produktech | No | No |
> | zdrojů | No | No |
> | register | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | classicdevservices | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | o | No | No |
> | offertypes | No | No |

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
> | appclusters | No | No |

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | assessmentprojects | No | No |
> | migrateprojects | No | No |
> | movecollections | No | No |
> | projekty | No | No |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | holographicsbroadcastaccounts | No | No |
> | objectunderstandingaccounts | No | No |
> | remoterenderingaccounts | Ano | Ano |
> | spatialanchorsaccounts | Ano | Ano |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | netappaccounts | No | No |
> | netappaccounts / capacitypools | No | No |
> | netappaccounts/capacitypools/svazky | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Viz [pokyny k přesunu sítě](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationgateways | No | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | No |
> | applicationsecuritygroups | Ano | Ano |
> | azurefirewalls | No | No |
> | bastionhosts | No | No |
> | bgpservicecommunities | No | No |
> | připojení | Ano | Ano |
> | ddoscustompolicies | Ano | Ano |
> | ddosprotectionplans | No | No |
> | dnszones | Ano | Ano |
> | expressroutecircuits | No | No |
> | expressroutegateways | No | No |
> | expressrouteserviceproviders | No | No |
> | firewallpolicies | Ano | Ano |
> | frontdoors | No | No |
> | ipallocations | Ano | Ano |
> | ipgroups | Ano | Ano |
> | loadbalancers | Ano – základní SKU<br> Ano – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | localnetworkgateways | Ano | Ano |
> | natgateways | No | No |
> | networkexperimentprofiles | No | No |
> | networkintentpolicies | Ano | Ano |
> | networkinterfaces | Ano | Ano |
> | networkprofiles | No | No |
> | networksecuritygroups | Ano | Ano |
> | networkwatchers | No | No |
> | networkwatchers / connectionmonitors | Ano | No |
> | networkwatchers / flowlogs | Ano | No |
> | networkwatchers / pingmeshes | Ano | No |
> | p2svpngateways | No | No |
> | privatednszones | Ano | Ano |
> | privatednszones / virtualnetworklinks | Ano | Ano |
> | privatednszonesinternal | No | No |
> | privateendpointredirectmaps | No | No |
> | privateendpoints | No | No |
> | privatelinkservices | No | No |
> | publicipaddresses | Ano – základní SKU<br>Ano – standardní SKU | Ano – základní SKU<br>No – standardní SKU |
> | publicipprefixes | Ano | Ano |
> | routefilters | No | No |
> | routetables | Ano | Ano |
> | securitypartnerproviders | Ano | Ano |
> | serviceendpointpolicies | Ano | Ano |
> | trafficmanagergeographichierarchies | No | No |
> | trafficmanagerprofiles | Ano | Ano |
> | trafficmanagerprofiles/Heat mapy | No | No |
> | trafficmanagerusermetricskeys | No | No |
> | virtualhubs | No | No |
> | virtualnetworkgateways | Ano | Ano |
> | virtualnetworks | Ano | Ano |
> | virtualnetworktaps | No | No |
> | virtualrouters | Ano | Ano |
> | virtualwans | No | No |
> | vpngateways (virtuální síť WAN) | No | No |
> | vpnserverconfigurations | No | No |
> | vpnsites (virtuální síť WAN) | No | No |

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
> | hypervsites | No | No |
> | importsites | No | No |
> | serversites | No | No |
> | vmwaresites | No | No |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Pracovní prostory, které mají propojený účet Automation, nejde přesunout. Než začnete s přesunem, nezapomeňte zrušit propojení jakýchkoli účtů Automation.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | No | No |
> | deletedworkspaces | No | No |
> | linktargets | No | No |
> | storageinsightconfigs | No | No |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | managementassociations | No | No |
> | managementconfigurations | Ano | Ano |
> | Řešení | Ano | Ano |
> | zobrazení | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | legacypeerings | No | No |
> | peerasns | No | No |
> | peeringlocations | No | No |
> | partnerské vztahy | No | No |
> | peeringservicecountries | No | No |
> | peeringservicelocations | No | No |
> | peeringserviceproviders | No | No |
> | peeringservices | No | No |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | policyevents | No | No |
> | policystates | No | No |
> | policytrackedresources | No | No |
> | nápravy | No | No |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Konzoly nástroje | No | No |
> | řídicí panely | Ano | Ano |
> | usersettings | No | No |

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
> | accounts | No | No |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availableaccounts | No | No |
> | providerregistrations | No | No |
> | uvádění | No | No |

## <a name="microsoftquantum"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | replicationeligibilityresults | No | No |
> | trezory | Ano | Ano |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | openshiftclusters | No | No |

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
> | resourcechangedetails | No | No |
> | resourcechanges | No | No |
> | resources | No | No |
> | resourceshistory | No | No |
> | subscriptionsstatus | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | childresources | No | No |
> | emergingissues | No | No |
> | stránka events | No | No |
> | zprostředkovatele identity | No | No |
> | připomenutí | No | No |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | nasazení | No | No |
> | deploymentscripts | No | No |
> | deploymentscripts/protokoly | No | No |
> | odkazy | No | No |
> | dodavateli | No | No |
> | ResourceGroups | No | No |
> | resources | No | No |
> | odběru | No | No |
> | tags | No | No |
> | templatespecs | No | No |
> | templatespecs/verze | No | No |
> | tenantů | No | No |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | No |
> | saasresources | No | No |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!IMPORTANT]
> V jedné operaci nemůžete přesouvat několik prostředků vyhledávání v různých oblastech. Místo toho je přesuňte do samostatných operací.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | resourcehealthmetadata | No | No |
> | searchservices | Ano | Ano |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | adaptivenetworkhardenings | No | No |
> | advancedthreatprotectionsettings | No | No |
> | výstrahy | No | No |
> | allowedconnections | No | No |
> | applicationwhitelistings | No | No |
> | assessmentmetadata | No | No |
> | hodnocení | No | No |
> | autodismissalertsrules | No | No |
> | automatizace | Ano | Ano |
> | autoprovisioningsettings | No | No |
> | complianceresults | No | No |
> | předpisů | No | No |
> | datacollectionagents | No | No |
> | devicesecuritygroups | No | No |
> | discoveredsecuritysolutions | No | No |
> | externalsecuritysolutions | No | No |
> | informationprotectionpolicies | No | No |
> | iotsecuritysolutions | Ano | Ano |
> | iotsecuritysolutions / analyticsmodels | No | No |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | No | No |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | No | No |
> | jitnetworkaccesspolicies | No | No |
> | Zásady | No | No |
> | ceny | No | No |
> | regulatorycompliancestandards | No | No |
> | regulatorycompliancestandards / regulatorycompliancecontrols | No | No |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | No | No |
> | securitycontacts | No | No |
> | securitysolutions | No | No |
> | securitysolutionsreferencedata | No | No |
> | securitystatuses | No | No |
> | securitystatusessummaries | No | No |
> | servervulnerabilityassessments | No | No |
> | nastavení | No | No |
> | podhodnocení | No | No |
> | úlohy | No | No |
> | topologie | No | No |
> | workspacesettings | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | agregace | No | No |
> | alertrules | No | No |
> | alertruletemplates | No | No |
> | automationrules | No | No |
> | záložky | No | No |
> | věcech | No | No |
> | dataconnects | No | No |
> | podnikům | No | No |
> | entityqueries | No | No |
> | Incidenty | No | No |
> | officeconsents | No | No |
> | nastavení | No | No |
> | threatintelligence | No | No |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | consoleservices | No | No |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | brány | No | No |
> | sortiment | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | obsažené | Ano | Ano |
> | premiummessagingregions | No | No |
> | skladové | No | No |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | No | No |
> | existující | Ano | Ano |
> | containergroups | No | No |
> | containergroupsets | No | No |
> | edgeclusters | No | No |
> | managedclusters | No | No |
> | sítí | No | No |
> | secretstores | No | No |
> | volumes | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | aplikace | Ano | Ano |
> | containergroups | No | No |
> | brány | Ano | Ano |
> | sítí | Ano | Ano |
> | záleží | Ano | Ano |
> | volumes | Ano | Ano |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | uvádění | No | No |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | SignalR | Ano | Ano |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | hybridusebenefits | No | No |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | applicationdefinitions | No | No |
> | aplikace | No | No |
> | jitrequests | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Databáze a server musí být ve stejné skupině prostředků. Když přesunete SQL Server, přesunou se také všechny jeho databáze. Toto chování platí pro databáze Azure SQL Database a Azure synapse Analytics.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | instancepools | No | No |
> | polohy | Ano | Ano |
> | managedinstances | No | No |
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
> | mezipaměti | No | No |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | Ano | Ano |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | storagesyncservices | No | No |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | jednatel | No | No |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!IMPORTANT]
> Stream Analytics úlohy nelze přesunout, pokud je ve spuštěném stavu.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | existující | No | No |
> | streamingjobs | Ano | Ano |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | Environment | No | No |
> | instance | No | No |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | odběru | No | No |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | services | No | No |
> | supporttickets | No | No |

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | pracovní prostory | No | No |
> | pracovní prostory/bigdatapools | No | No |
> | pracovní prostory/sqlpools | No | No |

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
> | imagetemplates | No | No |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!IMPORTANT]
> Pokud chcete změnit předplatné služby Azure DevOps, přečtěte si téma [Změna předplatného Azure používaného pro fakturaci](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | account | No | No |
> | účet/rozšíření | No | No |
> | účet/projekt | No | No |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | arczones | No | No |
> | resourcepools | No | No |
> | servery vCenter | No | No |
> | virtualmachines | No | No |
> | virtualmachinetemplates | No | No |
> | virtualnetworks | No | No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | dedicatedcloudnodes | No | No |
> | dedicatedcloudservices | No | No |
> | virtualmachines | No | No |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | zařízení | No | No |
> | vnfs | No | No |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | accounts | No | No |
> | plánují | No | No |
> | registeredsubscriptions | No | No |

## <a name="microsoftweb"></a>Microsoft. Web

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | availablestacks | No | No |
> | billingmeters | No | No |
> | certifikáty | No | Ano |
> | connectiongateways | Ano | Ano |
> | připojení | Ano | Ano |
> | customapis | Ano | Ano |
> | deletedsites | No | No |
> | deploymentlocations | No | No |
> | geografická umístění | No | No |
> | hostingenvironments | No | No |
> | kubeenvironments | Ano | Ano |
> | publishingusers | No | No |
> | doporučit | No | No |
> | resourcehealthmetadata | No | No |
> | moduly runtime | No | No |
> | serverových farem | Ano | Ano |
> | serverových farem/eventgridfilters | No | No |
> | místa | Ano | Ano |
> | lokality/premieraddons | Ano | Ano |
> | lokality/sloty | Ano | Ano |
> | sourcecontrols | No | No |
> | staticsites | No | No |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | multipleactivationkeys | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | deviceservices | No | No |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | úlohy | No | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné |
> | ------------- | ----------- | ---------- |
> | konstrukční | No | No |
> | componentssummary | No | No |
> | monitorinstances | No | No |
> | monitorinstancessummary | No | No |
> | Monitor | No | No |

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

## <a name="next-steps"></a>Další kroky

Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

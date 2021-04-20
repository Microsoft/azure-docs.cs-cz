---
title: Přesunout podporu operace podle typu prostředku
description: Zobrazuje seznam typů prostředků Azure, které se dají přesunout do nové skupiny prostředků, předplatného nebo oblasti.
ms.topic: conceptual
ms.date: 04/16/2021
ms.openlocfilehash: a56a9e6f04aa800e16bbab0190ce7b41d87da590
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107740083"
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
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | domainservices | No | No |  No |

## <a name="microsoftaadiam"></a>Microsoft. aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | diagnosticsettings | No | No | No |
> | diagnosticsettingscategories | No | No | No |
> | privatelinkforazuread | Yes | Yes | No |
> | tenantů | Yes | Yes | No |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | supportproviders | No | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | aadsupportcases | No | No | No |
> | addsservices | No | No | No |
> | technici | No | No | No |
> | anonymousapiusers | No | No | No |
> | konfigurace | No | No | No |
> | Protokoly | No | No | No |
> | sestavy | No | No | No |
> | servicehealthmetrics | No | No | No |
> | services | No | No | No |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | konfiguračních | No | No | No |
> | generaterecommendations | No | No | No |
> | zprostředkovatele identity | No | No | No |
> | doporučit | No | No | No |
> | potlačení | No | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | actionrules | Yes | Yes | No |
> | výstrahy | No | No | No |
> | alertslist | No | No | No |
> | alertsmetadata | No | No | No |
> | alertssummary | No | No | No |
> | alertssummarylist | No | No | No |
> | smartdetectoralertrules | Yes | Yes | No |
> | smartgroups | No | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | servery | Yes | Yes | No |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!IMPORTANT]
> Službu API Management, která je nastavená na SKU spotřeby, se nedá přesunout.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | reportfeedback | No | No | No |
> | service | Yes | Yes | Ano (pomocí šablony) <br/><br/> [Přesunutí API Management napříč oblastmi](../../api-management/api-management-howto-migrate.md). |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationstores | Yes | Yes | No |
> | configurationstores / eventgridfilters | No | No | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | návratu | Yes | Yes | No |

## <a name="microsoftappservice"></a>Microsoft. AppService

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | apiapps | No | No | Ano (pomocí šablony)<br/><br/> [Přesunutí aplikace App Service do jiné oblasti](../../app-service/manage-move-across-regions.md) |
> | appidentities | No | No | No |
> | brány | No | No | No |

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | attestationproviders | Yes | Yes | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | classicadministrators | No | No | No |
> | dataaliasy | No | No | No |
> | denyassignments | No | No | No |
> | elevateaccess | No | No | No |
> | findorphanroleassignments | No | No | No |
> | počtu | No | No | No |
> | oprávnění | No | No | No |
> | policyassignments | No | No | No |
> | policydefinitions | No | No | No |
> | policysetdefinitions | No | No | No |
> | privatelinkassociations | No | No | No |
> | resourcemanagementprivatelinks | No | No | No |
> | RoleAssignments | No | No | No |
> | roleassignmentsusagemetrics | No | No | No |
> | roledefinitions | No | No | No |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!IMPORTANT]
> Runbooky musí existovat ve stejné skupině prostředků jako účet Automation.
>
> Informace najdete v tématu [přesunutí účtu Azure Automation do jiného předplatného](../../automation/how-to/move-account.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | automationaccounts | Yes | Yes | Ano (pomocí šablony) <br/><br/> [Použití geografické replikace](../../automation/automation-managing-data.md#geo-replication-in-azure-automation) |
> | automationaccounts/konfigurace | Yes | Yes | No |
> | automationaccounts/Runbooky | Yes | Yes | No |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | privateclouds | Yes | Yes | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | b2cdirectories | Yes | Yes | No |
> | b2ctenants | No | No | No |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | datacontrollery | No | No | No |
> | hybriddatamanagers | No | No | No |
> | postgresinstances | No | No | No |
> | sqlinstances | No | No | No |
> | sqlmanagedinstances | No | No | No |
> | sqlserverinstances | No | No | No |
> | sqlserverregistrations | Yes | Yes | No |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | cloudmanifestfiles | No | No | No |
> | rozpoznávání | Yes | Yes | No |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | existující | No | No | No |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | batchaccounts | Yes | Yes | Účty Batch nejde přesunout přímo z jedné oblasti do druhé, ale šablonu můžete použít k exportu šablony, její úpravě a nasazení šablony do nové oblasti. <br/><br/> Další informace o [přesunu účtu Batch napříč oblastmi](../../batch/best-practices.md#moving-batch-accounts-across-regions) |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | billingaccounts | No | No | No |
> | billingperiods | No | No | No |
> | billingpermissions | No | No | No |
> | billingproperty | No | No | No |
> | billingroleassignments | No | No | No |
> | billingroledefinitions | No | No | No |
> | oddělení | No | No | No |
> | enrollmentaccounts | No | No | No |
> | faktury | No | No | No |
> | Převede | No | No | No |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | mapapis | No | No | No |

## <a name="microsoftbiztalkservices"></a>Microsoft. BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | biztalk | No | No | No |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | blockchainmembers | No | No | No <br/><br/> Síť blockchain nemůže mít uzly v různých oblastech. |
> | cordamembers | No | No | No |
> | sledovacích procesů | No | No | No |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | tokenservices | No | No | No |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | blueprintassignments | No | No | No |
> | podrobné plány | No | No | No |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | botservices | Yes | Yes | No |

## <a name="microsoftcache"></a>Microsoft. cache

> [!IMPORTANT]
> Pokud je instance Azure cache for Redis nakonfigurovaná pomocí virtuální sítě, nedá se tato instance přesunout do jiného předplatného. Viz [omezení přesunu sítě](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | Redis | Yes | Yes | No |
> | redisenterprise | No | No | No |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | appliedreservations | No | No | No |
> | calculateexchange | No | No | No |
> | calculateprice | No | No | No |
> | calculatepurchaseprice | No | No | No |
> | spustí | No | No | No |
> | commercialreservationorders | No | No | No |
> | výměn | No | No | No |
> | reservationorders | No | No | No |
> | rezervace | No | No | No |
> | resources | No | No | No |
> | validatereservationorder | No | No | No |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | cdnwebapplicationfirewallmanagedrulesets | No | No | No |
> | cdnwebapplicationfirewallpolicies | Yes | Yes | No |
> | edgenodes | No | No | No |
> | uživatelů | Yes | Yes | No |
> | profily/koncové body | Yes | Yes | No |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | certificateorders | Yes | Yes | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | možnosti | No | No | No |
> | domainnames | Yes | No | No |
> | quotas | No | No | No |
> | typ prostředků | No | No | No |
> | validatesubscriptionmoveavailability | No | No | No |
> | virtualmachines | Yes | Yes | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | classicinfrastructureresources | No | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | možnosti | No | No | No |
> | expressroutecrossconnections | No | No | No |
> | expressroutecrossconnections/partnerské vztahy | No | No | No |
> | gatewaysupporteddevices | No | No | No |
> | networksecuritygroups | No | No | No |
> | quotas | No | No | No |
> | reservedips | No | No | No |
> | virtualnetworks | No | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | disků | No | No | No |
> | images | No | No | No |
> | OSImage | No | No | No |
> | osplatformimages | No | No | No |
> | publicimages | No | No | No |
> | quotas | No | No | No |
> | storageaccounts | Yes | No | Yes |
> | vmimages | No | No | No |

## <a name="microsoftclassicsubscription"></a>Microsoft. ClassicSubscription

> [!IMPORTANT]
> Viz [pokyny k přesunu klasického nasazení](./move-limitations/classic-model-move-limitations.md). Klasické prostředky nasazení se dají přesouvat mezi předplatnými s operací specifickou pro tento scénář.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | Operace | No | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | No |
> | Cognitive Search | **uložené** | **uložené** | Podporováno v ručních krocích.<br/><br/> Přečtěte si o [přesunu služby Azure kognitivní hledání do jiné oblasti](../../search/search-howto-move-across-regions.md) . |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | ratecard | No | No | No |
> | usageaggregates | No | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Virtual Machines](./move-limitations/virtual-machines-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | availabilitysets | Yes | Yes |  Yes <br/><br/> K přesunutí skupin dostupnosti použijte [prostředek Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | diskaccesses | No | No | No |
> | diskencryptionsets | No | No | No |
> | disků | Yes | Yes | Yes <br/><br/> K přesunutí virtuálních počítačů Azure a souvisejících disků použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | Galerie | No | No | No |
> | Galerie/image | No | No | No |
> | Galerie/image/verze | No | No | No |
> | hostgroups | No | No | No |
> | hostgroups/hostitelé | No | No | No |
> | images | Yes | Yes | No |
> | proximityplacementgroups | Yes | Yes | No |
> | restorepointcollections | No | No | No |
> | restorepointcollections / restorepoints | No | No | No |
> | sharedvmextensions | No | No | No |
> | sharedvmimages | No | No | No |
> | sharedvmimages/verze | No | No | No |
> | snímky | Yes | Yes | No |
> | sshpublickeys | No | No | No |
> | virtualmachines | Yes | Yes | Yes <br/><br/> K přesunu virtuálních počítačů Azure použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | VirtualMachines/rozšíření | Yes | Yes | No |
> | virtualmachinescalesets | Yes | Yes | No |

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | aggregatedcost | No | No | No |
> | Přesun | No | No | No |
> | projektů | No | No | No |
> | poplatky za | No | No | No |
> | costtags | No | No | No |
> | dobropis | No | No | No |
> | stránka events | No | No | No |
> | prognózy | No | No | No |
> | ŠARŽ | No | No | No |
> | tržišť | No | No | No |
> | pricesheets | No | No | No |
> | produktech | No | No | No |
> | reservationdetails | No | No | No |
> | reservationrecommendationdetails | No | No | No |
> | reservationrecommendations | No | No | No |
> | reservationsummaries | No | No | No |
> | reservationtransactions | No | No | No |
> | tags | No | No | No |
> | tenantů | No | No | No |
> | uvedenými | No | No | No |
> | usagedetails | No | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | containergroups | No | No | No |
> | serviceassociationlinks | No | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | registr | Yes | Yes | No |
> | Registry/agentpools | Yes | Yes | No |
> | Registry/BuildTasks | Yes | Yes | No |
> | Registry/replikace | Yes | Yes | No |
> | Registry/úlohy | Yes | Yes | No |
> | Registry a Webhooky | Yes | Yes | No |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | containerservices | No | No | No |
> | managedclusters | No | No | No |
> | openshiftmanagedclusters | No | No | No |

## <a name="microsoftcontentmoderator"></a>Microsoft. ContentModerator

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | aplikace | No | No | No |

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | výstrahy | No | No | No |
> | billingaccounts | No | No | No |
> | projektů | No | No | No |
> | cloudconnectors | No | No | No |
> | konektory | Yes | Yes | No |
> | oddělení | No | No | No |
> | použijí | No | No | No |
> | enrollmentaccounts | No | No | No |
> | vývozních | No | No | No |
> | externalbillingaccounts | No | No | No |
> | forecast | No | No | No |
> | query | No | No | No |
> | register | No | No | No |
> | reportconfigs | No | No | No |
> | sestavy | No | No | No |
> | nastavení | No | No | No |
> | showbackrules | No | No | No |
> | zobrazení | No | No | No |

## <a name="microsoftcustomerinsights"></a>Microsoft. CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | Hubs | No | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | požádal | No | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | přidružení | No | No | No |
> | resourceproviders | Yes | Yes | No |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | úlohy | No | No | No |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | availableskus | No | No | No |
> | databoxedgedevices | No | No | No |

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | pracovní prostory | No | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | spustí | Yes | Yes | No |
> | datacatalogs | No | No | No |

## <a name="microsoftdataconnect"></a>Microsoft. DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | connectionmanagers | No | No | No |

## <a name="microsoftdataexchange"></a>Microsoft. dataexchange

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | zásilk | No | No | No |
> | plánují | No | No | No |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | objekty DataFactory | Yes | Yes | No |
> | továrny | Yes | Yes | No |

## <a name="microsoftdatalake"></a>Microsoft. datalake

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | datalakeaccounts | No | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | No |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | No |

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | services | No | No | No |
> | služby a projekty | No | No | No |
> | otvory | No | No | No |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ---------- |
> | backupvaults | No | No | No |

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | No |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | servery | Yes | Yes | K přesunutí stávajícího serveru můžete použít repliku čtení mezi oblastmi. [Další informace](../../postgresql/howto-move-regions-portal.md).<br/><br/> Pokud je služba zřízená s geograficky redundantním úložištěm zálohování, můžete k obnovení v jiných oblastech použít geografické obnovení. [Další informace](../../mariadb/concepts-business-continuity.md#recover-from-an-azure-regional-data-center-outage).

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | No | No | No |
> | servery | Yes | Yes | K přesunutí stávajícího serveru můžete použít repliku čtení mezi oblastmi. [Další informace](../../mysql/howto-move-regions-portal.md).

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | flexibleServers | No | No | No |
> | servergroups | No | No | No |
> | servery | Yes | Yes | K přesunutí stávajícího serveru můžete použít repliku čtení mezi oblastmi. [Další informace](../../postgresql/howto-move-regions-portal.md).
> | serversv2 | Yes | Yes | No |
> | singleservers | Yes | Yes | No |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | artifactsources | Yes | Yes | No |
> | uvádění | Yes | Yes | No |
> | servicetopologies | Yes | Yes | No |
> | servicetopologies/služby | Yes | Yes | No |
> | servicetopologies/služby/serviceunits | Yes | Yes | No |
> | kroky | Yes | Yes | No |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgroups | Yes | Yes | No |
> | hostpools | Yes | Yes | No |
> | pracovní prostory | Yes | Yes | No |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | elasticpools | No | No | No. Prostředek není vystavený. |
> | elasticpools / iothubtenants | No | No | No. Prostředek není vystavený. |
> | iothubs | Yes | Ano | Ano. [Další informace](../../iot-hub/iot-hub-how-to-clone.md) |
> | provisioningservices | Yes | Yes | No |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | kanály | Yes | Yes | No |
> | kontrolery | **uložené** | **uložené** | No |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | kontrolery | Yes | Yes | No |
> | Cluster AKS | **uložené** | **uložené** | No<br/><br/> [Přečtěte si další informace](../../dev-spaces/faq.md#can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region) o přesunu do jiné oblasti.

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | labcenters | No | No | No |
> | Labs | Yes | No | No |
> | laboratoře/prostředí | Yes | Yes | No |
> | Labs/servicerunners | Yes | Yes | No |
> | Labs/VirtualMachines | Yes | No | No |
> | časových | Yes | Yes | No |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | digitaltwinsinstances | No | No | Ano, opětovným vytvořením prostředků v nové oblasti. [Další informace](../../digital-twins/how-to-move-regions.md) |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | databaseaccountnames | No | No | No |
> | databaseaccounts | Yes | Yes | No |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | doménu | Yes | Yes | No |
> | generatessorequest | No | No | No |
> | topleveldomains | No | No | No |
> | validatedomainregistrationinformation | No | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Yes | Yes | No |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | doménu | Yes | Yes | No |
> | eventsubscriptions | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. | No – nejde přesunout nezávisle, ale automaticky se přesune s předplacený prostředkem. | No |
> | extensiontopics | No | No | No |
> | partnernamespaces | Yes | Yes | No |
> | partnerregistrations | No | No | No |
> | partnertopics | Yes | Yes | No |
> | systemtopics | Yes | Yes | No |
> | popisující | Yes | Yes | No |
> | topictypes | No | No | No |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | existující | Yes | Yes | No |
> | obsažené | Yes | Yes | Ano (se šablonou)<br/><br/> [Přesunutí oboru názvů centra událostí do jiné oblasti](../../event-hubs/move-across-regions.md) |
> | skladové | No | No | No |

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | experimentworkspaces | No | No | No |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | obsažené | Yes | Yes | No |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | featureproviders | No | No | No |
> | funkce | No | No | No |
> | dodavateli | No | No | No |
> | subscriptionfeatureregistrations | No | No | No |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | automanagedaccounts | No | No | No |
> | automanagedvmconfigurationprofiles | No | No | No |
> | guestconfigurationassignments | No | No | No |
> | Vybavení | No | No | No |
> | softwareupdateprofile | No | No | No |
> | softwareupdates | No | No | No |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | hanainstances | No | No | No |
> | sapmonitors | No | No | No |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedhsms | No | No | No |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!IMPORTANT]
> Clustery HDInsight můžete přesunout do nového předplatného nebo skupiny prostředků. Nemůžete se ale pohybovat mezi předplatnými síťových prostředků propojených s clusterem HDInsight (například virtuální síť, síťová karta nebo nástroj pro vyrovnávání zatížení). Kromě toho nemůžete přesunout do nové skupiny prostředků síťovou kartu, která je připojená k virtuálnímu počítači pro daný cluster.
>
> Když přesunete cluster HDInsight do nového předplatného, napřed přesuňte další prostředky (třeba účet úložiště). Pak můžete cluster HDInsight přesunout sám o sobě.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | existující | Yes | Yes | No |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | services | Yes | Yes | No |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | virtuální | Yes | Yes | No |
> | počítače/rozšíření | Yes | Yes | No |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | datamanagery | Yes | Yes | No |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | zařízení | No | No | No |
> | vnfs | No | No | No |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | konstrukční | No | No | No |
> | networkscopes | No | No | No |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | úlohy | Yes | Yes | No |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | **uložené** | **uložené** | No. [Další informace](../../azure-monitor/faq.md#how-do-i-move-an-application-insights-resource-to-a-new-region). |
> | actiongroups | Yes | Yes | No |
> | upozorněníprotokoluaktivit | No | No | No |
> | alertrules | Yes | Yes | No |
> | autoscalesettings | Yes | Yes | No |
> | standardní hodnoty | No | No | No |
> | konstrukční | Yes | Yes | No |
> | datacollectionrules | No | No | No |
> | diagnosticsettings | No | No | No |
> | diagnosticsettingscategories | No | No | No |
> | eventcategories | No | No | No |
> | EventType | No | No | No |
> | extendeddiagnosticsettings | No | No | No |
> | guestdiagnosticsettings | No | No | No |
> | listmigrationdate | No | No | No |
> | logdefinitions | No | No | No |
> | logprofiles | No | No | No |
> | Protokoly | No | No | No |
> | metricalerts | No | No | No |
> | metricbaselines | No | No | No |
> | metricbatch | No | No | No |
> | metricdefinitions | No | No | No |
> | metricnamespaces | No | No | No |
> | metriky | No | No | No |
> | migratealertrules | No | No | No |
> | migratetonewpricingmodel | No | No | No |
> | myworkbooks | No | No | No |
> | notificationgroups | No | No | No |
> | privatelinkscopes | No | No | No |
> | rollbacktolegacypricingmodel | No | No | No |
> | scheduledqueryrules | Yes | Yes | No |
> | topologie | No | No | No |
> | transactions | No | No | No |
> | vminsightsonboardingstatuses | No | No | No |
> | webové testy | Yes | Yes | No |
> | webtests/gettestresultfile | No | No | No |
> | sešity | Yes | Yes | No |
> | workbooktemplates | Yes | Yes | No |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | apptemplates | No | No | No |
> | iotapps | Yes | Yes | No |

## <a name="microsoftiothub"></a>Microsoft.IoTHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | iothub | **uložené** | **uložené** | Ano (klonovat centrum) <br/><br/> [Klonování služby IoT Hub do jiné oblasti](../../iot-hub/iot-hub-how-to-clone.md) |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | zapisovací | Yes | Yes | No |

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!IMPORTANT]
> Trezory klíčů použité pro šifrování disku nejde přesunout do skupiny prostředků v rámci stejného předplatného nebo napříč předplatnými.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | deletedvaults | No | No | No |
> | hsmpools | No | No | No |
> | managedhsms | No | No | No |
> | trezory | Yes | Yes | No |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | connectedclusters | Yes | Yes | No |
> | registeredsubscriptions | No | No | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | sourcecontrolconfigurations | No | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | existující | Yes | Yes | No |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | labaccounts | No | No | No |
> | uživatelé | No | No | No |

## <a name="microsoftlocationbasedservices"></a>Microsoft. LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |

## <a name="microsoftlocationservices"></a>Microsoft. LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | Ne, jedná se o globální službu. |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | hostingenvironments | No | No | No |
> | integrationaccounts | Yes | Yes | No |
> | integrationserviceenvironments | Yes | No | No |
> | integrationserviceenvironments/Inspirujte | Yes | No | No |
> | isolatedenvironments | No | No | No |
> | Zpracovávaný | Yes | Yes | No |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | commitmentplans | No | No | No |
> | WebServices | Yes | No | No |
> | pracovní prostory | Yes | Yes | No |

## <a name="microsoftmachinelearningcompute"></a>Microsoft. MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | operationalizationclusters | No | No | No |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |
> | teamaccounts | No | No | No |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft. MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | pracovní prostory | No | No | No |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | configurationassignments | No | No | Yes. [Další informace](../../virtual-machines/move-region-maintenance-configuration.md) |
> | maintenanceconfigurations | Yes | Ano | Ano. [Další informace](../../virtual-machines/move-region-maintenance-configuration-resources.md) |
> | aktualizovány | No | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | nebyly | No | No | No |
> | userassignedidentities | No | No | No |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | managednetworks | No | No | No |
> | managednetworks / managednetworkgroups | No | No | No |
> | managednetworks / managednetworkpeeringpolicies | No | No | No |
> | oznámení | No | No | No |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | marketplaceregistrationdefinitions | No | No | No |
> | registrationassignments | No | No | No |
> | registrationdefinitions | No | No | No |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | getentities | No | No | No |
> | managementgroups | No | No | No |
> | managementgroups/nastavení | No | No | No |
> | resources | No | No | No |
> | starttenantbackfill | No | No | No |
> | tenantbackfillstatus | No | No | No |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | Yes | Yes | Ne, Azure Maps je geoprostorové služba. |
> | účty/privateatlases | Yes | Yes | No |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | Rozsah | No | No | No |
> | offertypes | No | No | No |
> | privategalleryitems | No | No | No |
> | privatestoreclient | No | No | No |
> | privatestores | No | No | No |
> | produktech | No | No | No |
> | zdrojů | No | No | No |
> | register | No | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | classicdevservices | No | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | o | No | No | No |
> | offertypes | No | No | No |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | MediaServices | Yes | Yes | No |
> | MediaServices/liveevents | Yes | Yes | No |
> | MediaServices/starají | Yes | Yes | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | appclusters | No | No | No |

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | assessmentprojects | No | No | No |
> | migrateprojects | No | No | No |
> | movecollections | No | No | No |
> | projekty | No | No | No |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ---------- |
> | holographicsbroadcastaccounts | No | No | No |
> | objectunderstandingaccounts | No | No | No |
> | remoterenderingaccounts | Yes | Yes | No |
> | spatialanchorsaccounts | Yes | Yes | No |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | netappaccounts | No | No | No |
> | netappaccounts / capacitypools | No | No | No |
> | netappaccounts/capacitypools/svazky | No | No | No |
> | netappaccounts/capacitypools/svazky/mounttargets | No | No | No |
> | netappaccounts/capacitypools/svazky/snímky | No | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!IMPORTANT]
> Viz [pokyny k přesunu sítě](./move-limitations/networking-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationgateways | No | No | No |
> | applicationgatewaywebapplicationfirewallpolicies | No | No | No |
> | applicationsecuritygroups | Yes | Yes | No |
> | azurefirewalls | No | No | No |
> | bastionhosts | No | No | No |
> | bgpservicecommunities | No | No | No |
> | připojení | Yes | Yes | No |
> | ddoscustompolicies | Yes | Yes | No |
> | ddosprotectionplans | No | No | No |
> | dnszones | Yes | Yes | No |
> | expressroutecircuits | No | No | No |
> | expressroutegateways | No | No | No |
> | expressrouteserviceproviders | No | No | No |
> | firewallpolicies | Yes | Yes | No |
> | frontdoors | No | No | No |
> | ipallocations | Yes | Yes | No |
> | ipgroups | Yes | Yes | No |
> | loadbalancers | Ano – základní SKU<br> Ano – standardní SKU | Ano – základní SKU<br>No – standardní SKU | Yes <br/><br/> K přesunutí interních a externích nástrojů pro vyrovnávání zatížení použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | localnetworkgateways | Yes | Yes | No |
> | natgateways | No | No | No |
> | networkexperimentprofiles | No | No | No |
> | networkintentpolicies | Yes | Yes | No |
> | networkinterfaces | Yes | Yes | Yes <br/><br/> K přesunutí síťových adaptérů použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | networkprofiles | No | No | No |
> | networksecuritygroups | Yes | Yes | Yes <br/><br/> K přesunutí skupin zabezpečení sítě (NGSs) použijte [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) . |
> | networkwatchers | No | No | No |
> | networkwatchers / connectionmonitors | Yes | No | No |
> | networkwatchers / flowlogs | Yes | No | No |
> | networkwatchers / pingmeshes | Yes | No | No |
> | p2svpngateways | No | No | No |
> | privatednszones | Yes | Yes | No |
> | privatednszones / virtualnetworklinks | Yes | Yes | No |
> | privatednszonesinternal | No | No | No |
> | privateendpointredirectmaps | No | No | No |
> | privateendpoints | No | No | No |
> | privatelinkservices | No | No | No |
> | publicipaddresses | Ano – základní SKU<br>Ano – standardní SKU | Ano – základní SKU<br>No – standardní SKU | Yes<br/><br/> Pomocí služby [Azure Resource stěhovací](../../resource-mover/tutorial-move-region-virtual-machines.md) PŘESUŇTE veřejné IP adresy. |
> | publicipprefixes | Yes | Yes | No |
> | routefilters | No | No | No |
> | routetables | Yes | Yes | No |
> | securitypartnerproviders | Yes | Yes | No |
> | serviceendpointpolicies | Yes | Yes | No |
> | trafficmanagergeographichierarchies | No | No | No |
> | trafficmanagerprofiles | Yes | Yes | No |
> | trafficmanagerprofiles/Heat mapy | No | No | No |
> | trafficmanagerusermetricskeys | No | No | No |
> | virtualhubs | No | No | No |
> | virtualnetworkgateways | Yes | Yes | No |
> | virtualnetworks | Yes | Yes | No |
> | virtualnetworktaps | No | No | No |
> | virtualrouters | Yes | Yes | No |
> | virtualwans | No | No |
> | vpngateways (virtuální síť WAN) | No | No | No |
> | vpnserverconfigurations | No | No | No |
> | vpnsites (virtuální síť WAN) | No | No | No |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | obsažené | Yes | Yes | No |
> | obory názvů/notificationhubs | Yes | Yes | No |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | osnamespaces | Yes | Yes | No |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | hypervsites | No | No | No |
> | importsites | No | No | No |
> | serversites | No | No | No |
> | vmwaresites | No | No | No |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!IMPORTANT]
> Ujistěte se, že přesun do nového předplatného nepřekračuje [kvóty předplatného](azure-subscription-service-limits.md#azure-monitor-limits).
>
> Pracovní prostory, které mají propojený účet Automation, nejde přesunout. Než začnete s přesunem, nezapomeňte zrušit propojení jakýchkoli účtů Automation.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | existující | No | No | No |
> | deletedworkspaces | No | No | No |
> | linktargets | No | No | No |
> | storageinsightconfigs | No | No | No |
> | pracovní prostory | Yes | Yes | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | managementassociations | No | No | No |
> | managementconfigurations | Yes | Yes | No |
> | Řešení | Yes | Yes | No |
> | zobrazení | Yes | Yes | No |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | legacypeerings | No | No | No |
> | peerasns | No | No | No |
> | peeringlocations | No | No | No |
> | partnerské vztahy | No | No | No |
> | peeringservicecountries | No | No | No |
> | peeringservicelocations | No | No | No |
> | peeringserviceproviders | No | No | No |
> | peeringservices | No | No | No |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | policyevents | No | No | No |
> | policystates | No | No | No |
> | policytrackedresources | No | No | No |
> | nápravy | No | No | No |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | Konzoly nástroje | No | No | No |
> | řídicí panely | Yes | Yes | No |
> | usersettings | No | No | No |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | workspacecollections | Yes | Yes | No |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | schopností | Yes | Yes | No |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | No | No | No |

## <a name="microsoftpurview"></a>Microsoft. dosah

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ---------- |
> | accounts | **uložené** | **uložené** | No |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | availableaccounts | No | No | No |
> | providerregistrations | No | No | No |
> | uvádění | No | No | No |

## <a name="microsoftquantum"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | pracovní prostory | No | No | No |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí Recovery Services](../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | replicationeligibilityresults | No | No | No |
> | trezory | Yes | Ano | Ne.<br/><br/> Přesunutí Recovery Services trezorů pro Azure Backup napříč oblastmi Azure se nepodporuje.<br/><br/> V Recovery Services trezory pro Azure Site Recovery můžete [trezor zakázat a znovu vytvořit](../../site-recovery/move-vaults-across-regions.md) v cílové oblasti. |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | openshiftclusters | No | No | No |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | obsažené | Yes | Yes | No |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | odešle | Yes | Yes | No |
> | resourcechangedetails | No | No | No |
> | resourcechanges | No | No | No |
> | resources | No | No | No |
> | resourceshistory | No | No | No |
> | subscriptionsstatus | No | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | childresources | No | No | No |
> | emergingissues | No | No | No |
> | stránka events | No | No | No |
> | zprostředkovatele identity | No | No | No |
> | připomenutí | No | No | No |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | nasazení | No | No | No |
> | deploymentscripts | No | No | Yes<br/><br/>[Přesunout prostředky Microsoft. Resources do nové oblasti](microsoft-resources-move-regions.md) |
> | deploymentscripts/protokoly | No | No | No |
> | odkazy | No | No | No |
> | dodavateli | No | No | No |
> | ResourceGroups | No | No | No |
> | resources | No | No | No |
> | odběru | No | No | No |
> | tags | No | No | No |
> | templatespecs | No | No | Yes<br/><br/>[Přesunout prostředky Microsoft. Resources do nové oblasti](microsoft-resources-move-regions.md) |
> | templatespecs/verze | No | No | No |
> | tenantů | No | No | No |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | aplikace | Yes | No | No |
> | saasresources | No | No | No |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!IMPORTANT]
> V jedné operaci nemůžete přesouvat několik prostředků vyhledávání v různých oblastech. Místo toho je přesuňte do samostatných operací.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | resourcehealthmetadata | No | No | No |
> | searchservices | Yes | Yes | No |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | adaptivenetworkhardenings | No | No | No |
> | advancedthreatprotectionsettings | No | No | No |
> | výstrahy | No | No | No |
> | allowedconnections | No | No | No |
> | applicationwhitelistings | No | No | No |
> | assessmentmetadata | No | No | No |
> | hodnocení | No | No | No |
> | autodismissalertsrules | No | No | No |
> | automatizace | Yes | Yes | No |
> | autoprovisioningsettings | No | No | No |
> | complianceresults | No | No | No |
> | předpisů | No | No | No |
> | datacollectionagents | No | No | No |
> | devicesecuritygroups | No | No | No |
> | discoveredsecuritysolutions | No | No | No |
> | externalsecuritysolutions | No | No | No |
> | informationprotectionpolicies | No | No | No |
> | iotsecuritysolutions | Yes | Yes | No |
> | iotsecuritysolutions / analyticsmodels | No | No | No |
> | iotsecuritysolutions / analyticsmodels / aggregatedalerts | No | No | No |
> | iotsecuritysolutions / analyticsmodels / aggregatedrecommendations | No | No | No |
> | jitnetworkaccesspolicies | No | No | No |
> | Zásady | No | No | No |
> | ceny | No | No | No |
> | regulatorycompliancestandards | No | No | No |
> | regulatorycompliancestandards / regulatorycompliancecontrols | No | No | No |
> | regulatorycompliancestandards / regulatorycompliancecontrols / regulatorycomplianceassessments | No | No | No |
> | securitycontacts | No | No | No |
> | securitysolutions | No | No | No |
> | securitysolutionsreferencedata | No | No | No |
> | securitystatuses | No | No | No |
> | securitystatusessummaries | No | No | No |
> | servervulnerabilityassessments | No | No | No |
> | nastavení | No | No | No |
> | podhodnocení | No | No | No |
> | úlohy | No | No | No |
> | topologie | No | No | No |
> | workspacesettings | No | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | agregace | No | No | No |
> | alertrules | No | No | No |
> | alertruletemplates | No | No | No |
> | automationrules | No | No | No |
> | záložky | No | No | No |
> | věcech | No | No | No |
> | dataconnects | No | No | No |
> | podnikům | No | No | No |
> | entityqueries | No | No | No |
> | Incidenty | No | No | No |
> | officeconsents | No | No | No |
> | nastavení | No | No | No |
> | threatintelligence | No | No | No |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | consoleservices | No | No | No |

## <a name="microsoftservermanagement"></a>Microsoft. ServerManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | brány | No | No | No |
> | sortiment | No | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | obsažené | Yes | Yes | No |
> | premiummessagingregions | No | No | No |
> | skladové | No | No | No |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | aplikace | No | No | No |
> | existující | Yes | Yes | No |
> | containergroups | No | No | No |
> | containergroupsets | No | No | No |
> | edgeclusters | No | No | No |
> | managedclusters | No | No | No |
> | sítí | No | No | No |
> | secretstores | No | No | No |
> | volumes | No | No | No |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | aplikace | Yes | Yes | No |
> | containergroups | No | No | No |
> | brány | Yes | Yes | No |
> | sítí | Yes | Yes | No |
> | záleží | Yes | Yes | No |
> | volumes | Yes | Yes | No |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | uvádění | No | No | No |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | SignalR | Yes | Yes | No |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | hybridusebenefits | No | No | No |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | applicationdefinitions | No | No | No |
> | aplikace | No | No | No |
> | jitrequests | No | No | No |

## <a name="microsoftsql"></a>Microsoft.Sql

> [!IMPORTANT]
> Databáze a server musí být ve stejné skupině prostředků. Když přesunete SQL Server, přesunou se také všechny jeho databáze. Toto chování platí pro databáze Azure SQL Database a Azure synapse Analytics.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | instancepools | No | No | No |
> | polohy | Yes | Yes | No |
> | managedinstances | No | No | Yes <br/><br/> [Přečtěte si další informace](../../azure-sql/database/move-resources-across-regions.md) o přesouvání spravovaných instancí napříč oblastmi. |
> | managedinstances/databáze | No | No | Yes |
> | servery | Yes | Yes |Yes |
> | servery/databáze | Yes | Yes | Yes <br/><br/> [Přečtěte si další informace](../../azure-sql/database/move-resources-across-regions.md) o přesouvání databází napříč oblastmi.<br/><br/> [Přečtěte si další informace](../../resource-mover/tutorial-move-region-sql.md) o použití Azure Resource stěhovací k přesunu databází SQL Azure.  |
> | servery/databáze/backuplongtermretentionpolicies | Yes | Yes | No |
> | servery/elasticpools | Yes | Yes | Yes <br/><br/> [Přečtěte si další informace](../../azure-sql/database/move-resources-across-regions.md) o přesouvání elastických fondů napříč oblastmi.<br/><br/> [Přečtěte si další informace](../../resource-mover/tutorial-move-region-sql.md) o použití Azure Resource stěhovací k přesunu elastických fondů Azure SQL.  |
> | servery/jobaccounts | Yes | Yes | No |
> | servery/jobagents | Yes | Yes | No |
> | virtualclusters | Yes | Yes | Yes |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | sqlvirtualmachinegroups | Yes | Yes | No |
> | sqlvirtualmachines | Yes | Yes | No |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | storageaccounts | Yes | Yes | Yes<br/><br/> [Přesunutí účtu Azure Storage do jiné oblasti](../../storage/common/storage-account-move.md) |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | mezipaměti | No | No | No |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | Yes | Yes | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | No | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | storagesyncservices | No | No | No |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | jednatel | No | No | No |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!IMPORTANT]
> Stream Analytics úlohy nelze přesunout, pokud je ve spuštěném stavu.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | existující | No | No | No |
> | streamingjobs | Yes | Yes | No |

## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft. StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | Environment | No | No | No |
> | instance | No | No | No |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | odběru | No | No | No |

## <a name="microsoftsupport"></a>Microsoft. support

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | services | No | No | No |
> | supporttickets | No | No | No |

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | pracovní prostory | No | No | No |
> | pracovní prostory/bigdatapools | No | No | No |
> | pracovní prostory/sqlpools | No | No | No |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | Environment | Yes | Yes | No |
> | prostředí/EventSources | Yes | Yes | No |
> | prostředí/referencedatasets | Yes | Yes | No |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | Store | Yes | Yes | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | imagetemplates | No | No | No |

## <a name="microsoftvisualstudio"></a>Microsoft. VisualStudio

> [!IMPORTANT]
> Pokud chcete změnit předplatné služby Azure DevOps, přečtěte si téma [Změna předplatného Azure používaného pro fakturaci](/azure/devops/organizations/billing/change-azure-subscription?toc=/azure/azure-resource-manager/toc.json).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | account | No | No | No |
> | účet/rozšíření | No | No | No |
> | účet/projekt | No | No | No |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | arczones | No | No | No |
> | resourcepools | No | No | No |
> | servery vCenter | No | No | No |
> | virtualmachines | No | No | No |
> | virtualmachinetemplates | No | No | No |
> | virtualnetworks | No | No | No |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | dedicatedcloudnodes | No | No | No |
> | dedicatedcloudservices | No | No | No |
> | virtualmachines | No | No | No |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | zařízení | No | No | No |
> | vnfs | No | No | No |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | accounts | No | No | No |
> | plánují | No | No | No |
> | registeredsubscriptions | No | No | No |

## <a name="microsoftweb"></a>Microsoft. Web

> [!IMPORTANT]
> Přečtěte si téma [pokyny k přesunutí App Service](./move-limitations/app-service-move-limitations.md).

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | availablestacks | No | No | No |
> | billingmeters | No | No | No |
> | certifikáty | No | Yes | No |
> | connectiongateways | Yes | Yes | No |
> | připojení | Yes | Yes | No |
> | customapis | Yes | Yes | No |
> | deletedsites | No | No | No |
> | deploymentlocations | No | No | No |
> | geografická umístění | No | No | No |
> | hostingenvironments | No | No | No |
> | kubeenvironments | Yes | Yes | No |
> | publishingusers | No | No | No |
> | doporučit | No | No | No |
> | resourcehealthmetadata | No | No | No |
> | moduly runtime | No | No | No |
> | serverových farem | Yes | Yes | No |
> | serverových farem/eventgridfilters | No | No | No |
> | místa | Yes | Yes | No |
> | lokality/premieraddons | Yes | Yes | No |
> | lokality/sloty | Yes | Yes | No |
> | sourcecontrols | No | No | No |
> | staticsites | No | No | No |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | multipleactivationkeys | No | No | No |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | deviceservices | No | No | No |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | úlohy | No | No | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Skupina prostředků | Předplatné | Přesunutí oblasti |
> | ------------- | ----------- | ---------- | ----------- |
> | konstrukční | No | No | No |
> | componentssummary | No | No | No |
> | monitorinstances | No | No | No |
> | monitorinstancessummary | No | No | No |
> | Monitor | No | No | No |

## <a name="third-party-services"></a>Služby třetích stran

Služby třetích stran aktuálně nepodporují operaci přesunu.

## <a name="next-steps"></a>Další kroky

- Příkazy pro přesunutí prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](move-resource-group-and-subscription.md).
- [Přečtěte si další informace](../../resource-mover/overview.md) o službě Resource stěhovací.
- Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte [move-support-resources.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/move-support-resources.csv).

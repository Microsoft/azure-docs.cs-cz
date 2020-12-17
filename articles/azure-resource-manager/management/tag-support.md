---
title: Podpora značek pro prostředky
description: Zobrazuje, které typy prostředků Azure podporují značky. Poskytuje podrobnosti pro všechny služby Azure.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 12a57577241e3b2206aab9c3badb29502e7b0301
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656967"
---
# <a name="tag-support-for-azure-resources"></a>Podpora značek pro prostředky Azure
Tento článek popisuje, zda typ prostředku podporuje [značky](tag-resources.md). Sloupec s popisem **podporuje značky** označuje, zda typ prostředku má vlastnost pro značku. Sloupec s označením **značka v sestavě náklady** označuje, zda tento typ prostředku předá značku k sestavě nákladů. Náklady můžete zobrazit podle značek v [cost management analýza nákladů](../../cost-management-billing/costs/group-filter.md) a [fakturace Azure a data o denním využití](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Přejít na obor názvů poskytovatele prostředků:
> [!div class="op_single_selector"]
> - [Microsoft. AAD](#microsoftaad)
> - [Microsoft. addons](#microsoftaddons)
> - [Microsoft. ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft. Advisor](#microsoftadvisor)
> - [Microsoft. AgFoodPlatform](#microsoftagfoodplatform)
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft. automanage](#microsoftautomanage)
> - [Microsoft. Automation](#microsoftautomation)
> - [Microsoft. AVS](#microsoftavs)
> - [Microsoft. Azure. Ženeva](#microsoftazuregeneva)
> - [Microsoft. Azureactivedirectory selhala](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. AzureStackHCI](#microsoftazurestackhci)
> - [Microsoft. BareMetalInfrastructure](#microsoftbaremetalinfrastructure)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft. fakturace](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. BlockchainTokens](#microsoftblockchaintokens)
> - [Microsoft. detail](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ChangeAnalysis](#microsoftchangeanalysis)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Codespaces](#microsoftcodespaces)
> - [Microsoft. Cognitiveservices Account](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft. ConnectedCache](#microsoftconnectedcache)
> - [Microsoft. spotřeba](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. CostManagement](#microsoftcostmanagement)
> - [Microsoft. CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft. CustomProviders](#microsoftcustomproviders)
> - [Microsoft. D365CustomerInsights](#microsoftd365customerinsights)
> - [Microsoft. DataBox](#microsoftdatabox)
> - [Microsoft. DataBoxEdge](#microsoftdataboxedge)
> - [Cihly Microsoft.](#microsoftdatabricks)
> - [Microsoft. datacatalog](#microsoftdatacatalog)
> - [Microsoft. DataFactory](#microsoftdatafactory)
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
> - [Microsoft. DeviceUpdate](#microsoftdeviceupdate)
> - [Microsoft. DevOps](#microsoftdevops)
> - [Microsoft. DevSpaces](#microsoftdevspaces)
> - [Microsoft. DevTestLab](#microsoftdevtestlab)
> - [Microsoft. DigitalTwins](#microsoftdigitaltwins)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
> - [Microsoft. experimentování](#microsoftexperimentation)
> - [Microsoft. Falcon](#microsoftfalcon)
> - [Microsoft. Features](#microsoftfeatures)
> - [Microsoft. Gallery](#microsoftgallery)
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
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Trezor Microsoft.](#microsoftkeyvault)
> - [Microsoft. Kubernetes](#microsoftkubernetes)
> - [Microsoft. KubernetesConfiguration](#microsoftkubernetesconfiguration)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
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
> - [Microsoft. poznámkové bloky](#microsoftnotebooks)
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
> - [Microsoft. ScVmm](#microsoftscvmm)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft. SerialConsole](#microsoftserialconsole)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. jednotného](#microsoftsingularity)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. synapse](#microsoftsynapse)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. token](#microsofttoken)
> - [Microsoft. VirtualMachineImages](#microsoftvirtualmachineimages)
> - [Microsoft. VMware](#microsoftvmware)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. VnfManager](#microsoftvnfmanager)
> - [Microsoft. VSOnline](#microsoftvsonline)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsESU](#microsoftwindowsesu)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. WorkloadBuilder](#microsoftworkloadbuilder)
> - [Microsoft. monitor zátěže byl](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ano | Ano |
> | DomainServices / oucontainer | Ne | Ne |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | supportProviders | Ne | Ne |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
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
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | advisorScore | Ne | Ne |
> | konfiguračních | Ne | Ne |
> | generateRecommendations | Ne | Ne |
> | zprostředkovatele identity | Ne | Ne |
> | doporučit | Ne | Ne |
> | potlačení | Ne | Ne |

## <a name="microsoftagfoodplatform"></a>Microsoft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | farmBeats | Ano | Ano |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | actionRules | Ano | Ano |
> | výstrahy | Ne | Ne |
> | alertsList | Ne | Ne |
> | alertsMetaData | Ne | Ne |
> | alertsSummary | Ne | Ne |
> | alertsSummaryList | Ne | Ne |
> | smartDetectorAlertRules | Ano | Ano |
> | smartGroups | Ne | Ne |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | servery | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Ne | Ne |
> | service | Ano | Ano |
> | validateServiceName | Ne | Ne |

> [!NOTE]
> Azure API Management podporuje jenom vytváření maximálně 15 párů název/hodnota značky pro každou službu.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ano | Ne |
> | configurationStores / eventGridFilters | Ne | Ne |
> | configurationStores/hodnoty | Ne | Ne |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Spring | Ano | Ano |
> | Pružina/aplikace | Ne | Ne |
> | Jarní/aplikace/nasazení | Ne | Ne |

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Ano | Ano |
> | defaultProvider – neexistují | Ne | Ne |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | Ne | Ne |
> | accessReviewScheduleSettings | Ne | Ne |
> | classicAdministrators | Ne | Ne |
> | dataaliasy | Ne | Ne |
> | denyAssignments | Ne | Ne |
> | elevateAccess | Ne | Ne |
> | findOrphanRoleAssignments | Ne | Ne |
> | počtu | Ne | Ne |
> | oprávnění | Ne | Ne |
> | policyAssignments | Ne | Ne |
> | policyDefinitions | Ne | Ne |
> | policyExemptions | Ne | Ne |
> | policySetDefinitions | Ne | Ne |
> | privateLinkAssociations | Ne | Ne |
> | providerOperations | Ne | Ne |
> | resourceManagementPrivateLinks | Ano | Ano |
> | roleAssignments | Ne | Ne |
> | roleAssignmentsUsageMetrics | Ne | Ne |
> | roleDefinitions | Ne | Ne |

## <a name="microsoftautomanage"></a>Microsoft. automanage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | configurationProfileAssignments | Ne | Ne |
> | configurationProfilePreferences | Ano | Ano |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ano | Ano |
> | automationAccounts/konfigurace | Ano | Ano |
> | automationAccounts/úlohy | Ne | Ne |
> | automationAccounts / privateEndpointConnectionProxies | Ne | Ne |
> | automationAccounts / privateEndpointConnections | Ne | Ne |
> | automationAccounts / privateLinkResources | Ne | Ne |
> | automationAccounts/Runbooky | Ano | Ano |
> | automationAccounts / softwareUpdateConfigurations | Ne | Ne |
> | automationAccounts nebo Webhooky | Ne | Ne |

> [!NOTE]
> Azure Automation podporuje pouze vytváření maximálně 15 párů název/hodnota značky pro každý prostředek automatizace.

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | privateClouds | Ano | Ano |
> | privateClouds/Doplňky | Ne | Ne |
> | privateClouds/autorizace | Ne | Ne |
> | privateClouds/clustery | Ne | Ne |
> | privateClouds / globalReachConnections | Ne | Ne |
> | privateClouds / hcxEnterpriseSites | Ne | Ne |
> | privateClouds / workloadNetworks | Ne | Ne |
> | privateClouds / workloadNetworks / dhcpConfigurations | Ne | Ne |
> | privateClouds/workloadNetworks/Brans | Ne | Ne |
> | privateClouds / workloadNetworks / portMirroringProfiles | Ne | Ne |
> | privateClouds/workloadNetworks/segmenty | Ne | Ne |
> | privateClouds / workloadNetworks / virtualMachines | Ne | Ne |
> | privateClouds / workloadNetworks / vmGroups | Ne | Ne |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Ženeva

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Environment | Ne | Ne |
> | prostředí/účty | Ne | Ne |
> | prostředí/účty/obory názvů | Ne | Ne |
> | prostředí/účty/obory názvů/konfigurace | Ne | Ne |

## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Ano | Ne |
> | b2ctenants | Ne | Ne |
> | guestUsages | Ano | Ano |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | datacontrollery | Ano | Ano |
> | postgresInstances | Ano | Ano |
> | sqlManagedInstances | Ano | Ano |
> | sqlServerInstances | Ano | Ano |
> | sqlServerRegistrations | Ano | Ano |
> | sqlServerRegistrations/sqlServers | Ne | Ne |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Ne | Ne |
> | edgeSubscriptions | Ano | Ano |
> | linkedSubscriptions | Ano | Ano |
> | rozpoznávání | Ano | Ano |
> | registrace/customerSubscriptions | Ne | Ne |
> | registrace/produkty | Ne | Ne |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Ano | Ano |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | bareMetalInstances | Ano | Ano |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ano | Ano |
> | batchAccounts/certifikáty | Ne | Ne |
> | batchAccounts/fondy | Ne | Ne |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Ne | Ne |
> | billingAccounts/smlouvy | Ne | Ne |
> | billingAccounts / billingPermissions | Ne | Ne |
> | billingAccounts / billingProfiles | Ne | Ne |
> | billingAccounts / billingProfiles / billingPermissions | Ne | Ne |
> | billingAccounts / billingProfiles / billingRoleAssignments | Ne | Ne |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Ne | Ne |
> | billingAccounts / billingProfiles / billingSubscriptions | Ne | Ne |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Ne | Ne |
> | billingAccounts/billingProfiles/zákazníci | Ne | Ne |
> | billingAccounts/billingProfiles/– pokyny | Ne | Ne |
> | billingAccounts/billingProfiles/faktury | Ne | Ne |
> | billingAccounts/billingProfiles/faktury/pricesheet | Ne | Ne |
> | billingAccounts/billingProfiles/faktury/transakce | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Ne | Ne |
> | billingAccounts/billingProfiles/invoiceSections/Products | Ne | Ne |
> | billingAccounts/billingProfiles/invoiceSections/Products/Transfer | Ne | Ne |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Ne | Ne |
> | billingAccounts/billingProfiles/invoiceSections/transakce | Ne | Ne |
> | billingAccounts/billingProfiles/invoiceSections/transfery | Ne | Ne |
> | billingAccounts / BillingProfiles / patchOperations | Ne | Ne |
> | billingAccounts / billingProfiles / paymentMethods | Ne | Ne |
> | billingAccounts/billingProfiles/– zásady | Ne | Ne |
> | billingAccounts/billingProfiles/pricesheet | Ne | Ne |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Ne | Ne |
> | billingAccounts/billingProfiles/produkty | Ne | Ne |
> | billingAccounts/billingProfiles/rezervace | Ne | Ne |
> | billingAccounts/billingProfiles/transakcí | Ne | Ne |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Ne | Ne |
> | billingAccounts / billingRoleAssignments | Ne | Ne |
> | billingAccounts / billingRoleDefinitions | Ne | Ne |
> | billingAccounts / billingSubscriptions | Ne | Ne |
> | billingAccounts/billingSubscriptions/faktury | Ne | Ne |
> | billingAccounts / createBillingRoleAssignment | Ne | Ne |
> | billingAccounts / createInvoiceSectionOperations | Ne | Ne |
> | billingAccounts/zákazníci | Ne | Ne |
> | billingAccounts/Customers/billingPermissions | Ne | Ne |
> | billingAccounts/Customers/billingSubscriptions | Ne | Ne |
> | billingAccounts/Customers/initiateTransfer | Ne | Ne |
> | billingAccounts/Customers/policies | Ne | Ne |
> | billingAccounts/zákazníci/produkty | Ne | Ne |
> | billingAccounts/zákazníci/transakce | Ne | Ne |
> | billingAccounts/zákazníci/přenosy | Ne | Ne |
> | billingAccounts/oddělení | Ne | Ne |
> | billingAccounts/oddělení/billingPermissions | Ne | Ne |
> | billingAccounts/oddělení/billingRoleAssignments | Ne | Ne |
> | billingAccounts/oddělení/billingRoleDefinitions | Ne | Ne |
> | billingAccounts / enrollmentAccounts | Ne | Ne |
> | billingAccounts / enrollmentAccounts / billingPermissions | Ne | Ne |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Ne | Ne |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Ne | Ne |
> | billingAccounts/faktury | Ne | Ne |
> | billingAccounts/faktury/transakce | Ne | Ne |
> | billingAccounts / invoiceSections | Ne | Ne |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Ne | Ne |
> | billingAccounts / invoiceSections / billingSubscriptions | Ne | Ne |
> | billingAccounts/invoiceSections/billingSubscriptions/Transfer | Ne | Ne |
> | billingAccounts/invoiceSections/zvýšení oprávnění | Ne | Ne |
> | billingAccounts / invoiceSections / initiateTransfer | Ne | Ne |
> | billingAccounts / invoiceSections / patchOperations | Ne | Ne |
> | billingAccounts / invoiceSections / productMoveOperations | Ne | Ne |
> | billingAccounts/invoiceSections/produkty | Ne | Ne |
> | billingAccounts/invoiceSections/produkty/přenos | Ne | Ne |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Ne | Ne |
> | billingAccounts/invoiceSections/transakcí | Ne | Ne |
> | billingAccounts/invoiceSections/transfery | Ne | Ne |
> | billingAccounts / lineOfCredit | Ne | Ne |
> | billingAccounts / patchOperations | Ne | Ne |
> | billingAccounts / paymentMethods | Ne | Ne |
> | billingAccounts/produkty | Ne | Ne |
> | billingAccounts/rezervace | Ne | Ne |
> | billingAccounts/transakce | Ne | Ne |
> | billingPeriods | Ne | Ne |
> | billingPermissions | Ne | Ne |
> | billingProperty | Ne | Ne |
> | billingRoleAssignments | Ne | Ne |
> | billingRoleDefinitions | Ne | Ne |
> | createBillingRoleAssignment | Ne | Ne |
> | oddělení | Ne | Ne |
> | enrollmentAccounts | Ne | Ne |
> | faktury | Ne | Ne |
> | Převede | Ne | Ne |
> | přenosy/acceptTransfer | Ne | Ne |
> | přenosy/declineTransfer | Ne | Ne |
> | přenosy/stav operationstatus | Ne | Ne |
> | přenosy/validateTransfer | Ne | Ne |
> | validateAddress | Ne | Ne |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | mapApis | Ano | Ano |
> | updateCommunicationPreference | Ne | Ne |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Ano | Ano |
> | cordaMembers | Ano | Ano |
> | sledovacích procesů | Ano | Ano |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | TokenServices | Ano | Ano |
> | TokenServices / BlockchainNetworks | Ne | Ne |
> | TokenServices/skupiny | Ne | Ne |
> | TokenServices/skupiny/účty | Ne | Ne |
> | TokenServices / TokenTemplates | Ne | Ne |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Ne | Ne |
> | blueprintAssignments / assignmentOperations | Ne | Ne |
> | blueprintAssignments/operace | Ne | Ne |
> | podrobné plány | Ne | Ne |
> | modrotisky/artefakty | Ne | Ne |
> | plány/verze | Ne | Ne |
> | modrotisky/verze/artefakty | Ne | Ne |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | botServices | Ano | Ano |
> | botServices/kanály | Ne | Ne |
> | botServices/připojení | Ne | Ne |
> | jazyky | Ne | Ne |
> | šablony | Ne | Ne |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Redis | Ano | Ano |
> | Redis/EventGridFilters | Ne | Ne |
> | Redis/privateEndpointConnectionProxies | Ne | Ne |
> | Redis/privateEndpointConnectionProxies/ověřit | Ne | Ne |
> | Redis/privateEndpointConnections | Ne | Ne |
> | Redis/privateLinkResources | Ne | Ne |
> | redisEnterprise | Ano | Ano |
> | RedisEnterprise / privateEndpointConnectionProxies | Ne | Ne |
> | RedisEnterprise/privateEndpointConnectionProxies/ověřit | Ne | Ne |
> | RedisEnterprise / privateEndpointConnections | Ne | Ne |
> | RedisEnterprise / privateLinkResources | Ne | Ne |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Ne | Ne |
> | autoQuotaIncrease | Ne | Ne |
> | calculateExchange | Ne | Ne |
> | calculatePrice | Ne | Ne |
> | calculatePurchasePrice | Ne | Ne |
> | spustí | Ne | Ne |
> | commercialReservationOrders | Ne | Ne |
> | výměn | Ne | Ne |
> | ownReservations | Ne | Ne |
> | placePurchaseOrder | Ne | Ne |
> | reservationOrders | Ne | Ne |
> | reservationOrders / calculateRefund | Ne | Ne |
> | reservationOrders/sloučit | Ne | Ne |
> | reservationOrders/rezervace | Ne | Ne |
> | reservationOrders/rezervace/Revize | Ne | Ne |
> | reservationOrders/vrátit | Ne | Ne |
> | reservationOrders/Split | Ne | Ne |
> | reservationOrders/swap | Ne | Ne |
> | rezervace | Ne | Ne |
> | resourceProviders | Ne | Ne |
> | resources | Ne | Ne |
> | validateReservationOrder | Ne | Ne |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Ne | Ne |
> | CdnWebApplicationFirewallPolicies | Ano | Ano |
> | edgenodes | Ne | Ne |
> | uživatelů | Ano | Ano |
> | profily/koncové body | Ano | Ano |
> | profily/koncové body/customdomains | Ne | Ne |
> | profily/koncové body/origingroups | Ne | Ne |
> | profily/koncové body/zdroje | Ne | Ne |
> | validateProbe | Ne | Ne |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ano | Ano |
> | certificateOrders/certifikáty | Ne | Ne |
> | validateCertificateRegistrationInformation | Ne | Ne |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | profil | Ne | Ne |
> | resourceChanges | Ne | Ne |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | možnosti | Ne | Ne |
> | domainNames | Ne | Ne |
> | domainNames/možnosti | Ne | Ne |
> | domainNames / internalLoadBalancers | Ne | Ne |
> | domainNames / serviceCertificates | Ne | Ne |
> | domainNames/sloty | Ne | Ne |
> | domainNames/sloty/role | Ne | Ne |
> | domainNames/sloty/role/metricDefinitions | Ne | Ne |
> | domainNames/sloty/role/metriky | Ne | Ne |
> | moveSubscriptionResources | Ne | Ne |
> | operatingSystemFamilies | Ne | Ne |
> | operatingSystems | Ne | Ne |
> | quotas | Ne | Ne |
> | Typ prostředků | Ne | Ne |
> | validateSubscriptionMoveAvailability | Ne | Ne |
> | virtualMachines | Ne | Ne |
> | virtualMachines / diagnosticSettings | Ne | Ne |
> | virtualMachines / metricDefinitions | Ne | Ne |
> | virtualMachines/metriky | Ne | Ne |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Ne | Ne |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | možnosti | Ne | Ne |
> | expressRouteCrossConnections | Ne | Ne |
> | expressRouteCrossConnections/partnerské vztahy | Ne | Ne |
> | gatewaySupportedDevices | Ne | Ne |
> | networkSecurityGroups | Ne | Ne |
> | quotas | Ne | Ne |
> | reservedIps | Ne | Ne |
> | virtualNetworks | Ne | Ne |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Ne | Ne |
> | virtualNetworks/virtualNetworkPeerings | Ne | Ne |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | možnosti | Ne | Ne |
> | disků | Ne | Ne |
> | images | Ne | Ne |
> | OSImage | Ne | Ne |
> | osPlatformImages | Ne | Ne |
> | publicImages | Ne | Ne |
> | quotas | Ne | Ne |
> | storageAccounts | Ne | Ne |
> | storageAccounts/blobServices | Ne | Ne |
> | storageAccounts/služby | Ne | Ne |
> | storageAccounts/metricDefinitions | Ne | Ne |
> | storageAccounts/metriky | Ne | Ne |
> | storageAccounts/queueServices | Ne | Ne |
> | storageAccounts/služby | Ne | Ne |
> | storageAccounts/služby/diagnosticSettings | Ne | Ne |
> | storageAccounts/služby/metricDefinitions | Ne | Ne |
> | storageAccounts/služby/metriky | Ne | Ne |
> | storageAccounts/tableServices | Ne | Ne |
> | storageAccounts/vmImages | Ne | Ne |
> | vmImages | Ne | Ne |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | plánují | Ano | Ne |
> | registeredSubscriptions | Ne | Ne |

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty/privateEndpointConnectionProxies | Ne | Ne |
> | účty/privateEndpointConnections | Ne | Ne |
> | účty/privateLinkResources | Ne | Ne |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | RateCard | Ne | Ne |
> | UsageAggregates | Ne | Ne |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Ano | Ano |
> | cloudServices | Ano | Ano |
> | cloudServices / networkInterfaces | Ne | Ne |
> | cloudServices/publicIPAddresses | Ne | Ne |
> | cloudServices / roleInstances | Ne | Ne |
> | cloudServices / roleInstances / networkInterfaces | Ne | Ne |
> | cloudServices/role | Ne | Ne |
> | diskAccesses | Ano | Ano |
> | diskEncryptionSets | Ano | Ano |
> | disků | Ano | Ano |
> | Galerie | Ano | Ano |
> | Galerie/aplikace | Ne | Ne |
> | Galerie/aplikace/verze | Ne | Ne |
> | Galerie/image | Ne | Ne |
> | Galerie/image/verze | Ne | Ne |
> | hostGroups | Ano | Ano |
> | hostGroups/hostitelé | Ano | Ano |
> | images | Ano | Ano |
> | proximityPlacementGroups | Ano | Ano |
> | restorePointCollections | Ano | Ano |
> | restorePointCollections / restorePoints | Ne | Ne |
> | sharedVMExtensions | Ano | Ano |
> | sharedVMExtensions/verze | Ne | Ne |
> | sharedVMImages | Ano | Ano |
> | sharedVMImages/verze | Ne | Ne |
> | snímky | Ano | Ano |
> | sshPublicKeys | Ano | Ano |
> | virtualMachines | Ano | Ano |
> | virtualMachines/rozšíření | Ano | Ano |
> | virtualMachines / metricDefinitions | Ne | Ne |
> | virtualMachines / runCommands | Ano | Ano |
> | virtualMachineScaleSets | Ano | Ano |
> | virtualMachineScaleSets/rozšíření | Ne | Ne |
> | virtualMachineScaleSets/networkInterfaces | Ne | Ne |
> | virtualMachineScaleSets/publicIPAddresses | Ano | Ne |
> | virtualMachineScaleSets/virtualMachines | Ne | Ne |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Ne | Ne |

> [!NOTE]
> Nelze přidat značku k virtuálnímu počítači, který byl označen jako zobecněný. Virtuální počítač označíte jako zobecněný pomocí [set-AzVm-](/powershell/module/Az.Compute/Set-AzVM) [Generalized nebo AZ VM generalize](/cli/azure/vm#az-vm-generalize).

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Ano | Ano |

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Ne | Ne |
> | Zůstatky | Ne | Ne |
> | Rozpočty | Ne | Ne |
> | Poplatky | Ne | Ne |
> | CostTags | Ne | Ne |
> | dobropis | Ne | Ne |
> | stránka events | Ne | Ne |
> | Prognózy | Ne | Ne |
> | ŠARŽ | Ne | Ne |
> | Tržišť | Ne | Ne |
> | Ceníky | Ne | Ne |
> | produktech | Ne | Ne |
> | ReservationDetails | Ne | Ne |
> | ReservationRecommendationDetails | Ne | Ne |
> | ReservationRecommendations | Ne | Ne |
> | ReservationSummaries | Ne | Ne |
> | ReservationTransactions | Ne | No |
> | Značky | No | Ne |
> | tenantů | Ne | Ne |
> | Terminologie | Ne | Ne |
> | UsageDetails (Podrobnosti o využití) | Ne | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ano | Ano |
> | serviceAssociationLinks | Ne | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | registr | Ano | Ano |
> | Registry/agentPools | Ano | Ano |
> | Registry a sestavení | Ne | Ne |
> | Registry/buildy/zrušit | Ne | Ne |
> | Registry/buildy/getLogLink | Ne | Ne |
> | Registry/buildTasks | Ano | Ano |
> | Registry/buildTasks/kroky | Ne | Ne |
> | Registry/eventGridFilters | Ne | Ne |
> | Registry/exportPipelines | Ne | Ne |
> | Registry/generateCredentials | Ne | Ne |
> | Registry/getBuildSourceUploadUrl | Ne | Ne |
> | Registry/getpřihlašovací údaje | Ne | Ne |
> | Registry/importImage | Ne | Ne |
> | Registry/importPipelines | Ne | Ne |
> | Registry/pipelineRuns | Ne | Ne |
> | Registry/privateEndpointConnectionProxies | Ne | Ne |
> | Registry/privateEndpointConnectionProxies/ověřit | Ne | Ne |
> | Registry/privateEndpointConnections | Ne | Ne |
> | Registry/privateLinkResources | Ne | Ne |
> | Registry/queueBuild | Ne | Ne |
> | Registry/regenerateCredential | Ne | Ne |
> | Registry/regenerateCredentials | Ne | Ne |
> | Registry/replikace | Ano | Ano |
> | Registry/běhy | Ne | Ne |
> | Registry/spuštění/zrušit | Ne | Ne |
> | Registry/scheduleRun | Ne | Ne |
> | Registry/scopeMaps | Ne | Ne |
> | Registry/taskRuns | Ne | Ne |
> | Registry/úlohy | Ano | Ano |
> | Registry a tokeny | Ne | Ne |
> | Registry/updatePolicies | Ne | Ne |
> | Registry a Webhooky | Ano | Ano |
> | Registry/Webhooky/getCallbackConfig | Ne | Ne |
> | Registry/Webhooky/příkazy k odeslání | Ne | Ne |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | containerServices | Ano | Ano |
> | managedClusters | Ano | Ano |
> | openShiftManagedClusters | Ano | Ano |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Výstrahy | Ne | Ne |
> | BillingAccounts | Ne | Ne |
> | Rozpočty | Ne | Ne |
> | CloudConnectors | Ne | Ne |
> | Konektory | Ano | Ano |
> | costAllocationRules | Ne | Ne |
> | Oddělení | Ne | Ne |
> | Dimenze | Ne | Ne |
> | EnrollmentAccounts | Ne | Ne |
> | exporty | Ne | Ne |
> | ExternalBillingAccounts | Ne | Ne |
> | ExternalBillingAccounts/výstrahy | Ne | Ne |
> | ExternalBillingAccounts/dimenzí | Ne | Ne |
> | ExternalBillingAccounts/prognóza | Ne | Ne |
> | ExternalBillingAccounts/dotaz | Ne | Ne |
> | ExternalSubscriptions | Ne | Ne |
> | ExternalSubscriptions/výstrahy | Ne | Ne |
> | ExternalSubscriptions/dimenzí | Ne | Ne |
> | ExternalSubscriptions/prognóza | Ne | Ne |
> | ExternalSubscriptions/dotaz | Ne | Ne |
> | Prognóza | Ne | Ne |
> | Insights | Ne | Ne |
> | Dotaz | Ne | Ne |
> | register | Ne | Ne |
> | Reportconfigs | Ne | Ne |
> | Sestavy | Ne | Ne |
> | Nastavení | Ne | Ne |
> | showbackRules | Ne | Ne |
> | Zobrazení | Ne | Ne |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | požádal | Ne | Ne |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | přidružení | Ne | Ne |
> | resourceProviders | Ano | Ano |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | instance | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | úlohy | Ano | Ano |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Ano | Ano |

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | pracovní prostory | Ano | Ano |
> | pracovní prostory/dbWorkspaces | Ne | Ne |
> | pracovní prostory/virtualNetworkPeerings | Ne | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | spustí | Ano | Ano |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Objekty DataFactory | Ano | Ano |
> | DataFactory/diagnosticSettings | Ne | Ne |
> | DataFactory/metricDefinitions | Ne | Ne |
> | dataFactorySchema | Ne | Ne |
> | továrny | Ano | Ano |
> | továrny/integrationRuntimes | Ne | Ne |

> [!NOTE]
> Pokud máte v datové továrně prostředí Azure-SSIS Integration runtime, budou se jejich provozní náklady označit pomocí značek Data Factory. Spuštění prostředí Azure-SSIS Integration runtime se musí zastavit a restartovat, aby se nové značky služby Data Factory uplatnily na jejich provozní náklady.

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty/dataLakeStoreAccounts | Ne | Ne |
> | účty/storageAccounts | Ne | Ne |
> | účty/storageAccounts/kontejnery | Ne | Ne |
> | účty/transferAnalyticsUnits | Ne | Ne |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty/eventGridFilters | Ne | Ne |
> | účty/firewallRules | Ne | Ne |

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | services | Ne | Ne |
> | služby a projekty | Ne | Ne |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Ano | Ano |
> | ResourceOperationGateKeepers | Ano | Ano |

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty/sdílené složky | Ne | Ne |
> | účty/akcie/datové sady | Ne | Ne |
> | účty/akcie/pozvánky | Ne | Ne |
> | účty/akcie/providersharesubscriptions | Ne | Ne |
> | účty/akcie/synchronizationSettings | Ne | Ne |
> | účty/sharesubscriptions | Ne | Ne |
> | účty/sharesubscriptions/consumerSourceDataSets | Ne | Ne |
> | účty/sharesubscriptions/datasetmappings | Ne | Ne |
> | účty/sharesubscriptions/triggery | Ne | Ne |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | servery | Ano | Ano |
> | servery/poradci | Ne | Ne |
> | servery/klíče | Ne | Ne |
> | servery/privateEndpointConnectionProxies | Ne | Ne |
> | servery/privateEndpointConnections | Ne | Ne |
> | servery/privateLinkResources | Ne | Ne |
> | servery/queryTexts | Ne | Ne |
> | servery/recoverableServers | Ne | Ne |
> | servery/spustit | Ne | Ne |
> | servery/zastavit | Ne | Ne |
> | servery/topQueryStatistics | Ne | Ne |
> | servery/virtualNetworkRules | Ne | Ne |
> | servery/waitStatistics | Ne | Ne |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ano | Ano |
> | servery | Ano | Ano |
> | servery/poradci | Ne | Ne |
> | servery/klíče | Ne | Ne |
> | servery/privateEndpointConnectionProxies | Ne | Ne |
> | servery/privateEndpointConnections | Ne | Ne |
> | servery/privateLinkResources | Ne | Ne |
> | servery/queryTexts | Ne | Ne |
> | servery/recoverableServers | Ne | Ne |
> | servery/spustit | Ne | Ne |
> | servery/zastavit | Ne | Ne |
> | servery/topQueryStatistics | Ne | Ne |
> | servery/upgrade | Ne | Ne |
> | servery/virtualNetworkRules | Ne | Ne |
> | servery/waitStatistics | Ne | Ne |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Ano | Ano |
> | serverGroups | Ano | Ano |
> | servery | Ano | Ano |
> | servery/poradci | Ne | Ne |
> | servery/klíče | Ne | Ne |
> | servery/privateEndpointConnectionProxies | Ne | Ne |
> | servery/privateEndpointConnections | Ne | Ne |
> | servery/privateLinkResources | Ne | Ne |
> | servery/queryTexts | Ne | Ne |
> | servery/recoverableServers | Ne | Ne |
> | servery/topQueryStatistics | Ne | Ne |
> | servery/virtualNetworkRules | Ne | Ne |
> | servery/waitStatistics | Ne | Ne |
> | serversv2 | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | artifactSources | Ano | Ano |
> | uvádění | Ano | Ano |
> | serviceTopologies | Ano | Ano |
> | serviceTopologies/služby | Ano | Ano |
> | serviceTopologies/služby/serviceUnits | Ano | Ano |
> | kroky | Ano | Ano |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Ano | Ano |
> | applicationgroups/aplikace | Ne | Ne |
> | applicationgroups/desktopy | Ne | Ne |
> | applicationgroups / startmenuitems | Ne | Ne |
> | hostpools | Ano | Ano |
> | hostpools / msixpackages | Ne | Ne |
> | hostpools / sessionhosts | Ne | Ne |
> | hostpools / sessionhosts / usersessions | Ne | Ne |
> | hostpools / usersessions | Ne | Ne |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ano | Ano |
> | ElasticPools / IotHubTenants | Ano | Ano |
> | ElasticPools/IotHubTenants/securitySettings | Ne | Ne |
> | IotHubs | Ano | Ano |
> | IotHubs/eventGridFilters | Ne | Ne |
> | IotHubs/securitySettings | Ne | Ne |
> | ProvisioningServices | Ano | Ano |
> | použití | Ne | Ne |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty/instance | Ano | Ano |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | kanály | Ano | Ano |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | kontrolery | Ano | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | labcenters | Ano | Ano |
> | Labs | Ano | Ano |
> | laboratoře/prostředí | Ano | Ano |
> | Labs/serviceRunners | Ano | Ano |
> | Labs/virtualMachines | Ano | Ano |
> | časových | Ano | Ano |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Ano | Ano |
> | digitalTwinsInstances/koncové body | Ne | Ne |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Ne | Ne |
> | databaseAccounts | Ano | Ano |
> | restorableDatabaseAccounts | Ne | Ne |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | doménu | Ano | Ano |
> | domény/domainOwnershipIdentifiers | Ne | Ne |
> | generateSsoRequest | Ne | Ne |
> | topLevelDomains | Ne | Ne |
> | validateDomainRegistrationInformation | Ne | Ne |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Ne | Ne |
> | lcsprojects / clouddeployments | Ne | Ne |
> | lcsprojects/konektory | Ne | Ne |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | services | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | doménu | Ano | Ano |
> | domény a témata | Ne | Ne |
> | eventSubscriptions | Ne | Ne |
> | extensionTopics | Ne | Ne |
> | partnerNamespaces | Ano | Ano |
> | partnerNamespaces/eventChannels | Ne | Ne |
> | partnerRegistrations | Ano | Ano |
> | partnerTopics | Ano | Ano |
> | partnerTopics / eventSubscriptions | Ne | Ne |
> | systemTopics | Ano | Ano |
> | systemTopics / eventSubscriptions | Ne | Ne |
> | popisující | Ano | Ano |
> | topicTypes | Ne | Ne |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Ano | Ano |
> | obsažené | Ano | Ano |
> | obory názvů/autorizačních pravidel | Ne | Ne |
> | obory názvů/disasterrecoveryconfigs | Ne | Ne |
> | obory názvů/eventhubs | Ne | Ne |
> | obory názvů/eventhubs/autorizačních pravidel | Ne | Ne |
> | obory názvů/eventhubs/consumergroups | Ne | Ne |
> | obory názvů/networkrulesets | Ne | Ne |
> | obory názvů/privateEndpointConnections | Ne | Ne |

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Ano | Ano |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | obsažené | Ano | Ano |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | featureProviders | Ne | Ne |
> | funkce | Ne | Ne |
> | dodavateli | Ne | Ne |
> | subscriptionFeatureRegistrations | Ne | Ne |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | registrované | Ne | Ne |
> | galleryitems | Ne | Ne |
> | generateartifactaccessuri | Ne | Ne |
> | myareas | Ne | Ne |
> | myareas/oblasti | Ne | Ne |
> | myareas/oblasti/oblasti | Ne | Ne |
> | myareas/oblasti/oblasti/galleryitems | Ne | Ne |
> | myareas/oblasti/galleryitems | Ne | Ne |
> | myareas / galleryitems | Ne | Ne |
> | register | Ne | Ne |
> | resources | Ne | Ne |
> | retrieveresourcesbyid | Ne | Ne |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Ano | Ano |
> | autoManagedVmConfigurationProfiles | Ano | Ano |
> | configurationProfileAssignments | Ne | Ne |
> | guestConfigurationAssignments | Ne | Ne |
> | Vybavení | Ne | Ne |
> | softwareUpdateProfile | Ne | Ne |
> | softwareUpdates | Ne | Ne |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Ano | Ano |
> | sapMonitors | Ano | Ano |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Ano | Ano |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Ano | Ano |
> | clustery/aplikace | Ne | Ne |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | services | Ano | Ano |
> | služby/iomtconnectors | Ne | Ne |
> | služby/iomtconnectors/připojení | Ne | Ne |
> | služby/iomtconnectors/mapování | Ne | Ne |
> | služby/privateEndpointConnectionProxies | Ne | Ne |
> | služby/privateEndpointConnections | Ne | Ne |
> | služby/privateLinkResources | Ne | Ne |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | virtuální | Ano | Ano |
> | počítače/assessPatches | Ne | Ne |
> | počítače/rozšíření | Ano | Ano |
> | počítače/installPatches | Ne | Ne |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | datamanagery | Ano | Ano |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | zařízení | Ano | Ano |
> | networkFunctions | Ano | Ano |
> | networkFunctionVendors | Ne | Ne |
> | registeredSubscriptions | Ne | Ne |
> | dodavateli | Ne | Ne |
> | dodavatelé/vendorSkus | Ne | Ne |
> | dodavatelé/vendorSkus/previewSubscriptions | Ne | Ne |
> | virtualNetworkFunctions | Ano | Ano |
> | virtualNetworkFunctionVendors | Ne | Ne |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | konstrukční | Ano | Ano |
> | networkScopes | Ano | Ano |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | úlohy | Ano | Ano |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | actionGroups | Ano | Ano |
> | Upozorněníprotokoluaktivit | Ano | Ano |
> | alertrules | Ano | Ano |
> | autoscalesettings | Ano | Ano |
> | konstrukční | Ano | Ano |
> | komponenty/linkedStorageAccounts | Ne | Ne |
> | komponenty/ProactiveDetectionConfigs | Ne | Ne |
> | diagnosticSettings | Ne | Ne |
> | guestDiagnosticSettings | Ano | Ano |
> | guestDiagnosticSettingsAssociation | Ano | Ano |
> | logprofiles | Ano | Ano |
> | metricAlerts | Ano | Ano |
> | privateLinkScopes | Ano | Ano |
> | privateLinkScopes / privateEndpointConnections | Ne | Ne |
> | privateLinkScopes / scopedResources | Ne | Ne |
> | queryPacks | Ano | Ano |
> | queryPacks/dotazy | Ne | Ne |
> | scheduledQueryRules | Ano | Ano |
> | webové testy | Ano | Ano |
> | sešity | Ano | Ano |
> | workbooktemplates | Ano | Ano |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Ne | Ne |
> | diagnosticSettingsCategories | Ne | Ne |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | appTemplates | Ne | Ne |
> | IoTApps | Ano | Ano |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Graph | Ano | Ano |

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Ne | Ne |
> | hsmPools | Ano | Ano |
> | managedHSMs | Ano | Ano |
> | trezory | Ano | Ano |
> | trezory/accessPolicies | Ne | Ne |
> | trezory/eventGridFilters | Ne | Ne |
> | trezory a klíče | Ne | Ne |
> | trezory/klíče/verze | Ne | Ne |
> | trezory/tajné klíče | Ne | Ne |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Ano | Ano |
> | registeredSubscriptions | Ne | Ne |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | SND | Ne | Ne |
> | sourceControlConfigurations | Ne | Ne |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Ano | Ano |
> | clustery/attacheddatabaseconfigurations | Ne | Ne |
> | clustery/databáze | Ne | Ne |
> | clustery, databáze/datapřipojení | Ne | Ne |
> | clustery/databáze/eventhubconnections | Ne | Ne |
> | clustery/databáze/principalassignments | Ne | Ne |
> | clustery/datapřipojení | Ne | Ne |
> | clustery/principalassignments | Ne | Ne |
> | clustery/sharedidentities | Ne | Ne |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ano | Ne |
> | uživatelé | Ne | Ne |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ano | Ano |
> | integrationAccounts | Ano | Ano |
> | integrationServiceEnvironments | Ano | Ano |
> | integrationServiceEnvironments/Inspirujte | Ne | Ne |
> | isolatedEnvironments | Ano | Ano |
> | Zpracovávaný | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Ano | Ano |
> | webServices | Ano | Ano |
> | Pracovní prostory | Ano | Ano |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | pracovní prostory | Ano | Ano |
> | pracovní prostory/batchEndpoints | Ano | Ano |
> | pracovní prostory/batchEndpointsy/nasazení | Ano | Ano |
> | pracovní prostory/kódy | Ne | Ne |
> | pracovní prostory/kódy/verze | Ne | Ne |
> | pracovní prostory a výpočetní prostředky | Ne | Ne |
> | pracovní prostory/úložiště dat | Ne | Ne |
> | pracovní prostory/eventGridFilters | Ne | Ne |
> | pracovní prostory/úlohy | Ne | Ne |
> | pracovní prostory/labelingJobs | Ne | Ne |
> | pracovní prostory/linkedServices | Ne | Ne |
> | pracovní prostory a modely | Ne | Ne |
> | pracovní prostory/modely/verze | Ne | Ne |
> | pracovní prostory/onlineEndpoints | Ano | Ano |
> | pracovní prostory/onlineEndpointsy/nasazení | Ano | Ano |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Ne | Ne |
> | configurationAssignments | Ne | Ne |
> | maintenanceConfigurations | Ano | Ano |
> | publicMaintenanceConfigurations | Ne | Ne |
> | aktualizovány | Ne | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Identity | Ne | Ne |
> | userAssignedIdentities | Ano | Ano |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Ano | Ano |
> | managedNetworks / managedNetworkGroups | Ano | Ano |
> | managedNetworks / managedNetworkPeeringPolicies | Ano | Ano |
> | oznámení | Ano | Ano |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Ne | Ne |
> | registrationAssignments | Ne | Ne |
> | registrationDefinitions | Ne | Ne |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | getentities | Ne | Ne |
> | managementGroups | Ne | Ne |
> | managementGroups/nastavení | Ne | Ne |
> | resources | Ne | Ne |
> | startTenantBackfill | Ne | Ne |
> | tenantBackfillStatus | Ne | Ne |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty/eventGridFilters | Ne | Ne |
> | účty/privateAtlases | Ano | Ano |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | macc | Ne | Ne |
> | Rozsah | Ne | Ne |
> | offerTypes | Ne | Ne |
> | offerTypes/vydavatelé | Ne | Ne |
> | offerTypes/vydavatelé/nabídky | Ne | Ne |
> | offerTypes/vydavatelé/nabídky/plány | Ne | Ne |
> | offerTypes/vydavatelé/nabídky/plány/smlouvy | Ne | Ne |
> | offerTypes/vydavatelé/nabídky/plány/konfigurace | Ne | Ne |
> | offerTypes/vydavatelé/nabídky/plány/konfigurace/importImage | Ne | Ne |
> | privategalleryitems | Ne | Ne |
> | privateStoreClient | Ne | Ne |
> | privateStores | Ne | Ne |
> | privateStores/nabídky | Ne | Ne |
> | produktech | Ne | Ne |
> | zdrojů | Ne | Ne |
> | Vydavatelé/nabídky | Ne | Ne |
> | Vydavatelé/nabídky/změny | Ne | Ne |
> | register | Ne | Ne |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ano | Ano |
> | updateCommunicationPreference | Ne | Ne |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | o | Ne | Ne |
> | offertypes | Ne | Ne |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | MediaServices | Ano | Ano |
> | MediaServices/accountFilters | Ne | Ne |
> | MediaServices/assety | Ne | Ne |
> | MediaServices/assety/assetFilters | Ne | Ne |
> | MediaServices/contentKeyPolicies | Ne | Ne |
> | MediaServices/eventGridFilters | Ne | Ne |
> | MediaServices/liveEventOperations | Ne | Ne |
> | MediaServices/liveEvents | Ano | Ano |
> | MediaServices/liveEvents/liveOutputs | Ne | Ne |
> | MediaServices/liveOutputOperations | Ne | Ne |
> | MediaServices/mediaGraphs | Ne | Ne |
> | MediaServices/privateEndpointConnectionOperations | Ne | Ne |
> | MediaServices/privateEndpointConnectionProxies | Ne | Ne |
> | MediaServices/privateEndpointConnections | Ne | Ne |
> | MediaServices/streamingEndpointOperations | Ne | Ne |
> | MediaServices/starají | Ano | Ano |
> | MediaServices/streamingLocators | Ne | Ne |
> | MediaServices/streamingPolicies | Ne | Ne |
> | MediaServices/transformace | Ne | Ne |
> | MediaServices/transformace/úlohy | Ne | Ne |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | appClusters | Ano | Ano |

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Ano | Ano |
> | migrateprojects | Ano | Ano |
> | moveCollections | Ano | Ano |
> | projekty | Ano | Ano |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ano | Ano |
> | objectUnderstandingAccounts | Ano | Ano |
> | remoteRenderingAccounts | Ano | Ano |
> | spatialAnchorsAccounts | Ano | Ano |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ano | Ne |
> | netAppAccounts / accountBackups | Ne | Ne |
> | netAppAccounts / capacityPools | Ano | Ne |
> | netAppAccounts/capacityPools/svazky | Ano | Ne |
> | netAppAccounts/capacityPools/svazky/snímky | Ne | Ne |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ano | Ano |
> | applicationGatewayWebApplicationFirewallPolicies | Ano | Ano |
> | applicationSecurityGroups | Ano | Ano |
> | azureFirewallFqdnTags | Ne | Ne |
> | azureFirewalls | Ano | Ne |
> | bastionHosts | Ano | Ne |
> | bgpServiceCommunities | Ne | Ne |
> | připojení | Ano | Ano |
> | ddosCustomPolicies | Ano | Ano |
> | ddosProtectionPlans | Ano | Ano |
> | dnsOperationStatuses | Ne | Ne |
> | dnszones | Ano | Ano |
> | dnszones/A | Ne | Ne |
> | dnszones/AAAA | Ne | Ne |
> | dnszones/vše | Ne | Ne |
> | dnszones/CAA | Ne | Ne |
> | dnszones/CNAME | Ne | Ne |
> | dnszones/MX | Ne | Ne |
> | dnszones/NS | Ne | Ne |
> | dnszones/PTR | Ne | Ne |
> | dnszones/sady záznamů | Ne | Ne |
> | dnszones/SOA | Ne | Ne |
> | dnszones/SRV | Ne | Ne |
> | dnszones/TXT | Ne | Ne |
> | expressRouteCircuits | Ano | Ano |
> | expressRouteCrossConnections | Ano | Ano |
> | expressRouteGateways | Ano | Ano |
> | expressRoutePorts | Ano | Ano |
> | expressRouteServiceProviders | Ne | Ne |
> | firewallPolicies | Ano | Ano |
> | frontdoors | Ano, ale omezeno (viz [Poznámka níže](#frontdoor)) | Ano |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ano, ale omezeno (viz [Poznámka níže](#frontdoor)) | Ne |
> | frontdoorWebApplicationFirewallPolicies | Ano, ale omezeno (viz [Poznámka níže](#frontdoor)) | Ano |
> | getDnsResourceReference | Ne | Ne |
> | internalNotify | Ne | Ne |
> | ipGroups | Ano | Ano |
> | loadBalancers | Ano | Ano |
> | localNetworkGateways | Ano | Ano |
> | natGateways | Ano | Ano |
> | networkIntentPolicies | Ano | Ano |
> | networkInterfaces | Ano | Ano |
> | networkProfiles | Ano | Ano |
> | networkSecurityGroups | Ano | Ano |
> | networkWatchers | Ano | Ano |
> | networkWatchers / connectionMonitors | Ano | Ne |
> | networkWatchers / flowLogs | Ne | Ne |
> | networkWatchers/čočky | Ano | Ne |
> | networkWatchers / pingMeshes | Ano | Ne |
> | p2sVpnGateways | Ano | Ano |
> | privateDnsOperationStatuses | Ne | Ne |
> | privateDnsZones | Ano | Ano |
> | privateDnsZones/A | Ne | Ne |
> | privateDnsZones/AAAA | Ne | Ne |
> | privateDnsZones/vše | Ne | Ne |
> | privateDnsZones/CNAME | Ne | Ne |
> | privateDnsZones/MX | Ne | Ne |
> | privateDnsZones/PTR | Ne | Ne |
> | privateDnsZones/SOA | Ne | Ne |
> | privateDnsZones/SRV | Ne | Ne |
> | privateDnsZones/TXT | Ne | Ne |
> | privateDnsZones / virtualNetworkLinks | Ano | Ano |
> | privateEndpoints | Ano | Ano |
> | privateLinkServices | Ano | Ano |
> | publicIPAddresses | Ano | Ano |
> | publicIPPrefixes | Ano | Ano |
> | routeFilters | Ano | Ano |
> | routeTables | Ano | Ano |
> | serviceEndpointPolicies | Ano | Ano |
> | trafficManagerGeographicHierarchies | Ne | Ne |
> | trafficmanagerprofiles | Ano | Ano |
> | trafficmanagerprofiles/Heat mapy | Ne | Ne |
> | trafficManagerUserMetricsKeys | Ne | Ne |
> | virtualHubs | Ano | Ano |
> | virtualNetworkGateways | Ano | Ano |
> | virtualNetworks | Ano | Ano |
> | virtualNetworks/podsítě | Ne | Ne |
> | virtualNetworkTaps | Ano | Ano |
> | virtualWans | Ano | Ne |
> | vpnGateways | Ano | Ano |
> | vpnSites | Ano | Ano |
> | webApplicationFirewallPolicies | Ano | Ano |

<a id="frontdoor"></a>

> [!NOTE]
> U služby front-dveří pro Azure můžete při vytváření prostředku použít značky, ale aktualizace nebo přidávání značek se momentálně nepodporuje.


## <a name="microsoftnotebooks"></a>Microsoft. poznámkové bloky

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | Ne | Ne |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | obsažené | Ano | Ne |
> | obory názvů/notificationHubs | Ano | Ne |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Ano | Ano |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ano | Ano |
> | ImportSites | Ano | Ano |
> | MasterSites | Ano | Ano |
> | ServerSites | Ano | Ano |
> | VMwareSites | Ano | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Ano | Ano |
> | deletedWorkspaces | Ne | Ne |
> | linkTargets | Ne | Ne |
> | storageInsightConfigs | Ne | Ne |
> | pracovní prostory | Ano | Ano |
> | pracovní prostory/dataexporty | Ne | Ne |
> | pracovní prostory/zdroje dat | Ne | Ne |
> | pracovní prostory/linkedServices | Ne | Ne |
> | pracovní prostory/linkedStorageAccounts | Ne | Ne |
> | pracovní prostory/metadata | Ne | Ne |
> | pracovní prostory a dotazy | Ne | Ne |
> | pracovní prostory/scopedPrivateLinkProxies | Ne | Ne |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | managementassociations | Ne | Ne |
> | managementconfigurations | Ano | Ano |
> | Řešení | Ano | Ano |
> | zobrazení | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Ne | Ne |
> | peerAsns | Ne | Ne |
> | partnerské vztahy | Ano | Ano |
> | peeringServiceCountries | Ne | Ne |
> | peeringServiceProviders | Ne | Ne |
> | peeringServices | Ano | Ano |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | atestace | Ne | Ne |
> | policyEvents | Ne | Ne |
> | policyMetadata | Ne | Ne |
> | policyStates | Ne | Ne |
> | policyTrackedResources | Ne | Ne |
> | nápravy | Ne | Ne |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Konzoly nástroje | Ne | Ne |
> | řídicí panely | Ano | Ano |
> | userSettings | Ne | Ne |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Ano | Ano |
> | tenantů | Ano | Ano |
> | klienti/pracovní prostory | Ne | Ne |
> | workspaceCollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | schopností | Ano | Ano |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | deletedAccounts | Ne | Ne |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Ne | Ne |
> | providerRegistrations / defaultRollouts | Ne | Ne |
> | providerRegistrations / resourceTypeRegistrations | Ne | Ne |
> | uvádění | Ano | Ano |

## <a name="microsoftquantum"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Pracovní prostory | Ano | Ano |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Ne | Ne |
> | trezory | Ano | Ano |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Ano | Ano |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | obsažené | Ano | Ano |
> | obory názvů/autorizačních pravidel | Ne | Ne |
> | obory názvů/hybridconnections | Ne | Ne |
> | obory názvů/hybridconnections/autorizačních pravidel | Ne | Ne |
> | obory názvů/privateEndpointConnections | Ne | Ne |
> | obory názvů/wcfrelays | Ne | Ne |
> | obory názvů/wcfrelays/autorizačních pravidel | Ne | Ne |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | odešle | Ano | Ano |
> | resourceChangeDetails | Ne | Ne |
> | resourceChanges | Ne | Ne |
> | resources | Ne | Ne |
> | resourcesHistory | Ne | Ne |
> | subscriptionsStatus | Ne | Ne |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Ne | Ne |
> | childAvailabilityStatuses | Ne | Ne |
> | childResources | Ne | Ne |
> | emergingissues | Ne | Ne |
> | stránka events | Ne | Ne |
> | impactedResources | Ne | Ne |
> | zprostředkovatele identity | Ne | Ne |
> | připomenutí | Ne | Ne |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | calculateTemplateHash | Ne | Ne |
> | nasazení | Ano | Ne |
> | nasazení/operace | Ne | Ne |
> | deploymentScripts | Ano | Ano |
> | deploymentScripts/protokoly | Ne | Ne |
> | odkazy | Ne | Ne |
> | notifyResourceJobs | Ne | Ne |
> | dodavateli | Ne | Ne |
> | resourceGroups | Ano | Ne |
> | odběru | Ano | Ne |
> | templateSpecs | Ano | Ano |
> | templateSpecs/verze | Ano | Ano |
> | tenantů | Ne | Ne |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aplikace | Ano | Ano |
> | saasresources | Ne | Ne |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Cloud | Ano | Ano |
> | Virtuální počítače | Ano | Ano |
> | VirtualMachineTemplates | Ano | Ano |
> | VirtualNetworks | Ano | Ano |
> | vmmservers | Ano | Ano |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Ne | Ne |
> | searchServices | Ano | Ano |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Ne | Ne |
> | advancedThreatProtectionSettings | Ne | Ne |
> | výstrahy | Ne | Ne |
> | alertsSuppressionRules | Ne | Ne |
> | allowedConnections | Ne | Ne |
> | applicationWhitelistings | Ne | Ne |
> | assessmentMetadata | Ne | Ne |
> | hodnocení | Ne | Ne |
> | autoDismissAlertsRules | Ne | Ne |
> | automatizace | Ano | Ano |
> | AutoProvisioningSettings | Ne | Ne |
> | Předpisů | Ne | Ne |
> | konektory | Ne | Ne |
> | dataCollectionAgents | Ne | Ne |
> | deviceSecurityGroups | Ne | Ne |
> | discoveredSecuritySolutions | Ne | Ne |
> | externalSecuritySolutions | Ne | Ne |
> | InformationProtectionPolicies | Ne | Ne |
> | iotDefenderSettings | Ne | Ne |
> | iotSecuritySolutions | Ano | Ano |
> | iotSecuritySolutions / analyticsModels | Ne | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Ne | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Ne | Ne |
> | iotSecuritySolutions / iotAlerts | Ne | Ne |
> | iotSecuritySolutions / iotAlertTypes | Ne | Ne |
> | iotSecuritySolutions / iotRecommendations | Ne | Ne |
> | iotSecuritySolutions / iotRecommendationTypes | Ne | Ne |
> | iotSensors | Ne | Ne |
> | jitNetworkAccessPolicies | Ne | Ne |
> | jitPolicies | Ne | Ne |
> | Zásady | Ne | Ne |
> | ceny | Ne | Ne |
> | regulatoryComplianceStandards | Ne | Ne |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Ne | Ne |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Ne | Ne |
> | secureScoreControlDefinitions | Ne | Ne |
> | secureScoreControls | Ne | Ne |
> | secureScores | Ne | Ne |
> | secureScores / secureScoreControls | Ne | Ne |
> | securityContacts | Ne | Ne |
> | securitySolutions | Ne | Ne |
> | securitySolutionsReferenceData | Ne | Ne |
> | securityStatuses | Ne | Ne |
> | securityStatusesSummaries | Ne | Ne |
> | serverVulnerabilityAssessments | Ne | Ne |
> | nastavení | Ne | Ne |
> | sqlVulnerabilityAssessments | Ne | Ne |
> | podhodnocení | Ne | Ne |
> | úlohy | Ne | Ne |
> | topologie | Ne | Ne |
> | workspaceSettings | Ne | Ne |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Ne | Ne |
> | diagnosticSettingsCategories | Ne | Ne |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | agregace | Ne | Ne |
> | alertRules | Ne | Ne |
> | alertRuleTemplates | Ne | Ne |
> | automationRules | Ne | Ne |
> | záložky | Ne | Ne |
> | věcech | Ne | Ne |
> | dataconnects | Ne | Ne |
> | dataConnectorsCheckRequirements | Ne | Ne |
> | podnikům | Ne | Ne |
> | entityQueries | Ne | Ne |
> | Incidenty | Ne | Ne |
> | officeConsents | Ne | Ne |
> | nastavení | Ne | Ne |
> | threatIntelligence | Ne | Ne |
> | watchlists | Ne | Ne |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | consoleServices | Ne | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | obsažené | Ano | Ano |
> | obory názvů/autorizačních pravidel | Ne | Ne |
> | obory názvů/disasterrecoveryconfigs | Ne | Ne |
> | obory názvů/eventgridfilters | Ne | Ne |
> | obory názvů/networkrulesets | Ne | Ne |
> | obory názvů/privateEndpointConnections | Ne | Ne |
> | obory názvů/fronty | Ne | Ne |
> | obory názvů/fronty/autorizačních pravidel | Ne | Ne |
> | obory názvů/témata | Ne | Ne |
> | obory názvů/témata/autorizačních pravidel | Ne | Ne |
> | obory názvů/témata/předplatná | Ne | Ne |
> | obory názvů/témata/předplatná/pravidla | Ne | Ne |
> | premiumMessagingRegions | Ne | Ne |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aplikace | Ano | Ano |
> | existující | Ano | Ano |
> | clustery/aplikace | Ne | Ne |
> | containerGroups | Ano | Ano |
> | containerGroupSets | Ano | Ano |
> | edgeclusters | Ano | Ano |
> | edgeclusters/aplikace | Ne | Ne |
> | managedclusters | Ano | Ano |
> | managedclusters/nodetypes | Ne | Ne |
> | sítí | Ano | Ano |
> | secretstores | Ano | Ano |
> | secretstores/certifikáty | Ne | Ne |
> | secretstores/tajné kódy | Ne | Ne |
> | volumes | Ano | Ano |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aplikace | Ano | Ano |
> | containerGroups | Ano | Ano |
> | brány | Ano | Ano |
> | sítí | Ano | Ano |
> | záleží | Ano | Ano |
> | volumes | Ano | Ano |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Ne | Ne |
> | providerRegistrations / resourceTypeRegistrations | Ne | Ne |
> | uvádění | Ano | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | SignalR | Ano | Ano |
> | Signál/eventGridFilters | Ne | Ne |

## <a name="microsoftsingularity"></a>Microsoft. jednotného

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty/accountQuotaPolicies | Ne | Ne |
> | účty/groupPolicies | Ne | Ne |
> | účty/úlohy | Ne | Ne |
> | účty/storageContainers | Ne | Ne |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Ne | Ne |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Ano | Ano |
> | aplikace | Ano | Ano |
> | jitRequests | Ano | Ano |


## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | managedInstances | Ano | Ano |
> | managedInstances/databáze | Ne | Ne |
> | managedInstances/databáze/backupShortTermRetentionPolicies | Ne | Ne |
> | managedInstances/databáze/schémata/tabulky/sloupce/sensitivityLabels | Ne | Ne |
> | managedInstances/databáze/vulnerabilityAssessments | Ne | Ne |
> | managedInstances/databáze/vulnerabilityAssessments/Rules/směrné plány | Ne | Ne |
> | managedInstances / encryptionProtector | Ne | Ne |
> | managedInstances/klíče | Ne | Ne |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Ne | Ne |
> | managedInstances / vulnerabilityAssessments | Ne | Ne |
> | servery | Ano | Ano |
> | servery/správci | Ne | Ne |
> | servery/communicationLinks | Ne | Ne |
> | servery/databáze | Ano (viz [Poznámka níže](#sqlnote)) | Ano |
> | servery/encryptionProtector | Ne | Ne |
> | servery/firewallRules | Ne | Ne |
> | servery/klíče | Ne | Ne |
> | servery/restorableDroppedDatabases | Ne | Ne |
> | servery/serviceobjectives | Ne | Ne |
> | servery/tdeCertificates | Ne | Ne |
> | virtualClusters | Ne | Ne |

<a id="sqlnote"></a>

> [!NOTE]
> Hlavní databáze nepodporuje značky, ale další databáze, včetně databází Azure synapse Analytics, podporují značky. Databáze Azure synapse Analytics musí být ve stavu aktivní (není pozastaveno).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ano | Ano |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Ne | Ne |
> | SqlVirtualMachines | Ano | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | Ne | Ne |
> | storageAccounts | Ano | Ano |
> | storageAccounts/blobServices | Ne | Ne |
> | storageAccounts/služby | Ne | Ne |
> | storageAccounts/queueServices | Ne | Ne |
> | storageAccounts/služby | Ne | Ne |
> | storageAccounts/služby/metricDefinitions | Ne | Ne |
> | storageAccounts/tableServices | Ne | Ne |
> | použití | Ne | Ne |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | mezipaměti | Ano | Ano |
> | mezipaměti/storageTargets | Ne | Ne |
> | usageModels | Ne | Ne |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Ne | Ne |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ano | Ano |
> | storageSyncServices/registeredServer | Ne | Ne |
> | storageSyncServices / syncGroups | Ne | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne | Ne |
> | storageSyncServices/pracovní postupy | Ne | Ne |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ano | Ano |
> | storageSyncServices/registeredServer | Ne | Ne |
> | storageSyncServices / syncGroups | Ne | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne | Ne |
> | storageSyncServices/pracovní postupy | Ne | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ano | Ano |
> | storageSyncServices/registeredServer | Ne | Ne |
> | storageSyncServices / syncGroups | Ne | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne | Ne |
> | storageSyncServices/pracovní postupy | Ne | Ne |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | jednatel | Ano | Ano |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Ano | Ano |
> | clustery/privateEndpoints | Ne | Ne |
> | streamingjobs | Ano (viz poznámka níže) | Ano |

> [!NOTE]
> Značku nelze přidat, je-li spuštěna aplikace streamingjobs. Zastavením prostředku přidejte značku.

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | Ne | Ne |
> | hromad | Ne | Ne |
> | cancel | Ne | Ne |
> | changeTenantRequest | Ne | Ne |
> | changeTenantStatus | Ne | Ne |
> | CreateSubscription | Ne | Ne |
> | aby | Ne | Ne |
> | přejmenovat | Ne | Ne |
> | SubscriptionDefinitions | Ne | Ne |
> | SubscriptionOperations | Ne | Ne |
> | odběru | Ne | Ne |

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Ano | Ano |
> | pracovní prostory | Ano | Ano |
> | pracovní prostory/bigDataPools | Ano | Ano |
> | pracovní prostory/operationStatuses | Ne | Ne |
> | pracovní prostory/sqlDatabases | Ano | Ano |
> | pracovní prostory/sqlPools | Ano | Ano |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Environment | Ano | Ne |
> | prostředí/accessPolicies | Ne | Ne |
> | prostředí/EventSources | Ano | Ne |
> | prostředí/referenceDataSets | Ano | Ne |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Store | Ano | Ano |
> | obchody/accessPolicies | Ne | Ne |
> | obchody/služby | Ne | Ne |
> | úložiště/služby/tokeny | Ne | Ne |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Ano | Ano |
> | imageTemplates / runOutputs | Ne | Ne |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | ArcZones | Ano | Ano |
> | ResourcePools | Ano | Ano |
> | Servery vCenter | Ano | Ano |
> | Virtuální počítače | Ano | Ano |
> | VirtualMachineTemplates | Ano | Ano |
> | VirtualNetworks | Ano | Ano |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ano | Ano |
> | dedicatedCloudServices | Ano | Ano |
> | virtualMachines | Ano | Ano |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | zařízení | Ano | Ano |
> | registeredSubscriptions | Ne | Ne |
> | dodavateli | Ne | Ne |
> | dodavatelé/SKU | Ne | Ne |
> | dodavatelé/vnfs | Ne | Ne |
> | virtualNetworkFunctionSkus | Ne | Ne |
> | vnfs | Ano | Ano |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ne |
> | plánují | Ano | Ne |
> | registeredSubscriptions | Ne | Ne |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Ne | Ne |
> | apiManagementAccounts / apiAcls | Ne | Ne |
> | apiManagementAccounts/rozhraní API | Ne | Ne |
> | apiManagementAccounts/API/apiAcls | Ne | Ne |
> | apiManagementAccounts/API/connectionAcls | Ne | Ne |
> | apiManagementAccounts/rozhraní API/připojení | Ne | Ne |
> | apiManagementAccounts/rozhraní API/připojení/connectionAcls | Ne | Ne |
> | apiManagementAccounts/API/localizedDefinitions | Ne | Ne |
> | apiManagementAccounts / connectionAcls | Ne | Ne |
> | apiManagementAccounts/připojení | Ne | Ne |
> | billingMeters | Ne | Ne |
> | certifikáty | Ano | Ano |
> | connectionGateways | Ano | Ano |
> | připojení | Ano | Ano |
> | customApis | Ano | Ano |
> | deletedSites | Ne | Ne |
> | hostingEnvironments | Ano | Ano |
> | hostingEnvironments / eventGridFilters | Ne | Ne |
> | hostingEnvironments / multiRolePools | Ne | Ne |
> | hostingEnvironments / workerPools | Ne | Ne |
> | kubeEnvironments | Ano | Ano |
> | publishingUsers | Ne | Ne |
> | doporučit | Ne | Ne |
> | resourceHealthMetadata | Ne | Ne |
> | moduly runtime | Ne | Ne |
> | Serverových farem | Ano | Ano |
> | Serverových farem/eventGridFilters | Ne | Ne |
> | Serverových farem/firstPartyApps | Ne | Ne |
> | Serverových farem/firstPartyApps/keyVaultSettings | Ne | Ne |
> | místa | Ano | Ano |
> | lokality/konfigurace  | Ne | Ne |
> | lokality/eventGridFilters | Ne | Ne |
> | lokality/hostNameBindings | Ne | Ne |
> | lokality/networkConfig | Ne | Ne |
> | lokality/premieraddons | Ano | Ano |
> | lokality/sloty | Ano | Ano |
> | lokality/sloty/eventGridFilters | Ne | Ne |
> | lokality/sloty/hostNameBindings | Ne | Ne |
> | lokality/sloty/networkConfig | Ne | Ne |
> | sourceControls | Ne | Ne |
> | staticSites | Ano | Ano |
> | oproti | Ne | Ne |
> | verifyHostingEnvironmentVnet | Ne | Ne |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Ne | Ne |
> | diagnosticSettingsCategories | Ne | Ne |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Ano | Ano |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Ano | Ano |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | úlohy | Ano | Ano |
> | úlohy/instance | Ne | Ne |
> | zatížení a verze | Ne | Ne |
> | úlohy/verze/artefakty | Ne | Ne |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | konstrukční | Ne | Ne |
> | componentsSummary | Ne | Ne |
> | monitorInstances | Ne | Ne |
> | monitorInstancesSummary | Ne | Ne |
> | Monitor | Ne | Ne |
> | notificationSettings | Ne | No |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak používat značky pro prostředky, najdete v tématu [použití značek k uspořádání prostředků Azure](tag-resources.md).

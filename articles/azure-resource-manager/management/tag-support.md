---
title: Podpora značek pro prostředky
description: Zobrazuje, které typy prostředků Azure podporují značky. Poskytuje podrobnosti pro všechny služby Azure.
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: dd991167e703ad9faa7803bcecbbb6dc7eff94e7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91284954"
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
> | DomainServices | Yes | Yes |
> | DomainServices / oucontainer | No | No |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | supportProviders | No | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
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
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | advisorScore | No | No |
> | konfiguračních | No | No |
> | generateRecommendations | No | No |
> | zprostředkovatele identity | No | No |
> | doporučit | No | No |
> | potlačení | No | No |

## <a name="microsoftagfoodplatform"></a>Microsoft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | farmBeats | Yes | Yes |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | actionRules | Yes | Yes |
> | výstrahy | No | No |
> | alertsList | No | No |
> | alertsMetaData | No | No |
> | alertsSummary | No | No |
> | alertsSummaryList | No | No |
> | smartDetectorAlertRules | Yes | Yes |
> | smartGroups | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | servery | Yes | Yes |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | reportFeedback | No | No |
> | service | Yes | Yes |
> | validateServiceName | No | No |

> [!NOTE]
> Azure API Management podporuje jenom vytváření maximálně 15 párů název/hodnota značky pro každou službu.

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | configurationStores | Yes | Yes |
> | configurationStores / eventGridFilters | No | No |
> | configurationStores/hodnoty | No | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Spring | Yes | Yes |
> | Pružina/aplikace | No | No |
> | Jarní/aplikace/nasazení | No | No |

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Yes | Yes |
> | defaultProvider – neexistují | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accessReviewScheduleDefinitions | No | No |
> | accessReviewScheduleSettings | No | No |
> | classicAdministrators | No | No |
> | dataaliasy | No | No |
> | denyAssignments | No | No |
> | elevateAccess | No | No |
> | findOrphanRoleAssignments | No | No |
> | počtu | No | No |
> | oprávnění | No | No |
> | policyAssignments | No | No |
> | policyDefinitions | No | No |
> | policyExemptions | No | No |
> | policySetDefinitions | No | No |
> | privateLinkAssociations | No | No |
> | providerOperations | No | No |
> | resourceManagementPrivateLinks | Yes | Yes |
> | roleAssignments | No | No |
> | roleAssignmentsUsageMetrics | No | No |
> | roleDefinitions | No | No |

## <a name="microsoftautomanage"></a>Microsoft. automanage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | configurationProfileAssignments | No | No |
> | configurationProfilePreferences | Yes | Yes |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Yes | Yes |
> | automationAccounts/konfigurace | Yes | Yes |
> | automationAccounts/úlohy | No | No |
> | automationAccounts / privateEndpointConnectionProxies | No | No |
> | automationAccounts / privateEndpointConnections | No | No |
> | automationAccounts / privateLinkResources | No | No |
> | automationAccounts/Runbooky | Yes | Yes |
> | automationAccounts / softwareUpdateConfigurations | No | No |
> | automationAccounts nebo Webhooky | No | No |

> [!NOTE]
> Azure Automation podporuje pouze vytváření maximálně 15 párů název/hodnota značky pro každý prostředek automatizace.

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | privateClouds | Yes | Yes |
> | privateClouds/autorizace | No | No |
> | privateClouds/clustery | No | No |
> | privateClouds / globalReachConnections | No | No |
> | privateClouds / hcxEnterpriseSites | No | No |
> | privateClouds / workloadNetworks | No | No |
> | privateClouds / workloadNetworks / dhcpConfigurations | No | No |
> | privateClouds/workloadNetworks/Brans | No | No |
> | privateClouds / workloadNetworks / portMirroringProfiles | No | No |
> | privateClouds/workloadNetworks/segmenty | No | No |
> | privateClouds / workloadNetworks / virtualMachines | No | No |
> | privateClouds / workloadNetworks / vmGroups | No | No |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Ženeva

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Environment | No | No |
> | prostředí/účty | No | No |
> | prostředí/účty/obory názvů | No | No |
> | prostředí/účty/obory názvů/konfigurace | No | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Yes | No |
> | b2ctenants | No | No |
> | guestUsages | Yes | Yes |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | datacontrollery | Yes | Yes |
> | postgresInstances | Yes | Yes |
> | sqlManagedInstances | Yes | Yes |
> | sqlServerInstances | Yes | Yes |
> | sqlServerRegistrations | Yes | Yes |
> | sqlServerRegistrations/sqlServers | No | No |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | No | No |
> | edgeSubscriptions | Yes | Yes |
> | linkedSubscriptions | Yes | Yes |
> | rozpoznávání | Yes | Yes |
> | registrace/customerSubscriptions | No | No |
> | registrace/produkty | No | No |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Yes | Yes |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Yes | Yes |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | billingAccounts | No | No |
> | billingAccounts/smlouvy | No | No |
> | billingAccounts / billingPermissions | No | No |
> | billingAccounts / billingProfiles | No | No |
> | billingAccounts / billingProfiles / billingPermissions | No | No |
> | billingAccounts / billingProfiles / billingRoleAssignments | No | No |
> | billingAccounts / billingProfiles / billingRoleDefinitions | No | No |
> | billingAccounts / billingProfiles / billingSubscriptions | No | No |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | No | No |
> | billingAccounts/billingProfiles/zákazníci | No | No |
> | billingAccounts/billingProfiles/– pokyny | No | No |
> | billingAccounts/billingProfiles/faktury | No | No |
> | billingAccounts/billingProfiles/faktury/pricesheet | No | No |
> | billingAccounts/billingProfiles/faktury/transakce | No | No |
> | billingAccounts / billingProfiles / invoiceSections | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | No | No |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | No | No |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | No | No |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | No | No |
> | billingAccounts/billingProfiles/invoiceSections/Products | No | No |
> | billingAccounts/billingProfiles/invoiceSections/Products/Transfer | No | No |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | No | No |
> | billingAccounts/billingProfiles/invoiceSections/transakce | No | No |
> | billingAccounts/billingProfiles/invoiceSections/transfery | No | No |
> | billingAccounts / BillingProfiles / patchOperations | No | No |
> | billingAccounts / billingProfiles / paymentMethods | No | No |
> | billingAccounts/billingProfiles/– zásady | No | No |
> | billingAccounts/billingProfiles/pricesheet | No | No |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | No | No |
> | billingAccounts/billingProfiles/produkty | No | No |
> | billingAccounts/billingProfiles/rezervace | No | No |
> | billingAccounts/billingProfiles/transakcí | No | No |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | No | No |
> | billingAccounts / billingRoleAssignments | No | No |
> | billingAccounts / billingRoleDefinitions | No | No |
> | billingAccounts / billingSubscriptions | No | No |
> | billingAccounts/billingSubscriptions/faktury | No | No |
> | billingAccounts / createBillingRoleAssignment | No | No |
> | billingAccounts / createInvoiceSectionOperations | No | No |
> | billingAccounts/zákazníci | No | No |
> | billingAccounts/Customers/billingPermissions | No | No |
> | billingAccounts/Customers/billingSubscriptions | No | No |
> | billingAccounts/Customers/initiateTransfer | No | No |
> | billingAccounts/Customers/policies | No | No |
> | billingAccounts/zákazníci/produkty | No | No |
> | billingAccounts/zákazníci/transakce | No | No |
> | billingAccounts/zákazníci/přenosy | No | No |
> | billingAccounts/oddělení | No | No |
> | billingAccounts/oddělení/billingPermissions | No | No |
> | billingAccounts/oddělení/billingRoleAssignments | No | No |
> | billingAccounts/oddělení/billingRoleDefinitions | No | No |
> | billingAccounts / enrollmentAccounts | No | No |
> | billingAccounts / enrollmentAccounts / billingPermissions | No | No |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | No | No |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | No | No |
> | billingAccounts/faktury | No | No |
> | billingAccounts/faktury/transakce | No | No |
> | billingAccounts / invoiceSections | No | No |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | No | No |
> | billingAccounts / invoiceSections / billingSubscriptions | No | No |
> | billingAccounts/invoiceSections/billingSubscriptions/Transfer | No | No |
> | billingAccounts/invoiceSections/zvýšení oprávnění | No | No |
> | billingAccounts / invoiceSections / initiateTransfer | No | No |
> | billingAccounts / invoiceSections / patchOperations | No | No |
> | billingAccounts / invoiceSections / productMoveOperations | No | No |
> | billingAccounts/invoiceSections/produkty | No | No |
> | billingAccounts/invoiceSections/produkty/přenos | No | No |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | No | No |
> | billingAccounts/invoiceSections/transakcí | No | No |
> | billingAccounts/invoiceSections/transfery | No | No |
> | billingAccounts / lineOfCredit | No | No |
> | billingAccounts / patchOperations | No | No |
> | billingAccounts / paymentMethods | No | No |
> | billingAccounts/produkty | No | No |
> | billingAccounts/rezervace | No | No |
> | billingAccounts/transakce | No | No |
> | billingPeriods | No | No |
> | billingPermissions | No | No |
> | billingProperty | No | No |
> | billingRoleAssignments | No | No |
> | billingRoleDefinitions | No | No |
> | createBillingRoleAssignment | No | No |
> | oddělení | No | No |
> | enrollmentAccounts | No | No |
> | faktury | No | No |
> | Převede | No | No |
> | přenosy/acceptTransfer | No | No |
> | přenosy/declineTransfer | No | No |
> | přenosy/stav operationstatus | No | No |
> | přenosy/validateTransfer | No | No |
> | validateAddress | No | No |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | mapApis | Yes | Yes |
> | updateCommunicationPreference | No | No |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Yes | Yes |
> | cordaMembers | Yes | Yes |
> | sledovacích procesů | Yes | Yes |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | TokenServices | Yes | Yes |
> | TokenServices / BlockchainNetworks | No | No |
> | TokenServices/skupiny | No | No |
> | TokenServices/skupiny/účty | No | No |
> | TokenServices / TokenTemplates | No | No |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | No | No |
> | blueprintAssignments / assignmentOperations | No | No |
> | blueprintAssignments/operace | No | No |
> | podrobné plány | No | No |
> | modrotisky/artefakty | No | No |
> | plány/verze | No | No |
> | modrotisky/verze/artefakty | No | No |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | botServices | Yes | Yes |
> | botServices/kanály | No | No |
> | botServices/připojení | No | No |
> | jazyky | No | No |
> | šablony | No | No |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Redis | Yes | Yes |
> | Redis/EventGridFilters | No | No |
> | Redis/privateEndpointConnectionProxies | No | No |
> | Redis/privateEndpointConnectionProxies/ověřit | No | No |
> | Redis/privateEndpointConnections | No | No |
> | Redis/privateLinkResources | No | No |
> | redisEnterprise | Yes | Yes |
> | RedisEnterprise / privateEndpointConnectionProxies | No | No |
> | RedisEnterprise/privateEndpointConnectionProxies/ověřit | No | No |
> | RedisEnterprise / privateEndpointConnections | No | No |
> | RedisEnterprise / privateLinkResources | No | No |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | appliedReservations | No | No |
> | autoQuotaIncrease | No | No |
> | calculateExchange | No | No |
> | calculatePrice | No | No |
> | calculatePurchasePrice | No | No |
> | spustí | No | No |
> | commercialReservationOrders | No | No |
> | výměn | No | No |
> | ownReservations | No | No |
> | placePurchaseOrder | No | No |
> | reservationOrders | No | No |
> | reservationOrders / calculateRefund | No | No |
> | reservationOrders/sloučit | No | No |
> | reservationOrders/rezervace | No | No |
> | reservationOrders/rezervace/Revize | No | No |
> | reservationOrders/vrátit | No | No |
> | reservationOrders/Split | No | No |
> | reservationOrders/swap | No | No |
> | rezervace | No | No |
> | resourceProviders | No | No |
> | resources | No | No |
> | validateReservationOrder | No | No |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No | No |
> | CdnWebApplicationFirewallPolicies | Yes | Yes |
> | edgenodes | No | No |
> | uživatelů | Yes | Yes |
> | profily/koncové body | Yes | Yes |
> | profily/koncové body/customdomains | No | No |
> | profily/koncové body/origingroups | No | No |
> | profily/koncové body/zdroje | No | No |
> | validateProbe | No | No |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Yes | Yes |
> | certificateOrders/certifikáty | No | No |
> | validateCertificateRegistrationInformation | No | No |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | profil | No | No |
> | resourceChanges | No | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | možnosti | No | No |
> | domainNames | No | No |
> | domainNames/možnosti | No | No |
> | domainNames / internalLoadBalancers | No | No |
> | domainNames / serviceCertificates | No | No |
> | domainNames/sloty | No | No |
> | domainNames/sloty/role | No | No |
> | domainNames/sloty/role/metricDefinitions | No | No |
> | domainNames/sloty/role/metriky | No | No |
> | moveSubscriptionResources | No | No |
> | operatingSystemFamilies | No | No |
> | operatingSystems | No | No |
> | quotas | No | No |
> | Typ prostředků | No | No |
> | validateSubscriptionMoveAvailability | No | No |
> | virtualMachines | No | No |
> | virtualMachines / diagnosticSettings | No | No |
> | virtualMachines / metricDefinitions | No | No |
> | virtualMachines/metriky | No | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | No | No |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | možnosti | No | No |
> | expressRouteCrossConnections | No | No |
> | expressRouteCrossConnections/partnerské vztahy | No | No |
> | gatewaySupportedDevices | No | No |
> | networkSecurityGroups | No | No |
> | quotas | No | No |
> | reservedIps | No | No |
> | virtualNetworks | No | No |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No | No |
> | virtualNetworks/virtualNetworkPeerings | No | No |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | možnosti | No | No |
> | disků | No | No |
> | images | No | No |
> | OSImage | No | No |
> | osPlatformImages | No | No |
> | publicImages | No | No |
> | quotas | No | No |
> | storageAccounts | No | No |
> | storageAccounts/blobServices | No | No |
> | storageAccounts/služby | No | No |
> | storageAccounts/metricDefinitions | No | No |
> | storageAccounts/metriky | No | No |
> | storageAccounts/queueServices | No | No |
> | storageAccounts/služby | No | No |
> | storageAccounts/služby/diagnosticSettings | No | No |
> | storageAccounts/služby/metricDefinitions | No | No |
> | storageAccounts/služby/metriky | No | No |
> | storageAccounts/tableServices | No | No |
> | storageAccounts/vmImages | No | No |
> | vmImages | No | No |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | plánují | Yes | Yes |
> | registeredSubscriptions | No | No |

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | účty/privateEndpointConnectionProxies | No | No |
> | účty/privateEndpointConnections | No | No |
> | účty/privateLinkResources | No | No |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | RateCard | No | No |
> | UsageAggregates | No | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Yes | Yes |
> | cloudServices | Yes | Yes |
> | cloudServices / networkInterfaces | No | No |
> | cloudServices/publicIPAddresses | No | No |
> | cloudServices / roleInstances | No | No |
> | cloudServices / roleInstances / networkInterfaces | No | No |
> | cloudServices/role | No | No |
> | diskAccesses | Yes | Yes |
> | diskEncryptionSets | Yes | Yes |
> | disků | Yes | Yes |
> | Galerie | Yes | Yes |
> | Galerie/aplikace | No | No |
> | Galerie/aplikace/verze | No | No |
> | Galerie/image | No | No |
> | Galerie/image/verze | No | No |
> | hostGroups | Yes | Yes |
> | hostGroups/hostitelé | Yes | Yes |
> | images | Yes | Yes |
> | proximityPlacementGroups | Yes | Yes |
> | restorePointCollections | Yes | Yes |
> | restorePointCollections / restorePoints | No | No |
> | sharedVMExtensions | Yes | Yes |
> | sharedVMExtensions/verze | No | No |
> | sharedVMImages | Yes | Yes |
> | sharedVMImages/verze | No | No |
> | snímky | Yes | Yes |
> | sshPublicKeys | Yes | Yes |
> | virtualMachines | Yes | Yes |
> | virtualMachines/rozšíření | Yes | Yes |
> | virtualMachines / metricDefinitions | No | No |
> | virtualMachines / runCommands | Yes | Yes |
> | virtualMachineScaleSets | Yes | Yes |
> | virtualMachineScaleSets/rozšíření | No | No |
> | virtualMachineScaleSets/networkInterfaces | No | No |
> | virtualMachineScaleSets/publicIPAddresses | Yes | No |
> | virtualMachineScaleSets/virtualMachines | No | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No | No |

> [!NOTE]
> Nelze přidat značku k virtuálnímu počítači, který byl označen jako zobecněný. Virtuální počítač označíte jako zobecněný pomocí [set-AzVm-](/powershell/module/Az.Compute/Set-AzVM) [Generalized nebo AZ VM generalize](/cli/azure/vm#az-vm-generalize).

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | CacheNodes | Yes | Yes |

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | No | No |
> | Zůstatky | No | No |
> | Rozpočty | No | No |
> | Poplatky | No | No |
> | CostTags | No | No |
> | dobropis | No | No |
> | stránka events | No | No |
> | Prognózy | No | No |
> | ŠARŽ | No | No |
> | Tržišť | No | No |
> | Ceníky | No | No |
> | produktech | No | No |
> | ReservationDetails | No | No |
> | ReservationRecommendationDetails | No | No |
> | ReservationRecommendations | No | No |
> | ReservationSummaries | No | No |
> | ReservationTransactions | No | No |
> | Značky | No | No |
> | tenantů | No | No |
> | Terminologie | No | No |
> | UsageDetails (Podrobnosti o využití) | No | No |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | containerGroups | Yes | Yes |
> | serviceAssociationLinks | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | registr | Yes | Yes |
> | Registry/agentPools | Yes | Yes |
> | Registry a sestavení | No | No |
> | Registry/buildy/zrušit | No | No |
> | Registry/buildy/getLogLink | No | No |
> | Registry/buildTasks | Yes | Yes |
> | Registry/buildTasks/kroky | No | No |
> | Registry/eventGridFilters | No | No |
> | Registry/exportPipelines | No | No |
> | Registry/generateCredentials | No | No |
> | Registry/getBuildSourceUploadUrl | No | No |
> | Registry/getpřihlašovací údaje | No | No |
> | Registry/importImage | No | No |
> | Registry/importPipelines | No | No |
> | Registry/pipelineRuns | No | No |
> | Registry/privateEndpointConnectionProxies | No | No |
> | Registry/privateEndpointConnectionProxies/ověřit | No | No |
> | Registry/privateEndpointConnections | No | No |
> | Registry/privateLinkResources | No | No |
> | Registry/queueBuild | No | No |
> | Registry/regenerateCredential | No | No |
> | Registry/regenerateCredentials | No | No |
> | Registry/replikace | Yes | Yes |
> | Registry/běhy | No | No |
> | Registry/spuštění/zrušit | No | No |
> | Registry/scheduleRun | No | No |
> | Registry/scopeMaps | No | No |
> | Registry/taskRuns | No | No |
> | Registry/úlohy | Yes | Yes |
> | Registry a tokeny | No | No |
> | Registry/updatePolicies | No | No |
> | Registry a Webhooky | Yes | Yes |
> | Registry/Webhooky/getCallbackConfig | No | No |
> | Registry/Webhooky/příkazy k odeslání | No | No |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | containerServices | Yes | Yes |
> | managedClusters | Yes | Yes |
> | openShiftManagedClusters | Yes | Yes |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Upozornění | No | No |
> | BillingAccounts | No | No |
> | Rozpočty | No | No |
> | CloudConnectors | No | No |
> | Konektory | Yes | Yes |
> | costAllocationRules | No | No |
> | Oddělení | No | No |
> | Dimenze | No | No |
> | EnrollmentAccounts | No | No |
> | Vývozních | No | No |
> | ExternalBillingAccounts | No | No |
> | ExternalBillingAccounts/výstrahy | No | No |
> | ExternalBillingAccounts/dimenzí | No | No |
> | ExternalBillingAccounts/prognóza | No | No |
> | ExternalBillingAccounts/dotaz | No | No |
> | ExternalSubscriptions | No | No |
> | ExternalSubscriptions/výstrahy | No | No |
> | ExternalSubscriptions/dimenzí | No | No |
> | ExternalSubscriptions/prognóza | No | No |
> | ExternalSubscriptions/dotaz | No | No |
> | Prognóza | No | No |
> | Insights | No | No |
> | Dotazy | No | No |
> | register | No | No |
> | Reportconfigs | No | No |
> | Sestavy | No | No |
> | Nastavení | No | No |
> | showbackRules | No | No |
> | Zobrazení | No | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | požádal | No | No |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | přidružení | No | No |
> | resourceProviders | Yes | Yes |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | instance | Yes | Yes |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | úlohy | Yes | Yes |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Yes | Yes |

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | pracovní prostory | Yes | Yes |
> | pracovní prostory/dbWorkspaces | No | No |
> | pracovní prostory/storageEncryption | No | No |
> | pracovní prostory/virtualNetworkPeerings | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | spustí | Yes | Yes |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Objekty DataFactory | Yes | Yes |
> | DataFactory/diagnosticSettings | No | No |
> | DataFactory/metricDefinitions | No | No |
> | dataFactorySchema | No | No |
> | továrny | Yes | Yes |
> | továrny/integrationRuntimes | No | No |

> [!NOTE]
> Pokud máte v datové továrně prostředí Azure-SSIS Integration runtime, budou se jejich provozní náklady označit pomocí značek Data Factory. Spuštění prostředí Azure-SSIS Integration runtime se musí zastavit a restartovat, aby se nové značky služby Data Factory uplatnily na jejich provozní náklady.

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | účty/dataLakeStoreAccounts | No | No |
> | účty/storageAccounts | No | No |
> | účty/storageAccounts/kontejnery | No | No |
> | účty/transferAnalyticsUnits | No | No |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | účty/eventGridFilters | No | No |
> | účty/firewallRules | No | No |

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | services | No | No |
> | služby a projekty | No | No |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | BackupVaults | Yes | Yes |
> | ResourceOperationGateKeepers | Yes | Yes |

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | účty/sdílené složky | No | No |
> | účty/akcie/datové sady | No | No |
> | účty/akcie/pozvánky | No | No |
> | účty/akcie/providersharesubscriptions | No | No |
> | účty/akcie/synchronizationSettings | No | No |
> | účty/sharesubscriptions | No | No |
> | účty/sharesubscriptions/consumerSourceDataSets | No | No |
> | účty/sharesubscriptions/datasetmappings | No | No |
> | účty/sharesubscriptions/triggery | No | No |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | servery | Yes | Yes |
> | servery/poradci | No | No |
> | servery/klíče | No | No |
> | servery/privateEndpointConnectionProxies | No | No |
> | servery/privateEndpointConnections | No | No |
> | servery/privateLinkResources | No | No |
> | servery/queryTexts | No | No |
> | servery/recoverableServers | No | No |
> | servery/spustit | No | No |
> | servery/zastavit | No | No |
> | servery/topQueryStatistics | No | No |
> | servery/virtualNetworkRules | No | No |
> | servery/waitStatistics | No | No |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Yes | Yes |
> | servery | Yes | Yes |
> | servery/poradci | No | No |
> | servery/klíče | No | No |
> | servery/privateEndpointConnectionProxies | No | No |
> | servery/privateEndpointConnections | No | No |
> | servery/privateLinkResources | No | No |
> | servery/queryTexts | No | No |
> | servery/recoverableServers | No | No |
> | servery/spustit | No | No |
> | servery/zastavit | No | No |
> | servery/topQueryStatistics | No | No |
> | servery/upgrade | No | No |
> | servery/virtualNetworkRules | No | No |
> | servery/waitStatistics | No | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | flexibleServers | Yes | Yes |
> | serverGroups | Yes | Yes |
> | servery | Yes | Yes |
> | servery/poradci | No | No |
> | servery/klíče | No | No |
> | servery/privateEndpointConnectionProxies | No | No |
> | servery/privateEndpointConnections | No | No |
> | servery/privateLinkResources | No | No |
> | servery/queryTexts | No | No |
> | servery/recoverableServers | No | No |
> | servery/topQueryStatistics | No | No |
> | servery/virtualNetworkRules | No | No |
> | servery/waitStatistics | No | No |
> | serversv2 | Yes | Yes |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | artifactSources | Yes | Yes |
> | uvádění | Yes | Yes |
> | serviceTopologies | Yes | Yes |
> | serviceTopologies/služby | Yes | Yes |
> | serviceTopologies/služby/serviceUnits | Yes | Yes |
> | kroky | Yes | Yes |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Yes | Yes |
> | applicationgroups/aplikace | No | No |
> | applicationgroups/desktopy | No | No |
> | applicationgroups / startmenuitems | No | No |
> | hostpools | Yes | Yes |
> | hostpools / sessionhosts | No | No |
> | hostpools / sessionhosts / usersessions | No | No |
> | hostpools / usersessions | No | No |
> | pracovní prostory | Yes | Yes |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Yes | Yes |
> | ElasticPools / IotHubTenants | Yes | Yes |
> | ElasticPools/IotHubTenants/securitySettings | No | No |
> | IotHubs | Yes | Yes |
> | IotHubs/eventGridFilters | No | No |
> | IotHubs/securitySettings | No | No |
> | ProvisioningServices | Yes | Yes |
> | použití | No | No |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | účty/instance | Yes | Yes |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | kanály | Yes | Yes |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | kontrolery | Yes | Yes |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | labcenters | Yes | Yes |
> | Labs | Yes | Yes |
> | laboratoře/prostředí | Yes | Yes |
> | Labs/serviceRunners | Yes | Yes |
> | Labs/virtualMachines | Yes | Yes |
> | časových | Yes | Yes |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | digitalTwinsInstances | Yes | Yes |
> | digitalTwinsInstances/koncové body | No | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | No | No |
> | databaseAccounts | Yes | Yes |
> | restorableDatabaseAccounts | No | No |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | doménu | Yes | Yes |
> | domény/domainOwnershipIdentifiers | No | No |
> | generateSsoRequest | No | No |
> | topLevelDomains | No | No |
> | validateDomainRegistrationInformation | No | No |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | lcsprojects | No | No |
> | lcsprojects / clouddeployments | No | No |
> | lcsprojects/konektory | No | No |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | services | Yes | Yes |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | doménu | Yes | Yes |
> | domény a témata | No | No |
> | eventSubscriptions | No | No |
> | extensionTopics | No | No |
> | partnerNamespaces | Yes | Yes |
> | partnerNamespaces/eventChannels | No | No |
> | partnerRegistrations | Yes | Yes |
> | partnerTopics | Yes | Yes |
> | partnerTopics / eventSubscriptions | No | No |
> | systemTopics | Yes | Yes |
> | systemTopics / eventSubscriptions | No | No |
> | popisující | Yes | Yes |
> | topicTypes | No | No |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Yes | Yes |
> | obsažené | Yes | Yes |
> | obory názvů/autorizačních pravidel | No | No |
> | obory názvů/disasterrecoveryconfigs | No | No |
> | obory názvů/eventhubs | No | No |
> | obory názvů/eventhubs/autorizačních pravidel | No | No |
> | obory názvů/eventhubs/consumergroups | No | No |
> | obory názvů/networkrulesets | No | No |
> | obory názvů/privateEndpointConnections | No | No |

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | experimentWorkspaces | Yes | Yes |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | obsažené | Yes | Yes |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | featureProviders | No | No |
> | funkce | No | No |
> | dodavateli | No | No |
> | subscriptionFeatureRegistrations | No | No |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | registrované | No | No |
> | galleryitems | No | No |
> | generateartifactaccessuri | No | No |
> | myareas | No | No |
> | myareas/oblasti | No | No |
> | myareas/oblasti/oblasti | No | No |
> | myareas/oblasti/oblasti/galleryitems | No | No |
> | myareas/oblasti/galleryitems | No | No |
> | myareas / galleryitems | No | No |
> | register | No | No |
> | resources | No | No |
> | retrieveresourcesbyid | No | No |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Yes | Yes |
> | autoManagedVmConfigurationProfiles | Yes | Yes |
> | configurationProfileAssignments | No | No |
> | guestConfigurationAssignments | No | No |
> | Vybavení | No | No |
> | softwareUpdateProfile | No | No |
> | softwareUpdates | No | No |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Yes | Yes |
> | sapMonitors | Yes | Yes |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Yes | Yes |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Yes | Yes |
> | clustery/aplikace | No | No |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | services | Yes | Yes |
> | služby/iomtconnectors | No | No |
> | služby/iomtconnectors/připojení | No | No |
> | služby/iomtconnectors/mapování | No | No |
> | služby/privateEndpointConnectionProxies | No | No |
> | služby/privateEndpointConnections | No | No |
> | služby/privateLinkResources | No | No |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | virtuální | Yes | Yes |
> | počítače/assessPatches | No | No |
> | počítače/rozšíření | Yes | Yes |
> | počítače/installPatches | No | No |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | datamanagery | Yes | Yes |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | zařízení | Yes | Yes |
> | registeredSubscriptions | No | No |
> | dodavateli | No | No |
> | dodavatelé/vendorskus | No | No |
> | dodavatelé/vendorskus/previewSubscriptions | No | No |
> | virtualnetworkfunctions | Yes | Yes |
> | virtualnetworkfunctionvendors | No | No |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | konstrukční | Yes | Yes |
> | networkScopes | Yes | Yes |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | úlohy | Yes | Yes |

## <a name="microsoftinsights"></a>Microsoft. Insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | actionGroups | Yes | Yes |
> | Upozorněníprotokoluaktivit | Yes | Yes |
> | alertrules | Yes | Yes |
> | autoscalesettings | Yes | Yes |
> | konstrukční | Yes | Yes |
> | komponenty/linkedStorageAccounts | No | No |
> | komponenty/ProactiveDetectionConfigs | No | No |
> | diagnosticSettings | No | No |
> | guestDiagnosticSettings | Yes | Yes |
> | guestDiagnosticSettingsAssociation | Yes | Yes |
> | logprofiles | Yes | Yes |
> | metricAlerts | Yes | Yes |
> | privateLinkScopes | Yes | Yes |
> | privateLinkScopes / privateEndpointConnections | No | No |
> | privateLinkScopes / scopedResources | No | No |
> | queryPacks | Yes | Yes |
> | queryPacks/dotazy | No | No |
> | scheduledQueryRules | Yes | Yes |
> | webové testy | Yes | Yes |
> | sešity | Yes | Yes |
> | workbooktemplates | Yes | Yes |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | appTemplates | No | No |
> | IoTApps | Yes | Yes |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Graph | Yes | Yes |

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | deletedVaults | No | No |
> | hsmPools | Yes | Yes |
> | managedHSMs | Yes | Yes |
> | trezory | Yes | Yes |
> | trezory/accessPolicies | No | No |
> | trezory/eventGridFilters | No | No |
> | trezory a klíče | No | No |
> | trezory/klíče/verze | No | No |
> | trezory/tajné klíče | No | No |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Yes | Yes |
> | registeredSubscriptions | No | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | SND | No | No |
> | sourceControlConfigurations | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Yes | Yes |
> | clustery/attacheddatabaseconfigurations | No | No |
> | clustery/databáze | No | No |
> | clustery, databáze/datapřipojení | No | No |
> | clustery/databáze/eventhubconnections | No | No |
> | clustery/databáze/principalassignments | No | No |
> | clustery/datapřipojení | No | No |
> | clustery/principalassignments | No | No |
> | clustery/sharedidentities | No | No |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | labaccounts | Yes | No |
> | uživatelé | No | No |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Yes | Yes |
> | integrationAccounts | Yes | Yes |
> | integrationServiceEnvironments | Yes | Yes |
> | integrationServiceEnvironments/Inspirujte | No | No |
> | isolatedEnvironments | Yes | Yes |
> | Zpracovávaný | Yes | Yes |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Yes | Yes |
> | webServices | Yes | Yes |
> | Pracovní prostory | Yes | Yes |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | pracovní prostory | Yes | Yes |
> | pracovní prostory/codeJobs | No | No |
> | pracovní prostory a výpočetní prostředky | No | No |
> | pracovní prostory/eventGridFilters | No | No |
> | pracovní prostory/inferenceEndpoints | Yes | Yes |
> | pracovní prostory/inferenceEndpointsy/nasazení | Yes | Yes |
> | pracovní prostory/linkedServices | No | No |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applyUpdates | No | No |
> | configurationAssignments | No | No |
> | maintenanceConfigurations | Yes | Yes |
> | publicMaintenanceConfigurations | No | No |
> | aktualizovány | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Identity | No | No |
> | userAssignedIdentities | Yes | Yes |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | managedNetworks | Yes | Yes |
> | managedNetworks / managedNetworkGroups | Yes | Yes |
> | managedNetworks / managedNetworkPeeringPolicies | Yes | Yes |
> | oznámení | Yes | Yes |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | No | No |
> | registrationAssignments | No | No |
> | registrationDefinitions | No | No |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | getentities | No | No |
> | managementGroups | No | No |
> | managementGroups/nastavení | No | No |
> | resources | No | No |
> | startTenantBackfill | No | No |
> | tenantBackfillStatus | No | No |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | účty/eventGridFilters | No | No |
> | účty/privateAtlases | Yes | Yes |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | macc | No | No |
> | Rozsah | No | No |
> | offerTypes | No | No |
> | offerTypes/vydavatelé | No | No |
> | offerTypes/vydavatelé/nabídky | No | No |
> | offerTypes/vydavatelé/nabídky/plány | No | No |
> | offerTypes/vydavatelé/nabídky/plány/smlouvy | No | No |
> | offerTypes/vydavatelé/nabídky/plány/konfigurace | No | No |
> | offerTypes/vydavatelé/nabídky/plány/konfigurace/importImage | No | No |
> | privategalleryitems | No | No |
> | privateStoreClient | No | No |
> | privateStores | No | No |
> | privateStores/nabídky | No | No |
> | produktech | No | No |
> | zdrojů | No | No |
> | Vydavatelé/nabídky | No | No |
> | Vydavatelé/nabídky/změny | No | No |
> | register | No | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Yes | Yes |
> | updateCommunicationPreference | No | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | o | No | No |
> | offertypes | No | No |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | MediaServices | Yes | Yes |
> | MediaServices/accountFilters | No | No |
> | MediaServices/assety | No | No |
> | MediaServices/assety/assetFilters | No | No |
> | MediaServices/contentKeyPolicies | No | No |
> | MediaServices/eventGridFilters | No | No |
> | MediaServices/liveEventOperations | No | No |
> | MediaServices/liveEvents | Yes | Yes |
> | MediaServices/liveEvents/liveOutputs | No | No |
> | MediaServices/liveOutputOperations | No | No |
> | MediaServices/mediaGraphs | No | No |
> | MediaServices/privateEndpointConnectionOperations | No | No |
> | MediaServices/privateEndpointConnectionProxies | No | No |
> | MediaServices/privateEndpointConnections | No | No |
> | MediaServices/streamingEndpointOperations | No | No |
> | MediaServices/starají | Yes | Yes |
> | MediaServices/streamingLocators | No | No |
> | MediaServices/streamingPolicies | No | No |
> | MediaServices/transformace | No | No |
> | MediaServices/transformace/úlohy | No | No |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | appClusters | Yes | Yes |

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Yes | Yes |
> | migrateprojects | Yes | Yes |
> | moveCollections | Yes | Yes |
> | projekty | Yes | Yes |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Yes | Yes |
> | objectUnderstandingAccounts | Yes | Yes |
> | remoteRenderingAccounts | Yes | Yes |
> | spatialAnchorsAccounts | Yes | Yes |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Yes | No |
> | netAppAccounts / accountBackups | No | No |
> | netAppAccounts / capacityPools | Yes | No |
> | netAppAccounts/capacityPools/svazky | Yes | No |
> | netAppAccounts/capacityPools/svazky/snímky | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Yes | Yes |
> | applicationGatewayWebApplicationFirewallPolicies | Yes | Yes |
> | applicationSecurityGroups | Yes | Yes |
> | azureFirewallFqdnTags | No | No |
> | azureFirewalls | Yes | No |
> | bastionHosts | Yes | No |
> | bgpServiceCommunities | No | No |
> | připojení | Yes | Yes |
> | ddosCustomPolicies | Yes | Yes |
> | ddosProtectionPlans | Yes | Yes |
> | dnsOperationStatuses | No | No |
> | dnszones | Yes | Yes |
> | dnszones/A | No | No |
> | dnszones/AAAA | No | No |
> | dnszones/vše | No | No |
> | dnszones/CAA | No | No |
> | dnszones/CNAME | No | No |
> | dnszones/MX | No | No |
> | dnszones/NS | No | No |
> | dnszones/PTR | No | No |
> | dnszones/sady záznamů | No | No |
> | dnszones/SOA | No | No |
> | dnszones/SRV | No | No |
> | dnszones/TXT | No | No |
> | expressRouteCircuits | Yes | Yes |
> | expressRouteCrossConnections | Yes | Yes |
> | expressRouteGateways | Yes | Yes |
> | expressRoutePorts | Yes | Yes |
> | expressRouteServiceProviders | No | No |
> | firewallPolicies | Yes | Yes |
> | frontdoors | Ano, ale omezeno (viz [Poznámka níže](#frontdoor)) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ano, ale omezeno (viz [Poznámka níže](#frontdoor)) | No |
> | frontdoorWebApplicationFirewallPolicies | Ano, ale omezeno (viz [Poznámka níže](#frontdoor)) | Yes |
> | getDnsResourceReference | No | No |
> | internalNotify | No | No |
> | ipGroups | Yes | Yes |
> | loadBalancers | Yes | Yes |
> | localNetworkGateways | Yes | Yes |
> | natGateways | Yes | Yes |
> | networkIntentPolicies | Yes | Yes |
> | networkInterfaces | Yes | Yes |
> | networkProfiles | Yes | Yes |
> | networkSecurityGroups | Yes | Yes |
> | networkWatchers | Yes | Yes |
> | networkWatchers / connectionMonitors | Yes | No |
> | networkWatchers / flowLogs | No | No |
> | networkWatchers/čočky | Yes | No |
> | networkWatchers / pingMeshes | Yes | No |
> | p2sVpnGateways | Yes | Yes |
> | privateDnsOperationStatuses | No | No |
> | privateDnsZones | Yes | Yes |
> | privateDnsZones/A | No | No |
> | privateDnsZones/AAAA | No | No |
> | privateDnsZones/vše | No | No |
> | privateDnsZones/CNAME | No | No |
> | privateDnsZones/MX | No | No |
> | privateDnsZones/PTR | No | No |
> | privateDnsZones/SOA | No | No |
> | privateDnsZones/SRV | No | No |
> | privateDnsZones/TXT | No | No |
> | privateDnsZones / virtualNetworkLinks | Yes | Yes |
> | privateEndpoints | Yes | Yes |
> | privateLinkServices | Yes | Yes |
> | publicIPAddresses | Yes | Yes |
> | publicIPPrefixes | Yes | Yes |
> | routeFilters | Yes | Yes |
> | routeTables | Yes | Yes |
> | serviceEndpointPolicies | Yes | Yes |
> | trafficManagerGeographicHierarchies | No | No |
> | trafficmanagerprofiles | Yes | Yes |
> | trafficmanagerprofiles/Heat mapy | No | No |
> | trafficManagerUserMetricsKeys | No | No |
> | virtualHubs | Yes | Yes |
> | virtualNetworkGateways | Yes | Yes |
> | virtualNetworks | Yes | Yes |
> | virtualNetworks/podsítě | No | No |
> | virtualNetworkTaps | Yes | Yes |
> | virtualWans | Yes | No |
> | vpnGateways | Yes | Yes |
> | vpnSites | Yes | Yes |
> | webApplicationFirewallPolicies | Yes | Yes |

<a id="frontdoor"></a>

> [!NOTE]
> U služby front-dveří pro Azure můžete při vytváření prostředku použít značky, ale aktualizace nebo přidávání značek se momentálně nepodporuje.


## <a name="microsoftnotebooks"></a>Microsoft. poznámkové bloky

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | NotebookProxies | No | No |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | obsažené | Yes | No |
> | obory názvů/notificationHubs | Yes | No |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Yes | Yes |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Yes | Yes |
> | ImportSites | Yes | Yes |
> | MasterSites | Yes | Yes |
> | ServerSites | Yes | Yes |
> | VMwareSites | Yes | Yes |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Yes | Yes |
> | deletedWorkspaces | No | No |
> | linkTargets | No | No |
> | storageInsightConfigs | No | No |
> | pracovní prostory | Yes | Yes |
> | pracovní prostory/dataexporty | No | No |
> | pracovní prostory/zdroje dat | No | No |
> | pracovní prostory/linkedServices | No | No |
> | pracovní prostory/linkedStorageAccounts | No | No |
> | pracovní prostory/metadata | No | No |
> | pracovní prostory a dotazy | No | No |
> | pracovní prostory/scopedPrivateLinkProxies | No | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | managementassociations | No | No |
> | managementconfigurations | Yes | Yes |
> | Řešení | Yes | Yes |
> | zobrazení | Yes | Yes |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | No | No |
> | peerAsns | No | No |
> | partnerské vztahy | Yes | Yes |
> | peeringServiceCountries | No | No |
> | peeringServiceProviders | No | No |
> | peeringServices | Yes | Yes |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | atestace | No | No |
> | policyEvents | No | No |
> | policyMetadata | No | No |
> | policyStates | No | No |
> | policyTrackedResources | No | No |
> | nápravy | No | No |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Konzoly nástroje | No | No |
> | řídicí panely | Yes | Yes |
> | userSettings | No | No |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Yes | Yes |
> | tenantů | Yes | Yes |
> | klienti/pracovní prostory | No | No |
> | workspaceCollections | Yes | Yes |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | schopností | Yes | Yes |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | deletedAccounts | No | No |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | No | No |
> | providerRegistrations / defaultRollouts | No | No |
> | providerRegistrations / resourceTypeRegistrations | No | No |
> | uvádění | Yes | Yes |

## <a name="microsoftquantum"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Pracovní prostory | Yes | Yes |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | No | No |
> | trezory | Yes | Yes |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | OpenShiftClusters | Yes | Yes |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | obsažené | Yes | Yes |
> | obory názvů/autorizačních pravidel | No | No |
> | obory názvů/hybridconnections | No | No |
> | obory názvů/hybridconnections/autorizačních pravidel | No | No |
> | obory názvů/privateEndpointConnections | No | No |
> | obory názvů/wcfrelays | No | No |
> | obory názvů/wcfrelays/autorizačních pravidel | No | No |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | odešle | Yes | Yes |
> | resourceChangeDetails | No | No |
> | resourceChanges | No | No |
> | resources | No | No |
> | resourcesHistory | No | No |
> | subscriptionsStatus | No | No |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | No | No |
> | childAvailabilityStatuses | No | No |
> | childResources | No | No |
> | emergingissues | No | No |
> | stránka events | No | No |
> | impactedResources | No | No |
> | zprostředkovatele identity | No | No |
> | připomenutí | No | No |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | calculateTemplateHash | No | No |
> | nasazení | Yes | No |
> | nasazení/operace | No | No |
> | deploymentScripts | Yes | Yes |
> | deploymentScripts/protokoly | No | No |
> | odkazy | No | No |
> | notifyResourceJobs | No | No |
> | dodavateli | No | No |
> | resourceGroups | Yes | No |
> | odběru | Yes | No |
> | templateSpecs | Yes | Yes |
> | templateSpecs/verze | Yes | Yes |
> | tenantů | No | No |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aplikace | Yes | Yes |
> | saasresources | No | No |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Cloud | Yes | Yes |
> | VirtualMachines | Yes | Yes |
> | VirtualMachineTemplates | Yes | Yes |
> | VirtualNetworks | Yes | Yes |
> | vmmservers | Yes | Yes |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | No | No |
> | searchServices | Yes | Yes |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | No | No |
> | advancedThreatProtectionSettings | No | No |
> | výstrahy | No | No |
> | alertsSuppressionRules | No | No |
> | allowedConnections | No | No |
> | applicationWhitelistings | No | No |
> | assessmentMetadata | No | No |
> | hodnocení | No | No |
> | autoDismissAlertsRules | No | No |
> | automatizace | Yes | Yes |
> | AutoProvisioningSettings | No | No |
> | Předpisů | No | No |
> | konektory | No | No |
> | dataCollectionAgents | No | No |
> | deviceSecurityGroups | No | No |
> | discoveredSecuritySolutions | No | No |
> | externalSecuritySolutions | No | No |
> | InformationProtectionPolicies | No | No |
> | iotDefenderSettings | No | No |
> | iotSecuritySolutions | Yes | Yes |
> | iotSecuritySolutions / analyticsModels | No | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No | No |
> | iotSecuritySolutions / iotAlerts | No | No |
> | iotSecuritySolutions / iotAlertTypes | No | No |
> | iotSecuritySolutions / iotRecommendations | No | No |
> | iotSecuritySolutions / iotRecommendationTypes | No | No |
> | iotSensors | No | No |
> | jitNetworkAccessPolicies | No | No |
> | jitPolicies | No | No |
> | Zásady | No | No |
> | ceny | No | No |
> | regulatoryComplianceStandards | No | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls | No | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | No | No |
> | secureScoreControlDefinitions | No | No |
> | secureScoreControls | No | No |
> | secureScores | No | No |
> | secureScores / secureScoreControls | No | No |
> | securityContacts | No | No |
> | securitySolutions | No | No |
> | securitySolutionsReferenceData | No | No |
> | securityStatuses | No | No |
> | securityStatusesSummaries | No | No |
> | serverVulnerabilityAssessments | No | No |
> | nastavení | No | No |
> | sqlVulnerabilityAssessments | No | No |
> | podhodnocení | No | No |
> | úlohy | No | No |
> | topologie | No | No |
> | workspaceSettings | No | No |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | agregace | No | No |
> | alertRules | No | No |
> | alertRuleTemplates | No | No |
> | automationRules | No | No |
> | záložky | No | No |
> | věcech | No | No |
> | dataconnects | No | No |
> | dataConnectorsCheckRequirements | No | No |
> | podnikům | No | No |
> | entityQueries | No | No |
> | Incidenty | No | No |
> | officeConsents | No | No |
> | nastavení | No | No |
> | threatIntelligence | No | No |
> | watchlists | No | No |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | consoleServices | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | obsažené | Yes | Yes |
> | obory názvů/autorizačních pravidel | No | No |
> | obory názvů/disasterrecoveryconfigs | No | No |
> | obory názvů/eventgridfilters | No | No |
> | obory názvů/networkrulesets | No | No |
> | obory názvů/privateEndpointConnections | No | No |
> | obory názvů/fronty | No | No |
> | obory názvů/fronty/autorizačních pravidel | No | No |
> | obory názvů/témata | No | No |
> | obory názvů/témata/autorizačních pravidel | No | No |
> | obory názvů/témata/předplatná | No | No |
> | obory názvů/témata/předplatná/pravidla | No | No |
> | premiumMessagingRegions | No | No |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aplikace | Yes | Yes |
> | existující | Yes | Yes |
> | clustery/aplikace | No | No |
> | containerGroups | Yes | Yes |
> | containerGroupSets | Yes | Yes |
> | edgeclusters | Yes | Yes |
> | edgeclusters/aplikace | No | No |
> | managedclusters | Yes | Yes |
> | managedclusters/nodetypes | No | No |
> | sítí | Yes | Yes |
> | secretstores | Yes | Yes |
> | secretstores/certifikáty | No | No |
> | secretstores/tajné kódy | No | No |
> | volumes | Yes | Yes |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aplikace | Yes | Yes |
> | containerGroups | Yes | Yes |
> | brány | Yes | Yes |
> | sítí | Yes | Yes |
> | záleží | Yes | Yes |
> | volumes | Yes | Yes |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | No | No |
> | providerRegistrations / resourceTypeRegistrations | No | No |
> | uvádění | Yes | Yes |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | SignalR | Yes | Yes |
> | Signál/eventGridFilters | No | No |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | No | No |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Yes | Yes |
> | aplikace | Yes | Yes |
> | jitRequests | Yes | Yes |


## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | managedInstances | Yes | Yes |
> | managedInstances/databáze | Ano (viz [Poznámka níže](#sqlnote)) | Yes |
> | managedInstances/databáze/backupShortTermRetentionPolicies | No | No |
> | managedInstances/databáze/schémata/tabulky/sloupce/sensitivityLabels | No | No |
> | managedInstances/databáze/vulnerabilityAssessments | No | No |
> | managedInstances/databáze/vulnerabilityAssessments/Rules/směrné plány | No | No |
> | managedInstances / encryptionProtector | No | No |
> | managedInstances/klíče | No | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No | No |
> | managedInstances / vulnerabilityAssessments | No | No |
> | servery | Yes | Yes |
> | servery/správci | No | No |
> | servery/communicationLinks | No | No |
> | servery/databáze | Ano (viz [Poznámka níže](#sqlnote)) | Yes |
> | servery/encryptionProtector | No | No |
> | servery/firewallRules | No | No |
> | servery/klíče | No | No |
> | servery/restorableDroppedDatabases | No | No |
> | servery/serviceobjectives | No | No |
> | servery/tdeCertificates | No | No |
> | virtualClusters | No | No |

<a id="sqlnote"></a>

> [!NOTE]
> Hlavní databáze nepodporuje značky, ale další databáze, včetně databází Azure synapse Analytics, podporují značky. Databáze Azure synapse Analytics musí být ve stavu aktivní (není pozastaveno).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Yes | Yes |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No | No |
> | SqlVirtualMachines | Yes | Yes |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | deletedAccounts | No | No |
> | storageAccounts | Yes | Yes |
> | storageAccounts/blobServices | No | No |
> | storageAccounts/služby | No | No |
> | storageAccounts/queueServices | No | No |
> | storageAccounts/služby | No | No |
> | storageAccounts/služby/metricDefinitions | No | No |
> | storageAccounts/tableServices | No | No |
> | použití | No | No |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | mezipaměti | Yes | Yes |
> | mezipaměti/storageTargets | No | No |
> | usageModels | No | No |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | replicationGroups | No | No |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | storageSyncServices/registeredServer | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices/pracovní postupy | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | storageSyncServices/registeredServer | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices/pracovní postupy | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Yes | Yes |
> | storageSyncServices/registeredServer | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices/pracovní postupy | No | No |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | jednatel | Yes | Yes |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Yes | Yes |
> | streamingjobs | Ano (viz poznámka níže) | Yes |

> [!NOTE]
> Značku nelze přidat, je-li spuštěna aplikace streamingjobs. Zastavením prostředku přidejte značku.

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | acceptChangeTenant | No | No |
> | hromad | No | No |
> | cancel | No | No |
> | changeTenantRequest | No | No |
> | changeTenantStatus | No | No |
> | CreateSubscription | No | No |
> | aby | No | No |
> | přejmenovat | No | No |
> | SubscriptionDefinitions | No | No |
> | SubscriptionOperations | No | No |
> | odběru | No | No |

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | privateLinkHubs | Yes | Yes |
> | pracovní prostory | Yes | Yes |
> | pracovní prostory/bigDataPools | Yes | Yes |
> | pracovní prostory/operationStatuses | No | No |
> | pracovní prostory/sqlDatabases | Yes | Yes |
> | pracovní prostory/sqlPools | Yes | Yes |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Environment | Yes | No |
> | prostředí/accessPolicies | No | No |
> | prostředí/EventSources | Yes | No |
> | prostředí/referenceDataSets | Yes | No |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Store | Yes | Yes |
> | obchody/accessPolicies | No | No |
> | obchody/služby | No | No |
> | úložiště/služby/tokeny | No | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Yes | Yes |
> | imageTemplates / runOutputs | No | No |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | ArcZones | Yes | Yes |
> | ResourcePools | Yes | Yes |
> | Servery vCenter | Yes | Yes |
> | VirtualMachines | Yes | Yes |
> | VirtualMachineTemplates | Yes | Yes |
> | VirtualNetworks | Yes | Yes |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Yes | Yes |
> | dedicatedCloudServices | Yes | Yes |
> | virtualMachines | Yes | Yes |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | zařízení | Yes | Yes |
> | registeredSubscriptions | No | No |
> | dodavateli | No | No |
> | dodavatelé/SKU | No | No |
> | dodavatelé/vnfs | No | No |
> | virtualNetworkFunctionSkus | No | No |
> | vnfs | Yes | Yes |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Yes | Yes |
> | plánují | Yes | Yes |
> | registeredSubscriptions | No | No |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | No | No |
> | apiManagementAccounts / apiAcls | No | No |
> | apiManagementAccounts/rozhraní API | No | No |
> | apiManagementAccounts/API/apiAcls | No | No |
> | apiManagementAccounts/API/connectionAcls | No | No |
> | apiManagementAccounts/rozhraní API/připojení | No | No |
> | apiManagementAccounts/rozhraní API/připojení/connectionAcls | No | No |
> | apiManagementAccounts/API/localizedDefinitions | No | No |
> | apiManagementAccounts / connectionAcls | No | No |
> | apiManagementAccounts/připojení | No | No |
> | billingMeters | No | No |
> | certifikáty | Yes | Yes |
> | connectionGateways | Yes | Yes |
> | připojení | Yes | Yes |
> | customApis | Yes | Yes |
> | deletedSites | No | No |
> | hostingEnvironments | Yes | Yes |
> | hostingEnvironments / eventGridFilters | No | No |
> | hostingEnvironments / multiRolePools | No | No |
> | hostingEnvironments / workerPools | No | No |
> | kubeEnvironments | Yes | Yes |
> | publishingUsers | No | No |
> | doporučit | No | No |
> | resourceHealthMetadata | No | No |
> | moduly runtime | No | No |
> | Serverových farem | Yes | Yes |
> | Serverových farem/eventGridFilters | No | No |
> | místa | Yes | Yes |
> | lokality/konfigurace  | No | No |
> | lokality/eventGridFilters | No | No |
> | lokality/hostNameBindings | No | No |
> | lokality/networkConfig | No | No |
> | lokality/premieraddons | Yes | Yes |
> | lokality/sloty | Yes | Yes |
> | lokality/sloty/eventGridFilters | No | No |
> | lokality/sloty/hostNameBindings | No | No |
> | lokality/sloty/networkConfig | No | No |
> | sourceControls | No | No |
> | staticSites | Yes | Yes |
> | oproti | No | No |
> | verifyHostingEnvironmentVnet | No | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | No | No |
> | diagnosticSettingsCategories | No | No |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | multipleActivationKeys | Yes | Yes |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Yes | Yes |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | úlohy | Yes | Yes |
> | úlohy/instance | No | No |
> | zatížení a verze | No | No |
> | úlohy/verze/artefakty | No | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | konstrukční | No | No |
> | componentsSummary | No | No |
> | monitorInstances | No | No |
> | monitorInstancesSummary | No | No |
> | Monitor | No | No |
> | notificationSettings | No | No |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak používat značky pro prostředky, najdete v tématu [použití značek k uspořádání prostředků Azure](tag-resources.md).

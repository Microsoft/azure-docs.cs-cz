---
title: Podpora značek pro prostředky
description: Zobrazuje, které typy prostředků Azure podporují značky. Poskytuje podrobnosti pro všechny služby Azure.
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: f59e50e3eadb50db97756aa990f4de822a8ba089
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86026368"
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
> - [Microsoft. AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft. AnalysisServices](#microsoftanalysisservices)
> - [Microsoft. ApiManagement](#microsoftapimanagement)
> - [Microsoft. AppConfiguration](#microsoftappconfiguration)
> - [Microsoft. AppPlatform](#microsoftappplatform)
> - [Microsoft. Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
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
> - [Microsoft. Devices](#microsoftdevices)
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
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
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
> - [Microsoft. VMwareOnAzure](#microsoftvmwareonazure)
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
> | sestavy | No | No |
> | servicehealthmetrics | No | No |
> | services | No | No |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | konfiguračních | No | No |
> | generateRecommendations | No | No |
> | zprostředkovatele identity | No | No |
> | doporučit | No | No |
> | potlačení | No | No |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | actionRules | Ano | Ano |
> | výstrahy | No | No |
> | alertsList | No | No |
> | alertsMetaData | No | No |
> | alertsSummary | No | No |
> | alertsSummaryList | No | No |
> | smartDetectorAlertRules | Ano | Ano |
> | smartGroups | No | No |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | servery | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | reportFeedback | No | No |
> | služba | Ano | Ano |
> | validateServiceName | No | No |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ano | Ano |
> | configurationStores / eventGridFilters | No | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Spring | Ano | Ano |
> | Pružina/aplikace | No | No |
> | Jarní/aplikace/nasazení | No | No |

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Ano | Ano |
> | defaultProvider – neexistují | No | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | No | No |
> | dataaliasy | No | No |
> | denyAssignments | No | No |
> | elevateAccess | No | No |
> | findOrphanRoleAssignments | No | No |
> | počtu | No | No |
> | oprávnění | No | No |
> | policyAssignments | No | No |
> | policyDefinitions | No | No |
> | policySetDefinitions | No | No |
> | privateLinkAssociations | No | No |
> | providerOperations | No | No |
> | resourceManagementPrivateLinks | No | No |
> | roleAssignments | No | No |
> | roleAssignmentsUsageMetrics | No | No |
> | roleDefinitions | No | No |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ano | Ano |
> | automationAccounts/konfigurace | Ano | Ano |
> | automationAccounts/úlohy | No | No |
> | automationAccounts / privateEndpointConnectionProxies | No | No |
> | automationAccounts / privateEndpointConnections | No | No |
> | automationAccounts / privateLinkResources | No | No |
> | automationAccounts/Runbooky | Ano | Ano |
> | automationAccounts / softwareUpdateConfigurations | No | No |
> | automationAccounts nebo Webhooky | No | No |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | privateClouds | Ano | Ano |
> | privateClouds/autorizace | No | No |
> | privateClouds/clustery | No | No |
> | privateClouds / hcxEnterpriseSites | No | No |

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

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | datacontrollery | Ano | Ano |
> | hybridDataManagers | Ano | Ano |
> | postgresInstances | Ano | Ano |
> | sqlInstances | Ano | Ano |
> | sqlManagedInstances | Ano | Ano |
> | sqlServerInstances | Ano | Ano |
> | sqlServerRegistrations | Ano | Ano |
> | sqlServerRegistrations/sqlServers | No | No |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | No | No |
> | edgeSubscriptions | Ano | Ano |
> | rozpoznávání | Ano | Ano |
> | registrace/customerSubscriptions | No | No |
> | registrace/produkty | No | No |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Ano | Ano |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ano | Ano |

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
> | billingAccounts/billingProfiles/transakcí | No | No |
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
> | mapApis | Ano | Ano |
> | updateCommunicationPreference | No | No |

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
> | botServices | Ano | Ano |
> | botServices/kanály | No | No |
> | botServices/připojení | No | No |
> | jazyky | No | No |
> | šablony | No | No |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Redis | Ano | Ano |
> | Redis/EventGridFilters | No | No |
> | Redis/privateEndpointConnectionProxies | No | No |
> | Redis/privateEndpointConnectionProxies/ověřit | No | No |
> | Redis/privateEndpointConnections | No | No |
> | Redis/privateLinkResources | No | No |
> | redisEnterprise | Ano | Ano |

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
> | CdnWebApplicationFirewallPolicies | Ano | Ano |
> | edgenodes | No | No |
> | uživatelů | Ano | Ano |
> | profily/koncové body | Ano | Ano |
> | profily/koncové body/customdomains | No | No |
> | profily/koncové body/origingroups | No | No |
> | profily/koncové body/zdroje | No | No |
> | validateProbe | No | No |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ano | Ano |
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

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |

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
> | availabilitySets | Ano | Ano |
> | diskAccesses | Ano | Ano |
> | diskEncryptionSets | Ano | Ano |
> | disků | Ano | Ano |
> | Galerie | Ano | Ano |
> | Galerie/aplikace | No | No |
> | Galerie/aplikace/verze | No | No |
> | Galerie/image | No | No |
> | Galerie/image/verze | No | No |
> | hostGroups | Ano | Ano |
> | hostGroups/hostitelé | Ano | Ano |
> | images | Ano | Ano |
> | proximityPlacementGroups | Ano | Ano |
> | restorePointCollections | Ano | Ano |
> | restorePointCollections / restorePoints | No | No |
> | sharedVMExtensions | Ano | Ano |
> | sharedVMExtensions/verze | No | No |
> | sharedVMImages | Ano | Ano |
> | sharedVMImages/verze | No | No |
> | snímky | Ano | Ano |
> | sshPublicKeys | Ano | Ano |
> | virtualMachines | Ano | Ano |
> | virtualMachines/rozšíření | Ano | Ano |
> | virtualMachines / metricDefinitions | No | No |
> | virtualMachines / runCommands | Ano | Ano |
> | virtualMachineScaleSets | Ano | Ano |
> | virtualMachineScaleSets/rozšíření | No | No |
> | virtualMachineScaleSets/networkInterfaces | No | No |
> | virtualMachineScaleSets/publicIPAddresses | No | No |
> | virtualMachineScaleSets/virtualMachines | No | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No | No |

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
> | containerGroups | Ano | Ano |
> | serviceAssociationLinks | No | No |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | registr | Ano | Ano |
> | Registry/agentPools | Ano | Ano |
> | Registry a sestavení | No | No |
> | Registry/buildy/zrušit | No | No |
> | Registry/buildy/getLogLink | No | No |
> | Registry/buildTasks | Ano | Ano |
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
> | Registry/replikace | Ano | Ano |
> | Registry/běhy | No | No |
> | Registry/spuštění/zrušit | No | No |
> | Registry/scheduleRun | No | No |
> | Registry/scopeMaps | No | No |
> | Registry/taskRuns | No | No |
> | Registry/úlohy | Ano | Ano |
> | Registry a tokeny | No | No |
> | Registry/updatePolicies | No | No |
> | Registry a Webhooky | Ano | Ano |
> | Registry/Webhooky/getCallbackConfig | No | No |
> | Registry/Webhooky/příkazy k odeslání | No | No |

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
> | Výstrahy | No | No |
> | BillingAccounts | No | No |
> | Rozpočty | No | No |
> | CloudConnectors | No | No |
> | Konektory | Ano | Ano |
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
> | Dotaz | No | No |
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
> | resourceProviders | Ano | Ano |

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
> | pracovní prostory | Yes | No |
> | pracovní prostory/dbWorkspaces | No | No |
> | pracovní prostory/storageEncryption | No | No |
> | pracovní prostory/virtualNetworkPeerings | No | No |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | spustí | Ano | Ano |
> | datacatalogs | Ano | Ano |
> | datacatalogs/zdroje dat | No | No |
> | datacatalogs/zdroje dat/kontroly | No | No |
> | datové katalogy/zdroje dat/kontroly/datové sady | No | No |
> | datacatalogs/zdroje dat/kontroly/triggery | No | No |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Objekty DataFactory | Yes | No |
> | DataFactory/diagnosticSettings | No | No |
> | DataFactory/metricDefinitions | No | No |
> | dataFactorySchema | No | No |
> | továrny | Yes | No |
> | továrny/integrationRuntimes | No | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty/dataLakeStoreAccounts | No | No |
> | účty/storageAccounts | No | No |
> | účty/storageAccounts/kontejnery | No | No |
> | účty/transferAnalyticsUnits | No | No |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
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
> | BackupVaults | Ano | Ano |
> | ResourceOperationGateKeepers | Ano | Ano |

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
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
> | servery | Ano | Ano |
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

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | servery | Ano | Ano |
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

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | serverGroups | Ano | Ano |
> | servery | Ano | Ano |
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
> | serversv2 | Ano | Ano |
> | singleServers | Ano | Ano |

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
> | applicationgroups/aplikace | No | No |
> | applicationgroups/desktopy | No | No |
> | applicationgroups / startmenuitems | No | No |
> | hostpools | Ano | Ano |
> | hostpools / sessionhosts | No | No |
> | hostpools / sessionhosts / usersessions | No | No |
> | hostpools / usersessions | No | No |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ano | Ano |
> | ElasticPools / IotHubTenants | Ano | Ano |
> | ElasticPools/IotHubTenants/securitySettings | No | No |
> | IotHubs | Ano | Ano |
> | IotHubs/eventGridFilters | No | No |
> | IotHubs/securitySettings | No | No |
> | ProvisioningServices | Ano | Ano |
> | použití | No | No |

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
> | digitalTwinsInstances/koncové body | No | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | No | No |
> | databaseAccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | doménu | Ano | Ano |
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
> | services | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | doménu | Ano | Ano |
> | domény a témata | No | No |
> | eventSubscriptions | No | No |
> | extensionTopics | No | No |
> | partnerNamespaces | Ano | Ano |
> | partnerNamespaces/eventChannels | No | No |
> | partnerRegistrations | Ano | Ano |
> | partnerTopics | Ano | Ano |
> | partnerTopics / eventSubscriptions | No | No |
> | systemTopics | Ano | Ano |
> | systemTopics / eventSubscriptions | No | No |
> | popisující | Ano | Ano |
> | topicTypes | No | No |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Ano | Ano |
> | obsažené | Ano | Ano |
> | obory názvů/autorizačních pravidel | No | No |
> | obory názvů/disasterrecoveryconfigs | No | No |
> | obory názvů/eventhubs | No | No |
> | obory názvů/eventhubs/autorizačních pravidel | No | No |
> | obory názvů/eventhubs/consumergroups | No | No |
> | obory názvů/networkrulesets | No | No |

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
> | accounts | Ano | Ano |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | autoManagedAccounts | Ano | Ano |
> | autoManagedVmConfigurationProfiles | Ano | Ano |
> | configurationProfileAssignments | No | No |
> | guestConfigurationAssignments | No | No |
> | Vybavení | No | No |
> | softwareUpdateProfile | No | No |
> | softwareUpdates | No | No |

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
> | clustery/aplikace | No | No |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | services | Ano | Ano |
> | služby/iomtconnectors | No | No |
> | služby/iomtconnectors/připojení | No | No |
> | služby/iomtconnectors/mapování | No | No |
> | služby/privateEndpointConnectionProxies | No | No |
> | služby/privateEndpointConnections | Ano | Ano |
> | služby/privateLinkResources | Ano | Ano |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | virtuální | Ano | Ano |
> | počítače/rozšíření | Ano | Ano |

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
> | registeredSubscriptions | No | No |
> | dodavateli | No | No |
> | dodavatelé/SKU | No | No |
> | dodavatelé/vnfs | No | No |
> | virtualNetworkFunctionSkus | No | No |
> | vnfs | Ano | Ano |

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
> | komponenty/linkedStorageAccounts | No | No |
> | komponenty/ProactiveDetectionConfigs | No | No |
> | diagnosticSettings | No | No |
> | guestDiagnosticSettings | Ano | Ano |
> | guestDiagnosticSettingsAssociation | Ano | Ano |
> | logprofiles | Ano | Ano |
> | metricAlerts | Ano | Ano |
> | privateLinkScopes | Ano | Ano |
> | privateLinkScopes / privateEndpointConnections | No | No |
> | privateLinkScopes / scopedResources | No | No |
> | queryPacks | Ano | Ano |
> | queryPacks/dotazy | No | No |
> | scheduledQueryRules | Ano | Ano |
> | webové testy | Ano | Ano |
> | sešity | Ano | Ano |
> | workbooktemplates | Ano | Ano |

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
> | deletedVaults | No | No |
> | hsmPools | Ano | Ano |
> | managedHSMs | Ano | Ano |
> | trezory | Ano | Ano |
> | trezory/accessPolicies | No | No |
> | trezory/eventGridFilters | No | No |
> | trezory/tajné klíče | No | No |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | connectedClusters | Ano | Ano |
> | registeredSubscriptions | No | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | sourceControlConfigurations | No | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Ano | Ano |
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
> | labaccounts | Ano | Ano |
> | uživatelé | No | No |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ano | Ano |
> | integrationAccounts | Ano | Ano |
> | integrationServiceEnvironments | Ano | Ano |
> | integrationServiceEnvironments/Inspirujte | No | No |
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
> | pracovní prostory a výpočetní prostředky | No | No |
> | pracovní prostory/eventGridFilters | No | No |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applyUpdates | No | No |
> | configurationAssignments | No | No |
> | maintenanceConfigurations | Ano | Ano |
> | aktualizovány | No | No |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Identity | No | No |
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
> | accounts | Ano | Ano |
> | účty/eventGridFilters | No | No |
> | účty/privateAtlases | Ano | Ano |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
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
> | classicDevServices | Ano | Ano |
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
> | MediaServices | Ano | Ano |
> | MediaServices/accountFilters | No | No |
> | MediaServices/assety | No | No |
> | MediaServices/assety/assetFilters | No | No |
> | MediaServices/contentKeyPolicies | No | No |
> | MediaServices/eventGridFilters | No | No |
> | MediaServices/liveEventOperations | No | No |
> | MediaServices/liveEvents | Ano | Ano |
> | MediaServices/liveEvents/liveOutputs | No | No |
> | MediaServices/liveEvents/privateEndpointConnectionProxies | No | No |
> | MediaServices/liveOutputOperations | No | No |
> | MediaServices/mediaGraphs | No | No |
> | MediaServices/streamingEndpointOperations | No | No |
> | MediaServices/starají | Ano | Ano |
> | MediaServices/starají/privateEndpointConnectionProxies | No | No |
> | MediaServices/streamingLocators | No | No |
> | MediaServices/streamingPolicies | No | No |
> | MediaServices/streamingPrivateEndpointConnectionProxyOperations | No | No |
> | MediaServices/transformace | No | No |
> | MediaServices/transformace/úlohy | No | No |

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
> | netAppAccounts | Yes | No |
> | netAppAccounts / accountBackups | No | No |
> | netAppAccounts / capacityPools | Yes | No |
> | netAppAccounts/capacityPools/svazky | Yes | No |
> | netAppAccounts/capacityPools/svazky/snímky | No | No |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ano | Ano |
> | applicationGatewayWebApplicationFirewallPolicies | Ano | Ano |
> | applicationSecurityGroups | Ano | Ano |
> | azureFirewallFqdnTags | No | No |
> | azureFirewalls | Yes | No |
> | bastionHosts | Yes | No |
> | bgpServiceCommunities | No | No |
> | připojení | Ano | Ano |
> | ddosCustomPolicies | Ano | Ano |
> | ddosProtectionPlans | Ano | Ano |
> | dnsOperationStatuses | No | No |
> | dnszones | Ano | Ano |
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
> | expressRouteCircuits | Ano | Ano |
> | expressRouteCrossConnections | Ano | Ano |
> | expressRouteGateways | Ano | Ano |
> | expressRoutePorts | Ano | Ano |
> | expressRouteServiceProviders | No | No |
> | firewallPolicies | Ano | Ano |
> | frontdoors | Ano, ale omezeno (viz [Poznámka níže](#frontdoor)) | Yes |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ano, ale omezeno (viz [Poznámka níže](#frontdoor)) | No |
> | frontdoorWebApplicationFirewallPolicies | Ano, ale omezeno (viz [Poznámka níže](#frontdoor)) | Yes |
> | getDnsResourceReference | No | No |
> | internalNotify | No | No |
> | loadBalancers | Ano | Ano |
> | localNetworkGateways | Ano | Ano |
> | natGateways | Ano | Ano |
> | networkIntentPolicies | Ano | Ano |
> | networkInterfaces | Ano | Ano |
> | networkProfiles | Ano | Ano |
> | networkSecurityGroups | Ano | Ano |
> | networkWatchers | Ano | Ano |
> | networkWatchers / connectionMonitors | Yes | No |
> | networkWatchers / flowLogs | No | No |
> | networkWatchers/čočky | Yes | No |
> | networkWatchers / pingMeshes | Yes | No |
> | p2sVpnGateways | Ano | Ano |
> | privateDnsOperationStatuses | No | No |
> | privateDnsZones | Ano | Ano |
> | privateDnsZones/A | No | No |
> | privateDnsZones/AAAA | No | No |
> | privateDnsZones/vše | No | No |
> | privateDnsZones/CNAME | No | No |
> | privateDnsZones/MX | No | No |
> | privateDnsZones/PTR | No | No |
> | privateDnsZones/SOA | No | No |
> | privateDnsZones/SRV | No | No |
> | privateDnsZones/TXT | No | No |
> | privateDnsZones / virtualNetworkLinks | Ano | Ano |
> | privateEndpoints | Ano | Ano |
> | privateLinkServices | Ano | Ano |
> | publicIPAddresses | Ano | Ano |
> | publicIPPrefixes | Ano | Ano |
> | routeFilters | Ano | Ano |
> | routeTables | Ano | Ano |
> | serviceEndpointPolicies | Ano | Ano |
> | trafficManagerGeographicHierarchies | No | No |
> | trafficmanagerprofiles | Ano | Ano |
> | trafficmanagerprofiles/Heat mapy | No | No |
> | trafficManagerUserMetricsKeys | No | No |
> | virtualHubs | Ano | Ano |
> | virtualNetworkGateways | Ano | Ano |
> | virtualNetworks | Ano | Ano |
> | virtualNetworks/podsítě | No | No |
> | virtualNetworkTaps | Ano | Ano |
> | virtualWans | Yes | No |
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
> | osNamespaces | Ano | Ano |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ano | Ano |
> | ImportSites | Ano | Ano |
> | ServerSites | Ano | Ano |
> | VMwareSites | Ano | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | existující | Ano | Ano |
> | deletedWorkspaces | No | No |
> | linkTargets | No | No |
> | storageInsightConfigs | No | No |
> | pracovní prostory | Ano | Ano |
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
> | managementconfigurations | Ano | Ano |
> | Řešení | Ano | Ano |
> | zobrazení | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | No | No |
> | peerAsns | No | No |
> | partnerské vztahy | Ano | Ano |
> | peeringServiceCountries | No | No |
> | peeringServiceProviders | No | No |
> | peeringServices | Ano | Ano |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
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
> | řídicí panely | Ano | Ano |
> | userSettings | No | No |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | privateLinkServicesForPowerBI | Ano | Ano |
> | tenantů | Ano | Ano |
> | klienti/pracovní prostory | No | No |
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

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | No | No |
> | providerRegistrations / defaultRollouts | No | No |
> | providerRegistrations / resourceTypeRegistrations | No | No |
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
> | backupProtectedItems | No | No |
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
> | odešle | Ano | Ano |
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
> | deploymentScripts | Ano | Ano |
> | deploymentScripts/protokoly | No | No |
> | odkazy | No | No |
> | notifyResourceJobs | No | No |
> | dodavateli | No | No |
> | resourceGroups | Yes | No |
> | odběru | Yes | No |
> | templateSpecs | Ano | Ano |
> | templateSpecs/verze | Ano | Ano |
> | tenantů | No | No |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aplikace | Ano | Ano |
> | saasresources | No | No |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | No | No |
> | searchServices | Ano | Ano |

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
> | automatizace | Ano | Ano |
> | AutoProvisioningSettings | No | No |
> | Předpisů | No | No |
> | dataCollectionAgents | No | No |
> | deviceSecurityGroups | No | No |
> | discoveredSecuritySolutions | No | No |
> | externalSecuritySolutions | No | No |
> | InformationProtectionPolicies | No | No |
> | iotSecuritySolutions | Ano | Ano |
> | iotSecuritySolutions / analyticsModels | No | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No | No |
> | iotSecuritySolutions / iotAlerts | No | No |
> | iotSecuritySolutions / iotAlertTypes | No | No |
> | jitNetworkAccessPolicies | No | No |
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
> | incidenty | No | No |
> | officeConsents | No | No |
> | nastavení | No | No |
> | threatIntelligence | No | No |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | consoleServices | No | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | obsažené | Ano | Ano |
> | obory názvů/autorizačních pravidel | No | No |
> | obory názvů/disasterrecoveryconfigs | No | No |
> | obory názvů/eventgridfilters | No | No |
> | obory názvů/networkrulesets | No | No |
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
> | aplikace | Ano | Ano |
> | existující | Ano | Ano |
> | clustery/aplikace | No | No |
> | containerGroups | Ano | Ano |
> | containerGroupSets | Ano | Ano |
> | edgeclusters | Ano | Ano |
> | edgeclusters/aplikace | No | No |
> | managedclusters | Ano | Ano |
> | managedclusters/nodetypes | No | No |
> | sítí | Ano | Ano |
> | secretstores | Ano | Ano |
> | secretstores/certifikáty | No | No |
> | secretstores/tajné kódy | No | No |
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
> | providerRegistrations | No | No |
> | providerRegistrations / resourceTypeRegistrations | No | No |
> | uvádění | Ano | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | SignalR | Ano | Ano |
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
> | applicationDefinitions | Ano | Ano |
> | aplikace | Ano | Ano |
> | jitRequests | Ano | Ano |


## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | managedInstances | Ano | Ano |
> | managedInstances/databáze | Ano (viz [Poznámka níže](#sqlnote)) | Yes |
> | managedInstances/databáze/backupShortTermRetentionPolicies | No | No |
> | managedInstances/databáze/schémata/tabulky/sloupce/sensitivityLabels | No | No |
> | managedInstances/databáze/vulnerabilityAssessments | No | No |
> | managedInstances/databáze/vulnerabilityAssessments/Rules/směrné plány | No | No |
> | managedInstances / encryptionProtector | No | No |
> | managedInstances/klíče | No | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No | No |
> | managedInstances / vulnerabilityAssessments | No | No |
> | servery | Ano | Ano |
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
> | SqlVirtualMachineGroups | Ano | Ano |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No | No |
> | SqlVirtualMachines | Ano | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Ano | Ano |
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
> | mezipaměti | Ano | Ano |
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
> | storageSyncServices | Ano | Ano |
> | storageSyncServices/registeredServer | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices/pracovní postupy | No | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ano | Ano |
> | storageSyncServices/registeredServer | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices/pracovní postupy | No | No |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ano | Ano |
> | storageSyncServices/registeredServer | No | No |
> | storageSyncServices / syncGroups | No | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No | No |
> | storageSyncServices / syncGroups / serverEndpoints | No | No |
> | storageSyncServices/pracovní postupy | No | No |

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
> | streamingjobs | Ano (viz poznámka níže) | Yes |

> [!NOTE]
> Značku nelze přidat, je-li spuštěna aplikace streamingjobs. Zastavením prostředku přidejte značku.

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | cancel | No | No |
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
> | privateLinkHubs | Ano | Ano |
> | pracovní prostory | Ano | Ano |
> | pracovní prostory/bigDataPools | Ano | Ano |
> | pracovní prostory/operationStatuses | No | No |
> | pracovní prostory/sqlPools | Ano | Ano |

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
> | Store | Ano | Ano |
> | obchody/accessPolicies | No | No |
> | obchody/služby | No | No |
> | úložiště/služby/tokeny | No | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | imageTemplates | Ano | Ano |
> | imageTemplates / runOutputs | No | No |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | ArcZones | Ano | Ano |
> | ResourcePools | Ano | Ano |
> | Servery vCenter | Ano | Ano |
> | VirtualMachines | Ano | Ano |
> | VirtualMachineTemplates | Ano | Ano |
> | VirtualNetworks | Ano | Ano |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ano | Ano |
> | dedicatedCloudServices | Ano | Ano |
> | virtualMachines | Ano | Ano |

## <a name="microsoftvmwareonazure"></a>Microsoft. VMwareOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | privateClouds | Ano | Ano |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | zařízení | Ano | Ano |
> | registeredSubscriptions | No | No |
> | dodavateli | No | No |
> | dodavatelé/SKU | No | No |
> | dodavatelé/vnfs | No | No |
> | virtualNetworkFunctionSkus | No | No |
> | vnfs | Ano | Ano |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | plánují | Ano | Ano |
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
> | certifikáty | Ano | Ano |
> | connectionGateways | Ano | Ano |
> | připojení | Ano | Ano |
> | customApis | Ano | Ano |
> | deletedSites | No | No |
> | hostingEnvironments | Ano | Ano |
> | hostingEnvironments / eventGridFilters | No | No |
> | hostingEnvironments / multiRolePools | No | No |
> | hostingEnvironments / workerPools | No | No |
> | kubeEnvironments | Ano | Ano |
> | publishingUsers | No | No |
> | doporučit | No | No |
> | resourceHealthMetadata | No | No |
> | moduly runtime | No | No |
> | Serverových farem | Ano | Ano |
> | Serverových farem/eventGridFilters | No | No |
> | místa | Ano | Ano |
> | lokality/konfigurace  | No | No |
> | lokality/eventGridFilters | No | No |
> | lokality/hostNameBindings | No | No |
> | lokality/networkConfig | No | No |
> | lokality/premieraddons | Ano | Ano |
> | lokality/sloty | Ano | Ano |
> | lokality/sloty/eventGridFilters | No | No |
> | lokality/sloty/hostNameBindings | No | No |
> | lokality/sloty/networkConfig | No | No |
> | sourceControls | No | No |
> | staticSites | Ano | Ano |
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

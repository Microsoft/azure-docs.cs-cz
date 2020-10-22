---
title: Úplné odstranění režimu
description: Ukazuje, jak typy prostředků zpracovávají úplné odstraňování režimu v Azure Resource Manager šablonách.
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: b1f7623d6eee1ff629412a4cc0d76e59e6c9e847
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370120"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Odstranění prostředků Azure pro nasazení v úplném režimu

Tento článek popisuje, jak typy prostředků zpracovávají odstranění, když není v šabloně, která je nasazena v úplném režimu.

Typy prostředků označené **Ano** se odstraní, když typ není v šabloně nasazené s úplným režimem.

Typy prostředků označené jako **ne** se automaticky neodstraní, pokud nejsou v šabloně. jsou však odstraněny, pokud je odstraněn nadřazený prostředek. Úplný popis chování najdete v tématu [Azure Resource Manager režimy nasazení](deployment-modes.md).

Pokud nasadíte do [více než jedné skupiny prostředků v šabloně](cross-scope-deployment.md), prostředky ve skupině prostředků zadané v rámci operace nasazení mají nárok na odstranění. Prostředky v sekundárních skupinách prostředků se neodstraňují.

Prostředky jsou uvedeny v oboru názvů poskytovatele prostředků. Pokud chcete porovnat obor názvů poskytovatele prostředků se svým názvem služby Azure, přečtěte si téma [poskytovatelé prostředků pro služby Azure](../management/azure-services-resource-providers.md).

> [!NOTE]
> Vždy použijte [operaci citlivostní zpracování](template-deploy-what-if.md) před nasazením šablony v úplném režimu. Jak ukazuje, které prostředky se vytvoří, odstraní nebo upraví. Použijte co když, chcete-li se vyhnout neúmyslnému odstranění prostředků.
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
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | DomainServices | Ano |
> | DomainServices / oucontainer | Ne |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | supportProviders | Ne |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aadsupportcases | Ne |
> | addsservices | Ne |
> | technici | Ne |
> | anonymousapiusers | Ne |
> | konfigurace | Ne |
> | Protokoly | Ne |
> | reports | Ne |
> | servicehealthmetrics | Ne |
> | services | Ne |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | advisorScore | Ne |
> | konfiguračních | Ne |
> | generateRecommendations | Ne |
> | zprostředkovatele identity | Ne |
> | doporučit | Ne |
> | potlačení | Ne |

## <a name="microsoftagfoodplatform"></a>Microsoft. AgFoodPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | farmBeats | Ano |

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | actionRules | Ano |
> | výstrahy | Ne |
> | alertsList | Ne |
> | alertsMetaData | Ne |
> | alertsSummary | Ne |
> | alertsSummaryList | Ne |
> | smartDetectorAlertRules | Ano |
> | smartGroups | Ne |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servery | Ano |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | reportFeedback | Ne |
> | service | Ano |
> | validateServiceName | Ne |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | configurationStores | Ano |
> | configurationStores / eventGridFilters | Ne |
> | configurationStores/hodnoty | Ne |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Spring | Ano |
> | Pružina/aplikace | Ne |
> | Jarní/aplikace/nasazení | Ne |

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | attestationProviders | Ano |
> | defaultProvider – neexistují | Ne |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | Ne |
> | accessReviewScheduleSettings | Ne |
> | classicAdministrators | Ne |
> | dataaliasy | Ne |
> | denyAssignments | Ne |
> | elevateAccess | Ne |
> | findOrphanRoleAssignments | Ne |
> | počtu | Ne |
> | oprávnění | Ne |
> | policyAssignments | Ne |
> | policyDefinitions | Ne |
> | policyExemptions | Ne |
> | policySetDefinitions | Ne |
> | privateLinkAssociations | Ne |
> | providerOperations | Ne |
> | resourceManagementPrivateLinks | Ano |
> | roleAssignments | Ne |
> | roleAssignmentsUsageMetrics | Ne |
> | roleDefinitions | Ne |

## <a name="microsoftautomanage"></a>Microsoft. automanage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | configurationProfileAssignments | Ne |
> | configurationProfilePreferences | Ano |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | automationAccounts | Ano |
> | automationAccounts/konfigurace | Ano |
> | automationAccounts/úlohy | Ne |
> | automationAccounts / privateEndpointConnectionProxies | Ne |
> | automationAccounts / privateEndpointConnections | Ne |
> | automationAccounts / privateLinkResources | Ne |
> | automationAccounts/Runbooky | Ano |
> | automationAccounts / softwareUpdateConfigurations | Ne |
> | automationAccounts nebo Webhooky | Ne |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | privateClouds | Ano |
> | privateClouds/Doplňky | Ne |
> | privateClouds/autorizace | Ne |
> | privateClouds/clustery | Ne |
> | privateClouds / globalReachConnections | Ne |
> | privateClouds / hcxEnterpriseSites | Ne |
> | privateClouds / workloadNetworks | Ne |
> | privateClouds / workloadNetworks / dhcpConfigurations | Ne |
> | privateClouds/workloadNetworks/Brans | Ne |
> | privateClouds / workloadNetworks / portMirroringProfiles | Ne |
> | privateClouds/workloadNetworks/segmenty | Ne |
> | privateClouds / workloadNetworks / virtualMachines | Ne |
> | privateClouds / workloadNetworks / vmGroups | Ne |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Ženeva

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Environment | Ne |
> | prostředí/účty | Ne |
> | prostředí/účty/obory názvů | Ne |
> | prostředí/účty/obory názvů/konfigurace | Ne |

## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | b2cDirectories | Ano |
> | b2ctenants | Ne |
> | guestUsages | Ano |

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | datacontrollery | Ano |
> | postgresInstances | Ano |
> | sqlManagedInstances | Ano |
> | sqlServerInstances | Ano |
> | sqlServerRegistrations | Ano |
> | sqlServerRegistrations/sqlServers | Ne |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | cloudManifestFiles | Ne |
> | edgeSubscriptions | Ano |
> | linkedSubscriptions | Ano |
> | rozpoznávání | Ano |
> | registrace/customerSubscriptions | Ne |
> | registrace/produkty | Ne |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |

## <a name="microsoftbaremetalinfrastructure"></a>Microsoft. BareMetalInfrastructure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | bareMetalInstances | Ano |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | batchAccounts | Ano |
> | batchAccounts/certifikáty | Ne |
> | batchAccounts/fondy | Ne |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | billingAccounts | Ne |
> | billingAccounts/smlouvy | Ne |
> | billingAccounts / billingPermissions | Ne |
> | billingAccounts / billingProfiles | Ne |
> | billingAccounts / billingProfiles / billingPermissions | Ne |
> | billingAccounts / billingProfiles / billingRoleAssignments | Ne |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Ne |
> | billingAccounts / billingProfiles / billingSubscriptions | Ne |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Ne |
> | billingAccounts/billingProfiles/zákazníci | Ne |
> | billingAccounts/billingProfiles/– pokyny | Ne |
> | billingAccounts/billingProfiles/faktury | Ne |
> | billingAccounts/billingProfiles/faktury/pricesheet | Ne |
> | billingAccounts/billingProfiles/faktury/transakce | Ne |
> | billingAccounts / billingProfiles / invoiceSections | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Ne |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Ne |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Ne |
> | billingAccounts/billingProfiles/invoiceSections/Products | Ne |
> | billingAccounts/billingProfiles/invoiceSections/Products/Transfer | Ne |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Ne |
> | billingAccounts/billingProfiles/invoiceSections/transakce | Ne |
> | billingAccounts/billingProfiles/invoiceSections/transfery | Ne |
> | billingAccounts / BillingProfiles / patchOperations | Ne |
> | billingAccounts / billingProfiles / paymentMethods | Ne |
> | billingAccounts/billingProfiles/– zásady | Ne |
> | billingAccounts/billingProfiles/pricesheet | Ne |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Ne |
> | billingAccounts/billingProfiles/produkty | Ne |
> | billingAccounts/billingProfiles/rezervace | Ne |
> | billingAccounts/billingProfiles/transakcí | Ne |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | Ne |
> | billingAccounts / billingRoleAssignments | Ne |
> | billingAccounts / billingRoleDefinitions | Ne |
> | billingAccounts / billingSubscriptions | Ne |
> | billingAccounts/billingSubscriptions/faktury | Ne |
> | billingAccounts / createBillingRoleAssignment | Ne |
> | billingAccounts / createInvoiceSectionOperations | Ne |
> | billingAccounts/zákazníci | Ne |
> | billingAccounts/Customers/billingPermissions | Ne |
> | billingAccounts/Customers/billingSubscriptions | Ne |
> | billingAccounts/Customers/initiateTransfer | Ne |
> | billingAccounts/Customers/policies | Ne |
> | billingAccounts/zákazníci/produkty | Ne |
> | billingAccounts/zákazníci/transakce | Ne |
> | billingAccounts/zákazníci/přenosy | Ne |
> | billingAccounts/oddělení | Ne |
> | billingAccounts/oddělení/billingPermissions | Ne |
> | billingAccounts/oddělení/billingRoleAssignments | Ne |
> | billingAccounts/oddělení/billingRoleDefinitions | Ne |
> | billingAccounts / enrollmentAccounts | Ne |
> | billingAccounts / enrollmentAccounts / billingPermissions | Ne |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | Ne |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | Ne |
> | billingAccounts/faktury | Ne |
> | billingAccounts/faktury/transakce | Ne |
> | billingAccounts / invoiceSections | Ne |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Ne |
> | billingAccounts / invoiceSections / billingSubscriptions | Ne |
> | billingAccounts/invoiceSections/billingSubscriptions/Transfer | Ne |
> | billingAccounts/invoiceSections/zvýšení oprávnění | Ne |
> | billingAccounts / invoiceSections / initiateTransfer | Ne |
> | billingAccounts / invoiceSections / patchOperations | Ne |
> | billingAccounts / invoiceSections / productMoveOperations | Ne |
> | billingAccounts/invoiceSections/produkty | Ne |
> | billingAccounts/invoiceSections/produkty/přenos | Ne |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Ne |
> | billingAccounts/invoiceSections/transakcí | Ne |
> | billingAccounts/invoiceSections/transfery | Ne |
> | billingAccounts / lineOfCredit | Ne |
> | billingAccounts / patchOperations | Ne |
> | billingAccounts / paymentMethods | Ne |
> | billingAccounts/produkty | Ne |
> | billingAccounts/rezervace | Ne |
> | billingAccounts/transakce | Ne |
> | billingPeriods | Ne |
> | billingPermissions | Ne |
> | billingProperty | Ne |
> | billingRoleAssignments | Ne |
> | billingRoleDefinitions | Ne |
> | createBillingRoleAssignment | Ne |
> | oddělení | Ne |
> | enrollmentAccounts | Ne |
> | faktury | Ne |
> | Převede | Ne |
> | přenosy/acceptTransfer | Ne |
> | přenosy/declineTransfer | Ne |
> | přenosy/stav operationstatus | Ne |
> | přenosy/validateTransfer | Ne |
> | validateAddress | Ne |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | mapApis | Ano |
> | updateCommunicationPreference | Ne |

## <a name="microsoftblockchain"></a>Microsoft. blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | blockchainMembers | Ano |
> | cordaMembers | Ano |
> | sledovacích procesů | Ano |

## <a name="microsoftblockchaintokens"></a>Microsoft. BlockchainTokens

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | TokenServices | Ano |
> | TokenServices / BlockchainNetworks | Ne |
> | TokenServices/skupiny | Ne |
> | TokenServices/skupiny/účty | Ne |
> | TokenServices / TokenTemplates | Ne |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | blueprintAssignments | Ne |
> | blueprintAssignments / assignmentOperations | Ne |
> | blueprintAssignments/operace | Ne |
> | podrobné plány | Ne |
> | modrotisky/artefakty | Ne |
> | plány/verze | Ne |
> | modrotisky/verze/artefakty | Ne |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | botServices | Ano |
> | botServices/kanály | Ne |
> | botServices/připojení | Ne |
> | jazyky | Ne |
> | šablony | Ne |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Redis | Ano |
> | Redis/EventGridFilters | Ne |
> | Redis/privateEndpointConnectionProxies | Ne |
> | Redis/privateEndpointConnectionProxies/ověřit | Ne |
> | Redis/privateEndpointConnections | Ne |
> | Redis/privateLinkResources | Ne |
> | redisEnterprise | Ano |
> | RedisEnterprise / privateEndpointConnectionProxies | Ne |
> | RedisEnterprise/privateEndpointConnectionProxies/ověřit | Ne |
> | RedisEnterprise / privateEndpointConnections | Ne |
> | RedisEnterprise / privateLinkResources | Ne |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | appliedReservations | Ne |
> | autoQuotaIncrease | Ne |
> | calculateExchange | Ne |
> | calculatePrice | Ne |
> | calculatePurchasePrice | Ne |
> | spustí | Ne |
> | commercialReservationOrders | Ne |
> | výměn | Ne |
> | ownReservations | Ne |
> | placePurchaseOrder | Ne |
> | reservationOrders | Ne |
> | reservationOrders / calculateRefund | Ne |
> | reservationOrders/sloučit | Ne |
> | reservationOrders/rezervace | Ne |
> | reservationOrders/rezervace/Revize | Ne |
> | reservationOrders/vrátit | Ne |
> | reservationOrders/Split | Ne |
> | reservationOrders/swap | Ne |
> | rezervace | Ne |
> | resourceProviders | Ne |
> | resources | Ne |
> | validateReservationOrder | Ne |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Ne |
> | CdnWebApplicationFirewallPolicies | Ano |
> | edgenodes | Ne |
> | uživatelů | Ano |
> | profily/koncové body | Ano |
> | profily/koncové body/customdomains | Ne |
> | profily/koncové body/origingroups | Ne |
> | profily/koncové body/zdroje | Ne |
> | validateProbe | Ne |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | certificateOrders | Ano |
> | certificateOrders/certifikáty | Ne |
> | validateCertificateRegistrationInformation | Ne |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | profil | Ne |
> | resourceChanges | Ne |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | možnosti | Ne |
> | domainNames | Ano |
> | domainNames/možnosti | Ne |
> | domainNames / internalLoadBalancers | Ne |
> | domainNames / serviceCertificates | Ne |
> | domainNames/sloty | Ne |
> | domainNames/sloty/role | Ne |
> | domainNames/sloty/role/metricDefinitions | Ne |
> | domainNames/sloty/role/metriky | Ne |
> | moveSubscriptionResources | Ne |
> | operatingSystemFamilies | Ne |
> | operatingSystems | Ne |
> | quotas | Ne |
> | Typ prostředků | Ne |
> | validateSubscriptionMoveAvailability | Ne |
> | virtualMachines | Ano |
> | virtualMachines / diagnosticSettings | Ne |
> | virtualMachines / metricDefinitions | Ne |
> | virtualMachines/metriky | Ne |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | classicInfrastructureResources | Ne |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | možnosti | Ne |
> | expressRouteCrossConnections | Ne |
> | expressRouteCrossConnections/partnerské vztahy | Ne |
> | gatewaySupportedDevices | Ne |
> | networkSecurityGroups | Ano |
> | quotas | Ne |
> | reservedIps | Ano |
> | virtualNetworks | Ano |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Ne |
> | virtualNetworks/virtualNetworkPeerings | Ne |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | možnosti | Ne |
> | disků | Ne |
> | images | Ne |
> | OSImage | Ne |
> | osPlatformImages | Ne |
> | publicImages | Ne |
> | quotas | Ne |
> | storageAccounts | Ano |
> | storageAccounts/blobServices | Ne |
> | storageAccounts/služby | Ne |
> | storageAccounts/metricDefinitions | Ne |
> | storageAccounts/metriky | Ne |
> | storageAccounts/queueServices | Ne |
> | storageAccounts/služby | Ne |
> | storageAccounts/služby/diagnosticSettings | Ne |
> | storageAccounts/služby/metricDefinitions | Ne |
> | storageAccounts/služby/metriky | Ne |
> | storageAccounts/tableServices | Ne |
> | storageAccounts/vmImages | Ne |
> | vmImages | Ne |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | plánují | Ano |
> | registeredSubscriptions | Ne |

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/privateEndpointConnectionProxies | Ne |
> | účty/privateEndpointConnections | Ne |
> | účty/privateLinkResources | Ne |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | RateCard | Ne |
> | UsageAggregates | Ne |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | availabilitySets | Ano |
> | cloudServices | Ano |
> | cloudServices / networkInterfaces | Ne |
> | cloudServices/publicIPAddresses | Ne |
> | cloudServices / roleInstances | Ne |
> | cloudServices / roleInstances / networkInterfaces | Ne |
> | cloudServices/role | Ne |
> | diskAccesses | Ano |
> | diskEncryptionSets | Ano |
> | disků | Ano |
> | Galerie | Ano |
> | Galerie/aplikace | Ne |
> | Galerie/aplikace/verze | Ne |
> | Galerie/image | Ne |
> | Galerie/image/verze | Ne |
> | hostGroups | Ano |
> | hostGroups/hostitelé | Ano |
> | images | Ano |
> | proximityPlacementGroups | Ano |
> | restorePointCollections | Ano |
> | restorePointCollections / restorePoints | Ne |
> | sharedVMExtensions | Ano |
> | sharedVMExtensions/verze | Ne |
> | sharedVMImages | Ano |
> | sharedVMImages/verze | Ne |
> | snímky | Ano |
> | sshPublicKeys | Ano |
> | virtualMachines | Ano |
> | virtualMachines/rozšíření | Ano |
> | virtualMachines / metricDefinitions | Ne |
> | virtualMachines / runCommands | Ano |
> | virtualMachineScaleSets | Ano |
> | virtualMachineScaleSets/rozšíření | Ne |
> | virtualMachineScaleSets/networkInterfaces | Ne |
> | virtualMachineScaleSets/publicIPAddresses | Ne |
> | virtualMachineScaleSets/virtualMachines | Ne |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Ne |

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | CacheNodes | Ano |

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | AggregatedCost | Ne |
> | Zůstatky | Ne |
> | Rozpočty | Ne |
> | Poplatky | Ne |
> | CostTags | Ne |
> | dobropis | Ne |
> | stránka events | Ne |
> | Prognózy | Ne |
> | ŠARŽ | Ne |
> | Tržišť | Ne |
> | Ceníky | Ne |
> | produktech | Ne |
> | ReservationDetails | Ne |
> | ReservationRecommendationDetails | Ne |
> | ReservationRecommendations | Ne |
> | ReservationSummaries | Ne |
> | ReservationTransactions | No |
> | Značky | No |
> | tenantů | Ne |
> | Terminologie | Ne |
> | UsageDetails (Podrobnosti o využití) | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | containerGroups | Ano |
> | serviceAssociationLinks | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | registr | Ano |
> | Registry/agentPools | Ano |
> | Registry a sestavení | Ne |
> | Registry/buildy/zrušit | Ne |
> | Registry/buildy/getLogLink | Ne |
> | Registry/buildTasks | Ano |
> | Registry/buildTasks/kroky | Ne |
> | Registry/eventGridFilters | Ne |
> | Registry/exportPipelines | Ne |
> | Registry/generateCredentials | Ne |
> | Registry/getBuildSourceUploadUrl | Ne |
> | Registry/getpřihlašovací údaje | Ne |
> | Registry/importImage | Ne |
> | Registry/importPipelines | Ne |
> | Registry/pipelineRuns | Ne |
> | Registry/privateEndpointConnectionProxies | Ne |
> | Registry/privateEndpointConnectionProxies/ověřit | Ne |
> | Registry/privateEndpointConnections | Ne |
> | Registry/privateLinkResources | Ne |
> | Registry/queueBuild | Ne |
> | Registry/regenerateCredential | Ne |
> | Registry/regenerateCredentials | Ne |
> | Registry/replikace | Ano |
> | Registry/běhy | Ne |
> | Registry/spuštění/zrušit | Ne |
> | Registry/scheduleRun | Ne |
> | Registry/scopeMaps | Ne |
> | Registry/taskRuns | Ne |
> | Registry/úlohy | Ano |
> | Registry a tokeny | Ne |
> | Registry/updatePolicies | Ne |
> | Registry a Webhooky | Ano |
> | Registry/Webhooky/getCallbackConfig | Ne |
> | Registry/Webhooky/příkazy k odeslání | Ne |

## <a name="microsoftcontainerservice"></a>Microsoft. ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | containerServices | Ano |
> | managedClusters | Ano |
> | openShiftManagedClusters | Ano |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Výstrahy | Ne |
> | BillingAccounts | Ne |
> | Rozpočty | Ne |
> | CloudConnectors | Ne |
> | Konektory | Ano |
> | costAllocationRules | Ne |
> | Oddělení | Ne |
> | Dimenze | Ne |
> | EnrollmentAccounts | Ne |
> | Vývozních | Ne |
> | ExternalBillingAccounts | Ne |
> | ExternalBillingAccounts/výstrahy | Ne |
> | ExternalBillingAccounts/dimenzí | Ne |
> | ExternalBillingAccounts/prognóza | Ne |
> | ExternalBillingAccounts/dotaz | Ne |
> | ExternalSubscriptions | Ne |
> | ExternalSubscriptions/výstrahy | Ne |
> | ExternalSubscriptions/dimenzí | Ne |
> | ExternalSubscriptions/prognóza | Ne |
> | ExternalSubscriptions/dotaz | Ne |
> | Prognóza | Ne |
> | Insights | Ne |
> | Dotaz | Ne |
> | register | Ne |
> | Reportconfigs | Ne |
> | Sestavy | Ne |
> | Nastavení | Ne |
> | showbackRules | Ne |
> | Zobrazení | Ne |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | požádal | Ne |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | přidružení | Ne |
> | resourceProviders | Ano |

## <a name="microsoftd365customerinsights"></a>Microsoft. D365CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | instance | Ano |

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | úlohy | Ano |

## <a name="microsoftdataboxedge"></a>Microsoft. DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Ano |

## <a name="microsoftdatabricks"></a>Cihly Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | pracovní prostory | Ano |
> | pracovní prostory/dbWorkspaces | Ne |
> | pracovní prostory/virtualNetworkPeerings | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | spustí | Ano |

## <a name="microsoftdatafactory"></a>Microsoft. DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Objekty DataFactory | Ano |
> | DataFactory/diagnosticSettings | Ne |
> | DataFactory/metricDefinitions | Ne |
> | dataFactorySchema | Ne |
> | továrny | Ano |
> | továrny/integrationRuntimes | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/dataLakeStoreAccounts | Ne |
> | účty/storageAccounts | Ne |
> | účty/storageAccounts/kontejnery | Ne |
> | účty/transferAnalyticsUnits | Ne |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/eventGridFilters | Ne |
> | účty/firewallRules | Ne |

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | services | Ano |
> | služby a projekty | Ano |

## <a name="microsoftdataprotection"></a>Microsoft. DataProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | BackupVaults | Ano |
> | ResourceOperationGateKeepers | Ano |

## <a name="microsoftdatashare"></a>Microsoft. datashare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/sdílené složky | Ne |
> | účty/akcie/datové sady | Ne |
> | účty/akcie/pozvánky | Ne |
> | účty/akcie/providersharesubscriptions | Ne |
> | účty/akcie/synchronizationSettings | Ne |
> | účty/sharesubscriptions | Ne |
> | účty/sharesubscriptions/consumerSourceDataSets | Ne |
> | účty/sharesubscriptions/datasetmappings | Ne |
> | účty/sharesubscriptions/triggery | Ne |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servery | Ano |
> | servery/poradci | Ne |
> | servery/klíče | Ne |
> | servery/privateEndpointConnectionProxies | Ne |
> | servery/privateEndpointConnections | Ne |
> | servery/privateLinkResources | Ne |
> | servery/queryTexts | Ne |
> | servery/recoverableServers | Ne |
> | servery/spustit | Ne |
> | servery/zastavit | Ne |
> | servery/topQueryStatistics | Ne |
> | servery/virtualNetworkRules | Ne |
> | servery/waitStatistics | Ne |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | flexibleServers | Ano |
> | servery | Ano |
> | servery/poradci | Ne |
> | servery/klíče | Ne |
> | servery/privateEndpointConnectionProxies | Ne |
> | servery/privateEndpointConnections | Ne |
> | servery/privateLinkResources | Ne |
> | servery/queryTexts | Ne |
> | servery/recoverableServers | Ne |
> | servery/spustit | Ne |
> | servery/zastavit | Ne |
> | servery/topQueryStatistics | Ne |
> | servery/upgrade | Ne |
> | servery/virtualNetworkRules | Ne |
> | servery/waitStatistics | Ne |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | flexibleServers | Ano |
> | serverGroups | Ano |
> | servery | Ano |
> | servery/poradci | Ne |
> | servery/klíče | Ne |
> | servery/privateEndpointConnectionProxies | Ne |
> | servery/privateEndpointConnections | Ne |
> | servery/privateLinkResources | Ne |
> | servery/queryTexts | Ne |
> | servery/recoverableServers | Ne |
> | servery/topQueryStatistics | Ne |
> | servery/virtualNetworkRules | Ne |
> | servery/waitStatistics | Ne |
> | serversv2 | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft. DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | artifactSources | Ano |
> | uvádění | Ano |
> | serviceTopologies | Ano |
> | serviceTopologies/služby | Ano |
> | serviceTopologies/služby/serviceUnits | Ano |
> | kroky | Ano |

## <a name="microsoftdesktopvirtualization"></a>Microsoft. DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applicationgroups | Ano |
> | applicationgroups/aplikace | Ne |
> | applicationgroups/desktopy | Ne |
> | applicationgroups / startmenuitems | Ne |
> | hostpools | Ano |
> | hostpools / msixpackages | Ne |
> | hostpools / sessionhosts | Ne |
> | hostpools / sessionhosts / usersessions | Ne |
> | hostpools / usersessions | Ne |
> | pracovní prostory | Ano |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | ElasticPools | Ano |
> | ElasticPools / IotHubTenants | Ano |
> | ElasticPools/IotHubTenants/securitySettings | Ne |
> | IotHubs | Ano |
> | IotHubs/eventGridFilters | Ne |
> | IotHubs/securitySettings | Ne |
> | ProvisioningServices | Ano |
> | použití | Ne |

## <a name="microsoftdeviceupdate"></a>Microsoft. DeviceUpdate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/instance | Ano |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | kanály | Ano |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | kontrolery | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft. DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | labcenters | Ano |
> | Labs | Ano |
> | laboratoře/prostředí | Ano |
> | Labs/serviceRunners | Ano |
> | Labs/virtualMachines | Ano |
> | časových | Ano |

## <a name="microsoftdigitaltwins"></a>Microsoft. DigitalTwins

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | digitalTwinsInstances | Ano |
> | digitalTwinsInstances/koncové body | Ne |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | databaseAccountNames | Ne |
> | databaseAccounts | Ano |
> | restorableDatabaseAccounts | Ne |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | doménu | Ano |
> | domény/domainOwnershipIdentifiers | Ne |
> | generateSsoRequest | Ne |
> | topLevelDomains | Ne |
> | validateDomainRegistrationInformation | Ne |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | lcsprojects | Ne |
> | lcsprojects / clouddeployments | Ne |
> | lcsprojects/konektory | Ne |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | services | Ano |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | doménu | Ano |
> | domény a témata | Ne |
> | eventSubscriptions | Ne |
> | extensionTopics | Ne |
> | partnerNamespaces | Ano |
> | partnerNamespaces/eventChannels | Ne |
> | partnerRegistrations | Ano |
> | partnerTopics | Ano |
> | partnerTopics / eventSubscriptions | Ne |
> | systemTopics | Ano |
> | systemTopics / eventSubscriptions | Ne |
> | popisující | Ano |
> | topicTypes | Ne |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |
> | obsažené | Ano |
> | obory názvů/autorizačních pravidel | Ne |
> | obory názvů/disasterrecoveryconfigs | Ne |
> | obory názvů/eventhubs | Ne |
> | obory názvů/eventhubs/autorizačních pravidel | Ne |
> | obory názvů/eventhubs/consumergroups | Ne |
> | obory názvů/networkrulesets | Ne |
> | obory názvů/privateEndpointConnections | Ne |

## <a name="microsoftexperimentation"></a>Microsoft. experimentování

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | experimentWorkspaces | Ano |

## <a name="microsoftfalcon"></a>Microsoft. Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | obsažené | Ano |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | featureProviders | Ne |
> | funkce | Ne |
> | dodavateli | Ne |
> | subscriptionFeatureRegistrations | Ne |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | registrované | Ne |
> | galleryitems | Ne |
> | generateartifactaccessuri | Ne |
> | myareas | Ne |
> | myareas/oblasti | Ne |
> | myareas/oblasti/oblasti | Ne |
> | myareas/oblasti/oblasti/galleryitems | Ne |
> | myareas/oblasti/galleryitems | Ne |
> | myareas / galleryitems | Ne |
> | register | Ne |
> | resources | Ne |
> | retrieveresourcesbyid | Ne |

## <a name="microsoftgenomics"></a>Microsoft. genomika

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | autoManagedAccounts | Ano |
> | autoManagedVmConfigurationProfiles | Ano |
> | configurationProfileAssignments | Ne |
> | guestConfigurationAssignments | Ne |
> | Vybavení | Ne |
> | softwareUpdateProfile | Ne |
> | softwareUpdates | Ne |

## <a name="microsofthanaonazure"></a>Microsoft. HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hanaInstances | Ano |
> | sapMonitors | Ano |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft. HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dedicatedHSMs | Ano |

## <a name="microsofthdinsight"></a>Microsoft. HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |
> | clustery/aplikace | Ne |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | services | Ano |
> | služby/iomtconnectors | Ne |
> | služby/iomtconnectors/připojení | Ne |
> | služby/iomtconnectors/mapování | Ne |
> | služby/privateEndpointConnectionProxies | Ne |
> | služby/privateEndpointConnections | Ne |
> | služby/privateLinkResources | Ne |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | virtuální | Ano |
> | počítače/assessPatches | Ne |
> | počítače/rozšíření | Ano |
> | počítače/installPatches | Ne |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | datamanagery | Ano |

## <a name="microsofthybridnetwork"></a>Microsoft. HybridNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | zařízení | Ano |
> | networkFunctions | Ano |
> | networkFunctionVendors | Ne |
> | registeredSubscriptions | Ne |
> | dodavateli | Ne |
> | dodavatelé/vendorSkus | Ne |
> | dodavatelé/vendorSkus/previewSubscriptions | Ne |
> | virtualNetworkFunctions | Ano |
> | virtualNetworkFunctionVendors | Ne |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | konstrukční | Ano |
> | networkScopes | Ano |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | úlohy | Ano |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | diagnosticSettings | Ne |
> | diagnosticSettingsCategories | Ne |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | appTemplates | Ne |
> | IoTApps | Ano |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Graph | Ano |

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | deletedVaults | Ne |
> | hsmPools | Ano |
> | managedHSMs | Ano |
> | trezory | Ano |
> | trezory/accessPolicies | Ne |
> | trezory/eventGridFilters | Ne |
> | trezory a klíče | Ne |
> | trezory/klíče/verze | Ne |
> | trezory/tajné klíče | Ne |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | connectedClusters | Ano |
> | registeredSubscriptions | Ne |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SND | Ne |
> | sourceControlConfigurations | Ne |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |
> | clustery/attacheddatabaseconfigurations | Ne |
> | clustery/databáze | Ne |
> | clustery, databáze/datapřipojení | Ne |
> | clustery/databáze/eventhubconnections | Ne |
> | clustery/databáze/principalassignments | Ne |
> | clustery/datapřipojení | Ne |
> | clustery/principalassignments | Ne |
> | clustery/sharedidentities | Ne |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | labaccounts | Ano |
> | uživatelé | Ne |

## <a name="microsoftlogic"></a>Microsoft. Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hostingEnvironments | Ano |
> | integrationAccounts | Ano |
> | integrationServiceEnvironments | Ano |
> | integrationServiceEnvironments/Inspirujte | Ano |
> | isolatedEnvironments | Ano |
> | Zpracovávaný | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft. MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | commitmentPlans | Ano |
> | webServices | Ano |
> | Pracovní prostory | Ano |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | pracovní prostory | Ano |
> | pracovní prostory/batchEndpoints | Ano |
> | pracovní prostory/batchEndpointsy/nasazení | Ano |
> | pracovní prostory/kódy | Ne |
> | pracovní prostory/kódy/verze | Ne |
> | pracovní prostory a výpočetní prostředky | Ne |
> | pracovní prostory/úložiště dat | Ne |
> | pracovní prostory/eventGridFilters | Ne |
> | pracovní prostory/úlohy | Ne |
> | pracovní prostory/labelingJobs | Ne |
> | pracovní prostory/linkedServices | Ne |
> | pracovní prostory a modely | Ne |
> | pracovní prostory/modely/verze | Ne |
> | pracovní prostory/onlineEndpoints | Ano |
> | pracovní prostory/onlineEndpointsy/nasazení | Ano |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applyUpdates | Ne |
> | configurationAssignments | Ne |
> | maintenanceConfigurations | Ano |
> | publicMaintenanceConfigurations | Ne |
> | aktualizovány | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Identity | Ne |
> | userAssignedIdentities | Ano |

## <a name="microsoftmanagednetwork"></a>Microsoft. ManagedNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | managedNetworks | Ano |
> | managedNetworks / managedNetworkGroups | Ano |
> | managedNetworks / managedNetworkPeeringPolicies | Ano |
> | oznámení | Ano |

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Ne |
> | registrationAssignments | Ne |
> | registrationDefinitions | Ne |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | getentities | Ne |
> | managementGroups | Ne |
> | managementGroups/nastavení | Ne |
> | resources | Ne |
> | startTenantBackfill | Ne |
> | tenantBackfillStatus | Ne |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/eventGridFilters | Ne |
> | účty/privateAtlases | Ano |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | macc | Ne |
> | Rozsah | Ne |
> | offerTypes | Ne |
> | offerTypes/vydavatelé | Ne |
> | offerTypes/vydavatelé/nabídky | Ne |
> | offerTypes/vydavatelé/nabídky/plány | Ne |
> | offerTypes/vydavatelé/nabídky/plány/smlouvy | Ne |
> | offerTypes/vydavatelé/nabídky/plány/konfigurace | Ne |
> | offerTypes/vydavatelé/nabídky/plány/konfigurace/importImage | Ne |
> | privategalleryitems | Ne |
> | privateStoreClient | Ne |
> | privateStores | Ne |
> | privateStores/nabídky | Ne |
> | produktech | Ne |
> | zdrojů | Ne |
> | Vydavatelé/nabídky | Ne |
> | Vydavatelé/nabídky/změny | Ne |
> | register | Ne |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | classicDevServices | Ano |
> | updateCommunicationPreference | Ne |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | o | Ne |
> | offertypes | Ne |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | MediaServices | Ano |
> | MediaServices/accountFilters | Ne |
> | MediaServices/assety | Ne |
> | MediaServices/assety/assetFilters | Ne |
> | MediaServices/contentKeyPolicies | Ne |
> | MediaServices/eventGridFilters | Ne |
> | MediaServices/liveEventOperations | Ne |
> | MediaServices/liveEvents | Ano |
> | MediaServices/liveEvents/liveOutputs | Ne |
> | MediaServices/liveOutputOperations | Ne |
> | MediaServices/mediaGraphs | Ne |
> | MediaServices/privateEndpointConnectionOperations | Ne |
> | MediaServices/privateEndpointConnectionProxies | Ne |
> | MediaServices/privateEndpointConnections | Ne |
> | MediaServices/streamingEndpointOperations | Ne |
> | MediaServices/starají | Ano |
> | MediaServices/streamingLocators | Ne |
> | MediaServices/streamingPolicies | Ne |
> | MediaServices/transformace | Ne |
> | MediaServices/transformace/úlohy | Ne |

## <a name="microsoftmicroservices4spring"></a>Microsoft. Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | appClusters | Ano |

## <a name="microsoftmigrate"></a>Microsoft. migruje

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | assessmentProjects | Ano |
> | migrateprojects | Ano |
> | moveCollections | Ano |
> | projekty | Ano |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Ano |
> | objectUnderstandingAccounts | Ano |
> | remoteRenderingAccounts | Ano |
> | spatialAnchorsAccounts | Ano |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | netAppAccounts | Ano |
> | netAppAccounts / accountBackups | Ne |
> | netAppAccounts / capacityPools | Ano |
> | netAppAccounts/capacityPools/svazky | Ano |
> | netAppAccounts/capacityPools/svazky/snímky | Ne |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applicationGateways | Ano |
> | applicationGatewayWebApplicationFirewallPolicies | Ano |
> | applicationSecurityGroups | Ano |
> | azureFirewallFqdnTags | Ne |
> | azureFirewalls | Ano |
> | bastionHosts | Ano |
> | bgpServiceCommunities | Ne |
> | připojení | Ano |
> | ddosCustomPolicies | Ano |
> | ddosProtectionPlans | Ano |
> | dnsOperationStatuses | Ne |
> | dnszones | Ano |
> | dnszones/A | Ne |
> | dnszones/AAAA | Ne |
> | dnszones/vše | Ne |
> | dnszones/CAA | Ne |
> | dnszones/CNAME | Ne |
> | dnszones/MX | Ne |
> | dnszones/NS | Ne |
> | dnszones/PTR | Ne |
> | dnszones/sady záznamů | Ne |
> | dnszones/SOA | Ne |
> | dnszones/SRV | Ne |
> | dnszones/TXT | Ne |
> | expressRouteCircuits | Ano |
> | expressRouteCrossConnections | Ano |
> | expressRouteGateways | Ano |
> | expressRoutePorts | Ano |
> | expressRouteServiceProviders | Ne |
> | firewallPolicies | Ano |
> | frontdoors | Ano |
> | frontdoorWebApplicationFirewallManagedRuleSets | Ne |
> | frontdoorWebApplicationFirewallPolicies | Ano |
> | getDnsResourceReference | Ne |
> | internalNotify | Ne |
> | ipGroups | Ano |
> | loadBalancers | Ano |
> | localNetworkGateways | Ano |
> | natGateways | Ano |
> | networkIntentPolicies | Ano |
> | networkInterfaces | Ano |
> | networkProfiles | Ano |
> | networkSecurityGroups | Ano |
> | networkWatchers | Ano |
> | networkWatchers / connectionMonitors | Ano |
> | networkWatchers / flowLogs | Ano |
> | networkWatchers/čočky | Ano |
> | networkWatchers / pingMeshes | Ano |
> | p2sVpnGateways | Ano |
> | privateDnsOperationStatuses | Ne |
> | privateDnsZones | Ano |
> | privateDnsZones/A | Ne |
> | privateDnsZones/AAAA | Ne |
> | privateDnsZones/vše | Ne |
> | privateDnsZones/CNAME | Ne |
> | privateDnsZones/MX | Ne |
> | privateDnsZones/PTR | Ne |
> | privateDnsZones/SOA | Ne |
> | privateDnsZones/SRV | Ne |
> | privateDnsZones/TXT | Ne |
> | privateDnsZones / virtualNetworkLinks | Ano |
> | privateEndpoints | Ano |
> | privateLinkServices | Ano |
> | publicIPAddresses | Ano |
> | publicIPPrefixes | Ano |
> | routeFilters | Ano |
> | routeTables | Ano |
> | serviceEndpointPolicies | Ano |
> | trafficManagerGeographicHierarchies | Ne |
> | trafficmanagerprofiles | Ano |
> | trafficmanagerprofiles/Heat mapy | Ne |
> | trafficManagerUserMetricsKeys | Ne |
> | virtualHubs | Ano |
> | virtualNetworkGateways | Ano |
> | virtualNetworks | Ano |
> | virtualNetworks/podsítě | Ne |
> | virtualNetworkTaps | Ano |
> | virtualWans | Ano |
> | vpnGateways | Ano |
> | vpnSites | Ano |
> | webApplicationFirewallPolicies | Ano |

## <a name="microsoftnotebooks"></a>Microsoft. poznámkové bloky

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | NotebookProxies | Ne |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | obsažené | Ano |
> | obory názvů/notificationHubs | Ano |

## <a name="microsoftobjectstore"></a>Microsoft. ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | osNamespaces | Ano |

## <a name="microsoftoffazure"></a>Microsoft. OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | HyperVSites | Ano |
> | ImportSites | Ano |
> | MasterSites | Ano |
> | ServerSites | Ano |
> | VMwareSites | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |
> | deletedWorkspaces | Ne |
> | linkTargets | Ne |
> | storageInsightConfigs | Ne |
> | pracovní prostory | Ano |
> | pracovní prostory/dataexporty | Ne |
> | pracovní prostory/zdroje dat | Ne |
> | pracovní prostory/linkedServices | Ne |
> | pracovní prostory/linkedStorageAccounts | Ne |
> | pracovní prostory/metadata | Ne |
> | pracovní prostory a dotazy | Ne |
> | pracovní prostory/scopedPrivateLinkProxies | Ne |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | managementassociations | Ne |
> | managementconfigurations | Ano |
> | Řešení | Ano |
> | zobrazení | Ano |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | legacyPeerings | Ne |
> | peerAsns | Ne |
> | partnerské vztahy | Ano |
> | peeringServiceCountries | Ne |
> | peeringServiceProviders | Ne |
> | peeringServices | Ano |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | atestace | Ne |
> | policyEvents | Ne |
> | policyMetadata | Ne |
> | policyStates | Ne |
> | policyTrackedResources | Ne |
> | nápravy | Ne |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Konzoly nástroje | Ne |
> | řídicí panely | Ano |
> | userSettings | Ne |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Ano |
> | tenantů | Ano |
> | klienti/pracovní prostory | Ne |
> | workspaceCollections | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | schopností | Ano |

## <a name="microsoftprojectbabylon"></a>Microsoft. ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | deletedAccounts | Ne |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | providerRegistrations | Ne |
> | providerRegistrations / defaultRollouts | Ne |
> | providerRegistrations / resourceTypeRegistrations | Ne |
> | uvádění | Ano |

## <a name="microsoftquantum"></a>Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Pracovní prostory | Ano |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | backupProtectedItems | Ne |
> | trezory | Ano |

## <a name="microsoftredhatopenshift"></a>Microsoft. RedHatOpenShift

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | OpenShiftClusters | Ano |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | obsažené | Ano |
> | obory názvů/autorizačních pravidel | Ne |
> | obory názvů/hybridconnections | Ne |
> | obory názvů/hybridconnections/autorizačních pravidel | Ne |
> | obory názvů/privateEndpointConnections | Ne |
> | obory názvů/wcfrelays | Ne |
> | obory názvů/wcfrelays/autorizačních pravidel | Ne |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | odešle | Ano |
> | resourceChangeDetails | Ne |
> | resourceChanges | Ne |
> | resources | Ne |
> | resourcesHistory | Ne |
> | subscriptionsStatus | Ne |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | availabilityStatuses | Ne |
> | childAvailabilityStatuses | Ne |
> | childResources | Ne |
> | emergingissues | Ne |
> | stránka events | Ne |
> | impactedResources | Ne |
> | zprostředkovatele identity | Ne |
> | připomenutí | Ne |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | calculateTemplateHash | Ne |
> | nasazení | Ne |
> | nasazení/operace | Ne |
> | deploymentScripts | Ano |
> | deploymentScripts/protokoly | Ne |
> | odkazy | Ne |
> | notifyResourceJobs | Ne |
> | dodavateli | Ne |
> | resourceGroups | Ne |
> | odběru | Ne |
> | templateSpecs | Ano |
> | templateSpecs/verze | Ano |
> | tenantů | Ne |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikace | Ano |
> | saasresources | Ne |

## <a name="microsoftscvmm"></a>Microsoft. ScVmm

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Cloud | Ano |
> | VirtualMachines | Ano |
> | VirtualMachineTemplates | Ano |
> | VirtualNetworks | Ano |
> | vmmservers | Ano |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | resourceHealthMetadata | Ne |
> | searchServices | Ano |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Ne |
> | advancedThreatProtectionSettings | Ne |
> | výstrahy | Ne |
> | alertsSuppressionRules | Ne |
> | allowedConnections | Ne |
> | applicationWhitelistings | Ne |
> | assessmentMetadata | Ne |
> | hodnocení | Ne |
> | autoDismissAlertsRules | Ne |
> | automatizace | Ano |
> | AutoProvisioningSettings | Ne |
> | Předpisů | Ne |
> | konektory | Ne |
> | dataCollectionAgents | Ne |
> | deviceSecurityGroups | Ne |
> | discoveredSecuritySolutions | Ne |
> | externalSecuritySolutions | Ne |
> | InformationProtectionPolicies | Ne |
> | iotDefenderSettings | Ne |
> | iotSecuritySolutions | Ano |
> | iotSecuritySolutions / analyticsModels | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Ne |
> | iotSecuritySolutions / iotAlerts | Ne |
> | iotSecuritySolutions / iotAlertTypes | Ne |
> | iotSecuritySolutions / iotRecommendations | Ne |
> | iotSecuritySolutions / iotRecommendationTypes | Ne |
> | iotSensors | Ne |
> | jitNetworkAccessPolicies | Ne |
> | jitPolicies | Ne |
> | Zásady | Ne |
> | ceny | Ne |
> | regulatoryComplianceStandards | Ne |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Ne |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Ne |
> | secureScoreControlDefinitions | Ne |
> | secureScoreControls | Ne |
> | secureScores | Ne |
> | secureScores / secureScoreControls | Ne |
> | securityContacts | Ne |
> | securitySolutions | Ne |
> | securitySolutionsReferenceData | Ne |
> | securityStatuses | Ne |
> | securityStatusesSummaries | Ne |
> | serverVulnerabilityAssessments | Ne |
> | nastavení | Ne |
> | sqlVulnerabilityAssessments | Ne |
> | podhodnocení | Ne |
> | úlohy | Ne |
> | topologie | Ne |
> | workspaceSettings | Ne |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | diagnosticSettings | Ne |
> | diagnosticSettingsCategories | Ne |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | agregace | Ne |
> | alertRules | Ne |
> | alertRuleTemplates | Ne |
> | automationRules | Ne |
> | záložky | Ne |
> | věcech | Ne |
> | dataconnects | Ne |
> | dataConnectorsCheckRequirements | Ne |
> | podnikům | Ne |
> | entityQueries | Ne |
> | Incidenty | Ne |
> | officeConsents | Ne |
> | nastavení | Ne |
> | threatIntelligence | Ne |
> | watchlists | Ne |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | consoleServices | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | obsažené | Ano |
> | obory názvů/autorizačních pravidel | Ne |
> | obory názvů/disasterrecoveryconfigs | Ne |
> | obory názvů/eventgridfilters | Ne |
> | obory názvů/networkrulesets | Ne |
> | obory názvů/privateEndpointConnections | Ne |
> | obory názvů/fronty | Ne |
> | obory názvů/fronty/autorizačních pravidel | Ne |
> | obory názvů/témata | Ne |
> | obory názvů/témata/autorizačních pravidel | Ne |
> | obory názvů/témata/předplatná | Ne |
> | obory názvů/témata/předplatná/pravidla | Ne |
> | premiumMessagingRegions | Ne |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikace | Ano |
> | existující | Ano |
> | clustery/aplikace | Ne |
> | containerGroups | Ano |
> | containerGroupSets | Ano |
> | edgeclusters | Ano |
> | edgeclusters/aplikace | Ne |
> | managedclusters | Ano |
> | managedclusters/nodetypes | Ne |
> | sítí | Ano |
> | secretstores | Ano |
> | secretstores/certifikáty | Ne |
> | secretstores/tajné kódy | Ne |
> | volumes | Ano |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikace | Ano |
> | containerGroups | Ano |
> | brány | Ano |
> | sítí | Ano |
> | záleží | Ano |
> | volumes | Ano |

## <a name="microsoftservices"></a>Microsoft. Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | providerRegistrations | Ne |
> | providerRegistrations / resourceTypeRegistrations | Ne |
> | uvádění | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SignalR | Ano |
> | Signál/eventGridFilters | Ne |

## <a name="microsoftsingularity"></a>Microsoft. jednotného

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/accountQuotaPolicies | Ne |
> | účty/groupPolicies | Ne |
> | účty/úlohy | Ne |
> | účty/storageContainers | Ne |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hybridUseBenefits | Ne |

## <a name="microsoftsolutions"></a>Microsoft. Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applicationDefinitions | Ano |
> | aplikace | Ano |
> | jitRequests | Ano |

## <a name="microsoftsql"></a>Microsoft. SQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | managedInstances | Ano |
> | managedInstances/databáze | Ano |
> | managedInstances/databáze/backupShortTermRetentionPolicies | Ne |
> | managedInstances/databáze/schémata/tabulky/sloupce/sensitivityLabels | Ne |
> | managedInstances/databáze/vulnerabilityAssessments | Ne |
> | managedInstances/databáze/vulnerabilityAssessments/Rules/směrné plány | Ne |
> | managedInstances / encryptionProtector | Ne |
> | managedInstances/klíče | Ne |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Ne |
> | managedInstances / vulnerabilityAssessments | Ne |
> | servery | Ano |
> | servery/správci | Ne |
> | servery/communicationLinks | Ne |
> | servery/databáze | Ano |
> | servery/encryptionProtector | Ne |
> | servery/firewallRules | Ne |
> | servery/klíče | Ne |
> | servery/restorableDroppedDatabases | Ne |
> | servery/serviceobjectives | Ne |
> | servery/tdeCertificates | Ne |
> | virtualClusters | Ne |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Ano |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Ne |
> | SqlVirtualMachines | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | deletedAccounts | Ne |
> | storageAccounts | Ano |
> | storageAccounts/blobServices | Ne |
> | storageAccounts/služby | Ne |
> | storageAccounts/queueServices | Ne |
> | storageAccounts/služby | Ne |
> | storageAccounts/služby/metricDefinitions | Ne |
> | storageAccounts/tableServices | Ne |
> | použití | Ne |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | mezipaměti | Ano |
> | mezipaměti/storageTargets | Ne |
> | usageModels | Ne |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | replicationGroups | Ne |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices/registeredServer | Ne |
> | storageSyncServices / syncGroups | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne |
> | storageSyncServices/pracovní postupy | Ne |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices/registeredServer | Ne |
> | storageSyncServices / syncGroups | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne |
> | storageSyncServices/pracovní postupy | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices/registeredServer | Ne |
> | storageSyncServices / syncGroups | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne |
> | storageSyncServices/pracovní postupy | Ne |

## <a name="microsoftstorsimple"></a>Microsoft. StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | jednatel | Ano |

## <a name="microsoftstreamanalytics"></a>Microsoft. StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |
> | clustery/privateEndpoints | Ne |
> | streamingjobs | Ano |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | acceptChangeTenant | Ne |
> | hromad | Ne |
> | cancel | Ne |
> | changeTenantRequest | Ne |
> | changeTenantStatus | Ne |
> | CreateSubscription | Ne |
> | aby | Ne |
> | přejmenovat | Ne |
> | SubscriptionDefinitions | Ne |
> | SubscriptionOperations | Ne |
> | odběru | Ne |

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | privateLinkHubs | Ano |
> | pracovní prostory | Ano |
> | pracovní prostory/bigDataPools | Ano |
> | pracovní prostory/operationStatuses | Ne |
> | pracovní prostory/sqlDatabases | Ano |
> | pracovní prostory/sqlPools | Ano |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Environment | Ano |
> | prostředí/accessPolicies | Ne |
> | prostředí/EventSources | Ano |
> | prostředí/referenceDataSets | Ano |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Store | Ano |
> | obchody/accessPolicies | Ne |
> | obchody/služby | Ne |
> | úložiště/služby/tokeny | Ne |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | imageTemplates | Ano |
> | imageTemplates / runOutputs | Ne |

## <a name="microsoftvmware"></a>Microsoft. VMware

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | ArcZones | Ano |
> | ResourcePools | Ano |
> | Servery vCenter | Ano |
> | VirtualMachines | Ano |
> | VirtualMachineTemplates | Ano |
> | VirtualNetworks | Ano |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ano |
> | dedicatedCloudServices | Ano |
> | virtualMachines | Ano |

## <a name="microsoftvnfmanager"></a>Microsoft. VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | zařízení | Ano |
> | registeredSubscriptions | Ne |
> | dodavateli | Ne |
> | dodavatelé/SKU | Ne |
> | dodavatelé/vnfs | Ne |
> | virtualNetworkFunctionSkus | Ne |
> | vnfs | Ano |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | plánují | Ano |
> | registeredSubscriptions | Ne |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | apiManagementAccounts | Ne |
> | apiManagementAccounts / apiAcls | Ne |
> | apiManagementAccounts/rozhraní API | Ne |
> | apiManagementAccounts/API/apiAcls | Ne |
> | apiManagementAccounts/API/connectionAcls | Ne |
> | apiManagementAccounts/rozhraní API/připojení | Ne |
> | apiManagementAccounts/rozhraní API/připojení/connectionAcls | Ne |
> | apiManagementAccounts/API/localizedDefinitions | Ne |
> | apiManagementAccounts / connectionAcls | Ne |
> | apiManagementAccounts/připojení | Ne |
> | billingMeters | Ne |
> | certifikáty | Ano |
> | connectionGateways | Ano |
> | připojení | Ano |
> | customApis | Ano |
> | deletedSites | Ne |
> | hostingEnvironments | Ano |
> | hostingEnvironments / eventGridFilters | Ne |
> | hostingEnvironments / multiRolePools | Ne |
> | hostingEnvironments / workerPools | Ne |
> | kubeEnvironments | Ano |
> | publishingUsers | Ne |
> | doporučit | Ne |
> | resourceHealthMetadata | Ne |
> | moduly runtime | Ne |
> | Serverových farem | Ano |
> | Serverových farem/eventGridFilters | Ne |
> | Serverových farem/firstPartyApps | Ne |
> | Serverových farem/firstPartyApps/keyVaultSettings | Ne |
> | místa | Ano |
> | lokality/konfigurace  | Ne |
> | lokality/eventGridFilters | Ne |
> | lokality/hostNameBindings | Ne |
> | lokality/networkConfig | Ne |
> | lokality/premieraddons | Ano |
> | lokality/sloty | Ano |
> | lokality/sloty/eventGridFilters | Ne |
> | lokality/sloty/hostNameBindings | Ne |
> | lokality/sloty/networkConfig | Ne |
> | sourceControls | Ne |
> | staticSites | Ano |
> | oproti | Ne |
> | verifyHostingEnvironmentVnet | Ne |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | diagnosticSettings | Ne |
> | diagnosticSettingsCategories | Ne |

## <a name="microsoftwindowsesu"></a>Microsoft. WindowsESU

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | multipleActivationKeys | Ano |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | DeviceServices | Ano |

## <a name="microsoftworkloadbuilder"></a>Microsoft. WorkloadBuilder

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | úlohy | Ano |
> | úlohy/instance | Ne |
> | zatížení a verze | Ne |
> | úlohy/verze/artefakty | Ne |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | konstrukční | Ne |
> | componentsSummary | Ne |
> | monitorInstances | Ne |
> | monitorInstancesSummary | Ne |
> | Monitor | Ne |
> | notificationSettings | No |

## <a name="next-steps"></a>Další kroky

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

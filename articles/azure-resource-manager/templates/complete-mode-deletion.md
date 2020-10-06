---
title: Úplné odstranění režimu
description: Ukazuje, jak typy prostředků zpracovávají úplné odstraňování režimu v Azure Resource Manager šablonách.
ms.topic: conceptual
ms.date: 10/06/2020
ms.openlocfilehash: 72303a7916aec39c05f9b4fa2cbc77de18b7fb3e
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766708"
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
> | DomainServices / oucontainer | No |

## <a name="microsoftaddons"></a>Microsoft. addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | supportProviders | No |

## <a name="microsoftadhybridhealthservice"></a>Microsoft. ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aadsupportcases | No |
> | addsservices | No |
> | technici | No |
> | anonymousapiusers | No |
> | konfigurace | No |
> | Protokoly | No |
> | reports | No |
> | servicehealthmetrics | No |
> | services | No |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | advisorScore | No |
> | konfiguračních | No |
> | generateRecommendations | No |
> | zprostředkovatele identity | No |
> | doporučit | No |
> | potlačení | No |

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
> | výstrahy | No |
> | alertsList | No |
> | alertsMetaData | No |
> | alertsSummary | No |
> | alertsSummaryList | No |
> | smartDetectorAlertRules | Ano |
> | smartGroups | No |

## <a name="microsoftanalysisservices"></a>Microsoft. AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servery | Ano |

## <a name="microsoftapimanagement"></a>Microsoft. ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | reportFeedback | No |
> | service | Ano |
> | validateServiceName | No |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | configurationStores | Ano |
> | configurationStores / eventGridFilters | No |
> | configurationStores/hodnoty | No |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Spring | Ano |
> | Pružina/aplikace | No |
> | Jarní/aplikace/nasazení | No |

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | attestationProviders | Ano |
> | defaultProvider – neexistují | No |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accessReviewScheduleDefinitions | No |
> | accessReviewScheduleSettings | No |
> | classicAdministrators | No |
> | dataaliasy | No |
> | denyAssignments | No |
> | elevateAccess | No |
> | findOrphanRoleAssignments | No |
> | počtu | No |
> | oprávnění | No |
> | policyAssignments | No |
> | policyDefinitions | No |
> | policyExemptions | No |
> | policySetDefinitions | No |
> | privateLinkAssociations | No |
> | providerOperations | No |
> | resourceManagementPrivateLinks | Ano |
> | roleAssignments | No |
> | roleAssignmentsUsageMetrics | No |
> | roleDefinitions | No |

## <a name="microsoftautomanage"></a>Microsoft. automanage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | configurationProfileAssignments | No |
> | configurationProfilePreferences | Ano |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | automationAccounts | Ano |
> | automationAccounts/konfigurace | Ano |
> | automationAccounts/úlohy | No |
> | automationAccounts / privateEndpointConnectionProxies | No |
> | automationAccounts / privateEndpointConnections | No |
> | automationAccounts / privateLinkResources | No |
> | automationAccounts/Runbooky | Ano |
> | automationAccounts / softwareUpdateConfigurations | No |
> | automationAccounts nebo Webhooky | No |

## <a name="microsoftavs"></a>Microsoft. AVS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | privateClouds | Ano |
> | privateClouds/autorizace | No |
> | privateClouds/clustery | No |
> | privateClouds / globalReachConnections | No |
> | privateClouds / hcxEnterpriseSites | No |
> | privateClouds / workloadNetworks | No |
> | privateClouds / workloadNetworks / dhcpConfigurations | No |
> | privateClouds/workloadNetworks/Brans | No |
> | privateClouds / workloadNetworks / portMirroringProfiles | No |
> | privateClouds/workloadNetworks/segmenty | No |
> | privateClouds / workloadNetworks / virtualMachines | No |
> | privateClouds / workloadNetworks / vmGroups | No |

## <a name="microsoftazuregeneva"></a>Microsoft. Azure. Ženeva

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Environment | No |
> | prostředí/účty | No |
> | prostředí/účty/obory názvů | No |
> | prostředí/účty/obory názvů/konfigurace | No |

## <a name="microsoftazureactivedirectory"></a>Microsoft. Azureactivedirectory selhala

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | b2cDirectories | Ano |
> | b2ctenants | No |
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
> | sqlServerRegistrations/sqlServers | No |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | cloudManifestFiles | No |
> | edgeSubscriptions | Ano |
> | linkedSubscriptions | Ano |
> | rozpoznávání | Ano |
> | registrace/customerSubscriptions | No |
> | registrace/produkty | No |

## <a name="microsoftazurestackhci"></a>Microsoft. AzureStackHCI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | batchAccounts | Ano |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | billingAccounts | No |
> | billingAccounts/smlouvy | No |
> | billingAccounts / billingPermissions | No |
> | billingAccounts / billingProfiles | No |
> | billingAccounts / billingProfiles / billingPermissions | No |
> | billingAccounts / billingProfiles / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / billingSubscriptions | No |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | No |
> | billingAccounts/billingProfiles/zákazníci | No |
> | billingAccounts/billingProfiles/– pokyny | No |
> | billingAccounts/billingProfiles/faktury | No |
> | billingAccounts/billingProfiles/faktury/pricesheet | No |
> | billingAccounts/billingProfiles/faktury/transakce | No |
> | billingAccounts / billingProfiles / invoiceSections | No |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | No |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | No |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | No |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | No |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | No |
> | billingAccounts/billingProfiles/invoiceSections/Products | No |
> | billingAccounts/billingProfiles/invoiceSections/Products/Transfer | No |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | No |
> | billingAccounts/billingProfiles/invoiceSections/transakce | No |
> | billingAccounts/billingProfiles/invoiceSections/transfery | No |
> | billingAccounts / BillingProfiles / patchOperations | No |
> | billingAccounts / billingProfiles / paymentMethods | No |
> | billingAccounts/billingProfiles/– zásady | No |
> | billingAccounts/billingProfiles/pricesheet | No |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | No |
> | billingAccounts/billingProfiles/produkty | No |
> | billingAccounts/billingProfiles/rezervace | No |
> | billingAccounts/billingProfiles/transakcí | No |
> | billingAccounts / billingProfiles / validateDetachPaymentMethodEligibility | No |
> | billingAccounts / billingRoleAssignments | No |
> | billingAccounts / billingRoleDefinitions | No |
> | billingAccounts / billingSubscriptions | No |
> | billingAccounts/billingSubscriptions/faktury | No |
> | billingAccounts / createBillingRoleAssignment | No |
> | billingAccounts / createInvoiceSectionOperations | No |
> | billingAccounts/zákazníci | No |
> | billingAccounts/Customers/billingPermissions | No |
> | billingAccounts/Customers/billingSubscriptions | No |
> | billingAccounts/Customers/initiateTransfer | No |
> | billingAccounts/Customers/policies | No |
> | billingAccounts/zákazníci/produkty | No |
> | billingAccounts/zákazníci/transakce | No |
> | billingAccounts/zákazníci/přenosy | No |
> | billingAccounts/oddělení | No |
> | billingAccounts/oddělení/billingPermissions | No |
> | billingAccounts/oddělení/billingRoleAssignments | No |
> | billingAccounts/oddělení/billingRoleDefinitions | No |
> | billingAccounts / enrollmentAccounts | No |
> | billingAccounts / enrollmentAccounts / billingPermissions | No |
> | billingAccounts / enrollmentAccounts / billingRoleAssignments | No |
> | billingAccounts / enrollmentAccounts / billingRoleDefinitions | No |
> | billingAccounts/faktury | No |
> | billingAccounts/faktury/transakce | No |
> | billingAccounts / invoiceSections | No |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | No |
> | billingAccounts / invoiceSections / billingSubscriptions | No |
> | billingAccounts/invoiceSections/billingSubscriptions/Transfer | No |
> | billingAccounts/invoiceSections/zvýšení oprávnění | No |
> | billingAccounts / invoiceSections / initiateTransfer | No |
> | billingAccounts / invoiceSections / patchOperations | No |
> | billingAccounts / invoiceSections / productMoveOperations | No |
> | billingAccounts/invoiceSections/produkty | No |
> | billingAccounts/invoiceSections/produkty/přenos | No |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | No |
> | billingAccounts/invoiceSections/transakcí | No |
> | billingAccounts/invoiceSections/transfery | No |
> | billingAccounts / lineOfCredit | No |
> | billingAccounts / patchOperations | No |
> | billingAccounts / paymentMethods | No |
> | billingAccounts/produkty | No |
> | billingAccounts/rezervace | No |
> | billingAccounts/transakce | No |
> | billingPeriods | No |
> | billingPermissions | No |
> | billingProperty | No |
> | billingRoleAssignments | No |
> | billingRoleDefinitions | No |
> | createBillingRoleAssignment | No |
> | oddělení | No |
> | enrollmentAccounts | No |
> | faktury | No |
> | Převede | No |
> | přenosy/acceptTransfer | No |
> | přenosy/declineTransfer | No |
> | přenosy/stav operationstatus | No |
> | přenosy/validateTransfer | No |
> | validateAddress | No |

## <a name="microsoftbingmaps"></a>Microsoft. BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | mapApis | Ano |
> | updateCommunicationPreference | No |

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
> | TokenServices / BlockchainNetworks | No |
> | TokenServices/skupiny | No |
> | TokenServices/skupiny/účty | No |
> | TokenServices / TokenTemplates | No |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | blueprintAssignments | No |
> | blueprintAssignments / assignmentOperations | No |
> | blueprintAssignments/operace | No |
> | podrobné plány | No |
> | modrotisky/artefakty | No |
> | plány/verze | No |
> | modrotisky/verze/artefakty | No |

## <a name="microsoftbotservice"></a>Microsoft. BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | botServices | Ano |
> | botServices/kanály | No |
> | botServices/připojení | No |
> | jazyky | No |
> | šablony | No |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Redis | Ano |
> | Redis/EventGridFilters | No |
> | Redis/privateEndpointConnectionProxies | No |
> | Redis/privateEndpointConnectionProxies/ověřit | No |
> | Redis/privateEndpointConnections | No |
> | Redis/privateLinkResources | No |
> | redisEnterprise | Ano |
> | RedisEnterprise / privateEndpointConnectionProxies | No |
> | RedisEnterprise/privateEndpointConnectionProxies/ověřit | No |
> | RedisEnterprise / privateEndpointConnections | No |
> | RedisEnterprise / privateLinkResources | No |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | appliedReservations | No |
> | autoQuotaIncrease | No |
> | calculateExchange | No |
> | calculatePrice | No |
> | calculatePurchasePrice | No |
> | spustí | No |
> | commercialReservationOrders | No |
> | výměn | No |
> | ownReservations | No |
> | placePurchaseOrder | No |
> | reservationOrders | No |
> | reservationOrders / calculateRefund | No |
> | reservationOrders/sloučit | No |
> | reservationOrders/rezervace | No |
> | reservationOrders/rezervace/Revize | No |
> | reservationOrders/vrátit | No |
> | reservationOrders/Split | No |
> | reservationOrders/swap | No |
> | rezervace | No |
> | resourceProviders | No |
> | resources | No |
> | validateReservationOrder | No |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | No |
> | CdnWebApplicationFirewallPolicies | Ano |
> | edgenodes | No |
> | uživatelů | Ano |
> | profily/koncové body | Ano |
> | profily/koncové body/customdomains | No |
> | profily/koncové body/origingroups | No |
> | profily/koncové body/zdroje | No |
> | validateProbe | No |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | certificateOrders | Ano |
> | certificateOrders/certifikáty | No |
> | validateCertificateRegistrationInformation | No |

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | profil | No |
> | resourceChanges | No |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | možnosti | No |
> | domainNames | Ano |
> | domainNames/možnosti | No |
> | domainNames / internalLoadBalancers | No |
> | domainNames / serviceCertificates | No |
> | domainNames/sloty | No |
> | domainNames/sloty/role | No |
> | domainNames/sloty/role/metricDefinitions | No |
> | domainNames/sloty/role/metriky | No |
> | moveSubscriptionResources | No |
> | operatingSystemFamilies | No |
> | operatingSystems | No |
> | quotas | No |
> | Typ prostředků | No |
> | validateSubscriptionMoveAvailability | No |
> | virtualMachines | Ano |
> | virtualMachines / diagnosticSettings | No |
> | virtualMachines / metricDefinitions | No |
> | virtualMachines/metriky | No |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft. ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | classicInfrastructureResources | No |

## <a name="microsoftclassicnetwork"></a>Microsoft. ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | možnosti | No |
> | expressRouteCrossConnections | No |
> | expressRouteCrossConnections/partnerské vztahy | No |
> | gatewaySupportedDevices | No |
> | networkSecurityGroups | Ano |
> | quotas | No |
> | reservedIps | Ano |
> | virtualNetworks | Ano |
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | No |
> | virtualNetworks/virtualNetworkPeerings | No |

## <a name="microsoftclassicstorage"></a>Microsoft. ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | možnosti | No |
> | disků | No |
> | images | No |
> | OSImage | No |
> | osPlatformImages | No |
> | publicImages | No |
> | quotas | No |
> | storageAccounts | Ano |
> | storageAccounts/blobServices | No |
> | storageAccounts/služby | No |
> | storageAccounts/metricDefinitions | No |
> | storageAccounts/metriky | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/služby | No |
> | storageAccounts/služby/diagnosticSettings | No |
> | storageAccounts/služby/metricDefinitions | No |
> | storageAccounts/služby/metriky | No |
> | storageAccounts/tableServices | No |
> | storageAccounts/vmImages | No |
> | vmImages | No |

## <a name="microsoftcodespaces"></a>Microsoft. Codespaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | plánují | Ano |
> | registeredSubscriptions | No |

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/privateEndpointConnectionProxies | No |
> | účty/privateEndpointConnections | No |
> | účty/privateLinkResources | No |

## <a name="microsoftcommerce"></a>Microsoft. Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | RateCard | No |
> | UsageAggregates | No |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | availabilitySets | Ano |
> | cloudServices | Ano |
> | cloudServices / networkInterfaces | No |
> | cloudServices/publicIPAddresses | No |
> | cloudServices / roleInstances | No |
> | cloudServices / roleInstances / networkInterfaces | No |
> | cloudServices/role | No |
> | diskAccesses | Ano |
> | diskEncryptionSets | Ano |
> | disků | Ano |
> | Galerie | Ano |
> | Galerie/aplikace | No |
> | Galerie/aplikace/verze | No |
> | Galerie/image | No |
> | Galerie/image/verze | No |
> | hostGroups | Ano |
> | hostGroups/hostitelé | Ano |
> | images | Ano |
> | proximityPlacementGroups | Ano |
> | restorePointCollections | Ano |
> | restorePointCollections / restorePoints | No |
> | sharedVMExtensions | Ano |
> | sharedVMExtensions/verze | No |
> | sharedVMImages | Ano |
> | sharedVMImages/verze | No |
> | snímky | Ano |
> | sshPublicKeys | Ano |
> | virtualMachines | Ano |
> | virtualMachines/rozšíření | Ano |
> | virtualMachines / metricDefinitions | No |
> | virtualMachines / runCommands | Ano |
> | virtualMachineScaleSets | Ano |
> | virtualMachineScaleSets/rozšíření | No |
> | virtualMachineScaleSets/networkInterfaces | No |
> | virtualMachineScaleSets/publicIPAddresses | No |
> | virtualMachineScaleSets/virtualMachines | No |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | No |

## <a name="microsoftconnectedcache"></a>Microsoft. ConnectedCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | CacheNodes | Ano |

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | AggregatedCost | No |
> | Zůstatky | No |
> | Rozpočty | No |
> | Poplatky | No |
> | CostTags | No |
> | dobropis | No |
> | stránka events | No |
> | Prognózy | No |
> | ŠARŽ | No |
> | Tržišť | No |
> | Ceníky | No |
> | produktech | No |
> | ReservationDetails | No |
> | ReservationRecommendationDetails | No |
> | ReservationRecommendations | No |
> | ReservationSummaries | No |
> | ReservationTransactions | No |
> | Značky | No |
> | tenantů | No |
> | Terminologie | No |
> | UsageDetails (Podrobnosti o využití) | No |

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | containerGroups | Ano |
> | serviceAssociationLinks | No |

## <a name="microsoftcontainerregistry"></a>Microsoft. ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | registr | Ano |
> | Registry/agentPools | Ano |
> | Registry a sestavení | No |
> | Registry/buildy/zrušit | No |
> | Registry/buildy/getLogLink | No |
> | Registry/buildTasks | Ano |
> | Registry/buildTasks/kroky | No |
> | Registry/eventGridFilters | No |
> | Registry/exportPipelines | No |
> | Registry/generateCredentials | No |
> | Registry/getBuildSourceUploadUrl | No |
> | Registry/getpřihlašovací údaje | No |
> | Registry/importImage | No |
> | Registry/importPipelines | No |
> | Registry/pipelineRuns | No |
> | Registry/privateEndpointConnectionProxies | No |
> | Registry/privateEndpointConnectionProxies/ověřit | No |
> | Registry/privateEndpointConnections | No |
> | Registry/privateLinkResources | No |
> | Registry/queueBuild | No |
> | Registry/regenerateCredential | No |
> | Registry/regenerateCredentials | No |
> | Registry/replikace | Ano |
> | Registry/běhy | No |
> | Registry/spuštění/zrušit | No |
> | Registry/scheduleRun | No |
> | Registry/scopeMaps | No |
> | Registry/taskRuns | No |
> | Registry/úlohy | Ano |
> | Registry a tokeny | No |
> | Registry/updatePolicies | No |
> | Registry a Webhooky | Ano |
> | Registry/Webhooky/getCallbackConfig | No |
> | Registry/Webhooky/příkazy k odeslání | No |

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
> | Výstrahy | No |
> | BillingAccounts | No |
> | Rozpočty | No |
> | CloudConnectors | No |
> | Konektory | Ano |
> | costAllocationRules | No |
> | Oddělení | No |
> | Dimenze | No |
> | EnrollmentAccounts | No |
> | Vývozních | No |
> | ExternalBillingAccounts | No |
> | ExternalBillingAccounts/výstrahy | No |
> | ExternalBillingAccounts/dimenzí | No |
> | ExternalBillingAccounts/prognóza | No |
> | ExternalBillingAccounts/dotaz | No |
> | ExternalSubscriptions | No |
> | ExternalSubscriptions/výstrahy | No |
> | ExternalSubscriptions/dimenzí | No |
> | ExternalSubscriptions/prognóza | No |
> | ExternalSubscriptions/dotaz | No |
> | Prognóza | No |
> | Insights | No |
> | Dotaz | No |
> | register | No |
> | Reportconfigs | No |
> | Sestavy | No |
> | Nastavení | No |
> | showbackRules | No |
> | Zobrazení | No |

## <a name="microsoftcustomerlockbox"></a>Microsoft. CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | požádal | No |

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | přidružení | No |
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
> | pracovní prostory/dbWorkspaces | No |
> | pracovní prostory/storageEncryption | No |
> | pracovní prostory/virtualNetworkPeerings | No |

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
> | DataFactory/diagnosticSettings | No |
> | DataFactory/metricDefinitions | No |
> | dataFactorySchema | No |
> | továrny | Ano |
> | továrny/integrationRuntimes | No |

## <a name="microsoftdatalakeanalytics"></a>Microsoft. DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/dataLakeStoreAccounts | No |
> | účty/storageAccounts | No |
> | účty/storageAccounts/kontejnery | No |
> | účty/transferAnalyticsUnits | No |

## <a name="microsoftdatalakestore"></a>Microsoft. DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/eventGridFilters | No |
> | účty/firewallRules | No |

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
> | účty/sdílené složky | No |
> | účty/akcie/datové sady | No |
> | účty/akcie/pozvánky | No |
> | účty/akcie/providersharesubscriptions | No |
> | účty/akcie/synchronizationSettings | No |
> | účty/sharesubscriptions | No |
> | účty/sharesubscriptions/consumerSourceDataSets | No |
> | účty/sharesubscriptions/datasetmappings | No |
> | účty/sharesubscriptions/triggery | No |

## <a name="microsoftdbformariadb"></a>Microsoft. DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servery | Ano |
> | servery/poradci | No |
> | servery/klíče | No |
> | servery/privateEndpointConnectionProxies | No |
> | servery/privateEndpointConnections | No |
> | servery/privateLinkResources | No |
> | servery/queryTexts | No |
> | servery/recoverableServers | No |
> | servery/spustit | No |
> | servery/zastavit | No |
> | servery/topQueryStatistics | No |
> | servery/virtualNetworkRules | No |
> | servery/waitStatistics | No |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | flexibleServers | Ano |
> | servery | Ano |
> | servery/poradci | No |
> | servery/klíče | No |
> | servery/privateEndpointConnectionProxies | No |
> | servery/privateEndpointConnections | No |
> | servery/privateLinkResources | No |
> | servery/queryTexts | No |
> | servery/recoverableServers | No |
> | servery/spustit | No |
> | servery/zastavit | No |
> | servery/topQueryStatistics | No |
> | servery/upgrade | No |
> | servery/virtualNetworkRules | No |
> | servery/waitStatistics | No |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | flexibleServers | Ano |
> | serverGroups | Ano |
> | servery | Ano |
> | servery/poradci | No |
> | servery/klíče | No |
> | servery/privateEndpointConnectionProxies | No |
> | servery/privateEndpointConnections | No |
> | servery/privateLinkResources | No |
> | servery/queryTexts | No |
> | servery/recoverableServers | No |
> | servery/topQueryStatistics | No |
> | servery/virtualNetworkRules | No |
> | servery/waitStatistics | No |
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
> | applicationgroups/aplikace | No |
> | applicationgroups/desktopy | No |
> | applicationgroups / startmenuitems | No |
> | hostpools | Ano |
> | hostpools / sessionhosts | No |
> | hostpools / sessionhosts / usersessions | No |
> | hostpools / usersessions | No |
> | pracovní prostory | Ano |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | ElasticPools | Ano |
> | ElasticPools / IotHubTenants | Ano |
> | ElasticPools/IotHubTenants/securitySettings | No |
> | IotHubs | Ano |
> | IotHubs/eventGridFilters | No |
> | IotHubs/securitySettings | No |
> | ProvisioningServices | Ano |
> | použití | No |

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
> | digitalTwinsInstances/koncové body | No |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | databaseAccountNames | No |
> | databaseAccounts | Ano |
> | restorableDatabaseAccounts | No |

## <a name="microsoftdomainregistration"></a>Microsoft. DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | doménu | Ano |
> | domény/domainOwnershipIdentifiers | No |
> | generateSsoRequest | No |
> | topLevelDomains | No |
> | validateDomainRegistrationInformation | No |

## <a name="microsoftdynamicslcs"></a>Microsoft. DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | lcsprojects | No |
> | lcsprojects / clouddeployments | No |
> | lcsprojects/konektory | No |

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
> | domény a témata | No |
> | eventSubscriptions | No |
> | extensionTopics | No |
> | partnerNamespaces | Ano |
> | partnerNamespaces/eventChannels | No |
> | partnerRegistrations | Ano |
> | partnerTopics | Ano |
> | partnerTopics / eventSubscriptions | No |
> | systemTopics | Ano |
> | systemTopics / eventSubscriptions | No |
> | popisující | Ano |
> | topicTypes | No |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |
> | obsažené | Ano |
> | obory názvů/autorizačních pravidel | No |
> | obory názvů/disasterrecoveryconfigs | No |
> | obory názvů/eventhubs | No |
> | obory názvů/eventhubs/autorizačních pravidel | No |
> | obory názvů/eventhubs/consumergroups | No |
> | obory názvů/networkrulesets | No |
> | obory názvů/privateEndpointConnections | No |

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
> | featureProviders | No |
> | funkce | No |
> | dodavateli | No |
> | subscriptionFeatureRegistrations | No |

## <a name="microsoftgallery"></a>Microsoft. Gallery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | registrované | No |
> | galleryitems | No |
> | generateartifactaccessuri | No |
> | myareas | No |
> | myareas/oblasti | No |
> | myareas/oblasti/oblasti | No |
> | myareas/oblasti/oblasti/galleryitems | No |
> | myareas/oblasti/galleryitems | No |
> | myareas / galleryitems | No |
> | register | No |
> | resources | No |
> | retrieveresourcesbyid | No |

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
> | configurationProfileAssignments | No |
> | guestConfigurationAssignments | No |
> | Vybavení | No |
> | softwareUpdateProfile | No |
> | softwareUpdates | No |

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
> | clustery/aplikace | No |

## <a name="microsofthealthcareapis"></a>Microsoft. HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | services | Ano |
> | služby/iomtconnectors | No |
> | služby/iomtconnectors/připojení | No |
> | služby/iomtconnectors/mapování | No |
> | služby/privateEndpointConnectionProxies | No |
> | služby/privateEndpointConnections | No |
> | služby/privateLinkResources | No |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | virtuální | Ano |
> | počítače/assessPatches | No |
> | počítače/rozšíření | Ano |
> | počítače/installPatches | No |

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
> | registeredSubscriptions | No |
> | dodavateli | No |
> | dodavatelé/vendorskus | No |
> | dodavatelé/vendorskus/previewSubscriptions | No |
> | virtualnetworkfunctions | Ano |
> | virtualnetworkfunctionvendors | No |

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
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftiotcentral"></a>Microsoft. IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | appTemplates | No |
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
> | deletedVaults | No |
> | hsmPools | Ano |
> | managedHSMs | Ano |
> | trezory | Ano |
> | trezory/accessPolicies | No |
> | trezory/eventGridFilters | No |
> | trezory a klíče | No |
> | trezory/klíče/verze | No |
> | trezory/tajné klíče | No |

## <a name="microsoftkubernetes"></a>Microsoft. Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | connectedClusters | Ano |
> | registeredSubscriptions | No |

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SND | No |
> | sourceControlConfigurations | No |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |
> | clustery/attacheddatabaseconfigurations | No |
> | clustery/databáze | No |
> | clustery, databáze/datapřipojení | No |
> | clustery/databáze/eventhubconnections | No |
> | clustery/databáze/principalassignments | No |
> | clustery/datapřipojení | No |
> | clustery/principalassignments | No |
> | clustery/sharedidentities | No |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | labaccounts | Ano |
> | uživatelé | No |

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
> | pracovní prostory a výpočetní prostředky | No |
> | pracovní prostory/eventGridFilters | No |
> | pracovní prostory/linkedServices | No |

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applyUpdates | No |
> | configurationAssignments | No |
> | maintenanceConfigurations | Ano |
> | publicMaintenanceConfigurations | No |
> | aktualizovány | No |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Identity | No |
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
> | marketplaceRegistrationDefinitions | No |
> | registrationAssignments | No |
> | registrationDefinitions | No |

## <a name="microsoftmanagement"></a>Microsoft. Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | getentities | No |
> | managementGroups | No |
> | managementGroups/nastavení | No |
> | resources | No |
> | startTenantBackfill | No |
> | tenantBackfillStatus | No |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/eventGridFilters | No |
> | účty/privateAtlases | Ano |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | macc | No |
> | Rozsah | No |
> | offerTypes | No |
> | offerTypes/vydavatelé | No |
> | offerTypes/vydavatelé/nabídky | No |
> | offerTypes/vydavatelé/nabídky/plány | No |
> | offerTypes/vydavatelé/nabídky/plány/smlouvy | No |
> | offerTypes/vydavatelé/nabídky/plány/konfigurace | No |
> | offerTypes/vydavatelé/nabídky/plány/konfigurace/importImage | No |
> | privategalleryitems | No |
> | privateStoreClient | No |
> | privateStores | No |
> | privateStores/nabídky | No |
> | produktech | No |
> | zdrojů | No |
> | Vydavatelé/nabídky | No |
> | Vydavatelé/nabídky/změny | No |
> | register | No |

## <a name="microsoftmarketplaceapps"></a>Microsoft. MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | classicDevServices | Ano |
> | updateCommunicationPreference | No |

## <a name="microsoftmarketplaceordering"></a>Microsoft. MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | o | No |
> | offertypes | No |

## <a name="microsoftmedia"></a>Microsoft. Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | MediaServices | Ano |
> | MediaServices/accountFilters | No |
> | MediaServices/assety | No |
> | MediaServices/assety/assetFilters | No |
> | MediaServices/contentKeyPolicies | No |
> | MediaServices/eventGridFilters | No |
> | MediaServices/liveEventOperations | No |
> | MediaServices/liveEvents | Ano |
> | MediaServices/liveEvents/liveOutputs | No |
> | MediaServices/liveOutputOperations | No |
> | MediaServices/mediaGraphs | No |
> | MediaServices/privateEndpointConnectionOperations | No |
> | MediaServices/privateEndpointConnectionProxies | No |
> | MediaServices/privateEndpointConnections | No |
> | MediaServices/streamingEndpointOperations | No |
> | MediaServices/starají | Ano |
> | MediaServices/streamingLocators | No |
> | MediaServices/streamingPolicies | No |
> | MediaServices/transformace | No |
> | MediaServices/transformace/úlohy | No |

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
> | netAppAccounts / accountBackups | No |
> | netAppAccounts / capacityPools | Ano |
> | netAppAccounts/capacityPools/svazky | Ano |
> | netAppAccounts/capacityPools/svazky/snímky | No |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applicationGateways | Ano |
> | applicationGatewayWebApplicationFirewallPolicies | Ano |
> | applicationSecurityGroups | Ano |
> | azureFirewallFqdnTags | No |
> | azureFirewalls | Ano |
> | bastionHosts | Ano |
> | bgpServiceCommunities | No |
> | připojení | Ano |
> | ddosCustomPolicies | Ano |
> | ddosProtectionPlans | Ano |
> | dnsOperationStatuses | No |
> | dnszones | Ano |
> | dnszones/A | No |
> | dnszones/AAAA | No |
> | dnszones/vše | No |
> | dnszones/CAA | No |
> | dnszones/CNAME | No |
> | dnszones/MX | No |
> | dnszones/NS | No |
> | dnszones/PTR | No |
> | dnszones/sady záznamů | No |
> | dnszones/SOA | No |
> | dnszones/SRV | No |
> | dnszones/TXT | No |
> | expressRouteCircuits | Ano |
> | expressRouteCrossConnections | Ano |
> | expressRouteGateways | Ano |
> | expressRoutePorts | Ano |
> | expressRouteServiceProviders | No |
> | firewallPolicies | Ano |
> | frontdoors | Ano |
> | frontdoorWebApplicationFirewallManagedRuleSets | No |
> | frontdoorWebApplicationFirewallPolicies | Ano |
> | getDnsResourceReference | No |
> | internalNotify | No |
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
> | privateDnsOperationStatuses | No |
> | privateDnsZones | Ano |
> | privateDnsZones/A | No |
> | privateDnsZones/AAAA | No |
> | privateDnsZones/vše | No |
> | privateDnsZones/CNAME | No |
> | privateDnsZones/MX | No |
> | privateDnsZones/PTR | No |
> | privateDnsZones/SOA | No |
> | privateDnsZones/SRV | No |
> | privateDnsZones/TXT | No |
> | privateDnsZones / virtualNetworkLinks | Ano |
> | privateEndpoints | Ano |
> | privateLinkServices | Ano |
> | publicIPAddresses | Ano |
> | publicIPPrefixes | Ano |
> | routeFilters | Ano |
> | routeTables | Ano |
> | serviceEndpointPolicies | Ano |
> | trafficManagerGeographicHierarchies | No |
> | trafficmanagerprofiles | Ano |
> | trafficmanagerprofiles/Heat mapy | No |
> | trafficManagerUserMetricsKeys | No |
> | virtualHubs | Ano |
> | virtualNetworkGateways | Ano |
> | virtualNetworks | Ano |
> | virtualNetworks/podsítě | No |
> | virtualNetworkTaps | Ano |
> | virtualWans | Ano |
> | vpnGateways | Ano |
> | vpnSites | Ano |
> | webApplicationFirewallPolicies | Ano |

## <a name="microsoftnotebooks"></a>Microsoft. poznámkové bloky

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | NotebookProxies | No |

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
> | deletedWorkspaces | No |
> | linkTargets | No |
> | storageInsightConfigs | No |
> | pracovní prostory | Ano |
> | pracovní prostory/dataexporty | No |
> | pracovní prostory/zdroje dat | No |
> | pracovní prostory/linkedServices | No |
> | pracovní prostory/linkedStorageAccounts | No |
> | pracovní prostory/metadata | No |
> | pracovní prostory a dotazy | No |
> | pracovní prostory/scopedPrivateLinkProxies | No |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | managementassociations | No |
> | managementconfigurations | Ano |
> | Řešení | Ano |
> | zobrazení | Ano |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | legacyPeerings | No |
> | peerAsns | No |
> | partnerské vztahy | Ano |
> | peeringServiceCountries | No |
> | peeringServiceProviders | No |
> | peeringServices | Ano |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | atestace | No |
> | policyEvents | No |
> | policyMetadata | No |
> | policyStates | No |
> | policyTrackedResources | No |
> | nápravy | No |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Konzoly nástroje | No |
> | řídicí panely | Ano |
> | userSettings | No |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | privateLinkServicesForPowerBI | Ano |
> | tenantů | Ano |
> | klienti/pracovní prostory | No |
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
> | deletedAccounts | No |

## <a name="microsoftproviderhub"></a>Microsoft. ProviderHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | providerRegistrations | No |
> | providerRegistrations / defaultRollouts | No |
> | providerRegistrations / resourceTypeRegistrations | No |
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
> | backupProtectedItems | No |
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
> | obory názvů/autorizačních pravidel | No |
> | obory názvů/hybridconnections | No |
> | obory názvů/hybridconnections/autorizačních pravidel | No |
> | obory názvů/privateEndpointConnections | No |
> | obory názvů/wcfrelays | No |
> | obory názvů/wcfrelays/autorizačních pravidel | No |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | odešle | Ano |
> | resourceChangeDetails | No |
> | resourceChanges | No |
> | resources | No |
> | resourcesHistory | No |
> | subscriptionsStatus | No |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | availabilityStatuses | No |
> | childAvailabilityStatuses | No |
> | childResources | No |
> | emergingissues | No |
> | stránka events | No |
> | impactedResources | No |
> | zprostředkovatele identity | No |
> | připomenutí | No |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | calculateTemplateHash | No |
> | nasazení | No |
> | nasazení/operace | No |
> | deploymentScripts | Ano |
> | deploymentScripts/protokoly | No |
> | odkazy | No |
> | notifyResourceJobs | No |
> | dodavateli | No |
> | resourceGroups | No |
> | odběru | No |
> | templateSpecs | Ano |
> | templateSpecs/verze | Ano |
> | tenantů | No |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikace | Ano |
> | saasresources | No |

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
> | resourceHealthMetadata | No |
> | searchServices | Ano |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | No |
> | advancedThreatProtectionSettings | No |
> | výstrahy | No |
> | alertsSuppressionRules | No |
> | allowedConnections | No |
> | applicationWhitelistings | No |
> | assessmentMetadata | No |
> | hodnocení | No |
> | autoDismissAlertsRules | No |
> | automatizace | Ano |
> | AutoProvisioningSettings | No |
> | Předpisů | No |
> | konektory | No |
> | dataCollectionAgents | No |
> | deviceSecurityGroups | No |
> | discoveredSecuritySolutions | No |
> | externalSecuritySolutions | No |
> | InformationProtectionPolicies | No |
> | iotDefenderSettings | No |
> | iotSecuritySolutions | Ano |
> | iotSecuritySolutions / analyticsModels | No |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | No |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | No |
> | iotSecuritySolutions / iotAlerts | No |
> | iotSecuritySolutions / iotAlertTypes | No |
> | iotSecuritySolutions / iotRecommendations | No |
> | iotSecuritySolutions / iotRecommendationTypes | No |
> | iotSensors | No |
> | jitNetworkAccessPolicies | No |
> | jitPolicies | No |
> | Zásady | No |
> | ceny | No |
> | regulatoryComplianceStandards | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls | No |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | No |
> | secureScoreControlDefinitions | No |
> | secureScoreControls | No |
> | secureScores | No |
> | secureScores / secureScoreControls | No |
> | securityContacts | No |
> | securitySolutions | No |
> | securitySolutionsReferenceData | No |
> | securityStatuses | No |
> | securityStatusesSummaries | No |
> | serverVulnerabilityAssessments | No |
> | nastavení | No |
> | sqlVulnerabilityAssessments | No |
> | podhodnocení | No |
> | úlohy | No |
> | topologie | No |
> | workspaceSettings | No |

## <a name="microsoftsecuritygraph"></a>Microsoft. SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | agregace | No |
> | alertRules | No |
> | alertRuleTemplates | No |
> | automationRules | No |
> | záložky | No |
> | věcech | No |
> | dataconnects | No |
> | dataConnectorsCheckRequirements | No |
> | podnikům | No |
> | entityQueries | No |
> | Incidenty | No |
> | officeConsents | No |
> | nastavení | No |
> | threatIntelligence | No |
> | watchlists | No |

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | consoleServices | No |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | obsažené | Ano |
> | obory názvů/autorizačních pravidel | No |
> | obory názvů/disasterrecoveryconfigs | No |
> | obory názvů/eventgridfilters | No |
> | obory názvů/networkrulesets | No |
> | obory názvů/privateEndpointConnections | No |
> | obory názvů/fronty | No |
> | obory názvů/fronty/autorizačních pravidel | No |
> | obory názvů/témata | No |
> | obory názvů/témata/autorizačních pravidel | No |
> | obory názvů/témata/předplatná | No |
> | obory názvů/témata/předplatná/pravidla | No |
> | premiumMessagingRegions | No |

## <a name="microsoftservicefabric"></a>Microsoft. ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikace | Ano |
> | existující | Ano |
> | clustery/aplikace | No |
> | containerGroups | Ano |
> | containerGroupSets | Ano |
> | edgeclusters | Ano |
> | edgeclusters/aplikace | No |
> | managedclusters | Ano |
> | managedclusters/nodetypes | No |
> | sítí | Ano |
> | secretstores | Ano |
> | secretstores/certifikáty | No |
> | secretstores/tajné kódy | No |
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
> | providerRegistrations | No |
> | providerRegistrations / resourceTypeRegistrations | No |
> | uvádění | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SignalR | Ano |
> | Signál/eventGridFilters | No |

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hybridUseBenefits | No |

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
> | managedInstances/databáze/backupShortTermRetentionPolicies | No |
> | managedInstances/databáze/schémata/tabulky/sloupce/sensitivityLabels | No |
> | managedInstances/databáze/vulnerabilityAssessments | No |
> | managedInstances/databáze/vulnerabilityAssessments/Rules/směrné plány | No |
> | managedInstances / encryptionProtector | No |
> | managedInstances/klíče | No |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | No |
> | managedInstances / vulnerabilityAssessments | No |
> | servery | Ano |
> | servery/správci | No |
> | servery/communicationLinks | No |
> | servery/databáze | Ano |
> | servery/encryptionProtector | No |
> | servery/firewallRules | No |
> | servery/klíče | No |
> | servery/restorableDroppedDatabases | No |
> | servery/serviceobjectives | No |
> | servery/tdeCertificates | No |
> | virtualClusters | No |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft. SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SqlVirtualMachineGroups | Ano |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | No |
> | SqlVirtualMachines | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | deletedAccounts | No |
> | storageAccounts | Ano |
> | storageAccounts/blobServices | No |
> | storageAccounts/služby | No |
> | storageAccounts/queueServices | No |
> | storageAccounts/služby | No |
> | storageAccounts/služby/metricDefinitions | No |
> | storageAccounts/tableServices | No |
> | použití | No |

## <a name="microsoftstoragecache"></a>Microsoft. StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | mezipaměti | Ano |
> | mezipaměti/storageTargets | No |
> | usageModels | No |

## <a name="microsoftstoragereplication"></a>Microsoft. StorageReplication

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | replicationGroups | No |

## <a name="microsoftstoragesync"></a>Microsoft. StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices/registeredServer | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/pracovní postupy | No |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices/registeredServer | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/pracovní postupy | No |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices/registeredServer | No |
> | storageSyncServices / syncGroups | No |
> | storageSyncServices / syncGroups / cloudEndpoints | No |
> | storageSyncServices / syncGroups / serverEndpoints | No |
> | storageSyncServices/pracovní postupy | No |

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
> | streamingjobs | Ano |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | acceptChangeTenant | No |
> | hromad | No |
> | cancel | No |
> | changeTenantRequest | No |
> | changeTenantStatus | No |
> | CreateSubscription | No |
> | aby | No |
> | přejmenovat | No |
> | SubscriptionDefinitions | No |
> | SubscriptionOperations | No |
> | odběru | No |

## <a name="microsoftsynapse"></a>Microsoft. synapse

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | privateLinkHubs | Ano |
> | pracovní prostory | Ano |
> | pracovní prostory/bigDataPools | Ano |
> | pracovní prostory/operationStatuses | No |
> | pracovní prostory/sqlDatabases | Ano |
> | pracovní prostory/sqlPools | Ano |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Environment | Ano |
> | prostředí/accessPolicies | No |
> | prostředí/EventSources | Ano |
> | prostředí/referenceDataSets | Ano |

## <a name="microsofttoken"></a>Microsoft. token

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Store | Ano |
> | obchody/accessPolicies | No |
> | obchody/služby | No |
> | úložiště/služby/tokeny | No |

## <a name="microsoftvirtualmachineimages"></a>Microsoft. VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | imageTemplates | Ano |
> | imageTemplates / runOutputs | No |

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
> | registeredSubscriptions | No |
> | dodavateli | No |
> | dodavatelé/SKU | No |
> | dodavatelé/vnfs | No |
> | virtualNetworkFunctionSkus | No |
> | vnfs | Ano |

## <a name="microsoftvsonline"></a>Microsoft. VSOnline

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | plánují | Ano |
> | registeredSubscriptions | No |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | apiManagementAccounts | No |
> | apiManagementAccounts / apiAcls | No |
> | apiManagementAccounts/rozhraní API | No |
> | apiManagementAccounts/API/apiAcls | No |
> | apiManagementAccounts/API/connectionAcls | No |
> | apiManagementAccounts/rozhraní API/připojení | No |
> | apiManagementAccounts/rozhraní API/připojení/connectionAcls | No |
> | apiManagementAccounts/API/localizedDefinitions | No |
> | apiManagementAccounts / connectionAcls | No |
> | apiManagementAccounts/připojení | No |
> | billingMeters | No |
> | certifikáty | Ano |
> | connectionGateways | Ano |
> | připojení | Ano |
> | customApis | Ano |
> | deletedSites | No |
> | hostingEnvironments | Ano |
> | hostingEnvironments / eventGridFilters | No |
> | hostingEnvironments / multiRolePools | No |
> | hostingEnvironments / workerPools | No |
> | kubeEnvironments | Ano |
> | publishingUsers | No |
> | doporučit | No |
> | resourceHealthMetadata | No |
> | moduly runtime | No |
> | Serverových farem | Ano |
> | Serverových farem/eventGridFilters | No |
> | místa | Ano |
> | lokality/konfigurace  | No |
> | lokality/eventGridFilters | No |
> | lokality/hostNameBindings | No |
> | lokality/networkConfig | No |
> | lokality/premieraddons | Ano |
> | lokality/sloty | Ano |
> | lokality/sloty/eventGridFilters | No |
> | lokality/sloty/hostNameBindings | No |
> | lokality/sloty/networkConfig | No |
> | sourceControls | No |
> | staticSites | Ano |
> | oproti | No |
> | verifyHostingEnvironmentVnet | No |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | diagnosticSettings | No |
> | diagnosticSettingsCategories | No |

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
> | úlohy/instance | No |
> | zatížení a verze | No |
> | úlohy/verze/artefakty | No |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | konstrukční | No |
> | componentsSummary | No |
> | monitorInstances | No |
> | monitorInstancesSummary | No |
> | Monitor | No |
> | notificationSettings | No |

## <a name="next-steps"></a>Další kroky

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

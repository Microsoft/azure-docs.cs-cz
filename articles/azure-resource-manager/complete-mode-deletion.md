---
title: Azure Resource Manager dokončení odstranění režimu
description: Ukazuje, jak typy prostředků zpracovávají úplné odstraňování režimu v Azure Resource Manager šablonách.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/27/2019
ms.author: tomfitz
ms.openlocfilehash: aac01032b06c13564475c58c89c7e572bb9a6022
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162184"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Odstranění prostředků Azure pro nasazení v úplném režimu

Tento článek popisuje, jak typy prostředků zpracovávají odstranění, když není v šabloně, která je nasazena v úplném režimu.

Typy prostředků označené **Ano** se odstraní, když typ není v šabloně nasazené s úplným režimem.

Typy prostředků označené jako **ne** se automaticky neodstraní, pokud nejsou v šabloně. jsou však odstraněny, pokud je odstraněn nadřazený prostředek. Úplný popis chování najdete v tématu [Azure Resource Manager režimy nasazení](deployment-modes.md).

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
> - [Microsoft. Azconfig](#microsoftazconfig)
> - [Microsoft. Azure. Ženeva](#microsoftazuregeneva)
> - [Microsoft. Azureactivedirectory selhala](#microsoftazureactivedirectory)
> - [Microsoft. AzureData](#microsoftazuredata)
> - [Microsoft. AzureStack](#microsoftazurestack)
> - [Microsoft. Batch](#microsoftbatch)
> - [Microsoft. fakturace](#microsoftbilling)
> - [Microsoft. BingMaps](#microsoftbingmaps)
> - [Microsoft. blockchain](#microsoftblockchain)
> - [Microsoft. detail](#microsoftblueprint)
> - [Microsoft. BotService](#microsoftbotservice)
> - [Microsoft. cache](#microsoftcache)
> - [Microsoft. Capacity](#microsoftcapacity)
> - [Microsoft. CDN](#microsoftcdn)
> - [Microsoft. CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft. ClassicCompute](#microsoftclassiccompute)
> - [Microsoft. ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft. ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft. ClassicStorage](#microsoftclassicstorage)
> - [Microsoft. Cognitiveservices Account](#microsoftcognitiveservices)
> - [Microsoft. Commerce](#microsoftcommerce)
> - [Microsoft. COMPUTE](#microsoftcompute)
> - [Microsoft. spotřeba](#microsoftconsumption)
> - [Microsoft. ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft. ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft. ContainerService](#microsoftcontainerservice)
> - [Microsoft. CortanaAnalytics](#microsoftcortanaanalytics)
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
> - [Microsoft. DocumentDB](#microsoftdocumentdb)
> - [Microsoft. DomainRegistration](#microsoftdomainregistration)
> - [Microsoft. DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft. EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft. EventGrid](#microsofteventgrid)
> - [Microsoft. EventHub](#microsofteventhub)
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
> - [Microsoft. Hydra](#microsofthydra)
> - [Microsoft. ImportExport](#microsoftimportexport)
> - [Microsoft. Intune](#microsoftintune)
> - [Microsoft. IoTCentral](#microsoftiotcentral)
> - [Microsoft. IoTSpaces](#microsoftiotspaces)
> - [Trezor Microsoft.](#microsoftkeyvault)
> - [Microsoft. Kusto](#microsoftkusto)
> - [Microsoft. LabServices](#microsoftlabservices)
> - [Microsoft. Logic](#microsoftlogic)
> - [Microsoft. MachineLearning](#microsoftmachinelearning)
> - [Microsoft. MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft. ManagedIdentity](#microsoftmanagedidentity)
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
> - [Microsoft. Network](#microsoftnetwork)
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
> - [Microsoft. RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft. Relay](#microsoftrelay)
> - [Microsoft. RemoteApp](#microsoftremoteapp)
> - [Microsoft. ResourceGraph](#microsoftresourcegraph)
> - [Microsoft. ResourceHealth](#microsoftresourcehealth)
> - [Microsoft. Resources](#microsoftresources)
> - [Microsoft. SaaS](#microsoftsaas)
> - [Microsoft. Scheduler](#microsoftscheduler)
> - [Microsoft. Search](#microsoftsearch)
> - [Microsoft. Security](#microsoftsecurity)
> - [Microsoft. SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft. SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft. ServiceFabric](#microsoftservicefabric)
> - [Microsoft. ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft. Services](#microsoftservices)
> - [Microsoft. SignalRService](#microsoftsignalrservice)
> - [Microsoft. SiteRecovery](#microsoftsiterecovery)
> - [Microsoft. SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft. Solutions](#microsoftsolutions)
> - [Microsoft. SQL](#microsoftsql)
> - [Microsoft. SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft. Storage](#microsoftstorage)
> - [Microsoft. StorageCache](#microsoftstoragecache)
> - [Microsoft. StorageReplication](#microsoftstoragereplication)
> - [Microsoft. StorageSync](#microsoftstoragesync)
> - [Microsoft. StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft. StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft. StorSimple](#microsoftstorsimple)
> - [Microsoft. StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft. Subscription](#microsoftsubscription)
> - [Microsoft. TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft. VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft. Web](#microsoftweb)
> - [Microsoft. WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft. WindowsIoT](#microsoftwindowsiot)
> - [Microsoft. monitor zátěže byl](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft. AAD

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | DomainServices | Ano |
> | DomainServices/oucontainer | Ne |
> | DomainServices/ReplicaSets | Ano |

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
> | Technici | Ne |
> | anonymousapiusers | Ne |
> | konfigurace | Ne |
> | Protokoly | Ne |
> | protokoly | Ne |
> | servicehealthmetrics | Ne |
> | Služby | Ne |

## <a name="microsoftadvisor"></a>Microsoft. Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | konfiguračních | Ne |
> | generateRecommendations | Ne |
> | zprostředkovatele identity | Ne |
> | Doporučit | Ne |
> | potlačení | Ne |

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
> | Od | Ne |
> | smartDetectorAlertRules | Ano |
> | smartDetectorRuntimeEnvironments | Ne |
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
> | služba | Ano |
> | validateServiceName | Ne |

## <a name="microsoftappconfiguration"></a>Microsoft. AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | configurationStores | Ano |
> | configurationStores/eventGridFilters | Ne |

## <a name="microsoftappplatform"></a>Microsoft. AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Spring | Ano |

## <a name="microsoftattestation"></a>Microsoft. Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | attestationProviders | Ne |

## <a name="microsoftauthorization"></a>Microsoft. Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | classicAdministrators | Ne |
> | dataaliasy | Ne |
> | denyAssignments | Ne |
> | elevateAccess | Ne |
> | findOrphanRoleAssignments | Ne |
> | Počtu | Ne |
> | nastaven | Ne |
> | policyAssignments | Ne |
> | policyDefinitions | Ne |
> | policySetDefinitions | Ne |
> | providerOperations | Ne |
> | roleAssignments | Ne |
> | roleDefinitions | Ne |

## <a name="microsoftautomation"></a>Microsoft. Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | automationAccounts | Ano |
> | automationAccounts/konfigurace | Ano |
> | automationAccounts/úlohy | Ne |
> | automationAccounts/Runbooky | Ano |
> | automationAccounts/softwareUpdateConfigurations | Ne |
> | automationAccounts nebo Webhooky | Ne |

## <a name="microsoftazconfig"></a>Microsoft. Azconfig

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | configurationStores | Ano |
> | configurationStores/eventGridFilters | Ne |

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

## <a name="microsoftazuredata"></a>Microsoft. AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hybridDataManagers | Ano |
> | postgresInstances | Ano |
> | sqlBigDataClusters | Ano |
> | sqlInstances | Ano |
> | sqlServerRegistrations | Ano |
> | sqlServerRegistrations/sqlServers | Ne |

## <a name="microsoftazurestack"></a>Microsoft. AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | rozpoznávání | Ano |
> | registrace/customerSubscriptions | Ne |
> | registrace/produkty | Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | batchAccounts | Ano |

## <a name="microsoftbilling"></a>Microsoft. fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | billingAccounts | Ne |
> | billingAccounts/smlouvy | Ne |
> | billingAccounts/billingPermissions | Ne |
> | billingAccounts/billingProfiles | Ne |
> | billingAccounts/billingProfiles/billingPermissions | Ne |
> | billingAccounts/billingProfiles/billingRoleAssignments | Ne |
> | billingAccounts/billingProfiles/billingRoleDefinitions | Ne |
> | billingAccounts/billingProfiles/billingSubscriptions | Ne |
> | billingAccounts/billingProfiles/createBillingRoleAssignment | Ne |
> | billingAccounts/billingProfiles/zákazníci | Ne |
> | billingAccounts/billingProfiles/faktury | Ne |
> | billingAccounts/billingProfiles/faktury/pricesheet | Ne |
> | billingAccounts/billingProfiles/invoiceSections | Ne |
> | billingAccounts/billingProfiles/invoiceSections/billingPermissions | Ne |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleAssignments | Ne |
> | billingAccounts/billingProfiles/invoiceSections/billingRoleDefinitions | Ne |
> | billingAccounts/billingProfiles/invoiceSections/billingSubscriptions | Ne |
> | billingAccounts/billingProfiles/invoiceSections/createBillingRoleAssignment | Ne |
> | billingAccounts/billingProfiles/invoiceSections/initiateTransfer | Ne |
> | billingAccounts/billingProfiles/invoiceSections/Products | Ne |
> | billingAccounts/billingProfiles/invoiceSections/Products/Transfer | Ne |
> | billingAccounts/billingProfiles/invoiceSections/Products/updateAutoRenew | Ne |
> | billingAccounts/billingProfiles/invoiceSections/transakce | Ne |
> | billingAccounts/billingProfiles/invoiceSections/transfery | Ne |
> | billingAccounts/BillingProfiles/patchOperations | Ne |
> | billingAccounts/billingProfiles/paymentMethods | Ne |
> | billingAccounts/billingProfiles/– zásady | Ne |
> | billingAccounts/billingProfiles/pricesheet | Ne |
> | billingAccounts/billingProfiles/pricesheetDownloadOperations | Ne |
> | billingAccounts/billingProfiles/produkty | Ne |
> | billingAccounts/billingProfiles/transakcí | Ne |
> | billingAccounts/billingRoleAssignments | Ne |
> | billingAccounts/billingRoleDefinitions | Ne |
> | billingAccounts/billingSubscriptions | Ne |
> | billingAccounts/createBillingRoleAssignment | Ne |
> | billingAccounts/createInvoiceSectionOperations | Ne |
> | billingAccounts/zákazníci | Ne |
> | billingAccounts/Customers/billingPermissions | Ne |
> | billingAccounts/Customers/billingSubscriptions | Ne |
> | billingAccounts/Customers/initiateTransfer | Ne |
> | billingAccounts/Customers/policies | Ne |
> | billingAccounts/zákazníci/produkty | Ne |
> | billingAccounts/zákazníci/transakce | Ne |
> | billingAccounts/zákazníci/přenosy | Ne |
> | billingAccounts/oddělení | Ne |
> | billingAccounts/enrollmentAccounts | Ne |
> | billingAccounts/faktury | Ne |
> | billingAccounts/invoiceSections | Ne |
> | billingAccounts/invoiceSections/billingSubscriptionMoveOperations | Ne |
> | billingAccounts/invoiceSections/billingSubscriptions | Ne |
> | billingAccounts/invoiceSections/billingSubscriptions/Transfer | Ne |
> | billingAccounts/invoiceSections/zvýšení oprávnění | Ne |
> | billingAccounts/invoiceSections/initiateTransfer | Ne |
> | billingAccounts/invoiceSections/patchOperations | Ne |
> | billingAccounts/invoiceSections/productMoveOperations | Ne |
> | billingAccounts/invoiceSections/produkty | Ne |
> | billingAccounts/invoiceSections/produkty/přenos | Ne |
> | billingAccounts/invoiceSections/Products/updateAutoRenew | Ne |
> | billingAccounts/invoiceSections/transakcí | Ne |
> | billingAccounts/invoiceSections/transfery | Ne |
> | billingAccounts/lineOfCredit | Ne |
> | billingAccounts/patchOperations | Ne |
> | billingAccounts/paymentMethods | Ne |
> | billingAccounts/produkty | Ne |
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
> | diváků | Ano |

## <a name="microsoftblueprint"></a>Microsoft. detail

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | blueprintAssignments | Ne |
> | blueprintAssignments/assignmentOperations | Ne |
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
> | Jazyky | Ne |
> | šablon | Ne |

## <a name="microsoftcache"></a>Microsoft. cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Redis | Ano |
> | RedisConfigDefinition | Ne |

## <a name="microsoftcapacity"></a>Microsoft. Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | appliedReservations | Ne |
> | calculateExchange | Ne |
> | calculatePrice | Ne |
> | calculatePurchasePrice | Ne |
> | spustí | Ne |
> | commercialReservationOrders | Ne |
> | exchange | Ne |
> | placePurchaseOrder | Ne |
> | reservationOrders | Ne |
> | reservationOrders/calculateRefund | Ne |
> | reservationOrders/sloučit | Ne |
> | reservationOrders/rezervace | Ne |
> | reservationOrders/rezervace/Revize | Ne |
> | reservationOrders/vrátit | Ne |
> | reservationOrders/Split | Ne |
> | reservationOrders/swap | Ne |
> | rezervace | Ne |
> | Prostředky | Ne |
> | validateReservationOrder | Ne |

## <a name="microsoftcdn"></a>Microsoft. CDN

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Ne |
> | CdnWebApplicationFirewallPolicies | Ano |
> | edgenodes | Ne |
> | Uživatelů | Ano |
> | profily/koncové body | Ano |
> | profily/koncové body/customdomains | Ne |
> | profily/koncové body/zdroje | Ne |
> | validateProbe | Ne |

## <a name="microsoftcertificateregistration"></a>Microsoft. CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | certificateOrders | Ano |
> | certificateOrders/certifikáty | Ne |
> | validateCertificateRegistrationInformation | Ne |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Vestavěn | Ne |
> | domainNames | Ano |
> | domainNames/možnosti | Ne |
> | domainNames/internalLoadBalancers | Ne |
> | domainNames/serviceCertificates | Ne |
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
> | VirtualMachines | Ano |
> | virtualMachines/diagnosticSettings | Ne |
> | virtualMachines/metricDefinitions | Ne |
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
> | Vestavěn | Ne |
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
> | Vestavěn | Ne |
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

## <a name="microsoftcognitiveservices"></a>Microsoft. Cognitiveservices Account

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |

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
> | restorePointCollections/restorePoints | Ne |
> | sharedVMExtensions | Ano |
> | sharedVMExtensions/verze | Ne |
> | sharedVMImages | Ano |
> | sharedVMImages/verze | Ne |
> | snímky | Ano |
> | VirtualMachines | Ano |
> | virtualMachines/rozšíření | Ano |
> | virtualMachines/metricDefinitions | Ne |
> | virtualMachineScaleSets | Ano |
> | virtualMachineScaleSets/rozšíření | Ne |
> | virtualMachineScaleSets/networkInterfaces | Ne |
> | virtualMachineScaleSets/publicIPAddresses | Ne |
> | virtualMachineScaleSets/virtualMachines | Ne |
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Ne |

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
> | Pricesheets | Ne |
> | produktech | Ne |
> | ReservationDetails | Ne |
> | ReservationRecommendations | Ne |
> | ReservationSummaries | Ne |
> | ReservationTransactions | Ne |
> | Značky | Ne |
> | Tenantů | Ne |
> | Výrazy | Ne |
> | UsageDetails | Ne |

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
> | Registr | Ano |
> | Registry a sestavení | Ne |
> | Registry/buildy/zrušit | Ne |
> | Registry/buildy/getLogLink | Ne |
> | Registry/buildTasks | Ano |
> | Registry/buildTasks/kroky | Ne |
> | Registry/eventGridFilters | Ne |
> | Registry/generateCredentials | Ne |
> | Registry/getBuildSourceUploadUrl | Ne |
> | Registry/getpřihlašovací údaje | Ne |
> | Registry/importImage | Ne |
> | Registry/queueBuild | Ne |
> | Registry/regenerateCredential | Ne |
> | Registry/regenerateCredentials | Ne |
> | Registry/replikace | Ano |
> | Registry/běhy | Ne |
> | Registry/spuštění/zrušit | Ne |
> | Registry/scheduleRun | Ne |
> | Registry/scopeMaps | Ne |
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

## <a name="microsoftcortanaanalytics"></a>Microsoft. CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Výstrahy | Ne |
> | billingAccounts | Ne |
> | Rozpočty | Ne |
> | CloudConnectors | Ne |
> | Konektory | Ano |
> | oddělení | Ne |
> | Dimenze | Ne |
> | enrollmentAccounts | Ne |
> | Exporty | Ne |
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
> | Prognózy | Ne |
> | Dotaz | Ne |
> | Registrace | Ne |
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

## <a name="microsoftdatabox"></a>Microsoft. DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Úlohy | Ano |

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
> | pracovní prostory/virtualNetworkPeerings | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft. datacatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | spustí | Ano |
> | datacatalogs | Ano |
> | datacatalogs/zdroje dat | Ne |
> | datacatalogs/zdroje dat/kontroly | Ne |
> | datové katalogy/zdroje dat/kontroly/datové sady | Ne |
> | datacatalogs/zdroje dat/kontroly/triggery | Ne |

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
> | Služby | Ano |
> | služby a projekty | Ano |

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
> | servery/privateEndpointConnectionProxies | Ne |
> | servery/privateEndpointConnections | Ne |
> | servery/privateLinkResources | Ne |
> | servery/queryTexts | Ne |
> | servery/recoverableServers | Ne |
> | servery/topQueryStatistics | Ne |
> | servery/virtualNetworkRules | Ne |
> | servery/waitStatistics | Ne |

## <a name="microsoftdbformysql"></a>Microsoft. DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servery | Ano |
> | servery/poradci | Ne |
> | servery/privateEndpointConnectionProxies | Ne |
> | servery/privateEndpointConnections | Ne |
> | servery/privateLinkResources | Ne |
> | servery/queryTexts | Ne |
> | servery/recoverableServers | Ne |
> | servery/topQueryStatistics | Ne |
> | servery/virtualNetworkRules | Ne |
> | servery/waitStatistics | Ne |

## <a name="microsoftdbforpostgresql"></a>Microsoft. DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
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
> | applicationgroups/startmenuitems | Ne |
> | hostpools | Ano |
> | hostpools/sessionhosts | Ne |
> | hostpools/sessionhosts/usersessions | Ne |
> | hostpools/usersessions | Ne |
> | pracovní prostory | Ano |

## <a name="microsoftdevices"></a>Microsoft. Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | ElasticPools | Ano |
> | ElasticPools/IotHubTenants | Ano |
> | IotHubs | Ano |
> | IotHubs/eventGridFilters | Ne |
> | ProvisioningServices | Ano |
> | použití | Ne |

## <a name="microsoftdevops"></a>Microsoft. DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | kanály | Ano |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Kontrolou | Ano |

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

## <a name="microsoftdocumentdb"></a>Microsoft. DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | databaseAccountNames | Ne |
> | databaseAccounts | Ano |

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
> | lcsprojects/clouddeployments | Ne |
> | lcsprojects/konektory | Ne |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft. EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Služby | Ano |

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | doménu | Ano |
> | domény a témata | Ne |
> | eventSubscriptions | Ne |
> | extensionTopics | Ne |
> | popisující | Ano |
> | topicTypes | Ne |

## <a name="microsofteventhub"></a>Microsoft. EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |
> | Obsažené | Ano |
> | obory názvů/autorizačních pravidel | Ne |
> | obory názvů/disasterrecoveryconfigs | Ne |
> | obory názvů/eventhubs | Ne |
> | obory názvů/eventhubs/autorizačních pravidel | Ne |
> | obory názvů/eventhubs/consumergroups | Ne |
> | obory názvů/networkrulesets | Ne |

## <a name="microsoftfeatures"></a>Microsoft. Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Database | Ne |
> | Dodavateli | Ne |

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
> | myareas/galleryitems | Ne |
> | Registrace | Ne |
> | Prostředky | Ne |
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
> | Služby | Ano |

## <a name="microsofthybridcompute"></a>Microsoft. HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | virtuální | Ano |
> | počítače/rozšíření | Ano |

## <a name="microsofthybriddata"></a>Microsoft. HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | datamanagery | Ano |

## <a name="microsofthydra"></a>Microsoft. Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Konstrukční | Ano |
> | networkScopes | Ano |

## <a name="microsoftimportexport"></a>Microsoft. ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Úlohy | Ano |

## <a name="microsoftintune"></a>Microsoft. Intune

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
> | Graf | Ano |

## <a name="microsoftkeyvault"></a>Trezor Microsoft.

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | deletedVaults | Ne |
> | hsmPools | Ano |
> | trezory | Ano |
> | trezory/accessPolicies | Ne |
> | trezory/eventGridFilters | Ne |
> | trezory/tajné klíče | Ne |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |
> | clustery/attacheddatabaseconfigurations | Ne |
> | clustery/databáze | Ne |
> | clustery, databáze/datapřipojení | Ne |
> | clustery/databáze/eventhubconnections | Ne |
> | clustery/sharedidentities | Ne |

## <a name="microsoftlabservices"></a>Microsoft. LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | labaccounts | Ano |
> | uživatelů | Ne |

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
> | pracovní prostory a výpočetní prostředky | Ne |
> | pracovní prostory/eventGridFilters | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Nebyly | Ne |
> | userAssignedIdentities | Ano |

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
> | Prostředky | Ne |
> | startTenantBackfill | Ne |
> | tenantBackfillStatus | Ne |

## <a name="microsoftmaps"></a>Microsoft. Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty/eventGridFilters | Ne |

## <a name="microsoftmarketplace"></a>Microsoft. Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Rozsah | Ne |
> | offerTypes | Ne |
> | offerTypes/vydavatelé | Ne |
> | offerTypes/vydavatelé/nabídky | Ne |
> | offerTypes/vydavatelé/nabídky/plány | Ne |
> | offerTypes/vydavatelé/nabídky/plány/smlouvy | Ne |
> | offerTypes/vydavatelé/nabídky/plány/konfigurace | Ne |
> | offerTypes/vydavatelé/nabídky/plány/konfigurace/importImage | Ne |
> | privategalleryitems | Ne |
> | produktech | Ne |
> | zdrojů | Ne |
> | Vydavatelé/nabídky | Ne |
> | Vydavatelé/nabídky/změny | Ne |

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
> | projekty | Ano |

## <a name="microsoftmixedreality"></a>Microsoft. MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Ano |
> | objectUnderstandingAccounts | Ano |
> | remoteRenderingAccounts | Ano |
> | spatialAnchorsAccounts | Ano |
> | surfaceReconstructionAccounts | Ano |

## <a name="microsoftnetapp"></a>Microsoft. NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | netAppAccounts | Ano |
> | netAppAccounts/backupPolicies | Ano |
> | netAppAccounts/capacityPools | Ano |
> | netAppAccounts/capacityPools/svazky | Ano |
> | netAppAccounts/capacityPools/svazky/zálohy | Ne |
> | netAppAccounts/capacityPools/svazky/mountTargets | Ano |
> | netAppAccounts/capacityPools/svazky/snímky | Ano |
> | netAppAccounts nebo trezory | Ne |
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
> | Připojení | Ano |
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
> | loadBalancers | Ano |
> | localNetworkGateways | Ano |
> | natGateways | Ano |
> | networkIntentPolicies | Ano |
> | networkInterfaces | Ano |
> | networkProfiles | Ano |
> | networkSecurityGroups | Ano |
> | networkWatchers | Ano |
> | networkWatchers/connectionMonitors | Ano |
> | networkWatchers/čočky | Ano |
> | networkWatchers/pingMeshes | Ano |
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
> | privateDnsZones/virtualNetworkLinks | Ano |
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
> | virtualNetworkTaps | Ano |
> | virtualWans | Ano |
> | vpnGateways | Ano |
> | vpnSites | Ano |
> | webApplicationFirewallPolicies | Ano |

## <a name="microsoftnotificationhubs"></a>Microsoft. NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Obsažené | Ano |
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
> | ServerSites | Ano |
> | VMwareSites | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | existující | Ano |
> | zařízení | Ne |
> | linkTargets | Ne |
> | storageInsightConfigs | Ne |
> | pracovní prostory | Ano |
> | pracovní prostory/zdroje dat | Ne |
> | pracovní prostory/linkedServices | Ne |
> | pracovní prostory a dotazy | Ne |

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | managementassociations | Ne |
> | managementconfigurations | Ano |
> | Řešení | Ano |
> | Náhled | Ano |

## <a name="microsoftpeering"></a>Microsoft. peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | legacyPeerings | Ne |
> | peerAsns | Ne |
> | partnerské vztahy | Ano |
> | peeringServiceProviders | Ne |
> | peeringServices | Ano |

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | policyEvents | Ne |
> | policyMetadata | Ne |
> | policyStates | Ne |
> | policyTrackedResources | Ne |
> | nápravy | Ne |

## <a name="microsoftportal"></a>Microsoft. Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Console | Ne |
> | řídicí panely | Ano |
> | userSettings | Ne |

## <a name="microsoftpowerbi"></a>Microsoft. PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | workspaceCollections | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft. PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | schopností | Ano |

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | backupProtectedItems | Ne |
> | trezory | Ano |

## <a name="microsoftrelay"></a>Microsoft. Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Obsažené | Ano |
> | obory názvů/autorizačních pravidel | Ne |
> | obory názvů/hybridconnections | Ne |
> | obory názvů/hybridconnections/autorizačních pravidel | Ne |
> | obory názvů/wcfrelays | Ne |
> | obory názvů/wcfrelays/autorizačních pravidel | Ne |

## <a name="microsoftremoteapp"></a>Microsoft. RemoteApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ne |
> | Sbírk | Ano |
> | Kolekce/aplikace | Ne |
> | kolekce/securityprincipals | Ne |
> | templateImages | Ne |

## <a name="microsoftresourcegraph"></a>Microsoft. ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dotazy | Ano |
> | resourceChangeDetails | Ne |
> | resourceChanges | Ne |
> | Prostředky | Ne |
> | resourcesHistory | Ne |
> | subscriptionsStatus | Ne |

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | availabilityStatuses | Ne |
> | childAvailabilityStatuses | Ne |
> | childResources | Ne |
> | stránka events | Ne |
> | impactedResources | Ne |
> | zprostředkovatele identity | Ne |
> | připomenutí | Ne |

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Nasazení | Ne |
> | nasazení/operace | Ne |
> | deploymentScripts | Ano |
> | deploymentScripts/protokoly | Ne |
> | odkazy | Ne |
> | notifyResourceJobs | Ne |
> | Dodavateli | Ne |
> | resourceGroups | Ne |
> | Prostředky | Ne |
> | odběru | Ne |
> | předplatná/poskytovatelé | Ne |
> | předplatná/resourceGroups | Ne |
> | předplatná/ResourceGroups/prostředky | Ne |
> | předplatná/prostředky | Ne |
> | předplatná/TagNames | Ne |
> | předplatná/tagNames/tagValues | Ne |
> | Tenantů | Ne |

## <a name="microsoftsaas"></a>Microsoft. SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikací | Ano |
> | saasresources | Ne |

## <a name="microsoftscheduler"></a>Microsoft. Scheduler

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | jobcollections | Ano |

## <a name="microsoftsearch"></a>Microsoft. Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | resourceHealthMetadata | Ne |
> | searchServices | Ano |

## <a name="microsoftsecurity"></a>Microsoft. Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | adaptiveNetworkHardenings | Ne |
> | advancedThreatProtectionSettings | Ne |
> | výstrahy | Ne |
> | allowedConnections | Ne |
> | applicationWhitelistings | Ne |
> | assessmentMetadata | Ne |
> | posouzení | Ne |
> | automatizace | Ano |
> | AutoProvisioningSettings | Ne |
> | Předpisů | Ne |
> | dataCollectionAgents | Ne |
> | deviceSecurityGroups | Ne |
> | discoveredSecuritySolutions | Ne |
> | externalSecuritySolutions | Ne |
> | InformationProtectionPolicies | Ne |
> | iotSecuritySolutions | Ano |
> | iotSecuritySolutions/analyticsModels | Ne |
> | iotSecuritySolutions/analyticsModels/aggregatedAlerts | Ne |
> | iotSecuritySolutions/analyticsModels/aggregatedRecommendations | Ne |
> | jitNetworkAccessPolicies | Ne |
> | networkData | Ne |
> | playbookConfigurations | Ano |
> | Konfigurovaný | Ne |
> | ceny | Ne |
> | regulatoryComplianceStandards | Ne |
> | regulatoryComplianceStandards/regulatoryComplianceControls | Ne |
> | regulatoryComplianceStandards/regulatoryComplianceControls/regulatoryComplianceAssessments | Ne |
> | securityContacts | Ne |
> | securitySolutions | Ne |
> | securitySolutionsReferenceData | Ne |
> | securityStatuses | Ne |
> | securityStatusesSummaries | Ne |
> | serverVulnerabilityAssessments | Ne |
> | settings | Ne |
> | podhodnocení | Ne |
> | úlohy | Ne |
> | Topologie | Ne |
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
> | záložky | Ne |
> | věcech | Ne |
> | dataconnects | Ne |
> | podnikům | Ne |
> | entityQueries | Ne |
> | officeConsents | Ne |
> | settings | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Obsažené | Ano |
> | obory názvů/autorizačních pravidel | Ne |
> | obory názvů/disasterrecoveryconfigs | Ne |
> | obory názvů/eventgridfilters | Ne |
> | obory názvů/networkrulesets | Ne |
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
> | aplikací | Ano |
> | existující | Ano |
> | clustery/aplikace | Ne |
> | containerGroups | Ano |
> | containerGroupSets | Ano |
> | edgeclusters | Ano |
> | edgeclusters/aplikace | Ne |
> | sítí | Ano |
> | secretstores | Ano |
> | secretstores/certifikáty | Ne |
> | secretstores/tajné kódy | Ne |
> | volumes | Ano |

## <a name="microsoftservicefabricmesh"></a>Microsoft. ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikací | Ano |
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
> | providerRegistrations/resourceTypeRegistrations | Ne |
> | uvádění | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SignalR | Ano |
> | Signál/eventGridFilters | Ne |

## <a name="microsoftsiterecovery"></a>Microsoft. SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SiteRecoveryVault | Ano |

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
> | aplikací | Ano |
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
> | managedInstances/encryptionProtector | Ne |
> | managedInstances/klíče | Ne |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Ne |
> | managedInstances/vulnerabilityAssessments | Ne |
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
> | SqlVirtualMachineGroups/AvailabilityGroupListeners | Ne |
> | SqlVirtualMachines | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
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
> | storageSyncServices/syncGroups | Ne |
> | storageSyncServices/syncGroups/cloudEndpoints | Ne |
> | storageSyncServices/syncGroups/serverEndpoints | Ne |
> | storageSyncServices/pracovní postupy | Ne |

## <a name="microsoftstoragesyncdev"></a>Microsoft. StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices/registeredServer | Ne |
> | storageSyncServices/syncGroups | Ne |
> | storageSyncServices/syncGroups/cloudEndpoints | Ne |
> | storageSyncServices/syncGroups/serverEndpoints | Ne |
> | storageSyncServices/pracovní postupy | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft. StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices/registeredServer | Ne |
> | storageSyncServices/syncGroups | Ne |
> | storageSyncServices/syncGroups/cloudEndpoints | Ne |
> | storageSyncServices/syncGroups/serverEndpoints | Ne |
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
> | streamingjobs | Ano |

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | operaci | Ne |
> | CreateSubscription | Ne |
> | aby | Ne |
> | Změňte | Ne |
> | SubscriptionDefinitions | Ne |
> | SubscriptionOperations | Ne |

## <a name="microsofttimeseriesinsights"></a>Microsoft. TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Environment | Ano |
> | prostředí/accessPolicies | Ne |
> | prostředí/EventSources | Ano |
> | prostředí/referenceDataSets | Ano |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft. VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ano |
> | dedicatedCloudServices | Ano |
> | VirtualMachines | Ano |

## <a name="microsoftweb"></a>Microsoft. Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | apiManagementAccounts | Ne |
> | apiManagementAccounts/apiAcls | Ne |
> | apiManagementAccounts/rozhraní API | Ne |
> | apiManagementAccounts/API/apiAcls | Ne |
> | apiManagementAccounts/API/connectionAcls | Ne |
> | apiManagementAccounts/rozhraní API/připojení | Ne |
> | apiManagementAccounts/rozhraní API/připojení/connectionAcls | Ne |
> | apiManagementAccounts/API/localizedDefinitions | Ne |
> | apiManagementAccounts/connectionAcls | Ne |
> | apiManagementAccounts/připojení | Ne |
> | billingMeters | Ne |
> | Certifikáty | Ano |
> | connectionGateways | Ano |
> | Připojení | Ano |
> | customApis | Ano |
> | deletedSites | Ne |
> | functions | Ne |
> | hostingEnvironments | Ano |
> | hostingEnvironments/multiRolePools | Ne |
> | hostingEnvironments/workerPools | Ne |
> | publishingUsers | Ne |
> | Doporučit | Ne |
> | resourceHealthMetadata | Ne |
> | moduly runtime | Ne |
> | Serverových farem | Ano |
> | Serverových farem/eventGridFilters | Ne |
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
> | Oproti | Ne |
> | verifyHostingEnvironmentVnet | Ne |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft. WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | diagnosticSettings | Ne |
> | diagnosticSettingsCategories | Ne |

## <a name="microsoftwindowsiot"></a>Microsoft. WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | DeviceServices | Ano |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Konstrukční | Ne |
> | componentsSummary | Ne |
> | monitorInstances | Ne |
> | monitorInstancesSummary | Ne |
> | Monitor | Ne |
> | notificationSettings | Ne |

## <a name="next-steps"></a>Další kroky

Pokud chcete získat stejná data jako soubor hodnot oddělených čárkami, Stáhněte si [Complete-Mode-deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

---
title: Tag support for resources
description: Shows which Azure resource types support tags. Provides details for all Azure services.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 3a6fae9e0f85f7767b8d2d0b7f1364dbd83a81db
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230248"
---
# <a name="tag-support-for-azure-resources"></a>Tag support for Azure resources
This article describes whether a resource type supports [tags](resource-group-using-tags.md). The column labeled **Supports tags** indicates whether the resource type has a property for the tag. The column labeled **Tag in cost report** indicates whether that resource type passes the tag to the cost report.

To get the same data as a file of comma-separated values, download [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

Jump to a resource provider namespace:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.ADHybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppPlatform](#microsoftappplatform)
> - [Microsoft.Attestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Geneva](#microsoftazuregeneva)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Billing](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Capacity](#microsoftcapacity)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Consumption](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerLockbox](#microsoftcustomerlockbox)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.DesktopVirtualization](#microsoftdesktopvirtualization)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevOps](#microsoftdevops)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.DynamicsLcs](#microsoftdynamicslcs)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Gallery](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.Hydra](#microsofthydra)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [Microsoft.Intune](#microsoftintune)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.ManagedServices](#microsoftmanagedservices)
> - [Microsoft.Management](#microsoftmanagement)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.Marketplace](#microsoftmarketplace)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.MarketplaceOrdering](#microsoftmarketplaceordering)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.MixedReality](#microsoftmixedreality)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.SQL](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageReplication](#microsoftstoragereplication)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.Subscription](#microsoftsubscription)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsDefenderATP](#microsoftwindowsdefenderatp)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WorkloadMonitor](#microsoftworkloadmonitor)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ano | Ano |
> | DomainServices / oucontainer | Ne | Ne |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | supportProviders | Ne | Ne |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Ne | Ne |
> | addsservices | Ne | Ne |
> | agents | Ne | Ne |
> | anonymousapiusers | Ne | Ne |
> | konfigurace | Ne | Ne |
> | Protokoly | Ne | Ne |
> | reports | Ne | Ne |
> | servicehealthmetrics | Ne | Ne |
> | Služby | Ne | Ne |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurations | Ne | Ne |
> | generateRecommendations | Ne | Ne |
> | zprostředkovatele identity | Ne | Ne |
> | recommendations | Ne | Ne |
> | suppressions | Ne | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | actionRules | Ano | Ano |
> | výstrahy | Ne | Ne |
> | alertsList | Ne | Ne |
> | alertsMetaData | Ne | Ne |
> | alertsSummary | Ne | Ne |
> | alertsSummaryList | Ne | Ne |
> | feedback | Ne | Ne |
> | smartDetectorAlertRules | Ano | Ano |
> | smartDetectorRuntimeEnvironments | Ne | Ne |
> | smartGroups | Ne | Ne |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servery | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Ne | Ne |
> | služba | Ano | Ano |
> | validateServiceName | Ne | Ne |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ano | Ano |
> | configurationStores / eventGridFilters | Ne | Ne |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Spring | Ano | Ano |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | attestationProviders | Ne | Ne |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Ne | Ne |
> | dataAliases | Ne | Ne |
> | denyAssignments | Ne | Ne |
> | elevateAccess | Ne | Ne |
> | findOrphanRoleAssignments | Ne | Ne |
> | locks | Ne | Ne |
> | permissions | Ne | Ne |
> | policyAssignments | Ne | Ne |
> | policyDefinitions | Ne | Ne |
> | policySetDefinitions | Ne | Ne |
> | providerOperations | Ne | Ne |
> | roleAssignments | Ne | Ne |
> | roleDefinitions | Ne | Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ano | Ano |
> | automationAccounts / configurations | Ano | Ano |
> | automationAccounts / jobs | Ne | Ne |
> | automationAccounts / runbooks | Ano | Ano |
> | automationAccounts / softwareUpdateConfigurations | Ne | Ne |
> | automationAccounts / webhooks | Ne | Ne |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ano | Ano |
> | configurationStores / eventGridFilters | Ne | Ne |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Ne | Ne |
> | environments / accounts | Ne | Ne |
> | environments / accounts / namespaces | Ne | Ne |
> | environments / accounts / namespaces / configurations | Ne | Ne |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | b2cDirectories | Ano | Ne |
> | b2ctenants | Ne | Ne |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Ano | Ano |
> | postgresInstances | Ano | Ano |
> | sqlBigDataClusters | Ano | Ano |
> | sqlInstances | Ano | Ano |
> | sqlServerRegistrations | Ano | Ano |
> | sqlServerRegistrations / sqlServers | Ne | Ne |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registrations | Ano | Ano |
> | registrations / customerSubscriptions | Ne | Ne |
> | registrations / products | Ne | Ne |
> | verificationKeys | Ne | Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | batchAccounts | Ano | Ano |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | billingAccounts | Ne | Ne |
> | billingAccounts / agreements | Ne | Ne |
> | billingAccounts / billingPermissions | Ne | Ne |
> | billingAccounts / billingProfiles | Ne | Ne |
> | billingAccounts / billingProfiles / billingPermissions | Ne | Ne |
> | billingAccounts / billingProfiles / billingRoleAssignments | Ne | Ne |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Ne | Ne |
> | billingAccounts / billingProfiles / billingSubscriptions | Ne | Ne |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Ne | Ne |
> | billingAccounts / billingProfiles / customers | Ne | Ne |
> | billingAccounts / billingProfiles / invoices | Ne | Ne |
> | billingAccounts / billingProfiles / invoices / pricesheet | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / products | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Ne | Ne |
> | billingAccounts / BillingProfiles / patchOperations | Ne | Ne |
> | billingAccounts / billingProfiles / paymentMethods | Ne | Ne |
> | billingAccounts / billingProfiles / policies | Ne | Ne |
> | billingAccounts / billingProfiles / pricesheet | Ne | Ne |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Ne | Ne |
> | billingAccounts / billingProfiles / products | Ne | Ne |
> | billingAccounts / billingProfiles / transactions | Ne | Ne |
> | billingAccounts / billingRoleAssignments | Ne | Ne |
> | billingAccounts / billingRoleDefinitions | Ne | Ne |
> | billingAccounts / billingSubscriptions | Ne | Ne |
> | billingAccounts / billingSubscriptions / invoices | Ne | Ne |
> | billingAccounts / createBillingRoleAssignment | Ne | Ne |
> | billingAccounts / createInvoiceSectionOperations | Ne | Ne |
> | billingAccounts / customers | Ne | Ne |
> | billingAccounts / customers / billingPermissions | Ne | Ne |
> | billingAccounts / customers / billingSubscriptions | Ne | Ne |
> | billingAccounts / customers / initiateTransfer | Ne | Ne |
> | billingAccounts / customers / policies | Ne | Ne |
> | billingAccounts / customers / products | Ne | Ne |
> | billingAccounts / customers / transactions | Ne | Ne |
> | billingAccounts / customers / transfers | Ne | Ne |
> | billingAccounts / departments | Ne | Ne |
> | billingAccounts / enrollmentAccounts | Ne | Ne |
> | billingAccounts / invoices | Ne | Ne |
> | billingAccounts / invoiceSections | Ne | Ne |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Ne | Ne |
> | billingAccounts / invoiceSections / billingSubscriptions | Ne | Ne |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Ne | Ne |
> | billingAccounts / invoiceSections / elevate | Ne | Ne |
> | billingAccounts / invoiceSections / initiateTransfer | Ne | Ne |
> | billingAccounts / invoiceSections / patchOperations | Ne | Ne |
> | billingAccounts / invoiceSections / productMoveOperations | Ne | Ne |
> | billingAccounts / invoiceSections / products | Ne | Ne |
> | billingAccounts / invoiceSections / products / transfer | Ne | Ne |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Ne | Ne |
> | billingAccounts / invoiceSections / transactions | Ne | Ne |
> | billingAccounts / invoiceSections / transfers | Ne | Ne |
> | billingAccounts / lineOfCredit | Ne | Ne |
> | billingAccounts / patchOperations | Ne | Ne |
> | billingAccounts / paymentMethods | Ne | Ne |
> | billingAccounts / products | Ne | Ne |
> | billingAccounts / transactions | Ne | Ne |
> | billingPeriods | Ne | Ne |
> | billingPermissions | Ne | Ne |
> | billingProperty | Ne | Ne |
> | billingRoleAssignments | Ne | Ne |
> | billingRoleDefinitions | Ne | Ne |
> | createBillingRoleAssignment | Ne | Ne |
> | departments | Ne | Ne |
> | enrollmentAccounts | Ne | Ne |
> | invoices | Ne | Ne |
> | transfers | Ne | Ne |
> | transfers / acceptTransfer | Ne | Ne |
> | transfers / declineTransfer | Ne | Ne |
> | transfers / operationStatus | Ne | Ne |
> | transfers / validateTransfer | Ne | Ne |
> | validateAddress | Ne | Ne |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mapApis | Ano | Ano |
> | updateCommunicationPreference | Ne | Ne |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blockchainMembers | Ano | Ano |
> | cordaMembers | Ano | Ano |
> | diváků | Ano | Ano |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | blueprintAssignments | Ne | Ne |
> | blueprintAssignments / assignmentOperations | Ne | Ne |
> | blueprintAssignments / operations | Ne | Ne |
> | blueprints | Ne | Ne |
> | blueprints / artifacts | Ne | Ne |
> | blueprints / versions | Ne | Ne |
> | blueprints / versions / artifacts | Ne | Ne |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | botServices | Ano | Ano |
> | botServices / channels | Ne | Ne |
> | botServices / connections | Ne | Ne |
> | languages | Ne | Ne |
> | šablon | Ne | Ne |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Redis | Ano | Ano |
> | RedisConfigDefinition | Ne | Ne |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Ne | Ne |
> | calculateExchange | Ne | Ne |
> | calculatePrice | Ne | Ne |
> | calculatePurchasePrice | Ne | Ne |
> | catalogs | Ne | Ne |
> | commercialReservationOrders | Ne | Ne |
> | exchange | Ne | Ne |
> | placePurchaseOrder | Ne | Ne |
> | reservationOrders | Ne | Ne |
> | reservationOrders / calculateRefund | Ne | Ne |
> | reservationOrders / merge | Ne | Ne |
> | reservationOrders / reservations | Ne | Ne |
> | reservationOrders / reservations / revisions | Ne | Ne |
> | reservationOrders / return | Ne | Ne |
> | reservationOrders / split | Ne | Ne |
> | reservationOrders / swap | Ne | Ne |
> | reservations | Ne | Ne |
> | resources | Ne | Ne |
> | validateReservationOrder | Ne | Ne |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Ne | Ne |
> | CdnWebApplicationFirewallPolicies | Ano | Ano |
> | edgenodes | Ne | Ne |
> | profiles | Ano | Ano |
> | profiles / endpoints | Ano | Ano |
> | profiles / endpoints / customdomains | Ne | Ne |
> | profiles / endpoints / origins | Ne | Ne |
> | validateProbe | Ne | Ne |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | certificateOrders | Ano | Ano |
> | certificateOrders / certificates | Ne | Ne |
> | validateCertificateRegistrationInformation | Ne | Ne |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Ne | Ne |
> | domainNames | Ne | Ne |
> | domainNames / capabilities | Ne | Ne |
> | domainNames / internalLoadBalancers | Ne | Ne |
> | domainNames / serviceCertificates | Ne | Ne |
> | domainNames / slots | Ne | Ne |
> | domainNames / slots / roles | Ne | Ne |
> | domainNames / slots / roles / metricDefinitions | Ne | Ne |
> | domainNames / slots / roles / metrics | Ne | Ne |
> | moveSubscriptionResources | Ne | Ne |
> | operatingSystemFamilies | Ne | Ne |
> | operatingSystems | Ne | Ne |
> | quotas | Ne | Ne |
> | resourceTypes | Ne | Ne |
> | validateSubscriptionMoveAvailability | Ne | Ne |
> | virtualMachines | Ne | Ne |
> | virtualMachines / diagnosticSettings | Ne | Ne |
> | virtualMachines / metricDefinitions | Ne | Ne |
> | virtualMachines / metrics | Ne | Ne |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureResources | Ne | Ne |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Ne | Ne |
> | expressRouteCrossConnections | Ne | Ne |
> | expressRouteCrossConnections / peerings | Ne | Ne |
> | gatewaySupportedDevices | Ne | Ne |
> | networkSecurityGroups | Ne | Ne |
> | quotas | Ne | Ne |
> | reservedIps | Ne | Ne |
> | virtualNetworks | Ne | Ne |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Ne | Ne |
> | virtualNetworks / virtualNetworkPeerings | Ne | Ne |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capabilities | Ne | Ne |
> | disks | Ne | Ne |
> | images | Ne | Ne |
> | osImages | Ne | Ne |
> | osPlatformImages | Ne | Ne |
> | publicImages | Ne | Ne |
> | quotas | Ne | Ne |
> | storageAccounts | Ne | Ne |
> | storageAccounts / blobServices | Ne | Ne |
> | storageAccounts / fileServices | Ne | Ne |
> | storageAccounts / metricDefinitions | Ne | Ne |
> | storageAccounts / metrics | Ne | Ne |
> | storageAccounts / queueServices | Ne | Ne |
> | storageAccounts / services | Ne | Ne |
> | storageAccounts / services / diagnosticSettings | Ne | Ne |
> | storageAccounts / services / metricDefinitions | Ne | Ne |
> | storageAccounts / services / metrics | Ne | Ne |
> | storageAccounts / tableServices | Ne | Ne |
> | storageAccounts / vmImages | Ne | Ne |
> | vmImages | Ne | Ne |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | RateCard | Ne | Ne |
> | UsageAggregates | Ne | Ne |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilitySets | Ano | Ano |
> | diskEncryptionSets | Ano | Ano |
> | disks | Ano | Ano |
> | galleries | Ano | Ano |
> | galleries / applications | Ne | Ne |
> | galleries / applications / versions | Ne | Ne |
> | galleries / images | Ne | Ne |
> | galleries / images / versions | Ne | Ne |
> | hostGroups | Ano | Ano |
> | hostGroups / hosts | Ano | Ano |
> | images | Ano | Ano |
> | proximityPlacementGroups | Ano | Ano |
> | restorePointCollections | Ano | Ano |
> | restorePointCollections / restorePoints | Ne | Ne |
> | sharedVMImages | Ano | Ano |
> | sharedVMImages / versions | Ne | Ne |
> | snímky | Ano | Ano |
> | virtualMachines | Ano | Ano |
> | virtualMachines / extensions | Ano | Ano |
> | virtualMachines / metricDefinitions | Ne | Ne |
> | virtualMachineScaleSets | Ano | Ano |
> | virtualMachineScaleSets / extensions | Ne | Ne |
> | virtualMachineScaleSets / networkInterfaces | Ne | Ne |
> | virtualMachineScaleSets / publicIPAddresses | Ne | Ne |
> | virtualMachineScaleSets / virtualMachines | Ne | Ne |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Ne | Ne |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | AggregatedCost | Ne | Ne |
> | Zůstatky | Ne | Ne |
> | Rozpočty | Ne | Ne |
> | Poplatky | Ne | Ne |
> | CostTags | Ne | Ne |
> | credits | Ne | Ne |
> | stránka events | Ne | Ne |
> | Prognózy | Ne | Ne |
> | lots | Ne | Ne |
> | Marketplaces | Ne | Ne |
> | Pricesheets | Ne | Ne |
> | products | Ne | Ne |
> | ReservationDetails | Ne | Ne |
> | ReservationRecommendations | Ne | Ne |
> | ReservationSummaries | Ne | Ne |
> | ReservationTransactions | Ne | Ne |
> | Značky | Ne | Ne |
> | tenants | Ne | Ne |
> | Výrazy | Ne | Ne |
> | UsageDetails | Ne | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ano | Ano |
> | serviceAssociationLinks | Ne | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | registries | Ano | Ano |
> | registries / builds | Ne | Ne |
> | registries / builds / cancel | Ne | Ne |
> | registries / builds / getLogLink | Ne | Ne |
> | registries / buildTasks | Ano | Ano |
> | registries / buildTasks / steps | Ne | Ne |
> | registries / eventGridFilters | Ne | Ne |
> | registries / generateCredentials | Ne | Ne |
> | registries / getBuildSourceUploadUrl | Ne | Ne |
> | registries / GetCredentials | Ne | Ne |
> | registries / importImage | Ne | Ne |
> | registries / queueBuild | Ne | Ne |
> | registries / regenerateCredential | Ne | Ne |
> | registries / regenerateCredentials | Ne | Ne |
> | registries / replications | Ano | Ano |
> | registries / runs | Ne | Ne |
> | registries / runs / cancel | Ne | Ne |
> | registries / scheduleRun | Ne | Ne |
> | registries / scopeMaps | Ne | Ne |
> | registries / taskRuns | Ano | Ano |
> | registries / tasks | Ano | Ano |
> | registries / tokens | Ne | Ne |
> | registries / updatePolicies | Ne | Ne |
> | registries / webhooks | Ano | Ano |
> | registries / webhooks / getCallbackConfig | Ne | Ne |
> | registries / webhooks / ping | Ne | Ne |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | containerServices | Ano | Ano |
> | managedClusters | Ano | Ano |
> | openShiftManagedClusters | Ano | Ano |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Výstrahy | Ne | Ne |
> | BillingAccounts | Ne | Ne |
> | Rozpočty | Ne | Ne |
> | CloudConnectors | Ne | Ne |
> | Konektory | Ano | Ano |
> | Departments | Ne | Ne |
> | Dimenze | Ne | Ne |
> | EnrollmentAccounts | Ne | Ne |
> | Exporty | Ne | Ne |
> | ExternalBillingAccounts | Ne | Ne |
> | ExternalBillingAccounts / Alerts | Ne | Ne |
> | ExternalBillingAccounts / Dimensions | Ne | Ne |
> | ExternalBillingAccounts / Forecast | Ne | Ne |
> | ExternalBillingAccounts / Query | Ne | Ne |
> | ExternalSubscriptions | Ne | Ne |
> | ExternalSubscriptions / Alerts | Ne | Ne |
> | ExternalSubscriptions / Dimensions | Ne | Ne |
> | ExternalSubscriptions / Forecast | Ne | Ne |
> | ExternalSubscriptions / Query | Ne | Ne |
> | Forecast | Ne | Ne |
> | Dotaz | Ne | Ne |
> | register | Ne | Ne |
> | Reportconfigs | Ne | Ne |
> | Sestavy | Ne | Ne |
> | Nastavení | Ne | Ne |
> | showbackRules | Ne | Ne |
> | Zobrazení | Ne | Ne |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | requests | Ne | Ne |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | associations | Ne | Ne |
> | resourceProviders | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Ano | Ano |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DataBoxEdgeDevices | Ano | Ano |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Ano | Ne |
> | workspaces / virtualNetworkPeerings | Ne | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | catalogs | Ano | Ano |
> | datacatalogs | Ano | Ano |
> | datacatalogs / datasources | Ne | Ne |
> | datacatalogs / datasources / scans | Ne | Ne |
> | datacatalogs / datasources / scans / datasets | Ne | Ne |
> | datacatalogs / datasources / scans / triggers | Ne | Ne |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataFactories | Ano | Ne |
> | dataFactories / diagnosticSettings | Ne | Ne |
> | dataFactories / metricDefinitions | Ne | Ne |
> | dataFactorySchema | Ne | Ne |
> | factories | Ano | Ne |
> | factories / integrationRuntimes | Ne | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | accounts / dataLakeStoreAccounts | Ne | Ne |
> | accounts / storageAccounts | Ne | Ne |
> | accounts / storageAccounts / containers | Ne | Ne |
> | accounts / transferAnalyticsUnits | Ne | Ne |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | accounts / eventGridFilters | Ne | Ne |
> | accounts / firewallRules | Ne | Ne |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Služby | Ne | Ne |
> | services / projects | Ne | Ne |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | accounts / shares | Ne | Ne |
> | accounts / shares / datasets | Ne | Ne |
> | accounts / shares / invitations | Ne | Ne |
> | accounts / shares / providersharesubscriptions | Ne | Ne |
> | accounts / shares / synchronizationSettings | Ne | Ne |
> | accounts / sharesubscriptions | Ne | Ne |
> | accounts / sharesubscriptions / consumerSourceDataSets | Ne | Ne |
> | accounts / sharesubscriptions / datasetmappings | Ne | Ne |
> | accounts / sharesubscriptions / triggers | Ne | Ne |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servery | Ano | Ano |
> | servers / advisors | Ne | Ne |
> | servers / privateEndpointConnectionProxies | Ne | Ne |
> | servers / privateEndpointConnections | Ne | Ne |
> | servers / privateLinkResources | Ne | Ne |
> | servers / queryTexts | Ne | Ne |
> | servers / recoverableServers | Ne | Ne |
> | servers / topQueryStatistics | Ne | Ne |
> | servers / virtualNetworkRules | Ne | Ne |
> | servers / waitStatistics | Ne | Ne |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | servery | Ano | Ano |
> | servers / advisors | Ne | Ne |
> | servers / privateEndpointConnectionProxies | Ne | Ne |
> | servers / privateEndpointConnections | Ne | Ne |
> | servers / privateLinkResources | Ne | Ne |
> | servers / queryTexts | Ne | Ne |
> | servers / recoverableServers | Ne | Ne |
> | servers / topQueryStatistics | Ne | Ne |
> | servers / virtualNetworkRules | Ne | Ne |
> | servers / waitStatistics | Ne | Ne |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | serverGroups | Ano | Ano |
> | servery | Ano | Ano |
> | servers / advisors | Ne | Ne |
> | servers / keys | Ne | Ne |
> | servers / privateEndpointConnectionProxies | Ne | Ne |
> | servers / privateEndpointConnections | Ne | Ne |
> | servers / privateLinkResources | Ne | Ne |
> | servers / queryTexts | Ne | Ne |
> | servers / recoverableServers | Ne | Ne |
> | servers / topQueryStatistics | Ne | Ne |
> | servers / virtualNetworkRules | Ne | Ne |
> | servers / waitStatistics | Ne | Ne |
> | serversv2 | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | artifactSources | Ano | Ano |
> | rollouts | Ano | Ano |
> | serviceTopologies | Ano | Ano |
> | serviceTopologies / services | Ano | Ano |
> | serviceTopologies / services / serviceUnits | Ano | Ano |
> | kroky | Ano | Ano |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationgroups | Ano | Ano |
> | applicationgroups / applications | Ne | Ne |
> | applicationgroups / desktops | Ne | Ne |
> | applicationgroups / startmenuitems | Ne | Ne |
> | hostpools | Ano | Ano |
> | hostpools / sessionhosts | Ne | Ne |
> | hostpools / sessionhosts / usersessions | Ne | Ne |
> | hostpools / usersessions | Ne | Ne |
> | workspaces | Ano | Ano |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | ElasticPools | Ano | Ano |
> | ElasticPools / IotHubTenants | Ano | Ano |
> | IotHubs | Ano | Ano |
> | IotHubs / eventGridFilters | Ne | Ne |
> | ProvisioningServices | Ano | Ano |
> | usages | Ne | Ne |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | pipelines | Ano | Ano |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | controllers | Ano | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labcenters | Ano | Ano |
> | labs | Ano | Ano |
> | labs / environments | Ano | Ano |
> | labs / serviceRunners | Ano | Ano |
> | labs / virtualMachines | Ano | Ano |
> | schedules | Ano | Ano |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Ne | Ne |
> | databaseAccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Ano | Ano |
> | domains / domainOwnershipIdentifiers | Ne | Ne |
> | generateSsoRequest | Ne | Ne |
> | topLevelDomains | Ne | Ne |
> | validateDomainRegistrationInformation | Ne | Ne |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | lcsprojects | Ne | Ne |
> | lcsprojects / clouddeployments | Ne | Ne |
> | lcsprojects / connectors | Ne | Ne |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Služby | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | domains | Ano | Ano |
> | domains / topics | Ne | Ne |
> | eventSubscriptions | Ne | Ne |
> | extensionTopics | Ne | Ne |
> | topics | Ano | Ano |
> | topicTypes | Ne | Ne |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ano | Ano |
> | namespaces | Ano | Ano |
> | namespaces / authorizationrules | Ne | Ne |
> | namespaces / disasterrecoveryconfigs | Ne | Ne |
> | namespaces / eventhubs | Ne | Ne |
> | namespaces / eventhubs / authorizationrules | Ne | Ne |
> | namespaces / eventhubs / consumergroups | Ne | Ne |
> | namespaces / networkrulesets | Ne | Ne |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Database | Ne | Ne |
> | providers | Ne | Ne |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | enroll | Ne | Ne |
> | galleryitems | Ne | Ne |
> | generateartifactaccessuri | Ne | Ne |
> | myareas | Ne | Ne |
> | myareas / areas | Ne | Ne |
> | myareas / areas / areas | Ne | Ne |
> | myareas / areas / areas / galleryitems | Ne | Ne |
> | myareas / areas / galleryitems | Ne | Ne |
> | myareas / galleryitems | Ne | Ne |
> | register | Ne | Ne |
> | resources | Ne | Ne |
> | retrieveresourcesbyid | Ne | Ne |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | configurationProfileAssignments | Ne | Ne |
> | guestConfigurationAssignments | Ne | Ne |
> | software | Ne | Ne |
> | softwareUpdateProfile | Ne | Ne |
> | softwareUpdates | Ne | Ne |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Ano | Ano |
> | sapMonitors | Ano | Ano |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedHSMs | Ano | Ano |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ano | Ano |
> | clusters / applications | Ne | Ne |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Služby | Ano | Ano |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | machines | Ano | Ano |
> | machines / extensions | Ano | Ano |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dataManagers | Ano | Ano |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Ano | Ano |
> | networkScopes | Ano | Ano |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobs | Ano | Ano |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Ne | Ne |
> | diagnosticSettingsCategories | Ne | Ne |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appTemplates | Ne | Ne |
> | IoTApps | Ano | Ano |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Graf | Ano | Ano |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deletedVaults | Ne | Ne |
> | hsmPools | Ano | Ano |
> | vaults | Ano | Ano |
> | vaults / accessPolicies | Ne | Ne |
> | vaults / eventGridFilters | Ne | Ne |
> | vaults / secrets | Ne | Ne |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ano | Ano |
> | clusters / attacheddatabaseconfigurations | Ne | Ne |
> | clusters / databases | Ne | Ne |
> | clusters / databases / dataconnections | Ne | Ne |
> | clusters / databases / eventhubconnections | Ne | Ne |
> | clusters / sharedidentities | Ne | Ne |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | labaccounts | Ano | Ano |
> | uživatelů | Ne | Ne |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hostingEnvironments | Ano | Ano |
> | integrationAccounts | Ano | Ano |
> | integrationServiceEnvironments | Ano | Ano |
> | integrationServiceEnvironments / managedApis | Ano | Ano |
> | isolatedEnvironments | Ano | Ano |
> | workflows | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Ano | Ano |
> | webServices | Ano | Ano |
> | Pracovní prostory | Ano | Ano |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaces | Ano | Ano |
> | workspaces / computes | Ne | Ne |
> | workspaces / eventGridFilters | Ne | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | Identities | Ne | Ne |
> | userAssignedIdentities | Ano | Ano |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Ne | Ne |
> | registrationAssignments | Ne | Ne |
> | registrationDefinitions | Ne | Ne |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | getEntities | Ne | Ne |
> | managementGroups | Ne | Ne |
> | resources | Ne | Ne |
> | startTenantBackfill | Ne | Ne |
> | tenantBackfillStatus | Ne | Ne |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | accounts / eventGridFilters | Ne | Ne |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | offers | Ne | Ne |
> | offerTypes | Ne | Ne |
> | offerTypes / publishers | Ne | Ne |
> | offerTypes / publishers / offers | Ne | Ne |
> | offerTypes / publishers / offers / plans | Ne | Ne |
> | offerTypes / publishers / offers / plans / agreements | Ne | Ne |
> | offerTypes / publishers / offers / plans / configs | Ne | Ne |
> | offerTypes / publishers / offers / plans / configs / importImage | Ne | Ne |
> | privategalleryitems | Ne | Ne |
> | products | Ne | Ne |
> | publishers | Ne | Ne |
> | publishers / offers | Ne | Ne |
> | publishers / offers / amendments | Ne | Ne |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ano | Ano |
> | updateCommunicationPreference | Ne | Ne |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | agreements | Ne | Ne |
> | offertypes | Ne | Ne |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | mediaservices | Ano | Ano |
> | mediaservices / accountFilters | Ne | Ne |
> | mediaservices / assets | Ne | Ne |
> | mediaservices / assets / assetFilters | Ne | Ne |
> | mediaservices / contentKeyPolicies | Ne | Ne |
> | mediaservices / eventGridFilters | Ne | Ne |
> | mediaservices / liveEventOperations | Ne | Ne |
> | mediaservices / liveEvents | Ano | Ano |
> | mediaservices / liveEvents / liveOutputs | Ne | Ne |
> | mediaservices / liveOutputOperations | Ne | Ne |
> | mediaservices / mediaGraphs | Ne | Ne |
> | mediaservices / streamingEndpointOperations | Ne | Ne |
> | mediaservices / streamingEndpoints | Ano | Ano |
> | mediaservices / streamingLocators | Ne | Ne |
> | mediaservices / streamingPolicies | Ne | Ne |
> | mediaservices / transforms | Ne | Ne |
> | mediaservices / transforms / jobs | Ne | Ne |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | appClusters | Ano | Ano |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | assessmentProjects | Ano | Ano |
> | migrateprojects | Ano | Ano |
> | projects | Ano | Ano |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ano | Ano |
> | objectUnderstandingAccounts | Ano | Ano |
> | remoteRenderingAccounts | Ano | Ano |
> | spatialAnchorsAccounts | Ano | Ano |
> | surfaceReconstructionAccounts | Ano | Ano |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ano | Ne |
> | netAppAccounts / capacityPools | Ano | Ne |
> | netAppAccounts / capacityPools / volumes | Ano | Ne |
> | netAppAccounts / capacityPools / volumes / mountTargets | Ano | Ne |
> | netAppAccounts / capacityPools / volumes / snapshots | Ano | Ne |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ano | Ano |
> | applicationGatewayWebApplicationFirewallPolicies | Ano | Ano |
> | applicationSecurityGroups | Ano | Ano |
> | azureFirewallFqdnTags | Ne | Ne |
> | azureFirewalls | Ano | Ne |
> | bastionHosts | Ano | Ano |
> | bgpServiceCommunities | Ne | Ne |
> | connections | Ano | Ano |
> | ddosCustomPolicies | Ano | Ano |
> | ddosProtectionPlans | Ano | Ano |
> | dnsOperationStatuses | Ne | Ne |
> | dnszones | Ano | Ano |
> | dnszones / A | Ne | Ne |
> | dnszones / AAAA | Ne | Ne |
> | dnszones / all | Ne | Ne |
> | dnszones / CAA | Ne | Ne |
> | dnszones / CNAME | Ne | Ne |
> | dnszones / MX | Ne | Ne |
> | dnszones / NS | Ne | Ne |
> | dnszones / PTR | Ne | Ne |
> | dnszones / recordsets | Ne | Ne |
> | dnszones / SOA | Ne | Ne |
> | dnszones / SRV | Ne | Ne |
> | dnszones / TXT | Ne | Ne |
> | expressRouteCircuits | Ano | Ano |
> | expressRouteCrossConnections | Ano | Ano |
> | expressRouteGateways | Ano | Ano |
> | expressRoutePorts | Ano | Ano |
> | expressRouteServiceProviders | Ne | Ne |
> | firewallPolicies | Ano | Ano |
> | frontdoors | Yes, but limited (see [note below](#frontdoor)) | Ano |
> | frontdoorWebApplicationFirewallManagedRuleSets | Yes, but limited (see [note below](#frontdoor)) | Ne |
> | frontdoorWebApplicationFirewallPolicies | Yes, but limited (see [note below](#frontdoor)) | Ano |
> | getDnsResourceReference | Ne | Ne |
> | internalNotify | Ne | Ne |
> | loadBalancers | Ano | Ne |
> | localNetworkGateways | Ano | Ano |
> | natGateways | Ano | Ano |
> | networkIntentPolicies | Ano | Ano |
> | networkInterfaces | Ano | Ano |
> | networkProfiles | Ano | Ano |
> | networkSecurityGroups | Ano | Ano |
> | networkWatchers | Ano | Ne |
> | networkWatchers / connectionMonitors | Ano | Ne |
> | networkWatchers / lenses | Ano | Ne |
> | networkWatchers / pingMeshes | Ano | Ne |
> | p2sVpnGateways | Ano | Ano |
> | privateDnsOperationStatuses | Ne | Ne |
> | privateDnsZones | Ano | Ano |
> | privateDnsZones / A | Ne | Ne |
> | privateDnsZones / AAAA | Ne | Ne |
> | privateDnsZones / all | Ne | Ne |
> | privateDnsZones / CNAME | Ne | Ne |
> | privateDnsZones / MX | Ne | Ne |
> | privateDnsZones / PTR | Ne | Ne |
> | privateDnsZones / SOA | Ne | Ne |
> | privateDnsZones / SRV | Ne | Ne |
> | privateDnsZones / TXT | Ne | Ne |
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
> | trafficmanagerprofiles/heatMaps | Ne | Ne |
> | trafficManagerUserMetricsKeys | Ne | Ne |
> | virtualHubs | Ano | Ano |
> | virtualNetworkGateways | Ano | Ano |
> | virtualNetworks | Ano | Ano |
> | virtualNetworkTaps | Ano | Ano |
> | virtualWans | Ano | Ano |
> | vpnGateways | Ano | Ne |
> | vpnSites | Ano | Ano |
> | webApplicationFirewallPolicies | Ano | Ano |

<a id="frontdoor" />

> [!NOTE]
> For Azure Front Door Service, you can apply tags when creating the resource, but updating or adding tags is not currently supported.


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Ano | Ne |
> | namespaces / notificationHubs | Ano | Ne |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Ano | Ano |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ano | Ano |
> | ImportSites | Ano | Ano |
> | ServerSites | Ano | Ano |
> | VMwareSites | Ano | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | clusters | Ano | Ano |
> | zařízení | Ne | Ne |
> | linkTargets | Ne | Ne |
> | storageInsightConfigs | Ne | Ne |
> | workspaces | Ano | Ano |
> | workspaces / dataSources | Ne | Ne |
> | workspaces / linkedServices | Ne | Ne |
> | workspaces / query | Ne | Ne |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managementassociations | Ne | Ne |
> | managementconfigurations | Ano | Ano |
> | Řešení | Ano | Ano |
> | views | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | legacyPeerings | Ne | Ne |
> | peerAsns | Ne | Ne |
> | peerings | Ano | Ano |
> | peeringServiceProviders | Ne | Ne |
> | peeringServices | Ano | Ano |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | policyEvents | Ne | Ne |
> | policyMetadata | Ne | Ne |
> | policyStates | Ne | Ne |
> | policyTrackedResources | Ne | Ne |
> | remediations | Ne | Ne |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | consoles | Ne | Ne |
> | dashboards | Ano | Ano |
> | userSettings | Ne | Ne |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | workspaceCollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | capacities | Ano | Ano |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Ne | Ne |
> | vaults | Ano | Ano |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Ano | Ano |
> | namespaces / authorizationrules | Ne | Ne |
> | namespaces / hybridconnections | Ne | Ne |
> | namespaces / hybridconnections / authorizationrules | Ne | Ne |
> | namespaces / wcfrelays | Ne | Ne |
> | namespaces / wcfrelays / authorizationrules | Ne | Ne |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | accounts | Ne | Ne |
> | collections | Ano | Ano |
> | collections / applications | Ne | Ne |
> | collections / securityprincipals | Ne | Ne |
> | templateImages | Ne | Ne |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dotazy | Ano | Ano |
> | resourceChangeDetails | Ne | Ne |
> | resourceChanges | Ne | Ne |
> | resources | Ne | Ne |
> | resourcesHistory | Ne | Ne |
> | subscriptionsStatus | Ne | Ne |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | availabilityStatuses | Ne | Ne |
> | childAvailabilityStatuses | Ne | Ne |
> | childResources | Ne | Ne |
> | stránka events | Ne | Ne |
> | impactedResources | Ne | Ne |
> | zprostředkovatele identity | Ne | Ne |
> | notifications | Ne | Ne |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | deployments | Ano | Ne |
> | deployments / operations | Ne | Ne |
> | deploymentScripts | Ano | Ano |
> | deploymentScripts / logs | Ne | Ne |
> | links | Ne | Ne |
> | notifyResourceJobs | Ne | Ne |
> | providers | Ne | Ne |
> | resourceGroups | Ano | Ne |
> | resources | Ne | Ne |
> | subscriptions | Ne | Ne |
> | subscriptions / providers | Ne | Ne |
> | subscriptions / resources | Ne | Ne |
> | subscriptions / tagnames | Ne | Ne |
> | subscriptions / tagNames / tagValues | Ne | Ne |
> | tenants | Ne | Ne |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aplikací | Ano | Ano |
> | saasresources | Ne | Ne |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | jobcollections | Ano | Ano |

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Ne | Ne |
> | searchServices | Ano | Ano |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | adaptiveNetworkHardenings | Ne | Ne |
> | advancedThreatProtectionSettings | Ne | Ne |
> | výstrahy | Ne | Ne |
> | allowedConnections | Ne | Ne |
> | applicationWhitelistings | Ne | Ne |
> | assessmentMetadata | Ne | Ne |
> | assessments | Ne | Ne |
> | autoDismissAlertsRules | Ne | Ne |
> | automations | Ano | Ano |
> | AutoProvisioningSettings | Ne | Ne |
> | Compliances | Ne | Ne |
> | dataCollectionAgents | Ne | Ne |
> | deviceSecurityGroups | Ne | Ne |
> | discoveredSecuritySolutions | Ne | Ne |
> | externalSecuritySolutions | Ne | Ne |
> | InformationProtectionPolicies | Ne | Ne |
> | iotSecuritySolutions | Ano | Ano |
> | iotSecuritySolutions / analyticsModels | Ne | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Ne | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Ne | Ne |
> | jitNetworkAccessPolicies | Ne | Ne |
> | networkData | Ne | Ne |
> | policies | Ne | Ne |
> | pricings | Ne | Ne |
> | regulatoryComplianceStandards | Ne | Ne |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Ne | Ne |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Ne | Ne |
> | securityContacts | Ne | Ne |
> | securitySolutions | Ne | Ne |
> | securitySolutionsReferenceData | Ne | Ne |
> | securityStatuses | Ne | Ne |
> | securityStatusesSummaries | Ne | Ne |
> | serverVulnerabilityAssessments | Ne | Ne |
> | settings | Ne | Ne |
> | subAssessments | Ne | Ne |
> | úlohy | Ne | Ne |
> | topologies | Ne | Ne |
> | workspaceSettings | Ne | Ne |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Ne | Ne |
> | diagnosticSettingsCategories | Ne | Ne |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aggregations | Ne | Ne |
> | alertRules | Ne | Ne |
> | alertRuleTemplates | Ne | Ne |
> | bookmarks | Ne | Ne |
> | cases | Ne | Ne |
> | dataConnectors | Ne | Ne |
> | entities | Ne | Ne |
> | entityQueries | Ne | Ne |
> | officeConsents | Ne | Ne |
> | settings | Ne | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | namespaces | Ano | Ne |
> | namespaces / authorizationrules | Ne | Ne |
> | namespaces / disasterrecoveryconfigs | Ne | Ne |
> | namespaces / eventgridfilters | Ne | Ne |
> | namespaces / networkrulesets | Ne | Ne |
> | namespaces / queues | Ne | Ne |
> | namespaces / queues / authorizationrules | Ne | Ne |
> | namespaces / topics | Ne | Ne |
> | namespaces / topics / authorizationrules | Ne | Ne |
> | namespaces / topics / subscriptions | Ne | Ne |
> | namespaces / topics / subscriptions / rules | Ne | Ne |
> | premiumMessagingRegions | Ne | Ne |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aplikací | Ano | Ano |
> | clusters | Ano | Ano |
> | clusters / applications | Ne | Ne |
> | containerGroups | Ano | Ano |
> | containerGroupSets | Ano | Ano |
> | edgeclusters | Ano | Ano |
> | edgeclusters / applications | Ne | Ne |
> | networks | Ano | Ano |
> | secretstores | Ano | Ano |
> | secretstores / certificates | Ne | Ne |
> | secretstores / secrets | Ne | Ne |
> | volumes | Ano | Ano |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | aplikací | Ano | Ano |
> | containerGroups | Ano | Ano |
> | gateways | Ano | Ano |
> | networks | Ano | Ano |
> | secrets | Ano | Ano |
> | volumes | Ano | Ano |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | providerRegistrations | Ne | Ne |
> | providerRegistrations / resourceTypeRegistrations | Ne | Ne |
> | rollouts | Ano | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SignalR | Ano | Ano |
> | SignalR / eventGridFilters | Ne | Ne |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SiteRecoveryVault | Ano | Ano |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Ne | Ne |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Ano | Ano |
> | aplikací | Ano | Ano |
> | jitRequests | Ano | Ano |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managedInstances | Ano | Ano |
> | managedInstances / databases | Yes (see [note below](#sqlnote)) | Ano |
> | managedInstances / databases / backupShortTermRetentionPolicies | Ne | Ne |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Ne | Ne |
> | managedInstances / databases / vulnerabilityAssessments | Ne | Ne |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Ne | Ne |
> | managedInstances / encryptionProtector | Ne | Ne |
> | managedInstances / keys | Ne | Ne |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Ne | Ne |
> | managedInstances / vulnerabilityAssessments | Ne | Ne |
> | servery | Ano | Ano |
> | servers / administrators | Ne | Ne |
> | servers / communicationLinks | Ne | Ne |
> | servers / databases | Yes (see [note below](#sqlnote)) | Ano |
> | servers / encryptionProtector | Ne | Ne |
> | servers / firewallRules | Ne | Ne |
> | servers / keys | Ne | Ne |
> | servers / restorableDroppedDatabases | Ne | Ne |
> | servers / serviceobjectives | Ne | Ne |
> | servers / tdeCertificates | Ne | Ne |
> | virtualClusters | Ne | Ne |

<a id="sqlnote" />

> [!NOTE]
> The Master database doesn't support tags, but other databases, including Azure SQL Data Warehouse databases, support tags. Azure SQL Data Warehouse databases must be in Active (not Paused) state.

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | SqlVirtualMachineGroups | Ano | Ano |
> | SqlVirtualMachineGroups / AvailabilityGroupListeners | Ne | Ne |
> | SqlVirtualMachines | Ano | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageAccounts | Ano | Ano |
> | storageAccounts / blobServices | Ne | Ne |
> | storageAccounts / fileServices | Ne | Ne |
> | storageAccounts / queueServices | Ne | Ne |
> | storageAccounts / services | Ne | Ne |
> | storageAccounts / services / metricDefinitions | Ne | Ne |
> | storageAccounts / tableServices | Ne | Ne |
> | usages | Ne | Ne |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | caches | Ano | Ano |
> | caches / storageTargets | Ne | Ne |
> | usageModels | Ne | Ne |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | replicationGroups | Ne | Ne |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ano | Ano |
> | storageSyncServices / registeredServers | Ne | Ne |
> | storageSyncServices / syncGroups | Ne | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne | Ne |
> | storageSyncServices / workflows | Ne | Ne |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ano | Ano |
> | storageSyncServices / registeredServers | Ne | Ne |
> | storageSyncServices / syncGroups | Ne | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne | Ne |
> | storageSyncServices / workflows | Ne | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ano | Ano |
> | storageSyncServices / registeredServers | Ne | Ne |
> | storageSyncServices / syncGroups | Ne | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne | Ne |
> | storageSyncServices / workflows | Ne | Ne |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | managers | Ano | Ano |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | streamingjobs | Yes (see note below) | Ano |

> [!NOTE]
> You can't add a tag when streamingjobs is running. Stop the resource to add a tag.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | cancel | Ne | Ne |
> | CreateSubscription | Ne | Ne |
> | enable | Ne | Ne |
> | rename | Ne | Ne |
> | SubscriptionDefinitions | Ne | Ne |
> | SubscriptionOperations | Ne | Ne |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | environments | Ano | Ne |
> | environments / accessPolicies | Ne | Ne |
> | environments / eventsources | Ano | Ne |
> | environments / referenceDataSets | Ano | Ne |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ano | Ano |
> | dedicatedCloudServices | Ano | Ano |
> | virtualMachines | Ano | Ano |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Ne | Ne |
> | apiManagementAccounts / apiAcls | Ne | Ne |
> | apiManagementAccounts / apis | Ne | Ne |
> | apiManagementAccounts / apis / apiAcls | Ne | Ne |
> | apiManagementAccounts / apis / connectionAcls | Ne | Ne |
> | apiManagementAccounts / apis / connections | Ne | Ne |
> | apiManagementAccounts / apis / connections / connectionAcls | Ne | Ne |
> | apiManagementAccounts / apis / localizedDefinitions | Ne | Ne |
> | apiManagementAccounts / connectionAcls | Ne | Ne |
> | apiManagementAccounts / connections | Ne | Ne |
> | billingMeters | Ne | Ne |
> | certificates | Ano | Ano |
> | connectionGateways | Ano | Ano |
> | connections | Ano | Ano |
> | customApis | Ano | Ano |
> | deletedSites | Ne | Ne |
> | functions | Ne | Ne |
> | hostingEnvironments | Ano | Ano |
> | hostingEnvironments / multiRolePools | Ne | Ne |
> | hostingEnvironments / workerPools | Ne | Ne |
> | publishingUsers | Ne | Ne |
> | recommendations | Ne | Ne |
> | resourceHealthMetadata | Ne | Ne |
> | runtimes | Ne | Ne |
> | serverFarms | Ano | Ano |
> | serverFarms / eventGridFilters | Ne | Ne |
> | sites | Ano | Ano |
> | sites / config  | Ne | Ne |
> | sites / eventGridFilters | Ne | Ne |
> | sites / hostNameBindings | Ne | Ne |
> | sites / networkConfig | Ne | Ne |
> | sites / premieraddons | Ano | Ano |
> | sites / slots | Ano | Ano |
> | sites / slots / eventGridFilters | Ne | Ne |
> | sites / slots / hostNameBindings | Ne | Ne |
> | sites / slots / networkConfig | Ne | Ne |
> | sourceControls | Ne | Ne |
> | validate | Ne | Ne |
> | verifyHostingEnvironmentVnet | Ne | Ne |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Ne | Ne |
> | diagnosticSettingsCategories | Ne | Ne |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | DeviceServices | Ano | Ano |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Supports tags | Tag in cost report |
> | ------------- | ----------- | ----------- |
> | components | Ne | Ne |
> | componentsSummary | Ne | Ne |
> | monitorInstances | Ne | Ne |
> | monitorInstancesSummary | Ne | Ne |
> | monitors | Ne | Ne |
> | notificationSettings | Ne | Ne |

## <a name="next-steps"></a>Další kroky

To learn how to apply tags to resources, see [Use tags to organize your Azure resources](resource-group-using-tags.md).

---
title: Úplné odstranění režimu
description: Shows how resource types handle complete mode deletion in Azure Resource Manager templates.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 075dd5071d6c59c6cc73fff8d51a495b012665dd
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232690"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Deletion of Azure resources for complete mode deployments

This article describes how resource types handle deletion when not in a template that is deployed in complete mode.

The resource types marked with **Yes** are deleted when the type isn't in the template deployed with complete mode.

The resource types marked with **No** aren't automatically deleted when not in the template; however, they're deleted if the parent resource is deleted. For a full description of the behavior, see [Azure Resource Manager deployment modes](deployment-modes.md).

If you deploy to [more than one resource group in a template](resource-manager-cross-resource-group-deployment.md), resources in the resource group specified in the deployment operation are eligible to be deleted. Resources in the secondary resource groups aren't deleted.

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
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | DomainServices | Ano |
> | DomainServices / oucontainer | Ne |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | supportProviders | Ne |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aadsupportcases | Ne |
> | addsservices | Ne |
> | agents | Ne |
> | anonymousapiusers | Ne |
> | konfigurace | Ne |
> | Protokoly | Ne |
> | reports | Ne |
> | servicehealthmetrics | Ne |
> | Služby | Ne |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | configurations | Ne |
> | generateRecommendations | Ne |
> | zprostředkovatele identity | Ne |
> | recommendations | Ne |
> | suppressions | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | actionRules | Ano |
> | výstrahy | Ne |
> | alertsList | Ne |
> | alertsMetaData | Ne |
> | alertsSummary | Ne |
> | alertsSummaryList | Ne |
> | feedback | Ne |
> | smartDetectorAlertRules | Ano |
> | smartDetectorRuntimeEnvironments | Ne |
> | smartGroups | Ne |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servery | Ano |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | reportFeedback | Ne |
> | služba | Ano |
> | validateServiceName | Ne |

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | configurationStores | Ano |
> | configurationStores / eventGridFilters | Ne |

## <a name="microsoftappplatform"></a>Microsoft.AppPlatform

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Spring | Ano |

## <a name="microsoftattestation"></a>Microsoft.Attestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | attestationProviders | Ne |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | classicAdministrators | Ne |
> | dataAliases | Ne |
> | denyAssignments | Ne |
> | elevateAccess | Ne |
> | findOrphanRoleAssignments | Ne |
> | locks | Ne |
> | permissions | Ne |
> | policyAssignments | Ne |
> | policyDefinitions | Ne |
> | policySetDefinitions | Ne |
> | providerOperations | Ne |
> | roleAssignments | Ne |
> | roleDefinitions | Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | automationAccounts | Ano |
> | automationAccounts / configurations | Ano |
> | automationAccounts / jobs | Ne |
> | automationAccounts / runbooks | Ano |
> | automationAccounts / softwareUpdateConfigurations | Ne |
> | automationAccounts / webhooks | Ne |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | configurationStores | Ano |
> | configurationStores / eventGridFilters | Ne |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | environments | Ne |
> | environments / accounts | Ne |
> | environments / accounts / namespaces | Ne |
> | environments / accounts / namespaces / configurations | Ne |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | b2cDirectories | Ano |
> | b2ctenants | Ne |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hybridDataManagers | Ano |
> | postgresInstances | Ano |
> | sqlBigDataClusters | Ano |
> | sqlInstances | Ano |
> | sqlServerRegistrations | Ano |
> | sqlServerRegistrations / sqlServers | Ne |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | registrations | Ano |
> | registrations / customerSubscriptions | Ne |
> | registrations / products | Ne |
> | verificationKeys | Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | batchAccounts | Ano |

## <a name="microsoftbilling"></a>Microsoft.Billing

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | billingAccounts | Ne |
> | billingAccounts / agreements | Ne |
> | billingAccounts / billingPermissions | Ne |
> | billingAccounts / billingProfiles | Ne |
> | billingAccounts / billingProfiles / billingPermissions | Ne |
> | billingAccounts / billingProfiles / billingRoleAssignments | Ne |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Ne |
> | billingAccounts / billingProfiles / billingSubscriptions | Ne |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Ne |
> | billingAccounts / billingProfiles / customers | Ne |
> | billingAccounts / billingProfiles / invoices | Ne |
> | billingAccounts / billingProfiles / invoices / pricesheet | Ne |
> | billingAccounts / billingProfiles / invoiceSections | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleAssignments | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingRoleDefinitions | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingSubscriptions | Ne |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Ne |
> | billingAccounts / billingProfiles / invoiceSections / initiateTransfer | Ne |
> | billingAccounts / billingProfiles / invoiceSections / products | Ne |
> | billingAccounts / billingProfiles / invoiceSections / products / transfer | Ne |
> | billingAccounts / billingProfiles / invoiceSections / products / updateAutoRenew | Ne |
> | billingAccounts / billingProfiles / invoiceSections / transactions | Ne |
> | billingAccounts / billingProfiles / invoiceSections / transfers | Ne |
> | billingAccounts / BillingProfiles / patchOperations | Ne |
> | billingAccounts / billingProfiles / paymentMethods | Ne |
> | billingAccounts / billingProfiles / policies | Ne |
> | billingAccounts / billingProfiles / pricesheet | Ne |
> | billingAccounts / billingProfiles / pricesheetDownloadOperations | Ne |
> | billingAccounts / billingProfiles / products | Ne |
> | billingAccounts / billingProfiles / transactions | Ne |
> | billingAccounts / billingRoleAssignments | Ne |
> | billingAccounts / billingRoleDefinitions | Ne |
> | billingAccounts / billingSubscriptions | Ne |
> | billingAccounts / billingSubscriptions / invoices | Ne |
> | billingAccounts / createBillingRoleAssignment | Ne |
> | billingAccounts / createInvoiceSectionOperations | Ne |
> | billingAccounts / customers | Ne |
> | billingAccounts / customers / billingPermissions | Ne |
> | billingAccounts / customers / billingSubscriptions | Ne |
> | billingAccounts / customers / initiateTransfer | Ne |
> | billingAccounts / customers / policies | Ne |
> | billingAccounts / customers / products | Ne |
> | billingAccounts / customers / transactions | Ne |
> | billingAccounts / customers / transfers | Ne |
> | billingAccounts / departments | Ne |
> | billingAccounts / enrollmentAccounts | Ne |
> | billingAccounts / invoices | Ne |
> | billingAccounts / invoiceSections | Ne |
> | billingAccounts / invoiceSections / billingSubscriptionMoveOperations | Ne |
> | billingAccounts / invoiceSections / billingSubscriptions | Ne |
> | billingAccounts / invoiceSections / billingSubscriptions / transfer | Ne |
> | billingAccounts / invoiceSections / elevate | Ne |
> | billingAccounts / invoiceSections / initiateTransfer | Ne |
> | billingAccounts / invoiceSections / patchOperations | Ne |
> | billingAccounts / invoiceSections / productMoveOperations | Ne |
> | billingAccounts / invoiceSections / products | Ne |
> | billingAccounts / invoiceSections / products / transfer | Ne |
> | billingAccounts / invoiceSections / products / updateAutoRenew | Ne |
> | billingAccounts / invoiceSections / transactions | Ne |
> | billingAccounts / invoiceSections / transfers | Ne |
> | billingAccounts / lineOfCredit | Ne |
> | billingAccounts / patchOperations | Ne |
> | billingAccounts / paymentMethods | Ne |
> | billingAccounts / products | Ne |
> | billingAccounts / transactions | Ne |
> | billingPeriods | Ne |
> | billingPermissions | Ne |
> | billingProperty | Ne |
> | billingRoleAssignments | Ne |
> | billingRoleDefinitions | Ne |
> | createBillingRoleAssignment | Ne |
> | departments | Ne |
> | enrollmentAccounts | Ne |
> | invoices | Ne |
> | transfers | Ne |
> | transfers / acceptTransfer | Ne |
> | transfers / declineTransfer | Ne |
> | transfers / operationStatus | Ne |
> | transfers / validateTransfer | Ne |
> | validateAddress | Ne |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | mapApis | Ano |
> | updateCommunicationPreference | Ne |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | blockchainMembers | Ano |
> | cordaMembers | Ano |
> | diváků | Ano |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | blueprintAssignments | Ne |
> | blueprintAssignments / assignmentOperations | Ne |
> | blueprintAssignments / operations | Ne |
> | blueprints | Ne |
> | blueprints / artifacts | Ne |
> | blueprints / versions | Ne |
> | blueprints / versions / artifacts | Ne |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | botServices | Ano |
> | botServices / channels | Ne |
> | botServices / connections | Ne |
> | languages | Ne |
> | šablon | Ne |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Redis | Ano |
> | RedisConfigDefinition | Ne |

## <a name="microsoftcapacity"></a>Microsoft.Capacity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | appliedReservations | Ne |
> | calculateExchange | Ne |
> | calculatePrice | Ne |
> | calculatePurchasePrice | Ne |
> | catalogs | Ne |
> | commercialReservationOrders | Ne |
> | exchange | Ne |
> | placePurchaseOrder | Ne |
> | reservationOrders | Ne |
> | reservationOrders / calculateRefund | Ne |
> | reservationOrders / merge | Ne |
> | reservationOrders / reservations | Ne |
> | reservationOrders / reservations / revisions | Ne |
> | reservationOrders / return | Ne |
> | reservationOrders / split | Ne |
> | reservationOrders / swap | Ne |
> | reservations | Ne |
> | resources | Ne |
> | validateReservationOrder | Ne |

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedRuleSets | Ne |
> | CdnWebApplicationFirewallPolicies | Ano |
> | edgenodes | Ne |
> | profiles | Ano |
> | profiles / endpoints | Ano |
> | profiles / endpoints / customdomains | Ne |
> | profiles / endpoints / origins | Ne |
> | validateProbe | Ne |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | certificateOrders | Ano |
> | certificateOrders / certificates | Ne |
> | validateCertificateRegistrationInformation | Ne |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | capabilities | Ne |
> | domainNames | Ano |
> | domainNames / capabilities | Ne |
> | domainNames / internalLoadBalancers | Ne |
> | domainNames / serviceCertificates | Ne |
> | domainNames / slots | Ne |
> | domainNames / slots / roles | Ne |
> | domainNames / slots / roles / metricDefinitions | Ne |
> | domainNames / slots / roles / metrics | Ne |
> | moveSubscriptionResources | Ne |
> | operatingSystemFamilies | Ne |
> | operatingSystems | Ne |
> | quotas | Ne |
> | resourceTypes | Ne |
> | validateSubscriptionMoveAvailability | Ne |
> | virtualMachines | Ano |
> | virtualMachines / diagnosticSettings | Ne |
> | virtualMachines / metricDefinitions | Ne |
> | virtualMachines / metrics | Ne |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | classicInfrastructureResources | Ne |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | capabilities | Ne |
> | expressRouteCrossConnections | Ne |
> | expressRouteCrossConnections / peerings | Ne |
> | gatewaySupportedDevices | Ne |
> | networkSecurityGroups | Ano |
> | quotas | Ne |
> | reservedIps | Ano |
> | virtualNetworks | Ano |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Ne |
> | virtualNetworks / virtualNetworkPeerings | Ne |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | capabilities | Ne |
> | disks | Ne |
> | images | Ne |
> | osImages | Ne |
> | osPlatformImages | Ne |
> | publicImages | Ne |
> | quotas | Ne |
> | storageAccounts | Ano |
> | storageAccounts / blobServices | Ne |
> | storageAccounts / fileServices | Ne |
> | storageAccounts / metricDefinitions | Ne |
> | storageAccounts / metrics | Ne |
> | storageAccounts / queueServices | Ne |
> | storageAccounts / services | Ne |
> | storageAccounts / services / diagnosticSettings | Ne |
> | storageAccounts / services / metricDefinitions | Ne |
> | storageAccounts / services / metrics | Ne |
> | storageAccounts / tableServices | Ne |
> | storageAccounts / vmImages | Ne |
> | vmImages | Ne |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

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
> | disks | Ano |
> | galleries | Ano |
> | galleries / applications | Ne |
> | galleries / applications / versions | Ne |
> | galleries / images | Ne |
> | galleries / images / versions | Ne |
> | hostGroups | Ano |
> | hostGroups / hosts | Ano |
> | images | Ano |
> | proximityPlacementGroups | Ano |
> | restorePointCollections | Ano |
> | restorePointCollections / restorePoints | Ne |
> | sharedVMImages | Ano |
> | sharedVMImages / versions | Ne |
> | snímky | Ano |
> | virtualMachines | Ano |
> | virtualMachines / extensions | Ano |
> | virtualMachines / metricDefinitions | Ne |
> | virtualMachineScaleSets | Ano |
> | virtualMachineScaleSets / extensions | Ne |
> | virtualMachineScaleSets / networkInterfaces | Ne |
> | virtualMachineScaleSets / publicIPAddresses | Ne |
> | virtualMachineScaleSets / virtualMachines | Ne |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces | Ne |

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | AggregatedCost | Ne |
> | Zůstatky | Ne |
> | Rozpočty | Ne |
> | Poplatky | Ne |
> | CostTags | Ne |
> | credits | Ne |
> | stránka events | Ne |
> | Prognózy | Ne |
> | lots | Ne |
> | Marketplaces | Ne |
> | Pricesheets | Ne |
> | products | Ne |
> | ReservationDetails | Ne |
> | ReservationRecommendations | Ne |
> | ReservationSummaries | Ne |
> | ReservationTransactions | Ne |
> | Značky | Ne |
> | tenants | Ne |
> | Výrazy | Ne |
> | UsageDetails | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | containerGroups | Ano |
> | serviceAssociationLinks | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | registries | Ano |
> | registries / builds | Ne |
> | registries / builds / cancel | Ne |
> | registries / builds / getLogLink | Ne |
> | registries / buildTasks | Ano |
> | registries / buildTasks / steps | Ne |
> | registries / eventGridFilters | Ne |
> | registries / generateCredentials | Ne |
> | registries / getBuildSourceUploadUrl | Ne |
> | registries / GetCredentials | Ne |
> | registries / importImage | Ne |
> | registries / queueBuild | Ne |
> | registries / regenerateCredential | Ne |
> | registries / regenerateCredentials | Ne |
> | registries / replications | Ano |
> | registries / runs | Ne |
> | registries / runs / cancel | Ne |
> | registries / scheduleRun | Ne |
> | registries / scopeMaps | Ne |
> | registries / taskRuns | Ano |
> | registries / tasks | Ano |
> | registries / tokens | Ne |
> | registries / updatePolicies | Ne |
> | registries / webhooks | Ano |
> | registries / webhooks / getCallbackConfig | Ne |
> | registries / webhooks / ping | Ne |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | containerServices | Ano |
> | managedClusters | Ano |
> | openShiftManagedClusters | Ano |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Výstrahy | Ne |
> | BillingAccounts | Ne |
> | Rozpočty | Ne |
> | CloudConnectors | Ne |
> | Konektory | Ano |
> | Departments | Ne |
> | Dimenze | Ne |
> | EnrollmentAccounts | Ne |
> | Exporty | Ne |
> | ExternalBillingAccounts | Ne |
> | ExternalBillingAccounts / Alerts | Ne |
> | ExternalBillingAccounts / Dimensions | Ne |
> | ExternalBillingAccounts / Forecast | Ne |
> | ExternalBillingAccounts / Query | Ne |
> | ExternalSubscriptions | Ne |
> | ExternalSubscriptions / Alerts | Ne |
> | ExternalSubscriptions / Dimensions | Ne |
> | ExternalSubscriptions / Forecast | Ne |
> | ExternalSubscriptions / Query | Ne |
> | Forecast | Ne |
> | Dotaz | Ne |
> | register | Ne |
> | Reportconfigs | Ne |
> | Sestavy | Ne |
> | Nastavení | Ne |
> | showbackRules | Ne |
> | Zobrazení | Ne |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | requests | Ne |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | associations | Ne |
> | resourceProviders | Ano |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | jobs | Ano |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | DataBoxEdgeDevices | Ano |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | workspaces | Ano |
> | workspaces / virtualNetworkPeerings | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | catalogs | Ano |
> | datacatalogs | Ano |
> | datacatalogs / datasources | Ne |
> | datacatalogs / datasources / scans | Ne |
> | datacatalogs / datasources / scans / datasets | Ne |
> | datacatalogs / datasources / scans / triggers | Ne |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dataFactories | Ano |
> | dataFactories / diagnosticSettings | Ne |
> | dataFactories / metricDefinitions | Ne |
> | dataFactorySchema | Ne |
> | factories | Ano |
> | factories / integrationRuntimes | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | accounts / dataLakeStoreAccounts | Ne |
> | accounts / storageAccounts | Ne |
> | accounts / storageAccounts / containers | Ne |
> | accounts / transferAnalyticsUnits | Ne |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | accounts / eventGridFilters | Ne |
> | accounts / firewallRules | Ne |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Služby | Ano |
> | services / projects | Ano |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | accounts / shares | Ne |
> | accounts / shares / datasets | Ne |
> | accounts / shares / invitations | Ne |
> | accounts / shares / providersharesubscriptions | Ne |
> | accounts / shares / synchronizationSettings | Ne |
> | accounts / sharesubscriptions | Ne |
> | accounts / sharesubscriptions / consumerSourceDataSets | Ne |
> | accounts / sharesubscriptions / datasetmappings | Ne |
> | accounts / sharesubscriptions / triggers | Ne |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servery | Ano |
> | servers / advisors | Ne |
> | servers / privateEndpointConnectionProxies | Ne |
> | servers / privateEndpointConnections | Ne |
> | servers / privateLinkResources | Ne |
> | servers / queryTexts | Ne |
> | servers / recoverableServers | Ne |
> | servers / topQueryStatistics | Ne |
> | servers / virtualNetworkRules | Ne |
> | servers / waitStatistics | Ne |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servery | Ano |
> | servers / advisors | Ne |
> | servers / privateEndpointConnectionProxies | Ne |
> | servers / privateEndpointConnections | Ne |
> | servers / privateLinkResources | Ne |
> | servers / queryTexts | Ne |
> | servers / recoverableServers | Ne |
> | servers / topQueryStatistics | Ne |
> | servers / virtualNetworkRules | Ne |
> | servers / waitStatistics | Ne |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | serverGroups | Ano |
> | servery | Ano |
> | servers / advisors | Ne |
> | servers / keys | Ne |
> | servers / privateEndpointConnectionProxies | Ne |
> | servers / privateEndpointConnections | Ne |
> | servers / privateLinkResources | Ne |
> | servers / queryTexts | Ne |
> | servers / recoverableServers | Ne |
> | servers / topQueryStatistics | Ne |
> | servers / virtualNetworkRules | Ne |
> | servers / waitStatistics | Ne |
> | serversv2 | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | artifactSources | Ano |
> | rollouts | Ano |
> | serviceTopologies | Ano |
> | serviceTopologies / services | Ano |
> | serviceTopologies / services / serviceUnits | Ano |
> | kroky | Ano |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applicationgroups | Ano |
> | applicationgroups / applications | Ne |
> | applicationgroups / desktops | Ne |
> | applicationgroups / startmenuitems | Ne |
> | hostpools | Ano |
> | hostpools / sessionhosts | Ne |
> | hostpools / sessionhosts / usersessions | Ne |
> | hostpools / usersessions | Ne |
> | workspaces | Ano |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | ElasticPools | Ano |
> | ElasticPools / IotHubTenants | Ano |
> | IotHubs | Ano |
> | IotHubs / eventGridFilters | Ne |
> | ProvisioningServices | Ano |
> | usages | Ne |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | pipelines | Ano |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | controllers | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | labcenters | Ano |
> | labs | Ano |
> | labs / environments | Ano |
> | labs / serviceRunners | Ano |
> | labs / virtualMachines | Ano |
> | schedules | Ano |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | databaseAccountNames | Ne |
> | databaseAccounts | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | domains | Ano |
> | domains / domainOwnershipIdentifiers | Ne |
> | generateSsoRequest | Ne |
> | topLevelDomains | Ne |
> | validateDomainRegistrationInformation | Ne |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | lcsprojects | Ne |
> | lcsprojects / clouddeployments | Ne |
> | lcsprojects / connectors | Ne |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Služby | Ano |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | domains | Ano |
> | domains / topics | Ne |
> | eventSubscriptions | Ne |
> | extensionTopics | Ne |
> | topics | Ano |
> | topicTypes | Ne |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | clusters | Ano |
> | namespaces | Ano |
> | namespaces / authorizationrules | Ne |
> | namespaces / disasterrecoveryconfigs | Ne |
> | namespaces / eventhubs | Ne |
> | namespaces / eventhubs / authorizationrules | Ne |
> | namespaces / eventhubs / consumergroups | Ne |
> | namespaces / networkrulesets | Ne |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Database | Ne |
> | providers | Ne |

## <a name="microsoftgallery"></a>Microsoft.Gallery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | enroll | Ne |
> | galleryitems | Ne |
> | generateartifactaccessuri | Ne |
> | myareas | Ne |
> | myareas / areas | Ne |
> | myareas / areas / areas | Ne |
> | myareas / areas / areas / galleryitems | Ne |
> | myareas / areas / galleryitems | Ne |
> | myareas / galleryitems | Ne |
> | register | Ne |
> | resources | Ne |
> | retrieveresourcesbyid | Ne |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | configurationProfileAssignments | Ne |
> | guestConfigurationAssignments | Ne |
> | software | Ne |
> | softwareUpdateProfile | Ne |
> | softwareUpdates | Ne |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hanaInstances | Ano |
> | sapMonitors | Ano |

## <a name="microsofthardwaresecuritymodules"></a>Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dedicatedHSMs | Ano |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | clusters | Ano |
> | clusters / applications | Ne |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Služby | Ano |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | machines | Ano |
> | machines / extensions | Ano |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dataManagers | Ano |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | components | Ano |
> | networkScopes | Ano |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | jobs | Ano |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | diagnosticSettings | Ne |
> | diagnosticSettingsCategories | Ne |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | appTemplates | Ne |
> | IoTApps | Ano |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Graf | Ano |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | deletedVaults | Ne |
> | hsmPools | Ano |
> | vaults | Ano |
> | vaults / accessPolicies | Ne |
> | vaults / eventGridFilters | Ne |
> | vaults / secrets | Ne |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | clusters | Ano |
> | clusters / attacheddatabaseconfigurations | Ne |
> | clusters / databases | Ne |
> | clusters / databases / dataconnections | Ne |
> | clusters / databases / eventhubconnections | Ne |
> | clusters / sharedidentities | Ne |

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | labaccounts | Ano |
> | uživatelů | Ne |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hostingEnvironments | Ano |
> | integrationAccounts | Ano |
> | integrationServiceEnvironments | Ano |
> | integrationServiceEnvironments / managedApis | Ano |
> | isolatedEnvironments | Ano |
> | workflows | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

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
> | workspaces | Ano |
> | workspaces / computes | Ne |
> | workspaces / eventGridFilters | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Identities | Ne |
> | userAssignedIdentities | Ano |

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Ne |
> | registrationAssignments | Ne |
> | registrationDefinitions | Ne |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | getEntities | Ne |
> | managementGroups | Ne |
> | resources | Ne |
> | startTenantBackfill | Ne |
> | tenantBackfillStatus | Ne |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | accounts / eventGridFilters | Ne |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | offers | Ne |
> | offerTypes | Ne |
> | offerTypes / publishers | Ne |
> | offerTypes / publishers / offers | Ne |
> | offerTypes / publishers / offers / plans | Ne |
> | offerTypes / publishers / offers / plans / agreements | Ne |
> | offerTypes / publishers / offers / plans / configs | Ne |
> | offerTypes / publishers / offers / plans / configs / importImage | Ne |
> | privategalleryitems | Ne |
> | products | Ne |
> | publishers | Ne |
> | publishers / offers | Ne |
> | publishers / offers / amendments | Ne |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | classicDevServices | Ano |
> | updateCommunicationPreference | Ne |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | agreements | Ne |
> | offertypes | Ne |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | mediaservices | Ano |
> | mediaservices / accountFilters | Ne |
> | mediaservices / assets | Ne |
> | mediaservices / assets / assetFilters | Ne |
> | mediaservices / contentKeyPolicies | Ne |
> | mediaservices / eventGridFilters | Ne |
> | mediaservices / liveEventOperations | Ne |
> | mediaservices / liveEvents | Ano |
> | mediaservices / liveEvents / liveOutputs | Ne |
> | mediaservices / liveOutputOperations | Ne |
> | mediaservices / mediaGraphs | Ne |
> | mediaservices / streamingEndpointOperations | Ne |
> | mediaservices / streamingEndpoints | Ano |
> | mediaservices / streamingLocators | Ne |
> | mediaservices / streamingPolicies | Ne |
> | mediaservices / transforms | Ne |
> | mediaservices / transforms / jobs | Ne |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | appClusters | Ano |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | assessmentProjects | Ano |
> | migrateprojects | Ano |
> | projects | Ano |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Ano |
> | objectUnderstandingAccounts | Ano |
> | remoteRenderingAccounts | Ano |
> | spatialAnchorsAccounts | Ano |
> | surfaceReconstructionAccounts | Ano |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | netAppAccounts | Ano |
> | netAppAccounts / capacityPools | Ano |
> | netAppAccounts / capacityPools / volumes | Ano |
> | netAppAccounts / capacityPools / volumes / mountTargets | Ano |
> | netAppAccounts / capacityPools / volumes / snapshots | Ano |
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
> | connections | Ano |
> | ddosCustomPolicies | Ano |
> | ddosProtectionPlans | Ano |
> | dnsOperationStatuses | Ne |
> | dnszones | Ano |
> | dnszones / A | Ne |
> | dnszones / AAAA | Ne |
> | dnszones / all | Ne |
> | dnszones / CAA | Ne |
> | dnszones / CNAME | Ne |
> | dnszones / MX | Ne |
> | dnszones / NS | Ne |
> | dnszones / PTR | Ne |
> | dnszones / recordsets | Ne |
> | dnszones / SOA | Ne |
> | dnszones / SRV | Ne |
> | dnszones / TXT | Ne |
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
> | networkWatchers / connectionMonitors | Ano |
> | networkWatchers / lenses | Ano |
> | networkWatchers / pingMeshes | Ano |
> | p2sVpnGateways | Ano |
> | privateDnsOperationStatuses | Ne |
> | privateDnsZones | Ano |
> | privateDnsZones / A | Ne |
> | privateDnsZones / AAAA | Ne |
> | privateDnsZones / all | Ne |
> | privateDnsZones / CNAME | Ne |
> | privateDnsZones / MX | Ne |
> | privateDnsZones / PTR | Ne |
> | privateDnsZones / SOA | Ne |
> | privateDnsZones / SRV | Ne |
> | privateDnsZones / TXT | Ne |
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
> | trafficmanagerprofiles / heatMaps | Ne |
> | trafficManagerUserMetricsKeys | Ne |
> | virtualHubs | Ano |
> | virtualNetworkGateways | Ano |
> | virtualNetworks | Ano |
> | virtualNetworkTaps | Ano |
> | virtualWans | Ano |
> | vpnGateways | Ano |
> | vpnSites | Ano |
> | webApplicationFirewallPolicies | Ano |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | namespaces | Ano |
> | namespaces / notificationHubs | Ano |

## <a name="microsoftobjectstore"></a>Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | osNamespaces | Ano |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | HyperVSites | Ano |
> | ImportSites | Ano |
> | ServerSites | Ano |
> | VMwareSites | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | clusters | Ano |
> | zařízení | Ne |
> | linkTargets | Ne |
> | storageInsightConfigs | Ne |
> | workspaces | Ano |
> | workspaces / dataSources | Ne |
> | workspaces / linkedServices | Ne |
> | workspaces / query | Ne |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | managementassociations | Ne |
> | managementconfigurations | Ano |
> | Řešení | Ano |
> | views | Ano |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | legacyPeerings | Ne |
> | peerAsns | Ne |
> | peerings | Ano |
> | peeringServiceProviders | Ne |
> | peeringServices | Ano |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | policyEvents | Ne |
> | policyMetadata | Ne |
> | policyStates | Ne |
> | policyTrackedResources | Ne |
> | remediations | Ne |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | consoles | Ne |
> | dashboards | Ano |
> | userSettings | Ne |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | workspaceCollections | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | capacities | Ano |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | backupProtectedItems | Ne |
> | vaults | Ano |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | namespaces | Ano |
> | namespaces / authorizationrules | Ne |
> | namespaces / hybridconnections | Ne |
> | namespaces / hybridconnections / authorizationrules | Ne |
> | namespaces / wcfrelays | Ne |
> | namespaces / wcfrelays / authorizationrules | Ne |

## <a name="microsoftremoteapp"></a>Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ne |
> | collections | Ano |
> | collections / applications | Ne |
> | collections / securityprincipals | Ne |
> | templateImages | Ne |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dotazy | Ano |
> | resourceChangeDetails | Ne |
> | resourceChanges | Ne |
> | resources | Ne |
> | resourcesHistory | Ne |
> | subscriptionsStatus | Ne |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | availabilityStatuses | Ne |
> | childAvailabilityStatuses | Ne |
> | childResources | Ne |
> | stránka events | Ne |
> | impactedResources | Ne |
> | zprostředkovatele identity | Ne |
> | notifications | Ne |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | deployments | Ne |
> | deployments / operations | Ne |
> | deploymentScripts | Ano |
> | deploymentScripts / logs | Ne |
> | links | Ne |
> | notifyResourceJobs | Ne |
> | providers | Ne |
> | resourceGroups | Ne |
> | resources | Ne |
> | subscriptions | Ne |
> | subscriptions / providers | Ne |
> | subscriptions / resources | Ne |
> | subscriptions / tagnames | Ne |
> | subscriptions / tagNames / tagValues | Ne |
> | tenants | Ne |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikací | Ano |
> | saasresources | Ne |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | jobcollections | Ano |

## <a name="microsoftsearch"></a>Microsoft.Search

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
> | allowedConnections | Ne |
> | applicationWhitelistings | Ne |
> | assessmentMetadata | Ne |
> | assessments | Ne |
> | autoDismissAlertsRules | Ne |
> | automations | Ano |
> | AutoProvisioningSettings | Ne |
> | Compliances | Ne |
> | dataCollectionAgents | Ne |
> | deviceSecurityGroups | Ne |
> | discoveredSecuritySolutions | Ne |
> | externalSecuritySolutions | Ne |
> | InformationProtectionPolicies | Ne |
> | iotSecuritySolutions | Ano |
> | iotSecuritySolutions / analyticsModels | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Ne |
> | jitNetworkAccessPolicies | Ne |
> | networkData | Ne |
> | policies | Ne |
> | pricings | Ne |
> | regulatoryComplianceStandards | Ne |
> | regulatoryComplianceStandards / regulatoryComplianceControls | Ne |
> | regulatoryComplianceStandards / regulatoryComplianceControls / regulatoryComplianceAssessments | Ne |
> | securityContacts | Ne |
> | securitySolutions | Ne |
> | securitySolutionsReferenceData | Ne |
> | securityStatuses | Ne |
> | securityStatusesSummaries | Ne |
> | serverVulnerabilityAssessments | Ne |
> | settings | Ne |
> | subAssessments | Ne |
> | úlohy | Ne |
> | topologies | Ne |
> | workspaceSettings | Ne |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | diagnosticSettings | Ne |
> | diagnosticSettingsCategories | Ne |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aggregations | Ne |
> | alertRules | Ne |
> | alertRuleTemplates | Ne |
> | bookmarks | Ne |
> | cases | Ne |
> | dataConnectors | Ne |
> | entities | Ne |
> | entityQueries | Ne |
> | officeConsents | Ne |
> | settings | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | namespaces | Ano |
> | namespaces / authorizationrules | Ne |
> | namespaces / disasterrecoveryconfigs | Ne |
> | namespaces / eventgridfilters | Ne |
> | namespaces / networkrulesets | Ne |
> | namespaces / queues | Ne |
> | namespaces / queues / authorizationrules | Ne |
> | namespaces / topics | Ne |
> | namespaces / topics / authorizationrules | Ne |
> | namespaces / topics / subscriptions | Ne |
> | namespaces / topics / subscriptions / rules | Ne |
> | premiumMessagingRegions | Ne |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikací | Ano |
> | clusters | Ano |
> | clusters / applications | Ne |
> | containerGroups | Ano |
> | containerGroupSets | Ano |
> | edgeclusters | Ano |
> | edgeclusters / applications | Ne |
> | networks | Ano |
> | secretstores | Ano |
> | secretstores / certificates | Ne |
> | secretstores / secrets | Ne |
> | volumes | Ano |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikací | Ano |
> | containerGroups | Ano |
> | gateways | Ano |
> | networks | Ano |
> | secrets | Ano |
> | volumes | Ano |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | providerRegistrations | Ne |
> | providerRegistrations / resourceTypeRegistrations | Ne |
> | rollouts | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SignalR | Ano |
> | SignalR / eventGridFilters | Ne |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SiteRecoveryVault | Ano |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hybridUseBenefits | Ne |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applicationDefinitions | Ano |
> | aplikací | Ano |
> | jitRequests | Ano |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | managedInstances | Ano |
> | managedInstances / databases | Ano |
> | managedInstances / databases / backupShortTermRetentionPolicies | Ne |
> | managedInstances / databases / schemas / tables / columns / sensitivityLabels | Ne |
> | managedInstances / databases / vulnerabilityAssessments | Ne |
> | managedInstances / databases / vulnerabilityAssessments / rules / baselines | Ne |
> | managedInstances / encryptionProtector | Ne |
> | managedInstances / keys | Ne |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Ne |
> | managedInstances / vulnerabilityAssessments | Ne |
> | servery | Ano |
> | servers / administrators | Ne |
> | servers / communicationLinks | Ne |
> | servers / databases | Ano |
> | servers / encryptionProtector | Ne |
> | servers / firewallRules | Ne |
> | servers / keys | Ne |
> | servers / restorableDroppedDatabases | Ne |
> | servers / serviceobjectives | Ne |
> | servers / tdeCertificates | Ne |
> | virtualClusters | Ne |

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

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
> | storageAccounts | Ano |
> | storageAccounts / blobServices | Ne |
> | storageAccounts / fileServices | Ne |
> | storageAccounts / queueServices | Ne |
> | storageAccounts / services | Ne |
> | storageAccounts / services / metricDefinitions | Ne |
> | storageAccounts / tableServices | Ne |
> | usages | Ne |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | caches | Ano |
> | caches / storageTargets | Ne |
> | usageModels | Ne |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | replicationGroups | Ne |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices / registeredServers | Ne |
> | storageSyncServices / syncGroups | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne |
> | storageSyncServices / workflows | Ne |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices / registeredServers | Ne |
> | storageSyncServices / syncGroups | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne |
> | storageSyncServices / workflows | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices / registeredServers | Ne |
> | storageSyncServices / syncGroups | Ne |
> | storageSyncServices / syncGroups / cloudEndpoints | Ne |
> | storageSyncServices / syncGroups / serverEndpoints | Ne |
> | storageSyncServices / workflows | Ne |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | managers | Ano |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | streamingjobs | Ano |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | cancel | Ne |
> | CreateSubscription | Ne |
> | enable | Ne |
> | rename | Ne |
> | SubscriptionDefinitions | Ne |
> | SubscriptionOperations | Ne |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | environments | Ano |
> | environments / accessPolicies | Ne |
> | environments / eventsources | Ano |
> | environments / referenceDataSets | Ano |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ano |
> | dedicatedCloudServices | Ano |
> | virtualMachines | Ano |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | apiManagementAccounts | Ne |
> | apiManagementAccounts / apiAcls | Ne |
> | apiManagementAccounts / apis | Ne |
> | apiManagementAccounts / apis / apiAcls | Ne |
> | apiManagementAccounts / apis / connectionAcls | Ne |
> | apiManagementAccounts / apis / connections | Ne |
> | apiManagementAccounts / apis / connections / connectionAcls | Ne |
> | apiManagementAccounts / apis / localizedDefinitions | Ne |
> | apiManagementAccounts / connectionAcls | Ne |
> | apiManagementAccounts / connections | Ne |
> | billingMeters | Ne |
> | certificates | Ano |
> | connectionGateways | Ano |
> | connections | Ano |
> | customApis | Ano |
> | deletedSites | Ne |
> | functions | Ne |
> | hostingEnvironments | Ano |
> | hostingEnvironments / multiRolePools | Ne |
> | hostingEnvironments / workerPools | Ne |
> | publishingUsers | Ne |
> | recommendations | Ne |
> | resourceHealthMetadata | Ne |
> | runtimes | Ne |
> | serverFarms | Ano |
> | serverFarms / eventGridFilters | Ne |
> | sites | Ano |
> | sites/config  | Ne |
> | sites / eventGridFilters | Ne |
> | sites / hostNameBindings | Ne |
> | sites / networkConfig | Ne |
> | sites / premieraddons | Ano |
> | sites / slots | Ano |
> | sites / slots / eventGridFilters | Ne |
> | sites / slots / hostNameBindings | Ne |
> | sites / slots / networkConfig | Ne |
> | sourceControls | Ne |
> | validate | Ne |
> | verifyHostingEnvironmentVnet | Ne |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | diagnosticSettings | Ne |
> | diagnosticSettingsCategories | Ne |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | DeviceServices | Ano |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | components | Ne |
> | componentsSummary | Ne |
> | monitorInstances | Ne |
> | monitorInstancesSummary | Ne |
> | monitors | Ne |
> | notificationSettings | Ne |

## <a name="next-steps"></a>Další kroky

To get the same data as a file of comma-separated values, download [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

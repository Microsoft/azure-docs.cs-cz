---
title: Úplné odstranění režimu
description: Ukazuje, jak typy prostředků zpracovat odstranění úplného režimu v šablonách Azure Resource Manager.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e0250b289ce7a228d844023c3e1d1110438b3afc
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802565"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Odstranění prostředků Azure pro nasazení v kompletním režimu

Tento článek popisuje, jak typy prostředků zpracovat odstranění, když není v šabloně, která je nasazena v režimu dokončení.

Typy prostředků označené **ano** jsou odstraněny, pokud typ není v šabloně nasazené v režimu dokončení.

Typy prostředků označené **ne** nejsou automaticky odstraněny, pokud nejsou v šabloně; pokud je však odstraněn nadřazený prostředek, budou odstraněny. Úplný popis chování najdete v tématu [režimy nasazení Správce prostředků Azure](deployment-modes.md).

Pokud nasadíte do [více než jedné skupiny prostředků v šabloně](cross-resource-group-deployment.md), prostředky ve skupině prostředků zadané v operaci nasazení jsou způsobilé k odstranění. Prostředky v sekundárních skupinách prostředků se neodstraní.

Přejít na obor názvů poskytovatele prostředků:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [Microsoft.Addons](#microsoftaddons)
> - [Microsoft.AdhybridHealthService](#microsoftadhybridhealthservice)
> - [Microsoft.Advisor](#microsoftadvisor)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppKonfigurace](#microsoftappconfiguration)
> - [Platforma Microsoft.AppPlatforma](#microsoftappplatform)
> - [Microsoft.Atestation](#microsoftattestation)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.Azconfig](#microsoftazconfig)
> - [Microsoft.Azure.Ženeva](#microsoftazuregeneva)
> - [Adresář Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.Fakturace](#microsoftbilling)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.BlockchainTokeny](#microsoftblockchaintokens)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Kapacita](#microsoftcapacity)
> - [Soubor Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicInfrastructureMigrate](#microsoftclassicinfrastructuremigrate)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Commerce](#microsoftcommerce)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Spotřeba](#microsoftconsumption)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
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
> - [Microsoft.Falcon](#microsoftfalcon)
> - [Microsoft.Features](#microsoftfeatures)
> - [Microsoft.Galerie](#microsoftgallery)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.GuestConfiguration](#microsoftguestconfiguration)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Moduly Microsoft.HardwareSecurityModules](#microsofthardwaresecuritymodules)
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
> - [Microsoft.Kubernetes](#microsoftkubernetes)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Služby Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.Údržba](#microsoftmaintenance)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Služby Microsoft.ManagedServices](#microsoftmanagedservices)
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
> - [Microsoft.Poznámkové bloky](#microsoftnotebooks)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Úložiště Microsoft.ObjectStore](#microsoftobjectstore)
> - [Microsoft.OffAzure](#microsoftoffazure)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.PolicyInsights](#microsoftpolicyinsights)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectBabylon](#microsoftprojectbabylon)
> - [Microsoft.Quantum](#microsoftquantum)
> - [Služby Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Aplikace Microsoft.RemoteApp](#microsoftremoteapp)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.ResourceHealth](#microsoftresourcehealth)
> - [Microsoft.Resources](#microsoftresources)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Hledat](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.SecurityGraph](#microsoftsecuritygraph)
> - [Microsoft.SecurityInsights](#microsoftsecurityinsights)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.Services](#microsoftservices)
> - [Služba Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.SiteRecovery](#microsoftsiterecovery)
> - [Microsoft.SoftwarePlan](#microsoftsoftwareplan)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Služba Microsoft.SpoolService](#microsoftspoolservice)
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
> - [Microsoft.VMwareCloudJednoduché](#microsoftvmwarecloudsimple)
> - [Microsoft.VnfManager](#microsoftvnfmanager)
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

## <a name="microsoftadhybridhealthservice"></a>Microsoft.AdhybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aadsupportcases | Ne |
> | přidá služby | Ne |
> | Agenti | Ne |
> | anonymní apiusers | Ne |
> | konfigurace | Ne |
> | Protokoly | Ne |
> | reports | Ne |
> | servicehealthmetrics | Ne |
> | services | Ne |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Konfigurace | Ne |
> | generateRecommendations | Ne |
> | zprostředkovatele identity | Ne |
> | Doporučení | Ne |
> | potlačení | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | actionRules | Ano |
> | výstrahy | Ne |
> | seznam upozornění | Ne |
> | upozorněníMetaData | Ne |
> | alertsSummary | Ne |
> | seznam výstrahSouhrn | Ne |
> | smartDetectorAlertRules | Ano |
> | smartGroups | Ne |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Servery | Ano |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | reportFeedback | Ne |
> | služba | Ano |
> | validateServiceName | Ne |

## <a name="microsoftappconfiguration"></a>Microsoft.AppKonfigurace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | configurationStores | Ano |
> | configurationStores / eventGridFilters | Ne |

## <a name="microsoftappplatform"></a>Platforma Microsoft.AppPlatforma

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Spring | Ano |

## <a name="microsoftattestation"></a>Microsoft.Atestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | atestacePoskytovatelé | Ne |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | classicAdministrators | Ne |
> | dataAliases | Ne |
> | odepřítÚkoly | Ne |
> | elevateAccess | Ne |
> | findOrphanRoleAssignments | Ne |
> | Zámky | Ne |
> | Oprávnění | Ne |
> | policyAssignments | Ne |
> | policyDefinitions | Ne |
> | policySetDefinitions | Ne |
> | providerOperations | Ne |
> | roleÚkoly | Ne |
> | roleAssignmentsUsageMetrics | Ne |
> | definice rolí | Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | automationAccounts | Ano |
> | automationAccounts / konfigurace | Ano |
> | automationAccounts / úlohy | Ne |
> | automationAccounts / privateEndpointConnectionProxies | Ne |
> | automationAccounts / privateEndpointConnections | Ne |
> | automationAccounts / privateLinkResources | Ne |
> | automationAccounts / runbooks | Ano |
> | automationAccounts / softwareUpdateKonfigurace | Ne |
> | automationAccounts / webhooky | Ne |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | configurationStores | Ano |
> | configurationStores / eventGridFilters | Ne |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Ženeva

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Prostředí | Ne |
> | prostředí / účty | Ne |
> | prostředí / účty / obory názvů | Ne |
> | prostředí / účty / jmenné prostory / konfigurace | Ne |

## <a name="microsoftazureactivedirectory"></a>Adresář Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | b2cAdresáře | Ano |
> | b2ctenants | Ne |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hybridDataManagers | Ano |
> | postgresInstances | Ano |
> | instance sql Instances | Ano |
> | sqlServerRegistrations | Ano |
> | sqlServerRegistrations / sqlServers | Ne |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | cloudManifestFiles | Ne |
> | Registrace | Ano |
> | registrace / zákazníkPředplatné | Ne |
> | registrace / produkty | Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dávkové účty | Ano |

## <a name="microsoftbilling"></a>Microsoft.Fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | fakturační účty | Ne |
> | billingAccounts / smlouvy | Ne |
> | billingAccounts / billingPermissions | Ne |
> | billingAccounts / billingProfiles | Ne |
> | billingAccounts / billingProfiles / billingPermissions | Ne |
> | billingAccounts / billingProfiles / billingRoleAssignments | Ne |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Ne |
> | billingAccounts / billingProfiles / billingSubscriptions billing | Ne |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Ne |
> | billingAccounts / billingProfiles / zákazníci | Ne |
> | billingAccounts / billingProfiles / pokyny | Ne |
> | billingAccounts / billingProfiles / faktury | Ne |
> | billingAccounts / billingProfiles / faktury / ceník | Ne |
> | billingAccounts / billingProfiles / faktury / transakce | Ne |
> | billingAccounts / billingProfiles / invoiceSections | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Ne |
> | billingAccounts / billingProfiles / fakturSections / billingRoleAssignments billingAccounts / billingAccounts / billing | Ne |
> | billingAccounts / billingProfiles / fakturSections / billingRoleDefinitions | Ne |
> | billingAccounts / billingProfiles / fakturSections / billingSubscriptions billingAccounts / billingAccounts / billing | Ne |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Ne |
> | billingAccounts / billingProfiles / fakturySekce / initiateTransfer | Ne |
> | billingAccounts / billingProfiles / fakturYSekce / produkty | Ne |
> | billingAccounts / billingProfiles / fakturYSekce / produkty / převod | Ne |
> | billingAccounts / billingProfiles / fakturYSekce / produkty / updateAutoRenew | Ne |
> | billingAccounts / billingProfiles / fakturYSekce / transakce | Ne |
> | billingAccounts / billingProfiles / fakturySekce / převody | Ne |
> | billingAccounts / BillingProfiles / patchOperations | Ne |
> | billingAccounts / billingProfiles / paymentMethods | Ne |
> | billingAccounts / billingProfiles / zásady | Ne |
> | billingAccounts / billingProfiles / ceník | Ne |
> | billingAccounts / billingProfiles / ceníkDownloadOperations | Ne |
> | billingAccounts / billingProfiles / produkty | Ne |
> | billingAccounts / billingProfiles / transakce | Ne |
> | billingAccounts / billingRoleAssignments | Ne |
> | billingAccounts / billingRoleDefinitions | Ne |
> | billingAccounts / billingSubscriptions | Ne |
> | billingAccounts / billingSubscriptions / faktury | Ne |
> | billingAccounts / createBillingRoleAssignment | Ne |
> | billingAccounts / createInvoiceSectionOperations | Ne |
> | billingAccounts / zákazníci | Ne |
> | billingAccounts / customers / billingPermissions billingAccounts / billingPermissions billingAccounts / customers / billingPermissions billingAccounts / customers | Ne |
> | billingAccounts / customers / billingSubscriptions billing | Ne |
> | billingAccounts / customers / initiateTransfer billingAccounts / customers / initiateTransfer billingAccounts / customers / initiateTransfer billingAccounts | Ne |
> | billingAccounts / zákazníci / zásady | Ne |
> | billingAccounts / zákazníci / produkty | Ne |
> | billingAccounts / odběratelé / transakce | Ne |
> | billingAccounts / zákazníci / převody | Ne |
> | billingAccounts / oddělení | Ne |
> | billingAccounts / enrollmentAccounts | Ne |
> | billingAccounts / faktury | Ne |
> | billingAccounts / fakturySekce | Ne |
> | billingAccounts / fakturySekce / billingSubscriptionMoveOperations | Ne |
> | billingAccounts / fakturySekce / fakturaceOdběry | Ne |
> | billingAccounts / fakturySekce / fakturacePředplatné / převod | Ne |
> | billingAccounts / fakturySekce / zvýšení | Ne |
> | billingAccounts / fakturySekce / initiateTransfer | Ne |
> | billingAccounts / fakturySekce / patchOperations | Ne |
> | billingAccounts / fakturySekce / productMoveOperations | Ne |
> | billingAccounts / fakturySekce / produkty | Ne |
> | billingAccounts / fakturySekce / produkty / převod | Ne |
> | billingAccounts / fakturySekce / produkty / updateAutoRenew | Ne |
> | billingAccounts / fakturySekce / transakce | Ne |
> | billingAccounts / fakturySekce / převody | Ne |
> | billingAccounts / lineOfCredit | Ne |
> | billingAccounts / patchOperations | Ne |
> | billingAccounts / paymentMethods | Ne |
> | billingAccounts / produkty | Ne |
> | fakturaceÚčty / transakce | Ne |
> | fakturační období | Ne |
> | oprávnění fakturace | Ne |
> | billingProperty | Ne |
> | billingRoleAssignments | Ne |
> | billingRoleDefinitions | Ne |
> | createBillingRoleAssignment | Ne |
> | Oddělení | Ne |
> | enrollmentAccounts | Ne |
> | Faktury | Ne |
> | Převody | Ne |
> | převody / acceptTransfer | Ne |
> | převody / declineTransfer | Ne |
> | převody / provozStav | Ne |
> | převody / validateTransfer | Ne |
> | ověřitadresu | Ne |

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
> | blockchainČlenové | Ano |
> | cordaČlenové | Ano |
> | Pozorovatelů | Ano |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokeny

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Služby TokenServices | Ano |
> | TokenServices / BlockchainNetworks | Ne |
> | TokenServices / Skupiny | Ne |
> | TokenServices / Skupiny / Účty | Ne |
> | TokenServices / TokenTemplates | Ne |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | podrobné úkoly | Ne |
> | podrobné plányÚkoly / assignmentOperations | Ne |
> | podrobné plánYÚkoly / operace | Ne |
> | Plány | Ne |
> | plány / artefakty | Ne |
> | plány / verze | Ne |
> | plány / verze / artefakty | Ne |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | botServices | Ano |
> | botServices / kanály | Ne |
> | botServices / připojení | Ne |
> | jazyky | Ne |
> | šablony | Ne |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Redis | Ano |

## <a name="microsoftcapacity"></a>Microsoft.Kapacita

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | appliedReservations | Ne |
> | automatické zvýšení kvóty | Ne |
> | calculateExchange | Ne |
> | vypočítatCena | Ne |
> | calculatePurchasePrice | Ne |
> | Katalogy | Ne |
> | commercialReservationOrders | Ne |
> | Exchange | Ne |
> | placePurchaseOrder | Ne |
> | rezervaceObjednávky | Ne |
> | rezervaceObjednávky / calculateRefund | Ne |
> | rezervaceObjednávky / sloučení | Ne |
> | rezervaceObjednávky / rezervace | Ne |
> | rezervaceObjednávky / rezervace / revize | Ne |
> | rezervaceObjednávky / návrat | Ne |
> | rezervaceObjednávky / rozdělení | Ne |
> | rezervaceObjednávky / swap | Ne |
> | Rezervace | Ne |
> | resourceProviders | Ne |
> | resources | Ne |
> | validateReservationOrder | Ne |

## <a name="microsoftcdn"></a>Soubor Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | CdnWebApplicationFirewallManagedSady pravidel | Ne |
> | CdnWebApplicationFirewallPolicies | Ano |
> | okrajové uzly | Ne |
> | Profily | Ano |
> | profily / koncové body | Ano |
> | profily / koncové body / vlastní domény | Ne |
> | profily / koncové body / origingroups | Ne |
> | profily / koncové body / počátky | Ne |
> | validateProbe | Ne |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | certificateObjednávky | Ano |
> | certificateObjednávky / certifikáty | Ne |
> | validateCertificateRegistrationInformation | Ne |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Schopnosti | Ne |
> | domainNames | Ano |
> | domainNames / capabilities domainNames / capabilities domainNames / capabilities domainNames | Ne |
> | domainNames / internalLoadBalancers | Ne |
> | domainNames / serviceCertificates | Ne |
> | domainNames / sloty | Ne |
> | domainNames / sloty / role | Ne |
> | domainNames / sloty / role / metricDefinitions | Ne |
> | domainNames / sloty / role / metriky | Ne |
> | moveSubscriptionResources | Ne |
> | operatingSystemFamilies | Ne |
> | provoznísystémy | Ne |
> | quotas | Ne |
> | resourceTypes | Ne |
> | validateSubscriptionMoveAvailability | Ne |
> | virtualMachines | Ano |
> | virtualMachines / diagnosticSettings | Ne |
> | virtualMachines / metricDefinitions virtualMachines / metricDefinitions virtualMachines / metricDefinitions virtualMachines | Ne |
> | virtualMachines / metriky | Ne |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | classicInfrastructureZdroje | Ne |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Schopnosti | Ne |
> | expressRouteCrossConnections | Ne |
> | expressRouteCrossConnections / peerings expressRouteCrossConnections / peerings expressRouteCrossConnections / peerings expressRoute | Ne |
> | gatewaySupportedDevices | Ne |
> | síťSecuritygroups | Ano |
> | quotas | Ne |
> | rezervované Ips | Ano |
> | virtualNetworks | Ano |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Ne |
> | virtualNetworks / virtualNetworkPeerings | Ne |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Schopnosti | Ne |
> | Disky | Ne |
> | images | Ne |
> | osImages | Ne |
> | osPlatformImages | Ne |
> | publicImages | Ne |
> | quotas | Ne |
> | storageAccounts | Ano |
> | storageAccounts / blobServices | Ne |
> | storageAccounts / fileServices | Ne |
> | storageAccounts / metricDefinitions | Ne |
> | storageAccounts / metriky | Ne |
> | storageAccounts / queueServices | Ne |
> | úložištěÚčty / služby | Ne |
> | úložištěÚčty / služby / diagnostikaNastavení | Ne |
> | storageAccounts / služby / metricDefinitions | Ne |
> | úložištěÚčty / služby / metriky | Ne |
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
> | Agregace použití | Ne |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dostupnostSady | Ano |
> | diskové šifrovací sady | Ano |
> | Disky | Ano |
> | Galerie | Ano |
> | galerie / aplikace | Ne |
> | galerie / aplikace / verze | Ne |
> | galerie / obrázky | Ne |
> | galerie / obrázky / verze | Ne |
> | skupiny hostitelů | Ano |
> | hostGroups / hosts | Ano |
> | images | Ano |
> | proximityPlacementGroups | Ano |
> | obnoveníKolekce Point | Ano |
> | restorePointCollections / restorePoints | Ne |
> | sharedVMExtensions | Ano |
> | sharedVMExtensions / verze | Ne |
> | sdílenéObrazy VM | Ano |
> | sharedVMImages / verze | Ne |
> | snímky | Ano |
> | sshPublicKeys | Ano |
> | virtualMachines | Ano |
> | virtualMachines / rozšíření | Ano |
> | virtualMachines / metricDefinitions virtualMachines / metricDefinitions virtualMachines / metricDefinitions virtualMachines | Ne |
> | virtualMachineScaleSets | Ano |
> | virtualMachineScaleSets / rozšíření | Ne |
> | virtualMachineScaleSets / networkInterfaces | Ne |
> | virtualMachineScaleSets / publicIPAdresy | Ne |
> | virtualMachineScaleSets / virtualMachines | Ne |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces virtualMachineScaleSets / virtualMachines / networkInterfaces virtualMachineScaleSets / virtualMachines / networkInterfaces virtualMachine | Ne |

## <a name="microsoftconsumption"></a>Microsoft.Spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Agregované náklady | Ne |
> | Zůstatky | Ne |
> | Rozpočty | Ne |
> | Poplatky | Ne |
> | Značky nákladů | Ne |
> | Kredity | Ne |
> | stránka events | Ne |
> | Prognózy | Ne |
> | Hodně | Ne |
> | Tržiště | Ne |
> | Ceníky | Ne |
> | Produkty | Ne |
> | RezervacePodrobnosti | Ne |
> | RezervaceDoporučení | Ne |
> | RezervaceSouhrny | Ne |
> | ReservationTransactions | Ne |
> | Značky | Ne |
> | Nájemníky | Ne |
> | Výrazy | Ne |
> | Podrobnosti o použití | Ne |

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
> | Rejstříků | Ano |
> | registry / agentPooly | Ano |
> | registry / staví | Ne |
> | registry / staví / zrušit | Ne |
> | registry / staví / getLogLink | Ne |
> | registrů / buildTasks | Ano |
> | registry / buildTasks / kroky | Ne |
> | registry / eventGridFilters | Ne |
> | registry / generateCredentials | Ne |
> | registry / getBuildSourceUploadUrl | Ne |
> | registry / GetCredentials | Ne |
> | registry / importImage | Ne |
> | registry / privateEndpointConnectionProxies | Ne |
> | registry / privateEndpointConnectionProxies / ověřit | Ne |
> | registry / privateEndpointConnections | Ne |
> | registry / privateLinkResources | Ne |
> | registry / queueBuild | Ne |
> | registry / regenerateCredential | Ne |
> | registry / regenerateCredentials | Ne |
> | registry / replikace | Ano |
> | registry / běží | Ne |
> | registry / běží / zrušit | Ne |
> | registry / scheduleRun | Ne |
> | registry / scopeMapy | Ne |
> | registry / taskRuns | Ano |
> | registrů / úkolů | Ano |
> | registry / tokeny | Ne |
> | registry / updatePolitiky | Ne |
> | registry / webhooky | Ano |
> | registry / webhooky / getCallbackConfig | Ne |
> | registry / webhooky / ping | Ne |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | containerServices | Ano |
> | spravované clustery | Ano |
> | openShiftManagedClusters | Ano |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Výstrahy | Ne |
> | Fakturační účty | Ne |
> | Rozpočty | Ne |
> | Cloudkonektory | Ne |
> | Konektory | Ano |
> | Oddělení | Ne |
> | Dimenze | Ne |
> | Účty pro zápis | Ne |
> | Vývoz | Ne |
> | Externí billingové účty | Ne |
> | ExterníBillingAccounts / Výstrahy | Ne |
> | ExternalBillingAccounts / Dimenze | Ne |
> | ExternalBillingAccounts / Prognóza | Ne |
> | ExternalBillingAccounts / Dotaz | Ne |
> | Externí odběry | Ne |
> | Externí odběry / Upozornění | Ne |
> | Externí odběry / dimenze | Ne |
> | Externí odběry / Předpověď | Ne |
> | Externí odběry / Dotaz | Ne |
> | Prognóza | Ne |
> | Dotaz | Ne |
> | register | Ne |
> | Reportconfigs | Ne |
> | Sestavy | Ne |
> | Nastavení | Ne |
> | pravidla showback | Ne |
> | Zobrazení | Ne |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Požadavky | Ne |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Sdružení | Ne |
> | resourceProviders | Ano |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Úlohy | Ano |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Zařízení DataBoxEdgeDevices | Ano |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | pracovní prostory | Ano |
> | pracovní prostory / dbPracovní prostory | Ne |
> | pracovní prostory / úložištěŠifrování | Ne |
> | pracovní prostory / virtualNetworkPeerings | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Katalogy | Ano |
> | katalogy dat | Ano |
> | katalogy dat / zdroje dat | Ne |
> | katalogy dat / zdroje dat / skeny | Ne |
> | datové katalogy / zdroje dat / skeny / datové sady | Ne |
> | datové katalogy / zdroje dat / skeny / spouštěče | Ne |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dataTovárny | Ano |
> | dataTovárny / diagnostikaNastavení | Ne |
> | dataTovárny / metrické Definice | Ne |
> | dataFactorySchema | Ne |
> | Továrny | Ano |
> | továrny / integraceRuntimes | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty / dataLakeStoreAccounts | Ne |
> | účty / úložištěÚčty | Ne |
> | účty / úložištěÚčty / kontejnery | Ne |
> | účty / transferAnalyticsUnits | Ne |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty / eventGridFilters | Ne |
> | účty / firewallRules | Ne |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | services | Ano |
> | služby / projekty | Ano |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty / akcie | Ne |
> | účty / akcie / datové soubory | Ne |
> | účty / akcie / pozvánky | Ne |
> | účty / akcie / providersharesubscriptions | Ne |
> | účty / akcie / synchronizaceNastavení | Ne |
> | účty / sharesubscriptions | Ne |
> | účty / sharesubscriptions / consumerSourceDataSets | Ne |
> | účty / sharesubscriptions / datasetmappings | Ne |
> | účty / sharesubscriptions / spouští | Ne |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Servery | Ano |
> | servery / poradci | Ne |
> | servery / klíče | Ne |
> | servery / privátníEndpointConnectionProxies | Ne |
> | servery / privateEndpointConnections | Ne |
> | servery / privateLinkResources | Ne |
> | servery / queryTexty | Ne |
> | servery / obnovitelnéServery | Ne |
> | servery / topQueryStatistics | Ne |
> | servery / virtualNetworkRules | Ne |
> | servery / waitStatistics | Ne |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Servery | Ano |
> | servery / poradci | Ne |
> | servery / klíče | Ne |
> | servery / privátníEndpointConnectionProxies | Ne |
> | servery / privateEndpointConnections | Ne |
> | servery / privateLinkResources | Ne |
> | servery / queryTexty | Ne |
> | servery / obnovitelnéServery | Ne |
> | servery / topQueryStatistics | Ne |
> | servery / virtualNetworkRules | Ne |
> | servery / waitStatistics | Ne |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | serverové skupiny | Ano |
> | Servery | Ano |
> | servery / poradci | Ne |
> | servery / klíče | Ne |
> | servery / privátníEndpointConnectionProxies | Ne |
> | servery / privateEndpointConnections | Ne |
> | servery / privateLinkResources | Ne |
> | servery / queryTexty | Ne |
> | servery / obnovitelnéServery | Ne |
> | servery / topQueryStatistics | Ne |
> | servery / virtualNetworkRules | Ne |
> | servery / waitStatistics | Ne |
> | serveryv2 | Ano |
> | singleServers | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | artefaktzdroje | Ano |
> | zavedení | Ano |
> | serviceTopologie | Ano |
> | serviceTopologie / služby | Ano |
> | serviceTopologie / služby / serviceUnits | Ano |
> | kroky | Ano |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikační skupiny | Ano |
> | aplikační skupiny / aplikace | Ne |
> | aplikační skupiny / stolní počítače | Ne |
> | aplikační skupiny / položky startmenu | Ne |
> | hostitelských poolů | Ano |
> | hostpools / sessionhosts | Ne |
> | hostpools / sessionhosts / usersessions | Ne |
> | hostpools / usersessions | Ne |
> | pracovní prostory | Ano |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Elastické bazény | Ano |
> | ElasticPools / IotHubTenants | Ano |
> | ElasticPools / IotHubTenants / securitySettings | Ne |
> | IotHubs | Ano |
> | IotHubs / eventGridFilters | Ne |
> | IotHubs / zabezpečeníNastavení | Ne |
> | Zřizování služeb | Ano |
> | Použití | Ne |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Potrubí | Ano |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | kontrolery | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | centra laboratoře | Ano |
> | Labs | Ano |
> | laboratoře / prostředí | Ano |
> | laboratoře / servisBěžci | Ano |
> | laboratoře / virtualMachines | Ano |
> | Plány | Ano |

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
> | Domény | Ano |
> | domény / domainOwnershipIdentifiers | Ne |
> | generateSsoRequest | Ne |
> | topLevelDomains | Ne |
> | validateDomainRegistrationInformation | Ne |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | lcsprojekty | Ne |
> | lcsprojects / clouddeployments lcsprojects / clouddeployments lcsprojects / clouddeployments lcs | Ne |
> | lcsprojects / konektory | Ne |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | services | Ano |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Domény | Ano |
> | domény / témata | Ne |
> | eventSubscriptions | Ne |
> | extensionTopics | Ne |
> | obory_názvů partnerů | Ano |
> | partnerNamespaces / eventChannels | Ne |
> | registrace partnerů | Ano |
> | partnerTémata | Ano |
> | partnerTémata / eventOdběry | Ne |
> | systemTopics system | Ano |
> | systemTémata / eventOdběry | Ne |
> | Témata | Ano |
> | topicTypes | Ne |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Clustery | Ano |
> | Obory názvů | Ano |
> | obory názvů / autorizační pravidla | Ne |
> | obory názvů / disasterrecoveryconfigs | Ne |
> | jmenné prostory / eventhubs | Ne |
> | obory názvů / eventhubs / autorizační pravidla | Ne |
> | jmenné prostory / eventhubs / spotřebitelské skupiny | Ne |
> | obory názvů / sady síťových pravidel | Ne |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Obory názvů | Ano |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | featureProviders | Ne |
> | funkce | Ne |
> | Poskytovatelů | Ne |
> | subscriptionFeatureRegistrations | Ne |

## <a name="microsoftgallery"></a>Microsoft.Galerie

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Zapsat | Ne |
> | galerijní položky | Ne |
> | generateartifactaccessuri | Ne |
> | myareas | Ne |
> | myareas / oblasti | Ne |
> | myareas / oblasti / oblasti | Ne |
> | myareas / oblasti / oblasti / galeriepoložky | Ne |
> | myareas / oblasti / galeriepoložky | Ne |
> | myareas / galeriepoložky | Ne |
> | register | Ne |
> | resources | Ne |
> | načíst prostředkybyid | Ne |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | autospravované účty | Ano |
> | autoManagedVmConfigurationProfiles | Ano |
> | konfiguracePřiřazení profilů | Ne |
> | guestConfigurationAssignments | Ne |
> | Software | Ne |
> | softwareUpdateProfile | Ne |
> | softwareAktualizace | Ne |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hanaInstances | Ano |
> | sapMonitory | Ano |

## <a name="microsofthardwaresecuritymodules"></a>Moduly Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | specializované HSM | Ano |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Clustery | Ano |
> | clustery / aplikace | Ne |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | services | Ano |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Stroje | Ano |
> | stroje / rozšíření | Ano |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dataManažeři | Ano |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Součásti | Ano |
> | networkScopes | Ano |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Úlohy | Ano |

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
> | Graph | Ano |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | odstraněné trezory | Ne |
> | hsmPooly | Ano |
> | Klenby | Ano |
> | trezory / přístupPolitiky | Ne |
> | trezory / eventGridFilters | Ne |
> | trezory / tajemství | Ne |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | připojené clustery | Ano |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Clustery | Ano |
> | clustery / připojenékonfigurace databází | Ne |
> | clustery / databáze | Ne |
> | clustery / databáze / datová připojení | Ne |
> | clustery / databáze / eventhubconnections | Ne |
> | clustery / databáze / hlavní úkoly | Ne |
> | clustery / datová připojení | Ne |
> | clustery / principalassignments | Ne |
> | clustery / sdílené identity | Ne |

## <a name="microsoftlabservices"></a>Služby Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | laboratorní účty | Ano |
> | uživatelé | Ne |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hostingProstředí | Ano |
> | integraceÚčty | Ano |
> | integrationServiceEnvironments | Ano |
> | integrationServiceEnvironments / managedApis | Ano |
> | izolované Prostředí | Ano |
> | Pracovní postupy | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | commitmentPlans | Ano |
> | Webservices | Ano |
> | Pracovní prostory | Ano |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | pracovní prostory | Ano |
> | pracovní prostory / počítá | Ne |
> | pracovní prostory / eventGridFilters | Ne |

## <a name="microsoftmaintenance"></a>Microsoft.Údržba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applyUpdates | Ne |
> | konfiguraceÚlohy | Ne |
> | údržbaKonfigurace | Ano |
> | Aktualizace | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Identit | Ne |
> | userAssignedIdentities | Ano |

## <a name="microsoftmanagedservices"></a>Služby Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | marketplaceRegistrationDefinitions | Ne |
> | registraceÚkoly | Ne |
> | registraceDefinice | Ne |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | getEntities | Ne |
> | managementSkupiny | Ne |
> | managementGroups / nastavení | Ne |
> | resources | Ne |
> | startTenantBackfill | Ne |
> | tenantBackfillStatus | Ne |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |
> | účty / eventGridFilters | Ne |
> | účty / privátNíAtlasy | Ano |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Nabízí | Ne |
> | offerTypes | Ne |
> | nabídkaTypy / vydavatelé | Ne |
> | nabídkaTypy / vydavatelé / nabídky | Ne |
> | nabídkaTypy / vydavatelé / nabídky / plány | Ne |
> | nabídkaTypy / vydavatelé / nabídky / plány / dohody | Ne |
> | offerTypes / vydavatelé / nabídky / plány / configs | Ne |
> | offerTypes / vydavatelé / nabídky / plány / configs / importImage | Ne |
> | privategalleryitems | Ne |
> | privátní klient | Ne |
> | privátní obchody | Ne |
> | privátní obchody / nabídky | Ne |
> | Produkty | Ne |
> | Vydavatelů | Ne |
> | vydavatelé / nabídky | Ne |
> | vydavatelé / nabídky / změny | Ne |
> | register | Ne |

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
> | Dohody | Ne |
> | typy nabídek | Ne |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | mediální služby | Ano |
> | mediaservices / accountFilters | Ne |
> | mediální služby / datové zdroje | Ne |
> | mediaservices / asset / assetFilters | Ne |
> | mediaservices / contentKeyPolicies | Ne |
> | mediaservices / eventGridFilters | Ne |
> | mediaservices / liveEventOperations | Ne |
> | mediaservices / liveEvents | Ano |
> | mediaservices / liveEvents / liveOutputs | Ne |
> | mediaservices / liveOutputOperations | Ne |
> | mediaservices / mediaGraphs | Ne |
> | mediaservices / streamingEndpointOperations | Ne |
> | mediaservices / streamingKoncové body | Ano |
> | mediaservices / streamingLokátory | Ne |
> | mediaservices / streamingPolicies | Ne |
> | mediaservices / transformuje | Ne |
> | mediaservices / transformuje / pracovní místa | Ne |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | appClustery | Ano |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hodnoceníProjekty | Ano |
> | migrateprojects | Ano |
> | moveCollections | Ano |
> | Projekty | Ano |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | holographicsBroadcastAccounts | Ano |
> | objectUnderstandingAccounts | Ano |
> | vzdálené účty Rendering | Ano |
> | spatialAnchorsAccounts | Ano |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | netAppAccounts | Ano |
> | netAppAccounts / accountBackups | Ne |
> | netAppAccounts / capacityPools | Ano |
> | netAppAccounts / capacityPool / svazky | Ano |
> | netAppAccounts / capacityPool / svazky / snímky | Ano |
## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applicationGateways | Ano |
> | aplikaceGatewayWebApplicationFirewallPolicies | Ano |
> | applicationSecurityGroups | Ano |
> | azureFirewallFqdnTagy | Ne |
> | azureFirewalls | Ano |
> | bastionHosts | Ano |
> | bGPServiceCommunities | Ne |
> | Připojení | Ano |
> | ddosCustomPolicies | Ano |
> | ddosProtectionPlans | Ano |
> | dnsOperationStaves | Ne |
> | dnszóny | Ano |
> | dnszones / A | Ne |
> | dnszones / AAAA | Ne |
> | dnszones / vše | Ne |
> | dnszones / CAA | Ne |
> | dnszones / CNAME | Ne |
> | dnszones / MX | Ne |
> | dnszones / NS | Ne |
> | dnszones / PTR | Ne |
> | dnszones / sady záznamů | Ne |
> | dnszones / SOA | Ne |
> | dnszones / SRV | Ne |
> | dnszones / TXT | Ne |
> | expressRouteCircuits | Ano |
> | expressRouteCrossConnections | Ano |
> | expressRouteGateways | Ano |
> | expressRoutePorts | Ano |
> | expressRouteServiceProviders | Ne |
> | firewallZásady | Ano |
> | přední dveře | Ano |
> | frontdoorWebApplicationFirewallManagedSady pravidel | Ne |
> | frontdoorWebApplicationFirewallPolicies | Ano |
> | getDnsResourceReference | Ne |
> | internalNotify | Ne |
> | vynakládače | Ano |
> | localNetworkGateways | Ano |
> | natGateways | Ano |
> | networkIntentPolicies | Ano |
> | síťová rozhraní | Ano |
> | networkProfiles | Ano |
> | síťSecuritygroups | Ano |
> | networkWatchers | Ano |
> | networkWatchers / connectionMonitory | Ano |
> | networkWatchers / objektivy | Ano |
> | networkWatchers / pingMeshes | Ano |
> | p2sVpnBrány | Ano |
> | privateDnsOperationStaves | Ne |
> | privátnídnszóny | Ano |
> | privateDnsZones / A | Ne |
> | privateDnsZones / AAAA | Ne |
> | privateDnsZones / vše | Ne |
> | privateDnsZones / CNAME | Ne |
> | privateDnsZones / MX | Ne |
> | privateDnsZones / PTR | Ne |
> | privateDnsZones / SOA | Ne |
> | privateDnsZones / SRV | Ne |
> | privateDnsZones / TXT | Ne |
> | privateDnsZones / virtualNetworkLinks | Ano |
> | privateEndpoints | Ano |
> | privateLinkServices | Ano |
> | publicIPAdresy | Ano |
> | publicIPPrefixes | Ano |
> | routeFilters | Ano |
> | routeTables | Ano |
> | serviceEndpointPolicies | Ano |
> | trafficManagerGeographicHierarchie | Ne |
> | trafficmanagerprofiles | Ano |
> | trafficmanagerprofiles / heatMaps | Ne |
> | trafficManagerUserMetricsKeys | Ne |
> | virtuálníhuby | Ano |
> | virtualNetworkGateways | Ano |
> | virtualNetworks | Ano |
> | virtualNetworkKohouty | Ano |
> | virtualWans | Ano |
> | vpnBrány | Ano |
> | vpnWe | Ano |
> | webApplicationFirewallPolicies | Ano |

## <a name="microsoftnotebooks"></a>Microsoft.Poznámkové bloky

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Notebookproxies | Ne |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Obory názvů | Ano |
> | jmenné prostory / notificationHubs | Ano |

## <a name="microsoftobjectstore"></a>Úložiště Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | osNamespaces | Ano |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | HyperVSites | Ano |
> | Dovozní weby | Ano |
> | Serverové weby | Ano |
> | Weby VMware | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Clustery | Ano |
> | propojit cíle | Ne |
> | storageInsightConfigs | Ne |
> | pracovní prostory | Ano |
> | pracovní prostory / dataExport | Ne |
> | pracovní prostory / dataZdroje | Ne |
> | pracovní prostory / linkedServices | Ne |
> | pracovní prostory / propojenéstorageaccounts | Ne |
> | pracovní prostory / dotaz | Ne |
> | pracovní prostory / oborPrivateLinkProxies | Ne |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | managementassociations | Ne |
> | konfigurace správy | Ano |
> | Řešení | Ano |
> | zobrazení | Ano |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | starší partnerské společnosti | Ne |
> | peerAsns | Ne |
> | peeringy | Ano |
> | peeringServiceCountries | Ne |
> | zprostředkovatelé služby partnerského vztahu | Ne |
> | služby peeringServices | Ano |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | policyEvents | Ne |
> | policyMetadata | Ne |
> | policyStates | Ne |
> | policyTrackedResources | Ne |
> | náprava | Ne |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Konzoly nástroje | Ne |
> | řídicí panely | Ano |
> | userSettings | Ne |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | kolekce pracovního prostoru | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Kapacity | Ano |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Pracovní prostory | Ano |

## <a name="microsoftrecoveryservices"></a>Služby Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | backupProtectedItems | Ne |
> | Klenby | Ano |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Obory názvů | Ano |
> | obory názvů / autorizační pravidla | Ne |
> | jmenné prostory / hybridní připojení | Ne |
> | obory názvů / hybridconnections / authorizationrules | Ne |
> | jmenné prostory / wcfrelays | Ne |
> | jmenné prostory / wcfrelays / autorizační pravidla | Ne |

## <a name="microsoftremoteapp"></a>Aplikace Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ne |
> | Sbírky | Ano |
> | sbírky / aplikace | Ne |
> | kolekce / securityprincipals | Ne |
> | templateImages | Ne |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Dotazy | Ano |
> | resourceChangeDetails | Ne |
> | resourceChanges | Ne |
> | resources | Ne |
> | resourcesHistory | Ne |
> | subscriptionsStatus | Ne |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | availabilityStavy | Ne |
> | childAvailabilityStatuses | Ne |
> | childResources | Ne |
> | vznikající problémy | Ne |
> | stránka events | Ne |
> | impactedZdroje | Ne |
> | zprostředkovatele identity | Ne |
> | Oznámení | Ne |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Nasazení | Ne |
> | nasazení / provoz | Ne |
> | nasazeníScripts | Ano |
> | deploymentScripts / logs | Ne |
> | Odkazy | Ne |
> | upozornitúlohy | Ne |
> | Poskytovatelů | Ne |
> | skupiny prostředků | Ne |
> | Odběry | Ne |
> | Nájemníky | Ne |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikace | Ano |
> | saaszdroje | Ne |

## <a name="microsoftsearch"></a>Microsoft.Hledat

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | resourceHealthMetadata | Ne |
> | searchServices | Ano |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | adaptivníNetworkHardenings | Ne |
> | advancedThreatProtectionSettings | Ne |
> | výstrahy | Ne |
> | allowedConnections | Ne |
> | applicationWhitelistings | Ne |
> | hodnoceníMetadata | Ne |
> | hodnocení | Ne |
> | autoDismissAlertsRules | Ne |
> | automatizace | Ano |
> | Nastavení automatického zřízení | Ne |
> | Dodržování předpisů | Ne |
> | dataCollectionAgents | Ne |
> | deviceSecurityGroups | Ne |
> | objevilSecuritySolutions | Ne |
> | externíbezpečnostní řešení | Ne |
> | Zásady informační ochrany | Ne |
> | Řešení iotSecuritySolutions | Ano |
> | iotSecuritySolutions / analyticsModely | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Ne |
> | jitNetworkAccessPolicies | Ne |
> | networkData | Ne |
> | Zásady | Ne |
> | ceny | Ne |
> | regulační standardy compliance | Ne |
> | regulační compliancestandardy / regulační compliancecontrols | Ne |
> | regulační compliancestandardy / regulační compliancecontrols / regulační complianceassessments | Ne |
> | secureScoreControlDefinitions | Ne |
> | secureScoreControls | Ne |
> | secureScores | Ne |
> | secureScores / secureScoreControls | Ne |
> | securityKontakty | Ne |
> | bezpečnostní řešení | Ne |
> | securitySolutionsReferenceData | Ne |
> | securityStatuses | Ne |
> | securityStatusesSouhrny | Ne |
> | serverAssessments | Ne |
> | settings | Ne |
> | dílčí hodnocení | Ne |
> | úlohy | Ne |
> | Topologie | Ne |
> | nastavení pracovního prostoru | Ne |

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
> | Agregace | Ne |
> | alertRules | Ne |
> | alertRuleTemplates | Ne |
> | Záložky | Ne |
> | Případech | Ne |
> | datové konektory | Ne |
> | dataConnectorsCheckRequirements | Ne |
> | Entity | Ne |
> | entityDotazy | Ne |
> | Incidenty | Ne |
> | officeConsents | Ne |
> | settings | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Obory názvů | Ano |
> | obory názvů / autorizační pravidla | Ne |
> | obory názvů / disasterrecoveryconfigs | Ne |
> | obory názvů / eventgridfilters | Ne |
> | obory názvů / sady síťových pravidel | Ne |
> | jmenné prostory / fronty | Ne |
> | obory názvů / fronty / autorizační pravidla | Ne |
> | jmenné prostory / témata | Ne |
> | jmenné prostory / témata / autorizační pravidla | Ne |
> | jmenovky / témata / odběry | Ne |
> | jmenovky / témata / odběry / pravidla | Ne |
> | premiumMessagingRegiony | Ne |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikace | Ano |
> | Clustery | Ano |
> | clustery / aplikace | Ne |
> | containerGroups | Ano |
> | containerGroupSets | Ano |
> | okrajové clustery | Ano |
> | edgeclusters / aplikace | Ne |
> | spravované clustery | Ano |
> | spravované clustery / nodetypy | Ne |
> | Sítí | Ano |
> | tajné obchody | Ano |
> | tajné obchody / certifikáty | Ne |
> | tajné obchody / tajemství | Ne |
> | volumes | Ano |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | aplikace | Ano |
> | containerGroups | Ano |
> | Brány | Ano |
> | Sítí | Ano |
> | Tajemství | Ano |
> | volumes | Ano |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | providerRegistrace | Ne |
> | providerRegistrations / resourceTypeRegistrations | Ne |
> | zavedení | Ano |

## <a name="microsoftsignalrservice"></a>Služba Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SignalR | Ano |
> | SignalR / eventGridFilters | Ne |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Úložiště SiteRecoveryVault | Ano |

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
> | aplikace | Ano |
> | jitRequests | Ano |

## <a name="microsoftspoolservice"></a>Služba Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | registeredSubscriptions | Ne |
> | Cívky | Ano |

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | spravované instance | Ano |
> | managedInstances / databáze | Ano |
> | managedInstances / databáze / backupShortTermRetentionPolicies | Ne |
> | managedInstances / databáze / schémata / tabulky / sloupce / citlivostLabels | Ne |
> | managedInstance / databáze / hodnocení zranitelnosti | Ne |
> | managedInstance / databáze / zranitelnostAssessments / pravidla / základní | Ne |
> | managedInstances / encryptionProtector | Ne |
> | managedInstances / klíče | Ne |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Ne |
> | managedInstances / vulnerabilityAssessments | Ne |
> | Servery | Ano |
> | servery / správci | Ne |
> | servery / komunikačníOdkazy | Ne |
> | servery / databáze | Ano |
> | servery / encryptionProtector | Ne |
> | servery / firewallRules | Ne |
> | servery / klíče | Ne |
> | servery / restorableDroppedDatabáze | Ne |
> | servery / servisní cíle | Ne |
> | servery / tdeCertificates | Ne |
> | virtuální clustery | Ne |

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
> | úložištěÚčty / služby | Ne |
> | storageAccounts / služby / metricDefinitions | Ne |
> | storageAccounts / tableServices | Ne |
> | Použití | Ne |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Mezipaměti | Ano |
> | mezipaměti / storageTargets | Ne |
> | usageModely | Ne |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | replikační skupiny | Ne |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices / registeredServers | Ne |
> | storageSyncServices / syncGroups | Ne |
> | storageSyncServices / syncGroups / cloudKoncové body | Ne |
> | storageSyncServices / syncGroups / serverKoncové body | Ne |
> | storageSyncServices / pracovní postupy | Ne |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices / registeredServers | Ne |
> | storageSyncServices / syncGroups | Ne |
> | storageSyncServices / syncGroups / cloudKoncové body | Ne |
> | storageSyncServices / syncGroups / serverKoncové body | Ne |
> | storageSyncServices / pracovní postupy | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano |
> | storageSyncServices / registeredServers | Ne |
> | storageSyncServices / syncGroups | Ne |
> | storageSyncServices / syncGroups / cloudKoncové body | Ne |
> | storageSyncServices / syncGroups / serverKoncové body | Ne |
> | storageSyncServices / pracovní postupy | Ne |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Manažeři | Ano |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | streamováníúlohy | Ano |

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | cancel | Ne |
> | Createsubscription | Ne |
> | Povolit | Ne |
> | přejmenovat | Ne |
> | Definice předplatného | Ne |
> | PředplatnéOperace | Ne |
> | Odběry | Ne |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Prostředí | Ano |
> | prostředí / přístupPolitiky | Ne |
> | prostředí / eventsources | Ano |
> | prostředí / referenceDataSets | Ano |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudJednoduché

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | dedicatedCloudNodes | Ano |
> | dedicatedCloudServices | Ano |
> | virtualMachines | Ano |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | zařízení | Ano |
> | registeredSubscriptions | Ne |
> | Dodavatelů | Ne |
> | prodejci / skus | Ne |
> | prodejci / vnfs | Ne |
> | virtualNetworkFunctionSkus | Ne |
> | vnfs | Ano |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | apiManagementAccounts | Ne |
> | apiManagementAccounts / apiAcls | Ne |
> | apiManagementAccounts / apis | Ne |
> | apiManagementAccounts / api / apiAcls | Ne |
> | apiManagementAccounts / apis / connectionAcls | Ne |
> | apiManagementAccounts / apis / připojení | Ne |
> | apiManagementAccounts / apis / připojení / připojeníAcls | Ne |
> | apiManagementAccounts / apis / lokalizovanéDefinice | Ne |
> | apiManagementAccounts / connectionAcls | Ne |
> | apiManagementAccounts / připojení | Ne |
> | fakturační měřiče | Ne |
> | certifikáty | Ano |
> | connectionGateways | Ano |
> | Připojení | Ano |
> | vlastníApis | Ano |
> | smazané weby | Ne |
> | hostingProstředí | Ano |
> | hostingEnvironments / eventGridFilters | Ne |
> | hostingEnvironments / multiRolePools | Ne |
> | hostingEnvironments / workerPools | Ne |
> | kubeŽivotní prostředí | Ano |
> | publikováníUživatelé | Ne |
> | Doporučení | Ne |
> | resourceHealthMetadata | Ne |
> | Runtime | Ne |
> | serverFarms | Ano |
> | serverFarms / eventGridFilters | Ne |
> | Stránky | Ano |
> | weby/konfigurace  | Ne |
> | weby / eventGridFilters | Ne |
> | weby / hostNameBindings | Ne |
> | stránky / networkConfig | Ne |
> | stránky / premieraddons | Ano |
> | stránky / sloty | Ano |
> | stránky / sloty / eventGridFilters | Ne |
> | stránky / sloty / hostNameBindings | Ne |
> | stránky / sloty / networkConfig | Ne |
> | sourceControls | Ne |
> | statickéWeby | Ano |
> | Ověřit | Ne |
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
> | Služby DeviceServices | Ano |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Součásti | Ne |
> | componentsSummary | Ne |
> | monitorInstances | Ne |
> | monitorInstancesSummary | Ne |
> | Monitory | Ne |
> | notificationSettings | Ne |

## <a name="next-steps"></a>Další kroky

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte soubor [complete-mode-deletion.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).

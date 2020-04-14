---
title: Označit podporu pro zdroje
description: Zobrazuje, které typy prostředků Azure podporují značky. Obsahuje podrobnosti o všech službách Azure.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: c971d3af102faf99f97aac261882797460d71f37
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81255022"
---
# <a name="tag-support-for-azure-resources"></a>Podpora prostředků Azure pro značky
Tento článek popisuje, zda typ prostředku podporuje [značky](tag-resources.md). Sloupec s popiskem **Supports označuje,** zda má typ prostředku vlastnost značky. Sloupec označený **značka v sestavě nákladů** označuje, zda tento typ zdroje předá značku sestavě nákladů. Náklady můžete zobrazit podle značek v [analýze nákladů na správu nákladů](../../cost-management-billing/costs/quick-acm-cost-analysis.md#understanding-grouping-and-filtering-options) a fakturační [faktury Azure a denních dat o využití](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md).

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte soubor [tag-support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

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
> - [Microsoft.Insights](#microsoftinsights)
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
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | DomainServices | Ano | Ano |
> | DomainServices / oucontainer | Ne | Ne |

## <a name="microsoftaddons"></a>Microsoft.Addons

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | supportProviders | Ne | Ne |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.AdhybridHealthService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aadsupportcases | Ne | Ne |
> | přidá služby | Ne | Ne |
> | Agenti | Ne | Ne |
> | anonymní apiusers | Ne | Ne |
> | konfigurace | Ne | Ne |
> | Protokoly | Ne | Ne |
> | reports | Ne | Ne |
> | servicehealthmetrics | Ne | Ne |
> | services | Ne | Ne |

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Konfigurace | Ne | Ne |
> | generateRecommendations | Ne | Ne |
> | zprostředkovatele identity | Ne | Ne |
> | Doporučení | Ne | Ne |
> | potlačení | Ne | Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | actionRules | Ano | Ano |
> | výstrahy | Ne | Ne |
> | seznam upozornění | Ne | Ne |
> | upozorněníMetaData | Ne | Ne |
> | alertsSummary | Ne | Ne |
> | seznam výstrahSouhrn | Ne | Ne |
> | smartDetectorAlertRules | Ano | Ano |
> | smartGroups | Ne | Ne |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Servery | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | reportFeedback | Ne | Ne |
> | služba | Ano | Ano |
> | validateServiceName | Ne | Ne |

## <a name="microsoftappconfiguration"></a>Microsoft.AppKonfigurace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ano | Ano |
> | configurationStores / eventGridFilters | Ne | Ne |

## <a name="microsoftappplatform"></a>Platforma Microsoft.AppPlatforma

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Spring | Ano | Ano |

## <a name="microsoftattestation"></a>Microsoft.Atestation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | atestacePoskytovatelé | Ne | Ne |

## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | classicAdministrators | Ne | Ne |
> | dataAliases | Ne | Ne |
> | odepřítÚkoly | Ne | Ne |
> | elevateAccess | Ne | Ne |
> | findOrphanRoleAssignments | Ne | Ne |
> | Zámky | Ne | Ne |
> | Oprávnění | Ne | Ne |
> | policyAssignments | Ne | Ne |
> | policyDefinitions | Ne | Ne |
> | policySetDefinitions | Ne | Ne |
> | providerOperations | Ne | Ne |
> | roleÚkoly | Ne | Ne |
> | roleAssignmentsUsageMetrics | Ne | Ne |
> | definice rolí | Ne | Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | automationAccounts | Ano | Ano |
> | automationAccounts / konfigurace | Ano | Ano |
> | automationAccounts / úlohy | Ne | Ne |
> | automationAccounts / privateEndpointConnectionProxies | Ne | Ne |
> | automationAccounts / privateEndpointConnections | Ne | Ne |
> | automationAccounts / privateLinkResources | Ne | Ne |
> | automationAccounts / runbooks | Ano | Ano |
> | automationAccounts / softwareUpdateKonfigurace | Ne | Ne |
> | automationAccounts / webhooky | Ne | Ne |

## <a name="microsoftazconfig"></a>Microsoft.Azconfig

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | configurationStores | Ano | Ano |
> | configurationStores / eventGridFilters | Ne | Ne |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Ženeva

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Prostředí | Ne | Ne |
> | prostředí / účty | Ne | Ne |
> | prostředí / účty / obory názvů | Ne | Ne |
> | prostředí / účty / jmenné prostory / konfigurace | Ne | Ne |

## <a name="microsoftazureactivedirectory"></a>Adresář Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | b2cAdresáře | Ano | Ne |
> | b2ctenants | Ne | Ne |

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hybridDataManagers | Ano | Ano |
> | postgresInstances | Ano | Ano |
> | instance sql Instances | Ano | Ano |
> | sqlServerRegistrations | Ano | Ano |
> | sqlServerRegistrations / sqlServers | Ne | Ne |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | cloudManifestFiles | Ne | Ne |
> | Registrace | Ano | Ano |
> | registrace / zákazníkPředplatné | Ne | Ne |
> | registrace / produkty | Ne | Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | dávkové účty | Ano | Ano |

## <a name="microsoftbilling"></a>Microsoft.Fakturace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | fakturační účty | Ne | Ne |
> | billingAccounts / smlouvy | Ne | Ne |
> | billingAccounts / billingPermissions | Ne | Ne |
> | billingAccounts / billingProfiles | Ne | Ne |
> | billingAccounts / billingProfiles / billingPermissions | Ne | Ne |
> | billingAccounts / billingProfiles / billingRoleAssignments | Ne | Ne |
> | billingAccounts / billingProfiles / billingRoleDefinitions | Ne | Ne |
> | billingAccounts / billingProfiles / billingSubscriptions billing | Ne | Ne |
> | billingAccounts / billingProfiles / createBillingRoleAssignment | Ne | Ne |
> | billingAccounts / billingProfiles / zákazníci | Ne | Ne |
> | billingAccounts / billingProfiles / pokyny | Ne | Ne |
> | billingAccounts / billingProfiles / faktury | Ne | Ne |
> | billingAccounts / billingProfiles / faktury / ceník | Ne | Ne |
> | billingAccounts / billingProfiles / faktury / transakce | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / billingPermissions | Ne | Ne |
> | billingAccounts / billingProfiles / fakturSections / billingRoleAssignments billingAccounts / billingAccounts / billing | Ne | Ne |
> | billingAccounts / billingProfiles / fakturSections / billingRoleDefinitions | Ne | Ne |
> | billingAccounts / billingProfiles / fakturSections / billingSubscriptions billingAccounts / billingAccounts / billing | Ne | Ne |
> | billingAccounts / billingProfiles / invoiceSections / createBillingRoleAssignment | Ne | Ne |
> | billingAccounts / billingProfiles / fakturySekce / initiateTransfer | Ne | Ne |
> | billingAccounts / billingProfiles / fakturYSekce / produkty | Ne | Ne |
> | billingAccounts / billingProfiles / fakturYSekce / produkty / převod | Ne | Ne |
> | billingAccounts / billingProfiles / fakturYSekce / produkty / updateAutoRenew | Ne | Ne |
> | billingAccounts / billingProfiles / fakturYSekce / transakce | Ne | Ne |
> | billingAccounts / billingProfiles / fakturySekce / převody | Ne | Ne |
> | billingAccounts / BillingProfiles / patchOperations | Ne | Ne |
> | billingAccounts / billingProfiles / paymentMethods | Ne | Ne |
> | billingAccounts / billingProfiles / zásady | Ne | Ne |
> | billingAccounts / billingProfiles / ceník | Ne | Ne |
> | billingAccounts / billingProfiles / ceníkDownloadOperations | Ne | Ne |
> | billingAccounts / billingProfiles / produkty | Ne | Ne |
> | billingAccounts / billingProfiles / transakce | Ne | Ne |
> | billingAccounts / billingRoleAssignments | Ne | Ne |
> | billingAccounts / billingRoleDefinitions | Ne | Ne |
> | billingAccounts / billingSubscriptions | Ne | Ne |
> | billingAccounts / billingSubscriptions / faktury | Ne | Ne |
> | billingAccounts / createBillingRoleAssignment | Ne | Ne |
> | billingAccounts / createInvoiceSectionOperations | Ne | Ne |
> | billingAccounts / zákazníci | Ne | Ne |
> | billingAccounts / customers / billingPermissions billingAccounts / billingPermissions billingAccounts / customers / billingPermissions billingAccounts / customers | Ne | Ne |
> | billingAccounts / customers / billingSubscriptions billing | Ne | Ne |
> | billingAccounts / customers / initiateTransfer billingAccounts / customers / initiateTransfer billingAccounts / customers / initiateTransfer billingAccounts | Ne | Ne |
> | billingAccounts / zákazníci / zásady | Ne | Ne |
> | billingAccounts / zákazníci / produkty | Ne | Ne |
> | billingAccounts / odběratelé / transakce | Ne | Ne |
> | billingAccounts / zákazníci / převody | Ne | Ne |
> | billingAccounts / oddělení | Ne | Ne |
> | billingAccounts / enrollmentAccounts | Ne | Ne |
> | billingAccounts / faktury | Ne | Ne |
> | billingAccounts / fakturySekce | Ne | Ne |
> | billingAccounts / fakturySekce / billingSubscriptionMoveOperations | Ne | Ne |
> | billingAccounts / fakturySekce / fakturaceOdběry | Ne | Ne |
> | billingAccounts / fakturySekce / fakturacePředplatné / převod | Ne | Ne |
> | billingAccounts / fakturySekce / zvýšení | Ne | Ne |
> | billingAccounts / fakturySekce / initiateTransfer | Ne | Ne |
> | billingAccounts / fakturySekce / patchOperations | Ne | Ne |
> | billingAccounts / fakturySekce / productMoveOperations | Ne | Ne |
> | billingAccounts / fakturySekce / produkty | Ne | Ne |
> | billingAccounts / fakturySekce / produkty / převod | Ne | Ne |
> | billingAccounts / fakturySekce / produkty / updateAutoRenew | Ne | Ne |
> | billingAccounts / fakturySekce / transakce | Ne | Ne |
> | billingAccounts / fakturySekce / převody | Ne | Ne |
> | billingAccounts / lineOfCredit | Ne | Ne |
> | billingAccounts / patchOperations | Ne | Ne |
> | billingAccounts / paymentMethods | Ne | Ne |
> | billingAccounts / produkty | Ne | Ne |
> | fakturaceÚčty / transakce | Ne | Ne |
> | fakturační období | Ne | Ne |
> | oprávnění fakturace | Ne | Ne |
> | billingProperty | Ne | Ne |
> | billingRoleAssignments | Ne | Ne |
> | billingRoleDefinitions | Ne | Ne |
> | createBillingRoleAssignment | Ne | Ne |
> | Oddělení | Ne | Ne |
> | enrollmentAccounts | Ne | Ne |
> | Faktury | Ne | Ne |
> | Převody | Ne | Ne |
> | převody / acceptTransfer | Ne | Ne |
> | převody / declineTransfer | Ne | Ne |
> | převody / provozStav | Ne | Ne |
> | převody / validateTransfer | Ne | Ne |
> | ověřitadresu | Ne | Ne |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | mapApis | Ano | Ano |
> | updateCommunicationPreference | Ne | Ne |

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | blockchainČlenové | Ano | Ano |
> | cordaČlenové | Ano | Ano |
> | Pozorovatelů | Ano | Ano |

## <a name="microsoftblockchaintokens"></a>Microsoft.BlockchainTokeny

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Služby TokenServices | Ano | Ano |
> | TokenServices / BlockchainNetworks | Ne | Ne |
> | TokenServices / Skupiny | Ne | Ne |
> | TokenServices / Skupiny / Účty | Ne | Ne |
> | TokenServices / TokenTemplates | Ne | Ne |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | podrobné úkoly | Ne | Ne |
> | podrobné plányÚkoly / assignmentOperations | Ne | Ne |
> | podrobné plánYÚkoly / operace | Ne | Ne |
> | Plány | Ne | Ne |
> | plány / artefakty | Ne | Ne |
> | plány / verze | Ne | Ne |
> | plány / verze / artefakty | Ne | Ne |

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | botServices | Ano | Ano |
> | botServices / kanály | Ne | Ne |
> | botServices / připojení | Ne | Ne |
> | jazyky | Ne | Ne |
> | šablony | Ne | Ne |

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Redis | Ano | Ano |

## <a name="microsoftcapacity"></a>Microsoft.Kapacita

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | appliedReservations | Ne | Ne |
> | automatické zvýšení kvóty | Ne | Ne |
> | calculateExchange | Ne | Ne |
> | vypočítatCena | Ne | Ne |
> | calculatePurchasePrice | Ne | Ne |
> | Katalogy | Ne | Ne |
> | commercialReservationOrders | Ne | Ne |
> | Exchange | Ne | Ne |
> | placePurchaseOrder | Ne | Ne |
> | rezervaceObjednávky | Ne | Ne |
> | rezervaceObjednávky / calculateRefund | Ne | Ne |
> | rezervaceObjednávky / sloučení | Ne | Ne |
> | rezervaceObjednávky / rezervace | Ne | Ne |
> | rezervaceObjednávky / rezervace / revize | Ne | Ne |
> | rezervaceObjednávky / návrat | Ne | Ne |
> | rezervaceObjednávky / rozdělení | Ne | Ne |
> | rezervaceObjednávky / swap | Ne | Ne |
> | Rezervace | Ne | Ne |
> | resourceProviders | Ne | Ne |
> | resources | Ne | Ne |
> | validateReservationOrder | Ne | Ne |

## <a name="microsoftcdn"></a>Soubor Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | CdnWebApplicationFirewallManagedSady pravidel | Ne | Ne |
> | CdnWebApplicationFirewallPolicies | Ano | Ano |
> | okrajové uzly | Ne | Ne |
> | Profily | Ano | Ano |
> | profily / koncové body | Ano | Ano |
> | profily / koncové body / vlastní domény | Ne | Ne |
> | profily / koncové body / origingroups | Ne | Ne |
> | profily / koncové body / počátky | Ne | Ne |
> | validateProbe | Ne | Ne |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | certificateObjednávky | Ano | Ano |
> | certificateObjednávky / certifikáty | Ne | Ne |
> | validateCertificateRegistrationInformation | Ne | Ne |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Schopnosti | Ne | Ne |
> | domainNames | Ne | Ne |
> | domainNames / capabilities domainNames / capabilities domainNames / capabilities domainNames | Ne | Ne |
> | domainNames / internalLoadBalancers | Ne | Ne |
> | domainNames / serviceCertificates | Ne | Ne |
> | domainNames / sloty | Ne | Ne |
> | domainNames / sloty / role | Ne | Ne |
> | domainNames / sloty / role / metricDefinitions | Ne | Ne |
> | domainNames / sloty / role / metriky | Ne | Ne |
> | moveSubscriptionResources | Ne | Ne |
> | operatingSystemFamilies | Ne | Ne |
> | provoznísystémy | Ne | Ne |
> | quotas | Ne | Ne |
> | resourceTypes | Ne | Ne |
> | validateSubscriptionMoveAvailability | Ne | Ne |
> | virtualMachines | Ne | Ne |
> | virtualMachines / diagnosticSettings | Ne | Ne |
> | virtualMachines / metricDefinitions virtualMachines / metricDefinitions virtualMachines / metricDefinitions virtualMachines | Ne | Ne |
> | virtualMachines / metriky | Ne | Ne |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | classicInfrastructureZdroje | Ne | Ne |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Schopnosti | Ne | Ne |
> | expressRouteCrossConnections | Ne | Ne |
> | expressRouteCrossConnections / peerings expressRouteCrossConnections / peerings expressRouteCrossConnections / peerings expressRoute | Ne | Ne |
> | gatewaySupportedDevices | Ne | Ne |
> | síťSecuritygroups | Ne | Ne |
> | quotas | Ne | Ne |
> | rezervované Ips | Ne | Ne |
> | virtualNetworks | Ne | Ne |
> | virtualNetworks / remoteVirtualNetworkPeeringProxies | Ne | Ne |
> | virtualNetworks / virtualNetworkPeerings | Ne | Ne |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Schopnosti | Ne | Ne |
> | Disky | Ne | Ne |
> | images | Ne | Ne |
> | osImages | Ne | Ne |
> | osPlatformImages | Ne | Ne |
> | publicImages | Ne | Ne |
> | quotas | Ne | Ne |
> | storageAccounts | Ne | Ne |
> | storageAccounts / blobServices | Ne | Ne |
> | storageAccounts / fileServices | Ne | Ne |
> | storageAccounts / metricDefinitions | Ne | Ne |
> | storageAccounts / metriky | Ne | Ne |
> | storageAccounts / queueServices | Ne | Ne |
> | úložištěÚčty / služby | Ne | Ne |
> | úložištěÚčty / služby / diagnostikaNastavení | Ne | Ne |
> | storageAccounts / služby / metricDefinitions | Ne | Ne |
> | úložištěÚčty / služby / metriky | Ne | Ne |
> | storageAccounts / tableServices | Ne | Ne |
> | storageAccounts / vmImages | Ne | Ne |
> | vmImages | Ne | Ne |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |

## <a name="microsoftcommerce"></a>Microsoft.Commerce

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | RateCard | Ne | Ne |
> | Agregace použití | Ne | Ne |

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | dostupnostSady | Ano | Ano |
> | diskové šifrovací sady | Ano | Ano |
> | Disky | Ano | Ano |
> | Galerie | Ano | Ano |
> | galerie / aplikace | Ne | Ne |
> | galerie / aplikace / verze | Ne | Ne |
> | galerie / obrázky | Ne | Ne |
> | galerie / obrázky / verze | Ne | Ne |
> | skupiny hostitelů | Ano | Ano |
> | hostGroups / hosts | Ano | Ano |
> | images | Ano | Ano |
> | proximityPlacementGroups | Ano | Ano |
> | obnoveníKolekce Point | Ano | Ano |
> | restorePointCollections / restorePoints | Ne | Ne |
> | sharedVMExtensions | Ano | Ano |
> | sharedVMExtensions / verze | Ne | Ne |
> | sdílenéObrazy VM | Ano | Ano |
> | sharedVMImages / verze | Ne | Ne |
> | snímky | Ano | Ano |
> | sshPublicKeys | Ano | Ano |
> | virtualMachines | Ano | Ano |
> | virtualMachines / rozšíření | Ano | Ano |
> | virtualMachines / metricDefinitions virtualMachines / metricDefinitions virtualMachines / metricDefinitions virtualMachines | Ne | Ne |
> | virtualMachineScaleSets | Ano | Ano |
> | virtualMachineScaleSets / rozšíření | Ne | Ne |
> | virtualMachineScaleSets / networkInterfaces | Ne | Ne |
> | virtualMachineScaleSets / publicIPAdresy | Ne | Ne |
> | virtualMachineScaleSets / virtualMachines | Ne | Ne |
> | virtualMachineScaleSets / virtualMachines / networkInterfaces virtualMachineScaleSets / virtualMachines / networkInterfaces virtualMachineScaleSets / virtualMachines / networkInterfaces virtualMachine | Ne | Ne |

## <a name="microsoftconsumption"></a>Microsoft.Spotřeba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Agregované náklady | Ne | Ne |
> | Zůstatky | Ne | Ne |
> | Rozpočty | Ne | Ne |
> | Poplatky | Ne | Ne |
> | Značky nákladů | Ne | Ne |
> | Kredity | Ne | Ne |
> | stránka events | Ne | Ne |
> | Prognózy | Ne | Ne |
> | Hodně | Ne | Ne |
> | Tržiště | Ne | Ne |
> | Ceníky | Ne | Ne |
> | Produkty | Ne | Ne |
> | RezervacePodrobnosti | Ne | Ne |
> | RezervaceDoporučení | Ne | Ne |
> | RezervaceSouhrny | Ne | Ne |
> | ReservationTransactions | Ne | Ne |
> | Značky | Ne | Ne |
> | Nájemníky | Ne | Ne |
> | Výrazy | Ne | Ne |
> | Podrobnosti o použití | Ne | Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | containerGroups | Ano | Ano |
> | serviceAssociationLinks | Ne | Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Rejstříků | Ano | Ano |
> | registry / agentPooly | Ano | Ano |
> | registry / staví | Ne | Ne |
> | registry / staví / zrušit | Ne | Ne |
> | registry / staví / getLogLink | Ne | Ne |
> | registrů / buildTasks | Ano | Ano |
> | registry / buildTasks / kroky | Ne | Ne |
> | registry / eventGridFilters | Ne | Ne |
> | registry / generateCredentials | Ne | Ne |
> | registry / getBuildSourceUploadUrl | Ne | Ne |
> | registry / GetCredentials | Ne | Ne |
> | registry / importImage | Ne | Ne |
> | registry / privateEndpointConnectionProxies | Ne | Ne |
> | registry / privateEndpointConnectionProxies / ověřit | Ne | Ne |
> | registry / privateEndpointConnections | Ne | Ne |
> | registry / privateLinkResources | Ne | Ne |
> | registry / queueBuild | Ne | Ne |
> | registry / regenerateCredential | Ne | Ne |
> | registry / regenerateCredentials | Ne | Ne |
> | registry / replikace | Ano | Ano |
> | registry / běží | Ne | Ne |
> | registry / běží / zrušit | Ne | Ne |
> | registry / scheduleRun | Ne | Ne |
> | registry / scopeMapy | Ne | Ne |
> | registry / taskRuns | Ano | Ano |
> | registrů / úkolů | Ano | Ano |
> | registry / tokeny | Ne | Ne |
> | registry / updatePolitiky | Ne | Ne |
> | registry / webhooky | Ano | Ano |
> | registry / webhooky / getCallbackConfig | Ne | Ne |
> | registry / webhooky / ping | Ne | Ne |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | containerServices | Ano | Ano |
> | spravované clustery | Ano | Ano |
> | openShiftManagedClusters | Ano | Ano |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Výstrahy | Ne | Ne |
> | Fakturační účty | Ne | Ne |
> | Rozpočty | Ne | Ne |
> | Cloudkonektory | Ne | Ne |
> | Konektory | Ano | Ano |
> | Oddělení | Ne | Ne |
> | Dimenze | Ne | Ne |
> | Účty pro zápis | Ne | Ne |
> | Vývoz | Ne | Ne |
> | Externí billingové účty | Ne | Ne |
> | ExterníBillingAccounts / Výstrahy | Ne | Ne |
> | ExternalBillingAccounts / Dimenze | Ne | Ne |
> | ExternalBillingAccounts / Prognóza | Ne | Ne |
> | ExternalBillingAccounts / Dotaz | Ne | Ne |
> | Externí odběry | Ne | Ne |
> | Externí odběry / Upozornění | Ne | Ne |
> | Externí odběry / dimenze | Ne | Ne |
> | Externí odběry / Předpověď | Ne | Ne |
> | Externí odběry / Dotaz | Ne | Ne |
> | Prognóza | Ne | Ne |
> | Dotaz | Ne | Ne |
> | register | Ne | Ne |
> | Reportconfigs | Ne | Ne |
> | Sestavy | Ne | Ne |
> | Nastavení | Ne | Ne |
> | pravidla showback | Ne | Ne |
> | Zobrazení | Ne | Ne |

## <a name="microsoftcustomerlockbox"></a>Microsoft.CustomerLockbox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Požadavky | Ne | Ne |

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Sdružení | Ne | Ne |
> | resourceProviders | Ano | Ano |

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Úlohy | Ano | Ano |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Zařízení DataBoxEdgeDevices | Ano | Ano |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | pracovní prostory | Ano | Ne |
> | pracovní prostory / dbPracovní prostory | Ne | Ne |
> | pracovní prostory / úložištěŠifrování | Ne | Ne |
> | pracovní prostory / virtualNetworkPeerings | Ne | Ne |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Katalogy | Ano | Ano |
> | katalogy dat | Ano | Ano |
> | katalogy dat / zdroje dat | Ne | Ne |
> | katalogy dat / zdroje dat / skeny | Ne | Ne |
> | datové katalogy / zdroje dat / skeny / datové sady | Ne | Ne |
> | datové katalogy / zdroje dat / skeny / spouštěče | Ne | Ne |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | dataTovárny | Ano | Ne |
> | dataTovárny / diagnostikaNastavení | Ne | Ne |
> | dataTovárny / metrické Definice | Ne | Ne |
> | dataFactorySchema | Ne | Ne |
> | Továrny | Ano | Ne |
> | továrny / integraceRuntimes | Ne | Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty / dataLakeStoreAccounts | Ne | Ne |
> | účty / úložištěÚčty | Ne | Ne |
> | účty / úložištěÚčty / kontejnery | Ne | Ne |
> | účty / transferAnalyticsUnits | Ne | Ne |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty / eventGridFilters | Ne | Ne |
> | účty / firewallRules | Ne | Ne |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | services | Ne | Ne |
> | služby / projekty | Ne | Ne |

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty / akcie | Ne | Ne |
> | účty / akcie / datové soubory | Ne | Ne |
> | účty / akcie / pozvánky | Ne | Ne |
> | účty / akcie / providersharesubscriptions | Ne | Ne |
> | účty / akcie / synchronizaceNastavení | Ne | Ne |
> | účty / sharesubscriptions | Ne | Ne |
> | účty / sharesubscriptions / consumerSourceDataSets | Ne | Ne |
> | účty / sharesubscriptions / datasetmappings | Ne | Ne |
> | účty / sharesubscriptions / spouští | Ne | Ne |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Servery | Ano | Ano |
> | servery / poradci | Ne | Ne |
> | servery / klíče | Ne | Ne |
> | servery / privátníEndpointConnectionProxies | Ne | Ne |
> | servery / privateEndpointConnections | Ne | Ne |
> | servery / privateLinkResources | Ne | Ne |
> | servery / queryTexty | Ne | Ne |
> | servery / obnovitelnéServery | Ne | Ne |
> | servery / topQueryStatistics | Ne | Ne |
> | servery / virtualNetworkRules | Ne | Ne |
> | servery / waitStatistics | Ne | Ne |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Servery | Ano | Ano |
> | servery / poradci | Ne | Ne |
> | servery / klíče | Ne | Ne |
> | servery / privátníEndpointConnectionProxies | Ne | Ne |
> | servery / privateEndpointConnections | Ne | Ne |
> | servery / privateLinkResources | Ne | Ne |
> | servery / queryTexty | Ne | Ne |
> | servery / obnovitelnéServery | Ne | Ne |
> | servery / topQueryStatistics | Ne | Ne |
> | servery / virtualNetworkRules | Ne | Ne |
> | servery / waitStatistics | Ne | Ne |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | serverové skupiny | Ano | Ano |
> | Servery | Ano | Ano |
> | servery / poradci | Ne | Ne |
> | servery / klíče | Ne | Ne |
> | servery / privátníEndpointConnectionProxies | Ne | Ne |
> | servery / privateEndpointConnections | Ne | Ne |
> | servery / privateLinkResources | Ne | Ne |
> | servery / queryTexty | Ne | Ne |
> | servery / obnovitelnéServery | Ne | Ne |
> | servery / topQueryStatistics | Ne | Ne |
> | servery / virtualNetworkRules | Ne | Ne |
> | servery / waitStatistics | Ne | Ne |
> | serveryv2 | Ano | Ano |
> | singleServers | Ano | Ano |

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | artefaktzdroje | Ano | Ano |
> | zavedení | Ano | Ano |
> | serviceTopologie | Ano | Ano |
> | serviceTopologie / služby | Ano | Ano |
> | serviceTopologie / služby / serviceUnits | Ano | Ano |
> | kroky | Ano | Ano |

## <a name="microsoftdesktopvirtualization"></a>Microsoft.DesktopVirtualization

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aplikační skupiny | Ano | Ano |
> | aplikační skupiny / aplikace | Ne | Ne |
> | aplikační skupiny / stolní počítače | Ne | Ne |
> | aplikační skupiny / položky startmenu | Ne | Ne |
> | hostitelských poolů | Ano | Ano |
> | hostpools / sessionhosts | Ne | Ne |
> | hostpools / sessionhosts / usersessions | Ne | Ne |
> | hostpools / usersessions | Ne | Ne |
> | pracovní prostory | Ano | Ano |

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Elastické bazény | Ano | Ano |
> | ElasticPools / IotHubTenants | Ano | Ano |
> | ElasticPools / IotHubTenants / securitySettings | Ne | Ne |
> | IotHubs | Ano | Ano |
> | IotHubs / eventGridFilters | Ne | Ne |
> | IotHubs / zabezpečeníNastavení | Ne | Ne |
> | Zřizování služeb | Ano | Ano |
> | Použití | Ne | Ne |

## <a name="microsoftdevops"></a>Microsoft.DevOps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Potrubí | Ano | Ano |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | kontrolery | Ano | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | centra laboratoře | Ano | Ano |
> | Labs | Ano | Ano |
> | laboratoře / prostředí | Ano | Ano |
> | laboratoře / servisBěžci | Ano | Ano |
> | laboratoře / virtualMachines | Ano | Ano |
> | Plány | Ano | Ano |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | databaseAccountNames | Ne | Ne |
> | databaseAccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Domény | Ano | Ano |
> | domény / domainOwnershipIdentifiers | Ne | Ne |
> | generateSsoRequest | Ne | Ne |
> | topLevelDomains | Ne | Ne |
> | validateDomainRegistrationInformation | Ne | Ne |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | lcsprojekty | Ne | Ne |
> | lcsprojects / clouddeployments lcsprojects / clouddeployments lcsprojects / clouddeployments lcs | Ne | Ne |
> | lcsprojects / konektory | Ne | Ne |

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | services | Ano | Ano |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Domény | Ano | Ano |
> | domény / témata | Ne | Ne |
> | eventSubscriptions | Ne | Ne |
> | extensionTopics | Ne | Ne |
> | obory_názvů partnerů | Ano | Ano |
> | partnerNamespaces / eventChannels | Ne | Ne |
> | registrace partnerů | Ano | Ano |
> | partnerTémata | Ano | Ano |
> | partnerTémata / eventOdběry | Ne | Ne |
> | systemTopics system | Ano | Ano |
> | systemTémata / eventOdběry | Ne | Ne |
> | Témata | Ano | Ano |
> | topicTypes | Ne | Ne |

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Clustery | Ano | Ano |
> | Obory názvů | Ano | Ano |
> | obory názvů / autorizační pravidla | Ne | Ne |
> | obory názvů / disasterrecoveryconfigs | Ne | Ne |
> | jmenné prostory / eventhubs | Ne | Ne |
> | obory názvů / eventhubs / autorizační pravidla | Ne | Ne |
> | jmenné prostory / eventhubs / spotřebitelské skupiny | Ne | Ne |
> | obory názvů / sady síťových pravidel | Ne | Ne |

## <a name="microsoftfalcon"></a>Microsoft.Falcon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Obory názvů | Ano | Ano |

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | featureProviders | Ne | Ne |
> | funkce | Ne | Ne |
> | Poskytovatelů | Ne | Ne |
> | subscriptionFeatureRegistrations | Ne | Ne |

## <a name="microsoftgallery"></a>Microsoft.Galerie

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Zapsat | Ne | Ne |
> | galerijní položky | Ne | Ne |
> | generateartifactaccessuri | Ne | Ne |
> | myareas | Ne | Ne |
> | myareas / oblasti | Ne | Ne |
> | myareas / oblasti / oblasti | Ne | Ne |
> | myareas / oblasti / oblasti / galeriepoložky | Ne | Ne |
> | myareas / oblasti / galeriepoložky | Ne | Ne |
> | myareas / galeriepoložky | Ne | Ne |
> | register | Ne | Ne |
> | resources | Ne | Ne |
> | načíst prostředkybyid | Ne | Ne |

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | autospravované účty | Ano | Ano |
> | autoManagedVmConfigurationProfiles | Ano | Ano |
> | konfiguracePřiřazení profilů | Ne | Ne |
> | guestConfigurationAssignments | Ne | Ne |
> | Software | Ne | Ne |
> | softwareUpdateProfile | Ne | Ne |
> | softwareAktualizace | Ne | Ne |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hanaInstances | Ano | Ano |
> | sapMonitory | Ano | Ano |

## <a name="microsofthardwaresecuritymodules"></a>Moduly Microsoft.HardwareSecurityModules

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | specializované HSM | Ano | Ano |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Clustery | Ano | Ano |
> | clustery / aplikace | Ne | Ne |

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | services | Ano | Ano |

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Stroje | Ano | Ano |
> | stroje / rozšíření | Ano | Ano |

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | dataManažeři | Ano | Ano |

## <a name="microsofthydra"></a>Microsoft.Hydra

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Součásti | Ano | Ano |
> | networkScopes | Ano | Ano |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Úlohy | Ano | Ano |

## <a name="microsoftinsights"></a>Microsoft.Insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | skupiny akcí | Ano | Ano |
> | activityLogAlerts | Ano | Ano |
> | pravidla výstrah | Ano | Ano |
> | nastavení automatického škálování | Ano | Ano |
> | Součásti | Ano | Ano |
> | komponenty / linkedStorageAccounts | Ne | Ne |
> | komponenty / ProactiveDetectionConfigs | Ne | Ne |
> | diagnosticSettings | Ne | Ne |
> | guestDiagnosticSettings | Ano | Ano |
> | guestDiagnosticSettingsAssociation | Ano | Ano |
> | logprofiles | Ano | Ano |
> | metricAlerts | Ano | Ano |
> | privátnílinkové obory | Ano | Ano |
> | privátníLinkScopes / privateEndpointConnections | Ne | Ne |
> | privateLinkScopes / oborZdroje | Ne | Ne |
> | queryPacks | Ano | Ano |
> | queryPacks / dotazy | Ne | Ne |
> | scheduledQueryRules | Ano | Ano |
> | webové testy | Ano | Ano |
> | sešity | Ano | Ano |
> | šablony sešitů | Ano | Ano |

## <a name="microsoftintune"></a>Microsoft.Intune

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Ne | Ne |
> | diagnosticSettingsCategories | Ne | Ne |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | appTemplates | Ne | Ne |
> | IoTApps | Ano | Ano |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Graph | Ano | Ano |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | odstraněné trezory | Ne | Ne |
> | hsmPooly | Ano | Ano |
> | Klenby | Ano | Ano |
> | trezory / přístupPolitiky | Ne | Ne |
> | trezory / eventGridFilters | Ne | Ne |
> | trezory / tajemství | Ne | Ne |

## <a name="microsoftkubernetes"></a>Microsoft.Kubernetes

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | připojené clustery | Ano | Ano |

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Clustery | Ano | Ano |
> | clustery / připojenékonfigurace databází | Ne | Ne |
> | clustery / databáze | Ne | Ne |
> | clustery / databáze / datová připojení | Ne | Ne |
> | clustery / databáze / eventhubconnections | Ne | Ne |
> | clustery / databáze / hlavní úkoly | Ne | Ne |
> | clustery / datová připojení | Ne | Ne |
> | clustery / principalassignments | Ne | Ne |
> | clustery / sdílené identity | Ne | Ne |

## <a name="microsoftlabservices"></a>Služby Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | laboratorní účty | Ano | Ano |
> | uživatelé | Ne | Ne |

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hostingProstředí | Ano | Ano |
> | integraceÚčty | Ano | Ano |
> | integrationServiceEnvironments | Ano | Ano |
> | integrationServiceEnvironments / managedApis | Ano | Ano |
> | izolované Prostředí | Ano | Ano |
> | Pracovní postupy | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | commitmentPlans | Ano | Ano |
> | Webservices | Ano | Ano |
> | Pracovní prostory | Ano | Ano |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | pracovní prostory | Ano | Ano |
> | pracovní prostory / počítá | Ne | Ne |
> | pracovní prostory / eventGridFilters | Ne | Ne |

## <a name="microsoftmaintenance"></a>Microsoft.Údržba

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applyUpdates | Ne | Ne |
> | konfiguraceÚlohy | Ne | Ne |
> | údržbaKonfigurace | Ano | Ano |
> | Aktualizace | Ne | Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Identit | Ne | Ne |
> | userAssignedIdentities | Ano | Ano |

## <a name="microsoftmanagedservices"></a>Služby Microsoft.ManagedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | marketplaceRegistrationDefinitions | Ne | Ne |
> | registraceÚkoly | Ne | Ne |
> | registraceDefinice | Ne | Ne |

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | getEntities | Ne | Ne |
> | managementSkupiny | Ne | Ne |
> | managementGroups / nastavení | Ne | Ne |
> | resources | Ne | Ne |
> | startTenantBackfill | Ne | Ne |
> | tenantBackfillStatus | Ne | Ne |

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |
> | účty / eventGridFilters | Ne | Ne |
> | účty / privátNíAtlasy | Ano | Ano |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Nabízí | Ne | Ne |
> | offerTypes | Ne | Ne |
> | nabídkaTypy / vydavatelé | Ne | Ne |
> | nabídkaTypy / vydavatelé / nabídky | Ne | Ne |
> | nabídkaTypy / vydavatelé / nabídky / plány | Ne | Ne |
> | nabídkaTypy / vydavatelé / nabídky / plány / dohody | Ne | Ne |
> | offerTypes / vydavatelé / nabídky / plány / configs | Ne | Ne |
> | offerTypes / vydavatelé / nabídky / plány / configs / importImage | Ne | Ne |
> | privategalleryitems | Ne | Ne |
> | privátní klient | Ne | Ne |
> | privátní obchody | Ne | Ne |
> | privátní obchody / nabídky | Ne | Ne |
> | Produkty | Ne | Ne |
> | Vydavatelů | Ne | Ne |
> | vydavatelé / nabídky | Ne | Ne |
> | vydavatelé / nabídky / změny | Ne | Ne |
> | register | Ne | Ne |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | classicDevServices | Ano | Ano |
> | updateCommunicationPreference | Ne | Ne |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Dohody | Ne | Ne |
> | typy nabídek | Ne | Ne |

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | mediální služby | Ano | Ano |
> | mediaservices / accountFilters | Ne | Ne |
> | mediální služby / datové zdroje | Ne | Ne |
> | mediaservices / asset / assetFilters | Ne | Ne |
> | mediaservices / contentKeyPolicies | Ne | Ne |
> | mediaservices / eventGridFilters | Ne | Ne |
> | mediaservices / liveEventOperations | Ne | Ne |
> | mediaservices / liveEvents | Ano | Ano |
> | mediaservices / liveEvents / liveOutputs | Ne | Ne |
> | mediaservices / liveOutputOperations | Ne | Ne |
> | mediaservices / mediaGraphs | Ne | Ne |
> | mediaservices / streamingEndpointOperations | Ne | Ne |
> | mediaservices / streamingKoncové body | Ano | Ano |
> | mediaservices / streamingLokátory | Ne | Ne |
> | mediaservices / streamingPolicies | Ne | Ne |
> | mediaservices / transformuje | Ne | Ne |
> | mediaservices / transformuje / pracovní místa | Ne | Ne |

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | appClustery | Ano | Ano |

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hodnoceníProjekty | Ano | Ano |
> | migrateprojects | Ano | Ano |
> | moveCollections | Ano | Ano |
> | Projekty | Ano | Ano |

## <a name="microsoftmixedreality"></a>Microsoft.MixedReality

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | holographicsBroadcastAccounts | Ano | Ano |
> | objectUnderstandingAccounts | Ano | Ano |
> | vzdálené účty Rendering | Ano | Ano |
> | spatialAnchorsAccounts | Ano | Ano |

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | netAppAccounts | Ano | Ne |
> | netAppAccounts / accountBackups | Ne | Ne |
> | netAppAccounts / capacityPools | Ano | Ne |
> | netAppAccounts / capacityPool / svazky | Ano | Ne |
> | netAppAccounts / capacityPool / svazky / snímky | Ne | Ne |

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applicationGateways | Ano | Ano |
> | aplikaceGatewayWebApplicationFirewallPolicies | Ano | Ano |
> | applicationSecurityGroups | Ano | Ano |
> | azureFirewallFqdnTagy | Ne | Ne |
> | azureFirewalls | Ano | Ne |
> | bastionHosts | Ano | Ne |
> | bGPServiceCommunities | Ne | Ne |
> | Připojení | Ano | Ano |
> | ddosCustomPolicies | Ano | Ano |
> | ddosProtectionPlans | Ano | Ano |
> | dnsOperationStaves | Ne | Ne |
> | dnszóny | Ano | Ano |
> | dnszones / A | Ne | Ne |
> | dnszones / AAAA | Ne | Ne |
> | dnszones / vše | Ne | Ne |
> | dnszones / CAA | Ne | Ne |
> | dnszones / CNAME | Ne | Ne |
> | dnszones / MX | Ne | Ne |
> | dnszones / NS | Ne | Ne |
> | dnszones / PTR | Ne | Ne |
> | dnszones / sady záznamů | Ne | Ne |
> | dnszones / SOA | Ne | Ne |
> | dnszones / SRV | Ne | Ne |
> | dnszones / TXT | Ne | Ne |
> | expressRouteCircuits | Ano | Ano |
> | expressRouteCrossConnections | Ano | Ano |
> | expressRouteGateways | Ano | Ano |
> | expressRoutePorts | Ano | Ano |
> | expressRouteServiceProviders | Ne | Ne |
> | firewallZásady | Ano | Ano |
> | přední dveře | Ano, ale omezené (viz [poznámka níže)](#frontdoor) | Ano |
> | frontdoorWebApplicationFirewallManagedSady pravidel | Ano, ale omezené (viz [poznámka níže)](#frontdoor) | Ne |
> | frontdoorWebApplicationFirewallPolicies | Ano, ale omezené (viz [poznámka níže)](#frontdoor) | Ano |
> | getDnsResourceReference | Ne | Ne |
> | internalNotify | Ne | Ne |
> | vynakládače | Ano | Ano |
> | localNetworkGateways | Ano | Ano |
> | natGateways | Ano | Ano |
> | networkIntentPolicies | Ano | Ano |
> | síťová rozhraní | Ano | Ano |
> | networkProfiles | Ano | Ano |
> | síťSecuritygroups | Ano | Ano |
> | networkWatchers | Ano | Ano |
> | networkWatchers / connectionMonitory | Ano | Ne |
> | networkWatchers / flowLogs | Ne | Ne |
> | networkWatchers / objektivy | Ano | Ne |
> | networkWatchers / pingMeshes | Ano | Ne |
> | p2sVpnBrány | Ano | Ano |
> | privateDnsOperationStaves | Ne | Ne |
> | privátnídnszóny | Ano | Ano |
> | privateDnsZones / A | Ne | Ne |
> | privateDnsZones / AAAA | Ne | Ne |
> | privateDnsZones / vše | Ne | Ne |
> | privateDnsZones / CNAME | Ne | Ne |
> | privateDnsZones / MX | Ne | Ne |
> | privateDnsZones / PTR | Ne | Ne |
> | privateDnsZones / SOA | Ne | Ne |
> | privateDnsZones / SRV | Ne | Ne |
> | privateDnsZones / TXT | Ne | Ne |
> | privateDnsZones / virtualNetworkLinks | Ano | Ano |
> | privateEndpoints | Ano | Ano |
> | privateLinkServices | Ano | Ano |
> | publicIPAdresy | Ano | Ano |
> | publicIPPrefixes | Ano | Ano |
> | routeFilters | Ano | Ano |
> | routeTables | Ano | Ano |
> | serviceEndpointPolicies | Ano | Ano |
> | trafficManagerGeographicHierarchie | Ne | Ne |
> | trafficmanagerprofiles | Ano | Ano |
> | trafficmanagerprofiles/heatMaps | Ne | Ne |
> | trafficManagerUserMetricsKeys | Ne | Ne |
> | virtuálníhuby | Ano | Ano |
> | virtualNetworkGateways | Ano | Ano |
> | virtualNetworks | Ano | Ano |
> | virtualNetworkKohouty | Ano | Ano |
> | virtualWans | Ano | Ne |
> | vpnBrány | Ano | Ano |
> | vpnWe | Ano | Ano |
> | webApplicationFirewallPolicies | Ano | Ano |

<a id="frontdoor" />

> [!NOTE]
> Pro službu Azure Front Door Service můžete při vytváření prostředku použít značky, ale aktualizace nebo přidání značek není aktuálně podporováno.


## <a name="microsoftnotebooks"></a>Microsoft.Poznámkové bloky

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Notebookproxies | Ne | Ne |

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Obory názvů | Ano | Ne |
> | jmenné prostory / notificationHubs | Ano | Ne |

## <a name="microsoftobjectstore"></a>Úložiště Microsoft.ObjectStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | osNamespaces | Ano | Ano |

## <a name="microsoftoffazure"></a>Microsoft.OffAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | HyperVSites | Ano | Ano |
> | Dovozní weby | Ano | Ano |
> | Serverové weby | Ano | Ano |
> | Weby VMware | Ano | Ano |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Clustery | Ano | Ano |
> | propojit cíle | Ne | Ne |
> | storageInsightConfigs | Ne | Ne |
> | pracovní prostory | Ano | Ano |
> | pracovní prostory / dataExport | Ne | Ne |
> | pracovní prostory / dataZdroje | Ne | Ne |
> | pracovní prostory / linkedServices | Ne | Ne |
> | pracovní prostory / propojenéstorageaccounts | Ne | Ne |
> | pracovní prostory / dotaz | Ne | Ne |
> | pracovní prostory / oborPrivateLinkProxies | Ne | Ne |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | managementassociations | Ne | Ne |
> | konfigurace správy | Ano | Ano |
> | Řešení | Ano | Ano |
> | zobrazení | Ano | Ano |

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | starší partnerské společnosti | Ne | Ne |
> | peerAsns | Ne | Ne |
> | peeringy | Ano | Ano |
> | peeringServiceCountries | Ne | Ne |
> | zprostředkovatelé služby partnerského vztahu | Ne | Ne |
> | služby peeringServices | Ano | Ano |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | policyEvents | Ne | Ne |
> | policyMetadata | Ne | Ne |
> | policyStates | Ne | Ne |
> | policyTrackedResources | Ne | Ne |
> | náprava | Ne | Ne |

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Konzoly nástroje | Ne | Ne |
> | řídicí panely | Ano | Ano |
> | userSettings | Ne | Ne |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | kolekce pracovního prostoru | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Kapacity | Ano | Ano |

## <a name="microsoftprojectbabylon"></a>Microsoft.ProjectBabylon

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ano | Ano |

## <a name="microsoftquantum"></a>Microsoft.Quantum

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Pracovní prostory | Ano | Ano |

## <a name="microsoftrecoveryservices"></a>Služby Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | backupProtectedItems | Ne | Ne |
> | Klenby | Ano | Ano |

## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Obory názvů | Ano | Ano |
> | obory názvů / autorizační pravidla | Ne | Ne |
> | jmenné prostory / hybridní připojení | Ne | Ne |
> | obory názvů / hybridconnections / authorizationrules | Ne | Ne |
> | jmenné prostory / wcfrelays | Ne | Ne |
> | jmenné prostory / wcfrelays / autorizační pravidla | Ne | Ne |

## <a name="microsoftremoteapp"></a>Aplikace Microsoft.RemoteApp

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | accounts | Ne | Ne |
> | Sbírky | Ano | Ano |
> | sbírky / aplikace | Ne | Ne |
> | kolekce / securityprincipals | Ne | Ne |
> | templateImages | Ne | Ne |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Dotazy | Ano | Ano |
> | resourceChangeDetails | Ne | Ne |
> | resourceChanges | Ne | Ne |
> | resources | Ne | Ne |
> | resourcesHistory | Ne | Ne |
> | subscriptionsStatus | Ne | Ne |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | availabilityStavy | Ne | Ne |
> | childAvailabilityStatuses | Ne | Ne |
> | childResources | Ne | Ne |
> | vznikající problémy | Ne | Ne |
> | stránka events | Ne | Ne |
> | impactedZdroje | Ne | Ne |
> | zprostředkovatele identity | Ne | Ne |
> | Oznámení | Ne | Ne |

## <a name="microsoftresources"></a>Microsoft.Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Nasazení | Ano | Ne |
> | nasazení / provoz | Ne | Ne |
> | nasazeníScripts | Ano | Ano |
> | deploymentScripts / logs | Ne | Ne |
> | Odkazy | Ne | Ne |
> | upozornitúlohy | Ne | Ne |
> | Poskytovatelů | Ne | Ne |
> | skupiny prostředků | Ano | Ne |
> | Odběry | Ano | Ne |
> | Nájemníky | Ne | Ne |

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aplikace | Ano | Ano |
> | saaszdroje | Ne | Ne |

## <a name="microsoftsearch"></a>Microsoft.Hledat

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | resourceHealthMetadata | Ne | Ne |
> | searchServices | Ano | Ano |

## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | adaptivníNetworkHardenings | Ne | Ne |
> | advancedThreatProtectionSettings | Ne | Ne |
> | výstrahy | Ne | Ne |
> | allowedConnections | Ne | Ne |
> | applicationWhitelistings | Ne | Ne |
> | hodnoceníMetadata | Ne | Ne |
> | hodnocení | Ne | Ne |
> | autoDismissAlertsRules | Ne | Ne |
> | automatizace | Ano | Ano |
> | Nastavení automatického zřízení | Ne | Ne |
> | Dodržování předpisů | Ne | Ne |
> | dataCollectionAgents | Ne | Ne |
> | deviceSecurityGroups | Ne | Ne |
> | objevilSecuritySolutions | Ne | Ne |
> | externíbezpečnostní řešení | Ne | Ne |
> | Zásady informační ochrany | Ne | Ne |
> | Řešení iotSecuritySolutions | Ano | Ano |
> | iotSecuritySolutions / analyticsModely | Ne | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedAlerts | Ne | Ne |
> | iotSecuritySolutions / analyticsModels / aggregatedRecommendations | Ne | Ne |
> | jitNetworkAccessPolicies | Ne | Ne |
> | networkData | Ne | Ne |
> | Zásady | Ne | Ne |
> | ceny | Ne | Ne |
> | regulační standardy compliance | Ne | Ne |
> | regulační compliancestandardy / regulační compliancecontrols | Ne | Ne |
> | regulační compliancestandardy / regulační compliancecontrols / regulační complianceassessments | Ne | Ne |
> | secureScoreControlDefinitions | Ne | Ne |
> | secureScoreControls | Ne | Ne |
> | secureScores | Ne | Ne |
> | secureScores / secureScoreControls | Ne | Ne |
> | securityKontakty | Ne | Ne |
> | bezpečnostní řešení | Ne | Ne |
> | securitySolutionsReferenceData | Ne | Ne |
> | securityStatuses | Ne | Ne |
> | securityStatusesSouhrny | Ne | Ne |
> | serverAssessments | Ne | Ne |
> | settings | Ne | Ne |
> | dílčí hodnocení | Ne | Ne |
> | úlohy | Ne | Ne |
> | Topologie | Ne | Ne |
> | nastavení pracovního prostoru | Ne | Ne |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Ne | Ne |
> | diagnosticSettingsCategories | Ne | Ne |

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Agregace | Ne | Ne |
> | alertRules | Ne | Ne |
> | alertRuleTemplates | Ne | Ne |
> | Záložky | Ne | Ne |
> | Případech | Ne | Ne |
> | datové konektory | Ne | Ne |
> | dataConnectorsCheckRequirements | Ne | Ne |
> | Entity | Ne | Ne |
> | entityDotazy | Ne | Ne |
> | Incidenty | Ne | Ne |
> | officeConsents | Ne | Ne |
> | settings | Ne | Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Obory názvů | Ano | Ano |
> | obory názvů / autorizační pravidla | Ne | Ne |
> | obory názvů / disasterrecoveryconfigs | Ne | Ne |
> | obory názvů / eventgridfilters | Ne | Ne |
> | obory názvů / sady síťových pravidel | Ne | Ne |
> | jmenné prostory / fronty | Ne | Ne |
> | obory názvů / fronty / autorizační pravidla | Ne | Ne |
> | jmenné prostory / témata | Ne | Ne |
> | jmenné prostory / témata / autorizační pravidla | Ne | Ne |
> | jmenovky / témata / odběry | Ne | Ne |
> | jmenovky / témata / odběry / pravidla | Ne | Ne |
> | premiumMessagingRegiony | Ne | Ne |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aplikace | Ano | Ano |
> | Clustery | Ano | Ano |
> | clustery / aplikace | Ne | Ne |
> | containerGroups | Ano | Ano |
> | containerGroupSets | Ano | Ano |
> | okrajové clustery | Ano | Ano |
> | edgeclusters / aplikace | Ne | Ne |
> | spravované clustery | Ano | Ano |
> | spravované clustery / nodetypy | Ne | Ne |
> | Sítí | Ano | Ano |
> | tajné obchody | Ano | Ano |
> | tajné obchody / certifikáty | Ne | Ne |
> | tajné obchody / tajemství | Ne | Ne |
> | volumes | Ano | Ano |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | aplikace | Ano | Ano |
> | containerGroups | Ano | Ano |
> | Brány | Ano | Ano |
> | Sítí | Ano | Ano |
> | Tajemství | Ano | Ano |
> | volumes | Ano | Ano |

## <a name="microsoftservices"></a>Microsoft.Services

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | providerRegistrace | Ne | Ne |
> | providerRegistrations / resourceTypeRegistrations | Ne | Ne |
> | zavedení | Ano | Ano |

## <a name="microsoftsignalrservice"></a>Služba Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | SignalR | Ano | Ano |
> | SignalR / eventGridFilters | Ne | Ne |

## <a name="microsoftsiterecovery"></a>Microsoft.SiteRecovery

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Úložiště SiteRecoveryVault | Ano | Ano |

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | hybridUseBenefits | Ne | Ne |

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | applicationDefinitions | Ano | Ano |
> | aplikace | Ano | Ano |
> | jitRequests | Ano | Ano |

## <a name="microsoftspoolservice"></a>Služba Microsoft.SpoolService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | registeredSubscriptions | Ne | Ne |
> | Cívky | Ano | Ano |


## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | spravované instance | Ano | Ano |
> | managedInstances / databáze | Ano (viz [poznámka níže)](#sqlnote) | Ano |
> | managedInstances / databáze / backupShortTermRetentionPolicies | Ne | Ne |
> | managedInstances / databáze / schémata / tabulky / sloupce / citlivostLabels | Ne | Ne |
> | managedInstance / databáze / hodnocení zranitelnosti | Ne | Ne |
> | managedInstance / databáze / zranitelnostAssessments / pravidla / základní | Ne | Ne |
> | managedInstances / encryptionProtector | Ne | Ne |
> | managedInstances / klíče | Ne | Ne |
> | managedInstances / restorableDroppedDatabases / backupShortTermRetentionPolicies | Ne | Ne |
> | managedInstances / vulnerabilityAssessments | Ne | Ne |
> | Servery | Ano | Ano |
> | servery / správci | Ne | Ne |
> | servery / komunikačníOdkazy | Ne | Ne |
> | servery / databáze | Ano (viz [poznámka níže)](#sqlnote) | Ano |
> | servery / encryptionProtector | Ne | Ne |
> | servery / firewallRules | Ne | Ne |
> | servery / klíče | Ne | Ne |
> | servery / restorableDroppedDatabáze | Ne | Ne |
> | servery / servisní cíle | Ne | Ne |
> | servery / tdeCertificates | Ne | Ne |
> | virtuální clustery | Ne | Ne |

<a id="sqlnote" />

> [!NOTE]
> Hlavní databáze nepodporuje značky, ale jiné databáze, včetně databází Azure SQL Data Warehouse, podporují značky. Databáze Datového skladu Azure SQL musí být ve stavu Aktivní (ne pozastaveno).

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

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
> | storageAccounts | Ano | Ano |
> | storageAccounts / blobServices | Ne | Ne |
> | storageAccounts / fileServices | Ne | Ne |
> | storageAccounts / queueServices | Ne | Ne |
> | úložištěÚčty / služby | Ne | Ne |
> | storageAccounts / služby / metricDefinitions | Ne | Ne |
> | storageAccounts / tableServices | Ne | Ne |
> | Použití | Ne | Ne |

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Mezipaměti | Ano | Ano |
> | mezipaměti / storageTargets | Ne | Ne |
> | usageModely | Ne | Ne |

## <a name="microsoftstoragereplication"></a>Microsoft.StorageReplication

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | replikační skupiny | Ne | Ne |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ano | Ano |
> | storageSyncServices / registeredServers | Ne | Ne |
> | storageSyncServices / syncGroups | Ne | Ne |
> | storageSyncServices / syncGroups / cloudKoncové body | Ne | Ne |
> | storageSyncServices / syncGroups / serverKoncové body | Ne | Ne |
> | storageSyncServices / pracovní postupy | Ne | Ne |

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ano | Ano |
> | storageSyncServices / registeredServers | Ne | Ne |
> | storageSyncServices / syncGroups | Ne | Ne |
> | storageSyncServices / syncGroups / cloudKoncové body | Ne | Ne |
> | storageSyncServices / syncGroups / serverKoncové body | Ne | Ne |
> | storageSyncServices / pracovní postupy | Ne | Ne |

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | storageSyncServices | Ano | Ano |
> | storageSyncServices / registeredServers | Ne | Ne |
> | storageSyncServices / syncGroups | Ne | Ne |
> | storageSyncServices / syncGroups / cloudKoncové body | Ne | Ne |
> | storageSyncServices / syncGroups / serverKoncové body | Ne | Ne |
> | storageSyncServices / pracovní postupy | Ne | Ne |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Manažeři | Ano | Ano |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | streamováníúlohy | Ano (viz poznámka níže) | Ano |

> [!NOTE]
> Značku nelze přidat, když jsou spuštěny úlohy streamování. Zastavte zdroj pro přidání značky.

## <a name="microsoftsubscription"></a>Microsoft.Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | cancel | Ne | Ne |
> | Createsubscription | Ne | Ne |
> | Povolit | Ne | Ne |
> | přejmenovat | Ne | Ne |
> | Definice předplatného | Ne | Ne |
> | PředplatnéOperace | Ne | Ne |
> | Odběry | Ne | Ne |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Prostředí | Ano | Ne |
> | prostředí / přístupPolitiky | Ne | Ne |
> | prostředí / eventsources | Ano | Ne |
> | prostředí / referenceDataSets | Ano | Ne |

## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudJednoduché

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | dedicatedCloudNodes | Ano | Ano |
> | dedicatedCloudServices | Ano | Ano |
> | virtualMachines | Ano | Ano |

## <a name="microsoftvnfmanager"></a>Microsoft.VnfManager

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | zařízení | Ano | Ano |
> | registeredSubscriptions | Ne | Ne |
> | Dodavatelů | Ne | Ne |
> | prodejci / skus | Ne | Ne |
> | prodejci / vnfs | Ne | Ne |
> | virtualNetworkFunctionSkus | Ne | Ne |
> | vnfs | Ano | Ano |

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | apiManagementAccounts | Ne | Ne |
> | apiManagementAccounts / apiAcls | Ne | Ne |
> | apiManagementAccounts / apis | Ne | Ne |
> | apiManagementAccounts / api / apiAcls | Ne | Ne |
> | apiManagementAccounts / apis / connectionAcls | Ne | Ne |
> | apiManagementAccounts / apis / připojení | Ne | Ne |
> | apiManagementAccounts / apis / připojení / připojeníAcls | Ne | Ne |
> | apiManagementAccounts / apis / lokalizovanéDefinice | Ne | Ne |
> | apiManagementAccounts / connectionAcls | Ne | Ne |
> | apiManagementAccounts / připojení | Ne | Ne |
> | fakturační měřiče | Ne | Ne |
> | certifikáty | Ano | Ano |
> | connectionGateways | Ano | Ano |
> | Připojení | Ano | Ano |
> | vlastníApis | Ano | Ano |
> | smazané weby | Ne | Ne |
> | hostingProstředí | Ano | Ano |
> | hostingEnvironments / eventGridFilters | Ne | Ne |
> | hostingEnvironments / multiRolePools | Ne | Ne |
> | hostingEnvironments / workerPools | Ne | Ne |
> | kubeŽivotní prostředí | Ano | Ano |
> | publikováníUživatelé | Ne | Ne |
> | Doporučení | Ne | Ne |
> | resourceHealthMetadata | Ne | Ne |
> | Runtime | Ne | Ne |
> | serverFarms | Ano | Ano |
> | serverFarms / eventGridFilters | Ne | Ne |
> | Stránky | Ano | Ano |
> | stránky / config  | Ne | Ne |
> | weby / eventGridFilters | Ne | Ne |
> | weby / hostNameBindings | Ne | Ne |
> | stránky / networkConfig | Ne | Ne |
> | stránky / premieraddons | Ano | Ano |
> | stránky / sloty | Ano | Ano |
> | stránky / sloty / eventGridFilters | Ne | Ne |
> | stránky / sloty / hostNameBindings | Ne | Ne |
> | stránky / sloty / networkConfig | Ne | Ne |
> | sourceControls | Ne | Ne |
> | statickéWeby | Ano | Ano |
> | Ověřit | Ne | Ne |
> | verifyHostingEnvironmentVnet | Ne | Ne |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | diagnosticSettings | Ne | Ne |
> | diagnosticSettingsCategories | Ne | Ne |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Služby DeviceServices | Ano | Ano |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
> | ------------- | ----------- | ----------- |
> | Součásti | Ne | Ne |
> | componentsSummary | Ne | Ne |
> | monitorInstances | Ne | Ne |
> | monitorInstancesSummary | Ne | Ne |
> | Monitory | Ne | Ne |
> | notificationSettings | Ne | Ne |

## <a name="next-steps"></a>Další kroky

Informace o tom, jak použít značky na prostředky, najdete [v tématu Použití značek k uspořádání prostředků Azure](tag-resources.md).

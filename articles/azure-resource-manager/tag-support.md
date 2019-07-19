---
title: Podpora značek Azure Resource Manager pro prostředky
description: Zobrazuje, které typy prostředků Azure podporují značky. Poskytuje podrobnosti pro všechny služby Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 72bb11cd064c90c2bbe1e9e6452dcbf07fe37817
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68304874"
---
# <a name="tag-support-for-azure-resources"></a>Podpora značek pro prostředky Azure
Tento článek popisuje, zda typ prostředku podporuje [značky](resource-group-using-tags.md). Sloupec s popisem **podporuje značky** označuje, zda typ prostředku má vlastnost pro značku. Sloupec s označením **značka v sestavě náklady** označuje, zda tento typ prostředku předá značku k sestavě nákladů.

Pokud chcete získat stejná data jako soubor hodnot oddělených čárkami, Stáhněte si [tag-support. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| DomainServices | Ano | Ano |
| DomainServices/oucontainer | Ne | Ne |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Ne |  Ne |
| diagnosticSettingsCategories | Ne |  Ne |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| supportProviders | Ne |  Ne |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| aadsupportcases | Ne |  Ne |
| addsservices | Ne |  Ne |
| Technici | Ne |  Ne |
| anonymousapiusers | Ne |  Ne |
| konfigurace | Ne |  Ne |
| logs | Ne |  Ne |
| sestavy | Ne |  Ne |
| services | Ne |  Ne |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Konfiguračních | Ne |  Ne |
| generateRecommendations | Ne |  Ne |
| Doporučení | Ne |  Ne |
| potlačení | Ne |  Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| actionRules | Ne |  Ne |
| výstrahy | Ne |  Ne |
| alertsList | Ne |  Ne |
| alertsSummary | Ne |  Ne |
| alertsSummaryList | Ne |  Ne |
| smartDetectorAlertRules | Ne |  Ne |
| smartDetectorRuntimeEnvironments | Ne |  Ne |
| smartGroups | Ne |  Ne |

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| servers | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| reportFeedback | Ne |  Ne |
| service | Ano | Ano |
| validateServiceName | Ne |  Ne |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| attestationProviders | Ne |  Ne |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| classicAdministrators | Ne |  Ne |
| denyAssignments | Ne |  Ne |
| elevateAccess | Ne |  Ne |
| Počtu | Ne |  Ne |
| oprávnění | Ne |  Ne |
| policyAssignments | Ne |  Ne |
| policyDefinitions | Ne |  Ne |
| policySetDefinitions | Ne |  Ne |
| providerOperations | Ne |  Ne |
| roleAssignments | Ne |  Ne |
| roleDefinitions | Ne |  Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| automationAccounts | Ano | Ano |
| automationAccounts/konfigurace | Ano | Ano |
| automationAccounts/jobs | Ne |  Ne |
| automationAccounts/runbooks | Ano | Ano |
| automationAccounts/softwareUpdateConfigurations | Ne | Ne |
| automationAccounts/webhooks | Ne |  Ne |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| environments | Ne |  Ne |
| prostředí/účty | Ne |  Ne |
| prostředí/účty/obory názvů | Ne |  Ne |
| prostředí/účty/obory názvů/konfigurace | Ne |  Ne |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| b2cDirectories | Ano | Ne |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| registrations | Ano | Ano |
| registrace/customerSubscriptions | Ne |  Ne |
| registrace/produkty | Ne |  Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| batchAccounts | Ano | Ano |

## <a name="microsoftbilling"></a>Microsoft. fakturace
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| billingAccounts | Ne |  Ne |
| billingAccounts/billingProfiles | Ne |  Ne |
| billingAccounts/billingProfiles/billingSubscriptions | Ne |  Ne |
| billingAccounts/billingProfiles/faktury | Ne |  Ne |
| billingAccounts/billingProfiles/invoices/pricesheet | Ne |  Ne |
| billingAccounts/billingProfiles/operationStatus | Ne |  Ne |
| billingAccounts/billingProfiles/paymentMethods | Ne |  Ne |
| billingAccounts/billingProfiles/– zásady | Ne |  Ne |
| billingAccounts/billingProfiles/pricesheet | Ne |  Ne |
| billingAccounts/billingProfiles/produkty | Ne |  Ne |
| billingAccounts/billingProfiles/transactions | Ne |  Ne |
| billingAccounts/billingSubscriptions | Ne |  Ne |
| billingAccounts/oddělení | Ne |  Ne |
| billingAccounts/eligibleOffers | Ne |  Ne |
| billingAccounts/enrollmentAccounts | Ne |  Ne |
| billingAccounts/faktury | Ne |  Ne |
| billingAccounts/invoiceSections | Ne |  Ne |
| billingAccounts/invoiceSections/billingSubscriptions | Ne |  Ne |
| billingAccounts/invoiceSections/billingSubscriptions/transfer | Ne |  Ne |
| billingAccounts/invoiceSections/importRequests | Ne |  Ne |
| billingAccounts/invoiceSections/initiateImportRequest | Ne |  Ne |
| billingAccounts/invoiceSections/initiateTransfer | Ne |  Ne |
| billingAccounts/invoiceSections/operationStatus | Ne |  Ne |
| billingAccounts/invoiceSections/produkty | Ne |  Ne |
| billingAccounts/invoiceSections/transfery | Ne |  Ne |
| billingAccounts/produkty | Ne |  Ne |
| billingAccounts/projekty | Ne |  Ne |
| billingAccounts/projekty/billingSubscriptions | Ne |  Ne |
| billingAccounts/projekty/importRequests | Ne |  Ne |
| billingAccounts/projects/initiateImportRequest | Ne |  Ne |
| billingAccounts/projects/operationStatus | Ne |  Ne |
| billingAccounts/projekty/produkty | Ne |  Ne |
| billingAccounts/transakce | Ne |  Ne |
| billingPeriods | Ne |  Ne |
| BillingPermissions | Ne |  Ne |
| billingProperty | Ne |  Ne |
| BillingRoleAssignments | Ne |  Ne |
| BillingRoleDefinitions | Ne |  Ne |
| CreateBillingRoleAssignment | Ne |  Ne |
| oddělení | Ne |  Ne |
| enrollmentAccounts | Ne |  Ne |
| importRequests | Ne |  Ne |
| importRequests/acceptImportRequest | Ne |  Ne |
| importRequests/declineImportRequest | Ne |  Ne |
| faktury | Ne |  Ne |
| Převede | Ne |  Ne |
| přenosy/acceptTransfer | Ne |  Ne |
| přenosy/declineTransfer | Ne |  Ne |
| transfers/operationStatus | Ne |  Ne |
| usagePlans | Ne |  Ne |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| mapApis | Ano | Ano |
| updateCommunicationPreference | Ne |  Ne |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| BizTalk | Ano | Ano |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Ne |  Ne |
| blueprintAssignments/assignmentOperations | Ne |  Ne |
| blueprintAssignments/operace | Ne |  Ne |
| podrobné plány | Ne |  Ne |
| modrotisky/artefakty | Ne |  Ne |
| plány/verze | Ne |  Ne |
| modrotisky/verze/artefakty | Ne |  Ne |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| botServices | Ano | Ano |
| botServices/kanály | Ne |  Ne |
| botServices/připojení | Ne |  Ne |

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Redis | Ano | Ano |
| RedisConfigDefinition | Ne |  Ne |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| appliedReservations | Ne |  Ne |
| calculatePrice | Ne |  Ne |
| catalogs | Ne |  Ne |
| commercialReservationOrders | Ne |  Ne |
| reservationOrders | Ne |  Ne |
| reservationOrders/calculateRefund | Ne |  Ne |
| reservationOrders/sloučit | Ne |  Ne |
| reservationOrders/rezervace | Ne |  Ne |
| reservationOrders/rezervace/Revize | Ne |  Ne |
| reservationOrders/vrátit | Ne |  Ne |
| reservationOrders/Split | Ne |  Ne |
| reservationOrders/swap | Ne |  Ne |
| rezervace | Ne |  Ne |
| Prostředky | Ne |  Ne |
| validateReservationOrder | Ne |  Ne |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| edgenodes | Ne |  Ne |
| profiles | Ano | Ano |
| profiles/endpoints | Ano | Ano |
| profily/koncové body/customdomains | Ne |  Ne |
| profily/koncové body/zdroje | Ne |  Ne |
| validateProbe | Ne |  Ne |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| certificateOrders | Ano | Ano |
| certificateOrders/certifikáty | Ne |  Ne |
| validateCertificateRegistrationInformation | Ne |  Ne |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| vestavěn | Ne |  Ne |
| domainNames | Ne |  Ne |
| domainNames/možnosti | Ne |  Ne |
| domainNames/internalLoadBalancers | Ne |  Ne |
| domainNames/serviceCertificates | Ne |  Ne |
| domainNames/sloty | Ne |  Ne |
| domainNames/sloty/role | Ne |  Ne |
| moveSubscriptionResources | Ne |  Ne |
| operatingSystemFamilies | Ne |  Ne |
| operatingSystems | Ne |  Ne |
| quotas | Ne |  Ne |
| Typ prostředků | Ne |  Ne |
| validateSubscriptionMoveAvailability | Ne |  Ne |
| virtualMachines | Ne |  Ne |
| virtualMachines/diagnosticSettings | Ne |  Ne |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Ne |  Ne |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| vestavěn | Ne |  Ne |
| expressRouteCrossConnections | Ne |  Ne |
| expressRouteCrossConnections/peerings | Ne |  Ne |
| gatewaySupportedDevices | Ne |  Ne |
| networkSecurityGroups | Ne |  Ne |
| quotas | Ne |  Ne |
| reservedIps | Ne |  Ne |
| virtualNetworks | Ne |  Ne |
| virtualNetworks/remoteVirtualNetworkPeeringProxies | Ne |  Ne |
| virtualNetworks/virtualNetworkPeerings | Ne |  Ne |

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| vestavěn | Ne |  Ne |
| disks | Ne |  Ne |
| images | Ne |  Ne |
| OSImage | Ne |  Ne |
| osPlatformImages | Ne |  Ne |
| publicImages | Ne |  Ne |
| quotas | Ne |  Ne |
| storageAccounts | Ne |  Ne |
| storageAccounts/služby | Ne |  Ne |
| storageAccounts/služby/diagnosticSettings | Ne |  Ne |
| storageAccounts/vmImages | Ne |  Ne |
| vmImages | Ne |  Ne |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| RateCard | Ne |  Ne |
| UsageAggregates | Ne |  Ne |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| availabilitySets | Ano | Ano |
| disks | Ano | Ano |
| images | Ano | Ano |
| restorePointCollections | Ano | Ano |
| restorePointCollections/restorePoints | Ne |  Ne |
| sharedVMImages | Ano | Ano |
| sharedVMImages/verze | Ano | Ano |
| snapshots | Ano | Ano |
| virtualMachines | Ano | Ano |
| virtualMachines/diagnosticSettings | Ne |  Ne |
| virtualMachines/extensions | Ano | Ano |
| virtualMachineScaleSets | Ano | Ano |
| virtualMachineScaleSets/extensions | Ne |  Ne |
| virtualMachineScaleSets/networkInterfaces | Ne |  Ne |
| virtualMachineScaleSets/publicIPAddresses | Ne |  Ne |
| virtualMachineScaleSets/virtualMachines | Ne |  Ne |
| virtualMachineScaleSets/virtualMachines/networkInterfaces | Ne |  Ne |

## <a name="microsoftconsumption"></a>Microsoft.Consumption
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| AggregatedCost | Ne |  Ne |
| Zůstatky | Ne |  Ne |
| Rozpočty | Ne |  Ne |
| Poplatky za | Ne |  Ne |
| CostTags | Ne |  Ne |
| Dobropis | Ne |  Ne |
| stránka events | Ne |  Ne |
| Prognózy | Ne |  Ne |
| ŠARŽ | Ne |  Ne |
| Tržišť | Ne |  Ne |
| Pricesheets | Ne |  Ne |
| Produktech | Ne |  Ne |
| ReservationDetails | Ne |  Ne |
| ReservationRecommendations | Ne |  Ne |
| ReservationSummaries | Ne |  Ne |
| ReservationTransactions | Ne |  Ne |
| Tags | Ne |  Ne |
| Výrazy | Ne |  Ne |
| UsageDetails | Ne |  Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| containerGroups | Ano | Ano |
| serviceAssociationLinks | Ne |  Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| registries | Ano | Ano |
| Registry a sestavení | Ne |  Ne |
| Registry/buildy/zrušit | Ne |  Ne |
| Registry/buildy/getLogLink | Ne |  Ne |
| Registry/buildTasks | Ano | Ano |
| Registry/buildTasks/kroky | Ne |  Ne |
| registries/eventGridFilters | Ne |  Ne |
| registries/getBuildSourceUploadUrl | Ne |  Ne |
| Registry/getpřihlašovací údaje | Ne |  Ne |
| Registry/importImage | Ne |  Ne |
| Registry/queueBuild | Ne |  Ne |
| Registry/regenerateCredential | Ne |  Ne |
| Registry/regenerateCredentials | Ne |  Ne |
| registries/replications | Ano | Ano |
| Registry/běhy | Ne |  Ne |
| Registry/spuštění/zrušit | Ne |  Ne |
| Registry/scheduleRun | Ne |  Ne |
| registries/tasks | Ano | Ano |
| Registry/updatePolicies | Ne |  Ne |
| registries/webhooks | Ano | Ano |
| registries/webhooks/getCallbackConfig | Ne |  Ne |
| Registry/Webhooky/příkazy k odeslání | Ne |  Ne |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| containerServices | Ano | Ano |
| managedClusters | Ano | Ano |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| applications | Ano | Ano |
| updateCommunicationPreference | Ne |  Ne |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Upozornění | Ne |  Ne |
| billingAccounts | Ne |  Ne |
| Konektory | Ano | Ano |
| oddělení | Ne |  Ne |
| Dimenze | Ne |  Ne |
| enrollmentAccounts | Ne |  Ne |
| Dotaz | Ne |  Ne |
| Registrace | Ne |  Ne |
| Reportconfigs | Ne |  Ne |
| Sestavy | Ne |  Ne |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| hubs | Ano | Ano |
| rozbočovače/authorizationPolicies | Ne |  Ne |
| rozbočovače/konektory | Ne |  Ne |
| rozbočovače/konektory/mapování | Ne |  Ne |
| centra/interakce | Ne |  Ne |
| centra/klíčový ukazatel výkonu | Ne |  Ne |
| centra/odkazy | Ne |  Ne |
| centra/profily | Ne |  Ne |
| rozbočovače/roleAssignments | Ne |  Ne |
| centra/role | Ne |  Ne |
| rozbočovače/suggestTypeSchema | Ne |  Ne |
| centra/zobrazení | Ne |  Ne |
| rozbočovače/widgetTypes | Ne |  Ne |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| jobs | Ano | Ano |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Ano | Ano |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| workspaces | Ano | Ne |
| pracovní prostory/virtualNetworkPeerings | Ne |  Ne |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| catalogs | Ano | Ano |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| connectionManagers | Ano | Ano |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Objekty DataFactory | Ano | Ne |
| DataFactory/diagnosticSettings | Ne |  Ne |
| dataFactorySchema | Ne |  Ne |
| factories | Ano | Ne |
| továrny/integrationRuntimes | Ne |  Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |
| accounts/dataLakeStoreAccounts | Ne |  Ne |
| účty/storageAccounts | Ne |  Ne |
| účty/storageAccounts/kontejnery | Ne |  Ne |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |
| účty/eventGridFilters | Ne |  Ne |
| účty/firewallRules | Ne |  Ne |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| services | Ano | Ano |
| services/projects | Ano | Ano |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| servers | Ano | Ano |
| servery/recoverableServers | Ne |  Ne |
| servery/virtualNetworkRules | Ne |  Ne |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| servers | Ano | Ano |
| servery/recoverableServers | Ne |  Ne |
| servery/virtualNetworkRules | Ne |  Ne |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| servers | Ano | Ano |
| servery/poradci | Ne |  Ne |
| servers/queryTexts | Ne |  Ne |
| servery/recoverableServers | Ne |  Ne |
| servers/topQueryStatistics | Ne |  Ne |
| servery/virtualNetworkRules | Ne |  Ne |
| servery/waitStatistics | Ne |  Ne |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| IotHubs | Ano | Ano |
| IotHubs/eventGridFilters | Ne |  Ne |
| ProvisioningServices | Ano | Ano |
| použití | Ne |  Ne |

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Kontrolou | Ano | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| labs | Ano | Ano |
| Labs/serviceRunners | Ano | Ano |
| Labs/virtualMachines | Ano | Ano |
| schedules | Ano | Ano |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Ne |  Ne |
| databaseAccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| domains | Ano | Ano |
| domény/domainOwnershipIdentifiers | Ne |  Ne |
| generateSsoRequest | Ne |  Ne |
| topLevelDomains | Ne |  Ne |
| validateDomainRegistrationInformation | Ne |  Ne |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| lcsprojects | Ne |  Ne |
| lcsprojects/clouddeployments | Ne |  Ne |
| lcsprojects/konektory | Ne |  Ne |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| domains | Ano | Ne |
| domény a témata | Ne |  Ne |
| eventSubscriptions | Ne |  Ne |
| extensionTopics | Ne |  Ne |
| topics | Ano | Ne |
| topicTypes | Ne |  Ne |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| clusters | Ano | Ne |
| namespaces | Ano | Ne |
| obory názvů/autorizačních pravidel | Ne |  Ne |
| obory názvů/disasterrecoveryconfigs | Ne |  Ne |
| obory názvů/eventhubs | Ne |  Ne |
| obory názvů/eventhubs/autorizačních pravidel | Ne |  Ne |
| obory názvů/eventhubs/consumergroups | Ne |  Ne |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Database | Ne |  Ne |
| Zprostředkovatelé | Ne |  Ne |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| registrované | Ne |  Ne |
| galleryitems | Ne |  Ne |
| generateartifactaccessuri | Ne |  Ne |
| myareas | Ne |  Ne |
| myareas/oblasti | Ne |  Ne |
| myareas/oblasti/oblasti | Ne |  Ne |
| myareas/oblasti/oblasti/galleryitems | Ne |  Ne |
| myareas/oblasti/galleryitems | Ne |  Ne |
| myareas/galleryitems | Ne |  Ne |
| Registrace | Ne |  Ne |
| Prostředky | Ne |  Ne |
| retrieveresourcesbyid | Ne |  Ne |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Ne |  Ne |
| software | Ne |  Ne |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| hanaInstances | Ano |  Ano |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| clusters | Ano | Ano |
| clustery/aplikace | Ne |  Ne |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| jobs | Ano | Ano |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| labelGroups | Ne |  Ne |
| labelGroups/popisky | Ne |  Ne |
| labelGroups/štítky/podmínky | Ne |  Ne |
| labelGroups/popisky/podjmenovky | Ne |  Ne |
| labelGroups/labels/subLabels/conditions | Ne |  Ne |

## <a name="microsoftinsights"></a>microsoft.insights
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| actiongroups | Ano | Ano |
| activityLogAlerts | Ano | Ano |
| alertrules | Ano | Ano |
| automatedExportSettings | Ne |  Ne |
| autoscalesettings | Ano | Ano |
| směrný | Ne |  Ne |
| calculatebaseline | Ne |  Ne |
| components | Ano | Ano |
| komponenty/události | Ne |  Ne |
| komponenty/pricingPlans | Ne |  Ne |
| komponenty a dotazy | Ne |  Ne |
| diagnosticSettings | Ne |  Ne |
| diagnosticSettingsCategories | Ne |  Ne |
| eventCategories | Ne |  Ne |
| EventType | Ne |  Ne |
| extendedDiagnosticSettings | Ne |  Ne |
| logDefinitions | Ne |  Ne |
| logprofiles | Ne |  Ne |
| logs | Ne |  Ne |
| metricAlerts | Ano | Ano |
| migrateToNewPricingModel | Ne |  Ne |
| myWorkbooks | Ne |  Ne |
| dotazy | Ne |  Ne |
| rollbackToLegacyPricingModel | Ne |  Ne |
| scheduledqueryrules | Ano | Ano |
| vmInsightsOnboardingStatuses | Ne |  Ne |
| webtests | Ano | Ano |
| workbooks | Ano | Ano |

## <a name="microsoftintune"></a>Microsoft.Intune
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Ne |  Ne |
| diagnosticSettingsCategories | Ne |  Ne |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| IoTApps | Ano | Ano |

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Graph | Ano | Ano |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| deletedVaults | Ne |  Ne |
| vaults | Ano | Ano |
| trezory/accessPolicies | Ne |  Ne |
| trezory/tajné klíče | Ne |  Ne |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| clusters | Ano | Ano |
| clustery/databáze | Ne |  Ne |
| clustery, databáze/datapřipojení | Ne |  Ne |
| clustery/databáze/eventhubconnections | Ne |  Ne |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| labaccounts | Ano | Ano |
| uživatelé | Ne |  Ne |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| logs | Ne |  Ne |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| integrationAccounts | Ano | Ano |
| workflows | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| commitmentPlans | Ano | Ano |
| webServices | Ano | Ano |
| Pracovní prostory | Ano | Ano |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |
| accounts/workspaces | Ano | Ano |
| accounts/workspaces/projects | Ano | Ano |
| teamAccounts | Ano | Ano |
| teamAccounts/pracovní prostory | Ano | Ano |
| teamAccounts/pracovní prostory/projekty | Ano | Ano |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| workspaces | Ano | Ano |
| pracovní prostory a výpočetní prostředky | Ne |  Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Nebyly | Ne |  Ne |
| userAssignedIdentities | Ano | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| getentities | Ne |  Ne |
| managementGroups | Ne |  Ne |
| Prostředky | Ne |  Ne |
| startTenantBackfill | Ne |  Ne |
| tenantBackfillStatus | Ne |  Ne |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |
| účty/eventGridFilters | Ne |  Ne |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Rozsah | Ne |  Ne |
| offerTypes | Ne |  Ne |
| offerTypes/vydavatelé | Ne |  Ne |
| offerTypes/vydavatelé/nabídky | Ne |  Ne |
| offerTypes/vydavatelé/nabídky/plány | Ne |  Ne |
| offerTypes/vydavatelé/nabídky/plány/smlouvy | Ne |  Ne |
| offerTypes/publishers/offers/plans/configs | Ne |  Ne |
| offerTypes/publishers/offers/plans/configs/importImage | Ne |  Ne |
| privategalleryitems | Ne |  Ne |
| Produktech | Ne |  Ne |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| classicDevServices | Ano | Ano |
| updateCommunicationPreference | Ne |  Ne |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| o | Ne |  Ne |
| offertypes | Ne |  Ne |

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| MediaServices | Ano | Ano |
| mediaservices/accountFilters | Ne |  Ne |
| MediaServices/assety | Ne |  Ne |
| mediaservices/assets/assetFilters | Ne |  Ne |
| mediaservices/contentKeyPolicies | Ne |  Ne |
| mediaservices/eventGridFilters | Ne |  Ne |
| MediaServices/liveEventOperations | Ne |  Ne |
| MediaServices/liveEvents | Ano | Ano |
| MediaServices/liveEvents/liveOutputs | Ne |  Ne |
| mediaservices/liveOutputOperations | Ne |  Ne |
| MediaServices/streamingEndpointOperations | Ne |  Ne |
| MediaServices/starají | Ano | Ano |
| MediaServices/streamingLocators | Ne |  Ne |
| MediaServices/streamingPolicies | Ne |  Ne |
| MediaServices/transformace | Ne |  Ne |
| MediaServices/transformace/úlohy | Ne |  Ne |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| projects | Ano | Ano |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| applicationGateways | Ano | Ne |
| applicationSecurityGroups | Ano | Ano |
| azureFirewallFqdnTags | Ne |  Ne |
| azureFirewalls | Ano | Ne |
| bgpServiceCommunities | Ne |  Ne |
| connections | Ano | Ano |
| ddosCustomPolicies | Ano | Ano |
| ddosProtectionPlans | Ano | Ano |
| dnsOperationStatuses | Ne |  Ne |
| dnszones | Ano | Ano |
| dnszones/A | Ne |  Ne |
| dnszones/AAAA | Ne |  Ne |
| dnszones/vše | Ne |  Ne |
| dnszones/CAA | Ne |  Ne |
| dnszones/CNAME | Ne |  Ne |
| dnszones/MX | Ne |  Ne |
| dnszones/NS | Ne |  Ne |
| dnszones/PTR | Ne |  Ne |
| dnszones/sady záznamů | Ne |  Ne |
| dnszones/SOA | Ne |  Ne |
| dnszones/SRV | Ne |  Ne |
| dnszones/TXT | Ne |  Ne |
| expressRouteCircuits | Ano  | Ne |
| expressRouteServiceProviders | Ne |  Ne |
| frontdoors | Ano, ale omezeno (viz [Poznámka níže](#frontdoor)) | Ano |
| frontdoorWebApplicationFirewallPolicies | Ano, ale omezeno (viz [Poznámka níže](#frontdoor)) | Ano |
| getDnsResourceReference | Ne |  Ne |
| interfaceEndpoints | Ano | Ano |
| internalNotify | Ne |  Ne |
| loadBalancers | Ano | Ne |
| localNetworkGateways | Ano | Ano |
| natGateways | Ano | Ano |
| networkIntentPolicies | Ano | Ano |
| networkInterfaces | Ano | Ano |
| networkProfiles | Ano | Ano |
| networkSecurityGroups | Ano | Ano |
| networkWatchers | Ano | Ne |
| networkWatchers/connectionMonitors | Ano | Ne |
| networkWatchers/čočky | Ano | Ne |
| networkWatchers/pingMeshes | Ano | Ne |
| privateLinkServices | Ano | Ano |
| publicIPAddresses | Ano | Ano |
| publicIPPrefixes | Ano | Ano |
| routeFilters | Ano | Ano |
| routeTables | Ano | Ano |
| serviceEndpointPolicies | Ano | Ano |
| trafficManagerGeographicHierarchies | Ne |  Ne |
| trafficmanagerprofiles | Ano | Ano |
| trafficmanagerprofiles/heatMaps | Ne |  Ne |
| virtualHubs | Ano | Ano |
| virtualNetworkGateways | Ano | Ano |
| virtualNetworks | Ano | Ano |
| virtualNetworks/subnets | Ne |  Ne |
| virtualNetworkTaps | Ano | Ano |
| virtualWans | Ano | Ano |
| vpnGateways | Ano | Ne |
| vpnSites | Ano | Ano |
| webApplicationFirewallPolicies | Ano | Ano |

<a id="frontdoor" />

U služby front-dveří pro Azure můžete při vytváření prostředku použít značky, ale aktualizace nebo přidávání značek se momentálně nepodporuje.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| namespaces | Ano | Ne |
| obory názvů/notificationHubs | Ano | Ne |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| zařízení | Ne |  Ne |
| linkTargets | Ne |  Ne |
| storageInsightConfigs | Ne |  Ne |
| workspaces | Ano | Ano |
| pracovní prostory/zdroje dat | Ne |  Ne |
| pracovní prostory/linkedServices | Ne |  Ne |
| pracovní prostory a dotazy | Ne |  Ne |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| managementassociations | Ne |  Ne |
| managementconfigurations | Ano | Ano |
| solutions | Ano | Ano |
| views | Ano | Ano |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| policyEvents | Ne |  Ne |
| policyStates | Ne |  Ne |
| policyTrackedResources | Ne |  Ne |
| nápravy | Ne |  Ne |

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Console | Ne |  Ne |
| dashboards | Ano | Ano |
| userSettings | Ne |  Ne |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| workspaceCollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| capacities | Ano | Ano |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Ne |  Ne |
| vaults | Ano | Ano |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| namespaces | Ano | Ano |
| obory názvů/autorizačních pravidel | Ne |  Ne |
| obory názvů/hybridconnections | Ne |  Ne |
| obory názvů/hybridconnections/autorizačních pravidel | Ne |  Ne |
| obory názvů/wcfrelays | Ne |  Ne |
| obory názvů/wcfrelays/autorizačních pravidel | Ne |  Ne |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Prostředky | Ne |  Ne |
| subscriptionsStatus | Ne |  Ne |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Ne |  Ne |
| childAvailabilityStatuses | Ne |  Ne |
| childResources | Ne |  Ne |
| stránka events | Ne |  Ne |
| impactedResources | Ne |  Ne |
| Připomenutí | Ne |  Ne |

## <a name="microsoftresources"></a>Microsoft. Resources
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| Nasazení | Ne |  Ne |
| nasazení/operace | Ne |  Ne |
| Odkazy | Ne |  Ne |
| notifyResourceJobs | Ne |  Ne |
| Zprostředkovatelé | Ne |  Ne |
| resourceGroups | Ne |  Ne |
| Prostředky | Ne |  Ne |
| Odběru | Ne |  Ne |
| předplatná/poskytovatelé | Ne |  Ne |
| subscriptions/resourceGroups | Ne |  Ne |
| předplatná/ResourceGroups/prostředky | Ne |  Ne |
| předplatná/prostředky | Ne |  Ne |
| předplatná/TagNames | Ne |  Ne |
| subscriptions/tagNames/tagValues | Ne |  Ne |
| tenantů | Ne |  Ne |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| applications | Ano | Ano |
| saasresources | Ne |  Ne |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| flows | Ano | Ano |
| jobcollections | Ano | Ano |

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Ne |  Ne |
| searchServices | Ano | Ano |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Ne |  Ne |
| výstrahy | Ne |  Ne |
| allowedConnections | Ne |  Ne |
| appliances | Ne |  Ne |
| applicationWhitelistings | Ne |  Ne |
| AutoProvisioningSettings | Ne |  Ne |
| Předpisů | Ne |  Ne |
| dataCollectionAgents | Ne |  Ne |
| discoveredSecuritySolutions | Ne |  Ne |
| externalSecuritySolutions | Ne |  Ne |
| InformationProtectionPolicies | Ne |  Ne |
| jitNetworkAccessPolicies | Ne |  Ne |
| Sledovaný | Ne |  Ne |
| monitorování/antimalware | Ne |  Ne |
| monitorování/směrný plán | Ne |  Ne |
| monitorování/Oprava | Ne |  Ne |
| Konfigurovaný | Ne |  Ne |
| ceny | Ne |  Ne |
| securityContacts | Ne |  Ne |
| securitySolutions | Ne |  Ne |
| securitySolutionsReferenceData | Ne |  Ne |
| securityStatus | Ne |  Ne |
| securityStatus/koncové body | Ne |  Ne |
| securityStatus/subnets | Ne |  Ne |
| securityStatus/virtualMachines | Ne |  Ne |
| securityStatuses | Ne |  Ne |
| securityStatusesSummaries | Ne |  Ne |
| settings | Ne |  Ne |
| úlohy | Ne |  Ne |
| topologie | Ne |  Ne |
| workspaceSettings | Ne |  Ne |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Ne |  Ne |
| diagnosticSettingsCategories | Ne |  Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| namespaces | Ano | Ne |
| obory názvů/autorizačních pravidel | Ne |  Ne |
| obory názvů/disasterrecoveryconfigs | Ne |  Ne |
| obory názvů/eventgridfilters | Ne |  Ne |
| obory názvů/fronty | Ne |  Ne |
| obory názvů/fronty/autorizačních pravidel | Ne |  Ne |
| obory názvů/témata | Ne |  Ne |
| obory názvů/témata/autorizačních pravidel | Ne |  Ne |
| obory názvů/témata/předplatná | Ne |  Ne |
| obory názvů/témata/předplatná/pravidla | Ne |  Ne |
| premiumMessagingRegions | Ne |  Ne |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| clusters | Ano | Ano |
| clustery/aplikace | Ne |  Ne |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| applications | Ano | Ano |
| brány | Ano | Ano |
| networks | Ano | Ano |
| záleží | Ano | Ano |
| volumes | Ano | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| SignalR | Ano | Ano |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Ano | Ano |
| appliances | Ano | Ano |
| applicationDefinitions | Ano | Ano |
| applications | Ano | Ano |
| jitRequests | Ano | Ano |

## <a name="microsoftsql"></a>Microsoft.SQL
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| managedInstances | Ano | Ano |
| managedInstances/databáze | Ano (viz poznámka níže) | Ano |
| managedInstances/databases/backupShortTermRetentionPolicies | Ne | Ne |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Ne | Ne |
| managedInstances/databáze/vulnerabilityAssessments | Ne | Ne |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Ne | Ne |
| managedInstances/encryptionProtector | Ne | Ne |
| managedInstances/klíče | Ne | Ne |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Ne | Ne |
| managedInstances/vulnerabilityAssessments | Ne | Ne |
| servers | Ano | Ano |
| servery/správci | Ne |  Ne |
| servery/communicationLinks | Ne |  Ne |
| servers/databases | Ano (viz poznámka níže) | Ano |
| servery/encryptionProtector | Ne |  Ne |
| servery/firewallRules | Ne |  Ne |
| servery/klíče | Ne |  Ne |
| servery/restorableDroppedDatabases | Ne |  Ne |
| servery/serviceobjectives | Ne |  Ne |
| servery/tdeCertificates | Ne |  Ne |

> [!NOTE]
> Hlavní databáze nepodporuje značky, ale další databáze, včetně Azure SQL Data Warehousech databází, podporuje značky. Azure SQL Data Warehouse databáze musí být ve stavu aktivní (není pozastaveno).


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Ano | Ano |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Ne |  Ne |
| SqlVirtualMachines | Ano | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| storageAccounts | Ano | Ano |
| storageAccounts/blobServices | Ne |  Ne |
| storageAccounts/služby | Ne |  Ne |
| storageAccounts/queueServices | Ne |  Ne |
| storageAccounts/služby | Ne |  Ne |
| storageAccounts/tableServices | Ne |  Ne |
| použití | Ne |  Ne |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| storageSyncServices | Ano | Ano |
| storageSyncServices/registeredServer | Ne |  Ne |
| storageSyncServices/syncGroups | Ne |  Ne |
| storageSyncServices/syncGroups/cloudEndpoints | Ne |  Ne |
| storageSyncServices/syncGroups/serverEndpoints | Ne |  Ne |
| storageSyncServices/pracovní postupy | Ne |  Ne |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| managers | Ano | Ano |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| streamingjobs | Ano (viz poznámka níže) | Ano |
| streamingjobs/diagnosticSettings | Ne |  Ne |

> [!NOTE]
> Značku nelze přidat, je-li spuštěna aplikace streamingjobs. Zastavením prostředku přidejte značku.

## <a name="microsoftsubscription"></a>Microsoft. Subscription
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| CreateSubscription | Ne |  Ne |
| SubscriptionDefinitions | Ne |  Ne |
| SubscriptionOperations | Ne |  Ne |

## <a name="microsoftsupport"></a>microsoft.support
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| supporttickets | Ne |  Ne |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| providerRegistrations | Ano | Ano |
| Prostředky | Ano | Ano |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| environments | Ano | Ne |
| prostředí/accessPolicies | Ne |  Ne |
| environments/eventsources | Ano | Ne |
| prostředí/referenceDataSets | Ano | Ne |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| account | Ano | Ano |
| account/extension | Ano | Ano |
| account/project | Ano | Ano |

## <a name="microsoftweb"></a>Microsoft.Web
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | Ne |  Ne |
| apiManagementAccounts/apiAcls | Ne |  Ne |
| apiManagementAccounts/rozhraní API | Ne |  Ne |
| apiManagementAccounts/API/apiAcls | Ne |  Ne |
| apiManagementAccounts/API/connectionAcls | Ne |  Ne |
| apiManagementAccounts/rozhraní API/připojení | Ne |  Ne |
| apiManagementAccounts/rozhraní API/připojení/connectionAcls | Ne |  Ne |
| apiManagementAccounts/API/localizedDefinitions | Ne |  Ne |
| apiManagementAccounts/connectionAcls | Ne |  Ne |
| apiManagementAccounts/připojení | Ne |  Ne |
| billingMeters | Ne |  Ne |
| certificates | Ano | Ano |
| connectionGateways | Ano | Ano |
| connections | Ano | Ano |
| customApis | Ano | Ano |
| deletedSites | Ne |  Ne |
| – funkce | Ne |  Ne |
| hostingEnvironments | Ano | Ano |
| hostingEnvironments/multiRolePools | Ne |  Ne |
| hostingEnvironments/multiRolePools/instance | Ne |  Ne |
| hostingEnvironments/workerPools | Ne |  Ne |
| hostingEnvironments/workerPools/instance | Ne |  Ne |
| publishingUsers | Ne |  Ne |
| Doporučení | Ne |  Ne |
| resourceHealthMetadata | Ne |  Ne |
| moduly runtime | Ne |  Ne |
| serverFarms | Ano | Ano |
| Serverových farem/pracovní procesy | Ne |  Ne |
| sites | Ano | Ano |
| lokality/domainOwnershipIdentifiers | Ne |  Ne |
| sites/hostNameBindings | Ne |  Ne |
| lokality/instance | Ne |  Ne |
| lokality, instance/rozšíření | Ne |  Ne |
| sites/premieraddons | Ano | Ano |
| lokality/doporučení | Ne |  Ne |
| sites/resourceHealthMetadata | Ne |  Ne |
| sites/slots | Ano | Ano |
| sites/slots/hostNameBindings | Ne |  Ne |
| lokality/sloty/instance | Ne |  Ne |
| lokality/sloty/instance/rozšíření | Ne |  Ne |
| sourceControls | Ne |  Ne |
| Oproti | Ne |  Ne |
| verifyHostingEnvironmentVnet | Ne |  Ne |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Ne |  Ne |
| diagnosticSettingsCategories | Ne |  Ne |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| DeviceServices | Ano | Ano |

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl
| Typ prostředku | Podporuje značky | Značka v sestavě nákladů |
| ------------- | ----------- | ----------- |
| components | Ne |  Ne |
| componentsSummary | Ne |  Ne |
| monitorInstances | Ne |  Ne |
| monitorInstancesSummary | Ne |  Ne |
| Monitor | Ne |  Ne |
| notificationSettings | Ne |  Ne |

## <a name="next-steps"></a>Další postup
Informace o tom, jak používat značky pro prostředky, najdete v tématu [použití značek k uspořádání prostředků Azure](resource-group-using-tags.md).

---
title: Podpora značky Azure Resource Manageru pro prostředky
description: Ukazuje, jaké typy prostředků Azure podporují značky. Poskytuje podrobnosti pro všechny služby Azure.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 06/07/2019
ms.author: tomfitz
ms.openlocfilehash: 4f1bc1415fbb875120d7b64128cae69e1e3f442c
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2019
ms.locfileid: "67339847"
---
# <a name="tag-support-for-azure-resources"></a>Podpora značek pro prostředky Azure
Tento článek popisuje, zda typ prostředku podporuje [značky](resource-group-using-tags.md). Sloupec s názvem **podporuje značky** označuje, zda typ prostředku má vlastnost pro značku. Sloupec s názvem **značku sestavy nákladů** označuje, zda tento typ prostředku předá značka sestavy nákladů.

Chcete-li získat stejná data jako soubor hodnot oddělených čárkami, stáhněte si [značky support.csv](https://github.com/tfitzmac/resource-capabilities/blob/master/tag-support.csv).

## <a name="microsoftaad"></a>Microsoft.AAD
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| DomainServices | Ano | Ano |
| DomainServices/oucontainer | Ne | Ne |

## <a name="microsoftaadiam"></a>microsoft.aadiam
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Ne |  Ne |
| diagnosticSettingsCategories | Ne |  Ne |

## <a name="microsoftaddons"></a>Microsoft.Addons
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| supportProviders | Ne |  Ne |

## <a name="microsoftadhybridhealthservice"></a>Microsoft.ADHybridHealthService
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| aadsupportcases | Ne |  Ne |
| addsservices | Ne |  Ne |
| Agenti | Ne |  Ne |
| anonymousapiusers | Ne |  Ne |
| konfigurace | Ne |  Ne |
| logs | Ne |  Ne |
| sestavy | Ne |  Ne |
| services | Ne |  Ne |

## <a name="microsoftadvisor"></a>Microsoft.Advisor
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Konfigurace | Ne |  Ne |
| generateRecommendations | Ne |  Ne |
| Doporučení | Ne |  Ne |
| potlačení | Ne |  Ne |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
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
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| servers | Ano | Ano |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| reportFeedback | Ne |  Ne |
| service | Ano | Ano |
| validateServiceName | Ne |  Ne |

## <a name="microsoftattestation"></a>Microsoft.Attestation
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| attestationProviders | Ne |  Ne |

## <a name="microsoftauthorization"></a>Microsoft.Authorization
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| classicAdministrators | Ne |  Ne |
| denyAssignments | Ne |  Ne |
| elevateAccess | Ne |  Ne |
| Zámky | Ne |  Ne |
| oprávnění | Ne |  Ne |
| policyAssignments | Ne |  Ne |
| policyDefinitions | Ne |  Ne |
| policySetDefinitions | Ne |  Ne |
| providerOperations | Ne |  Ne |
| roleAssignments | Ne |  Ne |
| roleDefinitions | Ne |  Ne |

## <a name="microsoftautomation"></a>Microsoft.Automation
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| automationAccounts | Ano | Ano |
| automationAccounts/configurations | Ano | Ano |
| automationAccounts/jobs | Ne |  Ne |
| automationAccounts/runbooks | Ano | Ano |
| automationAccounts/softwareUpdateConfigurations | Ne | Ne |
| automationAccounts/webhooks | Ne |  Ne |

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| environments | Ne |  Ne |
| prostředí/účty | Ne |  Ne |
| prostředí/účty nebo obory názvů | Ne |  Ne |
| prostředí/účty nebo obory názvů/konfigurace | Ne |  Ne |

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| b2cDirectories | Ano | Ne |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| registrations | Ano | Ano |
| registrations/customerSubscriptions | Ne |  Ne |
| registrace a produkty | Ne |  Ne |

## <a name="microsoftbatch"></a>Microsoft.Batch
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| batchAccounts | Ano | Ano |

## <a name="microsoftbilling"></a>Microsoft.Billing
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| billingAccounts | Ne |  Ne |
| billingAccounts/billingProfiles | Ne |  Ne |
| billingAccounts/billingProfiles/billingSubscriptions | Ne |  Ne |
| billingAccounts/billingProfiles/invoices | Ne |  Ne |
| billingAccounts/billingProfiles/invoices/pricesheet | Ne |  Ne |
| billingAccounts/billingProfiles/operationStatus | Ne |  Ne |
| billingAccounts/billingProfiles/paymentMethods | Ne |  Ne |
| billingAccounts/billingProfiles/policies | Ne |  Ne |
| billingAccounts/billingProfiles/pricesheet | Ne |  Ne |
| billingAccounts/billingProfiles/products | Ne |  Ne |
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
| billingAccounts/invoiceSections/transfers | Ne |  Ne |
| billingAccounts/produkty | Ne |  Ne |
| billingAccounts/projekty | Ne |  Ne |
| billingAccounts/projects/billingSubscriptions | Ne |  Ne |
| billingAccounts/projects/importRequests | Ne |  Ne |
| billingAccounts/projects/initiateImportRequest | Ne |  Ne |
| billingAccounts/projects/operationStatus | Ne |  Ne |
| billingAccounts/projektů/produkty | Ne |  Ne |
| billingAccounts/transakce | Ne |  Ne |
| billingPeriods | Ne |  Ne |
| BillingPermissions | Ne |  Ne |
| billingProperty | Ne |  Ne |
| BillingRoleAssignments | Ne |  Ne |
| BillingRoleDefinitions | Ne |  Ne |
| CreateBillingRoleAssignment | Ne |  Ne |
| Oddělení | Ne |  Ne |
| enrollmentAccounts | Ne |  Ne |
| importRequests | Ne |  Ne |
| importRequests/acceptImportRequest | Ne |  Ne |
| importRequests/declineImportRequest | Ne |  Ne |
| Faktury | Ne |  Ne |
| Převody | Ne |  Ne |
| přenosy/acceptTransfer | Ne |  Ne |
| přenosy/declineTransfer | Ne |  Ne |
| transfers/operationStatus | Ne |  Ne |
| usagePlans | Ne |  Ne |

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| mapApis | Ano | Ano |
| updateCommunicationPreference | Ne |  Ne |

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| BizTalk | Ano | Ano |

## <a name="microsoftblueprint"></a>Microsoft.Blueprint
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| blueprintAssignments | Ne |  Ne |
| blueprintAssignments/assignmentOperations | Ne |  Ne |
| blueprintAssignments/operations | Ne |  Ne |
| podrobné plány. | Ne |  Ne |
| plány a artefakty | Ne |  Ne |
| plány a verze | Ne |  Ne |
| plány/verze/artefaktů | Ne |  Ne |

## <a name="microsoftbotservice"></a>Microsoft.BotService
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| botServices | Ano | Ano |
| botServices/channels | Ne |  Ne |
| botServices/připojení | Ne |  Ne |

## <a name="microsoftcache"></a>Microsoft.Cache
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Redis | Ano | Ano |
| RedisConfigDefinition | Ne |  Ne |

## <a name="microsoftcapacity"></a>Microsoft.Capacity
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| appliedReservations | Ne |  Ne |
| calculatePrice | Ne |  Ne |
| catalogs | Ne |  Ne |
| commercialReservationOrders | Ne |  Ne |
| reservationOrders | Ne |  Ne |
| reservationOrders/calculateRefund | Ne |  Ne |
| reservationOrders/merge | Ne |  Ne |
| reservationOrders/rezervace | Ne |  Ne |
| reservationOrders/rezervace/revize | Ne |  Ne |
| vrácení hodnoty reservationOrders | Ne |  Ne |
| reservationOrders/rozdělení | Ne |  Ne |
| reservationOrders/swap | Ne |  Ne |
| rezervace | Ne |  Ne |
| Prostředky | Ne |  Ne |
| validateReservationOrder | Ne |  Ne |

## <a name="microsoftcdn"></a>Microsoft.Cdn
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| edgenodes | Ne |  Ne |
| profiles | Ano | Ano |
| profiles/endpoints | Ano | Ano |
| profiles/endpoints/customdomains | Ne |  Ne |
| profily/koncové body/zdroje | Ne |  Ne |
| validateProbe | Ne |  Ne |

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| objednávky certifikátu | Ano | Ano |
| certifikáty objednávky certifikátu | Ne |  Ne |
| validateCertificateRegistrationInformation | Ne |  Ne |

## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Možnosti | Ne |  Ne |
| domainNames | Ne |  Ne |
| domainNames/možnosti | Ne |  Ne |
| domainNames/internalLoadBalancers | Ne |  Ne |
| domainNames/serviceCertificates | Ne |  Ne |
| domainNames/slots | Ne |  Ne |
| domainNames/slots/roles | Ne |  Ne |
| moveSubscriptionResources | Ne |  Ne |
| operatingSystemFamilies | Ne |  Ne |
| operatingSystems | Ne |  Ne |
| quotas | Ne |  Ne |
| resourceTypes | Ne |  Ne |
| validateSubscriptionMoveAvailability | Ne |  Ne |
| virtualMachines | Ne |  Ne |
| virtualMachines/diagnosticSettings | Ne |  Ne |

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| classicInfrastructureResources | Ne |  Ne |

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Možnosti | Ne |  Ne |
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
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Možnosti | Ne |  Ne |
| disks | Ne |  Ne |
| images | Ne |  Ne |
| OSImage | Ne |  Ne |
| osPlatformImages | Ne |  Ne |
| publicImages | Ne |  Ne |
| quotas | Ne |  Ne |
| storageAccounts | Ne |  Ne |
| storageAccounts/services | Ne |  Ne |
| storageAccounts/services/diagnosticSettings | Ne |  Ne |
| storageAccounts/vmImages | Ne |  Ne |
| vmImages | Ne |  Ne |

## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftcommerce"></a>Microsoft.Commerce
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| RateCard | Ne |  Ne |
| UsageAggregates | Ne |  Ne |

## <a name="microsoftcompute"></a>Microsoft.Compute
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| availabilitySets | Ano | Ano |
| disks | Ano | Ano |
| images | Ano | Ano |
| restorePointCollections | Ano | Ano |
| restorePointCollections/restorePoints | Ne |  Ne |
| sharedVMImages | Ano | Ano |
| sharedVMImages/versions | Ano | Ano |
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
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| AggregatedCost | Ne |  Ne |
| Zůstatky | Ne |  Ne |
| Rozpočty | Ne |  Ne |
| Poplatky za | Ne |  Ne |
| CostTags | Ne |  Ne |
| Kredity | Ne |  Ne |
| stránka events | Ne |  Ne |
| Prognózy | Ne |  Ne |
| Velké | Ne |  Ne |
| Marketplaces | Ne |  Ne |
| Pricesheets | Ne |  Ne |
| Produkty | Ne |  Ne |
| ReservationDetails | Ne |  Ne |
| ReservationRecommendations | Ne |  Ne |
| ReservationSummaries | Ne |  Ne |
| ReservationTransactions | Ne |  Ne |
| Tags | Ne |  Ne |
| Výrazy | Ne |  Ne |
| UsageDetails | Ne |  Ne |

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| containerGroups | Ano | Ano |
| serviceAssociationLinks | Ne |  Ne |

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| registries | Ano | Ano |
| Registry/sestavení | Ne |  Ne |
| Registry/sestavení/zrušit | Ne |  Ne |
| Registry/sestavení/getLogLink | Ne |  Ne |
| Registry/buildTasks | Ano | Ano |
| Registry/buildTasks/kroky | Ne |  Ne |
| registries/eventGridFilters | Ne |  Ne |
| registries/getBuildSourceUploadUrl | Ne |  Ne |
| Registry/GetCredentials | Ne |  Ne |
| registries/importImage | Ne |  Ne |
| registries/queueBuild | Ne |  Ne |
| Registry/regenerateCredential | Ne |  Ne |
| Registry/regenerateCredentials | Ne |  Ne |
| registries/replications | Ano | Ano |
| Registry/spuštění | Ne |  Ne |
| Registry/spuštění a zrušit | Ne |  Ne |
| registries/scheduleRun | Ne |  Ne |
| registries/tasks | Ano | Ano |
| registries/updatePolicies | Ne |  Ne |
| registries/webhooks | Ano | Ano |
| registries/webhooks/getCallbackConfig | Ne |  Ne |
| Registry a webhooky/příkaz ping | Ne |  Ne |

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| containerServices | Ano | Ano |
| managedClusters | Ano | Ano |

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| applications | Ano | Ano |
| updateCommunicationPreference | Ne |  Ne |

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Výstrahy | Ne |  Ne |
| BillingAccounts | Ne |  Ne |
| Konektory | Ano | Ano |
| Oddělení | Ne |  Ne |
| Dimenze | Ne |  Ne |
| enrollmentAccounts | Ne |  Ne |
| Dotaz | Ne |  Ne |
| Registrace | Ne |  Ne |
| Reportconfigs | Ne |  Ne |
| Sestavy | Ne |  Ne |

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| hubs | Ano | Ano |
| rozbočovače/authorizationPolicies | Ne |  Ne |
| rozbočovače a konektorů | Ne |  Ne |
| rozbočovače a konektorů/mapování | Ne |  Ne |
| rozbočovače a interakce | Ne |  Ne |
| hubs/kpi | Ne |  Ne |
| rozbočovače/odkazy | Ne |  Ne |
| rozbočovače a profily | Ne |  Ne |
| rozbočovače/roleAssignments | Ne |  Ne |
| rozbočovače/role | Ne |  Ne |
| hubs/suggestTypeSchema | Ne |  Ne |
| rozbočovače a zobrazení | Ne |  Ne |
| rozbočovače/widgetTypes | Ne |  Ne |

## <a name="microsoftdatabox"></a>Microsoft.DataBox
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| jobs | Ano | Ano |

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| DataBoxEdgeDevices | Ano | Ano |

## <a name="microsoftdatabricks"></a>Microsoft.Databricks
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| workspaces | Ano | Ne |
| workspaces/virtualNetworkPeerings | Ne |  Ne |

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| catalogs | Ano | Ano |

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| connectionManagers | Ano | Ano |

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| dataFactories | Ano | Ne |
| dataFactories/diagnosticSettings | Ne |  Ne |
| dataFactorySchema | Ne |  Ne |
| factories | Ano | Ne |
| objekty pro vytváření/integrationRuntimes | Ne |  Ne |

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |
| accounts/dataLakeStoreAccounts | Ne |  Ne |
| účty/storageAccounts | Ne |  Ne |
| accounts/storageAccounts/containers | Ne |  Ne |

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |
| accounts/eventGridFilters | Ne |  Ne |
| účty/firewallRules | Ne |  Ne |

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| services | Ano | Ano |
| services/projects | Ano | Ano |

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| servers | Ano | Ano |
| servery pro/recoverableServers | Ne |  Ne |
| servery pro/virtualNetworkRules | Ne |  Ne |

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| servers | Ano | Ano |
| servery pro/recoverableServers | Ne |  Ne |
| servery pro/virtualNetworkRules | Ne |  Ne |

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| servers | Ano | Ano |
| servery pro/poradci | Ne |  Ne |
| servers/queryTexts | Ne |  Ne |
| servery pro/recoverableServers | Ne |  Ne |
| servers/topQueryStatistics | Ne |  Ne |
| servery pro/virtualNetworkRules | Ne |  Ne |
| servers/waitStatistics | Ne |  Ne |

## <a name="microsoftdevices"></a>Microsoft.Devices
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| IotHubs | Ano | Ano |
| IotHubs/eventGridFilters | Ne |  Ne |
| ProvisioningServices | Ano | Ano |
| Použití | Ne |  Ne |

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Kontrolery | Ano | Ano |

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| labs | Ano | Ano |
| praktická cvičení/serviceRunners | Ano | Ano |
| labs/virtualMachines | Ano | Ano |
| schedules | Ano | Ano |

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| databaseAccountNames | Ne |  Ne |
| databaseAccounts | Ano | Ano |

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| domains | Ano | Ano |
| domény/domainOwnershipIdentifiers | Ne |  Ne |
| generateSsoRequest | Ne |  Ne |
| topLevelDomains | Ne |  Ne |
| validateDomainRegistrationInformation | Ne |  Ne |

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| lcsprojects | Ne |  Ne |
| lcsprojects/clouddeployments | Ne |  Ne |
| lcsprojects nebo konektory | Ne |  Ne |

## <a name="microsofteventgrid"></a>Microsoft.EventGrid
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| domains | Ano | Ne |
| domény a témat | Ne |  Ne |
| eventSubscriptions | Ne |  Ne |
| extensionTopics | Ne |  Ne |
| topics | Ano | Ne |
| topicTypes | Ne |  Ne |

## <a name="microsofteventhub"></a>Microsoft.EventHub
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| clusters | Ano | Ne |
| namespaces | Ano | Ne |
| obory názvů a autorizačních pravidel | Ne |  Ne |
| obory názvů/disasterrecoveryconfigs | Ne |  Ne |
| obory názvů/eventhubs | Ne |  Ne |
| obory názvů/eventhubs/autorizačních pravidel | Ne |  Ne |
| namespaces/eventhubs/consumergroups | Ne |  Ne |

## <a name="microsoftfeatures"></a>Microsoft.Features
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Database | Ne |  Ne |
| Zprostředkovatelé | Ne |  Ne |

## <a name="microsoftgallery"></a>Microsoft.Gallery
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Zaregistrovat | Ne |  Ne |
| galleryitems | Ne |  Ne |
| generateartifactaccessuri | Ne |  Ne |
| myareas | Ne |  Ne |
| myareas/oblastí | Ne |  Ne |
| myareas/oblasti a oblasti | Ne |  Ne |
| myareas/oblasti a oblasti/galleryitems | Ne |  Ne |
| myareas/oblasti/galleryitems | Ne |  Ne |
| myareas/galleryitems | Ne |  Ne |
| Registrace | Ne |  Ne |
| Prostředky | Ne |  Ne |
| retrieveresourcesbyid | Ne |  Ne |

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| guestConfigurationAssignments | Ne |  Ne |
| software | Ne |  Ne |

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| hanaInstances | Ano |  Ano |

## <a name="microsofthdinsight"></a>Microsoft.HDInsight
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| clusters | Ano | Ano |
| clustery/aplikace | Ne |  Ne |

## <a name="microsoftimportexport"></a>Microsoft.ImportExport
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| jobs | Ano | Ano |

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| labelGroups | Ne |  Ne |
| labelGroups/popisky | Ne |  Ne |
| labelGroups/labels/conditions | Ne |  Ne |
| labelGroups/labels/subLabels | Ne |  Ne |
| labelGroups/labels/subLabels/conditions | Ne |  Ne |

## <a name="microsoftinsights"></a>microsoft.insights
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| actiongroups | Ano | Ano |
| activityLogAlerts | Ano | Ano |
| alertrules | Ano | Ano |
| automatedExportSettings | Ne |  Ne |
| autoscalesettings | Ano | Ano |
| Standardní hodnoty | Ne |  Ne |
| calculatebaseline | Ne |  Ne |
| components | Ano | Ano |
| součásti/události | Ne |  Ne |
| components/pricingPlans | Ne |  Ne |
| součásti či dotazu | Ne |  Ne |
| diagnosticSettings | Ne |  Ne |
| diagnosticSettingsCategories | Ne |  Ne |
| eventCategories | Ne |  Ne |
| eventtypes | Ne |  Ne |
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
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Ne |  Ne |
| diagnosticSettingsCategories | Ne |  Ne |

## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| IoTApps | Ano | Ano |

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Graph | Ano | Ano |

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| deletedVaults | Ne |  Ne |
| vaults | Ano | Ano |
| trezory/accessPolicies | Ne |  Ne |
| Trezory/tajných klíčů | Ne |  Ne |

## <a name="microsoftkusto"></a>Microsoft.Kusto
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| clusters | Ano | Ano |
| clustery a databází | Ne |  Ne |
| clustery/databází/dataconnections | Ne |  Ne |
| clustery/databází/eventhubconnections | Ne |  Ne |

## <a name="microsoftlabservices"></a>Microsoft.LabServices
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| labaccounts | Ano | Ano |
| uživatelé | Ne |  Ne |

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| logs | Ne |  Ne |

## <a name="microsoftlogic"></a>Microsoft.Logic
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| integrationAccounts | Ano | Ano |
| workflows | Ano | Ano |

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| commitmentPlans | Ano | Ano |
| Webové služby | Ano | Ano |
| Pracovní prostory | Ano | Ano |

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |
| accounts/workspaces | Ano | Ano |
| accounts/workspaces/projects | Ano | Ano |
| teamAccounts | Ano | Ano |
| teamAccounts/pracovní prostory | Ano | Ano |
| teamAccounts/pracovních prostorů a projektů | Ano | Ano |

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| workspaces | Ano | Ano |
| pracovní prostory a výpočetní prostředí | Ne |  Ne |

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Identity | Ne |  Ne |
| userAssignedIdentities | Ano | 

## <a name="microsoftmanagement"></a>Microsoft.Management
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| getEntities | Ne |  Ne |
| managementGroups | Ne |  Ne |
| Prostředky | Ne |  Ne |
| startTenantBackfill | Ne |  Ne |
| tenantBackfillStatus | Ne |  Ne |

## <a name="microsoftmaps"></a>Microsoft.Maps
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |
| accounts/eventGridFilters | Ne |  Ne |

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Nabízí | Ne |  Ne |
| offerTypes | Ne |  Ne |
| offerTypes/zdroje | Ne |  Ne |
| offerTypes/vydavatele a nabídky | Ne |  Ne |
| offerTypes/publishers/offers/plans | Ne |  Ne |
| offerTypes/publishers/offers/plans/agreements | Ne |  Ne |
| offerTypes/publishers/offers/plans/configs | Ne |  Ne |
| offerTypes/publishers/offers/plans/configs/importImage | Ne |  Ne |
| privategalleryitems | Ne |  Ne |
| Produkty | Ne |  Ne |

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| classicDevServices | Ano | Ano |
| updateCommunicationPreference | Ne |  Ne |

## <a name="microsoftmarketplaceordering"></a>Microsoft.MarketplaceOrdering
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Smlouvy | Ne |  Ne |
| offertypes | Ne |  Ne |

## <a name="microsoftmedia"></a>Microsoft.Media
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| mediaservices | Ano | Ano |
| mediaservices/accountFilters | Ne |  Ne |
| mediaservices/assets | Ne |  Ne |
| mediaservices/assets/assetFilters | Ne |  Ne |
| mediaservices/contentKeyPolicies | Ne |  Ne |
| mediaservices/eventGridFilters | Ne |  Ne |
| mediaservices/liveEventOperations | Ne |  Ne |
| mediaservices/liveEvents | Ano | Ano |
| mediaservices/liveEvents/liveOutputs | Ne |  Ne |
| mediaservices/liveOutputOperations | Ne |  Ne |
| mediaservices/streamingEndpointOperations | Ne |  Ne |
| mediaservices/streamingEndpoints | Ano | Ano |
| mediaservices/streamingLocators | Ne |  Ne |
| mediaservices/streamingPolicies | Ne |  Ne |
| mediaservices/transforms | Ne |  Ne |
| mediaservices/transforms/jobs | Ne |  Ne |

## <a name="microsoftmigrate"></a>Microsoft.Migrate
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| projects | Ano | Ano |

## <a name="microsoftnetwork"></a>Microsoft.Network
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
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
| dnszones/all | Ne |  Ne |
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
| frontdoors | Ano, ale omezené (viz [poznámka níže](#frontdoor)) | Ano |
| frontdoorWebApplicationFirewallPolicies | Ano, ale omezené (viz [poznámka níže](#frontdoor)) | Ano |
| getDnsResourceReference | Ne |  Ne |
| interfaceEndpoints | Ano | Ano |
| internalNotify | Ne |  Ne |
| Load Balancerech | Ano | Ne |
| localNetworkGateways | Ano | Ano |
| natGateways | Ano | Ano |
| networkIntentPolicies | Ano | Ano |
| networkInterfaces | Ano | Ano |
| networkProfiles | Ano | Ano |
| networkSecurityGroups | Ano | Ano |
| networkWatchers | Ano | Ne |
| networkWatchers/connectionMonitors | Ano | Ne |
| networkWatchers/přehledů | Ano | Ne |
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
| virtualNetworkGateways | Ano | Ne |
| virtualNetworks | Ano | Ano |
| virtualNetworks/subnets | Ne |  Ne |
| virtualNetworkTaps | Ano | Ano |
| virtualWans | Ano | Ano |
| vpnGateways | Ano | Ne |
| vpnSites | Ano | Ano |
| webApplicationFirewallPolicies | Ano | Ano |

<a id="frontdoor" />

Branou služby Azure můžete použít značky při vytváření prostředku, ale aktualizaci nebo přidávání značek se momentálně nepodporuje.

## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| namespaces | Ano | Ne |
| namespaces/notificationHubs | Ano | Ne |

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| zařízení | Ne |  Ne |
| linkTargets | Ne |  Ne |
| storageInsightConfigs | Ne |  Ne |
| workspaces | Ano | Ano |
| pracovní prostory/zdroje dat | Ne |  Ne |
| pracovní prostory/linkedServices | Ne |  Ne |
| pracovní prostory/dotazu | Ne |  Ne |

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| managementassociations | Ne |  Ne |
| managementconfigurations | Ano | Ano |
| solutions | Ano | Ano |
| views | Ano | Ano |

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| policyEvents | Ne |  Ne |
| policyStates | Ne |  Ne |
| policyTrackedResources | Ne |  Ne |
| nápravy | Ne |  Ne |

## <a name="microsoftportal"></a>Microsoft.Portal
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| konzoly nástroje | Ne |  Ne |
| dashboards | Ano | Ano |
| userSettings | Ne |  Ne |

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| workspaceCollections | Ano | Ano |

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| capacities | Ano | Ano |

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| accounts | Ano | Ano |

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| backupProtectedItems | Ne |  Ne |
| vaults | Ano | Ano |

## <a name="microsoftrelay"></a>Microsoft.Relay
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| namespaces | Ano | Ano |
| obory názvů a autorizačních pravidel | Ne |  Ne |
| obory názvů/hybridconnections | Ne |  Ne |
| obory názvů/hybridconnections/autorizačních pravidel | Ne |  Ne |
| namespaces/wcfrelays | Ne |  Ne |
| obory názvů/wcfrelays/autorizačních pravidel | Ne |  Ne |

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Prostředky | Ne |  Ne |
| subscriptionsStatus | Ne |  Ne |

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| availabilityStatuses | Ne |  Ne |
| childAvailabilityStatuses | Ne |  Ne |
| childResources | Ne |  Ne |
| stránka events | Ne |  Ne |
| impactedResources | Ne |  Ne |
| Oznámení | Ne |  Ne |

## <a name="microsoftresources"></a>Microsoft.Resources
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| Nasazení | Ne |  Ne |
| Nasazení/operace | Ne |  Ne |
| Odkazy | Ne |  Ne |
| notifyResourceJobs | Ne |  Ne |
| Zprostředkovatelé | Ne |  Ne |
| resourceGroups | Ne |  Ne |
| Prostředky | Ne |  Ne |
| Předplatná | Ne |  Ne |
| Předplatná a poskytovatelé | Ne |  Ne |
| subscriptions/resourceGroups | Ne |  Ne |
| předplatné/resourcegroups/prostředky | Ne |  Ne |
| Předplatná a prostředky | Ne |  Ne |
| předplatné/tagnames | Ne |  Ne |
| subscriptions/tagNames/tagValues | Ne |  Ne |
| Tenanti | Ne |  Ne |

## <a name="microsoftsaas"></a>Microsoft.SaaS
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| applications | Ano | Ano |
| saasresources | Ne |  Ne |

## <a name="microsoftscheduler"></a>Microsoft.Scheduler
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| flows | Ano | Ano |
| jobcollections | Ano | Ano |

## <a name="microsoftsearch"></a>Microsoft.Search
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| resourceHealthMetadata | Ne |  Ne |
| searchServices | Ano | Ano |

## <a name="microsoftsecurity"></a>Microsoft.Security
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| advancedThreatProtectionSettings | Ne |  Ne |
| výstrahy | Ne |  Ne |
| allowedConnections | Ne |  Ne |
| appliances | Ne |  Ne |
| applicationWhitelistings | Ne |  Ne |
| AutoProvisioningSettings | Ne |  Ne |
| Dodržování předpisů | Ne |  Ne |
| dataCollectionAgents | Ne |  Ne |
| discoveredSecuritySolutions | Ne |  Ne |
| externalSecuritySolutions | Ne |  Ne |
| InformationProtectionPolicies | Ne |  Ne |
| jitNetworkAccessPolicies | Ne |  Ne |
| Monitorování | Ne |  Ne |
| Monitoring/antimalwaru | Ne |  Ne |
| monitorování a standardních hodnot | Ne |  Ne |
| Monitoring/patch | Ne |  Ne |
| Zásady | Ne |  Ne |
| ceny | Ne |  Ne |
| securityContacts | Ne |  Ne |
| securitySolutions | Ne |  Ne |
| securitySolutionsReferenceData | Ne |  Ne |
| securityStatus | Ne |  Ne |
| securityStatus/endpoints | Ne |  Ne |
| securityStatus/subnets | Ne |  Ne |
| securityStatus/virtualMachines | Ne |  Ne |
| securityStatuses | Ne |  Ne |
| securityStatusesSummaries | Ne |  Ne |
| settings | Ne |  Ne |
| úlohy | Ne |  Ne |
| Topologie | Ne |  Ne |
| workspaceSettings | Ne |  Ne |

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Ne |  Ne |
| diagnosticSettingsCategories | Ne |  Ne |

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| namespaces | Ano | Ne |
| obory názvů a autorizačních pravidel | Ne |  Ne |
| obory názvů/disasterrecoveryconfigs | Ne |  Ne |
| obory názvů/eventgridfilters | Ne |  Ne |
| obory názvů/fronty | Ne |  Ne |
| obory názvů/fronty/autorizačních pravidel | Ne |  Ne |
| obory názvů a témat | Ne |  Ne |
| obory názvů nebo témat/autorizačních pravidel | Ne |  Ne |
| obory názvů předplatná | Ne |  Ne |
| obory názvů nebo témata/předplatná/pravidla | Ne |  Ne |
| premiumMessagingRegions | Ne |  Ne |

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| clusters | Ano | Ano |
| clustery/aplikace | Ne |  Ne |

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| applications | Ano | Ano |
| gateways | Ano | Ano |
| networks | Ano | Ano |
| Tajné kódy | Ano | Ano |
| volumes | Ano | Ano |

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| SignalR | Ano | Ano |

## <a name="microsoftsolutions"></a>Microsoft.Solutions
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| applianceDefinitions | Ano | Ano |
| appliances | Ano | Ano |
| applicationDefinitions | Ano | Ano |
| applications | Ano | Ano |
| jitRequests | Ano | Ano |

## <a name="microsoftsql"></a>Microsoft.SQL
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| managedInstances | Ano | Ano |
| managedInstances/databáze | Ano (viz poznámka níže) | Ano |
| managedInstances/databases/backupShortTermRetentionPolicies | Ne | Ne |
| managedInstances/databases/schemas/tables/columns/sensitivityLabels | Ne | Ne |
| managedInstances/databases/vulnerabilityAssessments | Ne | Ne |
| managedInstances/databases/vulnerabilityAssessments/rules/baselines | Ne | Ne |
| managedInstances/encryptionProtector | Ne | Ne |
| managedInstances/klíče | Ne | Ne |
| managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Ne | Ne |
| managedInstances/vulnerabilityAssessments | Ne | Ne |
| servers | Ano | Ano |
| servery pro/správci | Ne |  Ne |
| servery pro/communicationLinks | Ne |  Ne |
| servers/databases | Ano (viz poznámka níže) | Ano |
| servers/encryptionProtector | Ne |  Ne |
| servery pro/firewallRules | Ne |  Ne |
| servery pro/klíče | Ne |  Ne |
| servers/restorableDroppedDatabases | Ne |  Ne |
| servery pro/serviceobjectives | Ne |  Ne |
| servers/tdeCertificates | Ne |  Ne |

> [!NOTE]
> Hlavní databáze nepodporuje značky, ale další databáze, včetně databází Azure SQL Data Warehouse, podporují značky. Databáze Azure SQL Data Warehouse, musí být v aktivní (není pozastavená) stavu.


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| SqlVirtualMachineGroups | Ano | Ano |
| SqlVirtualMachineGroups/AvailabilityGroupListeners | Ne |  Ne |
| SqlVirtualMachines | Ano | Ano |

## <a name="microsoftstorage"></a>Microsoft.Storage
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| storageAccounts | Ano | Ano |
| storageAccounts/blobServices | Ne |  Ne |
| storageAccounts/fileServices | Ne |  Ne |
| storageAccounts/queueServices | Ne |  Ne |
| storageAccounts/services | Ne |  Ne |
| storageAccounts/tableServices | Ne |  Ne |
| Použití | Ne |  Ne |

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| storageSyncServices | Ano | Ano |
| storageSyncServices/registeredServers | Ne |  Ne |
| storageSyncServices/syncGroups | Ne |  Ne |
| storageSyncServices/syncGroups/cloudEndpoints | Ne |  Ne |
| storageSyncServices/syncGroups/serverEndpoints | Ne |  Ne |
| storageSyncServices/workflows | Ne |  Ne |

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| managers | Ano | Ano |

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| streamingjobs | Ano (viz poznámka níže) | Ano |
| streamingjobs/diagnosticSettings | Ne |  Ne |

> [!NOTE]
> Při spuštění streamingjobs nelze přidat značku. Zastavte prostředek, který chcete přidat značku.

## <a name="microsoftsubscription"></a>Microsoft.Subscription
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| CreateSubscription | Ne |  Ne |
| SubscriptionDefinitions | Ne |  Ne |
| SubscriptionOperations | Ne |  Ne |

## <a name="microsoftsupport"></a>microsoft.support
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| supporttickets | Ne |  Ne |

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| providerRegistrations | Ano | Ano |
| Prostředky | Ano | Ano |

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| environments | Ano | Ne |
| prostředí/accessPolicies | Ne |  Ne |
| environments/eventsources | Ano | Ne |
| prostředí/referenceDataSets | Ano | Ne |

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| account | Ano | Ano |
| account/extension | Ano | Ano |
| account/project | Ano | Ano |

## <a name="microsoftweb"></a>Microsoft.Web
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| apiManagementAccounts | Ne |  Ne |
| apiManagementAccounts/apiAcls | Ne |  Ne |
| apiManagementAccounts/rozhraní API | Ne |  Ne |
| apiManagementAccounts/apis/apiAcls | Ne |  Ne |
| apiManagementAccounts/apis/connectionAcls | Ne |  Ne |
| apiManagementAccounts/API/připojení | Ne |  Ne |
| apiManagementAccounts/apis/connections/connectionAcls | Ne |  Ne |
| apiManagementAccounts/API/localizedDefinitions | Ne |  Ne |
| apiManagementAccounts/connectionAcls | Ne |  Ne |
| apiManagementAccounts/připojení | Ne |  Ne |
| billingMeters | Ne |  Ne |
| certificates | Ano | Ano |
| connectionGateways | Ano | Ano |
| connections | Ano | Ano |
| customApis | Ano | Ano |
| deletedSites | Ne |  Ne |
| functions | Ne |  Ne |
| hostingEnvironments | Ano | Ano |
| hostingEnvironments/multiRolePools | Ne |  Ne |
| hostingEnvironments/multiRolePools/instances | Ne |  Ne |
| hostingEnvironments/workerPools | Ne |  Ne |
| hostingEnvironments/workerPools/instances | Ne |  Ne |
| publishingUsers | Ne |  Ne |
| Doporučení | Ne |  Ne |
| resourceHealthMetadata | Ne |  Ne |
| Moduly runtime | Ne |  Ne |
| serverFarms | Ano | Ano |
| serverFarms/workers | Ne |  Ne |
| sites | Ano | Ano |
| sites/domainOwnershipIdentifiers | Ne |  Ne |
| sites/hostNameBindings | Ne |  Ne |
| servery a instancí | Ne |  Ne |
| servery/instance/rozšíření | Ne |  Ne |
| sites/premieraddons | Ano | Ano |
| servery a doporučení | Ne |  Ne |
| sites/resourceHealthMetadata | Ne |  Ne |
| sites/slots | Ano | Ano |
| sites/slots/hostNameBindings | Ne |  Ne |
| servery/sloty/instance | Ne |  Ne |
| sites/slots/instances/extensions | Ne |  Ne |
| sourceControls | Ne |  Ne |
| Ověření | Ne |  Ne |
| verifyHostingEnvironmentVnet | Ne |  Ne |

## <a name="microsoftwindowsdefenderatp"></a>Microsoft.WindowsDefenderATP
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| diagnosticSettings | Ne |  Ne |
| diagnosticSettingsCategories | Ne |  Ne |

## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| DeviceServices | Ano | Ano |

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor
| Typ prostředku | Podporuje značky | Sestava náklady na značku |
| ------------- | ----------- | ----------- |
| components | Ne |  Ne |
| componentsSummary | Ne |  Ne |
| monitorInstances | Ne |  Ne |
| monitorInstancesSummary | Ne |  Ne |
| Monitorování | Ne |  Ne |
| notificationSettings | Ne |  Ne |

## <a name="next-steps"></a>Další postup
Zjistěte, jak použít značky na prostředky, najdete v článku [používání značek k uspořádání prostředků Azure](resource-group-using-tags.md).

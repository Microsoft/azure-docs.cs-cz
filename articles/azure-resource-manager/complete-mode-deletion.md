---
title: Azure Resource Manager odstranění režimu dokončení podle typu prostředku
description: Ukazuje, jak typy prostředků zpracovávají úplné odstraňování režimu v Azure Resource Manager šablonách.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 04/24/2019
ms.author: tomfitz
ms.openlocfilehash: 5ac442c0ae1e397fd1e8b58fdbcf61eb8712046c
ms.sourcegitcommit: af58483a9c574a10edc546f2737939a93af87b73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68302846"
---
# <a name="deletion-of-azure-resources-for-complete-mode-deployments"></a>Odstranění prostředků Azure pro nasazení v úplném režimu
Tento článek popisuje, jak typy prostředků zpracovávají odstranění, když není v šabloně, která je nasazena v úplném režimu.

Typy prostředků s `Yes` označením jsou odstraněny, když typ není v šabloně nasazené s úplným režimem. 

Typy prostředků s `No` označením nejsou automaticky odstraněny, pokud nejsou v šabloně, ale jsou odstraněny, pokud je odstraněn nadřazený prostředek. Úplný popis chování najdete v tématu [Azure Resource Manager režimy nasazení](deployment-modes.md).


## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | DomainServices | Ano | 
> | DomainServices/oucontainer | Ne | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | diagnosticSettings | Ne | 
> | diagnosticSettingsCategories | Ne | 

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
> | Technici | Ne | 
> | anonymousapiusers | Ne | 
> | konfigurace | Ne | 
> | logs | Ne | 
> | sestavy | Ne | 
> | services | Ne | 

## <a name="microsoftadvisor"></a>Microsoft.Advisor

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Konfiguračních | Ne | 
> | generateRecommendations | Ne | 
> | Doporučení | Ne | 
> | potlačení | Ne | 

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | actionRules | Ne | 
> | výstrahy | Ne | 
> | alertsList | Ne | 
> | alertsSummary | Ne | 
> | alertsSummaryList | Ne | 
> | smartDetectorAlertRules | Ne | 
> | smartDetectorRuntimeEnvironments | Ne | 
> | smartGroups | Ne | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servers | Ano | 

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | reportFeedback | Ne | 
> | service | Ano | 
> | validateServiceName | Ne | 

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
> | denyAssignments | Ne | 
> | elevateAccess | Ne | 
> | Počtu | Ne | 
> | oprávnění | Ne | 
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
> | automationAccounts/konfigurace | Ano | 
> | automationAccounts/jobs | Ne | 
> | automationAccounts/runbooks | Ano | 
> | automationAccounts/softwareUpdateConfigurations | Ne | 
> | automationAccounts/webhooks | Ne | 

## <a name="microsoftazuregeneva"></a>Microsoft.Azure.Geneva

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | environments | Ne | 
> | prostředí/účty | Ne | 
> | prostředí/účty/obory názvů | Ne | 
> | prostředí/účty/obory názvů/konfigurace | Ne | 

## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | b2cDirectories | Ano | 

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | registrations | Ano | 
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
> | billingAccounts/billingProfiles | Ne | 
> | billingAccounts/billingProfiles/billingSubscriptions | Ne | 
> | billingAccounts/billingProfiles/faktury | Ne | 
> | billingAccounts/billingProfiles/invoices/pricesheet | Ne | 
> | billingAccounts/billingProfiles/operationStatus | Ne | 
> | billingAccounts/billingProfiles/paymentMethods | Ne | 
> | billingAccounts/billingProfiles/– zásady | Ne | 
> | billingAccounts/billingProfiles/pricesheet | Ne | 
> | billingAccounts/billingProfiles/produkty | Ne | 
> | billingAccounts/billingProfiles/transactions | Ne | 
> | billingAccounts/billingSubscriptions | Ne | 
> | billingAccounts/oddělení | Ne | 
> | billingAccounts/eligibleOffers | Ne | 
> | billingAccounts/enrollmentAccounts | Ne | 
> | billingAccounts/faktury | Ne | 
> | billingAccounts/invoiceSections | Ne | 
> | billingAccounts/invoiceSections/billingSubscriptions | Ne | 
> | billingAccounts/invoiceSections/billingSubscriptions/transfer | Ne | 
> | billingAccounts/invoiceSections/importRequests | Ne | 
> | billingAccounts/invoiceSections/initiateImportRequest | Ne | 
> | billingAccounts/invoiceSections/initiateTransfer | Ne | 
> | billingAccounts/invoiceSections/operationStatus | Ne | 
> | billingAccounts/invoiceSections/produkty | Ne | 
> | billingAccounts/invoiceSections/transfery | Ne | 
> | billingAccounts/produkty | Ne | 
> | billingAccounts/projekty | Ne | 
> | billingAccounts/projekty/billingSubscriptions | Ne | 
> | billingAccounts/projekty/importRequests | Ne | 
> | billingAccounts/projects/initiateImportRequest | Ne | 
> | billingAccounts/projects/operationStatus | Ne | 
> | billingAccounts/projekty/produkty | Ne | 
> | billingAccounts/transakce | Ne | 
> | billingPeriods | Ne | 
> | BillingPermissions | Ne | 
> | billingProperty | Ne | 
> | BillingRoleAssignments | Ne | 
> | BillingRoleDefinitions | Ne | 
> | CreateBillingRoleAssignment | Ne | 
> | oddělení | Ne | 
> | enrollmentAccounts | Ne | 
> | importRequests | Ne | 
> | importRequests/acceptImportRequest | Ne | 
> | importRequests/declineImportRequest | Ne | 
> | faktury | Ne | 
> | Převede | Ne | 
> | přenosy/acceptTransfer | Ne | 
> | přenosy/declineTransfer | Ne | 
> | transfers/operationStatus | Ne | 
> | usagePlans | Ne | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | mapApis | Ano | 
> | updateCommunicationPreference | Ne | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | BizTalk | Ano | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

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

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | botServices | Ano | 
> | botServices/kanály | Ne | 
> | botServices/připojení | Ne | 

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
> | calculatePrice | Ne | 
> | catalogs | Ne | 
> | commercialReservationOrders | Ne | 
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

## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | edgenodes | Ne | 
> | profiles | Ano | 
> | profiles/endpoints | Ano | 
> | profily/koncové body/customdomains | Ne | 
> | profily/koncové body/zdroje | Ne | 
> | validateProbe | Ne | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

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
> | vestavěn | Ne | 
> | domainNames | Ne | 
> | domainNames/možnosti | Ne | 
> | domainNames/internalLoadBalancers | Ne | 
> | domainNames/serviceCertificates | Ne | 
> | domainNames/sloty | Ne | 
> | domainNames/sloty/role | Ne | 
> | moveSubscriptionResources | Ne | 
> | operatingSystemFamilies | Ne | 
> | operatingSystems | Ne | 
> | quotas | Ne | 
> | Typ prostředků | Ne | 
> | validateSubscriptionMoveAvailability | Ne | 
> | virtualMachines | Ne | 
> | virtualMachines/diagnosticSettings | Ne | 

## <a name="microsoftclassicinfrastructuremigrate"></a>Microsoft.ClassicInfrastructureMigrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | classicInfrastructureResources | Ne | 

## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | vestavěn | Ne | 
> | expressRouteCrossConnections | Ne | 
> | expressRouteCrossConnections/peerings | Ne | 
> | gatewaySupportedDevices | Ne | 
> | networkSecurityGroups | Ne | 
> | quotas | Ne | 
> | reservedIps | Ne | 
> | virtualNetworks | Ne | 
> | virtualNetworks/remoteVirtualNetworkPeeringProxies | Ne | 
> | virtualNetworks/virtualNetworkPeerings | Ne | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | vestavěn | Ne | 
> | disks | Ne | 
> | images | Ne | 
> | OSImage | Ne | 
> | osPlatformImages | Ne | 
> | publicImages | Ne | 
> | quotas | Ne | 
> | storageAccounts | Ne | 
> | storageAccounts/služby | Ne | 
> | storageAccounts/služby/diagnosticSettings | Ne | 
> | storageAccounts/vmImages | Ne | 
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
> | disks | Ano | 
> | images | Ano | 
> | restorePointCollections | Ano | 
> | restorePointCollections/restorePoints | Ne | 
> | sharedVMImages | Ano | 
> | sharedVMImages/verze | Ano | 
> | snapshots | Ano | 
> | virtualMachines | Ano | 
> | virtualMachines/diagnosticSettings | Ne | 
> | virtualMachines/extensions | Ano | 
> | virtualMachineScaleSets | Ano | 
> | virtualMachineScaleSets/extensions | Ne | 
> | virtualMachineScaleSets/networkInterfaces | Ne | 
> | virtualMachineScaleSets/publicIPAddresses | Ne | 
> | virtualMachineScaleSets/virtualMachines | Ne | 
> | virtualMachineScaleSets/virtualMachines/networkInterfaces | Ne | 

## <a name="microsoftconsumption"></a>Microsoft.Consumption

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | AggregatedCost | Ne | 
> | Zůstatky | Ne | 
> | Rozpočty | Ne | 
> | Poplatky za | Ne | 
> | CostTags | Ne | 
> | Dobropis | Ne | 
> | stránka events | Ne | 
> | Prognózy | Ne | 
> | ŠARŽ | Ne | 
> | Tržišť | Ne | 
> | Pricesheets | Ne | 
> | Produktech | Ne | 
> | ReservationDetails | Ne | 
> | ReservationRecommendations | Ne | 
> | ReservationSummaries | Ne | 
> | ReservationTransactions | Ne | 
> | Tags | Ne | 
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
> | Registry a sestavení | Ne | 
> | Registry/buildy/zrušit | Ne | 
> | Registry/buildy/getLogLink | Ne | 
> | Registry/buildTasks | Ano | 
> | Registry/buildTasks/kroky | Ne | 
> | registries/eventGridFilters | Ne | 
> | registries/getBuildSourceUploadUrl | Ne | 
> | Registry/getpřihlašovací údaje | Ne | 
> | Registry/importImage | Ne | 
> | Registry/queueBuild | Ne | 
> | Registry/regenerateCredential | Ne | 
> | Registry/regenerateCredentials | Ne | 
> | registries/replications | Ano | 
> | Registry/běhy | Ne | 
> | Registry/spuštění/zrušit | Ne | 
> | Registry/scheduleRun | Ne | 
> | registries/tasks | Ano | 
> | Registry/updatePolicies | Ne | 
> | registries/webhooks | Ano | 
> | registries/webhooks/getCallbackConfig | Ne | 
> | Registry/Webhooky/příkazy k odeslání | Ne | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | containerServices | Ano | 
> | managedClusters | Ano | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applications | Ano | 
> | updateCommunicationPreference | Ne | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Upozornění | Ne | 
> | billingAccounts | Ne | 
> | Konektory | Ano | 
> | oddělení | Ne | 
> | Dimenze | Ne | 
> | enrollmentAccounts | Ne | 
> | Dotaz | Ne | 
> | Registrace | Ne | 
> | Reportconfigs | Ne | 
> | Sestavy | Ne | 

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hubs | Ano | 
> | rozbočovače/authorizationPolicies | Ne | 
> | rozbočovače/konektory | Ne | 
> | rozbočovače/konektory/mapování | Ne | 
> | centra/interakce | Ne | 
> | centra/klíčový ukazatel výkonu | Ne | 
> | centra/odkazy | Ne | 
> | centra/profily | Ne | 
> | rozbočovače/roleAssignments | Ne | 
> | centra/role | Ne | 
> | rozbočovače/suggestTypeSchema | Ne | 
> | centra/zobrazení | Ne | 
> | rozbočovače/widgetTypes | Ne | 

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
> | pracovní prostory/virtualNetworkPeerings | Ne | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | catalogs | Ano | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | connectionManagers | Ano | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Objekty DataFactory | Ano | 
> | DataFactory/diagnosticSettings | Ne | 
> | dataFactorySchema | Ne | 
> | factories | Ano | 
> | továrny/integrationRuntimes | Ne | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano | 
> | accounts/dataLakeStoreAccounts | Ne | 
> | účty/storageAccounts | Ne | 
> | účty/storageAccounts/kontejnery | Ne | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano | 
> | účty/eventGridFilters | Ne | 
> | účty/firewallRules | Ne | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | services | Ano | 
> | services/projects | Ano | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servers | Ano | 
> | servery/recoverableServers | Ne | 
> | servery/virtualNetworkRules | Ne | 

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servers | Ano | 
> | servery/recoverableServers | Ne | 
> | servery/virtualNetworkRules | Ne | 

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | servers | Ano | 
> | servery/poradci | Ne | 
> | servers/queryTexts | Ne | 
> | servery/recoverableServers | Ne | 
> | servers/topQueryStatistics | Ne | 
> | servery/virtualNetworkRules | Ne | 
> | servery/waitStatistics | Ne | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | IotHubs | Ano | 
> | IotHubs/eventGridFilters | Ne | 
> | ProvisioningServices | Ano | 
> | použití | Ne | 

## <a name="microsoftdevspaces"></a>Microsoft. DevSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Kontrolou | Ano | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | labs | Ano | 
> | Labs/serviceRunners | Ano | 
> | Labs/virtualMachines | Ano | 
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
> | domény/domainOwnershipIdentifiers | Ne | 
> | generateSsoRequest | Ne | 
> | topLevelDomains | Ne | 
> | validateDomainRegistrationInformation | Ne | 

## <a name="microsoftdynamicslcs"></a>Microsoft.DynamicsLcs

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | lcsprojects | Ne | 
> | lcsprojects/clouddeployments | Ne | 
> | lcsprojects/konektory | Ne | 

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | domains | Ano | 
> | domény a témata | Ne | 
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
> | obory názvů/autorizačních pravidel | Ne | 
> | obory názvů/disasterrecoveryconfigs | Ne | 
> | obory názvů/eventhubs | Ne | 
> | obory názvů/eventhubs/autorizačních pravidel | Ne | 
> | obory názvů/eventhubs/consumergroups | Ne | 

## <a name="microsoftfeatures"></a>Microsoft.Features

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Database | Ne | 
> | Zprostředkovatelé | Ne | 

## <a name="microsoftgallery"></a>Microsoft.Gallery

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

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | guestConfigurationAssignments | Ne | 
> | software | Ne | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | hanaInstances | Ano | 

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | clusters | Ano | 
> | clustery/aplikace | Ne | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | jobs | Ano | 

## <a name="microsoftinformationprotection"></a>Microsoft.InformationProtection

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | labelGroups | Ne | 
> | labelGroups/popisky | Ne | 
> | labelGroups/štítky/podmínky | Ne | 
> | labelGroups/popisky/podjmenovky | Ne | 
> | labelGroups/labels/subLabels/conditions | Ne | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | actiongroups | Ano | 
> | activityLogAlerts | Ano | 
> | alertrules | Ano | 
> | automatedExportSettings | Ne | 
> | autoscalesettings | Ano | 
> | směrný | Ne | 
> | calculatebaseline | Ne | 
> | components | Ano | 
> | komponenty/události | Ne | 
> | komponenty/pricingPlans | Ne | 
> | komponenty a dotazy | Ne | 
> | diagnosticSettings | Ne | 
> | diagnosticSettingsCategories | Ne | 
> | eventCategories | Ne | 
> | EventType | Ne | 
> | extendedDiagnosticSettings | Ne | 
> | logDefinitions | Ne | 
> | logprofiles | Ne | 
> | logs | Ne | 
> | metricAlerts | Ano |
> | migrateToNewPricingModel | Ne | 
> | myWorkbooks | Ne | 
> | dotazy | Ne | 
> | rollbackToLegacyPricingModel | Ne | 
> | scheduledqueryrules | Ano | 
> | vmInsightsOnboardingStatuses | Ne | 
> | webtests | Ano | 
> | workbooks | Ano | 

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
> | IoTApps | Ano | 

## <a name="microsoftiotspaces"></a>Microsoft. IoTSpaces

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Graph | Ano | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | deletedVaults | Ne | 
> | vaults | Ano | 
> | trezory/accessPolicies | Ne | 
> | trezory/tajné klíče | Ne | 

## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | clusters | Ano | 
> | clustery/databáze | Ne | 
> | clustery, databáze/datapřipojení | Ne | 
> | clustery/databáze/eventhubconnections | Ne | 

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | labaccounts | Ano | 
> | uživatelé | Ne | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano | 

## <a name="microsoftloganalytics"></a>Microsoft.LogAnalytics

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | logs | Ne | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | integrationAccounts | Ano | 
> | workflows | Ano | 

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | commitmentPlans | Ano | 
> | webServices | Ano | 
> | Pracovní prostory | Ano | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft. MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano | 
> | accounts/workspaces | Ano | 
> | accounts/workspaces/projects | Ano | 
> | teamAccounts | Ano | 
> | teamAccounts/pracovní prostory | Ano | 
> | teamAccounts/pracovní prostory/projekty | Ano | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices
> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | workspaces | Ano | 
> | pracovní prostory a výpočetní prostředky | Ne | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Nebyly | Ne | 
> | userAssignedIdentities | Ano | 

## <a name="microsoftmanagement"></a>Microsoft.Management

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | getentities | Ne | 
> | managementGroups | Ne | 
> | Prostředky | Ne | 
> | startTenantBackfill | Ne | 
> | tenantBackfillStatus | Ne | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano | 
> | účty/eventGridFilters | Ne | 

## <a name="microsoftmarketplace"></a>Microsoft.Marketplace

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Rozsah | Ne | 
> | offerTypes | Ne | 
> | offerTypes/vydavatelé | Ne | 
> | offerTypes/vydavatelé/nabídky | Ne | 
> | offerTypes/vydavatelé/nabídky/plány | Ne | 
> | offerTypes/vydavatelé/nabídky/plány/smlouvy | Ne | 
> | offerTypes/publishers/offers/plans/configs | Ne | 
> | offerTypes/publishers/offers/plans/configs/importImage | Ne | 
> | privategalleryitems | Ne | 
> | Produktech | Ne | 

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
> | o | Ne | 
> | offertypes | Ne | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | MediaServices | Ano | 
> | mediaservices/accountFilters | Ne | 
> | MediaServices/assety | Ne | 
> | mediaservices/assets/assetFilters | Ne | 
> | mediaservices/contentKeyPolicies | Ne | 
> | mediaservices/eventGridFilters | Ne | 
> | MediaServices/liveEventOperations | Ne | 
> | MediaServices/liveEvents | Ano | 
> | MediaServices/liveEvents/liveOutputs | Ne | 
> | mediaservices/liveOutputOperations | Ne | 
> | MediaServices/streamingEndpointOperations | Ne | 
> | MediaServices/starají | Ano | 
> | MediaServices/streamingLocators | Ne | 
> | MediaServices/streamingPolicies | Ne | 
> | MediaServices/transformace | Ne | 
> | MediaServices/transformace/úlohy | Ne | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | projects | Ano | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applicationGateways | Ano | 
> | applicationSecurityGroups | Ano | 
> | azureFirewallFqdnTags | Ne | 
> | azureFirewalls | Ano | 
> | bgpServiceCommunities | Ne | 
> | connections | Ano | 
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
> | expressRouteServiceProviders | Ne | 
> | frontdoors | Ano | 
> | frontdoorWebApplicationFirewallPolicies | Ano | 
> | getDnsResourceReference | Ne | 
> | interfaceEndpoints | Ano | 
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
> | privateLinkServices | Ano | 
> | publicIPAddresses | Ano | 
> | publicIPPrefixes | Ano | 
> | routeFilters | Ano | 
> | routeTables | Ano | 
> | serviceEndpointPolicies | Ano | 
> | trafficManagerGeographicHierarchies | Ne | 
> | trafficmanagerprofiles | Ano | 
> | trafficmanagerprofiles/heatMaps | Ne | 
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
> | obory názvů/notificationHubs | Ano | 

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | zařízení | Ne | 
> | linkTargets | Ne | 
> | storageInsightConfigs | Ne | 
> | workspaces | Ano | 
> | pracovní prostory/zdroje dat | Ne | 
> | pracovní prostory/linkedServices | Ne | 
> | pracovní prostory a dotazy | Ne | 

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | managementassociations | Ne | 
> | managementconfigurations | Ano | 
> | solutions | Ano | 
> | views | Ano | 

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | policyEvents | Ne | 
> | policyStates | Ne | 
> | policyTrackedResources | Ne | 
> | nápravy | Ne | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Console | Ne | 
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

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | accounts | Ano | 

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
> | obory názvů/autorizačních pravidel | Ne | 
> | obory názvů/hybridconnections | Ne | 
> | obory názvů/hybridconnections/autorizačních pravidel | Ne | 
> | obory názvů/wcfrelays | Ne | 
> | obory názvů/wcfrelays/autorizačních pravidel | Ne | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Prostředky | Ne | 
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
> | Připomenutí | Ne | 

## <a name="microsoftresources"></a>Microsoft. Resources

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | Nasazení | Ne | 
> | nasazení/operace | Ne | 
> | Odkazy | Ne | 
> | notifyResourceJobs | Ne | 
> | Zprostředkovatelé | Ne | 
> | resourceGroups | Ne | 
> | Prostředky | Ne | 
> | Odběru | Ne | 
> | předplatná/poskytovatelé | Ne | 
> | subscriptions/resourceGroups | Ne | 
> | předplatná/ResourceGroups/prostředky | Ne | 
> | předplatná/prostředky | Ne | 
> | předplatná/TagNames | Ne | 
> | subscriptions/tagNames/tagValues | Ne | 
> | tenantů | Ne | 

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applications | Ano | 
> | saasresources | Ne | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | flows | Ano | 
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
> | advancedThreatProtectionSettings | Ne | 
> | výstrahy | Ne | 
> | allowedConnections | Ne | 
> | appliances | Ne | 
> | applicationWhitelistings | Ne | 
> | AutoProvisioningSettings | Ne | 
> | Předpisů | Ne | 
> | dataCollectionAgents | Ne | 
> | discoveredSecuritySolutions | Ne | 
> | externalSecuritySolutions | Ne | 
> | InformationProtectionPolicies | Ne | 
> | jitNetworkAccessPolicies | Ne | 
> | Sledovaný | Ne | 
> | monitorování/antimalware | Ne | 
> | monitorování/směrný plán | Ne | 
> | monitorování/Oprava | Ne | 
> | Konfigurovaný | Ne | 
> | ceny | Ne | 
> | securityContacts | Ne | 
> | securitySolutions | Ne | 
> | securitySolutionsReferenceData | Ne | 
> | securityStatus | Ne | 
> | securityStatus/koncové body | Ne | 
> | securityStatus/subnets | Ne | 
> | securityStatus/virtualMachines | Ne | 
> | securityStatuses | Ne | 
> | securityStatusesSummaries | Ne | 
> | settings | Ne | 
> | úlohy | Ne | 
> | topologie | Ne | 
> | workspaceSettings | Ne | 

## <a name="microsoftsecuritygraph"></a>Microsoft.SecurityGraph

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | diagnosticSettings | Ne | 
> | diagnosticSettingsCategories | Ne | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | namespaces | Ano | 
> | obory názvů/autorizačních pravidel | Ne | 
> | obory názvů/disasterrecoveryconfigs | Ne | 
> | obory názvů/eventgridfilters | Ne | 
> | obory názvů/fronty | Ne | 
> | obory názvů/fronty/autorizačních pravidel | Ne | 
> | obory názvů/témata | Ne | 
> | obory názvů/témata/autorizačních pravidel | Ne | 
> | obory názvů/témata/předplatná | Ne | 
> | obory názvů/témata/předplatná/pravidla | Ne | 
> | premiumMessagingRegions | Ne | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | clusters | Ano | 
> | clustery/aplikace | Ne | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applications | Ano | 
> | brány | Ano | 
> | networks | Ano | 
> | záleží | Ano | 
> | volumes | Ano | 

## <a name="microsoftsignalrservice"></a>Microsoft. SignalRService

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | SignalR | Ano | 

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | applianceDefinitions | Ano | 
> | appliances | Ano | 
> | applicationDefinitions | Ano | 
> | applications | Ano | 
> | jitRequests | Ano | 

## <a name="microsoftsql"></a>Microsoft.SQL

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | managedInstances | Ano |
> | managedInstances/databáze | Ano |
> | managedInstances/databases/backupShortTermRetentionPolicies | Ne |
> | managedInstances/databases/schemas/tables/columns/sensitivityLabels | Ne |
> | managedInstances/databáze/vulnerabilityAssessments | Ne |
> | managedInstances/databases/vulnerabilityAssessments/rules/baselines | Ne |
> | managedInstances/encryptionProtector | Ne |
> | managedInstances/klíče | Ne |
> | managedInstances/restorableDroppedDatabases/backupShortTermRetentionPolicies | Ne |
> | managedInstances/vulnerabilityAssessments | Ne |
> | servers | Ano | 
> | servery/správci | Ne | 
> | servery/communicationLinks | Ne | 
> | servers/databases | Ano | 
> | servery/encryptionProtector | Ne | 
> | servery/firewallRules | Ne | 
> | servery/klíče | Ne | 
> | servery/restorableDroppedDatabases | Ne | 
> | servery/serviceobjectives | Ne | 
> | servery/tdeCertificates | Ne | 


## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

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
> | storageAccounts/tableServices | Ne | 
> | použití | Ne | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | storageSyncServices | Ano | 
> | storageSyncServices/registeredServer | Ne | 
> | storageSyncServices/syncGroups | Ne | 
> | storageSyncServices/syncGroups/cloudEndpoints | Ne | 
> | storageSyncServices/syncGroups/serverEndpoints | Ne | 
> | storageSyncServices/pracovní postupy | Ne | 

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
> | streamingjobs/diagnosticSettings | Ne | 

## <a name="microsoftsubscription"></a>Microsoft. Subscription

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | CreateSubscription | Ne | 
> | SubscriptionDefinitions | Ne | 
> | SubscriptionOperations | Ne | 

## <a name="microsoftsupport"></a>microsoft.support

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | supporttickets | Ne | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | providerRegistrations | Ano | 
> | Prostředky | Ano | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | environments | Ano | 
> | prostředí/accessPolicies | Ne | 
> | environments/eventsources | Ano | 
> | prostředí/referenceDataSets | Ano | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | account | Ano | 
> | account/extension | Ano | 
> | account/project | Ano | 

## <a name="microsoftweb"></a>Microsoft.Web

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
> | certificates | Ano | 
> | connectionGateways | Ano | 
> | connections | Ano | 
> | customApis | Ano | 
> | deletedSites | Ne | 
> | – funkce | Ne | 
> | hostingEnvironments | Ano | 
> | hostingEnvironments/multiRolePools | Ne | 
> | hostingEnvironments/multiRolePools/instance | Ne | 
> | hostingEnvironments/workerPools | Ne | 
> | hostingEnvironments/workerPools/instance | Ne | 
> | publishingUsers | Ne | 
> | Doporučení | Ne | 
> | resourceHealthMetadata | Ne | 
> | moduly runtime | Ne | 
> | serverFarms | Ano | 
> | Serverových farem/pracovní procesy | Ne | 
> | sites | Ano | 
> | lokality/domainOwnershipIdentifiers | Ne | 
> | sites/hostNameBindings | Ne | 
> | lokality/instance | Ne | 
> | lokality, instance/rozšíření | Ne | 
> | sites/premieraddons | Ano | 
> | lokality/doporučení | Ne | 
> | sites/resourceHealthMetadata | Ne | 
> | sites/slots | Ano | 
> | sites/slots/hostNameBindings | Ne | 
> | lokality/sloty/instance | Ne | 
> | lokality/sloty/instance/rozšíření | Ne | 
> | sourceControls | Ne | 
> | Oproti | Ne | 
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

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

> [!div class="mx-tableFixed"]
> | Typ prostředku | Úplné odstranění režimu |
> | ------------- | ----------- |
> | components | Ne | 
> | componentsSummary | Ne | 
> | monitorInstances | Ne | 
> | monitorInstancesSummary | Ne | 
> | Monitor | Ne | 
> | notificationSettings | Ne | 

## <a name="next-steps"></a>Další postup

Pokud chcete získat stejná data jako soubor hodnot oddělených čárkami, Stáhněte si [Complete-Mode-deletion. csv](https://github.com/tfitzmac/resource-capabilities/blob/master/complete-mode-deletion.csv).
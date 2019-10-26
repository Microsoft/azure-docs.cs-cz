---
title: Typy prostředků rozšíření Azure
description: Obsahuje seznam typů prostředků Azure, pomocí kterých se rozšiřuje schopnost dalších typů prostředků.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: tomfitz
ms.openlocfilehash: 00e8efaaa81ec0ce189d8cfe8a78285ded445c68
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901921"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Typy prostředků, které rozšiřuje možnosti jiných prostředků

Prostředek rozšíření je prostředek, který se přidává do možností jiného prostředku. Například zámek prostředku je prostředek rozšíření. Zámek prostředků můžete použít pro jiný prostředek, aby se zabránilo jeho odstranění nebo úpravě. Nedává smysl vytvořit prostředek jako zámek sám sebou. Prostředek rozšíření se vždycky použije na jiný prostředek.

## <a name="extension-resource-types"></a>Typy prostředků rozšíření

- Microsoft. Advisor/Configurations
- Microsoft. Advisor/doporučení
- Microsoft. Advisor/potlačení
- Microsoft. AlertsManagement/Alerts
- Microsoft. AlertsManagement/alertsSummary
- Microsoft. Authorization/checkAccess
- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/zámky
- Microsoft. Authorization/oprávnění
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleDefinitions
- Microsoft. fakturace/billingPeriods
- Microsoft. fakturace/billingPermissions
- Microsoft. fakturace/billingRoleAssignments
- Microsoft. fakturace/billingRoleDefinitions
- Microsoft. fakturace/createBillingRoleAssignment
- Microsoft. Details/blueprintAssignments
- Microsoft. Details/modrotisky
- Microsoft. spotřeb/AggregatedCost
- Microsoft. spotřeb/bilance
- Microsoft. spotřeba/rozpočty
- Microsoft. spotřeba/poplatky
- Microsoft. spotřeb/CostTags
- Microsoft. spotřeba/předpovědi
- Microsoft. spotřeb/Marketplace
- Microsoft. spotřeb/OperationResults
- Microsoft. spotřeb/stav operationstatus
- Microsoft. spotřeb/Pricesheets
- Microsoft. spotřeb/ReservationDetails
- Microsoft. spotřeb/ReservationRecommendations
- Microsoft. spotřeb/ReservationSummaries
- Microsoft. spotřeb/ReservationTransactions
- Microsoft. spotřeba/značky
- Microsoft. spotřeb/terms
- Microsoft. spotřeb/UsageDetails
- Microsoft. spotřeba/kredity
- Microsoft. spotřeb/Events
- Microsoft. spotřeba/šarže
- Microsoft. spotřeba/produkty
- Microsoft. spotřeba/klienti
- Microsoft. ContainerInstance/serviceAssociationLinks
- Microsoft. CostManagement/Alerts
- Microsoft. CostManagement/rozpočty
- Microsoft. CostManagement/Dimensions
- Microsoft. CostManagement/EXPORTS
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/FORECAST
- Microsoft. CostManagement/dotaz
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/Reports
- Microsoft. CostManagement/zobrazení
- Microsoft. CostManagement/showbackRules
- Microsoft. CustomProviders/Associations
- Microsoft. EventGrid/eventSubscriptions
- Microsoft. EventGrid/extensionTopics
- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/software
- Microsoft. GuestConfiguration/softwareUpdateProfile
- Microsoft. GuestConfiguration/softwareUpdates
- Microsoft. Insights/automatedExportSettings
- Microsoft. Insights/směrný plán
- Microsoft. Insights/calculatebaseline
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/diagnosticSettingsCategories
- Microsoft. Insights/EventTypes
- Microsoft. Insights/extendedDiagnosticSettings
- Microsoft. Insights/guestDiagnosticSettingsAssociation
- Microsoft. Insights/logDefinitions
- Microsoft. Insights/protokoly
- Microsoft. Insights/metricDefinitions
- Microsoft. Insights/metricNamespaces
- Microsoft. Insights/metricbaselines
- Microsoft. Insights/metriky
- Microsoft. Insights/myWorkbooks
- Microsoft. Insights/vmInsightsOnboardingStatuses
- Microsoft. KubernetesConfiguration/sourceControlConfigurations
- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/Updates
- Microsoft. ManagedIdentity/identity
- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions
- Microsoft. OperationalInsights/storageInsightConfigs
- Microsoft. OperationsManagement/managementassociations
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft. PolicyInsights/nápravy
- Microsoft. RecoveryServices/backupProtectedItems
- Microsoft. ResourceHealth/availabilityStatuses
- Microsoft. ResourceHealth/childAvailabilityStatuses
- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/Events
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/oznámení
- Microsoft. Resources/odkazy
- Microsoft. Resources/Tags
- Microsoft. Security/dodržování předpisů
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/Assessments
- Microsoft. Security/complianceResults
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/dataCollectionResults
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/networkData
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. SecurityInsights/agregace
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/záložky
- Microsoft. SecurityInsights/případy
- Microsoft. SecurityInsights/dataconnects
- Microsoft. SecurityInsights/entity
- Microsoft. SecurityInsights/entityQueries
- Microsoft. SecurityInsights/officeConsents
- Microsoft. SecurityInsights/Settings
- Microsoft. SoftwarePlan/hybridUseBenefits
- Microsoft. Subscription/CreateSubscription
- Microsoft. support/createsupportticket
- Microsoft. support/supporttickets
- Microsoft. monitor zátěže byl/– komponenty
- Microsoft. monitor zátěže byl/monitorInstances
- Microsoft. monitor zátěže byl/monitoruje
- Microsoft. monitor zátěže byl/notificationSettings

## <a name="next-steps"></a>Další kroky

- Chcete-li získat ID prostředku pro prostředek rozšíření v šabloně Azure Resource Manager, použijte [extensionResourceId](resource-group-template-functions-resource.md#extensionresourceid).
- Příklad vytvoření prostředku rozšíření v šabloně najdete v tématu [Event Grid odběry událostí](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).

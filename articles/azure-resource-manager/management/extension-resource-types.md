---
title: Typy prostředků rozšíření
description: Obsahuje seznam typů prostředků Azure, pomocí kterých se rozšiřuje schopnost dalších typů prostředků.
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 8b80c63d361f3ad8199fd669178f7bf88dabe02e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90969741"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Typy prostředků, které rozšiřuje možnosti jiných prostředků

Prostředek rozšíření je prostředek, který se přidává do možností jiného prostředku. Například zámek prostředku je prostředek rozšíření. Zámek prostředků můžete použít pro jiný prostředek, aby se zabránilo jeho odstranění nebo úpravě. Nedává smysl vytvořit prostředek jako zámek sám sebou. Prostředek rozšíření se vždycky použije na jiný prostředek.

## <a name="extension-resource-types"></a>Typy prostředků rozšíření

- Microsoft. Advisor/Configurations
- Microsoft. Advisor/doporučení
- Microsoft. Advisor/potlačení
- Microsoft. AlertsManagement/Alerts
- Microsoft. AlertsManagement/alertsSummary
- Microsoft. Authorization/accessReviewScheduleDefinitions
- Microsoft. Authorization/accessReviewScheduleSettings
- Microsoft. Authorization/checkAccess
- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/findOrphanRoleAssignments
- Microsoft. Authorization/zámky
- Microsoft. Authorization/oprávnění
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policyExemptions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/privateLinkAssociations
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleAssignmentsUsageMetrics
- Microsoft. Authorization/roleDefinitions
- Microsoft. automanage/configurationProfileAssignments
- Microsoft. fakturace/billingPeriods
- Microsoft. fakturace/billingPermissions
- Microsoft. fakturace/billingRoleAssignments
- Microsoft. fakturace/billingRoleDefinitions
- Microsoft. fakturace/createBillingRoleAssignment
- Microsoft. Details/blueprintAssignments
- Microsoft. Details/modrotisky
- Microsoft. ChangeAnalysis/resourceChanges
- Microsoft. spotřeb/AggregatedCost
- Microsoft. spotřeb/bilance
- Microsoft. spotřeba/rozpočty
- Microsoft. spotřeba/poplatky
- Microsoft. spotřeb/CostTags
- Microsoft. spotřeba/kredity
- Microsoft. spotřeb/Events
- Microsoft. spotřeba/předpovědi
- Microsoft. spotřeba/šarže
- Microsoft. spotřeb/Marketplace
- Microsoft. spotřeb/OperationResults
- Microsoft. spotřeb/stav operationstatus
- Microsoft. spotřeb/Pricesheets
- Microsoft. spotřeba/produkty
- Microsoft. spotřeb/ReservationDetails
- Microsoft. spotřeb/ReservationRecommendationDetails
- Microsoft. spotřeb/ReservationRecommendations
- Microsoft. spotřeb/ReservationSummaries
- Microsoft. spotřeb/ReservationTransactions
- Microsoft. spotřeba/značky
- Microsoft. spotřeba/klienti
- Microsoft. spotřeb/terms
- Microsoft. spotřeb/UsageDetails
- Microsoft. ContainerInstance/serviceAssociationLinks
- Microsoft. CostManagement/Alerts
- Microsoft. CostManagement/rozpočty
- Microsoft. CostManagement/costAllocationRules
- Microsoft. CostManagement/Dimensions
- Microsoft. CostManagement/EXPORTS
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/FORECAST
- Microsoft. CostManagement/Insights
- Microsoft. CostManagement/dotaz
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/Reports
- Microsoft. CostManagement/showbackRules
- Microsoft. CostManagement/zobrazení
- Microsoft. CustomProviders/Associations
- Microsoft. EventGrid/eventSubscriptions
- Microsoft. EventGrid/extensionTopics
- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/software
- Microsoft. GuestConfiguration/softwareUpdateProfile
- Microsoft. GuestConfiguration/softwareUpdates
- Microsoft. Insights/směrný plán
- Microsoft. Insights/calculatebaseline
- Microsoft. Insights/dataCollectionRuleAssociations
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/diagnosticSettingsCategories
- Microsoft. Insights/EventTypes
- Microsoft. Insights/extendedDiagnosticSettings
- Microsoft. Insights/generateLiveToken
- Microsoft. Insights/guestDiagnosticSettingsAssociation
- Microsoft. Insights/logDefinitions
- Microsoft. Insights/protokoly
- Microsoft. Insights/metricbaselines
- Microsoft. Insights/metricDefinitions
- Microsoft. Insights/metricNamespaces
- Microsoft. Insights/metriky
- Microsoft. Insights/myWorkbooks
- Microsoft. Insights/topologie
- Microsoft. Insights/transakcí
- Microsoft. Insights/vmInsightsOnboardingStatuses
- Microsoft. KubernetesConfiguration/Extensions
- Microsoft. KubernetesConfiguration/sourceControlConfigurations
- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/Updates
- Microsoft. ManagedIdentity/identity
- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions
- Microsoft. OperationalInsights/storageInsightConfigs
- Microsoft. OperationsManagement/managementassociations
- Microsoft. PolicyInsights/Attestation
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft. PolicyInsights/nápravy
- Microsoft. RecoveryServices/backupProtectedItems
- Microsoft. RecoveryServices/replicationEligibilityResults
- Microsoft. ResourceHealth/availabilityStatuses
- Microsoft. ResourceHealth/childAvailabilityStatuses
- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/Events
- Microsoft. ResourceHealth/impactedResources
- Microsoft. ResourceHealth/oznámení
- Microsoft. Resources/odkazy
- Microsoft. Resources/Tags
- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/Assessments
- Microsoft. Security/complianceResults
- Microsoft. Security/dodržování předpisů
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/iotSensors
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. Security/sqlVulnerabilityAssessments
- Microsoft. SecurityInsights/agregace
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/automationRules
- Microsoft. SecurityInsights/záložky
- Microsoft. SecurityInsights/případy
- Microsoft. SecurityInsights/dataconnects
- Microsoft. SecurityInsights/dataConnectorsCheckRequirements
- Microsoft. SecurityInsights/entity
- Microsoft. SecurityInsights/entityQueries
- Microsoft. SecurityInsights/incidenty
- Microsoft. SecurityInsights/officeConsents
- Microsoft. SecurityInsights/Settings
- Microsoft. SecurityInsights/threatIntelligence
- Microsoft. SecurityInsights/watchlists
- Microsoft. SoftwarePlan/hybridUseBenefits
- Microsoft. Subscription/CreateSubscription
- Microsoft. support/supporttickets
- Microsoft. monitor zátěže byl/– komponenty
- Microsoft. monitor zátěže byl/monitorInstances
- Microsoft. monitor zátěže byl/monitoruje
- Microsoft. monitor zátěže byl/notificationSettings

## <a name="next-steps"></a>Další kroky

- Chcete-li získat ID prostředku pro prostředek rozšíření v šabloně Azure Resource Manager, použijte [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Příklad vytvoření prostředku rozšíření v šabloně najdete v tématu [Event Grid odběry událostí](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).

---
title: Typy prostředků rozšíření
description: Obsahuje seznam typů prostředků Azure, pomocí kterých se rozšiřuje schopnost dalších typů prostředků.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 7085c0894fbf3bd56b56e4187d56f9af54916962
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363984"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Typy prostředků, které rozšiřuje možnosti jiných prostředků

Prostředek rozšíření je prostředek, který se přidává do možností jiného prostředku. Například zámek prostředku je prostředek rozšíření. Zámek prostředků můžete použít pro jiný prostředek, aby se zabránilo jeho odstranění nebo úpravě. Nedává smysl vytvořit prostředek jako zámek sám sebou. Prostředek rozšíření se vždycky použije na jiný prostředek.

## <a name="microsoftadvisor"></a>Microsoft. Advisor

- Microsoft. Advisor/Configurations
- Microsoft. Advisor/doporučení
- Microsoft. Advisor/potlačení

## <a name="microsoftalertsmanagement"></a>Microsoft. AlertsManagement

- Microsoft. AlertsManagement/Alerts

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft. Authorization/denyAssignments
- Microsoft. Authorization/zámky
- Microsoft. Authorization/policyAssignments
- Microsoft. Authorization/policyDefinitions
- Microsoft. Authorization/policyExemptions
- Microsoft. Authorization/policySetDefinitions
- Microsoft. Authorization/privateLinkAssociations
- Microsoft. Authorization/roleAssignmentApprovals
- Microsoft. Authorization/roleAssignments
- Microsoft. Authorization/roleAssignmentScheduleInstances
- Microsoft. Authorization/roleAssignmentScheduleRequests
- Microsoft. Authorization/roleAssignmentSchedules
- Microsoft. Authorization/roleDefinitions
- Microsoft. Authorization/roleEligibilityScheduleInstances
- Microsoft. Authorization/roleEligibilityScheduleRequests
- Microsoft. Authorization/roleEligibilitySchedules
- Microsoft. Authorization/roleManagementPolicies
- Microsoft. Authorization/roleManagementPolicyAssignments

## <a name="microsoftautomanage"></a>Microsoft. automanage

- Microsoft. automanage/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft. fakturace

- Microsoft. fakturace/billingPeriods
- Microsoft. fakturace/billingPermissions
- Microsoft. fakturace/billingRoleAssignments
- Microsoft. fakturace/billingRoleDefinitions
- Microsoft. fakturace/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Microsoft. detail

- Microsoft. Details/blueprintAssignments
- Microsoft. Details/modrotisky

## <a name="microsoftcapacity"></a>Microsoft. Capacity

- Microsoft. Capacity/listSkus

## <a name="microsoftchangeanalysis"></a>Microsoft. ChangeAnalysis

- Microsoft. ChangeAnalysis/změny

## <a name="microsoftconsumption"></a>Microsoft. spotřeba

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
- Microsoft. spotřeb/Pricesheets
- Microsoft. spotřeba/produkty
- Microsoft. spotřeb/ReservationDetails
- Microsoft. spotřeb/ReservationRecommendationDetails
- Microsoft. spotřeb/ReservationRecommendations
- Microsoft. spotřeb/ReservationSummaries
- Microsoft. spotřeb/ReservationTransactions

## <a name="microsoftcontainerinstance"></a>Microsoft. ContainerInstance

- Microsoft. ContainerInstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Microsoft. CostManagement

- Microsoft. CostManagement/Alerts
- Microsoft. CostManagement/rozpočty
- Microsoft. CostManagement/CheckNameAvailability
- Microsoft. CostManagement/Dimensions
- Microsoft. CostManagement/EXPORTS
- Microsoft. CostManagement/ExternalSubscriptions
- Microsoft. CostManagement/FORECAST
- Microsoft. CostManagement/GenerateDetailedCostReport
- Microsoft. CostManagement/Insights
- Microsoft. CostManagement/OperationResults
- Microsoft. CostManagement/stav operationstatus
- Microsoft. CostManagement/dotaz
- Microsoft. CostManagement/Reportconfigs
- Microsoft. CostManagement/Reports
- Microsoft. CostManagement/ScheduledActions
- Microsoft. CostManagement/zobrazení

## <a name="microsoftcustomproviders"></a>Microsoft. CustomProviders

- Microsoft. CustomProviders/Associations

## <a name="microsoftdatamigration"></a>Migrace Microsoft.

- Migrace Microsoft. dataDatabaseMigrations/

## <a name="microsofteventgrid"></a>Microsoft. EventGrid

- Microsoft. EventGrid/eventSubscriptions
- Microsoft. EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft. GuestConfiguration

- Microsoft. GuestConfiguration/configurationProfileAssignments
- Microsoft. GuestConfiguration/guestConfigurationAssignments
- Microsoft. GuestConfiguration/software

## <a name="microsoftinsights"></a>Microsoft. Insights

- Microsoft. Insights/směrný plán
- Microsoft. Insights/dataCollectionRuleAssociations
- Microsoft. Insights/diagnosticSettings
- Microsoft. Insights/diagnosticSettingsCategories
- Microsoft. Insights/EventTypes
- Microsoft. Insights/extendedDiagnosticSettings
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

## <a name="microsoftkubernetesconfiguration"></a>Microsoft. KubernetesConfiguration

- Microsoft. KubernetesConfiguration/Extensions
- Microsoft. KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Microsoft. Maintenance

- Microsoft. Maintenance/applyUpdates
- Microsoft. Maintenance/configurationAssignments
- Microsoft. Maintenance/Updates

## <a name="microsoftmanagedidentity"></a>Microsoft. ManagedIdentity

- Microsoft. ManagedIdentity/identity

## <a name="microsoftmanagedservices"></a>Microsoft. ManagedServices

- Microsoft. ManagedServices/registrationAssignments
- Microsoft. ManagedServices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft. OperationalInsights

- Microsoft. OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft. OperationsManagement

- Microsoft. OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft. PolicyInsights

- Microsoft. PolicyInsights/Attestation
- Microsoft. PolicyInsights/eventGridFilters
- Microsoft. PolicyInsights/policyEvents
- Microsoft. PolicyInsights/policyStates
- Microsoft. PolicyInsights/policyTrackedResources
- Microsoft. PolicyInsights/nápravy

## <a name="microsoftrecoveryservices"></a>Microsoft. RecoveryServices

- Microsoft. RecoveryServices/backupProtectedItems
- Microsoft. RecoveryServices/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft. ResourceHealth

- Microsoft. ResourceHealth/childResources
- Microsoft. ResourceHealth/Events
- Microsoft. ResourceHealth/impactedResources

## <a name="microsoftresources"></a>Microsoft. Resources

- Microsoft. Resources/odkazy
- Microsoft. Resources/Tags

## <a name="microsoftsecurity"></a>Microsoft.Security

- Microsoft. Security/adaptiveNetworkHardenings
- Microsoft. Security/advancedThreatProtectionSettings
- Microsoft. Security/assessmentMetadata
- Microsoft. Security/Assessments
- Microsoft. Security/dodržování předpisů
- Microsoft. Security/dataCollectionAgents
- Microsoft. Security/Devices
- Microsoft. Security/deviceSecurityGroups
- Microsoft. Security/InformationProtectionPolicies
- Microsoft. Security/Insights
- Microsoft. Security/iotAlerts
- Microsoft. Security/iotRecommendations
- Microsoft. Security/iotSensors
- Microsoft. Security/iotSites
- Microsoft. Security/jitPolicies
- Microsoft. Security/serverVulnerabilityAssessments
- Microsoft. Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Microsoft. SecurityInsights

- Microsoft. SecurityInsights/agregace
- Microsoft. SecurityInsights/alertRules
- Microsoft. SecurityInsights/alertRuleTemplates
- Microsoft. SecurityInsights/automationRules
- Microsoft. SecurityInsights/záložky
- Microsoft. SecurityInsights/případy
- Microsoft. SecurityInsights/dataconnects
- Microsoft. SecurityInsights/dataConnectorsCheckRequirements
- Microsoft. SecurityInsights/obohacení
- Microsoft. SecurityInsights/entity
- Microsoft. SecurityInsights/entityQueryTemplates
- Microsoft. SecurityInsights/incidenty
- Microsoft. SecurityInsights/Settings
- Microsoft. SecurityInsights/threatIntelligence
- Microsoft. SecurityInsights/watchlists

## <a name="microsoftserialconsole"></a>Microsoft. SerialConsole

- Microsoft. SerialConsole/serialPorts

## <a name="microsoftservicelinker"></a>Microsoft. ServiceLinker

- Microsoft. ServiceLinker/propojování

## <a name="microsoftsoftwareplan"></a>Microsoft. SoftwarePlan

- Microsoft. SoftwarePlan/hybridUseBenefits

## <a name="microsoftsubscription"></a>Microsoft. Subscription

- Microsoft. Subscription/policies

## <a name="microsoftsupport"></a>Microsoft. support

- Microsoft. support/supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft. monitor zátěže byl

- Microsoft. monitor zátěže byl/monitoruje

## <a name="next-steps"></a>Další kroky

- Chcete-li získat ID prostředku pro prostředek rozšíření v šabloně Azure Resource Manager, použijte [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Příklad vytvoření prostředku rozšíření v šabloně najdete v tématu [Event Grid odběry událostí](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).

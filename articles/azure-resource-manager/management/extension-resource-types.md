---
title: Typy prostředků rozšíření
description: Seznamy typů prostředků Azure se používají k rozšíření možností jiných typů prostředků.
ms.topic: conceptual
ms.date: 01/20/2020
ms.openlocfilehash: c14cf6fec2da11534a7358599c79961a3258470b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76515364"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Typy prostředků, které rozšiřují možnosti jiných prostředků

Prostředek rozšíření je prostředek, který přidává do možností jiného prostředku. Například zámek prostředků je prostředek rozšíření. Zámek prostředku použijete na jiný prostředek, abyste zabránili jeho odstranění nebo úpravám. Nemá smysl vytvořit zámek prostředků sám. Prostředek rozšíření je vždy použit a u jiného prostředku.

## <a name="extension-resource-types"></a>Typy prostředků rozšíření

- Microsoft.Advisor/konfigurace
- Microsoft.Advisor/doporučení
- Microsoft.Advisor/potlačení
- Microsoft.AlertsManagement/alerts
- Microsoft.AlertsManagement/alertsSummary
- Microsoft.Authorization/checkAccess
- Microsoft.Authorization/denyÚlohy
- Microsoft.Authorization/findOrphanRoleAssignments
- Microsoft.Authorization/zámky
- Microsoft.Autorizace/oprávnění
- Microsoft.Authorization/policyAssignments
- Microsoft.Authorization/policyDefinitions
- Microsoft.Authorization/policySetDefinitions
- Microsoft.Authorization/roleAssignments
- Microsoft.Authorization/roleAssignmentsUsageMetrics
- Microsoft.Authorization/roleDefinitions
- Microsoft.Billing/billingPeriods
- Microsoft.Billing/billingPermissions
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment
- Microsoft.Blueprint/blueprintÚkoly
- Microsoft.Blueprint/podrobné plány
- Microsoft.Consumption/AggregatedCost
- Microsoft.Spotřeba/zůstatky
- Microsoft.Spotřeba/rozpočty
- Microsoft.Spotřeba/poplatky
- Microsoft.Spotřeba/CostTags
- Microsoft.Spotřeba/prognózy
- Microsoft.Consumption/Tržiště
- Microsoft.Consumption/OperationResults
- Microsoft.Consumption/OperationStatus
- Microsoft.Spotřeba/Ceníky
- Microsoft.Odběr/RezervacePodrobnosti
- Microsoft.Consumption/ReservationRecommendations
- Microsoft.Consumption/ReservationSummaries
- Microsoft.Consumption/ReservationTransactions
- Microsoft.Consumption/Tags
- Microsoft.Consumption/Terms
- Microsoft.Consumption/UsageDetails
- Microsoft.Spotřeba/kredity
- Microsoft.Spotřeba/události
- Microsoft.Spotřeba/loty
- Microsoft.Spotřeba/produkty
- Microsoft.Consumption/tenants
- Microsoft.ContainerInstance/serviceAssociationLinks
- Microsoft.CostManagement/Alerts
- Microsoft.CostManagement/Rozpočty
- Microsoft.CostManagement/Dimenze
- Microsoft.CostManagement/Exporty
- Microsoft.CostManagement/ExternalSubscriptions
- Microsoft.CostManagement/Prognóza
- Microsoft.CostManagement/Dotaz
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Sestavy
- Microsoft.CostManagement/Zobrazení
- Microsoft.CostManagement/showbackRules
- Microsoft.CustomProviders/associations
- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics
- Microsoft.GuestKonfigurace/konfiguraceProfilúlohy
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestKonfigurace/software
- Microsoft.GuestKonfigurace/softwareUpdateProfile
- Microsoft.GuestKonfigurace/softwareAktualizace
- microsoft.insights/automatedExportSettings
- microsoft.insights/baseline
- Microsoft.Insights/calculatebaseline
- Microsoft.insights/dataCollectionRuleAssociations
- Microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- Microsoft.insights/extendedDiagnosticSettings
- Microsoft.insights/guestDiagnosticSettingsAssociation
- Microsoft.insights/logDefinitions
- microsoft.insights/logs
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- Microsoft.Insights/metricbaselines
- microsoft.insights/metriky
- microsoft.insights/myWorkbook
- microsoft.insights/topologie
- microsoft.insights/transactions
- microsoft.insights/vmInsightsOnboardingStaves
- Microsoft.KubernetesKonfigurace/sourceControlKonfigurace
- Microsoft.Maintenance/applyUpdates
- Microsoft.Maintenance/configurationÚkoly
- Microsoft.Údržba/aktualizace
- Microsoft.ManagedIdentity/Identities
- Microsoft.ManagedServices/registrationÚkoly
- Microsoft.ManagedServices/registrationDefinitions
- Microsoft.OperationalInsights/storageInsightConfigs
- Microsoft.OperationsManagement/managementassociations
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/náprava
- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicationEligibilityResults  
- Microsoft.ResourceHealth/availabilityStaves
- Microsoft.ResourceHealth/childAvailabilityStatuses
- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/události
- Microsoft.ResourceHealth/impactedResources
- Microsoft.ResourceHealth/oznámení
- Microsoft.Resources/odkazy
- Microsoft.Resources/tags
- Microsoft.Security/Compliances
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/assessments
- Microsoft.Security/complianceResults
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/dataCollectionResults
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/networkData
- Microsoft.Security/serverVulnerabilityAssessments
- Microsoft.SecurityInsights/agregace
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/alertRules
- Microsoft.SecurityInsights/záložky
- Microsoft.SecurityInsights/případy
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/entity
- Microsoft.SecurityInsights/entityDotazy
- Microsoft.SecurityInsights/officeConsents
- Microsoft.SecurityInsights/nastavení
- Microsoft.SoftwarePlan/hybridUseBenefits
- Microsoft.Subscription/CreateSubscription
- microsoft.support/createsupportticket
- Microsoft.support/supporttickets
- Microsoft.WorkloadMonitor/komponenty
- Instance Microsoft.WorkloadMonitor/monitorInstances
- Microsoft.WorkloadMonitor/monitory
- Microsoft.WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Další kroky

- Chcete-li získat ID prostředku pro prostředek rozšíření v šabloně Azure Resource Manager, použijte [rozšířeníResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Příklad vytvoření prostředku rozšíření v šabloně naleznete v tématu [Odběry událostí v mřížce událostí](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions).

---
title: Vytvoření a úprava Azure Policy zásady zabezpečení pomocí REST API
description: Přečtěte si o Azure Policy správy zásad pomocí REST API.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 6fcfed585aece4fd57c085e0c9f6fdcd6cbeae74
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448362"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Konfigurace zásady zabezpečení v Azure Policy pomocí REST API

V rámci nativní integrace s Azure Policy Azure Security Center umožňuje využít REST API Azure Policy k vytváření přiřazení zásad. Následující pokyny vás provedou vytvořením přiřazení zásad a přizpůsobením stávajících přiřazení. 

Důležité pojmy v Azure Policy: 

- **Definice zásady** je pravidlo 

- **Iniciativa** je kolekcí definic zásad (pravidel) 

- **Přiřazení** je aplikace iniciativy nebo zásady konkrétního oboru (skupina pro správu, předplatné atd.). 

Security Center má předdefinovanou iniciativu, která zahrnuje všechny zásady zabezpečení. Pokud chcete vyhodnotit zásady Security Center v prostředcích Azure, měli byste vytvořit přiřazení ke skupině pro správu nebo předplatnému, které chcete vyhodnotit.

Integrovaná iniciativa má ve výchozím nastavení povolené všechny zásady Security Center. Můžete zvolit, že chcete zakázat určité zásady z předdefinované iniciativy. Pokud například chcete použít všechny zásady Security Center s výjimkou **firewallu webových aplikací**, změňte hodnotu parametru efektu zásady na **disabled (zakázáno**). 

## <a name="api-examples"></a>Příklady rozhraní API

V následujících příkladech nahraďte tyto proměnné:

- **{Scope}** zadejte název skupiny pro správu nebo předplatného, na kterou zásady aplikujete.
- **{policyAssignmentName}** zadejte [název odpovídajícího přiřazení zásad](#policy-names).
- **{Name}** zadejte své jméno nebo jméno správce, který schválil změnu zásad.

V tomto příkladu se dozvíte, jak přiřadit integrovanou Security Center iniciativu pro předplatné nebo skupinu pro správu.
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

V tomto příkladu se dozvíte, jak přiřadit integrovanou Security Center iniciativu k předplatnému, a tyto zásady jsou zakázané: 

- Aktualizace systému (systemUpdatesMonitoringEffect) 

- Konfigurace zabezpečení (systemConfigurationsMonitoringEffect) 

- Endpoint Protection (endpointProtectionMonitoringEffect) 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
V tomto příkladu se dozvíte, jak odebrat přiřazení:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>Odkazy na názvy zásad <a name="policy-names"></a>

|Název zásady v Security Center|Název zásad zobrazený v Azure Policy |Název parametru efektu zásad|
|----|----|----|
|Šifrování SQL |Monitorování nešifrované databáze SQL v Azure Security Center |sqlEncryptionMonitoringEffect| 
|SQL Auditing (Auditování SQL) |Monitorování neauditované databáze SQL v Azure Security Center |sqlAuditingMonitoringEffect|
|Aktualizace systému |Monitorovat chybějící aktualizace systému v Azure Security Center |systemUpdatesMonitoringEffect|
|Šifrování úložiště |Auditování chybějícího šifrování objektů blob pro účty úložiště |storageEncryptionMonitoringEffect|
|Síťový přístup JIT |Sledování možného přístupu JIT (just-in-time) v síti v Azure Security Center |jitNetworkAccessMonitoringEffect |
|Adaptivní řízení aplikací |Monitorování možných seznamů povolených aplikací v Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Skupiny zabezpečení sítě |Monitorování opravňujícího přístupu k síti v Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Konfigurace zabezpečení |Monitorování ohrožení zabezpečení operačního systému v Azure Security Center |systemConfigurationsMonitoringEffect| 
|Ochrana koncového bodu |Monitorovat chybějící Endpoint Protection v Azure Security Center |endpointProtectionMonitoringEffect |
|Šifrování disků |Monitorování nešifrovaných disků virtuálních počítačů v Azure Security Center |diskEncryptionMonitoringEffect|
|Posouzení ohrožení zabezpečení |Monitorování ohrožení zabezpečení virtuálních počítačů v Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Brána firewall webových aplikací |Monitorování nechráněné webové aplikace v Azure Security Center |webApplicationFirewallMonitoringEffect |
|Brána firewall příští generace |Monitorování nechráněných koncových bodů sítě v Azure Security Center| |


## <a name="next-steps"></a>Další kroky

Další související materiály najdete v následujících článcích: 

- [Vlastní zásady zabezpečení](custom-security-policies.md)
- [Přehled zásad zabezpečení](tutorial-security-policy.md)
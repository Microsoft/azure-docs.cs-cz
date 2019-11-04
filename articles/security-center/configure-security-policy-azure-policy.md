---
title: Vytvoření a úprava Azure Policy zásady zabezpečení pomocí REST API | Microsoft Docs
description: Přečtěte si o Azure Policy správy zásad pomocí REST API.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 56c5ca8c4e1d70e002a338c753f9ab1f0b1aa411
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522018"
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

## Odkazy na názvy zásad<a name="policy-names"></a>

|Název zásady v Security Center|Název zásad zobrazený v Azure Policy |Název parametru efektu zásad|
|----|----|----|
|Šifrování SQL |Monitorování nešifrované databáze SQL v Azure Security Center |sqlEncryptionMonitoringEffect| 
|SQL Auditing (Auditování SQL) |Monitorování neauditované databáze SQL v Azure Security Center |sqlAuditingMonitoringEffect|
|Aktualizace systému |Monitorovat chybějící aktualizace systému v Azure Security Center |systemUpdatesMonitoringEffect|
|Šifrování úložiště |Auditovat chybějící šifrování objektů BLOB pro účty úložiště |storageEncryptionMonitoringEffect|
|Síťový přístup JIT |Sledování možného přístupu JIT (just-in-time) v síti v Azure Security Center |jitNetworkAccessMonitoringEffect |
|Adaptivní ovládací prvky aplikací |Monitorování možného seznamu povolených aplikací v Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Skupiny zabezpečení sítě |Monitorování opravňujícího přístupu k síti v Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Konfigurace zabezpečení |Monitorování ohrožení zabezpečení operačního systému v Azure Security Center |systemConfigurationsMonitoringEffect| 
|Ochrana koncových bodů |Monitorovat chybějící Endpoint Protection v Azure Security Center |endpointProtectionMonitoringEffect |
|Šifrování disků |Monitorování nešifrovaných disků virtuálních počítačů v Azure Security Center |diskEncryptionMonitoringEffect|
|Posouzení ohrožení zabezpečení |Monitorování ohrožení zabezpečení virtuálních počítačů v Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Firewall webových aplikací |Monitorování nechráněné webové aplikace v Azure Security Center |webApplicationFirewallMonitoringEffect |
|Brána firewall příští generace |Monitorování nechráněných koncových bodů sítě v Azure Security Center| |


## <a name="next-steps"></a>Další kroky

Další související materiály najdete v následujících článcích: 

- [Vlastní zásady zabezpečení](custom-security-policies.md)
- [Přehled zásad zabezpečení](tutorial-security-policy.md)
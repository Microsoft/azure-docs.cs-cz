---
title: Vytváření a úpravy zásad Azure pomocí rozhraní REST API
description: Přečtěte si o správě zásad Azure prostřednictvím rozhraní REST API.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430937"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Konfigurace zásad zabezpečení v zásadách Azure pomocí rozhraní REST API

Jako součást nativní integrace s Azure Policy vám Azure Security Center umožňuje využít výhod rozhraní REST API Azure Policy k vytváření přiřazení zásad. Následující pokyny vás provedou vytvářením přiřazení zásad a také přizpůsobením stávajících přiřazení. 

Důležité koncepty v Azure Policy: 

- **Definice politiky** je pravidlo 

- **Iniciativa** je soubor definic politik (pravidel) 

- **Přiřazení** je aplikace iniciativy nebo zásady pro určitý obor (skupina pro správu, předplatné atd.) 

Centrum zabezpečení má integrovanou iniciativu, která zahrnuje všechny jeho zásady zabezpečení. Chcete-li posoudit zásady Security Center na prostředky Azure, měli byste vytvořit přiřazení ve skupině pro správu nebo předplatné, které chcete posoudit.

Integrovaná iniciativa má všechny zásady Centra zabezpečení ve výchozím nastavení povolené. Můžete zakázat určité zásady z integrované iniciativy. Chcete-li například použít všechny zásady centra zabezpečení s výjimkou **brány firewall webové aplikace**, změňte hodnotu parametru efektu zásady na **Hodnotu Zakázáno**. 

## <a name="api-examples"></a>Příklady rozhraní API

V následujících příkladech nahraďte tyto proměnné:

- **{obor}** zadejte název skupiny pro správu nebo předplatného, na které zásadu používáte.
- **{policyAssignmentName}** zadejte [název příslušného přiřazení zásad](#policy-names).
- **{name}** zadejte své jméno nebo jméno správce, který změnu zásad schválil.

Tento příklad ukazuje, jak přiřadit integrovanou iniciativu Centra zabezpečení k předplatnému nebo skupině pro správu.
 
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

Tento příklad ukazuje, jak přiřadit integrovanou iniciativu Centra zabezpečení k předplatnému, přičemž jsou zakázány následující zásady: 

- Aktualizace systému ("systemUpdatesMonitoringEffect") 

- Konfigurace zabezpečení ("systemConfigurationsMonitoringEffect") 

- Ochrana koncového bodu ("endpointProtectionMonitoringEffect") 

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
Tento příklad ukazuje, jak odebrat přiřazení:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>Odkaz na názvy zásad<a name="policy-names"></a>

|Název zásady v Centru zabezpečení|Název zásad zobrazený v zásadách Azure |Název parametru efektu zásady|
|----|----|----|
|Šifrování SQL |Sledování nešifrované databáze SQL v Azure Security Center |sqlEncryptionMonitoringEffect| 
|SQL Auditing (Auditování SQL) |Sledování neauditované databáze SQL v Azure Security Center |sqlAuditingMonitoringEffect|
|Aktualizace systému |Sledování chybějících aktualizací systému v Azure Security Center |systemUpdatesMonitoringEffect|
|Šifrování úložiště |Auditování chybějícího šifrování objektů blob pro účty úložiště |storageEncryptionMonitoringEffect|
|Přístup k síti JIT |Monitorování možného přístupu k síti just-in-time (JIT) v Azure Security Center |jitNetworkAccessMonitoringEffect |
|Adaptivní řízení aplikací |Monitorování možného seznamu aplikací whitelisting v Azure Security Center |adaptivníApplicationControlsMonitoringEffect|
|Skupiny zabezpečení sítě |Monitorování pomůcení přístupu k síti v Azure Security Center |síťSecurityGroupsMonitoringEffect| 
|Konfigurace zabezpečení |Monitorování slabých míst operačního systému v Azure Security Center |systemConfigurationsMonitoringEffect| 
|Ochrana koncových bodů |Sledování chybějící ochrany koncového bodu v Azure Security Center |koncový bodProtectionMonitoringEffect |
|Šifrování disku |Monitorování nešifrovaných disků virtuálních počítačů v Azure Security Center |diskEncryptionMonitoringEffect|
|Posouzení ohrožení zabezpečení |Monitorování slabých míst virtuálních počítačů v Azure Security Center |zranitelnostAssessmentMonitoringEffect|
|Brána firewall webových aplikací |Monitorování nechráněných webových aplikací v Azure Security Center |webApplicationFirewallMonitoringEffect |
|Brána firewall příští generace |Monitorování nechráněných síťových koncových bodů v Azure Security Center| |


## <a name="next-steps"></a>Další kroky

Další související materiály naleznete v následujících článcích: 

- [Vlastní zásady zabezpečení](custom-security-policies.md)
- [Přehled zásad zabezpečení](tutorial-security-policy.md)
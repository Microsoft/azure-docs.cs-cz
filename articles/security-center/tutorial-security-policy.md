---
title: Upravit zásady zabezpečení ve službě Azure Policy | Dokumentace Microsoftu
description: Upravte zásady zabezpečení ve službě Azure Policy.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/3/2018
ms.author: rkarlin
ms.openlocfilehash: d6cc216f71efcd3b3973cd37349dd5145237f02f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52839326"
---
# <a name="edit-security-policies-in-azure-policy"></a>Upravit zásady zabezpečení ve službě Azure Policy
Security Center umožňuje zobrazit stav zásad zabezpečení a jak se používají napříč vašimi úlohami. Azure Security Center automaticky přiřadí jeho [integrované bezpečnostní zásady](security-center-policy-definitions.md) na každé předplatné, které zprovoznění. Můžete nakonfigurovat v [Azure Policy](../azure-policy/azure-policy-introduction.md), nebo pomocí rozhraní REST API, které také umožňuje nastavit zásady napříč skupinami pro správu a napříč několika předplatnými. Další informace najdete v tématu [Integrace zásad zabezpečení ve službě Security Center se službou Azure Policy](security-center-azure-policy.md). V tomto kurzu se naučíte, jak:

> [!div class="checklist"]
> * Konfigurace zásad zabezpečení pomocí rozhraní REST API
> * Vyhodnotit zabezpečení vašich prostředků

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
Chcete-li si vyzkoušet postupy popsané v tomto kurzu, budete potřebovat službu Security Center v cenové úrovni Standard. Security Center úrovně Standard můžete vyzkoušet zdarma. Další informace najdete na [stránce s cenami](https://azure.microsoft.com/pricing/details/security-center/). Článek Rychlý Start: [Onboarding předplatného Azure na Security Center Standard](security-center-get-started.md) vás provede postupem upgradu na úroveň Standard.

## <a name="configure-a-security-policy-using-the-rest-api"></a>Konfigurace zásad zabezpečení pomocí rozhraní REST API

Jako součást nativní integrace se službou Azure Policy Azure Security Center umožňuje využít výhod Azure Policy rozhraní REST API k vytvoření přiřazení zásady. Postupujte podle následujících pokynů vás provedou vytvořením přiřazení zásad, stejně jako vlastní nastavení z existující přiřazení. 

Důležité koncepty ve službě Azure Policy: 

- A **definice zásady** je pravidlo 

-  **Iniciativy** je kolekce definic zásad (pravidla) 

-  **Přiřazení** aplikaci iniciativy nebo zásady pro konkrétní obor (skupiny pro správu, předplatné, atd.) 

Security Center obsahuje vestavěné iniciativy, která obsahuje všechny jeho zásady zabezpečení. Aby bylo možné vyhodnotit zásady služby Security Center ve vašich prostředcích Azure, měli byste vytvořit přiřazení skupiny pro správu nebo předplatného, které chcete posoudit.  

Integrované iniciativy má všechny zásady služby Security Center ve výchozím nastavení povolená. Můžete také zakázat určitých zásad z předdefinované iniciativu, například můžete použít všechny zásady služby Security Center s výjimkou **firewallu webových aplikací**, tak, že změníte hodnotu parametru účinku zásad  **Zakázané**. 

### <a name="api-examples"></a>Příklady rozhraní API

V následujících příkladech nahraďte tyto proměnné:

- **{oboru}**  zadejte název skupiny pro správu nebo předplatného jsou použití zásady tak, aby.
- **{poicyAssignmentName}**  zadejte [název přiřazení zásady relevantní](#policy-names).
- **{name}**  zadejte své jméno nebo název správce, který schválení zásadu změnit.

Tento příklad ukazuje, jak přiřadit předdefinované iniciativu Security Center podle předplatného nebo skupiny pro správu
 
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

Tento příklad ukazuje, jak přiřadit předdefinované iniciativu Security Center v rámci předplatného se tyto zásady zakázaná: 

- Aktualizace systému ("systemUpdatesMonitoringEffect") 

- Konfigurace zabezpečení ("systemConfigurationsMonitoringEffect") 

- Aplikace Endpoint protection (dále jen "endpointProtectionMonitoringEffect") 

 
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

Tento příklad ukazuje, jak odebrat přiřazení:

    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 


## Referenční příručce o zásadách názvy <a name="policy-names"></a>

|Název zásad ve službě Security Center|Název zásady zobrazovaný v Azure Policy |Název parametru účinku zásad|
|----|----|----|
|Šifrování SQL |Monitorovat nešifrovanou databázi SQL ve službě Azure Security Center |sqlEncryptionMonitoringEffect| 
|SQL Auditing (Auditování SQL) |Monitorovat neauditovanou databázi SQL ve službě Azure Security Center |sqlAuditingMonitoringEffect|
|Aktualizace systému |Monitorovat chybějící aktualizace systému ve službě Azure Security Center |systemUpdatesMonitoringEffect|
|Šifrování úložiště |Auditovat chybějící šifrování objektů blob pro účty úložiště |storageEncryptionMonitoringEffect|
|Přístup k síti JIT |Monitorovat možné síťový přístup pouze In Time (JIT) ve službě Azure Security Center |jitNetworkAccessMonitoringEffect |
|Adaptivní řízení aplikací |Monitorovat možné přidávání aplikací na seznam povolených ve službě Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Skupiny zabezpečení sítě |Monitorovat benevolentní přístup k síti ve službě Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Konfigurace zabezpečení |Monitorování ohrožení zabezpečení operačního systému ve službě Azure Security Center |systemConfigurationsMonitoringEffect| 
|Ochrana koncových bodů |Monitorování chybějící služby Endpoint Protection ve službě Azure Security Center |endpointProtectionMonitoringEffect |
|Šifrování disku |Monitorovat nešifrované disky virtuálních počítačů ve službě Azure Security Center |diskEncryptionMonitoringEffect|
|Posouzení ohrožení zabezpečení |Monitorování ohrožení zabezpečení virtuálních počítačů ve službě Azure Security Center |vulnerabilityAssesmentMonitoringEffect|
|Brána firewall webových aplikací |Monitorovat nechráněné webové aplikace ve službě Azure Security Center |webApplicationFirewallMonitoringEffect |
|Brána firewall příští generace |Monitorovat nechráněné koncové body sítě ve službě Azure Security Center| |





## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak upravit zásady zabezpečení ve službě Azure Policy. Další informace o službě Security Center najdete v následujících článcích:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md) – Zjistěte, jak naplánovat a pochopit aspekty návrhu pro Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Získání viditelnosti v rámci celého tenanta pro Azure Security Center](security-center-management-groups.md) – Zjistěte, jak nastavit skupiny pro správu pro Azure Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Získejte odpovědi na nejčastější dotazy týkající se použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.

Další informace o Azure Policy najdete v tématu [Co je Azure Policy](../azure-policy/azure-policy-introduction.md).

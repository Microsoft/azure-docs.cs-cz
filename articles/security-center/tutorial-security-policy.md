---
title: Práce se zásadami zabezpečení | Dokumentace Microsoftu
description: Tento článek popisuje, jak pracovat se zásadami zabezpečení ve službě Azure Security Center.
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
ms.date: 1/4/2019
ms.author: rkarlin
ms.openlocfilehash: c706dbd4536b9c75281500ace6f02db00ff2f95d
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095510"
---
# <a name="working-with-security-policies"></a>Práce se zásadami zabezpečení

Tento článek vysvětluje, jak jsou nakonfigurované zásady zabezpečení a jak zobrazit ve službě Security Center. Azure Security Center automaticky přiřadí jeho [integrované bezpečnostní zásady](security-center-policy-definitions.md) na každé předplatné, které zprovoznění. Můžete nakonfigurovat v [Azure Policy](../azure-policy/azure-policy-introduction.md), který také umožňuje nastavit zásady napříč skupinami pro správu a napříč několika předplatnými.

Pokyny o tom, jak nastavit zásady pomocí Powershellu najdete v tématu [rychlý start: Vytvoření přiřazení zásady pro identifikaci neodpovídajících prostředků pomocí modulu Azure RM Powershellu](../azure-policy/assign-policy-definition-ps.md).

>[!NOTE]
> Security Center začít své integraci s Azure Policy. Stávající zákazníci budou automaticky migrovat na nové integrované podnět ve službě Azure Policy, namísto předchozí zásady zabezpečení ve službě Security Center. Tato změna nebude mít vliv na prostředky nebo prostředí s výjimkou přítomnost nové aktivity ve službě Azure Policy.

## <a name="what-are-security-policies"></a>Co jsou zásady zabezpečení?
Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení. Ve službě Azure Policy můžete definovat zásady pro vaše předplatná Azure a je typu úlohy nebo citlivosti dat. přizpůsobit. Aplikace, které používají regulovaná data, jako jsou identifikovatelné osobní údaje, například může vyžadovat vyšší úroveň zabezpečení než jiné úlohy. Nastavit zásady napříč předplatnými nebo skupin pro správu, je nastavit [Azure Policy](../azure-policy/azure-policy-introduction.md).

Zásady zabezpečení vliv na doporučení zabezpečení, získáte v Azure Security Center. Můžete monitorovat dodržování zásad v nich můžete najít potenciální nedostatky zabezpečení a zmírnit hrozby. Další informace o tom, jak určit, která možnost je pro vás vhodná, najdete v seznamu [integrované bezpečnostní zásady](security-center-policy-definitions.md).

Když povolíte Security Center, zásady zabezpečení, která je integrovaná do služby Security Center se projeví ve službě Azure Policy jako integrované iniciativy v rámci kategorie Security Center. Integrované iniciativy se automaticky přiřadí na všechny odběry registrované Security Center (úrovně Free nebo Standard). Integrované iniciativy obsahuje pouze zásady auditu. 


### <a name="management-groups"></a>Skupiny pro správu
Pokud má vaše organizace mnoho předplatných, možná budete potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tato předplatná. Skupiny pro správu Azure poskytují úroveň rozsahu nad předplatnými. Předplatná uspořádáte do kontejnerů označovaných jako skupiny pro správu a na tyto skupiny pro správu použijete své zásady správného řízení. Všechna předplatná v rámci skupiny pro správu automaticky dědí zásady, které se vztahují na skupinu pro správu. Každý adresář obdrží jednu skupinu pro správu nejvyšší úrovně označovanou jako kořenová skupina pro správu. Tato kořenová skupina pro správu je integrovaná do hierarchie tak, aby pod ní spadaly všechny skupiny pro správu a všechna předplatná. Tato kořenová skupina pro správu umožňuje používání globálních zásad a přiřazení RBAC na úrovni adresáře. Nastavení skupin pro správu pro použití se službou Azure Security Center, postupujte podle pokynů v [získejte potřebný Přehled celého tenanta pro Azure Security Center](security-center-management-groups.md).

> [!NOTE]
> Je důležité, abyste rozuměli hierarchii skupin pro správu a předplatných. Další informace o skupinách pro správu, kořenových skupinách pro správu a přístupu ke skupinám pro správu najdete v tématu věnovaném [uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/index.md#root-management-group-for-each-directory).
>

## <a name="how-security-policies-work"></a>Způsob fungování zásad zabezpečení
Security Center automaticky vytváří výchozí zásady zabezpečení pro každé z vašich předplatných Azure. Můžete upravit zásady ve službě Azure Policy provádět následující akce:
- Vytvoření nových definic zásad
- Přiřazení zásad napříč skupinami pro správu a předplatnými, které můžou reprezentovat celou organizaci nebo obchodní jednotku v rámci organizace
- Monitorování dodržování zásad

Další informace o službě Azure Policy najdete v tématu [Vytvoření a správa zásad pro vynucování dodržování předpisů](../azure-policy/create-manage-policy.md).

Zásada Azure se skládá z následujících součástí:

- A **zásady** je pravidlo.
- **Iniciativy** je kolekce zásady.
- **Přiřazení** je o iniciativy nebo zásady aplikace pro konkrétní obor (skupiny pro správu, předplatné nebo skupinu prostředků).

## <a name="view-security-policies"></a>Zobrazení zásad zabezpečení

Zobrazení zásad zabezpečení ve službě Security Center:

1. V **Security Center** řídicího panelu, vyberte **zásady zabezpečení**.

    ![Okno Správa zásad](./media/security-center-policies/security-center-policy-mgt.png)

  V **Správa zásad** obrazovky, zobrazí se počet skupin pro správu, předplatných a pracovních prostorů, jakož i strukturu skupin pro správu.

  > [!NOTE]
  > - Řídicí panel Security Center může zobrazit větší počet předplatných v rámci **pokrytí předplatného** než počet předplatných, které jsou uvedené v části **Správa zásad**. V části Pokrytí předplatného se zobrazuje počet předplatných úrovně Standard a Free a „nepokrytých“ předplatných. Předplatná "nejsou zahrnuta" nemají povolené Security Center a nejsou zobrazeny v části **Správa zásad**.
  >

  Ve sloupcích tabulky se zobrazuje:

 - **Přiřazení iniciativy zásad** – Security Center [integrované zásady](security-center-policy-definitions.md) a iniciativy, které jsou přiřazeny předplatné nebo skupinu pro správu.
 - **Pokrytí** – identifikuje cenové úrovně Free nebo Standard, která skupina pro správu, předplatné nebo pracovní prostor běží na.  Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
 - **Nastavení** – předplatné má odkaz **upravit nastavení**. Výběr **upravit nastavení** vám umožní aktualizovat vaše [nastavení Centra zabezpečení](security-center-policies-overview.md) pro každé předplatné nebo skupinu pro správu.
 - **Zabezpečené skóre** – [zabezpečené skóre](security-center-secure-score.md) poskytují měřítko zabezpečené stavu zabezpečení vašich úloh a pomůže vám určit prioritu doporučení pro vylepšení.

2. Vyberte předplatné nebo správu skupinu, jejichž zásady, které chcete zobrazit.

  - **Zásady zabezpečení** obrazovce se projeví akci provedenou na základě zásad přiřazených na předplatné nebo skupinu pro správu jste vybrali.
  - V horní části stránky, použijte odkazy otevřete jednotlivé zásady **přiřazení** , která se vztahuje na předplatné nebo skupinu pro správu. Odkazy můžete použít pro přístup k přiřazení a upravte nebo zakažte zásadu. Například pokud se zobrazí, že konkrétní zásady přiřazení efektivně odepření služby endpoint protection, můžete použít odkaz pro přístup k zásadám a upravit nebo zakázat.
  - V seznamu zásad uvidíte efektivní použití zásady na vaše předplatné nebo skupinu pro správu. To znamená, že jsou nastavení jednotlivých zásad, které se vztahují k oboru vzít v úvahu a jsou součástí souhrnné výsledky akci těmito zásadami. Například pokud v jedné přiřazení zásady zakázaly, ale v jiném nastavená na AuditIfNotExist, kumulativní efekt použije AuditIfNotExist. Aktivní efekt vždy přednost.
  - Tyto zásady vliv může být: Připojit, Audit, AuditIfNotExists, zakáže, DeployIfNotExists, zakázané. Další informace o použití efektů, naleznete v tématu [pochopit zásady účinky](../governance/policy/concepts/effects.md).

   ![zásady obrazovky](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - Při zobrazení můžete přiřadit zásady, můžete zobrazit více přiřazení a vy vidíte, konfiguraci každé přiřazení sama o sobě.

## <a name="edit-security-policies"></a>Upravení zásad zabezpečení
Můžete upravit výchozí zásady zabezpečení pro každé z vašich předplatných Azure a skupin pro správu v [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Pokud chcete upravit zásady zabezpečení, musíte být vlastníkem, přispěvatelem nebo správcem zabezpečení daného předplatného nebo skupiny pro správu, která ho obsahuje.

Pokyny o tom, jak upravit zásady zabezpečení ve službě Azure Policy najdete v tématu a [vytvoření a Správa zásad pro vynucování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md).

Můžete upravit zásady zabezpečení pomocí portálu Azure Policy, prostřednictvím rozhraní REST API nebo pomocí Windows Powershellu. Následující příklad uvádí pokyny k úpravám pomocí rozhraní REST API.

### <a name="configure-a-security-policy-using-the-rest-api"></a>Konfigurace zásad zabezpečení pomocí rozhraní REST API

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
- **{policyAssignmentName}**  zadejte [název přiřazení zásady relevantní](#policy-names).
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


### Referenční příručce o zásadách názvy <a name="policy-names"></a>

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
|Posouzení ohrožení zabezpečení |Monitorování ohrožení zabezpečení virtuálních počítačů ve službě Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Brána firewall webových aplikací |Monitorovat nechráněné webové aplikace ve službě Azure Security Center |webApplicationFirewallMonitoringEffect |
|Brána firewall příští generace |Monitorovat nechráněné koncové body sítě ve službě Azure Security Center| |


### <a name="who-can-edit-security-policies"></a>Kdo může upravit zásady zabezpečení?
Security Center používá Role-Based řízení přístupu (RBAC), který poskytuje předdefinované role, které je možné přiřadit uživatelům, skupinám a službám v Azure. Když uživatelé otevřou Security Center, uvidí jenom informace týkající se prostředky, ke kterým mají přístup. Což znamená, že uživatelé mají přiřazenou roli vlastník, Přispěvatel nebo Čtenář na předplatné nebo skupinu prostředků, které prostředek patří. Kromě těchto rolí existují ve službě Security Center dvě specifické role:

- Čtenář zabezpečení: Zobrazení oprávnění ke službě Security Center, což zahrnuje doporučení, výstrahy, zásady a stav, ale jejich nemůže provádět změny.
- Správce zabezpečení: Mají práva zobrazení stejné jako čtenář zabezpečení, a můžete také aktualizovat zásady zabezpečení a rušit doporučení a výstrahy.



## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak upravit zásady zabezpečení ve službě Azure Policy. Další informace o službě Security Center najdete v následujících článcích:

* [Průvodce Azure Security Center plánováním a provozem](security-center-planning-and-operations-guide.md): Zjistěte, jak naplánovat a pochopit aspekty návrhu pro Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md): Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Správa a reakce na výstrahy zabezpečení ve službě Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat a zpracovávat výstrahy zabezpečení.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak monitorovat stav partnerských řešení.
* [Získejte potřebný Přehled celého tenanta pro Azure Security Center](security-center-management-groups.md): Zjistěte, jak vytvořit skupiny pro správu pro Azure Security Center.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md): Získejte odpovědi na nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

Další informace o Azure Policy najdete v tématu [Co je Azure Policy](../azure-policy/azure-policy-introduction.md).

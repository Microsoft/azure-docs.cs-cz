---
title: Práce se zásadami zabezpečení | Microsoft Docs
description: Tento článek popisuje, jak pracovat se zásadami zabezpečení v Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: memildin
ms.openlocfilehash: 488210e4f2c5d3a8a978079d0c7293ce091d998b
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71338776"
---
# <a name="working-with-security-policies"></a>Práce se zásadami zabezpečení

Tento článek vysvětluje, jak se konfigurují zásady zabezpečení, a jak je zobrazit v Security Center. Azure Security Center automaticky přiřadí do každého předplatného, které je zaregistrované, [integrované zásady zabezpečení](security-center-policy-definitions.md) . Můžete je nakonfigurovat v [Azure Policy](../governance/policy/overview.md), což také umožňuje nastavit zásady napříč skupinami pro správu a mezi několika předplatnými.

Pokyny k nastavení zásad pomocí prostředí PowerShell najdete v tématu [rychlý Start: Vytvořte přiřazení zásady pro identifikaci prostředků, které nedodržují předpisy, pomocí](../governance/policy/assign-policy-powershell.md)modulu Azure PowerShell.

>[!NOTE]
> Security Center spustil svou integraci s Azure Policy. Stávající zákazníci budou automaticky migrováni na novou předdefinovanou iniciativu v Azure Policy místo na předchozí zásady zabezpečení v Security Center. Tato změna nebude mít vliv na vaše prostředky nebo prostředí s výjimkou přítomnosti nové iniciativy v Azure Policy.

## <a name="what-are-security-policies"></a>Co jsou zásady zabezpečení?
Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení. V Azure Policy můžete definovat zásady pro vaše předplatná Azure a přizpůsobit je vašemu typu úlohy nebo citlivosti vašich dat. Například aplikace, které používají regulovaná data, jako jsou osobní údaje nebo zákaznická data, mohou vyžadovat vyšší úroveň zabezpečení než jiné úlohy. Chcete-li nastavit zásadu napříč předplatnými nebo skupinami pro správu, nastavte je v [Azure Policy](../governance/policy/overview.md).

Vaše zásady zabezpečení řídí doporučení zabezpečení, která se zobrazí v Azure Security Center. Dodržování předpisů můžete monitorovat, abyste mohli identifikovat potenciální ohrožení zabezpečení a zmírnit hrozby. Další informace o tom, jak určit, která možnost je pro vás vhodná, najdete v seznamu [integrovaných zásad zabezpečení](security-center-policy-definitions.md).

Pokud povolíte Security Center, jsou zásady zabezpečení integrované s Security Center se projeví v Azure Policy jako integrovaná iniciativa v kategorii Security Center. Předdefinovaná iniciativa se automaticky přiřadí všem Security Center registrovaným předplatným (úrovně Free nebo Standard). Integrovaná iniciativa obsahuje pouze zásady auditu.


### <a name="management-groups"></a>Skupiny pro správu
Pokud má vaše organizace mnoho předplatných, možná budete potřebovat způsob, jak efektivně spravovat přístup, zásady a dodržování předpisů pro tato předplatná. Skupiny pro správu Azure poskytují úroveň rozsahu nad předplatnými. Předplatná uspořádáte do kontejnerů označovaných jako skupiny pro správu a na tyto skupiny pro správu použijete své zásady správného řízení. Všechna předplatná v rámci skupiny pro správu automaticky dědí zásady, které se vztahují na skupinu pro správu. Každý adresář obdrží jednu skupinu pro správu nejvyšší úrovně označovanou jako kořenová skupina pro správu. Tato kořenová skupina pro správu je integrovaná do hierarchie tak, aby pod ní spadaly všechny skupiny pro správu a všechna předplatná. Tato kořenová skupina pro správu umožňuje používání globálních zásad a přiřazení RBAC na úrovni adresáře. Chcete-li nastavit skupiny pro správu pro použití s Azure Security Center, postupujte podle pokynů v části [získání viditelnosti pro Azure Security Center v rámci tenanta](security-center-management-groups.md).

> [!NOTE]
> Je důležité, abyste rozuměli hierarchii skupin pro správu a předplatných. Další informace o skupinách pro správu, kořenových skupinách pro správu a přístupu ke skupinám pro správu najdete v tématu věnovaném [uspořádání prostředků pomocí skupin pro správu Azure](../governance/management-groups/overview.md#root-management-group-for-each-directory).
>

## <a name="how-security-policies-work"></a>Způsob fungování zásad zabezpečení
Security Center automaticky vytváří výchozí zásady zabezpečení pro každé z vašich předplatných Azure. Zásady v Azure Policy můžete upravit, abyste mohli provádět následující akce:
- Vytvoření nových definic zásad
- Přiřazení zásad napříč skupinami pro správu a předplatnými, které můžou reprezentovat celou organizaci nebo obchodní jednotku v rámci organizace
- Monitorování dodržování zásad

Další informace o službě Azure Policy najdete v tématu [Vytvoření a správa zásad pro vynucování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md).

Zásada Azure se skládá z následujících součástí:

- **Zásada** je pravidlo.
- **Iniciativa** je kolekce zásad.
- **Přiřazení** je aplikace iniciativy nebo zásady konkrétního oboru (skupina pro správu, předplatné nebo skupina prostředků).

## <a name="view-security-policies"></a>Zobrazení zásad zabezpečení

Zobrazení zásad zabezpečení ve službě Security Center:

1. Na řídicím panelu **Security Center** vyberte **zásady zabezpečení**.

    ![Okno Správa zásad](./media/security-center-policies/security-center-policy-mgt.png)

   Na obrazovce **Správa zásad** můžete zobrazit počet skupin pro správu, odběry a pracovní prostory i strukturu skupiny pro správu.

   > [!NOTE]
   > Řídicí panel Security Center může zobrazit větší počet předplatných v rámci **pokrytí** předplatného, než je počet předplatných zobrazený v části **Správa zásad**. V části Pokrytí předplatného se zobrazuje počet předplatných úrovně Standard a Free a „nepokrytých“ předplatných. Předplatná, která nejsou pokrytá, nemají povolený Security Center a nejsou zobrazená v části **Správa zásad**.
   >

2. Vyberte předplatné nebo skupinu pro správu, jejichž zásady chcete zobrazit.

   - Obrazovka **zásady zabezpečení** odráží akci provedenou zásadami přiřazenými k předplatnému nebo skupině pro správu, kterou jste vybrali.
   - V horní části použijte poskytnuté odkazy k otevření každého **přiřazení** zásady, které platí pro předplatné nebo skupinu pro správu. Pomocí odkazů můžete získat přístup k přiřazení a upravit nebo zakázat zásady. Pokud se například zobrazí, že konkrétní přiřazení zásad efektivně odmítá službu Endpoint Protection, můžete použít odkaz pro přístup k zásadám a jejich úpravu nebo zakázání.
   - V seznamu zásad se můžete podívat na efektivní použití zásad pro vaše předplatné nebo skupinu pro správu. To znamená, že se vezme v úvahu nastavení každé zásady, která se vztahují k oboru, a vy budete mít kumulativní výsledek toho, co zásady provádí. Například pokud je v jednom přiřazení zásada zakázána, ale v jiném je nastavená na AuditIfNotExist, pak se Kumulativní efekt použije AuditIfNotExist. Vyšší aktivní efekt vždy má přednost.
   - Účinek zásad může být: Připojení, audit, AuditIfNotExists, zamítnutí, DeployIfNotExists, zakázáno. Další informace o tom, jak se efekty používají, najdete v tématu [vysvětlení efektů zásad](../governance/policy/concepts/effects.md).

   ![obrazovka zásady](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> Když si zobrazíte přiřazené zásady, uvidíte několik přiřazení a uvidíte, jak se každé přiřazení konfiguruje sami.

## <a name="edit-security-policies"></a>Upravení zásad zabezpečení
Ve službě [Azure Policy](../governance/policy/tutorials/create-and-manage.md) můžete upravit výchozí zásady zabezpečení pro každou z vašich skupin pro správu a každé z vašich předplatných Azure. Chcete-li upravit zásadu zabezpečení, musíte být vlastníkem nebo správcem zabezpečení předplatného nebo obsahující skupiny pro správu.

Pokyny k úpravám zásad zabezpečení v Azure Policy najdete v tématech a [vytváření a Správa zásad pro vymáhání dodržování předpisů](../governance/policy/tutorials/create-and-manage.md).

Zásady zabezpečení můžete upravovat prostřednictvím Azure Policyového portálu prostřednictvím REST API nebo pomocí Windows PowerShellu. Následující příklad uvádí pokyny pro úpravy pomocí REST API.


## <a name="disable-security-policies"></a>Zakázat zásady zabezpečení
Pokud výchozí zásady zabezpečení generují doporučení, které není pro vaše prostředí důležité, můžete je zastavit tím, že zakážete definici zásady, která posílá doporučení.
Další informace o doporučeních najdete v tématu [Správa doporučení zabezpečení](security-center-recommendations.md).

1. V Security Center v části **zásady & dodržování předpisů** klikněte na **zásady zabezpečení**.

   ![Správa zásad](./media/tutorial-security-policy/policy-management.png)

2. Klikněte na předplatné nebo skupinu pro správu, pro kterou chcete doporučení zakázat.

   > [!Note]
   > Pamatujte, že skupina pro správu použije své zásady na své odběry. Proto pokud zakážete zásady předplatného a předplatné patří do skupiny pro správu, která stále používá stejné zásady, pak budou doporučení k zásadám dál dostávat. Zásada bude i nadále použita z úrovně správy a doporučení budou vygenerována.

1. Klikněte na přiřazenou zásadu.

   ![zakázat zásadu](./media/tutorial-security-policy/security-policy.png)

1. V části **parametry** vyhledejte zásadu, která vyvolá doporučení, které chcete zakázat, a v rozevíracím seznamu vyberte možnost **zakázáno** .

   ![zakázat zásadu](./media/tutorial-security-policy/disable-policy.png)
1. Klikněte na **Uložit**.
   > [!Note]
   > Změny zásad, které se projeví, můžou trvat až 12 hodin.


### <a name="configure-a-security-policy-using-the-rest-api"></a>Konfigurace zásad zabezpečení pomocí REST API

V rámci nativní integrace s Azure Policy Azure Security Center umožňuje využít REST API Azure Policy k vytváření přiřazení zásad. Následující pokyny vás provedou vytvořením přiřazení zásad a přizpůsobením stávajících přiřazení. 

Důležité pojmy v Azure Policy: 

- **Definice zásady** je pravidlo 

- **Iniciativa** je kolekcí definic zásad (pravidel) 

- **Přiřazení** je aplikace iniciativy nebo zásady konkrétního oboru (skupina pro správu, předplatné atd.). 

Security Center má předdefinovanou iniciativu, která zahrnuje všechny zásady zabezpečení. Aby bylo možné vyhodnotit zásady Security Center v prostředcích Azure, měli byste vytvořit přiřazení ke skupině pro správu nebo předplatnému, které chcete vyhodnotit.

Integrovaná iniciativa má ve výchozím nastavení povolené všechny zásady Security Center. Můžete zakázat určité zásady z předdefinované iniciativy, například můžete použít všechny zásady Security Center s výjimkou **firewallu webových aplikací**, a to tak, že změníte hodnotu parametru efektu zásady na Disabled ( **zakázáno**). 

### <a name="api-examples"></a>Příklady rozhraní API

V následujících příkladech nahraďte tyto proměnné:

- **{Scope}** zadejte název skupiny pro správu nebo předplatného, na které zásady aplikujete.
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

### Odkazy na názvy zásad<a name="policy-names"></a>

|Název zásady v Security Center|Název zásad zobrazený v Azure Policy |Název parametru efektu zásad|
|----|----|----|
|Šifrování SQL |Monitorování nešifrované databáze SQL v Azure Security Center |sqlEncryptionMonitoringEffect| 
|SQL Auditing (Auditování SQL) |Monitorování neauditované databáze SQL v Azure Security Center |sqlAuditingMonitoringEffect|
|Aktualizace systému |Monitorovat chybějící aktualizace systému v Azure Security Center |systemUpdatesMonitoringEffect|
|Šifrování úložiště |Auditovat chybějící šifrování objektů BLOB pro účty úložiště |storageEncryptionMonitoringEffect|
|Síťový přístup JIT |Monitorovat možný přístup k síti za běhu (just in time) v Azure Security Center |jitNetworkAccessMonitoringEffect |
|Adaptivní řízení aplikací |Monitorování možného seznamu povolených aplikací v Azure Security Center |adaptiveApplicationControlsMonitoringEffect|
|Skupiny zabezpečení sítě |Monitorování opravňujícího přístupu k síti v Azure Security Center |networkSecurityGroupsMonitoringEffect| 
|Konfigurace zabezpečení |Monitorování ohrožení zabezpečení operačního systému v Azure Security Center |systemConfigurationsMonitoringEffect| 
|Ochrana koncového bodu |Monitorovat chybějící Endpoint Protection v Azure Security Center |endpointProtectionMonitoringEffect |
|Šifrování disku |Monitorování nešifrovaných disků virtuálních počítačů v Azure Security Center |diskEncryptionMonitoringEffect|
|Posouzení ohrožení zabezpečení |Monitorování ohrožení zabezpečení virtuálních počítačů v Azure Security Center |vulnerabilityAssessmentMonitoringEffect|
|Firewall webových aplikací |Monitorování nechráněné webové aplikace v Azure Security Center |webApplicationFirewallMonitoringEffect |
|Brána firewall příští generace |Monitorování nechráněných koncových bodů sítě v Azure Security Center| |


### <a name="who-can-edit-security-policies"></a>Kdo může upravovat zásady zabezpečení?
Security Center používá Access Control na základě rolí (RBAC), která poskytuje předdefinované role, které se dají přiřadit uživatelům, skupinám a službám v Azure. Když uživatelé otevřou Security Center, uvidí jenom informace, které souvisejí s prostředky, ke kterým mají přístup. To znamená, že uživatelé mají přiřazenou roli vlastník, přispěvatel nebo čtenář do předplatného nebo skupiny prostředků, do které prostředek patří. Kromě těchto rolí existují ve službě Security Center dvě specifické role:

- Čtecí modul zabezpečení: Mít oprávnění k zobrazení Security Center, což zahrnuje doporučení, výstrahy, zásady a stav, ale nemůže provádět změny.
- Správce zabezpečení: Mít stejná oprávnění k zobrazení jako čtenář zabezpečení a můžou taky aktualizovat zásady zabezpečení a zrušit doporučení a upozornění.



## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak upravit zásady zabezpečení v Azure Policy. Další informace o službě Security Center najdete v následujících článcích:

* [Průvodce plánováním a provozem Azure Security Center](security-center-planning-and-operations-guide.md): Naučte se plánovat a porozumět hlediskům návrhu Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md): Zjistěte, jak monitorovat stav svých prostředků Azure.
* [Správa a reakce na výstrahy zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md): Zjistěte, jak spravovat a zpracovávat výstrahy zabezpečení.
* [Monitorování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md): Zjistěte, jak monitorovat stav partnerských řešení.
* [Získat viditelnost pro Azure Security Center v úrovni tenanta](security-center-management-groups.md): Přečtěte si, jak nastavit skupiny pro správu pro Azure Security Center.
* [Nejčastější dotazy k Azure Security Center](security-center-faq.md): Získejte odpovědi na nejčastější dotazy k používání této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení Azure a dodržování předpisů.

Další informace o Azure Policy najdete v tématu [Co je Azure Policy](../governance/policy/overview.md).

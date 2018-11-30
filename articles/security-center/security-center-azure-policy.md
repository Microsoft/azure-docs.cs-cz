---
title: Zásady zabezpečení služby Azure Security Center můžete nastavit jako součást zásady Azure a zobrazit ve službě Security Center | Dokumentace Microsoftu
description: Tento dokument vám umožní nastavit zásady ve službě Azure Policy, nebo je zobrazit ve službě Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: mbaldwin
editor: ''
ms.assetid: cd906856-f4f9-4ddc-9249-c998386f4085
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/25/2018
ms.author: rkarlin
ms.openlocfilehash: 330b66e64484417e50f39c35cf90a6fd62b1e888
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52334665"
---
# <a name="view-security-policies"></a>Zobrazit zásady zabezpečení

Tento článek vysvětluje, jak jsou nakonfigurované zásady zabezpečení a jak zobrazit ve službě Security Center. Azure Security Center automaticky přiřadí jeho [integrované bezpečnostní zásady](security-center-policy-definitions.md) na každé předplatné, které zprovoznění. Můžete nakonfigurovat v [Azure Policy](../azure-policy/azure-policy-introduction.md), který také umožňuje nastavit zásady napříč skupinami pro správu a napříč několika předplatnými.

Pokyny o tom, jak nastavit zásady pomocí Powershellu najdete v tématu [rychlý start: vytvoření přiřazení zásady pro identifikaci neodpovídajících prostředků pomocí modulu Azure RM Powershellu](../azure-policy/assign-policy-definition-ps.md).



## <a name="what-are-security-policies"></a>Co jsou zásady zabezpečení?
Zásady zabezpečení definují požadovanou konfiguraci úloh a pomáhají zajišťovat dodržování předpisů společnosti nebo soulad se zákonnými požadavky na zabezpečení. Ve službě Azure Policy můžete definovat zásady pro vaše předplatná Azure a je typu úlohy nebo citlivosti dat. přizpůsobit. Aplikace, které používají regulovaná data, jako jsou identifikovatelné osobní údaje, například může vyžadovat vyšší úroveň zabezpečení než jiné úlohy. Nastavit zásady napříč předplatnými nebo skupin pro správu, je nastavit [Azure Policy](../azure-policy/azure-policy-introduction.md).



Zásady zabezpečení vliv na doporučení zabezpečení, získáte v Azure Security Center. Můžete monitorovat dodržování zásad v nich můžete najít potenciální nedostatky zabezpečení a zmírnit hrozby. Další informace o tom, jak určit, která možnost je pro vás vhodná, najdete v seznamu [integrované bezpečnostní zásady](security-center-policy-definitions.md).

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

U prostředku se vyhodnotí zásady, které jsou k němu přiřazené, a ten pak získá koeficient dodržování předpisů podle počtu zásad, jejichž předpisy dodržuje.

## <a name="who-can-edit-security-policies"></a>Kdo může upravit zásady zabezpečení?
Security Center používá Role-Based řízení přístupu (RBAC), který poskytuje předdefinované role, které je možné přiřadit uživatelům, skupinám a službám v Azure. Když uživatelé otevřou Security Center, uvidí jenom informace týkající se prostředky, ke kterým mají přístup. Což znamená, že uživatelé mají přiřazenou roli vlastník, Přispěvatel nebo Čtenář na předplatné nebo skupinu prostředků, které prostředek patří. Kromě těchto rolí existují ve službě Security Center dvě specifické role:

- Čtenář zabezpečení: mají zobrazit práva ke službě Security Center, což zahrnuje doporučení, výstrahy, zásady a stav, ale že nemůže provádět změny.
- Správce zabezpečení: mají práva zobrazení stejné jako čtenář zabezpečení, a můžete také aktualizovat zásady zabezpečení a rušit doporučení a výstrahy.

## <a name="edit-security-policies"></a>Upravení zásad zabezpečení
Můžete upravit výchozí zásady zabezpečení pro každé z vašich předplatných Azure a skupin pro správu v [Azure Policy](../governance/policy/tutorials/create-and-manage.md). Pokud chcete upravit zásady zabezpečení, musíte být vlastníkem, přispěvatelem nebo správcem zabezpečení daného předplatného nebo skupiny pro správu, která ho obsahuje.

Pokyny o tom, jak upravit zásady zabezpečení ve službě Azure Policy najdete v tématu a [vytvoření a Správa zásad pro vynucování dodržování předpisů](../governance/policy/tutorials/create-and-manage.md).

## <a name="view-security-policies"></a>Zobrazit zásady zabezpečení

Zobrazení zásad zabezpečení ve službě Security Center:

1. V **Security Center** řídicího panelu, vyberte **zásady zabezpečení**.

    ![Okno Správa zásad](./media/security-center-policies/security-center-policy-mgt.png)

  V **Správa zásad** obrazovky, zobrazí se počet skupin pro správu, předplatných a pracovních prostorů, jakož i strukturu skupin pro správu.

  > [!NOTE]
  > - Řídicí panel Security Center může zobrazit větší počet předplatných v rámci **pokrytí předplatného** než počet předplatných, které jsou uvedené v části **Správa zásad**. V části Pokrytí předplatného se zobrazuje počet předplatných úrovně Standard a Free a „nepokrytých“ předplatných. Předplatná "nejsou zahrnuta" nemají povolené Security Center a nejsou zobrazeny v části **Správa zásad**.
  >

  Ve sloupcích tabulky se zobrazuje:

 - **Přiřazení iniciativy zásad** – Security Center [integrované zásady](security-center-policy-definitions.md) a iniciativy, které jsou přiřazeny předplatné nebo skupinu pro správu.
 - **Dodržování předpisů** – celkové skóre dodržování předpisů pro skupinu pro správu, předplatného nebo pracovního prostoru. Skóre je váženým průměrem přiřazení. Tento vážený průměr ovlivňuje počet zásad v jednom přiřazení a počet prostředků, na které se přiřazení vztahuje.

 Například pokud předplatné obsahuje dva virtuální počítače a má přiřazenou iniciativu s pěti zásadami, pak máte v předplatném 10 interních hodnocení. Pokud jeden z virtuálních počítačů nedodržuje předpisy dvou zásad, pak je celkové skóre dodržování předpisů přiřazení vašeho předplatného 80 %.

 - **Pokrytí** – identifikuje cenové úrovně Free nebo Standard, která skupina pro správu, předplatné nebo pracovní prostor běží na.  Další informace o cenových úrovních služby Security Center najdete na stránce s [cenami](security-center-pricing.md).
 - **Nastavení** – předplatné má odkaz **upravit nastavení**. Výběr **upravit nastavení** vám umožní aktualizovat vaše [nastavení Centra zabezpečení](security-center-policies-overview.md) pro každé předplatné nebo skupinu pro správu.

2. Vyberte předplatné nebo správu skupinu, jejichž zásady, které chcete zobrazit.

  - **Zásady zabezpečení** obrazovce se projeví akci provedenou na základě zásad přiřazených na předplatné nebo skupinu pro správu jste vybrali.
  - V horní části stránky, použijte odkazy otevřete jednotlivé zásady **přiřazení** , která se vztahuje na předplatné nebo skupinu pro správu. Odkazy můžete použít pro přístup k přiřazení a upravte nebo zakažte zásadu. Například pokud se zobrazí, že konkrétní zásady přiřazení efektivně odepření služby endpoint protection, můžete použít odkaz pro přístup k zásadám a upravit nebo zakázat.
  - V seznamu zásad uvidíte efektivní použití zásady na vaše předplatné nebo skupinu pro správu. To znamená, že jsou nastavení jednotlivých zásad, které se vztahují k oboru vzít v úvahu a jsou součástí souhrnné výsledky akci těmito zásadami. Například pokud v jedné přiřazení zásady zakázaly, ale v jiném nastavená na AuditIfNotExist, kumulativní efekt použije AuditIfNotExist. Aktivní efekt vždy přednost.
  - Tyto zásady vliv může být: připojení, Audit, AuditIfNotExists, Deny DeployIfNotExists, zakázané. Další informace o použití efektů, naleznete v tématu [pochopit zásady účinky](../governance/policy/concepts/effects.md).

   ![zásady obrazovky](./media/security-center-policies/policy-screen.png)

> [!NOTE]
> - Při zobrazení můžete přiřadit zásady, můžete zobrazit více přiřazení a vy vidíte, konfiguraci každé přiřazení sama o sobě.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak ve službě Security Center konfigurovat zásady zabezpečení. Další informace o službě Security Center najdete v následujících článcích:

* [Průvodce plánováním a provozem služby Azure Security Center](security-center-planning-and-operations-guide.md) – Zjistěte, jak naplánovat a pochopit aspekty návrhu pro Azure Security Center.
* [Sledování stavu zabezpečení v Azure Security Center](security-center-monitoring.md) – Naučte se sledovat stav svých prostředků Azure.
* [Správa a zpracování výstrah zabezpečení v Azure Security Center](security-center-managing-and-responding-alerts.md) – Zjistěte, jak spravovat výstrahy zabezpečení a reagovat na ně.
* [Sledování partnerských řešení pomocí Azure Security Center](security-center-partner-solutions.md) – Zjistěte, jak pomocí Azure Security Center sledovat stav vašich partnerských řešení.
* [Získání viditelnosti v rámci celého tenanta pro Azure Security Center](security-center-management-groups.md) – Zjistěte, jak nastavit skupiny pro správu pro Azure Security Center.
* [Azure Security Center – nejčastější dotazy](security-center-faq.md) – Získejte odpovědi na nejčastější dotazy týkající se použití této služby.
* [Blog o zabezpečení Azure](https://blogs.msdn.com/b/azuresecurity/): Přečtěte si příspěvky o zabezpečení a dodržování předpisů Azure.

Další informace o Azure Policy najdete v tématu [Co je Azure Policy](../azure-policy/azure-policy-introduction.md).

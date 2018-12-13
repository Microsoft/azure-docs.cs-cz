---
title: Zásady správného řízení infrastruktury Azure DevTest Labs
description: Tento článek obsahuje pokyny pro zásady správného řízení infrastruktury Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 47f3e8ab14ecd50e958c57ba4c8f9f098fd5bb7b
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868357"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Zásady správného řízení infrastruktury Azure DevTest Labs – Správa nákladů a vlastnictví
Náklady a vlastnictví jsou primární obavy, když zvažte sestavení vaše vývojové a testovací prostředí. V této části najdete informace, které vám pomůže optimalizovat náklady a zarovnat vlastnictví napříč vaším prostředím.

## <a name="optimize-for-cost"></a>Optimalizace nákladů

### <a name="question"></a>Otázka
Jak se můžete optimalizovat náklady v rámci mého prostředí DevTest Labs?

### <a name="answer"></a>Odpověď
Existují celou řadou integrovaných funkcí služby DevTest Labs, který vám pomůže optimalizovat náklady. Naleznete v tématu [náklady správy prahy](devtest-lab-configure-cost-management.md) [, zásady a](devtest-lab-set-lab-policy.md) články k omezení aktivity vašich uživatelů. 

Jak využívat DevTest Labs pro vývojová a testovací úlohy, můžete zvážit použití [zvýhodněné předplatné Enterprise pro vývoj/testování](https://azure.microsoft.com/offers/ms-azr-0148p/), jako součást vaší smlouvy Enterprise. Případně, pokud jste zákazník s průběžnými platbami, můžete chtít zvážit [platit jako můžete přejít DevTest nabídka](https://azure.microsoft.com/offers/ms-azr-0023p/).

Tento přístup vám poskytuje řadu výhod:

- Speciální nižší sazby pro vývoj/testování pro Windows virtual machines, cloud services, HDInsight, App Service a Logic Apps
- Skvělé sazby Enterprise Agreement (EA) na ostatní služby Azure
- Přístup k exkluzivním imagím pro vývoj/testování v Galerii, včetně Windows 8.1 a Windows 10
 
Prostředky Azure v rámci podniku předplatného pro vývoj/testování můžou používat jenom aktivní předplatitelé sady Visual Studio (standardní předplatná, roční Cloudová předplatná a měsíční Cloudová předplatná). Ale koncoví uživatelé přístup k aplikaci poskytnout zpětnou vazbu nebo provádět předávací testy. Využívání prostředků v rámci tohoto předplatného je omezené na vývoj a testování aplikací a neexistuje žádná garance doby provozu.

Pokud se rozhodnete použít nabídku DevTest, mějte na paměti, že tato výhoda je určená pro vývoj a testování aplikací. Používání v rámci předplatného není vázané žádnou finančně jištěnou smlouvu SLA, s výjimkou použití Azure DevOps a HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Definování přístupu podle role v organizaci
### <a name="question"></a>Otázka
Jak definovat řízení přístupu na základě rolí pro moje DevTest Labs prostředí, abyste, který může IT řídit při vývojáři a testování můžete své práci? 

### <a name="answer"></a>Odpověď
Neexistuje široké vzor, ale podrobností závisí na vaší organizaci.

Centrální IT by měl vlastní pouze co je potřeba a umožňuje týmům projektu a aplikace potřebné stupeň ovládacího prvku. Obvykle to znamená, že tento centrální IT vlastníkem předplatného a zpracovává základní IT funkcemi, jako je například konfigurace sítí. Sada **vlastníky** předplatného musí být malé. Tyto vlastníky můžete nominujte další vlastníky, když je potřeba, nebo použít zásady na úrovni předplatného, například "žádná veřejná IP adresa".

Může existovat podmnožinu uživatelů, které vyžadují přístup v rámci předplatného, jako je například podpora úrovně 1 nebo 2. V takovém případě vám doporučujeme, abyste udělili těmto uživatelům **Přispěvatel** přístup tak, aby nelze spravovat prostředky, ale poskytnout uživatelský přístup nebo úpravě zásad.

DevTest Labs prostředků musí být vlastněn vlastníky, kteří jsou blízko aplikace project/team. Je to proto, že tito uživatelé pochopit jejich požadavky z hlediska počítače a požadovaný software. Ve většině organizací vlastníka tohoto prostředku DevTest Labs je obvykle vedoucí projektu nebo vývoje. Tohoto vlastníka mohou spravovat uživatele a zásad v testovacím prostředí a můžete spravovat všechny virtuální počítače v prostředí DevTest Labs.

Členové týmu aplikace project a měli byste přidat k roli uživatele DevTest Labs. Tito uživatelé můžou vytvářet virtuální počítače (v řádku pomocí testovacího prostředí a zásad na úrovni předplatného). Mohou také spravovat vlastní virtuální počítače. Jejich nemůže spravovat virtuální počítače, které patří jiným uživatelům.

Další informace najdete v tématu [Základní kostra Azure enterprise – zásady správného řízení předplatná](/azure/architecture/cloud-adoption/appendix/azure-scaffold) dokumentaci.


## <a name="next-steps"></a>Další postup
Zobrazit [podnikové zásady a dodržování předpisů](devtest-lab-guidance-governance-policy-compliance.md).

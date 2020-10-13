---
title: Správa nákladů a vlastnictví v Azure DevTest Labs
description: Tento článek poskytuje informace, které vám pomůžou s optimalizací a vyrovnáním vlastnictví napříč vaším prostředím.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: dbf0bb9cae87a51eb6e0a4fb25c700ec6f423ff1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476253"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Zásady správného řízení infrastruktury Azure DevTest Labs – Správa nákladů a vlastnictví
Náklady a vlastnictví jsou primárními aspekty, když zvažte vytvoření vývojových a testovacích prostředí. V této části najdete informace, které vám pomůžou s optimalizací a vyrovnáním vlastnictví napříč vaším prostředím.

## <a name="optimize-for-cost"></a>Optimalizovat pro náklady

### <a name="question"></a>Otázka
Jak se dá optimalizovat pro náklady v prostředí DevTest Labs?

### <a name="answer"></a>Odpověď
Existuje řada integrovaných funkcí DevTest Labs, které vám pomůžou optimalizovat náklady. Pokud chcete omezit aktivity vašich uživatelů, přečtěte si článek [Správa nákladů, prahové hodnoty](devtest-lab-configure-cost-management.md) [a zásady](devtest-lab-set-lab-policy.md) . 

Pokud používáte DevTest Labs pro vývojové a testovací úlohy, můžete zvážit použití [výhod Enterprise pro vývoj/testování předplatného](https://azure.microsoft.com/offers/ms-azr-0148p/)v rámci smlouva Enterprise. Případně, pokud se platíte jako zákazník, možná budete chtít vzít v úvahu [nabídku s průběžnými platbami za DevTest](https://azure.microsoft.com/offers/ms-azr-0023p/).

Tento přístup poskytuje mnoho výhod:

- Speciální nižší sazby pro vývoj a testování na virtuálních počítačích s Windows, Cloud Services, HDInsight, App Service a Logic Apps
- Skvělé smlouva Enterprise (EA) tarify dalších služeb Azure
- Přístup k exkluzivním obrázkům pro vývoj a testování v galerii, včetně Windows 8.1 a Windows 10
 
Prostředky Azure běžící v rámci předplatného Enterprise pro vývoj/testování můžou používat jenom aktivní předplatitelé sady Visual Studio (standardní předplatná, roční cloudová předplatná a měsíční předplatné cloudu). Koncoví uživatelé ale mají k aplikaci přístup, aby mohli poskytovat zpětnou vazbu nebo provádět testy pro přijetí. Používání prostředků v rámci tohoto předplatného je omezené na vývoj a testování aplikací a nenabízí se žádná záruka na provozuschopnost.

Pokud se rozhodnete použít nabídku DevTest, mějte na paměti, že tato výhoda je určená výhradně pro vývoj a testování vašich aplikací. Využití v rámci předplatného nenese finančně zajištěnou smlouvu SLA, s výjimkou použití Azure DevOps a HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Definování přístupu na základě role v rámci vaší organizace
### <a name="question"></a>Otázka
Návody pro prostředí DevTest Labs definovat řízení přístupu na základě rolí, aby se zajistilo, že se může řídit, zatímco vývojáři/testy můžou svou práci dělat? 

### <a name="answer"></a>Odpověď
Existuje velký model, ale podrobnosti závisí na vaší organizaci.

Centrální IT oddělení by mělo mít pouze to, co je nezbytné, a povolit týmům projektů a aplikací, aby měli potřebnou úroveň řízení. Obvykle to znamená, že je ústřední IT vlastníkem předplatného a zpracovává základní funkce IT, jako jsou třeba síťové konfigurace. Sada **vlastníků** předplatného musí být malá. Tito vlastníci můžou jmenovat další vlastníky, pokud je potřeba, nebo použít zásady na úrovni předplatného, například bez veřejné IP adresy.

Může existovat podmnožina uživatelů, kteří vyžadují přístup v rámci předplatného, jako je Tier1 nebo podpora vrstvy 2. V takovém případě doporučujeme těmto uživatelům udělit přístup **přispěvatelům** , aby mohli spravovat prostředky, ale neposkytovali přístup k uživatelům nebo neupravují zásady.

Prostředek DevTest Labs by měl vlastnit vlastníci, kteří jsou blízko týmu projektu nebo aplikace. Je to proto, že zná požadavky na počítače a požadovaný software. Ve většině organizací je vlastníkem tohoto prostředku DevTest Labs obvykle vedoucí projektu nebo vývoje. Tento vlastník může spravovat uživatele a zásady v rámci testovacího prostředí a může spravovat všechny virtuální počítače v prostředí DevTest Labs.

Členové týmu projektu nebo aplikace by měli být přidáni do role uživatelé DevTest Labs. Tito uživatelé můžou vytvářet virtuální počítače (v souladu s zásadami testovacího prostředí a na úrovni předplatného). Můžou taky spravovat svoje vlastní virtuální počítače. Nemůžou spravovat virtuální počítače, které patří jiným uživatelům.

Další informace najdete v tématu základní informace o [službě Azure Enterprise lešení – dokumentace zásad správného řízení předplatného](/azure/architecture/cloud-adoption/appendix/azure-scaffold) .


## <a name="next-steps"></a>Další kroky
Viz [podnikové zásady a dodržování předpisů](devtest-lab-guidance-governance-policy-compliance.md).

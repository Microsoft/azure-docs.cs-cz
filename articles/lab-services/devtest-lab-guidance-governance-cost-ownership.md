---
title: Správa nákladů a vlastnictví v laboratořích Azure DevTest Labs
description: Tento článek obsahuje informace, které vám pomohou optimalizovat náklady a zarovnat vlastnictví v celém prostředí.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: b82d338f85f1b43712296ac7f27bdad55f8f1919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561661"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Řízení infrastruktury Azure DevTest Labs – správa nákladů a vlastnictví
Náklady a vlastnictví jsou primární problémy, když uvažujete o vytváření vývojového a testovacího prostředí. V této části najdete informace, které vám pomohou optimalizovat náklady a zarovnat vlastnictví v celém prostředí.

## <a name="optimize-for-cost"></a>Optimalizace pro náklady

### <a name="question"></a>Otázka
Jak lze optimalizovat náklady v prostředí DevTest Labs?

### <a name="answer"></a>Odpověď
Existuje celá řada vestavěných funkcí DevTest Labs, které vám pomohou optimalizovat náklady. Informace o omezení aktivit uživatelů naleznete v článku [Správa nákladů, prahové hodnoty](devtest-lab-configure-cost-management.md) [a zásady.](devtest-lab-set-lab-policy.md) 

Při využívání DevTest Labs pro vývoj a testování úloh, můžete zvážit využití [Enterprise Dev/Test Subscription Benefit](https://azure.microsoft.com/offers/ms-azr-0148p/), jako součást smlouvy Enterprise. Případně, pokud jste Pay as you Go zákazník, možná budete chtít, aby zvážila [Pay-as-you go DevTest nabídku](https://azure.microsoft.com/offers/ms-azr-0023p/).

Tento přístup vám poskytuje řadu výhod:

- Speciální nižší sazby za vývoj a testování na virtuálních počítačích s Windows, cloudových službách, HDInsightu, app service a logických aplikacích
- Sazby za velkou smlouvu enterprise (EA) u jiných služeb Azure
- Přístup k exkluzivním obrázkům pro vývoj a testování v galerii, včetně Windows 8.1 a Windows 10
 
Jenom aktivní předplatitelé Visual Studia (standardní předplatná, roční cloudová předplatná a měsíční cloudová předplatná) můžou používat prostředky Azure spuštěné v rámci podnikového předplatného Pro vývoj a testování. Koncoví uživatelé však mohou přistupovat k aplikaci, aby poskytli zpětnou vazbu nebo provedli akceptační testy. Použití prostředků v rámci tohoto předplatného je omezeno na vývoj a testování aplikací a není nabízena žádná záruka provozu.

Pokud se rozhodnete použít nabídku DevTest, všimněte si, že tato výhoda je výhradně pro vývoj a testování aplikací. Použití v rámci předplatného nenese finančně podporovanou sla, s výjimkou použití Azure DevOps a HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Definování přístupu založeného na rolích v celé organizaci
### <a name="question"></a>Otázka
Jak lze definovat řízení přístupu na základě rolí pro prostředí DevTest Labs, abych zajistil, že IT oddělení může řídit, zatímco vývojáři/test můžou dělat svou práci? 

### <a name="answer"></a>Odpověď
Existuje široký vzorec, nicméně detail závisí na vaší organizaci.

Centrální IT by mělo vlastnit pouze to, co je nezbytné, a umožnit týmům projektu a aplikací mít potřebnou úroveň kontroly. Obvykle to znamená, že centrální IT vlastní předplatné a zpracovává základní funkce IT, jako jsou konfigurace sítě. Sada **vlastníků** pro předplatné by měla být malá. Tito vlastníci mohou nominovat další vlastníky, když je to potřeba, nebo použít zásady na úrovni předplatného, například "Žádná veřejná IP adresa".

Může existovat podmnožina uživatelů, kteří vyžadují přístup přes předplatné, jako je například podpora Tier1 nebo Tier 2. V takovém případě doporučujeme poskytnout těmto uživatelům přístup **přispěvatele,** aby mohli spravovat prostředky, ale neposkytovat přístup uživatelů nebo upravovat zásady.

Prostředek DevTest Labs by měl být vlastněn vlastníky, kteří jsou blízko týmu projektu nebo aplikace. Je to proto, že chápou své požadavky, pokud jde o stroje, a požadovaný software. Ve většině organizací je vlastníkem tohoto prostředku DevTest Labs obvykle vedoucí projektu nebo vývoje. Tento vlastník může spravovat uživatele a zásady v testovacím prostředí a spravovat všechny virtuální uživatele v prostředí DevTest Labs.

Členové týmu projektu/aplikace by měli být přidáni do role Uživatelé devTest Labs. Tito uživatelé mohou vytvářet virtuální počítače (v souladu s testovacím prostředím a zásadami na úrovni předplatného). Mohou také spravovat své vlastní virtuální počítače. Nemohou spravovat virtuální počítače, které patří jiným uživatelům.

Další informace najdete v [tématu Azure enterprise scaffold – prescriptive dokumentace zásad správného předplatného.](/azure/architecture/cloud-adoption/appendix/azure-scaffold)


## <a name="next-steps"></a>Další kroky
Viz [Podnikové zásady a dodržování předpisů](devtest-lab-guidance-governance-policy-compliance.md).

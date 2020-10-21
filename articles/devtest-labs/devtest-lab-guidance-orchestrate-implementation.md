---
title: Orchestrace implementace Azure DevTest Labs
description: Tento článek poskytuje pokyny pro orchestraci implementace Azure DevTest Labs ve vaší organizaci.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1958e818f014b7419a1a33e9453fbad460dfc159
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92330610"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Orchestrace implementace Azure DevTest Labs
Tento článek poskytuje doporučený postup pro rychlé nasazení a implementaci Azure DevTest Labs. Následující obrázek zvýrazňuje celkový proces jako doporučené doprovodné materiály a přitom pozoruje flexibilitu při podpoře různých požadavků a scénářů v oboru.

![Postup pro implementaci Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Předpoklady
V tomto článku se předpokládá, že před implementací pilotního projektu DevTest Labs máte k dispozici následující položky:

- **Předplatné Azure**: pilotní tým má přístup k nasazení prostředků do předplatného Azure. Pokud jsou úlohy jenom vývoj a testování, doporučuje se vybrat nabídku Enterprise DevTest pro další dostupné image a nižší sazby na virtuálních počítačích s Windows.
- Místní **přístup**: v případě potřeby je místní přístup už nakonfigurovaný. Místní přístup je možné provést prostřednictvím připojení VPN typu Site-to-site nebo přes Express Route. Připojení prostřednictvím expresní trasy může obvykle trvat několik týdnů, než začnete s projektem.
- **Pilotní týmy**: u počátečních týmových projektů, které používají laboratoře DevTest Labs, se identifikovaly příslušné vývojové nebo testovací aktivity a navázaly požadavky na cíle/cíle pro tyto týmy.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Milník 1: zřízení počáteční síťové topologie a návrhu
První oblastí fokusu při nasazení Azure DevTest Labs řešení je navázání plánovaného připojení pro virtuální počítače. Následující kroky popisují nezbytné postupy:

1. Definujte **počáteční rozsahy IP adres** , které jsou přiřazené k předplatnému DevTest Labs v Azure. Tento krok vyžaduje předpověď očekávaného využití v počtu virtuálních počítačů, aby bylo možné poskytnout dostatečně velký blok pro budoucí rozšíření.
2. Identifikujte **metody požadovaného přístupu** do DevTest Labs (například externí/interní přístup). Klíčovým bodem tohoto kroku je určit, jestli virtuální počítače mají veřejné IP adresy (k dispozici přímo z Internetu).
3. Identifikujte a stanovujte **metody připojení** ke zbytku cloudového prostředí Azure a místním službám. Pokud je povoleno vynucené směrování pomocí Express Route, je pravděpodobné, že virtuální počítače budou k procházení podnikové brány firewall potřebovat vhodné konfigurace proxy serveru.
4. Pokud mají být virtuální počítače **připojené k doméně**, určete, jestli se připojí ke cloudové doméně (třeba k adresářovým službám AAD) nebo k místní doméně. V části místní určete, kterou organizační jednotku (OU) v rámci služby Active Directory připojíte k virtuálním počítačům. Kromě toho potvrďte, že uživatelé mají přístup k připojení (nebo vytvořte účet služby, který má možnost vytvářet záznamy počítačů v doméně).

## <a name="milestone-2-deploy-the-pilot-lab"></a>Milník 2: nasazení pilotního testovacího prostředí
Jakmile bude síťová topologie zavedena, je možné první/pilotní prostředí vytvořit provedením následujících kroků:

1. Vytvořte počáteční prostředí DevTest Labs.
2. Určete povolené image virtuálních počítačů a velikosti pro použití s testovacím prostředím. Rozhodněte se, jestli se vlastní image dají nahrát do Azure pro použití s DevTest Labs.
3. Zabezpečený přístup k testovacímu prostředí vytvořením počátečního řízení přístupu na základě role Azure (Azure RBAC) pro testovací prostředí (vlastníky testovacího prostředí a uživatele testovacího prostředí). Doporučujeme, abyste používali synchronizované účty služby Active Directory s Azure Active Directory pro identitu s DevTest Labs.
4. Nakonfigurujte DevTest Labs, aby používaly zásady, jako jsou plány, Správa nákladů, vynucené virtuální počítače, vlastní image nebo vzorce.
5. Navažte online úložiště, jako je například Azure Repos/Git.
6. Rozhodněte se, jak použít veřejné nebo soukromé úložiště nebo kombinaci obou. Uspořádání šablon JSON pro nasazení a dlouhodobé udržování.
7. V případě potřeby vytvořte vlastní artefakty. Tento krok je volitelný. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Milník 3: dokumentace, podpora, učení a vylepšení
Počáteční pilotní týmy můžou vyžadovat hloubkovou podporu pro zahájení práce. Využijte své prostředí, abyste zajistili správnou dokumentaci a podporu pro pokračování zavedení Azure DevTest Labs.

1. Zavedení pilotních týmů do nových prostředků DevTest Labs (ukázky, dokumentace)
2. Založený na zkušenostech pilotních týmů, plánování a poskytování dokumentace podle potřeby
3. Proces formalizovat pro připojování nových týmů (vytváření a konfigurace Labs, poskytování přístupu atd.)
4. V závislosti na počátečním příjmu ověřte, že je původní prognóza adresního prostoru IP adres stále přiměřená a přesná.
5. Zajistěte, aby byly dokončeny příslušné revize a kontroly zabezpečení.

## <a name="next-steps"></a>Další kroky
Podívejte se na další článek v této sérii: zásady [správného řízení infrastruktury Azure DevTest Labs](devtest-lab-guidance-governance-resources.md) .

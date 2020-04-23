---
title: Orchestrace implementace Azure DevTest Labs
description: Tento článek obsahuje pokyny pro orchestraci implementace Azure DevTest Labs ve vaší organizaci.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: fc6cbbd0aa9cb0750e497c7cc7edbd42f21bda55
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024995"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Orchestrace implementace Azure DevTest Labs
Tento článek obsahuje doporučený přístup pro rychlé nasazení a implementaci Azure DevTest Labs. Následující obrázek zdůrazňuje celkový proces jako normativní pokyny při zachování flexibility pro podporu různých oborových požadavků a scénářů.

![Postup implementace laboratoří Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Předpoklady
Tento článek předpokládá, že máte následující položky na místě před implementací DevTest Labs pilot:

- **Předplatné Azure**: Pilotní tým má přístup k nasazení prostředků do předplatného Azure. Pokud jsou úlohy jenom vývoj a testování, doporučujeme vybrat nabídku Enterprise DevTest pro další dostupné image a nižší sazby na virtuálních počítačích s Windows.
- **Místní přístup**: V případě potřeby již byl nakonfigurován místní přístup. Místní přístup lze provést prostřednictvím připojení VPN site-to-site nebo prostřednictvím expresní trasy. Připojení přes expresní trasu může obvykle trvat mnoho týdnů, doporučuje se mít expresní trasu na místě před zahájením projektu.
- **Pilotní týmy**: Počáteční vývojový projektový tým(y), který používá DevTest Labs, byl identifikován spolu s příslušnými vývojovými nebo testovacími aktivitami a stanovil požadavky/cíle/cíle pro tyto týmy.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Milník 1: Vytvoření počáteční topologie sítě a návrhu
První oblastí zaměření při nasazování řešení Azure DevTest Labs je vytvořit plánované připojení pro virtuální počítače. Následující kroky popisují nezbytné postupy:

1. Definujte **rozsahy počátečních IP adres,** které jsou přiřazeny k předplatnému DevTest Labs v Azure. Tento krok vyžaduje prognózu očekávané využití v počtu virtuálních počítače, takže můžete poskytnout dostatečně velký blok pro budoucí rozšíření.
2. Identifikujte **metody požadovaného přístupu** do devTest Labs (například externí / interní přístup). Klíčovým bodem v tomto kroku je zjistit, zda virtuální počítače mají veřejné IP adresy (to znamená, že přístupné z Internetu přímo).
3. Identifikujte a zaváděte **metody připojení** se zbytkem cloudového prostředí Azure a místně. Pokud je povoleno vynucené směrování s express route, je pravděpodobné, že virtuální počítače potřebují odpovídající konfigurace proxy pro procházení podnikové brány firewall.
4. Pokud mají být **virtuální servery připojeny k doméně**, zjistěte, zda se připojují k cloudové doméně (například adresářové službě AAD) nebo místní doméně. Pro místní určete, která organizační jednotka (OU) v rámci aktivního adresáře, ke které se virtuální počítače připojí. Kromě toho potvrďte, že uživatelé mají přístup k připojení (nebo vytvořit účet služby, který má možnost vytvářet záznamy počítače v doméně)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Milník 2: Nasazení pilotní laboratoře
Jakmile je topologie sítě na místě, první/pilotní laboratoř může být vytvořena pomocí následujících kroků:

1. Vytvořte počáteční prostředí DevTest Labs.
2. Určete povolené image virtuálních počítače a velikosti pro použití s testovacím prostředím. Rozhodněte se, jestli se vlastní image můžou nahrát do Azure pro použití s DevTest Labs.
3. Zabezpečený přístup k testovacímu prostředí vytvořením počáteční role základní řízení přístupu (RBAC) pro testovací prostředí (vlastníci testovacího prostředí a uživatelé testovacího prostředí). Doporučujeme používat synchronizované účty active directory s Azure Active Directory pro identitu s DevTest Labs.
4. Nakonfigurujte devTest Labs tak, aby používaly zásady, jako jsou plány, správa nákladů, nárokovatelné virtuální počítače, vlastní image nebo vzorce.
5. Vytvořte online úložiště, jako je Azure Repos/Git.
6. Rozhodněte o použití veřejných nebo soukromých úložišť nebo o kombinaci obou. Uspořádejte šablony JSON pro nasazení a dlouhodobou udržitelnost.
7. V případě potřeby vytvořte vlastní artefakty. Tento krok je volitelný. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Milník 3: Dokumentace, podpora, učení a zlepšení
Počáteční pilotní týmy mohou vyžadovat hloubkovou podporu pro zahájení. Využijte jejich prostředí k zajištění správné dokumentace a podpory pro další zavádění Azure DevTest Labs.

1. Představte pilotní týmy s jejich novými zdroji DevTest Labs (ukázky, dokumentace)
2. Na základě zkušeností pilotních týmů naplánujte a dodáte dokumentaci podle potřeby
3. Formalizovat proces pro zařazování nových týmů (vytváření a konfigurace testovacích prostředí, poskytování přístupu atd.)
4. Na základě počátečního příjmu ověřte, zda je původní prognóza adresního prostoru IP stále přiměřená a přesná
5. Zajistit, aby byly dokončeny příslušné kontroly dodržování předpisů a zabezpečení

## <a name="next-steps"></a>Další kroky
Podívejte se na další článek v této sérii: [Zásady správného řízení infrastruktury Azure DevTest Labs](devtest-lab-guidance-governance-resources.md)

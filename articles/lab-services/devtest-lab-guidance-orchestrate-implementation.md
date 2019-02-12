---
title: Orchestrace provádění Azure DevTest Labs
description: Tento článek obsahuje pokyny pro implementaci orchestrací Azure DevTest Labs ve vaší organizaci.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: christianreddington
manager: femila
editor: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: chredd
ms.reviewer: anthdela,juselph
ms.openlocfilehash: 1e657260eeb36dc813f856ab172882a396324450
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004430"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>Orchestrace implementace Azure DevTest Labs
Tento článek poskytuje doporučený postup pro rychlé nasazení a implementaci služby Azure DevTest Labs. Následující obrázek zdůrazňuje celkový proces jako doporučený postup při sledování flexibilitu pro podporu různých oborovými požadavky a scénáře.

![Kroky pro implementaci Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Předpoklady
Tento článek předpokládá, že máte následující položky na místě před implementací DevTest Labs pilotní nasazení:

- **Předplatné Azure**: Pilotní tým má přístup k nasazení prostředků do předplatného Azure. Pokud úlohy jsou pouze vývoj a testování, doporučujeme vybrat nabídku Enterprise DevTest dalších dostupných imagí a nižší sazby na virtuálních počítačích Windows.
- **Přístup k místnímu**: V případě potřeby přístup k místnímu je už nakonfigurovaná. Přístup k místnímu se dá udělat přes připojení sítě Site-to-site VPN nebo přes Express Route. Připojení přes Express Route může obvykle trvat mnoho týdnů k navázání, doporučuje se mít Express Route na místě před zahájením projektu.
- **Pilotní týmy**: Týmy původního vývojového projektu, které používá DevTest Labs byla zjištěna spolu s použitelné vývojové nebo testovací aktivity a stanovit požadavky nebo cíle/cíle pro tyto týmy.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Milník 1: Vytvořte počáteční síťové topologie a návrh
První oblast fokus při nasazení řešení Azure DevTest Labs je k vytvoření plánované připojení pro virtuální počítače. Následující kroky popisují nezbytné postupy:

1. Definování **počáteční rozsahy IP adres** , které jsou přiřazeny k předplatnému Azure DevTest Labs. Tento krok vyžaduje, Prognózování stanovili v počtu virtuálních počítačů tak, že zadáte dostatečně velký blok pro budoucí rozšíření.
2. Identifikujte **metody požadovaný přístup** ve službě DevTest Lab (například externí / interní přístup). Zásadním aspektem v tomto kroku je určit, jestli virtuální počítače mají veřejné IP adresy (to znamená, přístupné z Internetu přímo).
3. Určení a vytvořit **metody připojení** s využitím rest Azure cloudové prostředí a místní. Pokud je povolené vynucené směrování s Expressroute, je pravděpodobné, že je nutné virtuální počítače proxy příslušné konfigurace, které procházejí podniková brána firewall.
4. Pokud virtuální počítače mají být **připojených k doméně**, určete, zda bylo připojit ke cloudové doméně (adresáře služby AAD pro příklad) nebo místní doméně. Pro místní určete, které organizační jednotce (OU) v rámci služby active directory, který se připojí virtuální počítače. Kromě toho ověřte, že uživatelé mají přístup k připojení (nebo vytvořit účet služby, který má schopnost vytvářet záznamy počítače v doméně)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Milník 2: Nasazení pilotního nasazení testovacího prostředí
Jakmile se topologie sítě je na místě, první/pilotního nasazení testovacího prostředí lze vytvořit pomocí následujících kroků:

1. Vytvoření prostředí DevTest Labs počáteční (podrobné pokyny najdete [tady](https://github.com/Azure/fta-devops/blob/master/devtest-labs/articles/devtest-labs-walkthrough-it.md))
2. Určení povolených imagí virtuálních počítačů a velikosti pro použití s nástrojem lab. Rozhodněte, zda vlastní Image se dají nahrát do Azure pro použití s DevTest Labs.
3. Zabezpečený přístup k testovacím prostředí tak, že vytvoříte počáteční základní přístupu Role ovládacích prvků (RBAC) pro testovací prostředí (uživatelé testovacího prostředí a vlastníků testovacího prostředí). Doporučujeme použít účty active directory synchronizované se službou Azure Active Directory pro identitu s DevTest Labs.
4. Nakonfigurujte DevTest Labs můžete pomocí zásad, jako je například plány cost management, nárokovatelných virtuálních počítačů, vlastní Image nebo vzorce.
5. Vytvořte je online úložiště, jako je například úložiště/Git v Azure.
6. Při rozhodování o použití veřejného nebo privátního úložiště nebo kombinaci obojího. Pro nasazení a dlouhodobé sustainment uspořádání šablony JSON.
7. V případě potřeby při vytváření vlastních artefaktů. Tento krok je volitelný. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Milník 3: Dokumentaci, podporu, přečtěte si a zlepšit
Počáteční pilotní týmy můžou vyžadovat podrobné podporu pro zahájení práce. Pomocí možnosti zajistit potřebná dokumentace byla k podporu a podporu na místě pro další zavádění služby Azure DevTest Labs.

1. Pilotní týmy představí jejich nové prostředky DevTest Labs (ukázky, dokumentace)
2. Podle prostředí pro pilotní týmy, plánování a podle potřeby poskytovat dokumentace
3. Formalizujte proces registrace nové týmy (vytváření a konfiguraci testovacích prostředí, poskytují přístup, atd.)
4. Podle počáteční příjmu, ověřte, že původní odhad adresní prostor IP adres je stále přiměřené a přesné
5. Zkontrolujte dokončili příslušné kontroly dodržování předpisů a zabezpečení

## <a name="next-steps"></a>Další postup
Zobrazit další článek v této sérii: [Zásady správného řízení infrastruktury Azure DevTest Labs](devtest-lab-guidance-governance-resources.md)

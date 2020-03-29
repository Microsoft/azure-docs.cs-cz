---
title: Oblíbené scénáře pro používání azure devtest labs
description: Tento článek obsahuje primární scénáře pro použití Azure DevTest Labs a dvě obecné cesty k zahájení používání služby ve vaší organizaci.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60773804"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Oblíbené scénáře pro používání azure devtest labs
V závislosti na potřebách podniku, DevTest Labs lze nakonfigurovat tak, aby splňovaly různé požadavky.  Tento článek popisuje oblíbené scénáře. Každý scénář zahrnuje výhody, které přináší použití DevTest Labs a prostředky k použití k implementaci těchto scénářů.  

- Vývojářské plochy
- Testovací prostředí
- Školení, praktická cvičení a hackathonové
- Pískoviště vyšetřování
- Testovací prostředí v učebnách

## <a name="developer-desktops"></a>Vývojářské plochy
Vývojáři mají často různé požadavky na vývojové počítače pro různé projekty. S DevTest Labs, vývojáři mohou mít přístup k virtuálním počítačům na vyžádání, které jsou nakonfigurované tak, aby vyhovovaly jejich nejběžnější scénáře. DevTest Labs poskytuje následující výhody:

- Organizace mohou poskytovat běžné vývojové počítače zajišťující konzistenci mezi týmy.
- Vývojáři mohou rychle zřídit své vývojové počítače na vyžádání nebo [nárokovat existující předem nakonfigurovaný počítač](devtest-lab-add-claimable-vm.md).
- Vývojáři mohou zřazovat prostředky samoobslužným způsobem bez nutnosti oprávnění na úrovni předplatného.
- IT nebo správci mohou [předem definovat síťovou topologii](devtest-lab-configure-vnet.md) a vývojáři ji mohou přímo používat jednoduchým a intuitivním způsobem, aniž by vyžadovali zvláštní přístup.
- Vývojáři mohou snadno [přizpůsobit](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) své vývojové stroje podle potřeby.
- Správci mohou řídit náklady tím, že zajistí, aby:
    - Vývojáři [nemůžou získat víc virtuálních her,](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) než potřebují pro vývoj.
    - [Virtuální virtuální mase se vypnou,](devtest-lab-set-lab-policy.md#set-auto-shutdown) když se nepoužívají.
    - Povoluje pouze [podmnožinu velikostí instancí virtuálních počítače](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) pro konkrétní testovací prostředí.
    - [Správa cílů nákladů a oznámení](devtest-lab-configure-cost-management.md) pro každé testovací prostředí.

Další informace najdete [v tématu Použití azure devtest labs pro vývojáře](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Testovací prostředí
Vytváření a správa testovacích prostředí v rámci podniku může vyžadovat značné úsilí. S DevTest Labs, testovací prostředí lze snadno vytvořit, aktualizovat nebo duplikovat. Umožňuje týmům přístup k plně nakonfigurovanému prostředí, když je to potřeba. V tomto scénáři DevTest Labs poskytuje následující výhody:

- Organizace mohou poskytovat společné testovací prostředí zajišťující konzistenci mezi týmy.
- Testeři mohou otestovat nejnovější verzi své aplikace rychlým zřizováním prostředí Windows a Linux pomocí opakovaně použitelných šablon.
- Správci můžou připojit testovací prostředí k Azure DevOps a povolit scénáře DevOps.
- Vlastníci testovacích prostředí mohou řídit náklady tím, že zajistí, aby:
    - [Virtuální virtuální ho dýové v prostředí se vypnou,](devtest-lab-set-lab-policy.md#set-auto-shutdown) když se nepoužívají.
    - Povoluje pouze [podmnožinu velikostí instancí virtuálních počítače pro](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) konkrétní testovací prostředí.
    - [Správa cílů nákladů a oznámení](devtest-lab-configure-cost-management.md) pro každé testovací prostředí.

Další informace najdete [v tématu Použití Azure DevTest Labs pro testovací prostředí virtuálních počítačů a PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Pískoviště vyšetřování
Vývojáři často zkoumají různé technologie nebo návrh infrastruktury. Ve výchozím nastavení jsou všechna prostředí vytvořená pomocí devTest Labs vytvořena ve vlastní skupině prostředků. Uživatel DevTest Labs získá přístup pro čtení pouze pro čtení k těmto prostředkům. Pro vývojáře, kteří potřebují větší kontrolu, však nastavení pro celé testovací prostředí lze aktualizovat, aby [práva přispěvatele](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) původního uživatele DevTest Labs pro každé prostředí, které vytvoří.  S DevTest Labs, vývojáři mohou mít oprávnění přispěvatele automaticky prostředí, které vytvářejí v testovacím prostředí.  Tento scénář umožňuje vývojářům přidávat nebo měnit prostředky Azure podle potřeby pro jejich vývoj nebo testovací prostředí. [Stránka Náklady podle prostředků](devtest-lab-configure-cost-management.md#view-cost-by-resource) umožňuje vlastníkům testovacího prostředí sledovat náklady na každé prostředí používané pro šetření.

Další informace naleznete v tématu [Nastavení přístupových práv ke skupině prostředků prostředí](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Školení, praktická cvičení a hackathonové 
Testovací prostředí v laboratořích Azure DevTest Labs funguje jako skvělý kontejner pro přechodné aktivity, jako jsou workshopy, praktická cvičení, školení nebo hackathony.  Služba umožňuje vytvořit testovací prostředí, kde můžete poskytnout vlastní šablony, které každý stážista můžete použít k vytvoření identické a izolované prostředí pro školení. V tomto scénáři DevTest Labs poskytuje následující výhody:

- [Zásady](devtest-lab-set-lab-policy.md) zajišťují, že stážisté získají pouze počet prostředků, jako jsou virtuální počítače, které potřebují.
- Předem nakonfigurované a vytvořené počítače jsou [nárokovány](devtest-lab-add-claimable-vm.md) s jedinou akcí od stážisty.
- Laboratoře jsou sdíleny s stážisty přístupem k [adrese URL pro testovací prostředí](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Data vypršení platnosti](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) na virtuálních počítačích zajišťují, že počítače jsou odstraněny poté, co již nejsou potřeba.
- Je snadné [odstranit testovací prostředí](devtest-lab-delete-lab-vm.md#delete-a-lab) a všechny související [prostředky,](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) když školení je u konce.

Další informace najdete [v tématu použití Azure DevTest Labs pro školení](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Důkaz koncepce vs. škálované nasazení
Jakmile se rozhodnete prozkoumat DevTest Labs, existují dvě obecné cesty vpřed: Proof of Concept vs scaled nasazení.  

**Škálované nasazení** se skládá z týdnů nebo měsíců revize a plánování s úmyslem nasadit DevTest Labs do celého podniku, který má stovky nebo tisíce vývojářů.

Důkaz **nasazení konceptu** se zaměřuje na soustředěné úsilí jednoho týmu o vytvoření organizační hodnoty. I když to může být lákavé myslet na škálované nasazení, přístup má tendenci selhat častěji než možnost proof koncepce. Proto doporučujeme začít v malém, učit se od prvního týmu, opakovat stejný přístup se dvěma až třemi dalšími týmy a pak naplánovat nasazení s škálovaným i na základě získaných znalostí. Pro úspěšný důkaz konceptu doporučujeme vybrat jeden nebo dva týmy a identifikovat jejich scénáře (vývoj prostředí vs testovací prostředí), dokumentovat jejich aktuální případy použití a nasadit DevTest Labs.

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články:

- [Koncepce DevTest Labs](devtest-lab-concepts.md)
- [Nejčastější dotazy k devTest Labs](devtest-lab-faq.md)


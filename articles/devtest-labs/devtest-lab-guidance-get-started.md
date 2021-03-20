---
title: Populární scénáře použití Azure DevTest Labs
description: Tento článek poskytuje primární scénáře použití Azure DevTest Labs a dvou obecných cest pro zahájení používání služby ve vaší organizaci.
ms.topic: article
ms.date: 06/20/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 1adb9b009a250942bab7e9b0e3b4b3e52840341c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481591"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Populární scénáře použití Azure DevTest Labs
V závislosti na potřebách podniku je DevTest Labs možné nakonfigurovat tak, aby splňovaly různé požadavky.  Tento článek pojednává o oblíbených scénářích. Jednotlivé scénáře zahrnují výhody, které přináší DevTest Labs a prostředky, které se použijí k implementaci těchto scénářů.  

- Plochy pro vývojáře
- Testovací prostředí
- Školicí cvičení, praktická cvičení a hackathony
- Šetření v izolovaném prostoru
- Testovací prostředí v učebnách

## <a name="developer-desktops"></a>Plochy pro vývojáře
Vývojáři mají často různé požadavky na vývojové počítače pro různé projekty. Pomocí DevTest Labs můžou vývojáři získat přístup k virtuálním počítačům na vyžádání, které jsou nakonfigurované tak, aby vyhovovaly jejich nejběžnějším scénářům. DevTest Labs přináší následující výhody:

- Organizace můžou poskytovat běžné vývojové počítače zajišťující konzistenci napříč týmy.
- Vývojáři můžou rychle zřídit své vývojové počítače na vyžádání nebo [uplatnit existující předem nakonfigurovaný počítač](devtest-lab-add-claimable-vm.md).
- Vývojáři můžou prostřednictvím samoobslužné služby zřizovat prostředky bez oprávnění na úrovni předplatného.
- IT nebo Správci mohou [předem definovat topologii sítě](devtest-lab-configure-vnet.md) a vývojáři je můžou snadno používat jednoduchým a intuitivním způsobem, aniž by museli vyžadovat zvláštní přístup.
- Vývojáři mohou podle potřeby snadno [přizpůsobit](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) své vývojové počítače.
- Správci mohou řídit náklady tím, že zajistí, že:
    - Vývojáři [nemůžou získat víc virtuálních počítačů](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) , než potřebují pro vývoj.
    - [Virtuální počítače jsou vypnuté](devtest-lab-set-lab-policy.md#set-auto-shutdown) , když se nepoužívají.
    - Povoluje se jenom [podmnožina velikostí instancí virtuálních počítačů](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) pro konkrétní cvičení.
    - [Správa cílových nákladů a oznámení](devtest-lab-configure-cost-management.md) pro každé testovací prostředí.

Další informace najdete v tématu [použití Azure DevTest Labs pro vývojáře](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Testovací prostředí
Vytváření a Správa testovacích prostředí v podniku může vyžadovat značné úsilí. Pomocí DevTest Labs lze testovací prostředí snadno vytvořit, aktualizovat nebo duplikovat. Umožňuje týmům získat přístup k plně nakonfigurovanému prostředí, když je potřeba. V tomto scénáři přináší DevTest Labs následující výhody:

- Organizace můžou zajistit společné testovací prostředí, které zajistí konzistenci napříč týmy.
- Testery mohou otestovat nejnovější verzi své aplikace rychlým zřizováním prostředí Windows a Linux pomocí opakovaně použitelných šablon.
- Správci můžou ke službě Azure DevOps připojit testovací prostředí a povolit tak scénáře DevOps.
- Vlastníci testovacího prostředí mohou řídit náklady tím, že zajišťují, že:
    - [Virtuální počítače v prostředí jsou vypnuté](devtest-lab-set-lab-policy.md#set-auto-shutdown) , když se nepoužívají.
    - Povoluje se jenom [podmnožina velikostí instancí virtuálních počítačů pro](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) konkrétní cvičení.
    - [Správa cílových nákladů a oznámení](devtest-lab-configure-cost-management.md) pro každé testovací prostředí.

Další informace najdete v tématu [použití Azure DevTest Labs pro testovací prostředí virtuálních počítačů a PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Šetření v izolovaném prostoru
Vývojáři často prošetří různé technologie nebo návrh infrastruktury. Ve výchozím nastavení jsou všechna prostředí vytvořená pomocí DevTest Labs vytvořená ve vlastní skupině prostředků. Uživatel DevTest Labs získá jenom přístup pro čtení těchto prostředků. Pro vývojáře, kteří potřebují větší kontrolu, je však možné aktualizovat nastavení v rámci testovacího prostředí tak, aby poskytovalo [práva přispěvatele](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) k původnímu uživateli DevTest Labs pro každé prostředí, které vytvořil.  S DevTest Labs můžou vývojáři automaticky udělit oprávnění přispěvatelům k prostředím, která vytvoří v testovacím prostředí.  Tento scénář umožňuje vývojářům přidávat a měnit prostředky Azure tak, jak potřebují pro vývojová nebo testovací prostředí. Stránka [náklady podle prostředků](devtest-lab-configure-cost-management.md#view-cost-by-resource) umožňuje vlastníkům testovacího prostředí sledovat náklady na každé prostředí používané pro vyšetřování.

Další informace najdete v tématu [Nastavení přístupových práv ke skupině prostředků prostředí](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Školení, praktická cvičení a hackathony 
Testovací prostředí v Azure DevTest Labs funguje jako skvělý kontejner pro přechodné aktivity, jako jsou semináře, praktická cvičení, školení nebo hackathony.  Služba umožňuje vytvořit testovací prostředí, ve kterém můžete zadat vlastní šablony, které může každý praktikant použít k vytvoření identického a izolovaného prostředí pro školení. V tomto scénáři přináší DevTest Labs následující výhody:

- [Zásady](devtest-lab-set-lab-policy.md) zajišťují, aby praktikanti získali jenom počet prostředků, jako jsou třeba virtuální počítače, které potřebují.
- Předem nakonfigurované a vytvořené počítače jsou v programu praktikanti [vyžádány](devtest-lab-add-claimable-vm.md) jedinou akcí.
- Laboratoře jsou sdíleny s praktikanty pomocí přístupu k [adrese URL testovacího prostředí](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Data vypršení platnosti](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) virtuálních počítačů zajišťují, že se počítače po jejich odstranění nebudou potřebovat.
- Můžete snadno [Odstranit testovací prostředí](devtest-lab-delete-lab-vm.md#delete-a-lab) a všechny [související prostředky](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) , když je školení zapnuté.

Další informace najdete v tématu [použití Azure DevTest Labs pro školení](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Ověření konceptu vs. navýšení kapacity nasazení
Až se rozhodnete prozkoumat DevTest Labs, jsou k dispozici dvě obecné cesty: Ověření konceptu a nasazení se škálováním.  

**Škálované nasazení** se skládá z týdnů za měsíc recenzování a plánování s úmyslem nasadit DevTest Labs do celého podniku, který má stovky nebo tisíce vývojářů.

**Zkušební nasazení konceptu** se zaměřuje na soustředěné úsilí z jednoho týmu, aby se navázala hodnota organizace. I když se to může lépe rozmyslet na nasazení s více instancemi, přístup je často častěji než možnost zkoušení konceptu. Proto doporučujeme začít s malým, učit od prvního týmu, opakovat stejný přístup ke dvěma až třem dalším týmům a pak naplánovat nasazení s upraveným rozsahem na základě získaných znalostí. Pro úspěšné ověření konceptu doporučujeme, abyste si vybrali jeden nebo dva týmy a identifikovali své scénáře (vývojové prostředí vs testovací prostředí), dokumentují své aktuální případy použití a nasadili DevTest Labs.

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články:

- [Koncepce DevTest Labs](devtest-lab-concepts.md)
- [Nejčastější dotazy k DevTest Labs](devtest-lab-faq.md)


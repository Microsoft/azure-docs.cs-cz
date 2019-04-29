---
title: Oblíbené scénáře pro pomocí Azure DevTest Labs
description: Tento článek obsahuje základní scénáře pro pomocí Azure DevTest Labs a dvě obecné cesty Pokud chcete začít používat službu ve vaší organizaci.
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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773804"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Oblíbené scénáře pro pomocí Azure DevTest Labs
V závislosti na potřebách organizace je možné nakonfigurovat DevTest Labs pro splnění různých požadavků.  Tento článek popisuje Oblíbené scénáře. Každý scénář popisuje výhody převést do režimu s použitím DevTest Labs a prostředky používat k implementaci těchto scénářů.  

- Stolní počítače pro vývojáře
- Testovací prostředí
- Školení, praktických cvičení a hackathony
- V izolovaném prostoru vyšetřování
- Testovací prostředí v učebnách

## <a name="developer-desktops"></a>Stolní počítače pro vývojáře
Vývojáři často mají různé požadavky na počítače pro vývoj pro různé projekty. S DevTest Labs vývojáři mají přístup k virtuálním počítačům na vyžádání, které jsou nakonfigurovány tak, aby vyhovovaly jejich nejběžnějších scénářů. DevTest Labs poskytuje následující výhody:

- Běžné vývojové počítače zajistit konzistenci napříč týmy můžou organizace poskytovat.
- Vývojářům můžete rychle zřídit své vývojové počítače na vyžádání nebo [deklarace identity existujícího předem nakonfigurované počítače](devtest-lab-add-claimable-vm.md).
- Vývojáři můžou zřizovat prostředky způsobem samoobslužné služby bez nutnosti oprávnění na úrovni předplatného.
- IT nebo správci mohou [předem definovat topologii sítí](devtest-lab-configure-vnet.md) a vývojáři přímo ji můžete využít jednoduché a intuitivní způsob bez nutnosti jakékoli zvláštní přístup.
- Vývojáři mohou snadno [přizpůsobit](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) jejich vývojové počítače podle potřeby.
- Správci můžou řídit náklady tím, že zajišťuje, že:
    - Vývojáři [nelze získat další virtuální počítače](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) než potřebují pro vývoj
    - [Virtuální počítače jsou vypnout](devtest-lab-set-lab-policy.md#set-auto-shutdown) kdy se nepoužívá
    - Pouze [podmnožinu velikostí instancí virtuálních počítačů umožňuje](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) pro konkrétní testovací prostředí
    - [Správa nákladů cíle a oznámení](devtest-lab-configure-cost-management.md) pro každý testovací prostředí.

Další informace najdete v článku [pomocí Azure DevTest Labs pro vývojáře](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Testovací prostředí
Vytváření a správa testovacích prostředí v podniku může vyžadovat značné úsilí. S DevTest Labs testovací prostředí může být snadno vytvořil, aktualizoval nebo duplicitní. To umožňuje týmům přístup k plně nakonfigurovaného prostředí, když je to potřeba. V tomto scénáři DevTest Labs poskytuje následující výhody:

- Organizace může poskytovat běžné testovacích prostředích současném zajištění konzistence mezi týmy.
- Testeři otestovat rychlým zřízením prostředí Windows a Linuxem pomocí opakovaně použitelných šablon nejnovější verzi své aplikace.
- Správce může připojit k Azure DevOps, aby se povolily scénáře DevOps testovacího prostředí
- Vlastníků testovacího prostředí můžete řídit náklady tím, že zajišťuje, že:
    - [Vypnout virtuální počítače v prostředí](devtest-lab-set-lab-policy.md#set-auto-shutdown) kdy se nepoužívá
    - Pouze [podmnožinu velikostí instancí virtuálních počítačů pro povolení](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) specifické testovací prostředí
    - [Správa nákladů cíle a oznámení](devtest-lab-configure-cost-management.md) pro každý testovací prostředí.

Další informace najdete v tématu [testovacích prostředí pomocí Azure DevTest Labs pro virtuální počítač a PaaS](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>V izolovaném prostoru vyšetřování
Vývojáři často prozkoumat různé technologie nebo návrh infrastruktury. Ve výchozím nastavení jsou všechna prostředí vytvořená s DevTest Labs vytvoří ve vlastní skupině prostředků. DevTest Labs uživatel získá přístup jenom pro čtení k těmto prostředkům. Ale pro vývojáře, kteří potřebují mít větší kontrolu, nastavení testovacího prostředí můžete aktualizovat tak, aby [oprávněním přispěvatele](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) původní uživateli DevTest Labs pro každé prostředí vytvářejí.  S DevTest Labs vývojáři může dostat oprávnění Přispěvatel automaticky do prostředí, které vytvoří v testovacím prostředí.  Tento scénář umožňuje vývojářům přidat nebo změnit prostředky Azure podle potřeby pro jejich vývojová nebo testovací prostředí. [Náklady podle prostředků](devtest-lab-configure-cost-management.md#view-cost-by-resource) stránka umožňuje vlastníků testovacího prostředí můžete sledovat náklady na každé prostředí pro šetření.

Další informace najdete v tématu [nastavení přístupových práv na skupinu prostředků prostředí](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Školení, praktických cvičení a hackathony 
Testovací prostředí v Azure DevTest Labs funguje jako kontejner skvělé pro přechodný činnosti, jako jsou možnostech školení, praktickým cvičením, školení nebo hackathony.  Služba umožňuje vytvoření testovacího prostředí, ve kterém můžete zadat vlastní šablony, které každý účastník výcviku můžete použít k vytvoření shodné a jsou izolované prostředí pro školení. V tomto scénáři DevTest Labs poskytuje následující výhody:

- [Zásady](devtest-lab-set-lab-policy.md) zajistit stážisté získat pouze počet prostředků, jako jsou virtuální počítače, které potřebují.
- Jsou předem nakonfigurovaná a vytvořená počítače [nárokován](devtest-lab-add-claimable-vm.md) pomocí jedné akce z praktikanta.
- Testovací prostředí se sdílí s stážisté přístup k [adresu URL pro testovací prostředí](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Data vypršení platnosti](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) u virtuálních počítačů, ujistěte se, že počítače se odstraní po už nejsou potřeba.
- Je snadné [odstranění testovacího prostředí](devtest-lab-delete-lab-vm.md#delete-a-lab) a všechny [související prostředky](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) když je na školení.

Další informace najdete v tématu [pomocí Azure DevTest Labs k trénování](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Testování konceptu a měřítkem nasazení
Jakmile se rozhodnete prozkoumejte DevTest Labs, existují dvě obecné cesty vpřed: Testování konceptu a horizontální nasazení.  

A **škálovat nasazení** se skládá z týdnů a měsíců revizí a plánování se záměrem nasazení DevTest Labs pro celý podnik, který má stovky nebo i tisíce vývojářů.

A **testování konceptu služby** nasazení se zaměřuje na vměstnány úsilí jeden tým vytvořit organizační hodnotu. Mohou být lákavé představit škálován nasazení, přístup se obvykle častěji než testování konceptu možnost selže. Proto doporučujeme, že můžete začít v malém, Učte se od první týmu, opakujte stejný přístup s dvě až tři další týmy a naplánujte škálován nasazení založené na znalostech získaných. Pro úspěšné testování konceptu doporučujeme, musíte vybrat jednu nebo dvě týmy a určit jejich scénáře (vývoj prostředí vs testovací prostředí), zdokumentujte jejich aktuální případy použití a nasadit DevTest Labs.

## <a name="next-steps"></a>Další postup
V následujících článcích:

- [Koncepce DevTest Labs](devtest-lab-concepts.md)
- [Nejčastější dotazy k DevTest Labs](devtest-lab-faq.md)


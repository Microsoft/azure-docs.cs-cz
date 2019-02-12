---
title: Začínáme s využitím Azure DevTest Labs
description: Tento článek obsahuje primární scnearios pro pomocí Azure DevTest Labs a dvě obecné cesty Pokud chcete začít používat službu ve vaší organizaci.
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
ms.openlocfilehash: dfb73778a9d30ba50e69bff6414381235ad8b1d1
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005229"
---
# <a name="get-started-with-using-azure-devtest-labs"></a>Začínáme s využitím Azure DevTest Labs
Jakmile se rozhodnete prozkoumejte DevTest Labs, existují dvě obecné cesty vpřed – testování konceptu vs horizontální nasazení. 

A **škálovat nasazení** se skládá z týdnů a měsíců revizí a plánování se záměrem nasazení DevTest Labs pro celý podnik, který má stovky nebo i tisíce vývojářů. 

A **testování konceptu nasazení** se zaměřuje na vměstnány úsilí jeden tým vytvořit organizační hodnotu. Mohou být lákavé představit škálován nasazení, přístup se obvykle častěji než testování konceptu možnost selže. Proto doporučujeme, že můžete začít v malém, Učte se od první týmu, opakujte stejný přístup s dvě až tři další týmy a naplánujte škálován nasazení založené na znalostech získaných. Pro úspěšné testování konceptu doporučujeme, musíte vybrat jednu nebo dvě týmy a určit jejich scénáře (vývoj prostředí vs testovací prostředí), zdokumentujte jejich aktuální případy použití a nasadit DevTest Labs. 

## <a name="scenarios"></a>Scénáře
Existují tři základní scénáře pro implementaci DevTest Labs: 

- Stolní počítače pro vývojáře
- Testovací prostředí
- Praktická cvičení, školení nebo hackathon

## <a name="developer-desktops"></a>Stolní počítače pro vývojáře
Vývojáři často mají různé požadavky na počítače pro vývoj pro různé projekty. S DevTest Labs mohou vývojáři mít přístup k virtuálními počítači na vyžádání, které jsou předem nakonfigurovány tak, aby vyhovovaly jejich nejběžnějších scénářů. DevTest Labs poskytuje následující výhody:

- Organizace můžou poskytovat běžných vývojových a testovacích prostředích zajištění konzistence napříč týmy
- Vývojářům můžete rychle zřídit své vývojové počítače na vyžádání
- Vývojáři můžou zřizovat prostředky způsobem samoobslužné služby bez nutnosti oprávnění na úrovni předplatného
- IT správci můžou definovat předem síťové topologie nebo vývojáři možné přímo využití jednoduché a intuitivní způsob bez nutnosti jakékoli speciální přístup
- Vývojářům můžete snadno přizpůsobit své vývojové počítače podle potřeby
- Správci můžou řídit náklady tím, že zajišťuje, že:
    - Vývojáři nelze získat další virtuální počítače, než které potřebují pro vývoj
    - Virtuální počítače jsou vypnout kdy se nepoužívá
    - Povolení pouze podmnožinu velikostí instancí virtuálních počítačů pro konkrétní úlohy

## <a name="test-environments"></a>Testovací prostředí
Vytváření a správa testovacích prostředí v podniku může vyžadovat značné úsilí. S DevTest Labs testovací prostředí můžou snadno vytvořit, aktualizovat nebo duplicitní, který umožňuje týmům přístup k plně nakonfigurovaného prostředí, když je to potřeba. V tomto scénáři DevTest Labs poskytuje následující výhody:

- Testeři otestovat rychlým zřízením prostředí Windows a Linuxem pomocí opakovaně použitelných šablon a artefaktů nejnovější verzi své aplikace.
- Testerů můžete vertikálně navýšit kapacitu svých zátěžového testování zřízením několika testovacích agentů
- Správce může připojit k Azure DevOps, aby se povolily scénáře DevOps testovacího prostředí
- Správci můžou řídit náklady tím, že zajišťuje, že:
    - Testery nelze získat další virtuální počítače, než budou potřebovat
    - Virtuální počítače jsou vypnout kdy se nepoužívá
    - Povolení pouze podmnožinu velikostí instancí virtuálních počítačů pro konkrétní úlohy

## <a name="labs-workshops-trainings-and-hackathons"></a>Praktická cvičení, semináře, školení a hackathony  
Testovací prostředí v Azure DevTest Labs funguje jako kontejner skvělé pro přechodný činnosti, jako jsou semináře, praktických cvičení, školení nebo hackathony. Služba umožňuje vytvoření testovacího prostředí, ve kterém můžete zadat vlastní šablony, které každý účastník výcviku můžete použít k vytvoření shodné a jsou izolované prostředí pro školení. Můžete využít zásady pro zajištění, že školení prostředí jsou k dispozici pro každý účastník výcviku pouze v případě potřeby a obsahovat dostatek prostředků – například virtual machines – požadováno pro vzdělávání. Nakonec můžete jednoduše sdílet testovací prostředí s prováděli úkoly, které mají přístup k jedním kliknutím. Po třídy uzavřel, je snadné odstranit testovací prostředí a všechny související prostředky.


## <a name="next-steps"></a>Další postup
Zobrazit další článek v této sérii: [Vertikální navýšení kapacity nasazení DevTest Labs](devtest-lab-guidance-scale.md)

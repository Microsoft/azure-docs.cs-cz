---
title: O službě Azure Lab Services | Microsoft Docs
description: Zjistěte, jak lze pomocí služby Lab Services zjednodušit vytváření, správu a zabezpečení testovacího prostředí s virtuálními počítači, které můžou využívat vývojáři, testeři, pedagogové, studenti a další uživatelé.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/19/2018
ms.author: spelluru
ms.openlocfilehash: b42183369b9ad88c77a05a91fdba8fe0efca2a8c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="an-introduction-to-azure-lab-services-formerly-azure-devtest-labs"></a>Úvod ke službě Azure Lab Services (dříve Azure DevTest Labs)
Služba Azure Lab Services umožňuje rychle nastavit cloudové prostředí pro váš tým (například vývojové prostředí, testovací prostředí, testovací prostředí v učebně). Vlastník testovacího prostředí vytvoří testovací prostředí, zřídí virtuální počítače s Windows nebo Linuxem, nainstaluje nezbytný software a nástroje a zpřístupní je uživatelům testovacího prostředí. Uživatelé testovacího prostředí se připojí k virtuálním počítačům (VM) v testovacím prostředí a budou je používat ke každodenní práci, na krátkodobé projekty nebo aktivity ve vyučování. Jakmile uživatelé začnou využívat prostředky v testovacím prostředí, správce testovacího prostředí může analyzovat náklady a využití ve všech spravovaných testovacích prostředích a nastavením globálních zásad optimalizovat náklady organizace i jednotlivých týmů.

> [!IMPORTANT]
> Rozšiřujeme Azure DevTest Labs o nové typy testovacích prostředí (ve službě Azure Lab Services). 
> 
> Ve službě Azure Lab Services můžete vytvářet spravovaná testovací prostředí, jako je například testovací prostředí v učebně. Služba zajišťuje veškerou správu infrastruktury pro spravovaná testovací prostředí, od poskytování potřebného výkonu virtuálních počítačů přes zpracovávání chyb až po škálování infrastruktury. Spravovaná testovací prostředí jsou v současné době ve verzi Preview. Po skončení verze Preview se nové typy testovacích prostředí i existující DevTest Labs sjednotí pod novou službou s názvem Azure Lab Services, kde se budou všechny typy testovacích prostředí dál rozvíjet. 

## <a name="key-capabilities"></a>Klíčové funkce
Služba Azure Lab Services podporuje následující funkce: 

- **Rychlé a flexibilní nastavení testovacích prostředí**. V Azure Lab Services si můžou vlastníci testovacích prostředí rychle nastavit testovací prostředí podle svých potřeb. Můžete buď nechat službu, aby zajišťovala veškerou správu infrastruktury Azure pro spravovaná testovací prostředí za vás, nebo si jako vlastníci testovacích prostředí jejich infrastrukturu spravovat a přizpůsobovat sami prostřednictvím předplatného. Služba má integrované možnosti škálování a nastavení odolnosti infrastruktury pro testovací prostředí, která pro vás spravuje. 
- **Zjednodušené ovládání pro uživatele testovacích prostředí**. Ke spravovanému testovacímu prostředí, jako je testovací prostředí v učebně, se můžou jeho uživatelé zaregistrovat pomocí registračního kódu, aby k tomuto testovacímu prostředí a jeho prostředkům mohli kdykoli přistupovat. Ve vlastním testovacím prostředí v DevTest Labs může vlastník testovacího prostředí udělit oprávnění jeho uživatelům k vytváření virtuálních počítačů a přístupu k nim, správě a opakovanému používání datových disků a nastavení opakovaně použitelných tajných klíčů.  
- **Optimalizace nákladů a analýza**. Vlastník testovacího prostředí může nastavit plány automatického vypínání a spouštění virtuálních počítačů. Vlastník testovacího prostředí může vytvořit plán s časovými úseky, kdy mají být virtuální počítače v testovacím prostředí dostupné pro uživatele, optimalizovat náklady nasazením zásad pro uživatele nebo celé testovací prostředí a analyzovat trendy využití a aktivity v testovacím prostředí. Spravovaná testovací prostředí, jako je testovací prostředí v učebně, nabízejí momentálně méně možností pro optimalizaci nákladů a analýzu. 
- **Integrované zabezpečení**. Vlastník testovacího prostředí může pro testovací prostředí nastavit privátní virtuální sítě a podsítě a povolit sdílené veřejné IP adresy. Uživatelé testovacích prostředí můžou pomocí virtuálních sítí nakonfigurovaných s využitím ExpressRoute nebo sítě VPN typu Site-to-Site zajistit zabezpečený přístup k prostředkům. (Aktuálně k dispozici pouze v DevTest Labs)
- **Integrace v pracovních postupech a nástrojích**. Azure Lab Services umožňuje integrovat testovací prostředí do systémů pro správu a na web vaší organizace. Nástroje pro průběžnou integraci a průběžné nasazování (CI/CD) umožňují automaticky zřizovat prostředí. (Aktuálně k dispozici pouze v DevTest Labs)

## <a name="scenarios"></a>Scénáře
Tady je několik scénářů, které Azure Lab Services podporuje: 

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>Nastavení cloudové počítačové učebny s proměnlivou velikostí  

- Vytvořte spravované testovací prostředí v učebně. Stačí, když zadáte své požadavky, a služba už se postará o vytvoření a správu infrastruktury testovacího prostředí. Vy se tak nemusíte zabývat technickými detaily testovacího prostředí a můžete se soustředit na učení a na své studenty. 
- Poskytněte studentům testovací prostředí s virtuálními počítači, které jsou nakonfigurované přesně tak, jak je pro vyučování potřeba. Můžete pro studenty omezit používání virtuálních počítačů k práci na školních projektech na určitý počet hodin.  
- Přesuňte fyzickou počítačovou učebnu ve vaší škole do cloudu. Automaticky škálujte počet virtuálních počítačů tak, aby nedošlo k překročení limitů využití a nákladů, které jste pro testovací prostředí nastavili. 
- Jakmile testovací prostředí přestanete potřebovat, můžete ho jedním kliknutím odstranit. 

### <a name="use-devtest-labs-for-development-environments"></a>Použití DevTest Labs pro vývojová prostředí 
Azure DevTest Labs lze použít k implementaci mnoha klíčových scénářů. Jedním z nejzákladnějších je hostování počítačů pro vývojáře. V tomto scénáři poskytuje služba DevTest Labs následující výhody: 

- Vývojáři můžou na vyžádání rychle zřizovat počítače pro vývoj.
- Je možné zřizovat prostředí s Windows nebo Linuxem pomocí opakovaně použitelných šablon a artefaktů.
- Vývojáři si můžou počítače pro vývoj kdykoli snadno přizpůsobit.
- Správci mají pod kontrolou náklady – můžou zajistit, aby měli vývojáři k dispozici jen tolik virtuálních počítačů, kolik pro vývoj potřebují, a aby se virtuální počítače vypínaly, když nejsou potřeba. 

Další informace najdete v tématu [Použití DevTest Labs pro vývoj](devtest-lab-developer-lab.md). 

### <a name="use-devtest-labs-for-test-environments"></a>Použití DevTest Labs pro testovací účely
Azure DevTest Labs můžete použít k implementaci mnoha klíčových scénářů. Jedním z nejzákladnějších je hostování počítačů pro testery. V tomto scénáři poskytuje služba DevTest Labs následující výhody:

- Rychlé zřizování prostředí Windows nebo Linuxu pomocí opakovaně použitelných šablon a artefaktů umožňuje testerům testovat nejnovější verze jejich aplikací.
- Testeři můžou škálovat zátěžové testy zřízením více testovacích agentů.
- Správci mají pod kontrolou náklady – můžou zajistit, aby měli testeři k dispozici jen tolik virtuálních počítačů, kolik k testování potřebují, a aby se virtuální počítače vypínaly, když nejsou potřeba.

Další informace najdete v tématu [Použití DevTest Labs pro testování](devtest-lab-test-env.md).

## <a name="user-profiles"></a>Profily uživatelů
Tento článek popisuje různé uživatelské profily v Azure Lab Services. 

### <a name="lab-account-owner"></a>Vlastník účtu testovacího prostředí
Správce cloudových prostředků organizace, který je vlastníkem předplatného Azure, obvykle plní roli vlastníka účtu testovacího prostředí a má následující úkoly:   

- Nastavit účet testovacího prostředí pro organizaci
- Spravovat a konfigurovat zásady pro všechna testovací prostředí
- Udělovat uživatelům v organizaci oprávnění vytvářet v příslušném účtu testovací prostředí

### <a name="lab-creator"></a>Tvůrce testovacího prostředí 
Uživatel v roli správce, například vedoucí oddělení vývoje, učitel, hostitel hackathonu nebo online školitel, zpravidla vytvoří testovací prostředí pod příslušným účtem. Tvůrce testovacího prostředí má následující úkoly: 

- Vytvořit testovací prostředí
- Vytvořit v testovacím prostředí virtuální počítače 
- Nainstalovat na virtuálních počítačích příslušný software
- Určit, kdo má mít k testovacímu prostředí přístup
- Poskytnout uživatelům odkaz na testovací prostředí

### <a name="lab-user"></a>Uživatel testovacího prostředí
Uživatel testovacího prostředí má následující úkoly:

- Zaregistrovat se pomocí registračního odkazu, který obdrží od tvůrce testovacího prostředí 
- Připojit se k virtuálnímu počítači v testovacím prostředí a používat ho k vývoji, testování nebo práci na školních projektech 

## <a name="next-steps"></a>Další kroky
Začínáme s nastavením testovacího prostředí pomocí Azure Lab Services:

- [Nastavení testovacího prostředí v učebně](tutorial-setup-classroom-lab.md)
- [Nastavení vlastního testovacího prostředí](tutorial-create-custom-lab.md)

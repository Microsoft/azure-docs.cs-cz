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
ms.date: 07/13/2018
ms.author: spelluru
ms.openlocfilehash: a4ca5cba924a3269f279469f26e68acdb0ad0659
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59257616"
---
# <a name="an-introduction-to-azure-lab-services"></a>Seznámení s Azure Lab Services
Služba Azure Lab Services umožňuje rychle nastavit cloudové prostředí pro váš tým (například vývojové prostředí, testovací prostředí, testovací prostředí v učebně). Vlastník testovacího prostředí vytvoří testovací prostředí, zřídí virtuální počítače s Windows nebo Linuxem, nainstaluje nezbytný software a nástroje a zpřístupní je uživatelům testovacího prostředí. Uživatelé testovacího prostředí se připojí k virtuálním počítačům (VM) v testovacím prostředí a budou je používat ke každodenní práci, na krátkodobé projekty nebo aktivity ve vyučování. Jakmile uživatelé začnou využívat prostředky v testovacím prostředí, správce testovacího prostředí může analyzovat náklady a využití ve všech spravovaných testovacích prostředích a nastavením globálních zásad optimalizovat náklady organizace i jednotlivých týmů.

> [!IMPORTANT]
> Rozšiřujeme **Azure DevTest Labs** o nové typy testovacích prostředí (ve službě Azure Lab Services).
>  
> Azure Lab Services vám umožní vytvářet testovací prostředí spravované typy, jako jsou testovací prostředí v učebnách. Samotné služby zpracovává všechny správy infrastruktury pro typ spravovaného testovacího prostředí z opravíte vytvořením virtuálních počítačů pro zpracování chyb a škálování infrastruktury. Prozatím [DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) a [Azure Lab Services](https://azure.microsoft.com/services/lab-services/) bude dál samostatných služeb na webu Azure Portal. 

## <a name="key-capabilities"></a>Klíčové funkce

Služba Azure Lab Services podporuje následující funkce:

- **Rychlé a flexibilní nastavení testovacích prostředí**. V Azure Lab Services si můžou vlastníci testovacích prostředí rychle nastavit testovací prostředí podle svých potřeb. Tato služba nabízí možnost postará o veškerou práci infrastruktury Azure pro testovací prostředí spravované typy nebo povolit vlastníků testovacího prostředí na místním Správa a přizpůsobení infrastruktury v předplatném vlastník testovacího prostředí. Služba má integrované možnosti škálování a nastavení odolnosti infrastruktury pro testovací prostředí, která pro vás spravuje.
- **Zjednodušené ovládání pro uživatele testovacích prostředí**. V typu spravované testovacího prostředí, jako je například testovacího prostředí v učebně můžou uživatelé testovacího prostředí zaregistrovat do testovacího prostředí s registračním kódem a přístup k testovacímu prostředí můžete kdykoli použít prostředky testovacího prostředí. V testovacím prostředí v DevTest Labs může vlastník testovacího prostředí udělit oprávnění jeho uživatelům k vytváření virtuálních počítačů a přístupu k nim, správě a opakovanému používání datových disků a nastavení opakovaně použitelných tajných kódů.  
- **Optimalizace nákladů a analýza**. Vlastník testovacího prostředí může nastavit plány automatického vypínání a spouštění virtuálních počítačů. Vlastník testovacího prostředí může vytvořit plán s časovými úseky, kdy mají být virtuální počítače v testovacím prostředí dostupné pro uživatele, optimalizovat náklady nasazením zásad pro uživatele nebo celé testovací prostředí a analyzovat trendy využití a aktivity v testovacím prostředí. Pro testovací prostředí spravované typy například testovací prostředí v učebnách menší podmnožinu cenovým možnostem optimalizace a analýzy jsou teď dostupné.
- **Integrované zabezpečení**. Vlastník testovacího prostředí může pro testovací prostředí nastavit privátní virtuální sítě a podsítě a povolit sdílené veřejné IP adresy. Uživatelé testovacích prostředí můžou pomocí virtuálních sítí nakonfigurovaných s využitím ExpressRoute nebo sítě VPN typu Site-to-Site zajistit zabezpečený přístup k prostředkům. (Aktuálně k dispozici pouze v DevTest Labs)
- **Integrace v pracovních postupech a nástrojích**. Azure Lab Services umožňuje integrovat testovací prostředí do systémů pro správu a na web vaší organizace. Nástroje pro průběžnou integraci a průběžné nasazování (CI/CD) umožňují automaticky zřizovat prostředí. (Aktuálně k dispozici pouze v DevTest Labs)

> [!NOTE]
> Azure Lab Services momentálně podporuje jenom virtuální počítače vytvořené z imagí z webu Azure Marketplace. Pokud chcete použít vlastní image nebo vytvořit další prostředky PaaS v testovacím prostředí, použijte DevTest Labs. Další informace najdete v tématech věnovaných [vytvoření vlastní image v DevTest Labs](devtest-lab-create-custom-image-from-vm-using-portal.md) a [vytvoření testovacích s využitím šablon Resource Manageru](devtest-lab-create-environment-from-arm.md).

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

## <a name="types-of-labs"></a>Typy testovacích prostředí
Vytvoříte dva typy cvičení: **spravovaných typů testovacího prostředí** pomocí Azure Lab Services a **labs** pomocí Azure Lab Services. Pokud chcete jednoduše zadat potřebujete v testovacím prostředí a nechat službu nastavit a spravovat infrastrukturu potřebnou pro testovací prostředí, vyberte jednu z **spravovaných typů testovacího prostředí**. V současné době **testovacího prostředí v učebně** je testovacího prostředí pouze spravovaný typ, který můžete vytvořit pomocí Azure Lab Services. Pokud chcete spravovat vlastní infrastruktury, vytvoření testovacího prostředí s využitím **Azure DevTest Labs**.

Následující části poskytují další podrobnosti o těchto testovacích prostředích. 

## <a name="managed-lab-types"></a>Typy spravovaného testovacího prostředí
Azure Lab Services umožňuje vytvářet testovací prostředí, jejichž infrastrukturu spravuje Azure. Tento článek odkazuje na ně jako typy spravovaných testovacího prostředí. Spravovat různé typy nabídek testovacího prostředí typy cvičení, které vyhovují vašim konkrétním potřebám. V současné době je jen pro testovací prostředí typ, který je podporovaný spravované **testovacího prostředí v učebně**. 

Testovací prostředí spravované typy umožňují vám umožní začít hned, minimální instalaci. Samotná služba zajišťuje veškerou správu infrastruktury pro testovací prostředí, od poskytování potřebného výkonu virtuálních počítačů přes zpracovávání chyb až po škálování infrastruktury. Vytvoření typu spravované prostředí jako je například testovacího prostředí v učebně, musíte nejdřív vytvořit účet testovacího prostředí pro vaši organizaci. Účet testovacího prostředí slouží jako centrální účet, ve kterém se spravují všechna testovací prostředí v organizaci. 

Při vytváření a používání prostředků Azure v těchto typů spravovaných testovacího prostředí, služba vytvoří a spravuje prostředky v interní předplatné Microsoftu. Nevytvářejí se ve vašem vlastním předplatném Azure. Služba uchovává informace o využití těchto prostředků v interních předplatných Microsoftu. Toto využití se fakturuje zpět na vaše předplatné Azure, které obsahuje účet testovacího prostředí.   

Tady jsou některé **případy použití pro testovací prostředí spravované typy**: 

- Poskytněte studentům testovací prostředí s virtuálními počítači, které jsou nakonfigurované přesně tak, jak je pro vyučování potřeba. Můžete pro studenty omezit používání virtuálních počítačů pro domácí úkoly nebo osobní projekty na určitý počet hodin.
- Nastavte fond virtuálních počítačů s vysokým výpočetním výkonem tak, aby bylo možné provádět výzkum náročný na výpočetní výkon nebo na výkon grafiky. Spouštějte virtuální počítače podle potřeby a po dokončení tyto počítače vyčistěte. 
- Přesuňte fyzickou počítačovou učebnu ve vaší škole do cloudu. Automaticky škálujte počet virtuálních počítačů tak, aby nedošlo k překročení limitů využití a nákladů, které jste pro testovací prostředí nastavili.  
- Vytvořte rychle testovací prostředí s virtuálními počítači pro hostování hackathonu. Jakmile testovací prostředí přestanete potřebovat, můžete ho jedním kliknutím odstranit. 


## <a name="devtest-labs"></a>DevTest Labs
Můžete mít scénáře, ve kterých chcete spravovat veškerou infrastrukturu a konfiguraci sami v rámci svého vlastního předplatného. Uděláte to tak, že vytvoříte testovací prostředí pomocí služby Azure DevTest Labs na webu Azure Portal. Pro tato testovací prostředí není nutné vytvořit účet testovacího prostředí. Tyto testovací prostředí není uveden v účtu testovacího prostředí (která existuje po dobu typů spravovaných testovacího prostředí).  

Tady je několik **případů použití DevTest Labs**: 

- Vytvořte rychle testovací prostředí s virtuálními počítači pro hostování hackathonu nebo praktické relace na konferenci. Jakmile testovací prostředí přestanete potřebovat, můžete ho jedním kliknutím odstranit. 
- Vytvořte fond virtuálních počítačů, ve kterých je nakonfigurovaná vaše aplikace, a umožněte vašemu týmu použít virtuální počítač pro společné hledání chyb vývojáři.  
- Poskytněte vývojářům virtuální počítače, ve kterých jsou nakonfigurované všechny potřebné nástroje. Naplánujte automatické spouštění a vypínání, aby se minimalizovaly náklady. 
- Vytvořte v rámci vašeho nasazení opakovaně testovací prostředí s testovacími počítači. Otestujte váš nejnovější kód a po dokončení testovací počítače vyčistěte. 
- Nastavte řadu různě nakonfigurovaných virtuálních počítačů a testovacích agentů pro škálování a testování výkonu. 
- Nabídněte vašim zákazníkům školení s využitím testovacího prostředí, ve kterém je nakonfigurovaná nejnovější verze vašeho produktu. Poskytněte každému zákazníkovi určitý počet hodin pro práci v testovacím prostředí. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Typy spravovaného testovacího prostředí versus DevTest Labs
Následující tabulka porovnává dva typy testovacích prostředí, které služba Azure Lab Services podporuje: 

| Funkce | Typy spravovaného testovacího prostředí | DevTest Labs |
| -------- | ----------------- | ---------- |
| Správa infrastruktury Azure v testovacím prostředí |  Automaticky spravuje služba. | Spravujete sami.  |
| Předdefinovaná odolnost vůči problémům s infrastrukturou | Automaticky zpracovává služba. | Spravujete sami.  |
| Správa předplatného | Služba zpracovává přidělování prostředků v předplatných Microsoftu, která podporují danou službu. Škálování automaticky zpracovává služba. | Spravujete sami ve svém vlastním předplatném Azure. Žádné automatické škálování předplatných. |
| Nasazení Azure Resource Manageru v testovacím prostředí | Není k dispozici. | K dispozici. |

## <a name="next-steps"></a>Další postup

Viz následující články: 

- [Informace o testovacích prostředích v učebnách](./classroom-labs/classroom-labs-overview.md)
- [Informace o službě DevTest Labs](devtest-lab-overview.md)

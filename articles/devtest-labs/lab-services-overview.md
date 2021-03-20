---
title: Azure Lab Services vs. Azure DevTest Labs
description: Porovnejte Azure DevTest Labs a Azure Lab Services.
ms.topic: overview
ms.date: 06/26/2020
ms.openlocfilehash: b1cd476faf6c457033ffeace03cd2e37b51e8578
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "85480078"
---
# <a name="compare-azure-devtest-labs-and-azure-lab-services"></a>Porovnat Azure DevTest Labs a Azure Lab Services
V Azure existují dvě služby, které umožňují nastavit testovací prostředí v cloudu. 

- **Azure DevTest Labs** – tato služba vám umožňuje rychle nastavit prostředí pro váš tým (například vývojové prostředí nebo testovací prostředí v cloudu). Vlastník testovacího prostředí vytvoří testovací prostředí, zřídí virtuální počítače s Windows nebo Linuxem, nainstaluje nezbytný software a nástroje a zpřístupní je uživatelům testovacího prostředí. Uživatelé testovacího prostředí se můžou v testovacím prostředí připojit k virtuálním počítačům (VM) a používat je pro každodenní práci, krátkodobé projekty. Jakmile uživatelé začnou využívat prostředky v testovacím prostředí, správce testovacího prostředí může analyzovat náklady a využití ve všech spravovaných testovacích prostředích a nastavením globálních zásad optimalizovat náklady organizace i jednotlivých týmů.
- **Azure Lab Services** – tato služba umožňuje vytvářet spravované typy testovacího prostředí. V současné době je učebna Labs jediným typem spravovaného testovacího prostředí, které podporuje Azure Lab Services. Tato služba zpracovává veškerou správu infrastruktury pro typ spravovaného testovacího prostředí, od odstřeďování virtuálních počítačů po zpracování chyb a škálování infrastruktury. Jakmile správce IT vytvoří v Azure Lab Services účet testovacího prostředí, může instruktor rychle vytvořit testovací prostředí pro svou třídu, určit počet a typ virtuálních počítačů, které jsou ve třídě potřeba, a přidat uživatele do třídy. Jakmile se uživatel zaregistruje do třídy, uživatel může získat přístup k virtuálnímu počítači, aby mohl provádět cvičení pro třídu.  

## <a name="key-capabilities"></a>Klíčové funkce

Tyto služby (Azure DevTest Labs a Azure Lab Services) podporují následující klíčové funkce:

- **Rychlé a flexibilní nastavení testovacích prostředí**. V Azure Lab Services si můžou vlastníci testovacích prostředí rychle nastavit testovací prostředí podle svých potřeb. Služba nabízí možnost pořídit veškerou práci na infrastruktuře Azure pro spravované typy testovacího prostředí nebo umožnit vlastníkům testovacího prostředí samoobslužné řízení a přizpůsobení infrastruktury v předplatném vlastníka testovacího prostředí. Služba má integrované možnosti škálování a nastavení odolnosti infrastruktury pro testovací prostředí, která pro vás spravuje.
- **Zjednodušené ovládání pro uživatele testovacích prostředí**. V spravovaném typu testovacího prostředí, jako je například prostředí pro učebnu, se uživatelé testovacího prostředí můžou zaregistrovat do testovacího kódu s registračním kódem a kdykoli k testovacímu prostředí přistupovat pomocí prostředků testovacího prostředí. V testovacím prostředí v DevTest Labs může vlastník testovacího prostředí udělit oprávnění jeho uživatelům k vytváření virtuálních počítačů a přístupu k nim, správě a opakovanému používání datových disků a nastavení opakovaně použitelných tajných kódů.  
- **Optimalizace nákladů a analýza**. Vlastník testovacího prostředí může nastavit plány automatického vypínání a spouštění virtuálních počítačů. Vlastník testovacího prostředí může vytvořit plán s časovými úseky, kdy mají být virtuální počítače v testovacím prostředí dostupné pro uživatele, optimalizovat náklady nasazením zásad pro uživatele nebo celé testovací prostředí a analyzovat trendy využití a aktivity v testovacím prostředí. Pro spravované typy testovacích prostředí, jako je například učebna Labs, je aktuálně k dispozici menší podmnožina možností optimalizace nákladů a analýzy.
- **Integrované zabezpečení**. Vlastník testovacího prostředí může pro testovací prostředí nastavit privátní virtuální sítě a podsítě a povolit sdílené veřejné IP adresy. Uživatelé testovacích prostředí můžou pomocí virtuálních sítí nakonfigurovaných s využitím ExpressRoute nebo sítě VPN typu Site-to-Site zajistit zabezpečený přístup k prostředkům. (Aktuálně k dispozici pouze v DevTest Labs)
- **Integrace v pracovních postupech a nástrojích**. Azure Lab Services umožňuje integrovat testovací prostředí do systémů pro správu a na web vaší organizace. Nástroje pro průběžnou integraci a průběžné nasazování (CI/CD) umožňují automaticky zřizovat prostředí. (Aktuálně k dispozici pouze v DevTest Labs)

## <a name="scenarios"></a>Scénáře

Tady je několik scénářů, které Azure DevTest Labs a podpora Azure Lab Services:

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

## <a name="types-of-labs"></a>Typy cvičení
Můžete vytvořit dva typy cvičení: **spravované typy testovacích prostředí** pomocí Azure Lab Services a **Labs** s Azure Lab Services. Pokud chcete jenom zadat, co potřebujete v testovacím prostředí, a nechat službu nastavenou a spravovat infrastrukturu potřebnou pro testovací prostředí, vyberte jeden ze **spravovaných typů testovacího prostředí**. V současné době je **prostředí učebny** jediným typem spravovaného testovacího prostředí, které můžete vytvořit pomocí Azure Lab Services. Pokud chcete spravovat vlastní infrastrukturu, vytvořte testovací prostředí pomocí **Azure DevTest Labs**.

Následující části poskytují další podrobnosti o těchto testovacích prostředích. 

## <a name="managed-lab-types"></a>Typy spravovaného testovacího prostředí
Azure Lab Services umožňuje vytvářet testovací prostředí, jejichž infrastrukturu spravuje Azure. Tento článek se na ně odkazuje jako na typy spravovaných testovacích prostředí. Spravované typy testovacího prostředí nabízejí různé typy cvičení, které odpovídají vašim konkrétním potřebám. V současné době je jako **laboratorní prostředí učebny** pouze spravovaný typ testovacího prostředí. 

Spravované typy testovacího prostředí vám umožňují hned začít s minimálním nastavením. Samotná služba zajišťuje veškerou správu infrastruktury pro testovací prostředí, od poskytování potřebného výkonu virtuálních počítačů přes zpracovávání chyb až po škálování infrastruktury. Chcete-li vytvořit spravovaný typ testovacího prostředí, jako je například učebna, musíte nejprve vytvořit účet testovacího prostředí pro vaši organizaci. Účet testovacího prostředí slouží jako centrální účet, ve kterém se spravují všechna testovací prostředí v organizaci. 

Při vytváření a používání prostředků Azure v těchto spravovaných typech testovacích prostředí služba vytvoří a spravuje prostředky v interních předplatných Microsoftu. Nevytvářejí se ve vašem vlastním předplatném Azure. Služba uchovává informace o využití těchto prostředků v interních předplatných Microsoftu. Toto využití se fakturuje zpět na vaše předplatné Azure, které obsahuje účet testovacího prostředí.   

Zde jsou některé **případy použití pro spravované typy testovacích prostředí**: 

- Poskytněte studentům testovací prostředí s virtuálními počítači, které jsou nakonfigurované přesně tak, jak je pro vyučování potřeba. Můžete pro studenty omezit používání virtuálních počítačů pro domácí úkoly nebo osobní projekty na určitý počet hodin.
- Nastavte fond virtuálních počítačů s vysokým výpočetním výkonem tak, aby bylo možné provádět výzkum náročný na výpočetní výkon nebo na výkon grafiky. Spouštějte virtuální počítače podle potřeby a po dokončení tyto počítače vyčistěte. 
- Přesuňte fyzickou počítačovou učebnu ve vaší škole do cloudu. Automaticky škálujte počet virtuálních počítačů tak, aby nedošlo k překročení limitů využití a nákladů, které jste pro testovací prostředí nastavili.  
- Vytvořte rychle testovací prostředí s virtuálními počítači pro hostování hackathonu. Jakmile testovací prostředí přestanete potřebovat, můžete ho jedním kliknutím odstranit. 


## <a name="devtest-labs"></a>DevTest Labs
Můžete mít scénáře, ve kterých chcete spravovat veškerou infrastrukturu a konfiguraci sami v rámci svého vlastního předplatného. Uděláte to tak, že vytvoříte testovací prostředí pomocí služby Azure DevTest Labs na webu Azure Portal. Pro tato testovací prostředí není nutné vytvořit účet testovacího prostředí. Tyto laboratoře se nezobrazují v účtu testovacího prostředí (který existuje pro spravované typy testovacího prostředí).  

Tady jsou některé **případy použití pro používání DevTest Labs**: 

- Vytvořte rychle testovací prostředí s virtuálními počítači pro hostování hackathonu nebo praktické relace na konferenci. Jakmile testovací prostředí přestanete potřebovat, můžete ho jedním kliknutím odstranit. 
- Vytvořte fond virtuálních počítačů, ve kterých je nakonfigurovaná vaše aplikace, a umožněte vašemu týmu použít virtuální počítač pro společné hledání chyb vývojáři.  
- Poskytněte vývojářům virtuální počítače, ve kterých jsou nakonfigurované všechny potřebné nástroje. Naplánujte automatické spouštění a vypínání, aby se minimalizovaly náklady. 
- Vytvořte v rámci vašeho nasazení opakovaně testovací prostředí s testovacími počítači. Otestujte váš nejnovější kód a po dokončení testovací počítače vyčistěte. 
- Nastavte řadu různě nakonfigurovaných virtuálních počítačů a testovacích agentů pro škálování a testování výkonu. 
- Nabídněte vašim zákazníkům školení s využitím testovacího prostředí, ve kterém je nakonfigurovaná nejnovější verze vašeho produktu. Poskytněte každému zákazníkovi určitý počet hodin pro práci v testovacím prostředí. 


## <a name="managed-lab-types-vs-devtest-labs"></a>Spravované typy testovacích prostředí vs. DevTest Labs
Následující tabulka porovnává dva typy testovacích prostředí, které služba Azure Lab Services podporuje: 

| Funkce | Typy spravovaného testovacího prostředí | DevTest Labs |
| -------- | ----------------- | ---------- |
| Správa infrastruktury Azure v testovacím prostředí |  Automaticky spravuje služba. | Spravujete sami.  |
| Předdefinovaná odolnost vůči problémům s infrastrukturou | Automaticky zpracovává služba. | Spravujete sami.  |
| Správa předplatného | Služba zpracovává přidělování prostředků v předplatných Microsoftu, která podporují danou službu. Škálování automaticky zpracovává služba. | Spravujete sami ve svém vlastním předplatném Azure. Žádné automatické škálování předplatných. |
| Nasazení Azure Resource Manageru v testovacím prostředí | Není k dispozici | K dispozici |

## <a name="next-steps"></a>Další kroky

Viz následující články: 

- [Informace o testovacích prostředích v učebnách](../lab-services/classroom-labs-overview.md)
- [Informace o službě DevTest Labs](devtest-lab-overview.md)

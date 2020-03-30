---
title: Co je proces vědecké vědy o týmových datech?
description: Poskytuje metodiku datové vědy pro poskytování prediktivních analytických řešení a inteligentních aplikací.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 1/10/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 10d6e562301e089700940ac5dfb212bcc4e09653
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79088072"
---
# <a name="what-is-the-team-data-science-process"></a>Co je proces vědecké vědy o týmových datech?

Team Data Science Process (TDSP) je agilní, iterativní metodika datové vědy, která efektivně poskytuje prediktivní analytická řešení a inteligentní aplikace. TDSP pomáhá zlepšit týmovou spolupráci a učení tím, že navrhuje, jak týmové role fungují nejlépe společně. TDSP obsahuje osvědčené postupy a struktury od společnosti Microsoft a dalších vedoucích pracovníků v oboru, které pomáhají k úspěšné implementaci iniciativ datové vědy. Cílem je umožnit společnostem plně využít výhod jejich analytického programu.

Tento článek obsahuje přehled TDSP a jeho hlavní součásti. Poskytujeme obecný popis procesu zde, které mohou být implementovány s různými druhy nástrojů. Podrobnější popis projektových úkolů a rolí zapojených do životního cyklu procesu je uveden v dalších propojených tématech. Pokyny k implementaci TDSP pomocí konkrétní sadu nástrojů a infrastruktury Microsoftu, které používáme k implementaci TDSP v našich týmech je také k dispozici.

## <a name="key-components-of-the-tdsp"></a>Klíčové součásti TDSP

TDSP se skládá z těchto klíčových složek:

- Definice **životního cyklu datové vědy**
- **Standardizovaná struktura projektu**
- **Infrastruktura a zdroje** pro projekty datové vědy
- **Nástroje a nástroje** pro realizaci projektu


## <a name="data-science-lifecycle"></a>Životní cyklus datové vědy

Proces vědecké ho procesu týmových dat (TDSP) poskytuje životní cyklus pro strukturu vývoje vašich projektů datové vědy. Životní cyklus popisuje úplné kroky, které úspěšné projekty následují.

Pokud používáte jiný životní cyklus datové vědy, například [CRISP-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) nebo vlastní proces vaší organizace, můžete stále používat tdsp založený na úlohách v kontextu těchto životních cyklů vývoje. Na vysoké úrovni mají tyto různé metodiky mnoho společného. 

Tento životní cyklus byl navržen pro projekty datové vědy, které jsou dodávány jako součást inteligentních aplikací. Tyto aplikace nasazují modely strojového učení nebo umělé inteligence pro prediktivní analýzu. Z používání tohoto procesu mohou mít prospěch také projekty průzkumné datové vědy nebo improvizované analytické projekty. V takových případech však nemusí být některé popsané kroky potřebné.    

Životní cyklus popisuje hlavní fáze, které projekty obvykle provádějí, často iterativně:

* **Obchodní porozumění**
* **Získávání a porozumění dat**
* **Modelování**
* **Nasazení**
* **Přijetí zákazníkem**

Zde je vizuální reprezentace **životního cyklu procesu vědecké hospo-** 

![TDSP-Životní cyklus2](./media/overview/tdsp-lifecycle2.png) 

Cíle, úkoly a artefakty dokumentace pro každou fázi životního cyklu v tdsp jsou popsány v tématu [životního cyklu procesu týmových dat.](lifecycle.md) Tyto úkoly a artefakty jsou přidruženy k rolím projektu:

- Architekt řešení
- Projektový manažer
- Odborník přes data
- Vedoucí projektu 

Následující diagram obsahuje zobrazení mřížky úkolů (modře) a artefaktů (zeleně) spojených s každou fází životního cyklu (na vodorovné ose) pro tyto role (na svislé ose). 

[![TDSP-role a úkoly](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Standardizovaná struktura projektu

Mít všechny projekty sdílet adresářovou strukturu a používat šablony pro projektové dokumenty usnadňuje členům týmu najít informace o svých projektech. Veškerý kód a dokumenty jsou uloženy v systému správy verzí (VCS), jako je Git, TFS nebo Subversion, aby umožnily týmovou spolupráci. Sledování úkolů a funkcí v agilním systému sledování projektů, jako je Jira, Rally a Azure DevOps, umožňuje bližší sledování kódu pro jednotlivé funkce. Toto sledování také umožňuje týmům získat lepší odhady nákladů. TDSP doporučuje vytvořit samostatné úložiště pro každý projekt na VCS pro správu verzí, zabezpečení informací a spolupráci. Standardizovaná struktura pro všechny projekty pomáhá budovat institucionální znalosti v celé organizaci.

Poskytujeme šablony pro strukturu složek a požadované dokumenty ve standardních umístěních. Tato struktura složek uspořádá soubory, které obsahují kód pro zkoumání dat a extrakci funkcí a které zaznamenávají iterace modelu. Tyto šablony usnadňují členům týmu pochopit práci ostatních a přidávat nové členy do týmů. Je snadné zobrazit a aktualizovat šablony dokumentů ve formátu markdown. Pomocí šablon můžete poskytnout kontrolní seznamy s klíčovými otázkami pro každý projekt, abyste se ujistili, že problém je dobře definován a že dodávky splňují očekávanou kvalitu. Příklady obsahují:

- chartu projektu pro dokumentaci obchodního problému a rozsahu projektu
- datových zpráv pro dokumentaci struktury a statistiky nezpracovaných údajů
- sestavy modelů pro dokumentaci odvozených prvků
- metriky výkonu modelu, jako jsou křivky ROC nebo MSE


[![TDSP-adresáře](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

Adresářovou strukturu lze klonovat z [GitHubu](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktura a zdroje pro projekty datové vědy  

TDSP poskytuje doporučení pro správu sdílené analýzy a infrastruktury úložišť, jako jsou:

- cloudové souborové systémy pro ukládání datových sad 
- databáze
- clustery velkých objemů dat (Hadoop nebo Spark) 
- služba strojového učení 

Infrastruktura pro analýzy a úložiště, kde jsou uloženy nezpracované a zpracované datové sady, může být v cloudu nebo místně. Tato infrastruktura umožňuje reprodukovatelnou analýzu. Rovněž se vyhýbá duplicitě, což může vést k nesrovnalostem a zbytečným nákladům na infrastrukturu. Nástroje jsou k dispozici pro zřízení sdílených prostředků, jejich sledování a umožnit každému členovi týmu připojit se k těmto prostředkům bezpečně. Je také vhodné mít členy projektu vytvořit konzistentní výpočetní prostředí. Různí členové týmu pak mohou replikovat a ověřovat experimenty.

Tady je příklad týmu, který pracuje na více projektech a sdílí různé komponenty infrastruktury cloudové analýzy.

[![Infrastruktura TDSP](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Nástroje a nástroje pro realizaci projektu

Zavádění procesů ve většině organizací je náročné. Nástroje poskytované k implementaci procesu datové vědy a životního cyklu pomáhají snižovat překážky a zvyšovat konzistentnost jejich přijetí. TDSP poskytuje počáteční sadu nástrojů a skriptů pro nastartování přijetí TDSP v rámci týmu. Pomáhá také automatizovat některé běžné úkoly v životním cyklu datové vědy, jako je průzkum dat a základní modelování. Existuje dobře definovaná struktura, která jednotlivcům poskytuje příspěvky sdílených nástrojů a nástrojů do úložiště sdílených kódů svého týmu. Tyto prostředky pak mohou být využity jinými projekty v rámci týmu nebo organizace. TDSP také plánuje umožnit příspěvky nástrojů a nástrojů pro celou komunitu. Nástroje TDSP lze klonovat z [GitHubu](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Další kroky

[Proces vědecké ho spoje: Role a úkoly](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Popisuje klíčové role personálu a jejich přidružené úkoly pro tým datové vědy, který standardizuje tento proces. 

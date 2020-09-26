---
title: Co je vědecké zpracování týmových dat?
description: Poskytuje metodologii pro datovou vědu pro poskytování prediktivních analytických řešení a inteligentních aplikací.
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
ms.openlocfilehash: fc9a5e604e6ac52eb0e853725123272ca924fa36
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362227"
---
# <a name="what-is-the-team-data-science-process"></a>Co je vědecké zpracování týmových dat?

Vědecké zpracování týmových dat (TDSP) je agilní a iterativní metodologie pro datové vědy, která umožňuje efektivně dodávat prediktivní Analytická řešení a inteligentní aplikace. TDSP pomáhá zdokonalit týmovou spolupráci a učení tím, že navrhuje, jak budou role týmu fungovat nejlépe dohromady. TDSP zahrnuje osvědčené postupy a struktury od Microsoftu a jiných průmyslových vedoucích, které vám pomůžou s úspěšnou implementací iniciativ pro datové vědy. Cílem je umožnit společnostem plně využít výhod jejich analytického programu.

Tento článek poskytuje přehled o TDSP a hlavních součástech. Poskytujeme obecný popis tohoto procesu, který se dá implementovat s různými druhy nástrojů. Podrobnější popis úloh projektu a rolí, které se podílejí v životním cyklu procesu, je k dispozici v dalších tématech souvisejících s propojením. K dispozici jsou také pokyny k implementaci TDSP pomocí konkrétní sady nástrojů a infrastruktury společnosti Microsoft, kterou používáme k implementaci TDSP v našich týmech.

## <a name="key-components-of-the-tdsp"></a>Klíčové součásti TDSP

TDSP má následující klíčové komponenty:

- Definice **životního cyklu datové vědy**
- **Standardizovaná struktura projektu**
- Doporučené **infrastruktury a prostředky** pro projekty pro datové vědy
- **Nástroje a pomůcky** doporučené pro spuštění projektu


## <a name="data-science-lifecycle"></a>Životní cyklus pro datové vědy

Vědecké zpracování týmových dat (TDSP) poskytuje životní cyklus pro strukturu vývoje projektů pro datové vědy. Životní cyklus popisuje všechny kroky, které úspěšné projekty následují.

Pokud používáte jiný životní cyklus pro datové vědy, jako je například [ostrý, DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [konference KDD](https://wikipedia.org/wiki/Data_mining#Process) nebo vlastní vlastní proces vaší organizace, můžete i nadále používat TDSP založenou na úlohách v kontextu těchto životních cyklů vývoje. V nejvyšší úrovni mají tyto různé metodologie mnohem společné. 

Tento životní cyklus je navržený pro projekty pro datové vědy, které se dodávají jako součást inteligentních aplikací. Tyto aplikace nasazují modely strojového učení nebo uměle Intelligence pro prediktivní analýzy. Používání tohoto procesu může být výhodné i pro průzkumné projekty pro zpracování dat nebo projekty Improvised Analytics. V takových případech ale některé popsané kroky nemusí být potřeba.    

Životní cyklus popisuje hlavní fáze, které se obvykle spouštějí projekty, často iterativní:

* **Obchodní porozumění**
* **Získávání a porozumění dat**
* **Modelování**
* **Nasazení**

Tady je vizuální reprezentace **životního cyklu vědeckého zpracování týmových dat**. 

![Diagram znázorňuje životní cyklus pro datové vědy, včetně podnikového porozumění, získávání a porozumění dat, modelování a nasazování.](./media/overview/tdsp-lifecycle2.png) 

Cíle, úlohy a artefakty dokumentace pro každou fázi životního cyklu v TDSP jsou popsány v tématu [životní cyklus procesu vědeckého zpracování dat týmu](lifecycle.md) . Tyto úlohy a artefakty jsou přidruženy k rolím projektu:

- Architekt řešení
- Projektový manažer
- Datový inženýr
- Datový vědec
- Vývojář aplikace
- Vedoucí projektu 

Následující diagram představuje zobrazení mřížky úloh (modrých) a artefaktů (zeleně) přidružených k jednotlivým fázím životního cyklu (na vodorovné ose) pro tyto role (na svislé ose). 

[![TDSP – role a – úkoly](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Standardizovaná struktura projektu

Všechny projekty, které sdílí strukturu adresářů a používají šablony pro projektové dokumenty, usnadňují členům týmu hledání informací o jejich projektech. Veškerý kód a dokumenty jsou uloženy v systému správy verzí (VCS), jako je git, TFS nebo podverze, aby bylo možné povolit týmovou spolupráci. Sledování úloh a funkcí v systému agilního sledování projektů, jako je JIRA, rally a Azure DevOps, umožňuje blíže sledovat kód pro jednotlivé funkce. Toto sledování také umožňuje týmům získat lepší odhady nákladů. TDSP doporučuje vytvořit samostatné úložiště pro každý projekt na VC pro správu verzí, zabezpečení informací a spolupráci. Standardizovaná struktura pro všechny projekty pomáhá sestavovat institucionální znalosti napříč organizací.

Poskytujeme šablony pro strukturu složek a požadované dokumenty ve standardních umístěních. Tato struktura složky uspořádává soubory, které obsahují kód pro zkoumání dat a extrakci funkcí a které mají iterace modelu záznamu. Tyto šablony usnadňují členům týmu pochopit práci prováděnou ostatními uživateli a přidávat nové členy do týmů. Šablony dokumentů ve formátu Markdownu je snadné zobrazit a aktualizovat. Pomocí šablon můžete poskytnout kontrolní seznamy s klíčovými otázkami pro každý projekt, abyste měli jistotu, že problém je dobře definovaný a že dodávky odpovídají očekávané kvalitě. Příklady:

- Projektová Charta k dokumentaci obchodního problému a rozsahu projektu
- sestavy dat pro dokumentaci struktury a statistiky nezpracovaných dat
- sestavy modelů pro dokumentaci odvozených funkcí
- modelování metrik výkonu, jako jsou například ROC, křivky nebo MSE


[![TDSP – adresáře](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

Adresářová struktura může být klonována z [GitHubu](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktura a prostředky pro projekty pro datové vědy  

TDSP poskytuje doporučení pro správu sdílených analýz a infrastruktury úložiště, jako jsou:

- souborové systémy v cloudu pro ukládání datových sad 
- V databázích
- clustery s velkými objemy dat (SQL nebo Spark) 
- Služba Machine Learning 

Analýza a infrastruktura úložiště, kde jsou uložené a zpracovávané datové sady, můžou být v cloudu nebo místně. Tato infrastruktura umožňuje reprodukovatelnou analýzu. Vyloučí se taky duplicity, což může vést k nekonzistencím a zbytečným nákladům na infrastrukturu. Poskytují se nástroje pro zřizování sdílených prostředků, jejich sledování a umožnění každého člena týmu, aby se k těmto prostředkům připojovali bezpečně. Je také dobrým zvykem, aby členové projektu vytvořili konzistentní výpočetní prostředí. Různí členové týmu pak mohou replikovat a ověřovat experimenty.

Tady je příklad týmu pracujícího na více projektech a sdílení různých komponent infrastruktury Cloud Analytics.

[![TDSP – infrastruktura](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Nástroje a pomůcky pro provádění projektů

Představujeme procesy ve většině organizací je náročné. Nástroje, které jsou k dispozici pro implementaci procesu a životního cyklu datové vědy, můžou snížit překážky a zvýšit konzistenci jejich přijetí. TDSP poskytuje počáteční sadu nástrojů a skriptů, které umožňují přecházet k zahájení přijetí TDSP v rámci týmu. Pomáhá také automatizovat některé běžné úkoly v životním cyklu pro datové vědy, jako je zkoumání dat a modelování směrného plánu. K dispozici je dobře definovaná struktura pro jednotlivce, které přispívat sdíleným nástrojům a nástrojům do úložiště se sdíleným kódem svého týmu. Tyto prostředky pak mohou využívat jiné projekty v rámci týmu nebo organizace. TDSP také plánuje povolit příspěvky nástrojů a nástrojů do celé komunity. Nástroje TDSP můžou být naklonované z [GitHubu](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Další kroky

[Vědecké zpracování týmových dat: role a úlohy](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Popisuje klíčové role personálu a jejich přidružené úkoly pro tým pro datové vědy, který se v tomto procesu bude standardizovat. 

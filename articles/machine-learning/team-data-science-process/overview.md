---
title: Co je vědecké zpracování týmových dat? | Dokumenty Microsoft
description: Poskytuje data metodika vědecké účely k poskytování řešení prediktivní analýzy a inteligentní aplikací.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: b1f677bb-eef5-4acb-9b3b-8a5819fb0e78
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: deguhath
ms.openlocfilehash: 8479b23f49402962acbf50016226ea0f2eb67b68
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34839074"
---
# <a name="what-is-the-team-data-science-process"></a>Co je vědecké zpracování týmových dat?

Proces pro vědecké účely Data Team (TDSP) je metodika vědecké účely agilní, iterativní data k poskytování efektivní řešení prediktivní analýzy a inteligentní aplikací. Vědecké zpracování týmových dat pomáhá vylepšit spolupráci a učení v rámci týmů. Obsahuje výtah osvědčených postupů a struktur od Microsoftu a dalších společností v tomto oboru, které usnadňují úspěšnou implementaci iniciativ datových věd. Cílem je umožnit společnostem plně využít výhod jejich analytického programu.

Tento článek obsahuje přehled TDSP a jeho hlavní součásti. Poskytujeme obecný popis zde procesu, který může být implementováno s celou řadu nástrojů. Podrobnější popis projektu úlohy a role zahrnutých v životním cyklu procesu najdete v další související témata. K dispozici jsou také pokyny o tom, jak implementovat TDSP pomocí konkrétní sadu nástrojů Microsoft a infrastrukturu, která jsme použít k implementaci TDSP v našimi týmy.

## <a name="key-components-of-the-tdsp"></a>Klíčové komponenty TDSP

TDSP se skládá z následujících součástí klíče:

- A **datové vědy cyklu** definice
- A **standardizované struktura projektu**
- **Infrastruktura a prostředky** pro projekty data vědecké účely
- **Nástrojů a pomůcek** pro spuštění projektu


## <a name="data-science-lifecycle"></a>Životní cyklus dat vědecké účely

Proces pro vědecké účely Data Team (TDSP) poskytuje životního cyklu do struktury vývoj projekty vědecké účely data. Životní cyklus popisuje kroky, od začátku do konce, projekty obvykle postupujte při jejich spuštění.

Pokud použijete jiný vědecké účely životního cyklu data, jako [OSTRÉ DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) nebo vaše organizace vlastní proces založený na úlohách TDSP můžete dál používat v rámci těchto vývojové životní cykly. Na vysoké úrovni tyto různé metody mají hodně společné. 

Pro projekty vědecké účely dat, které se dodávají jako součást inteligentní aplikace byl navržen tak tohoto životního cyklu. Tyto aplikace nasadit machine learning nebo umělé intelligence modely pro prediktivní analýzy. Projekty vědecké účely nahodilého dat nebo ad hoc analytics projekty mohou také těžit z pomocí tohoto procesu. Ale v takovém případě některé z kroků popsaných nemusí být potřeba.    

Životní cyklus popisuje hlavní fází, které projekty obvykle provést, často interaktivně:

* **Pochopení obchodních**
* **Získávání dat a principy**
* **Modelování**
* **Nasazení**
* **Přijetí zákazníka**

Tady je vizuální reprezentace **procesu vědecké účely Team datového cyklu**. 

![TDSP Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

Cíle, úlohy a artefakty dokumentace pro každé fáze životního cyklu v TDSP jsou popsané v [procesu vědecké účely Team datového cyklu](lifecycle.md) tématu. Tyto úlohy a artefaktů jsou přidruženy role projektu:

- Architekt řešení
- Správce projektu
- Odborník přes data
- Vedoucí projektu 

Následující diagram představuje zobrazení mřížky úloh (modře) a artefakty (zeleně) přidružené každé fáze životního cyklu (na vodorovné ose) pro tyto role (na svislé ose). 

![TDSP-role a úlohy](./media/overview/tdsp-tasks-by-roles.png)

## <a name="standardized-project-structure"></a>Struktura standardizované projektu

Všechny projekty sdílet adresářovou strukturu a použití šablon projektu dokumentů s usnadňuje členové týmu najít informace o jejich projektů. Všechny kódu a dokumenty jsou uloženy v systému správy verzí (VC), jako je Git, sady TFS nebo Subversion umožňují týmovou spolupráci. Sledování úloh a funkce v projektu agilní sledování systému jako Jira, technologie Rally, Visual Studio Team Services umožňuje blíže sledování kód pro jednotlivé funkce. Takové sledování taky umožňuje týmy a získat lepší odhadované náklady. TDSP doporučuje vytvoření samostatné úložiště pro každý projekt na VC Správa verzí, informace o zabezpečení a spolupráci. Standardizovaná strukturu pro všechny projekty pomáhá vytvářet institucionální znalost celé organizace.

Poskytujeme šablony pro strukturu složek a požadované dokumenty standardní umístění. Tato struktura složek umožňuje uspořádat soubory obsahující kód pro zkoumání dat a funkce extrakce, a který záznam modelu iterací. Tyto šablony usnadňují členové týmu, abyste pochopili práci jinými uživateli a přidat nové členy do týmů. Je snadné k zobrazování a aktualizace šablony dokumentů ve formátu markdown. Použití šablon poskytnout klíčové otázky pro každý projekt zajistit, že problém je dobře definovaný a že výsledek splňují kvality očekávaný kontrolní seznamy. Příklady obsahují:

- Projekt titulů do dokumentů obchodního problému a rozsah projektu
- sestavy dat dokumentu strukturu a statistiky nezpracovaná data
- model sestavy do odvozené funkce dokumentů
- metriky výkonu modelu například křivek ROC nebo MSE


![TDSP adresáře](./media/overview/tdsp-dir-structure.png)

Strukturu adresáře můžete klonovat z [Githubu](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktury a prostředky pro projekty data vědecké účely  

TDSP poskytuje doporučení pro správu sdílené analýzy a infrastruktury úložiště, jako:

- cloudové systémy souborů pro ukládání datových sad 
- databáze
- velké objemy dat (Hadoop nebo Spark) clustery 
- služby Machine learning. 

Infrastruktura analýzy a úložiště může být v cloudu nebo místně. Toto je, kde jsou uložené datové sady nezpracovaná a zpracovány. Tato infrastruktura umožňuje reprodukovatelnou analýzu. Ho také předejdete duplikace, což může vést k nekonzistenci a náklady na infrastrukturu zbytečné. Nástroje jsou k dispozici a zřizovat sdílené prostředky, je sledovat a povolit každý člen týmu pro zabezpečené připojení k prostředkům. Je také vhodné mít členy projektu vytvořit konzistentní výpočetním prostředí. Členové týmu různých můžete replikovat a ověřit experimenty.

Tady je příklad týmu pracující na více projektů a sdílení různých součástí infrastruktury cloudu analytics.

![TDSP infrastruktury](./media/overview/tdsp-analytics-infra.png)


## <a name="tools-and-utilities-for-project-execution"></a>Nástroje pro spuštění projektu

Představení procesy v většina organizací je náročná. Nástroje poskytované implementace nápovědy proces a životního cyklu vědecké účely data nižší se překážek a zvýšit konzistence jejich přijetí. TDSP poskytuje počáteční sadu nástrojů a skripty, které vám pomohou rychle začít přijetí TDSP v rámci týmu. Pomáhá také automatizovat některé běžné úlohy v životním cyklu vědecké účely data například zkoumání dat a modelování směrného plánu. Není dobře nastavené struktury zadaný pro jednotlivce přispívání sdílené nástrojů a pomůcek do úložiště sdíleného kódu jejich tým. Tyto prostředky lze poté využít další projekty v rámci tým nebo organizace. TDSP také plány povolit příspěvky nástrojů a pomůcek celé komunitě. Nástroje TDSP dají klonovat z [Githubu](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Další postup

[Proces vědecké účely dat Team: Rolí a úloh](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) popisuje role klíče pracovníky a jejich přidružených úloh pro data tým vědecké účely, standardizující tohoto postupu. 

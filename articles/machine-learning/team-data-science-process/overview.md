---
title: Co je vědecké zpracování týmových dat? | Dokumenty Microsoft
description: Poskytuje metodologie pro datové vědy dodávat prediktivní analytická řešení a inteligentní aplikace.
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
ms.openlocfilehash: e6e6b8e03102825a15035adbea80ac5e9f3fa404
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46996447"
---
# <a name="what-is-the-team-data-science-process"></a>Co je vědecké zpracování týmových dat?

Zpracování vědy dat (TDSP) týmu je metodologie pro efektivně dodávat prediktivní analytická řešení a inteligentní aplikace agile, iterativní datové vědy. Vědecké zpracování týmových dat pomáhá vylepšit spolupráci a učení v rámci týmů. Obsahuje výtah osvědčených postupů a struktur od Microsoftu a dalších společností v tomto oboru, které usnadňují úspěšnou implementaci iniciativ datových věd. Cílem je umožnit společnostem plně využít výhod jejich analytického programu.

Tento článek obsahuje přehled TDSP a jeho hlavní komponenty. Poskytujeme obecný popis procesu tady, který je možné implementovat pomocí různých nástrojů. Podrobnější popis projektu úlohy a role zapojené do životního cyklu procesu najdete v dalších odkazovaných tématech. K dispozici je také pokyny, jak implementovat TDSP pomocí konkrétní sadu nástrojů Microsoft a infrastrukturou, která můžeme použít k implementaci TDSP v našich týmů.

## <a name="key-components-of-the-tdsp"></a>Klíčové komponenty TDSP

TDSP se skládá z následujících klíčových komponent:

- A **životního cyklu datové vědy** definice
- A **standardizované struktura projektu**
- **Infrastrukturu a prostředky** pro projekty datových věd
- **Nástroje a pomůcky** pro spuštění projektu


## <a name="data-science-lifecycle"></a>Životního cyklu datové vědy

Zpracování vědy dat (TDSP) týmu poskytuje životní cyklus strukturovat vývoj vašich projektů datové vědy. Životní cyklus popisuje kroky, od začátku do konce, projekty obvykle postupujte při jejich spuštění.

Pokud použijete jiný životního cyklu datové vědy, například [OSTRÉ-DM](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining), [KDD](https://wikipedia.org/wiki/Data_mining#Process) nebo vaše organizace vlastní proces, můžete stále použít TDSP založené na úlohách v rámci těchto životního cyklu vývoje. Na vysoké úrovni tyto jiné metody mají mnoho společnou. 

Tento životní cyklus byly navržené pro projekty datových věd, které se dodávají jako součást inteligentní aplikace. Tyto aplikace nasadit modely strojového učení a umělé inteligence pro prediktivní analýzy. Projekty průzkumného testování datových věd nebo ad hoc analytics projekty mohou také těžit z pomocí tohoto procesu. Ale v takových případech některé z kroků popsaných nemusí být potřeba.    

Životní cyklus popisuje hlavní fáze, které jsou projekty obvykle spouštěny, často opakované:

* **Principy podniku**
* **Získávání a pochopení dat**
* **Modelování**
* **Nasazení**
* **Schválení zákazníkem**

Zde je vizuální znázornění **životní cyklus vědeckého zpracování týmových dat**. 

![TDSP Lifecycle2](./media/overview/tdsp-lifecycle2.png) 

Cíle, úkoly a artefakty dokumentaci pro každou fázi životního cyklu v TDSP jsou popsané [životní cyklus vědeckého zpracování týmových dat](lifecycle.md) tématu. Tyto úkoly a artefakty jsou spojeny s rolemi projektu:

- Architekt řešení
- Projektový manažer
- Odborník přes data
- Vedoucí projektu 

Následující diagram představuje zobrazení mřížky úloh (modře) a artefakty (zeleně), které jsou spojené s každou fázi životního cyklu (na vodorovné ose) pro tyto role (na svislé ose). 

[ ![TDSP-role a úlohy](./media/overview/tdsp-tasks-by-roles.png) ](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Struktura standardizované projektu

Všechny projekty, sdílet adresářovou strukturu a používat šablony pro dokumenty projektu s usnadňuje členům týmu vyhledat informace o jejich projektech. Všechny kódu a dokumenty jsou uloženy v systému správy verzí (VC), jako je Git, TFS nebo Subversion, umožňuje spolupráci mezi týmy. Sledování úloh a funkcí v projektu aplikace agile sledování systému jako Jira technologie Rally a Azure DevOps umožňuje blíže sledování kódu pro jednotlivé funkce. Tyto sledování také umožňuje týmům získat lepší odhady nákladů. TDSP doporučuje vytvoření oddělené úložiště pro každý projekt na virtuální Clustery pro správu verzí, zabezpečení informací a spolupráci. Standardizované strukturu pro všechny projekty pomáhá institucionální znalosti budovat celé organizace.

Poskytujeme šablony pro strukturu složek a požadované dokumenty ve standardním umístění. Tuto strukturu složek uspořádá soubory, které obsahují kód pro zkoumání dat a extrakci funkce a, který si poznamenejte modelu iterací. Tyto šablony usnadňují členové týmu pochopit práci ostatních nebo přidání nových členů do teams. Je snadné k zobrazování a aktualizace šablony dokumentů ve formátu markdown. Pomocí šablon můžete poskytnout klíčových otázek pro každý projekt – pomáhat zajistit, že problém je dobře definovaný a, že výsledky splňují kvality očekává kontrolní seznamy. Příklady obsahují:

- Projekt gossmanem dokumentovat obchodního problému a rozsah projektu
- sestavy dat dokumentu strukturu a statistiky nezpracovaných dat
- model sestavy do odvozené funkce dokumentů
- metriky výkonu modelu, jako je například křivky roc s více TŘÍDAMI nebo MSE


[ ![Adresáře TDSP](./media/overview/tdsp-dir-structure.png) ](./media/overview/tdsp-dir-structure.png#lightbox)

Struktura adresářů můžete naklonovat z [Githubu](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastrukturu a prostředky pro projekty datových věd  

TDSP poskytuje doporučení pro správu sdílené analýzy a infrastruktury úložiště, jako například:

- systémy souborů cloudu pro ukládání datových sad 
- databáze
- clustery velkých objemů dat (Hadoop nebo Spark) 
- Služba Machine learning 

Analýzy a úložné infrastruktuře může být v cloudu nebo místně. To je, kde jsou uloženy zpracovaných a nezpracovaných datové sady. Tato infrastruktura umožňuje reprodukovatelné analýzy. Také předejdete duplikace, což může vést k nekonzistencím a zbytečným nákladům na infrastrukturu. Nástroje jsou k dispozici zřízení sdílených prostředků, sledovat a povolit jednotlivým členům týmu se navázat zabezpečené připojení k těmto prostředkům. Je také vhodné mít členy projektu vytvořit konzistentní výpočetní prostředí. Různí členové týmu můžete replikovat a ověřit experimentů.

Tady je příklad sdílení různých komponent infrastruktury cloudové analýzy a práce v několika projektech týmu.

[ ![Infrastruktura TDSP](./media/overview/tdsp-analytics-infra.png) ](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Nástroje pro spuštění projektu

Úvod do procesů ve většině organizací je náročná. Nástroje poskytované implementace nápovědy data science procesu a životního cyklu nižší překážky a zvýšit konzistence jejich přijetí. TDSP obsahuje počáteční sadu nástrojů a skriptů k nastartování přijetí TDSP v rámci týmu. Pomáhá také automatizovat některé běžné úlohy v životního cyklu datové vědy například pro zkoumání a modelování směrného plánu. Není k dispozici pro jednotlivce přispívat sdílené nástroje a pomůcky do úložiště svého týmu sdíleným kódem dobře nastavené struktury. Tyto prostředky mohou pak využívat jiné projekty v rámci týmu nebo organizace. TDSP také v plánu povolit příspěvky nástroje celé komunitě. Nástroje TDSP můžete naklonovat z [Githubu](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Další postup

[Vědeckého zpracování týmových dat: Role a úlohy](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Toto téma popisuje role klíče pracovníky a jejich přidružených úloh pro datové vědy tým, který standardizuje o tomto procesu. 

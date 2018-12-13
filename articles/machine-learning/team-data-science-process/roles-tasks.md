---
title: Tým vědecké zpracování dat role a úlohy
description: Přehled klíčových komponent, pracovníci rolí a přidružené úlohy pro datové vědy týmový projekt.
author: marktab
manager: cgronlun
editor: cgronlun
services: machine-learning
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 09/04/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 051f80f16254c25853e476f66e8696423641073b
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53250786"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Tým vědecké zpracování dat role a úlohy

Vědecké zpracování týmových dat je architektura vyvinutý microsoftem, která poskytuje strukturované metodologie efektivně vytvářet prediktivní analytická řešení a inteligentní aplikace. Tento článek popisuje role klíče pracovníky a jejich přidružených úloh, které jsou zpracovávány pro datovou vědu týmu standardizují používáním tohoto procesu.

Tento úvod odkazy na kurzy, které poskytují pokyny o tom, jak nastavit prostředí TDSP pro skupiny celé datové vědy, datové vědy týmy a projekty.
Poskytuje podrobné pokyny k používání Azure DevOps v kurzech. Azure DevOps poskytuje hosting kódu platformy a nástroji pro agilní plánování, Správa týmových úloh, řízení přístupu a spravovat úložiště.

Tyto informace můžete použít k implementaci TDSP na vlastní hosting kódu a agilního plánování nástroje.

## <a name="structures-of-data-science-groups-and-teams"></a>Struktury data science skupiny a týmy

Data science funkce v podnicích může často uspořádány do následující hierarchie:

1. ***Data science skupiny/s***

2. ***Data science týmu/s v rámci skupiny/s***

Ve struktuře jsou skupiny a tým potenciálních zákazníků. Obvykle provádí projektu datové vědy data science tým, který může být tvořený zájemci o projekt (pro úlohy správy a zásad správného řízení projektu) a odborníky přes data nebo technici (jednotlivých přispěvatelů a technickým pracovníky) který se spustí pro datové vědy data a analýze částí projektu. Před provedením nastavení a zásady správného řízení se provádí skupiny, tým nebo zájemci o projekt.

## <a name="definition-of-four-tdsp-roles"></a>Definice rolí čtyři TDSP
Pomocí výše uvedených předpokladů existují čtyři různé role pro pracovníky týmu:

1. ***Správce skupiny***. Správce skupiny je jednotky celé datové vědy v podniku. Datové vědy jednotky může mít několik týmů, z nichž každý pracuje na více projekty datových věd jednotlivou obchodní vertikálně. Správce skupiny může delegovat úkoly na náhradu, ale neměňte úloh přidružených k roli.

2. ***Vedoucí týmu***. Vedoucí týmu je Správa týmu v jednotce data science podniku. Tým se skládá z několika odborníci přes data. Pro datové vědy jednotky s pouze malý počet odborníci přes data Group Manager a vedoucí týmu mohou být tatáž osoba.

3. ***Vedoucí projektu***. Vedoucí projektu slouží ke správě každodenních aktivit jednotlivé datové vědce v projektu konkrétní datové vědy.

4. ***Jednotlivý Přispěvatel projektu***. Mezi odborníky přes data, obchodní analytik, datových inženýrů, architekt, atd. Jednotlivý Přispěvatel projektu provede projektu datové vědy.


> [!NOTE]
> V závislosti na struktuře v podniku jedna osoba může přehrát více než jednu roli nebo může být více než jednou osobou, pracovní role. Často to může být případ v malé podniky nebo velké podniky s malý počet zaměstnanců v organizaci datové vědy.

## <a name="tasks-to-be-completed-by-four-personnel"></a>Dokončení čtyři pracovníci úloh

Následující obrázek znázorňuje úkoly nejvyšší úrovně pro pracovníky role přijmout a implementace vědecké zpracování týmových dat jako conceptualized společností Microsoft.

![Přehled role a úlohy](./media/roles-tasks/overview-tdsp-top-level.png)

Toto schéma a následující, podrobnější přehled úkolů, které jsou přiřazeny ke každé roli v TDSP by mohli vybrat příslušné kurz založený na vaše odpovědnosti v organizaci.

> [!NOTE]
> Následující pokyny ukazují postup, jak nastavit prostředí TDSP a dokončete jiných úloh datových věd v Azure DevOps. Můžeme určit způsob k provedení těchto úloh s Azure DevOps, protože to je, co se používá k implementaci TDSP v Microsoftu. Azure DevOps usnadňuje spolupráci díky integraci správy pracovních položek, které sledování úkolů a kód hostitelská služba používá ke sdílení nástrojů, uspořádání verzích a poskytují zabezpečení na základě rolí. Budete moct zvolit jiné platformy, pokud chcete provádět úkoly, ohraničená TDSP. Ale v závislosti na vaší platformě, nemusí být k dispozici některé funkce z Azure DevOps.
>
>Pokyny zde také použít [dat virtuálního počítače VĚDY](https://aka.ms/dsvm) na Azure v cloudu jako analytický desktop s několik oblíbených nástrojů pro datové vědy předem nakonfigurované a integrují různé software společnosti Microsoft a Azure služby. Datové VĚDY nebo jiné vývojové prostředí můžete použít k implementaci TDSP.


## <a name="group-manager-tasks"></a>Správce skupiny úloh

Správce skupiny (nebo určený správce systému TDSP) přijmout TDSP jsou prováděny následující úlohy:

- Vytvoření **skupinový účet** na kód hostování platformy (jako jsou GitHub, Git, Azure DevOps nebo jiné)
- Vytvoření **úložiště šablon projektu** na účet skupiny a z úložiště šablon projektu vyvinutý týmem Microsoft TDSP počáteční hodnoty. Úložiště šablon projekt TDSP od Microsoftu
    - poskytuje **standardizované adresářovou strukturu** adresáře pro data, kód a dokumenty, včetně
    - poskytuje sadu **standardizované šablony dokumentů** vodítko vědecké zpracování dat efektivní.
- Vytvoření **nástroj úložiště**a naplnit z úložiště nástroj vyvinutý týmem Microsoft TDSP. Poskytuje úložiště nástroje TDSP od Microsoftu
    - Sada užitečné nástroje provádět práci mezi odborníky přes data mnohem efektivnější, včetně nástrojů pro interaktivní zkoumání dat, analýzu a generování sestav a pro směrný plán, modelování a vytváření sestav.
- Nastavit **zásada zabezpečení řízení** z těchto dvou úložišť na vašem účtu skupiny.

Podrobné pokyny krok za krokem, najdete v článku [úkoly správce skupiny pro datové vědy tým](group-manager-tasks.md).


## <a name="team-lead-tasks"></a>Úkoly vedoucí týmu

Vedoucí týmu (nebo správce projektu určené) přijmout TDSP jsou prováděny následující úlohy:

- Pokud Azure DevOps je vybrán jako kód hostitelskou platformu pro správu verzí a spolupráci, vytvořte **projektu** na skupiny Azure DevOps služby. V opačném případě můžete přeskočit tuto úlohu.
- Vytvořte **úložiště šablon projektu** v rámci projektu a počáteční hodnoty z úložiště šablon projektu skupiny nastavení skupiny správce nebo delegovaného správce.
- Vytvořte **úložiště týmu nástroje**a přidat do úložiště nástroje specifické pro tým.
- (Volitelné) Vytvoření **[Azure file storage](https://azure.microsoft.com/services/storage/files/)** se použije k uložení datových assetů, které může být užitečné pro celý tým. Ostatní členové týmu můžou připojit toto cloudové sdílené úložiště souborů na jejich ploše analytics.
- (Volitelné) Připojení Azure file storage pro **virtuální počítač pro datové vědy** (DSVM) týmu způsobit a přidejte datové assety v něm.
- Nastavit **řízení zabezpečení** tím, že přidání členů týmu a konfigurovat jejich oprávnění.

Podrobné pokyny krok za krokem, najdete v článku [vedoucí týmu úkoly pro datové vědy tým](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Úkoly vedoucí projektu

V projektu vést přijmout TDSP jsou prováděny následující úlohy:

- Vytvoření **úložiště projektu** v rámci projektu a naplnit z úložiště šablon projektu.
- (Volitelné) Vytvoření **Azure file storage** se použije k uložení datových assetů z projektu.
- (Volitelné) Připojení Azure file storage pro **virtuální počítač pro datové vědy** (DSVM) projektu vést a přidat projekt datové assety v něm.
- Nastavit **řízení zabezpečení** tím, že přidání členů projektu a konfigurovat jejich oprávnění.

Podrobné pokyny krok za krokem, najdete v článku [vedoucí projektu úlohy pro datové vědy tým](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Úkoly jednotlivý Přispěvatel projektu

Podle jednotlivých přispěvatelem projektu (obvykle vědec) provádět projekt vědeckého zpracování dat pomocí TDSP jsou prováděny následující úlohy:

- Klonování **úložiště projektu** zřízený vedoucí projektu.
- (Volitelné) Připojení sdílené **Azure file storage** týmu a projektů na jejich **virtuální počítač pro datové vědy** (DSVM).
- Spusťte projekt.


Podrobné pokyny k zavádění na projekt, naleznete v tématu [jednotlivých přispěvatelů projektu pro datové vědy tým](project-ic-tasks.md).


## <a name="data-science-project-execution"></a>Spuštění projektu datové vědy

Pomocí následujících relevantní sadu pokynů, můžete vytvořit pracovní položky ke sledování všech úloh a fází, které potřebuje projekt od jeho začátku na konec odborníci přes data, vedoucí projektu a týmů. Pomocí gitu také podporuje spolupráci mezi odborníky přes data a zajistí, že budou artefakty vytvořené během spuštění projektu verzi řízen a sdílí všichni členové projektu.

Pokynů pro spuštění projektu byly vyvinuty v závislosti na předpokladu, že pracovní položky a projektu git, které úložiště využívají Azure DevOps. Použití Azure DevOps pro obě umožňuje propojit pracovní položky s větvemi Git z úložišť vašeho projektu. Tímto způsobem můžete snadno sledovat, co byla provedena pro pracovní položku.

Následující obrázek popisuje, jak tento pracovní postup pro použití TDSP spuštění projektu.

![Spuštění projektu typické datové vědy](./media/roles-tasks/overview-project-execute.png)

Pracovní postup obsahuje kroky, které mohou být seskupeny do tří činností:

- (Vedoucí projektu) plánování sprintů
- Vývoj artefakty na větví systému git vyřešit pracovní položky (mezi odborníky přes Data)
- Revize kódu a slučování větví s hlavní větví (vedoucí projektu nebo ostatní členové týmu)

Podrobné pokyny v pracovním postupu spuštění projektu, naleznete v tématu [provádění projekty datových věd](project-execution.md).

## <a name="project-structure"></a>Struktura projektu

Použijte tento [úložiště šablon projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate) k podpoře project efektivní provádění a spolupráci. Toto úložiště poskytuje standardizované directory strukturu a dokumentu šablony, které můžete použít pro svůj projekt TDSP.

## <a name="next-steps"></a>Další postup

Prozkoumejte podrobnější popis role a úlohy určené vědecké zpracování týmových dat:

- [Úlohy správce skupiny pro datové vědy tým](group-manager-tasks.md)
- [Úkoly vedoucí týmu pro datové vědy tým](team-lead-tasks.md)
- [Vedoucí úkoly pro datové vědy tým projektu](project-lead-tasks.md)
- [Jednotlivé přispěvatelé projektu týmu datové vědy](project-ic-tasks.md)

---
title: Role a úkoly procesu vědeckého procesu týmových dat
description: Přehled klíčových součástí, rolí personálu a přidružených úkolů pro skupinu datových věd.
author: marktab
manager: marktab
editor: marktab
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c1ed731943abf0efdd99ea54d2318fa402835e08
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720006"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Role a úkoly procesu vědeckého procesu týmových dat

Team Data Science Process (TDSP) je framework vyvinutý společností Microsoft, který poskytuje strukturovanou metodiku pro efektivní vytváření prediktivních analytických řešení a inteligentních aplikací. Tento článek popisuje klíčové role personálu a přidružené úkoly pro tým datové vědy standardizující tento proces.

Tento úvodní článek odkazuje na návody na nastavení prostředí TDSP. Kurzy poskytují podrobné pokyny pro použití projektů Azure DevOps, úložišť Azure Repos a Azure Boards.  Motivujícím cílem je přechod od konceptu přes modelování a do nasazení.

Kurzy používají Azure DevOps, protože to je, jak implementovat TDSP v Microsoftu. Azure DevOps usnadňuje spolupráci integrací zabezpečení na základě rolí, správy pracovních položek a sledování a hostování kódu, sdílení a správy zdrojového kódu. Kurzy také používají [virtuální počítač](https://aka.ms/dsvm) Azure Data Science (DSVM) jako pracovní plochu analytics, která má několik oblíbených nástrojů pro datové vědy předem nakonfigurovaných a integrovaných se softwarem Microsoftu a službami Azure. 

Kurzy můžete použít k implementaci TDSP pomocí jiných kódhosting, agilní plánování a vývojové nástroje a prostředí, ale některé funkce nemusí být k dispozici.

## <a name="structure-of-data-science-groups-and-teams"></a>Struktura skupin a týmů datových věd

Funkce datové vědy v podnicích jsou často uspořádány v následující hierarchii:

- Skupina datových věd
  - Tým pro datové vědy v rámci skupiny

V takové struktuře jsou skupinové zájemce a týmové zájemce. Projekt datové vědy se obvykle provádí týmem datové vědy. Týmy pro datové vědy mají vedoucí projektů pro projektové řízení a úkoly řízení a jednotlivé datové vědce a inženýry, kteří provádějí části projektu pro datové vědy a datové inženýrství. Počáteční nastavení projektu a zásadsprávné hodlají skupina, tým nebo zájemci projektu.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definice a úkoly pro čtyři role TDSP
S předpokladem, že jednotka datové vědy se skládá z týmů v rámci skupiny, existují čtyři odlišné role pro pracovníky TDSP:

1. **Správce skupiny**: Spravuje celou jednotku datové vědy v rozlehlé síti. Jednotka datové vědy může mít více týmů, z nichž každý pracuje na více projektech datové vědy v různých obchodních vertikálách. Správce skupiny může delegovat své úkoly na náhradníka, ale úkoly spojené s rolí se nemění.
   
2. **Vedoucí týmu**: Spravuje tým v jednotce datové vědy podniku. Tým se skládá z více datových vědců. Pro malé jednotky datové vědy může být správce skupiny a vedoucí týmu stejná osoba.
   
3. **Vedoucí projektu**: Řídí každodenní aktivity jednotlivých datových vědců na konkrétním projektu datové vědy.
   
4. **Jednotliví přispěvatelé projektu**: Datoví vědci, obchodní analytici, datoví inženýři, architekti a další, kteří realizují projekt datové vědy.

> [!NOTE]
> V závislosti na struktuře a velikosti podniku může jedna osoba hrát více než jednu roli nebo více než jedna osoba může tuto roli obsadit.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Úkoly, které mají být dokončeny čtyřmi rolemi

Následující diagram znázorňuje úkoly nejvyšší úrovně pro každou roli procesu vědecké ho procesu týmových dat. Toto schéma a následující, podrobnější přehled úkolů pro každou roli TDSP vám může pomoci vybrat kurz, který potřebujete na základě vašich povinností.

![Přehled rolí a úkolů](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Úkoly správce skupiny

Správce skupiny nebo určený správce systému TDSP dokončí následující úkoly, aby přijal tdsp:

- Vytvoří **organizaci** Azure DevOps a skupinový projekt v rámci organizace. 
- Vytvoří **úložiště šablon projektu** v projektu skupiny Azure DevOps a semínky z úložiště šablony projektu vyvinuté týmem Microsoft TDSP. Úložiště šablon projektu Microsoft TDSP poskytuje:
  - **Standardizovaná adresářová struktura**, včetně adresářů pro data, kód a dokumenty.
  - Sada **standardizovaných šablon dokumentů, které** řídí efektivní proces datové vědy.
- Vytvoří **úložiště nástrojů**a vytvoří jeho ostřikované úložiště z úložiště nástrojů vyvinutého týmem Microsoft TDSP. Úložiště nástrojů TDSP od společnosti Microsoft poskytuje sadu užitečných nástrojů, které zefektivninují práci datového vědce. Úložiště nástrojů společnosti Microsoft obsahuje nástroje pro interaktivní zkoumání dat, analýzu, vytváření sestav a základní modelování a vytváření sestav.
- Nastaví **zásady řízení zabezpečení** pro účet organizace.

Podrobné pokyny naleznete v tématu [Úlohy správce skupiny pro tým datových věd](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Úkoly vedoucího týmu

Vedoucí týmu nebo určený správce projektu dokončí následující úkoly, aby přijal tdsp:

- Vytvoří týmový **projekt** v organizaci Azure DevOps ve skupině.
- Vytvoří **úložiště šablony projektu** v projektu a zasadí ho z úložiště šablony projektu skupiny nastavené správcem skupiny nebo delegátem.
- Vytvoří **úložiště nástrojů týmu**, semínky z úložiště nástrojů skupiny a přidá nástroje specifické pro tým do úložiště.
- Volitelně vytvoří [úložiště souborů Azure](https://azure.microsoft.com/services/storage/files/) pro ukládání užitečných datových prostředků pro tým. Ostatní členové týmu mohou připojit toto sdílené úložiště souborů cloud na svých analytických plochách.
- Volitelně připojí úložiště souborů Azure na **dsvm** týmu a přidá k němu datové prostředky týmu.
- Nastaví **ovládací prvek zabezpečení** přidáním členů týmu a konfigurací jejich oprávnění.

Podrobné pokyny naleznete v [tématu Team Lead úkoly pro tým datové vědy](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Úkoly zájemce o projekt

Vedoucí projektu dokončí následující úkoly, aby přijal tdsp:

- Vytvoří **úložiště projektu** v týmovém projektu a začlení jej z úložiště šablony projektu.
- Volitelně vytvoří **úložiště souborů Azure** pro uložení datových prostředků projektu.
- Volitelně připojí úložiště souborů Azure k **DSVM** a přidá do něj datové prostředky projektu.
- Nastaví **ovládací prvek zabezpečení** přidáním členů projektu a konfigurací jejich oprávnění.

Podrobné pokyny naleznete v [tématu Projekt vedoucí úkoly pro tým datové vědy](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Úkoly jednotlivého přispěvatele projektu

Individuální přispěvatel projektu, obvykle datový vědec, provádí pomocí tdsp následující úkoly:

- Klonuje **úložiště projektu** nastavené vedoucím projektu.
- Volitelně připojí sdílený tým a projekt **úložiště souborů Azure** na jejich virtuální **medailon pro datové vědy** (DSVM).
- Provede projekt.

Podrobné pokyny pro připojení k projektu najdete v [tématu Úkoly jednotlivých přispěvatelů projektu pro tým pro datové vědy](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Pracovní postup provádění projektu datové vědy

Podle následujících příslušných kurzů mohou datoví vědci, vedoucí projektu a vedoucí týmu vytvářet pracovní položky pro sledování všech úkolů a fází projektu od začátku do konce. Pomocí Azure Repos podporuje spolupráci mezi datovými vědci a zajišťuje, že artefakty generované během provádění projektu jsou řízeny verzí a sdíleny všemi členy projektu. Azure DevOps umožňuje propojit pracovní položky Azure Boards s větvemi úložiště Azure Repos a snadno sledovat, co se stalo pro pracovní položku.

Následující obrázek popisuje pracovní postup TDSP pro provádění projektu:

![Typický pracovní postup projektu datové vědy](./media/roles-tasks/overview-project-execute.png)

Kroky pracovního postupu lze seskupit do tří aktivit:

- Vedoucí projektu provádějí plánování sprintu
- Data Vědci vyvíjejí artefakty na `git` větvích k řešení pracovních položek
- Vedoucí projektu nebo jiní členové týmu provedou revize kódu a sloučí pracovní větve do hlavní větve

Podrobné pokyny k pracovnímu postupu provádění projektů naleznete [v tématu Agile development of data science projects](agile-development.md).

## <a name="tdsp-project-template-repository"></a>Úložiště šablon projektu TDSP

Pomocí [úložiště šablon projektu](https://github.com/Azure/Azure-TDSP-ProjectTemplate) týmu Microsoft TDSP můžete podporovat efektivní provádění projektů a spolupráci. Úložiště poskytuje standardizovanou adresářovou strukturu a šablony dokumentů, které můžete použít pro vlastní projekty TDSP.

## <a name="next-steps"></a>Další kroky

Prozkoumejte podrobnější popisrolí a úkolů definovaných procesem vědecké vědy o týmových datech:

- [Úlohy správce skupiny pro tým datových věd](group-manager-tasks.md)
- [Úkoly vedoucího týmu pro tým datové vědy](team-lead-tasks.md)
- [Úkoly vedoucího projektu pro tým datové vědy](project-lead-tasks.md)
- [Projekt ové úkoly jednotlivých přispěvatelů pro tým pro datové vědy](project-ic-tasks.md)

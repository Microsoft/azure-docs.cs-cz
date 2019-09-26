---
title: Tým vědecké zpracování dat role a úlohy
description: Osnova klíčových komponent, rolí personálu a přidružených úloh pro skupinu pro datové vědy.
author: marktab
manager: cgronlun
editor: cgronlun
services: machine-learning
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 09/12/2019
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bbc066f4b01a01a589849ad524b58a9b5d9dfae1
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260656"
---
# <a name="team-data-science-process-roles-and-tasks"></a>Tým vědecké zpracování dat role a úlohy

Vědecké zpracování týmových dat (TDSP) je architektura vyvinutá Microsoftem, která poskytuje strukturovanou metodologii pro efektivní vytváření řešení prediktivní analýzy a inteligentních aplikací. Tento článek popisuje klíčové role personálu a související úlohy pro tým datových věd, který se v tomto procesu standardizace.

Tento úvodní článek obsahuje odkazy na kurzy týkající se nastavení prostředí TDSP pro celou skupinu pro datové vědy, týmy pro datové vědy a projekty TDSP. Kurzy poskytují podrobné pokyny pro použití Azure DevOps Projects, Azure Repos úložišť a Azure Boards nástrojů pro agilní plánování pro řízení přístupu, hostování a sdílení kódu a správu týmových úloh.

Kurzy využívají Azure DevOps, protože to je způsob implementace TDSP v Microsoftu. Azure DevOps usnadňuje spolupráci díky integraci zabezpečení založeného na rolích, správy a sledování pracovních položek a hostování kódu, sdílení a správy zdrojového kódu. Kurzy také používají [Data Science Virtual Machine](https://aka.ms/dsvm) Azure (DSVM) jako Desktop Analytics, který obsahuje několik oblíbených nástrojů pro datové vědy, které jsou předem nakonfigurované a integrované se softwarem Microsoftu a službami Azure. 

Můžete použít kurzy k implementaci TDSP pomocí jiných nástrojů pro hostování kódu, agilního plánování a vývojářských nástrojů a prostředí, ale některé funkce nemusí být k dispozici.

## <a name="structure-of-data-science-groups-and-teams"></a>Struktura skupin a týmů pro datové vědy

Funkce pro datové vědy v podnicích jsou často organizovány v následující hierarchii:

- Skupina pro datové vědy
  - Tým pro datové vědy/s v rámci skupiny

V takové struktuře jsou k dispozici skupiny vedoucí a vedoucí týmu. Projekt pro datové vědy se obvykle provádí týmem pro datové vědy. Týmy pro datové vědy mají vedoucí projektu na řízení projektů a úkoly zásad správného řízení a jednotlivé odborníky na data a inženýrské specialisty na zpracování datových věd a inženýrů dat v projektu. Úvodní nastavení projektu a zásady správného řízení provádí vedoucí skupiny, týmu nebo projektu.

## <a name="definition-and-tasks-for-the-four-tdsp-roles"></a>Definice a úkoly pro čtyři role TDSP
S předpokladem, že se jednotka pro datové vědy skládá z týmů v rámci skupiny, existují čtyři samostatné role pro zaměstnance TDSP:

1. **Správce skupin**: Spravuje celou datovou jednotku v podniku. Datové vědy jednotky může mít několik týmů, z nichž každý pracuje na více projekty datových věd jednotlivou obchodní vertikálně. Správce skupiny může delegovat úkoly na náhradu, ale neměňte úloh přidružených k roli.
   
2. **Vedoucí týmu**: Spravuje tým v jednotce datové vědy podniku. Tým se skládá z několika odborníci přes data. V případě malých jednotek pro datové vědy může být správce skupin a vedoucí týmu stejnou osobou.
   
3. **Vedoucí projektu**: Spravuje každodenní činnosti jednotlivých vědeckých pracovníků dat v konkrétním projektu pro datové vědy.
   
4. **Jednotliví přispěvatelé projektu**: Odborníci na data, obchodní analytiky, technici na data, architekti a další, kteří provádějí projekt pro datové vědy.

> [!NOTE]
> V závislosti na struktuře a velikosti podniku může jedna osoba hrát více než jednu roli nebo může vyplnit roli více než jedna osoba.

### <a name="tasks-to-be-completed-by-the-four-roles"></a>Úkoly, které mají být dokončeny čtyřmi rolemi

Následující diagram znázorňuje úkoly nejvyšší úrovně pro každou roli vědeckého procesu týmového zpracování dat. Toto schéma a následující, podrobnější přehled úloh pro každou roli TDSP vám pomůžou vybrat kurz, který potřebujete, podle svých povinností.

![Přehled role a úlohy](./media/roles-tasks/overview-tdsp-top-level.png)

## <a name="group-manager-tasks"></a>Správce skupiny úloh

Správce skupin nebo určený správce systému TDSP dokončí následující úkoly pro přijetí TDSP:

- Vytvoří **organizaci** Azure DevOps a projekt skupiny v rámci organizace. 
- Vytvoří **úložiště šablony projektu** v projektu skupiny Azure DevOps a vysemení ho z úložiště šablony projektu vyvinuté týmem Microsoft TDSP. Úložiště šablon projektu Microsoft TDSP poskytuje:
  - **Standardizovaná adresářová struktura**, včetně adresářů pro data, kód a dokumenty.
  - Sada **standardizovaných šablon dokumentů** , které vám pomohou zefektivnit proces vědeckého zpracování dat.
- Vytvoří **úložiště nástrojů**a vysemení ho z úložiště nástrojů vyvinutého týmem Microsoft TDSPe. Úložiště nástrojů TDSP od Microsoftu poskytuje sadu užitečných nástrojů, které zajistí efektivnější práci s daty. Úložiště nástrojů společnosti Microsoft obsahuje nástroje pro interaktivní zkoumání dat, analýzu, generování sestav a modelování a vytváření sestav podle směrného plánu.
- Nastaví **zásady řízení zabezpečení** pro účet organizace.

Podrobné pokyny najdete v tématu [úlohy správce skupin pro tým pro datové vědy](group-manager-tasks.md).

## <a name="team-lead-tasks"></a>Úkoly vedoucí týmu

Vedoucí týmu nebo určený správce projektu dokončí následující úkoly pro přijetí TDSP:

- Vytvoří týmový **projekt** v organizaci Azure DevOps ve skupině.
- Vytvoří v projektu **úložiště šablon projektů** a vysemení ho z úložiště šablony projektu skupiny, které je nastaveno správcem skupiny nebo delegátem.
- Vytvoří **úložiště nástroje týmu**, vysemení ho z úložiště nástrojů skupiny a přidá nástroje pro konkrétní tým do úložiště.
- Volitelně vytvoří službu [Azure File Storage](https://azure.microsoft.com/services/storage/files/) pro ukládání užitečných datových assetů pro tým. Ostatní členové týmu můžou připojit toto cloudové sdílené úložiště souborů na jejich ploše analytics.
- Volitelně připojí službu Azure File Storage na **DSVM** týmu a přidá do ní datové prostředky týmu.
- Nastaví **řízení zabezpečení** přidáním členů týmu a konfigurací jejich oprávnění.

Podrobné pokyny najdete v tématu [úkoly vedoucí týmu pro tým pro datové vědy](team-lead-tasks.md).


## <a name="project-lead-tasks"></a>Úkoly vedoucí projektu

Vedoucí projektu dokončí následující úkoly pro přijetí TDSP:

- Vytvoří v týmovém projektu **úložiště projektu** a vysemení ho z úložiště šablony projektu.
- Volitelně vytvoří službu **Azure File Storage** pro uložení datových assetů projektu.
- Volitelně připojí službu Azure File Storage k **DSVM** a přidá do ní assety dat projektu.
- Nastaví **řízení zabezpečení** přidáním členů projektu a konfigurací jejich oprávnění.

Podrobné pokyny najdete v tématu [úkoly vedoucích týmu pro datové vědy](project-lead-tasks.md).

## <a name="project-individual-contributor-tasks"></a>Úkoly jednotlivý Přispěvatel projektu

Jednotliví Přispěvatel projektu, obvykle datový vědecký, provádí následující úlohy pomocí TDSP:

- Naklonuje **úložiště projektu** nastavené vedoucím projektu.
- Volitelně připojí sdílený tým a službu **Azure File Storage** na jejich **Data Science Virtual Machine** (DSVM).
- Spustí projekt.

Podrobné pokyny pro připojování k projektu naleznete v tématu [Project pro jednotlivé úkoly přispěvatelů pro tým pro datové vědy](project-ic-tasks.md).

## <a name="data-science-project-execution-workflow"></a>Pracovní postup spuštění projektu pro datové vědy

Podle příslušných kurzů, vědeckých pracovníků, vedoucích projektů a vedoucí týmu mohou vytvářet pracovní položky pro sledování všech úkolů a fází pro projekt od začátku do konce. Použití Azure Repos propaguje spolupráci mezi odborníky přes data a zajišťuje, aby artefakty vygenerované během provádění projektu byly řízené verzí a sdíleny všemi členy projektu. Azure DevOps umožňuje propojit pracovní položky Azure Boards s vašimi větvemi úložiště Azure Repos a snadno sledovat, co bylo provedeno pro pracovní položku.

Následující obrázek popisuje pracovní postup TDSP pro provedení projektu:

![Pracovní postup typického projektu pro datové vědy](./media/roles-tasks/overview-project-execute.png)

Kroky pracovního postupu můžete seskupit do tří aktivit:

- Vedoucí projektu provádějí plánování sprintů
- Vědečtí data vyvíjejí artefakty `git` na větvích, které řeší pracovní položky.
- Vedoucí projektu nebo jiní členové týmu dělají revize kódu a sloučí pracovní větve do hlavní větve.

Podrobné pokyny k pracovnímu postupu provádění projektu naleznete v tématu [agilní vývoj projektů pro datové vědy](agile-development.md).

## <a name="tdsp-project-template-repository"></a>Úložiště šablon projektů TDSP

Použijte [úložiště šablon projektů](https://github.com/Azure/Azure-TDSP-ProjectTemplate) v týmu Microsoft TDSP pro podporu efektivního spouštění a spolupráce projektů. Úložiště poskytuje standardizovanou adresářovou strukturu a šablony dokumentů, které můžete použít pro vlastní projekty TDSP.

## <a name="next-steps"></a>Další kroky

Prozkoumejte podrobnější popis role a úlohy určené vědecké zpracování týmových dat:

- [Úlohy správce skupiny pro datové vědy tým](group-manager-tasks.md)
- [Úkoly vedoucí týmu pro datové vědy tým](team-lead-tasks.md)
- [Vedoucí úkoly pro datové vědy tým projektu](project-lead-tasks.md)
- [Jednotlivé úkoly přispěvatele projektu pro tým pro datové vědy](project-ic-tasks.md)

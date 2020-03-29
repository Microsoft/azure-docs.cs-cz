---
title: Agilní vývoj projektů datové vědy - Team Data Science Process
description: Spusťte projekt datové vědy systematicky, řízenou verzí a spolupracujícím způsobem v rámci projektového týmu pomocí procesu vědecké ho procesu týmových dat.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: c097c14406349d973e905fadb806cc159d9b16d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722097"
---
# <a name="agile-development-of-data-science-projects"></a>Agilní vývoj projektů datové vědy

Tento dokument popisuje, jak mohou vývojáři provádět projekt datové vědy systematicky, řízenou verzí a spolupráci v rámci projektového týmu pomocí [procesu vědecké vědy o týmových datech](overview.md) (TDSP). TDSP je framework vyvinutý společností Microsoft, který poskytuje strukturovanou posloupnost aktivit pro efektivní provádění cloudových prediktivních analytických řešení. Přehled rolí a úkolů, které zpracovává tým pro datové vědy standardizující na TDSP, najdete v [tématu Role a úkoly procesu vědeckého zpracování týmových dat](roles-tasks.md). 

Tento článek obsahuje pokyny, jak: 

- Plánování *sprintu* pro pracovní položky zapojené do projektu.
- Přidejte *pracovní položky* do sprintů.
- Vytvořte a používejte *šablonu pracovní položky odvozenou agilní,* která je specificky zarovnána s fázemi životního cyklu TDSP.

Následující pokyny popisují kroky potřebné k nastavení týmového prostředí TDSP pomocí Azure Boards a Azure Repos v Azure DevOps. Pokyny používají Azure DevOps, protože to je, jak implementovat TDSP v Microsoftu. Pokud vaše skupina používá jinou platformu pro hostování kódu, úkoly vedoucího týmu se obvykle nemění, ale způsob dokončení úkolů se liší. Například propojení pracovní položky s větev Git nemusí být stejné s GitHub, jako je to s Azure Repos.

Následující obrázek znázorňuje typický pracovní postup plánování sprintu, kódování a řízení zdrojového kódu pro projekt datové vědy:

![Vědecké zpracování týmových dat](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Typy pracovních položek

V rámci plánování sprintu TDSP existují čtyři často používané typy *pracovních položek:* *Funkce*, *Příběhy uživatelů*, *Úkoly*a *Chyby*. Nevyřízené položky pro všechny pracovní položky je na úrovni projektu, nikoli na úrovni úložiště Git. 

Zde jsou definice pro typy pracovních položek:

- **Funkce**: Funkce odpovídá zapojení projektu. Různé vztahy s klientem jsou různé funkce a je nejlepší zvážit různé fáze projektu jako různé funkce. Pokud zvolíte schéma, jako * \<je ClientName\<>- EngagementName>* pojmenovat své funkce, můžete snadno rozpoznat kontext projektu a zapojení z názvů samotných.
  
- **Příběh uživatele**: Scénáře uživatelů jsou pracovní položky potřebné k dokončení funkce end-to-end. Příklady uživatelských scénářů:
  - Získání dat 
  - Zkoumání dat 
  - Generovat funkce
  - Sestavení modelů
  - Zprovoznění modelů 
  - Přeučování modelů
  
- **Úkol**: Úkoly jsou přiřaditelné pracovní položky, které je třeba provést k dokončení konkrétního uživatelského scénáře. Například úkoly v příběhu uživatele *získat data* může být:
  - Získání pověření serveru SQL Server
  - Nahrání dat do datového skladu SQL
  
- **Chyba**: Chyby jsou problémy v existujícím kódu nebo dokumentech, které musí být opraveny k dokončení úkolu. Pokud chyby jsou způsobeny chybějící pracovní položky, mohou eskalovat být příběhy uživatelů nebo úkoly. 

Vědci s daty se mohou cítit pohodlněji pomocí agilní šablony, která nahrazuje funkce, příběhy uživatelů a úkoly fázemi a dílčími fázemi životního cyklu TDSP. Chcete-li vytvořit agilní odvozenou šablonu, která je specificky zarovnána s fázemi životního cyklu TDSP, přečtěte si informace [o použití agilní šablony práce TDSP](#set-up-agile-dsp-6).

> [!NOTE]
> TDSP si vypůjčuje koncepty funkcí, uživatelských scénářů, úkolů a chyb ze správy kódu softwaru (SCM). Koncepty TDSP se mohou mírně lišit od svých konvenčních definic SCM.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Plánování sprintů

Mnoho datových vědců se zabývá několika projekty, jejichž dokončení a pokračování v různých krocích může trvat měsíce. Plánování sprintu je užitečné pro stanovení priorit projektu a plánování a přidělování zdrojů. V Azure Boards můžete snadno vytvářet, spravovat a sledovat pracovní položky pro vaše projekty a provádět plánování sprintu, abyste zajistili, že projekty budou postupovat podle očekávání.

Další informace o plánování sprintu naleznete v [tématu Sprinty Scrum](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint). 

Další informace o plánování sprintu v Azure Boards najdete v tématu [Přiřazení nevyřízených položek sprintu](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Přidání funkce do nevyřízených položek 

Po vytvoření úložiště kódu projektu a projektu můžete přidat funkci do nevyřízených položek, která bude představovat práci pro váš projekt.

1. Na stránce projektu vyberte v levém navigačním panelu**nevyřízené položky vývěsky.** **Boards** >  
   
1. Na kartě **Nevyřízené položky,** pokud typ pracovní položky v horním panelu je **příběhy**, rozbalte a vyberte **funkce**. Pak vyberte **Nová pracovní položka.**
   
   ![Vybrat novou pracovní položku](./media/agile-development/2-sprint-team-overview.png)
   
1. Zadejte název funkce, obvykle název projektu, a pak vyberte **Přidat nahoru**. 
   
   ![Zadejte název a vyberte Přidat nahoru.](./media/agile-development/3-sprint-team-add-work.png)
   
1. Ze seznamu **Nevyřízené položky** vyberte a otevřete novou funkci. Vyplňte popis, přiřaďte člena týmu a nastavte parametry plánování. 
   
   Funkci můžete také propojit s úložištěm kódu Azure Repos projektu výběrem **odkazu Přidat** v části **Vývoj.** 
   
   Po úpravě funkce vyberte **Uložit & Zavřít**.
   
   ![Upravit funkci a vybrat Uložit & Zavřít.](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Přidání uživatelského článku do funkce 

V části Funkce můžete přidat příběhy uživatelů, které popisují hlavní kroky potřebné k dokončení projektu. 

Přidání nového uživatelského scénáře do funkce:

1. Na kartě **Nevyřízené položky** **+** vyberte nalevo od funkce. 
   
   ![Přidání nového uživatelského článku pod funkci](./media/agile-development/4-sprint-add-story.png)
   
1. Podejte příběhuživatele název a upravte podrobnosti, jako je přiřazení, stav, popis, komentáře, plánování a priorita. 
   
   Příběh uživatele můžete také propojit s pobočkou úložiště kódu Azure Repos projektu výběrem **přidat odkaz** v části **Vývoj.** Vyberte úložiště a větev, se kterou chcete propojit pracovní položku, a pak vyberte **OK**.
   
   ![Přidat odkaz](./media/agile-development/5-sprint-edit-story.png)
   
1. Po dokončení úprav uživatelského scénáře vyberte **Uložit & Zavřít**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Přidání úkolu do příběhu uživatele 

Úkoly jsou konkrétní podrobné kroky, které jsou potřebné k dokončení každého příběhu uživatele. Po dokončení všech úkolů příběhu uživatele by měl být dokončen také uživatelský scénář. 

Chcete-li přidat úkol do příběhu uživatele, vyberte **+** vedle položky Příběh uživatele a vyberte **Úkol**. Vyplňte název a další informace v úkolu.

![Přidání úkolu do příběhu uživatele](./media/agile-development/7-sprint-add-task.png)

Po vytvoření funkcí, uživatelských scénářů a úkolů je můžete zobrazit v zobrazení **nevyřízené položky** nebo **vývěsky** a sledovat jejich stav.

![Zobrazení nevyřízených položek](./media/agile-development/8-sprint-backlog-view.png)

![Zobrazení vývěsky](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Použití agilní šablony práce TDSP

Vědci s daty se mohou cítit pohodlněji pomocí agilní šablony, která nahrazuje funkce, příběhy uživatelů a úkoly fázemi a dílčími fázemi životního cyklu TDSP. V Azure Boards můžete vytvořit šablonu odvozenou od agilní, která používá fáze životního cyklu TDSP k vytváření a sledování pracovních položek. Následující kroky projděte nastavení šablony agilního procesu specifické pro datovou vědu a vytváření pracovních položek datové vědy na základě šablony.

### <a name="set-up-an-agile-data-science-process-template"></a>Nastavení šablony agilního procesu vědeckého zpracování dat

1. Na hlavní stránce organizace Azure DevOps vyberte **nastavení organizace** v levé navigaci. 
   
1. V levém **navigačním panelu organizace** vyberte v části **Vývěsky** **položku Proces**. 
   
1. V podokně **Všechny procesy** vyberte **...** vedle **Agile**a pak vyberte **Vytvořit zděděný proces**.
   
   ![Vytvoření zděděného procesu z agilního procesu](./media/agile-development/10-settings.png) 
   
1. V dialogovém **okně Vytvořit zděděný proces z agilního** zadejte název *AgileDataScienceProcess*a vyberte **Vytvořit proces**.
   
   ![Vytvořit proces AgileDataScienceProcess](./media/agile-development/11-agileds.png)
   
1. Ve **všech procesech**vyberte nový **agileDataScienceProcess**. 
   
1. Na kartě **Typy pracovních položek** zakažte **epic**, **feature**, **user story**a **task** výběrem **...** vedle každé položky a výběrem **příkazu Zakázat**. 
   
   ![Zakázat typy pracovních položek](./media/agile-development/12-disable.png)
   
1. Ve **všech procesech**vyberte kartu **Úrovně nevyřízených položek.** V části **Nevyřízené položky portfolia**vyberte **...** vedle **Epic (zakázáno)** a pak vyberte **Upravit/Přejmenovat**. 
   
1. V dialogovém okně **Upravit úroveň nevyřízených položek:**
   1. V **části Název**nahraďte **Epic** *projekty TDSP*. 
   1. V části **Typy pracovních položek na této úrovni nevyřízených položek**vyberte **Nový typ pracovní položky**, zadejte *projekt TDSP*a vyberte **Přidat**. 
   1. V části **Výchozí typ pracovní položky**rozbalte a vyberte **projekt TDSP**. 
   1. Vyberte **Uložit**.
   
   ![Nastavení úrovně nevyřízených položek portfolia](./media/agile-development/13-rename.png)  
   
1. Stejným postupem **přejmenujte funkce** na *fáze TDSP*a přidejte následující nové typy pracovních položek:
   
   - *Obchodní porozumění*
   - *Získávání dat*
   - *Modelování*
   - *Nasazení*
   
1. V části **Nevyřízené** **položky** požadavku přejmenujte články na *dílčí fáze TDSP*, přidejte nové dílčí stupeň pracovní položky *TDSP*a nastavte výchozí typ pracovní položky na **dílčí stupeň TDSP**.
   
1. V **části nevyřízené položky iterace**přidejte nový typ pracovní položky *TDSP Task*a nastavte ji jako výchozí typ pracovní položky. 
   
Po dokončení kroků by měly úrovně nevyřízených položek vypadat takto:
   
 ![Úrovně nevyřízených položek šablony TDSP](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Vytvořit pracovní položky procesu agilní datové vědy

Šablonu procesu datové vědy můžete použít k vytvoření projektů TDSP a ke sledování pracovních položek, které odpovídají fázím životního cyklu TDSP.

1. Na hlavní stránce organizace Azure DevOps vyberte **Nový projekt**. 
   
1. V dialogovém okně **Vytvořit nový projekt** pojmenujte projekt a pak vyberte **Upřesnit**. 
   
1. V části **Proces pracovní položky**rozbalte a vyberte **AgileDataScienceProcess**a pak vyberte **Vytvořit**.
   
   ![Vytvoření projektu TDSP](./media/agile-development/15-newproject.png)
   
1. V nově vytvořeném projektu vyberte v levém navigačním panelu**položky Nevyřízené položky vývěsky.** **Boards** > 
   
1. Chcete-li, aby byly projekty TDSP viditelné, vyberte ikonu **Konfigurovat nastavení týmu.** Na obrazovce **Nastavení** zaškrtněte políčko **Projekty TDSP** a pak vyberte **Uložit a zavřít**.
   
   ![Zaškrtněte políčko TDSP Projects .](./media/agile-development/16-enabledsprojects1.png)
   
1. Chcete-li vytvořit projekt TDSP specifický pro datovou vědu, vyberte **projekty TDSP** v horním pruhu a pak vyberte **Nová pracovní položka**. 
   
1. Ve vyskakovacím okně pojmenujte pracovní položku projektu TDSP a vyberte **Přidat nahoru**.
   
   ![Vytvořit pracovní položku projektu datové vědy](./media/agile-development/17-dsworkitems0.png)
   
1. Chcete-li přidat pracovní položku v projektu **+** TDSP, vyberte vedle projektu a vyberte typ pracovní položky, kterou chcete vytvořit. 
   
   ![Vybrat typ pracovní položky datové vědy](./media/agile-development/17-dsworkitems1.png)
   
1. Vyplňte podrobnosti v nové pracovní položce a vyberte **Uložit & Zavřít**.
   
1. Pokračujte v **+** výběru symbolů vedle pracovních položek, které chcete přidat nové fáze TDSP, dílčí fáze a úkoly. 
   
Zde je příklad, jak by měly být pracovní položky projektu datové vědy zobrazeny v zobrazení **nevyřízených položek:**

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Další kroky

[Kolaborativní kódování s Gitem](collaborative-coding-with-git.md) popisuje, jak provést vývoj kolaborativního kódu pro projekty datových věd pomocí Gitu jako rámce pro vývoj sdíleného kódu a jak propojit tyto aktivity kódování s plánovanou prací s agilním procesem.

[Příklad návody](walkthroughs.md) uvádí návody konkrétní scénáře s odkazy a popisy miniatur. Propojené scénáře ilustrují, jak kombinovat cloudové a místní nástroje a služby do pracovních postupů nebo kanálů a vytvářet inteligentní aplikace.
  
Další zdroje informací o agilních procesech:

- [Proces Agile (agilní)](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Agilní typy pracovních položek procesu a pracovní postup](/azure/devops/boards/work-items/guidance/agile-process-workflow)


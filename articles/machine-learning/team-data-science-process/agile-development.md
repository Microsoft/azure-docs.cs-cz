---
title: Agilní vývoj projektů pro datové vědy – vědecké zpracování týmových dat
description: Spusťte projekt pro datové vědy systematicky, řízenou verzí a spolupracujícím způsobem v rámci projektového týmu pomocí vědeckého procesu týmového zpracování dat.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 113c11b749b42bbaf9e5e40cdf63623288997a92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "89440282"
---
# <a name="agile-development-of-data-science-projects"></a>Agilní vývoj projektů pro datové vědy

Tento dokument popisuje, jak můžou vývojáři spustit projekt pro datové vědy v systematické, řízené verzi a spolupráci v rámci projektového týmu pomocí [vědeckého zpracování týmových dat](overview.md) (TDSP). TDSP je architektura vyvinutá Microsoftem, která poskytuje strukturovanou sekvenci aktivit pro efektivní spouštění cloudových řešení prediktivní analýzy. Přehled rolí a úloh, které jsou zpracovávány týmem pro datové vědy, se standardizací na TDSP, najdete v tématu [role procesů a úlohy vědeckého zpracování dat týmu](roles-tasks.md). 

Tento článek obsahuje pokyny k tomu, jak: 

- *Plánování sprintu* pro pracovní položky zahrnuté v projektu.
- Přidejte *pracovní položky* do sprintů.
- Vytvořte a použijte *šablonu pro pracovní položky odvozenou od agilního zpracování* , která se specificky zarovnává se fázemi životního cyklu TDSP.

Následující pokyny popisují kroky potřebné k nastavení týmového prostředí TDSP pomocí Azure Boards a Azure Repos v Azure DevOps. Pokyny využívají Azure DevOps, protože to je způsob implementace TDSP v Microsoftu. Pokud vaše skupina používá jinou platformu hostování kódu, úkoly vedoucí týmu se obecně nemění, ale způsob, jakým se úkoly dokončí, se liší. Například propojení pracovní položky s větví Git nemusí být stejné s GitHubem, protože se jedná o Azure Repos.

Následující obrázek znázorňuje typické plánování sprintu, kódování a pracovní postup zdrojového řízení pro projekt datové vědy:

![Vědecké zpracování týmových dat](./media/agile-development/1-project-execute.png)

##  <a name="work-item-types"></a><a name='Terminology-1'></a>Typy pracovních položek

V architektuře plánování sprintu TDSP existují čtyři často používané typy *pracovních položek* : *funkce*, *uživatelské scénáře*, *úkoly* a *chyby*. Nevyřízené položky pro všechny pracovní položky jsou na úrovni projektu, nikoli na úrovni úložiště Git. 

Tady jsou definice pro typy pracovních položek:

- **Funkce**: funkce odpovídá zapojení projektu. Různé zapojení s klientem jsou různé funkce a je vhodné zvážit různé fáze projektu jako jiné funkce. Pokud zvolíte schéma, jako je například *\<ClientName>-\<EngagementName>* pro pojmenování funkcí, můžete snadno rozpoznat kontext projektu a zapojení z názvů samotných.
  
- **Uživatelský scénář**: uživatelské scénáře jsou pracovními položkami, které jsou potřeba k dokončení funkce na konci. Mezi příklady uživatelských scénářů patří:
  - Získání dat 
  - Zkoumání dat 
  - Generování funkcí
  - Sestavení modelů
  - Zprovoznění modelů 
  - Přeučování modelů
  
- **Úkol**: úkoly lze přiřadit pracovní položky, které je třeba provést, aby bylo možné dokončit konkrétní uživatelský scénář. Například úkoly v uživatelském scénáři *získají data* :
  - Získat přihlašovací údaje pro SQL Server
  - Nahrání dat do služby Azure synapse Analytics
  
- **Chyba**: Chyby jsou v existujícím kódu nebo dokumentech, které je třeba opravit, aby bylo možné dokončit úlohu. Pokud jsou chyby způsobeny chybějícími pracovními položkami, mohou být vyřešeny jako uživatelské scénáře nebo úkoly. 

Odborníci na data můžou být pohodlnější pomocí agilní šablony, která nahrazuje funkce, uživatelské scénáře a úkoly pomocí fází TDSP a dílčích fází životního cyklu. Pokud chcete vytvořit šablonu odvozenou od agilního plánování, která se specificky rovná se fázemi životního cyklu TDSP, přečtěte si téma [použití šablony agilní TDSP Work](#set-up-agile-dsp-6).

> [!NOTE]
> TDSP si vypůjčí koncepty funkcí, uživatelských scénářů, úloh a chyb ze správy kódu softwaru (SCM). Koncepty TDSP se mohou mírně lišit od jejich konvenčních definic SCM.

## <a name="plan-sprints"></a><a name='SprintPlanning-2'></a>Plánování sprintů

Mnoho vědeckých pracovníků se zabývá více projekty, což může trvat několik měsíců a pokračovat v různých tempech. Plánování sprintu je užitečné pro stanovení priorit projektů a plánování a přidělování prostředků. V Azure Boards můžete snadno vytvářet, spravovat a sledovat pracovní položky pro vaše projekty a provádět plánování sprintů, abyste zajistili, že se projekty pohybují podle očekávání.

Další informace o plánování sprintů naleznete v tématu [Scrum sprinty](https://en.wikipedia.org/wiki/Scrum_(software_development)#Sprint). 

Další informace o plánování sprintu v Azure Boards naleznete v tématu [přiřazení nevyřízených položek do sprintu](/azure/devops/boards/sprints/assign-work-sprint). 

## <a name="add-a-feature-to-the-backlog"></a><a name='AddFeature-3'></a>Přidání funkce do nevyřízených položek 

Po vytvoření projektu a úložiště kódu projektu můžete přidat funkci do nevyřízených položek, které představují práci pro váš projekt.

1. Na stránce **projektu vyberte**  >  v levém navigačním panelu položku **neprotokoly** . 
   
1. Pokud **je typ** pracovní položky na horním panelu na kartě **backlogu** , rozevírací seznam a výběr **funkcí**. Pak vyberte **nová pracovní položka.**
   
   ![Vybrat novou pracovní položku](./media/agile-development/2-sprint-team-overview.png)
   
1. Zadejte název funkce, obvykle název projektu, a pak vyberte **Přidat k hornímu**. 
   
   ![Zadejte název a vyberte Přidat do horní části.](./media/agile-development/3-sprint-team-add-work.png)
   
1. V seznamu **nevyřízených položek** vyberte a otevřete novou funkci. Zadejte popis, přiřaďte člena týmu a nastavte parametry plánování. 
   
   Tuto funkci můžete také propojit s Azure Reposm úložiště kódu projektu výběrem možnosti **Přidat odkaz** v části **vývoj** . 
   
   Po úpravě funkce vyberte **uložit & zavřít**.
   
   ![Upravit funkci a vybrat Uložit & zavřít](./media/agile-development/3a-add-link-repo.png)

## <a name="add-a-user-story-to-the-feature"></a><a name='AddStoryunderfeature-4'></a>Přidat uživatelský scénář do funkce 

V rámci této funkce můžete přidat uživatelské scénáře, které popisují hlavní kroky potřebné k dokončení projektu. 

Přidání nového uživatelského scénáře do funkce:

1. Na kartě **backlogu** vyberte **+** vlevo od funkce. 
   
   ![Přidání nového uživatelského scénáře pod funkci](./media/agile-development/4-sprint-add-story.png)
   
1. Dejte uživatelskému scénáři název a upravte podrobnosti, jako je například přiřazení, stav, popis, komentáře, plánování a Priorita. 
   
   Můžete také propojit uživatelský scénář s větví Azure Reposho úložiště kódu projektu výběrem možnosti **Přidat odkaz** v části **vývoj** . Vyberte úložiště a větev, na které chcete pracovní položku propojit, a pak vyberte **OK**.
   
   ![Přidat odkaz](./media/agile-development/5-sprint-edit-story.png)
   
1. Až skončíte s úpravou uživatelského scénáře, vyberte **uložit & zavřít**. 

## <a name="add-a-task-to-a-user-story"></a><a name='AddTaskunderstory-5'></a>Přidání úkolu do uživatelského scénáře 

Úlohy jsou konkrétní podrobné kroky, které jsou potřeba k dokončení jednotlivých uživatelských scénářů. Po dokončení všech úkolů uživatelského scénáře by měl být uživatelský scénář dokončený. 

Chcete-li přidat úkol do uživatelského scénáře, vyberte **+** vedle položky uživatelský scénář a vyberte možnost **úloha**. Vyplňte název a další informace v úkolu.

![Přidání úkolu do uživatelského scénáře](./media/agile-development/7-sprint-add-task.png)

Po vytvoření funkcí, uživatelských scénářů a úkolů je můžete zobrazit **v zobrazení** nevyřízených **položek nebo panelů** , abyste mohli sledovat jejich stav.

![Zobrazení nevyřízených položek](./media/agile-development/8-sprint-backlog-view.png)

![Zobrazení desek](./media/agile-development/8a-sprint-board-view.png)

## <a name="use-an-agile-tdsp-work-template"></a><a name='set-up-agile-dsp-6'></a>Použití šablony agilního TDSP práce

Odborníci na data můžou být pohodlnější pomocí agilní šablony, která nahrazuje funkce, uživatelské scénáře a úkoly pomocí fází TDSP a dílčích fází životního cyklu. V Azure Boards můžete vytvořit agilní šablonu odvozenou pro vytváření a sledování pracovních položek pomocí fází životního cyklu TDSP. Následující kroky vás provedou nastavením šablony agilního procesu specifické pro datovou vědu a vytvářením pracovních položek pro datové vědy na základě šablony.

### <a name="set-up-an-agile-data-science-process-template"></a>Nastavení agilní šablony procesu pro datové vědy

1. Na hlavní stránce vaší organizace Azure DevOps v levém navigačním panelu vyberte **Nastavení organizace** . 
   
1. V levém navigačním panelu **Nastavení organizace** vyberte v **části panely** možnost **proces**. 
   
1. V podokně **všechny procesy** vyberte **...** vedle **agilního** a pak vyberte **vytvořit zděděný proces**.
   
   ![Vytvořit Zděděný proces z agilního řízení](./media/agile-development/10-settings.png) 
   
1. V dialogu **vytvořit zděděný proces z agilního** projektu zadejte název *AgileDataScienceProcess* a vyberte **vytvořit proces**.
   
   ![Vytvořit proces AgileDataScienceProcess](./media/agile-development/11-agileds.png)
   
1. V části **všechny procesy** vyberte nový **AgileDataScienceProcess**. 
   
1. Na kartě **typy pracovní položky** zakažte **námětu**, **funkce**, **uživatelský scénář** a **úkol** tak, že vyberete **...** vedle každé položky a pak vyberete **Zakázat**. 
   
   ![Zakázat typy pracovních položek](./media/agile-development/12-disable.png)
   
1. V části **všechny procesy** vyberte kartu **úrovně backlogu** . V části **portfolia** nevyřízené položky vyberte **...** vedle **námětu (zakázáno)** a pak vyberte **upravit/přejmenovat**. 
   
1. V dialogovém okně **Upravit úroveň backlogu** :
   1. V části **název** nahraďte **námětu** *TDSP projekty*. 
   1. V části **typy pracovních položek na této úrovni backlogu** vyberte **nový typ pracovní položky**, zadejte *projekt TDSP* a vyberte **Přidat**. 
   1. V části **výchozí typ pracovní položky**, rozevírací seznam a vyberte **projekt TDSP**. 
   1. Vyberte **Uložit**.
   
   ![Nastavit úroveň nevyřízených položek portfolia](./media/agile-development/13-rename.png)  
   
1. Použijte stejný postup k přejmenování **funkcí** do *TDSP fází* a přidejte následující typy pracovních položek:
   
   - *Obchodní porozumění*
   - *Získání dat*
   - *Modelování*
   - *Nasazení*
   
1. V části **nevyřízené položky požadavků** přejmenujte **příběhy** na *dílčí fáze TDSP*, přidejte novou *dílčí fázi TDSP* typu pracovní položky a nastavte výchozí typ pracovní položky na **TDSP dílčí fáze**.
   
1. V části **nevyřízené položky iterace** přidejte nový úkol typu pracovní položky *TDSP* a nastavte jej jako výchozí typ pracovní položky. 
   
Po dokončení tohoto postupu by úrovně backlogu měly vypadat takto:
   
 ![Úrovně nevyřízených položek šablon TDSP](./media/agile-development/14-template.png)  

### <a name="create-agile-data-science-process-work-items"></a>Vytváření pracovních položek procesu agilních datových vědy

Šablonu procesu pro datové vědy můžete použít k vytvoření projektů TDSP a sledování pracovních položek, které odpovídají fázím životního cyklu TDSP.

1. Na hlavní stránce vaší organizace Azure DevOps vyberte **Nový projekt**. 
   
1. V dialogovém okně **vytvořit nový projekt** zadejte název projektu a pak vyberte **Upřesnit**. 
   
1. V části **proces pracovní položky** rozbalte rozevírací seznam a vyberte **AgileDataScienceProcess** a pak vyberte **vytvořit**.
   
   ![Vytvoření projektu TDSP](./media/agile-development/15-newproject.png)
   
1. V nově vytvořeném **projektu vyberte v**  >  levém navigačním panelu položku **neprotokoly** .
   
1. Pokud chcete, aby se projekty TDSP zobrazovaly, vyberte ikonu **Konfigurovat nastavení týmu** . Na obrazovce **Nastavení** zaškrtněte políčko **projekty TDSP** a pak vyberte **Uložit a zavřít**.
   
   ![Zaškrtávací políčko pro výběr projektů TDSP](./media/agile-development/16-enabledsprojects1.png)
   
1. Chcete-li vytvořit projekt TDSP specifický pro datové vědy, vyberte v horním panelu **projekty TDSP** a pak vyberte možnost **nová pracovní položka**. 
   
1. V překryvném okně zadejte název pracovní položky projektu TDSP a vyberte **Přidat k hornímu**.
   
   ![Vytvořit pracovní položku projektu pro datové vědy](./media/agile-development/17-dsworkitems0.png)
   
1. Chcete-li přidat pracovní položku v projektu TDSP, vyberte **+** vedle projektu a vyberte typ pracovní položky, který chcete vytvořit. 
   
   ![Vybrat typ pracovní položky pro datové vědy](./media/agile-development/17-dsworkitems1.png)
   
1. Vyplňte podrobnosti nové pracovní položky a vyberte **uložit & zavřít**.
   
1. Pokračujte výběrem **+** symbolů vedle pracovních položek a přidejte nové fáze TDSP, dílčí fáze a úlohy. 
   
Tady je příklad toho, jak se pracovní položky projektu datové vědy mají **Zobrazit v zobrazení** nevyřízených položek:

![18](./media/agile-development/18-workitems1.png)


## <a name="next-steps"></a>Další kroky

Kód pro [spolupráci s Git](collaborative-coding-with-git.md) popisuje, jak vyvíjet vývoj kódu pro projekty pro datové vědy pomocí Gitu jako rozhraní pro vývoj sdíleného kódu a jak propojit tyto aktivity kódu s prací plánovaným pomocí agilního procesu.

[Příklady návodů](walkthroughs.md) obsahuje návody pro konkrétní scénáře s odkazy a popisy miniatur. Propojené scénáře znázorňují, jak kombinovat cloudové a místní nástroje a služby do pracovních postupů nebo kanálů k vytváření inteligentních aplikací.
  
Další zdroje informací o agilních procesech:

- [Proces Agile (agilní)](/azure/devops/boards/work-items/guidance/agile-process)
  
- [Typy pracovních položek a pracovní postup agilního procesu](/azure/devops/boards/work-items/guidance/agile-process-workflow)


---
title: Agilní vývoj projekty datových věd - vědecké zpracování týmových dat
description: Jak můžete spustit projekt vědeckého zpracování dat v systematické, verzi řízen vývojáři a spolupráce v týmu projektu pomocí vědeckého zpracování týmových dat.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/28/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: ee8e3e3ef33a8b09b92d4dfc262fce26dd60abb3
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53134399"
---
# <a name="agile-development-of-data-science-projects"></a>Agilní vývoj projekty datových věd

Tento dokument popisuje, jak můžou vývojáři spustit projekt vědeckého zpracování dat v systematicky, správy verzí a spolupráce v rámci týmového projektu pomocí [vědecké zpracování týmových dat](overview.md) (TDSP). TDSP je architektura vyvinutý microsoftem, která poskytuje strukturovaných posloupnost aktivit k úspěšnému provedení cloudové prediktivní analytická řešení. Přehled rolí pracovníky a jejich přidružených úloh, které jsou zpracovávány standardizací data science týmu o tomto procesu najdete v části [vědecké zpracování týmových dat role a úlohy](roles-tasks.md). 

Tento článek obsahuje pokyny o tom, jak: 

1. proveďte **plánování sprintu** pro pracovní položky, které jsou součástí projektu.<br> Pokud nejste obeznámeni s plánování sprintů, můžete najít podrobnosti a obecné informace [tady](https://en.wikipedia.org/wiki/Sprint_(software_development) "tady"). 
2. **Přidání pracovních položek** do sprintů. 

> [!NOTE]
> Kroky potřebné k nastavení prostředí TDSP týmu DevOps služby Azure jsou popsány v následující sadu pokynů. Určí způsob k provedení těchto úloh se službami Azure DevOps, protože se jedná o tom, jak implementovat TDSP v Microsoftu.  Pokud budete chtít používání služeb Azure DevOps, položky (3) a (4) v předchozím seznamu jsou výhody, které přirozeně. Pokud jiný kód hostování platformy se používá pro vaši skupinu, úlohy, které musíte provést vedoucí týmu obecně se nezmění. Ale způsob k provedení těchto úloh se bude lišit. Například na položku v části šest **propojit pracovní položku s větví Git**, nemusí být snadné, protože se jedná o službách Azure DevOps.
>
>

Následující obrázek ukazuje typické sprintů, plánování, kódování a pracovní postup správy zdrojového kódu, které jsou součástí implementace projekt vědeckého zpracování dat:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologie 

V TDSP sprintů plánování framework, existují čtyři často používané typy **pracovní položky**: **funkce**, **uživatelský scénář**, **úloh**, a **Chyb**. Každý projekt udržuje jednu sadu nevyřízených položek pro všechny pracovní položky. Na úrovni úložiště Git v rámci projektu se nevyskytuje žádná nevyřízená úloha. Tady je jejich definice:

- **Funkce**: funkce odpovídá engagement projektu. Různé zapojení klienta jsou považovány za různých funkcí. Podobně je nejlepší vzít v úvahu různých fází projektu pomocí klienta jako různé funkce. Pokud se například rozhodnete schéma ***ClientName EngagementName*** název funkce, pak snadno poznáte kontext projektu/engagement z názvů sami.
- **Scénář**: scénáře jsou různé pracovní položky, které jsou vyžadovány k dokončení funkce (projekt) end až do konce. Příklady scénářů:
    - Získání dat 
    - Zkoumání dat 
    - Generuje se funkce
    - Vytváření modelů
    - Až po zprovoznění modelů 
    - Přeučení modelů
- **Úloha**: jsou úkoly lze přiřadit kódu nebo dokumentu pracovních položek nebo jiných aktivit, které je potřeba provést k dokončení konkrétní scénáře. Například úkoly v textu *načtení dat* může být:
    -  Získání přihlašovacích údajů systému SQL Server 
    -  Nahrávání dat do SQL Data Warehouse. 
- **Chyba**: chyby obvykle najdete opravy, které jsou potřeba pro existující kód nebo dokumentu, které se provádějí při dokončování úkolu. Je-li chybu nebyl nalezen fází nebo úlohy, můžete předat do scénáře nebo úkol. 

> [!NOTE]
> Koncepty jsou si funkcí, scénářů, úloh a chyb z softwaru správy kódu (SCM), který se má použít v datových věd. Se může od jejich konvenční SCM definice mírně liší.
>
>

> [!NOTE]
> Odborníci přes data mohou mít pocit pohodlnější použití šablona agile, které konkrétně v souladu s TDSP fáze životního cyklu. To na paměti plánování šablona Agile odvozené sprintu vytvořila, kde příběhy, články atd. jsou nahrazena fáze životního cyklu TDSP nebo substages. Pokyny o tom, jak vytvořit šablonu agilního najdete v tématu [agilní datový vědecký proces v aplikaci Visual Studio Online nastavit](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Plánování sprintů 

Plánování sprintu je užitečné pro stanovení priority projektu a plánování prostředků a přidělení. Mnoho odborníci přes data jsou s více projekty, z nichž každá může trvat měsíce na absolvování zapojení. Projekty se často pokračovat v různých mezery. Ve službě Azure DevOps můžete snadno vytvářet, spravovat a sledování pracovních položek ve vašem projektu a provést k zajištění, že vaše projekty se v budoucnu podle očekávání plánování sprintu. 

Postupujte podle [tento odkaz](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) podrobné pokyny k plánování služby Azure DevOps sprintu. 


## 3. <a name='AddFeature-3'></a>Přidat funkci  

Po vytvoření projektu úložiště v rámci projektu, přejděte na tým **přehled** stránky a klikněte na tlačítko **řídit práci**.

![2](./media/agile-development/2-sprint-team-overview.png)

Zahrnout funkce v nevyřízených položkách, klikněte na tlačítko **nevyřízených položek** --> **funkce** --> **nový**, typ ve funkci **Title**(obvykle název projektu) a potom klikněte na tlačítko **přidat** .

![3](./media/agile-development/3-sprint-team-add-work.png)

Klikněte dvakrát na funkce, kterou jste vytvořili. Zadejte popis, přiřazení členů týmu k této funkci a nastavit parametry pro tuto funkci plánování. 

Tuto funkci můžete také propojit úložiště projektu. Klikněte na tlačítko **odkaz Přidat** pod **vývoj** oddílu. Po dokončení úprav funkci klikněte na tlačítko **uložit a zavřít** ukončíte.


## 4. <a name='AddStoryunderfeature-4'></a>Přidat scénář podle funkce 

V části funkce lze přidat scénáře popsat hlavní kroky potřebné k dokončení projektu (funkce). Chcete-li přidat nový článek, klikněte na tlačítko **+** podepsat nalevo od funkce v zobrazení nevyřízených položek.  

![4](./media/agile-development/4-sprint-add-story.png)

Můžete upravit podrobnosti scénáře, jako je například stav, popis, komentáře, plánování a priority v místním okně.

![5](./media/agile-development/5-sprint-edit-story.png)

Tento scénář můžete propojit existující úložiště kliknutím **+ přidat propojení** pod **vývoj**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Přidejte úkol do textu 

Úlohy jsou konkrétní podrobné kroky, které jsou vyžadovány k dokončení každý scénář. Po dokončení všech úkolů scénáře, by se měl článek příliš dokončit. 

Přidejte úkol do článku, klikněte na tlačítko **+** znaménko vedle textu položky, vyberte **úloh**a pak zadejte podrobné informace o této úlohy v místním okně.

![7](./media/agile-development/7-sprint-add-task.png)

Po vytvoření funkce, scénáře a úkoly můžete zobrazit v **nevyřízených položek** nebo **panelu** zobrazení sledovat jejich stav.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Nastavit šablonu Agilního TDSP práce ve Visual Studiu Online

Tento článek vysvětluje, jak vytvořit šablonu procesu vědy agilní data, která používá fáze životního cyklu TDSP datové vědy a sleduje pracovní položky pomocí sady Visual Studio Online (vso). Následující kroky názorný postup vás provede Příkladem nastavení datové vědy – konkrétní agilní Šablona procesu *AgileDataScienceProcess* a ukazují, jak vytvářet data science pracovní položky na základě šablony.

### <a name="agile-data-science-process-template-setup"></a>Nastavení šablony procesu agile Data Science

1. Přejděte na domovské stránce serveru **konfigurovat** -> **procesu**.

    ![10](./media/agile-development/10-settings.png) 

2. Přejděte do **všechny procesy** -> **procesy**v části **Agile** a klikněte na **vytvořit zděděné procesu**. Pak přidejte název procesu "AgileDataScienceProcess" a klikněte na tlačítko **vytvořit proces**.

    ![11](./media/agile-development/11-agileds.png)

3. V části **AgileDataScienceProcess** -> **typy pracovních položek** kartu, zakažte **Epic**, **funkce**,  **Uživatelský scénář**, a **úloh** pomocí typů pracovních položek **konfigurace -> zakázat**

    ![12](./media/agile-development/12-disable.png)

4. Přejděte do **AgileDataScienceProcess** -> **úrovně nevyřízené položky** kartu. Přejmenovat "Náměty" na "TDSP projekty" po kliknutí na **konfigurovat** -> **úpravy/přejmenování**. V dialogovém okně stejné klikněte na tlačítko **+ nový typ pracovní položky** v "Projekt vědeckého zpracování dat" a nastavte hodnotu **výchozí typ pracovní položky** "TDSP projektu" 

    ![13](./media/agile-development/13-rename.png)  

5. Podobně, změňte název nevyřízené položky "Funkce" na "TDSP fáze" a přidejte následující text do **nové pracovní položky typu**:

    - Obchodní vysvětlení
    - Získání dat
    - Modelování
    - Nasazení

6. Přejmenujte "Uživatelský scénář" na "TDSP Substages" s výchozí typ pracovní položky typu nově vytvořený "TDSP Substage".

7. Nastavení "Úloh" ke nově vytvořit typ pracovní položky "TDSP úloha" 

8. Po provedení těchto kroků úrovně nevyřízených položek by měl vypadat takto:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Vytvoření datové vědy pracovní položky

Po vytvoření šablony procesu datové vědy můžete vytvořit a sledovat pracovní položky datové vědy, které odpovídají TDSP životního cyklu.

1. Když vytvoříte nový projekt, vyberte "Agile\AgileDataScienceProcess" jako **zpracování pracovní položky**:

    ![15](./media/agile-development/15-newproject.png)

2. Přejděte na nově vytvořený projekt a klikněte na **pracovní** -> **nevyřízených položek**.

3. Po kliknutí na zviditelnit "TDSP projekty" **konfigurace nastavení týmu** a zkontrolovat "TDSP projekty"; potom uložte.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Teď můžete začít vytvářet datové vědy konkrétní pracovní položky.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Tady je příklad toho, jak by měl pracovní položky datové vědy projektu zobrazí:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Další postup

[Kódování spolupráci s Gitem](collaborative-coding-with-git.md) popisuje postup vývoje spolupráci kódu pro projekty datových věd jako vývojářská platforma sdílený kód pomocí Gitu a propojíte kódování aktivity k práci, plánovaná s agilního procesu.

Tady jsou další odkazy na prostředky na agilní procesy.

- Agilní proces   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Agilní proces typy pracovních položek a pracovní postup   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Postupy, které popisují všechny kroky v procesu pro **konkrétních scénářů** jsou také k dispozici. Jsou uvedena v seznamu a propojené s Miniatura popisy v [příklad návody](walkthroughs.md) článku. Jejich ukazují, jak zkombinovat cloudové, místní nástroje a služby do pracovního postupu nebo kanálu pro vytváření inteligentních aplikací. 

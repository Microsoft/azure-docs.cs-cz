---
title: Agilní vývoj datové vědy projekty - Azure Machine Learning | Microsoft Docs
description: Jak vývojáři spuštění projektu vědecké účely data v řídí systematické, verzi a způsob spolupráce v rámci projektový tým pomocí procesu Team dat vědecké účely.
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: ''
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: deguhath
ms.openlocfilehash: b57b77f5adfa3fb73372ac8297f408fb339b5d79
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837425"
---
# <a name="agile-development-of-data-science-projects"></a>Agilní vývoj datové vědy projektů

Tento dokument popisuje, jak můžete vývojáři spustit projekt vědecké účely data v systematické, verze řízené a spolupráce způsob, jak v rámci projektový tým pomocí [proces vědecké účely dat Team](overview.md) (TDSP). TDSP je architektura vyvinuté společností Microsoft, která poskytuje strukturovaných posloupnost aktivit k úspěšnému provedení řešení založená na cloudu, prediktivní analýzy. Přehled role pracovníky a jejich přidružených úloh, které zpracovávají datové vědy team standardizace na tento proces, najdete v části [proces vědecké účely dat Team rolí a úloh](roles-tasks.md). 

Tento článek obsahuje pokyny o tom, jak: 

1. proveďte **plánování sprintu** pro pracovní položky spojených s projektem.<br> Pokud jste obeznámeni s plánování sprintu, můžete najít podrobnosti a obecné informace [sem](https://en.wikipedia.org/wiki/Sprint_(software_development) "zde"). 
2. **Přidání pracovních položek** k sprintů. 

> [!NOTE]
> Kroky potřebné k nastavení prostředí team TDSP pomocí Visual Studio Team Services (VSTS) jsou uvedeny v následující sadu pokynů. Určí, jak provést tyto úlohy s služby VSTS, protože se jedná o tom, jak implementovat TDSP v Microsoft.  Pokud si zvolíte používání služby VSTS, položky (3) a (4) v předchozím seznamu jsou výhody, které jste získali přirozeně. Pokud jiný kód hostování platformy slouží pro vaši skupinu, úlohy, které je potřeba dokončit vedoucí týmu obecně se nezmění. Ale způsob k dokončení těchto úloh se bude lišit. Například na položku v části půl, **propojit pracovní položky pomocí Git větev**, nemusí být stejně snadná jako na služby VSTS.
>
>

Následující obrázek ukazuje typické sprintu plánování, kódování a pracovní postup správy zdrojového kódu, které jsou součástí implementace projektu vědecké účely dat:

![1](./media/agile-development/1-project-execute.png)


##  1. <a name='Terminology-1'></a>Terminologie 

V TDSP úbytek sprintu plánování framework, existují čtyři často používané typy **pracovní položky**: **funkce**, **uživatelský scénář**, **úloh**, a **Chyb**. Každý týmový projekt udržuje jeden nevyřízených položek pro všechny pracovní položky. Neexistuje žádná nevyřízená úloha na úrovni úložiště Git v části týmového projektu. Zde jsou jejich definice:

- **Funkce**: funkce odpovídá engagement projektu. Různé oznámeních podporujících zapojení uživatelů s klientem jsou považovány za různých funkcí. Podobně je vhodné vzít v úvahu různých fází projektu s klientem jako různých funkcí. Pokud například vyberete schéma ***ClientName EngagementName*** na název vaší funkce, pak snadno poznáte kontextu projektu nebo engagement z názvů sami.
- **Scénáře**: scénářů se jiný pracovní položky, které jsou nutné k dokončení funkce (projekt) začátku do konce. Příklady scénářů:
    - Získání dat 
    - Zkoumání dat 
    - Generování funkce
    - Vytváření modelů
    - Zprovozňování modely 
    - Přeučení modelů
- **Úloha**: úlohy jsou přiřadit kód nebo dokumentu pracovní položky nebo jiných aktivit, které je potřeba k dokončení konkrétní scénáře. Například úloh v článku *získávání dat* může být:
    -  Získání pověření systému SQL Server 
    -  Nahrávání dat do SQL Data Warehouse. 
- **Chyby**: chyby jsou obvykle odkazovat na opravy, které jsou potřebné pro existující kód nebo dokumentu, které se provádějí při dokončení úlohy. Pokud chyb je způsobená chybějícím fázích nebo úlohy v uvedeném pořadí, můžete eskalovat pro scénáře nebo úlohu. 

> [!NOTE]
> Funkce, scénářů, úlohy a chyby ze softwaru kód správy (SCM), který se má použít v vědecké zpracování dat vždy pouze vypůjčí koncepty. Se může mírně lišit od jejich konvenční SCM definice.
>
>

> [!NOTE]
> Datových vědců může mít více možnost pomocí agilní šablony, která konkrétně zarovnaná s fáze životního cyklu TDSP. Si uvědomit byl vytvořen sprintu odvozené agilní plánování šablony, kde Epics atd. scénářů jsou nahrazovány fáze životního cyklu TDSP nebo substages. Pokyny o tom, jak vytvořit šablonu agilní najdete v tématu [nastavit vědecké účely procesu agile dat v prostředí Visual Studio Online](agile-development.md#set-up-agile-dsp-6).
>
>

## 2. <a name='SprintPlanning-2'></a>Plánování sprintu 

Plánování sprintu je užitečné pro stanovení priorit projekt a plánování prostředků a přidělení. Mnoho datových vědců pověření s více projekty, z nichž každá může trvat měsíců k dokončení. Projekty často pokračovat v různých mezerami. Na serveru služby VSTS můžete snadno vytvářet, spravovat a sledování pracovních položek v týmových projektech a proveďte sprintu plánování zajistit, že projekty přesouváte dál podle očekávání. 

Postupujte podle [tento odkaz](https://www.visualstudio.com/en-us/docs/work/scrum/sprint-planning) podrobné pokyny k plánování v služby VSTS sprintu. 


## 3. <a name='AddFeature-3'></a>Přidání funkce  

Po vytvoření projektu úložiště v rámci týmového projektu, přejděte do týmu **přehled** a klikněte na tlačítko **spravovat pracovní**.

![2](./media/agile-development/2-sprint-team-overview.png)

Chcete-li zahrnout funkce nevyřízené položky, klikněte na tlačítko **nevyřízené položky** --> **funkce** --> **nový**, typ ve funkci **název**(obvykle název projektu) a potom klikněte na **přidat** .

![3](./media/agile-development/3-sprint-team-add-work.png)

Dvakrát klikněte na funkce, kterou jste vytvořili. Zadejte popis, přiřadit členové týmu pro tuto funkci a nastavení plánování parametry pro tuto funkci. 

Tuto funkci můžete také propojit úložiště projektu. Klikněte na tlačítko **odkaz Přidat** pod **vývoj** části. Po dokončení úprav funkci, klikněte na tlačítko **uložit a zavřít** ukončíte.


## 4. <a name='AddStoryunderfeature-4'></a>Přidat scénáře s funkcí 

V části funkce lze přidat scénářů k popisu důležitými kroky potřebnými k dokončení projektu (funkce). Chcete-li přidat nový článek, klikněte na tlačítko **+** přihlásit nalevo od funkci v zobrazení nevyřízených položek.  

![4](./media/agile-development/4-sprint-add-story.png)

Můžete upravit podrobnosti o scénáře, jako je stav, popis, komentáře, plánování a priority v místním okně.

![5](./media/agile-development/5-sprint-edit-story.png)

Tento scénář můžete propojit existující úložiště kliknutím **+ přidat odkaz** pod **vývoj**. 

![6](./media/agile-development/6-sprint-link-existing-branch.png)


## 5. <a name='AddTaskunderstory-5'></a>Přidání úkolu do scénáře 

Úlohy jsou konkrétní podrobné kroky, které jsou nutné k dokončení každé scénáře. Po dokončení všech úloh scénáře by se měly článek příliš dokončit. 

Chcete-li přidat úlohu do článku, klikněte na tlačítko **+** přihlašovací vedle položky scénáře, vyberte **úloh**a pak zadejte podrobné informace o této úlohy v místním okně.

![7](./media/agile-development/7-sprint-add-task.png)

Po vytvoření funkce, scénáře a úlohy, můžete je zobrazit **nevyřízených položek** nebo **Tabule** zobrazení sledovat jejich stav.

![8](./media/agile-development/8-sprint-backlog-view.png)

![9](./media/agile-development/9-link-to-a-new-branch.png)


## 6. <a name='set-up-agile-dsp-6'></a> Nastavit šablonu pracovní Agile TDSP v prostředí Visual Studio Online

Tento článek vysvětluje, jak nastavit šablonu procesu vědecké účely agilní data, která používá fáze životního cyklu TDSP datové vědy a sleduje pracovních položek pomocí sady Visual Studio Online (vso). Kroků ukázce příklad nastavení data vědecké účely konkrétní agilní Šablona procesu *AgileDataScienceProcess* a ukazují, jak vytvářet datové vědy pracovní položky na základě šablony.

### <a name="agile-data-science-process-template-setup"></a>Nastavení šablony procesu agile Data vědecké účely

1. Přejděte na domovskou stránku serveru, **konfigurace** -> **proces**.

    ![10](./media/agile-development/10-settings.png) 

2. Přejděte na **všechny procesy** -> **procesy**v části **Agile** a klikněte na **vytvořit zděděná proces**. Pak uveďte název procesu "AgileDataScienceProcess" a klikněte na **vytvořit proces**.

    ![11](./media/agile-development/11-agileds.png)

3. V části **AgileDataScienceProcess** -> **pracovních položek typu** zakažte **námět**, **funkce**,  **Uživatelský scénář**, a **úloh** pracovních položek typu podle **Disable-konfigurace >**

    ![12](./media/agile-development/12-disable.png)

4. Přejděte na **AgileDataScienceProcess** -> **nevyřízených položek úrovně** kartě. Přejmenujete kliknutím na "Epics" na "TDSP projekty" **konfigurace** -> **úpravy nebo přejmenování**. V dialogovém okně stejné klikněte na **+ nový typ pracovní položky** v "Projektu vědecké účely Data" a nastavte hodnotu **výchozí typ pracovní položky** "TDSP projekt" 

    ![13](./media/agile-development/13-rename.png)  

5. Podobně, změňte název nevyřízených položek "Funkce" na "TDSP fází a přidejte následující **typ nové pracovní položky**:

    - Obchodní vysvětlení
    - Získávání dat
    - Modelování
    - Nasazení

6. Přejmenujte "TDSP Substages" s výchozí typ pracovní položky nastavení na nově vytvořený "TDSP Substage" typ "Uživatelský scénář".

7. Nastavit "Úkoly" pro nově vytvořené typu pracovní položky "TDSP úlohy" 

8. Po provedení těchto kroků úrovně nevyřízených položek by měl vypadat takto:

    ![14](./media/agile-development/14-template.png)  

 
### <a name="create-data-science-work-items"></a>Vytvoření datové vědy pracovní položky

Po vytvoření šablony procesu vědecké účely data, můžete vytvořit a sledovat vaše data vědecké účely pracovní položky, které odpovídají TDSP životního cyklu.

1. Při vytvoření nového týmového projektu, vybrat "Agile\AgileDataScienceProcess", jako **zpracování pracovní položky**:

    ![15](./media/agile-development/15-newproject.png)

2. Přejděte na nově vytvořený týmový projekt a klikněte na **pracovní** -> **nevyřízené položky**.

3. Zviditelnit "TDSP projekty" Kliknutím na **nakonfigurovat nastavení team** a zkontrolujte "TDSP projekty"; potom uložte.

    ![16](./media/agile-development/16-enabledsprojects.png)

4. Nyní můžete začít vytváření datové vědy konkrétní pracovní položky.

    ![17](./media/agile-development/17-dsworkitems.png)

5. Tady je příklad toho, jak by měla zobrazí datové vědy projektu pracovní položky:

    ![18](./media/agile-development/18-workitems.png)


## <a name="next-steps"></a>Další postup

[Spolupráce kódování s Gitem](collaborative-coding-with-git.md) popisuje postup vývoj spolupráce kódu pro projekty vědecké účely dat pomocí Git jako sdílený kód rozhraní pro vývoj a způsob propojení tyto aktivity k práci plánováno s procesu agile kódování.

Zde jsou další odkazy na zdroje informací na agilní procesy.

- Procesu agile   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process)
- Procesu agile typy pracovních položek a pracovní postup   [https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow](https://www.visualstudio.com/en-us/docs/work/guidance/agile-process-workflow)


Návody, které ukazují všechny kroky v procesu pro **konkrétních scénářů** jsou také uvedeny. Jsou uvedena v seznamu a propojené s miniatur popisy v [příklad návody](walkthroughs.md) článku. Se ukazují, jak kombinovat cloud, místní nástroje a služby do pracovního postupu nebo kanálu vytvoření inteligentního aplikace. 

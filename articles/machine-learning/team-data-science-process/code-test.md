---
title: Data kód vědecké účely testování v Azure pomocí UCI pro dospělé příjem předpovědi datovou sadu - proces vědecké účely Team dat a Visual Studio Team Services
description: Testování s daty předpovědi pro dospělé příjem UCI data vědecké účely kód
services: machine-learning, team-data-science-process
documentationcenter: ''
author: weig
manager: deguhath
editor: cgronlun
ms.assetid: b8fbef77-3e80-4911-8e84-23dbf42c9bee
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2018
ms.author: weig
ms.openlocfilehash: de1ed0b85957413a254503fc72375866dfd1bea1
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837153"
---
# <a name="data-science-code-testing-with-the-uci-adult-income-prediction-dataset"></a>Testování s datovou sadou UCI pro dospělé příjem předpovědi data vědecké účely kód
Tento článek popisuje předběžné pokyny pro testování kódu v pracovním postupu vědecké účely data. Takové testování dává datových vědců systematicky a efektivní způsob, jak zkontrolovat kvality a očekávaný výsledek svůj kód. Používáme tým datové vědy procesu (TDSP) [projekt, který používá datovou sadu pro dospělé příjem UCI](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) které jsme dříve publikované zobrazit, jak kód testování lze provést. 

## <a name="introduction-on-code-testing"></a>Úvod k testování kódu
"Testování částí" je dlouhodobě pro vývoj softwaru. Ale pro vědecké zpracování dat, není často jasné, jaký, která znamená, a jak byste měli otestovat kód pro různých fázích životního cyklu vědecké účely data, jako například:

* Příprava dat
* Prozkoumání dat kvality
* Modelování
* Model nasazení 

Tento článek nahrazuje termín "jednotkové testování" v "testování kód." Odkazuje na testování jako funkce, které pomáhají k vyhodnocení, pokud je kód pro krok životní vědecké účely data generovala výsledky "podle očekávání." Osoba, která zapisuje test definuje, co je "podle očekávání," v závislosti na výsledek funkce – například, kontrolu kvality dat nebo modelování.

Tento článek obsahuje odkazy na jako užitečné zdroje.

## <a name="visual-studio-team-services-for-the-testing-framework"></a>Visual Studio Team Services pro rozhraní testování
Tento článek popisuje, jak můžete provádět a automatizovat testování pomocí Visual Studio Team Services (VSTS). Můžete se rozhodnout používat alternativní nástroje. Můžeme také ukazují, jak nastavit automatické sestavení pomocí služby VSTS a agentů sestavení. Pro agenty sestavení používáme Azure datové vědy virtuálních počítačů (DSVMs).

## <a name="flow-of-code-testing"></a>Postup testování kódu
Celkové pracovní postup testovacího kódu v projektu vědecké účely dat vypadá takto: 

![Vývojový diagram testování kódu](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Podrobné kroky

Pro nastavení a spuštění testování kódu a automatizované sestavení pomocí agenta sestavení a služby VSTS použijte následující postup:

1. Vytvoření projektu v sadě Visual Studio desktopová aplikace:

    !["Vytvořit nový projekt" obrazovky v sadě Visual Studio](./media/code-test/create_project.PNG)

   Po vytvoření projektu, budete ji najít v Průzkumníku řešení v pravém podokně:
    
    ![Kroky pro vytvoření projektu](./media/code-test/create_python_project_in_vs.PNG)

    ![Průzkumník řešení](./media/code-test/solution_explorer_in_vs.PNG)

3. Kanál projektu kódu do úložiště služby VSTS projektu kódu: 

    ![Úložiště projektu kódu](./media/code-test/create_repo.PNG)

4. Předpokládejme, že jste se provést některé přípravy práce data, například přijímání dat, funkce inženýrství a vytváření popisek sloupce. Chcete Ujistěte se, že váš kód generuje výsledky, které očekáváte. Zde je kód, který můžete použít k ověření, zda kód zpracování dat pracuje správně:

    * Zkontrolujte, jestli jsou správné názvy sloupců:
    
      ![Kód pro odpovídající názvy sloupců](./media/code-test/check_column_names.PNG)

    * Zkontrolujte, jestli jsou správné úrovně odpovědi:

      ![Kód pro odpovídající úrovně](./media/code-test/check_response_levels.PNG)

    * Zkontrolujte, že procento odpovědi je možné logicky:

      ![Kód odpovědi procento](./media/code-test/check_response_percentage.PNG)

    * Zkontrolujte chybějící počet každý sloupec v datech:
    
      ![Kód pro chybějící rychlost](./media/code-test/check_missing_rate.PNG)


5. Poté, co provedete krok zpracování dat a engineering pracovní funkce a natrénovali model funkční, ujistěte se, že model, který jste cvičení můžete stanovení skóre nové datové sady správně. Ke kontrole předpovědi úrovně a distribuci popisek hodnoty můžete použít následující dva testy:

    * Zkontrolujte předpovědi úrovně:
    
      ![Kód pro kontrolu předpovědi úrovně](./media/code-test/check_prediction_levels.PNG)

    * Zkontrolujte distribuci předpovědi hodnot:

      ![Kód pro kontrolu předpovědi hodnot](./media/code-test/check_prediction_values.PNG)

6. PUT všechny společně testování funkcí do skript v jazyce Python názvem **test_funcs.py**:

    ![Skript v jazyce Python pro testovací funkce](./media/code-test/create_file_test_func.PNG)


7. Po kódy testovací připraveni, můžete nastavit testovací prostředí v sadě Visual Studio.

   Vytvořte soubor Python s názvem **test1.py**. V tomto souboru vytvořte třídu, která zahrnuje všechny testy, které chcete provést. Následující příklad ukazuje šesti testy připravit:
    
    ![Soubor Python seznam testů v třídě](./media/code-test/create_file_test1_class.PNG)

8. Tyto testy můžete automaticky zjistit když vložíte **codetest.testCase** po název třídy. Otevřete Průzkumníka testů v pravém podokně a vyberte **spustit všechny**. Všechny testy se spouští sekvenčně a zjistíte, zda je test úspěšná či nikoliv.

    ![Spouštění testů](./media/code-test/run_tests.PNG)

9. Zkontrolujte v kódu do projektu úložiště pomocí příkazů Git. Poslední práci se projeví v služby VSTS za chvíli.

    ![Příkazy Gitu pro kontrolu v kódu](./media/code-test/git_check_in.PNG)

    ![Nejnovější pracovní v služby VSTS](./media/code-test/git_check_in_most_recent_work.PNG)

10. Nastavení automatické sestavení a testování v služby VSTS:

    a. V úložišti projektu vyberte **sestavení a verze**a potom vyberte **+ nový** k vytvoření nového procesu sestavení.

       ![Výběr spuštění nového procesu sestavení](./media/code-test/create_new_build.PNG)

    b. Podle pokynů vyberte zdrojový kód umístění, název projektu, úložiště a informací o větve.
    
       ![Zdroj, název, úložiště a informací o větve](./media/code-test/fill_in_build_info.PNG)

    c. Vyberte šablonu. Vzhledem k tomu, že neexistuje žádná šablona projektu Python, začněte výběrem **prázdný proces**. 

       ![Seznam šablon a tlačítko "Prázdný proces"](./media/code-test/start_empty_process_template.PNG)

    d. Název sestavení a vyberte agenta. Výchozí hodnota zde můžete zvolit, pokud chcete použít DSVM na dokončení procesu sestavení. Další informace o nastavení agentů najdete v tématu [sestavení a verzí agentů](https://docs.microsoft.com/en-us/vsts/build-release/concepts/agents/agents?view=vsts).
    
       ![Výběr sestavení a agenta](./media/code-test/select_agent.PNG)

    e. Vyberte **+** v levém podokně, chcete-li přidat úloha v této fázi sestavení. Protože vytvoříme spustit skript Pythonu **test1.py** ukončíte všechny kontroly tato úloha používá příkaz prostředí PowerShell spustit kód Python.
    
       !["Přidat úlohy" podokně pomocí prostředí PowerShell vybrané](./media/code-test/add_task_powershell.PNG)

    f. V podrobnostech prostředí PowerShell zadejte požadované informace, např. název a verze prostředí PowerShell. Zvolte **vloženého skriptu** jako typ. 
    
       V rozevíracím seznamu **vloženého skriptu**, můžete zadat **python test1.py**. Ujistěte se, že proměnná prostředí je správně vytvořen pro jazyk Python. Pokud potřebujete jinou verzi nebo jádra jazyka Python, můžete explicitně zadejte cestu, jak je znázorněno na obrázku: 
    
       ![Podrobnosti o prostředí PowerShell](./media/code-test/powershell_scripts.PNG)

    g. Vyberte **Uložit & fronty** na dokončení procesu definice sestavení.

       ![Tlačítko "Uložit & fronty"](./media/code-test/save_and_queue_build_definition.PNG)

Nyní pokaždé, když nové potvrzení vložena do úložiště kódu, procesu sestavení se spustí automaticky. (Tady používáme hlavní jako úložiště, ale můžete definovat žádné větve.) Proces běží **test1.py** soubor v počítači agenta, abyste měli jistotu, že všechno definované v kódu běží správně. 

Pokud výstrahy jsou správně nastavena, budete upozorněni v e-mailu po dokončení sestavení. Můžete také zkontrolovat stav služby VSTS na sestavení. Pokud se nezdaří, můžete zkontrolovat podrobné informace o sestavení a zjistit, které část je poškozená.

![E-mailové oznámení o úspěchu sestavení](./media/code-test/email_build_succeed.PNG)

![Služby VSTS oznámení o úspěchu sestavení](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Další postup
* Najdete v článku [UCI příjem předpovědi úložiště](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) konkrétní příklady testy částí pro scénáře datových vědecké účely.
* Postupujte podle předchozí příklady scénáře předpovědi příjem UCI v projektech vlastní datové vědy a outline.

## <a name="references"></a>Odkazy
* [Vědecké zpracování týmových dat](https://aka.ms/tdsp)
* [Testovací nástroje sady Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Služby VSTS testování prostředky](https://www.visualstudio.com/team-services/)
* [Datové vědy virtuální počítače](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
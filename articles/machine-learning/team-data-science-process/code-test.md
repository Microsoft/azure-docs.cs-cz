---
title: Testování kódu datové vědy pomocí služeb Azure DevOps – proces týmové datové vědy
description: Testování kódu datové vědy v Azure pomocí datové sady predikce příjmů dospělých UCI s procesem vědecké ho procesu týmových dat a službami Azure DevOps Services
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=weig, previous-ms.author=weig
ms.openlocfilehash: 9612114bb368898ccf31b2c8692869b84544b652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76721958"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Testování kódu datové vědy v Azure pomocí procesu vědecké ho procesu týmových dat a služeb Azure DevOps Services
Tento článek obsahuje předběžné pokyny pro testování kódu v pracovním postupu datové vědy. Toto testování poskytuje datovým vědcům systematický a efektivní způsob, jak zkontrolovat kvalitu a očekávaný výsledek jejich kódu. Používáme projekt TDSP (Team Data Science Process), [který používá datovou sadu UCI Adult Income,](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) kterou jsme publikovali dříve, aby ukázal, jak lze provést testování kódu. 

## <a name="introduction-on-code-testing"></a>Úvod do testování kódu
"Testování částí" je dlouhodobá praxe pro vývoj softwaru. Ale pro datové vědy, to často není jasné, co "testování částí" znamená a jak byste měli testovat kód pro různé fáze životního cyklu datové vědy, jako například:

* Příprava dat
* Kontrola kvality údajů
* Modelování
* Nasazení modelu 

Tento článek nahrazuje termín "testování částí" "testování kódu." Odkazuje na testování jako funkce, které pomáhají posoudit, zda kód pro určitý krok životního cyklu datové vědy vytváří výsledky "podle očekávání". Osoba, která píše test definuje, co je "podle očekávání", v závislosti na výsledku funkce – například kontrola kvality dat nebo modelování.

Tento článek obsahuje odkazy jako užitečné prostředky.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps pro testovací architekturu
Tento článek popisuje, jak provádět a automatizovat testování pomocí Azure DevOps. Můžete se rozhodnout použít alternativní nástroje. Ukazujeme také, jak nastavit automatické sestavení pomocí Azure DevOps a agenti sestavení. Pro agenty sestavení používáme virtuální počítače Azure Data Science (DSVMs).

## <a name="flow-of-code-testing"></a>Tok testování kódu
Celkový pracovní postup testování kódu v projektu datové vědy vypadá takto: 

![Vývojový diagram testování kódu](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Podrobné kroky

Pomocí následujících kroků můžete nastavit a spustit testování kódu a automatické sestavení pomocí agenta sestavení a Azure DevOps:

1. Vytvoření projektu v desktopové aplikaci Visual Studio:

    ![Obrazovka "Vytvořit nový projekt" v sadě Visual Studio](./media/code-test/create_project.PNG)

   Po vytvoření projektu ho najdete v Průzkumníku řešení v pravém podokně:
    
    ![Kroky pro vytvoření projektu](./media/code-test/create_python_project_in_vs.PNG)

    ![Průzkumník řešení](./media/code-test/solution_explorer_in_vs.PNG)

1. Naváděte kód projektu do úložiště kódu projektu Azure DevOps: 

    ![Úložiště kódu projektu](./media/code-test/create_repo.PNG)

1. Předpokládejme, že jste provedli některé práce na přípravě dat, jako je například ingestování dat, vytváření inženýrských sítí a vytváření sloupců popisků. Chcete se ujistit, že váš kód generuje výsledky, které očekáváte. Tady je nějaký kód, který můžete použít k testování, zda kód zpracování dat funguje správně:

    * Zkontrolujte, zda jsou názvy sloupců správné:
    
      ![Kód pro odpovídající názvy sloupců](./media/code-test/check_column_names.PNG)

    * Zkontrolujte, zda jsou úrovně odezvy správné:

      ![Kód pro odpovídající úrovně](./media/code-test/check_response_levels.PNG)

    * Zkontrolujte, zda je procento odpovědí přiměřené:

      ![Kód pro procento odpovědi](./media/code-test/check_response_percentage.PNG)

    * Zkontrolujte chybějící rychlost každého sloupce v datech:
    
      ![Kód pro chybějící sazbu](./media/code-test/check_missing_rate.PNG)


1. Po dokončení zpracování dat a inženýrské práce funkcí a vyškolení dobrého modelu se ujistěte, že model, který jste trénovali, dokáže správně získat nové datové sady. Následující dva testy můžete použít ke kontrole úrovně předpovědi a rozložení hodnot popisků:

    * Zkontrolujte úrovně predikce:
    
      ![Kód pro kontrolu úrovní předpovědi](./media/code-test/check_prediction_levels.PNG)

    * Zkontrolujte rozložení predikčních hodnot:

      ![Kód pro kontrolu hodnot předpovědi](./media/code-test/check_prediction_values.PNG)

1. Vložte všechny testovací funkce do skriptu Pythonu s názvem **test_funcs.py**:

    ![Skript Pythonu pro testovací funkce](./media/code-test/create_file_test_func.PNG)


1. Po připravené testovací kódy, můžete nastavit testovací prostředí v sadě Visual Studio.

   Vytvořte soubor Pythonu s názvem **test1.py**. V tomto souboru vytvořte třídu, která obsahuje všechny testy, které chcete provést. Následující příklad ukazuje šest připravených testů:
    
    ![Soubor Pythonu se seznamem testů ve třídě](./media/code-test/create_file_test1_class.PNG)

1. Tyto testy mohou být automaticky zjištěny, pokud dáte **codetest.testCase** za název třídy. V pravém podokně otevřete Průzkumníka testů a vyberte **Spustit vše**. Všechny testy budou spuštěny postupně a řeknou vám, zda je test úspěšný nebo ne.

    ![Spuštění testů](./media/code-test/run_tests.PNG)

1. Pomocí příkazů Git ubytovejte kód do úložiště projektu. Vaše nejnovější práce se brzy projeví v Azure DevOps.

    ![Příkazy Git pro vrácení kódu se změnami](./media/code-test/git_check_in.PNG)

    ![Nejnovější práce v Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Nastavení automatického sestavení a testování v Azure DevOps:

    a. V úložišti projektu vyberte **Sestavit a uvolnit**a pak vyberte **+Nový,** chcete-li vytvořit nový proces sestavení.

    ![Výběry pro spuštění nového procesu sestavení](./media/code-test/create_new_build.PNG)

    b. Podle pokynů vyberte umístění zdrojového kódu, název projektu, úložiště a informace o větvi.
    
    ![Informace o zdroji, názvu, úložišti a větvi](./media/code-test/fill_in_build_info.PNG)

    c. Vyberte šablonu. Vzhledem k tomu, že neexistuje žádná šablona projektu Pythonu, začněte výběrem **prázdného procesu**. 

    ![Seznam šablon a tlačítko "Prázdný proces"](./media/code-test/start_empty_process_template.PNG)

    d. Pojmenujte sestavení a vyberte agenta. Zde můžete zvolit výchozí nastavení, pokud chcete k dokončení procesu sestavení použít dsvm. Další informace o nastavení agentů naleznete v [tématu Sestavení a uvolnění agentů](https://docs.microsoft.com/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Výběr sestavení a agentů](./media/code-test/select_agent.PNG)

    e. Výběrem **+** v levém podokně přidáte úlohu pro tuto fázi sestavení. Vzhledem k tomu, že spustíme skript Pythonu **test1.py** k dokončení všech kontrol, tato úloha používá příkaz PowerShell ke spuštění kódu Pythonu.
    
    ![Podokno Přidat úkoly s vybraným PowerShellem](./media/code-test/add_task_powershell.PNG)

    f. V podrobnostech prostředí PowerShell vyplňte požadované informace, jako je název a verze Prostředí PowerShell. Jako typ zvolte **Vřádkový skript.** 
    
    Do pole v části **Inline Script**můžete zadat **python test1.py**. Ujistěte se, že proměnná prostředí je správně nastavena pro Python. Pokud potřebujete jinou verzi nebo jádro Pythonu, můžete explicitně určit cestu, jak je znázorněno na obrázku: 
    
    ![Podrobnosti o Prostředí PowerShell](./media/code-test/powershell_scripts.PNG)

    g. Chcete-li dokončit proces kanálu sestavení, vyberte **uložit & fronty.**

    ![Tlačítko Uložit & fronty](./media/code-test/save_and_queue_build_definition.PNG)

Nyní pokaždé, když je nová revize zatlačena do úložiště kódu, proces sestavení se spustí automaticky. (Zde používáme master jako úložiště, ale můžete definovat libovolnou větev.) Proces spustí **soubor test1.py** v počítači agenta a ujistěte se, že vše definované v kódu běží správně. 

Pokud jsou výstrahy nastaveny správně, budete po dokončení sestavení upozorněni e-mailem. Můžete také zkontrolovat stav sestavení v Azure DevOps. Pokud se nezdaří, můžete zkontrolovat podrobnosti sestavení a zjistit, který kus je přerušený.

![E-mailové oznámení o úspěchu sestavení](./media/code-test/email_build_succeed.PNG)

![Oznámení Azure DevOps o úspěchu sestavení](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Další kroky
* Konkrétní příklady testů částí pro scénáře datové vědy najdete v [úložišti předpovědi příjmů UCI.](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome)
* Postupujte podle předchozí osnovy a příklady ze scénáře předpovědi příjmů UCI ve vlastních projektech datové vědy.

## <a name="references"></a>Odkazy
* [Vědecké zpracování týmových dat](https://aka.ms/tdsp)
* [Testovací nástroje sady Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Prostředky testování Azure DevOps](https://www.visualstudio.com/team-services/)
* [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)

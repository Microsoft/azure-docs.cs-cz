---
title: Testování kódu pro vědecké zpracování dat pomocí procesu Azure DevOps Services – tým pro datové vědy
description: Testování kódu pro datové vědy v Azure s využitím datové sady předpovědi pro příjem výnosů z aplikace UCI a vědeckého zpracování týmových dat a Azure DevOps Services
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
ms.openlocfilehash: bcebc568db4a0d2b4b287045ba0a67c88d9b8c92
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312676"
---
# <a name="data-science-code-testing-on-azure-with-the-team-data-science-process-and-azure-devops-services"></a>Testování kódu pro datové vědy v Azure pomocí vědeckého procesu týmového zpracování dat a Azure DevOps Services
Tento článek obsahuje předběžné pokyny pro testování kódu v pracovním postupu pro datové vědy. Toto testování dává pracovníkům dat systematický a efektivní způsob kontroly kvality a očekávaného výsledku jejich kódu. Používáme projekt vědeckého zpracování týmových dat (TDSP) [, který používá datovou sadu pro příjem dat z Ski dospělé](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) , kterou jsme publikovali dříve, a ukazuje, jak se dá provést testování kódu. 

## <a name="introduction-on-code-testing"></a>Úvod k testování kódu
Testování částí je dlouhodobě zavazuje chránit postup pro vývoj softwaru. Pro datové vědy ale často není jasné, co znamená jednotkové testování, a způsob, jakým byste měli testovat kód pro různé fáze životního cyklu datové vědy, jako je například:

* Příprava dat
* Kontrola kvality dat
* Modelování
* Nasazení modelu 

Tento článek nahrazuje pojem testování částí pomocí testování kódu. Odkazuje na testování jako funkce, které vám pomůžou posoudit, jestli kód pro určitý krok životního cyklu datové vědy vyrábí výsledky podle očekávání. Osoba, která napíše test, definuje, co je očekáváno, v závislosti na výsledku funkce – například kontrolu kvality dat nebo modelování.

Tento článek poskytuje odkazy jako užitečné prostředky.

## <a name="azure-devops-for-the-testing-framework"></a>Azure DevOps pro testovací rozhraní
Tento článek popisuje, jak provádět a automatizovat testování pomocí Azure DevOps. Můžete se rozhodnout použít alternativní nástroje. Také ukazuje, jak nastavit automatické sestavení pomocí Azure DevOps a agentů sestavení. Pro agenty sestavení používáme Azure Data věda Virtual Machines (DSVMs).

## <a name="flow-of-code-testing"></a>Tok testování kódu
Celkový pracovní postup testování kódu v projektu pro datové vědy vypadá takto: 

![Vývojový diagram testování kódu](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Podrobné kroky

Pomocí následujících kroků můžete nastavit a spustit testování kódu a automatizované sestavení pomocí agenta sestavení a Azure DevOps:

1. Vytvoření projektu v aplikaci Visual Studio Desktop:

    ![Obrazovka vytvoření nového projektu v aplikaci Visual Studio](./media/code-test/create_project.PNG)

   Po vytvoření projektu najdete ho v Průzkumník řešení v pravém podokně:
    
    ![Postup vytvoření projektu](./media/code-test/create_python_project_in_vs.PNG)

    ![Průzkumník řešení](./media/code-test/solution_explorer_in_vs.PNG)

1. Zakanálu svůj kód projektu do úložiště kódu projektu Azure DevOps: 

    ![Úložiště kódu projektu](./media/code-test/create_repo.PNG)

1. Předpokládejme, že jste provedli práci s přípravou dat, jako je například příjem dat, vytváření funkcí a vytváření sloupců popisků. Chcete se ujistit, že váš kód generuje očekávané výsledky. Zde je nějaký kód, který můžete použít k otestování, zda kód pro zpracování dat pracuje správně:

    * Ověřte, zda jsou názvy sloupců správné:
    
      ![Kód pro porovnání názvů sloupců](./media/code-test/check_column_names.PNG)

    * Ověřte, zda jsou úrovně odpovědi správné:

      ![Kód pro vyhovující úrovně](./media/code-test/check_response_levels.PNG)

    * Ověřte, zda je procento odezvy přijatelné:

      ![Kód pro procento odezvy](./media/code-test/check_response_percentage.PNG)

    * Ověřte chybějící rychlost každého sloupce v datech:
    
      ![Kód pro chybějící rychlost](./media/code-test/check_missing_rate.PNG)


1. Jakmile dokončíte zpracování dat a inženýrské práce a Vy jste si vyzkoušeli dobrý model, zajistěte, aby model, který jste si vyzkoušeli, mohl správně vyhodnotit nové datové sady. Pomocí následujících dvou testů můžete kontrolovat úrovně předpovědi a distribuci hodnot popisku:

    * Kontrolovat úrovně předpovědi:
    
      ![Kód pro kontrolu úrovní předpovědi](./media/code-test/check_prediction_levels.PNG)

    * Ověřte distribuci předpověď hodnot:

      ![Kód pro kontrolu hodnot předpovědi](./media/code-test/check_prediction_values.PNG)

1. Všechny testovací funkce vložte do skriptu Pythonu s názvem **test_funcs. py** :

    ![Skript Pythonu pro testovací funkce](./media/code-test/create_file_test_func.PNG)


1. Po přípravě testovacích kódů můžete nastavit testovací prostředí v sadě Visual Studio.

   Vytvořte soubor Pythonu s názvem **test1.py**. V tomto souboru vytvořte třídu, která obsahuje všechny testy, které chcete provést. Následující příklad ukazuje šest připravených testů:
    
    ![Soubor Pythonu se seznamem testů ve třídě](./media/code-test/create_file_test1_class.PNG)

1. Tyto testy mohou být automaticky zjišťovány, pokud jako název třídy vložíte **codetest. testovací případ** . Otevřete Průzkumník testů v pravém podokně a vyberte možnost **Spustit vše**. Všechny testy se spustí postupně a upozorní vás, pokud je test úspěšný, nebo ne.

    ![Spouštění testů](./media/code-test/run_tests.PNG)

1. Vraťte kód do úložiště projektu pomocí příkazů Git. Vaše nejnovější práce se projeví krátce v Azure DevOps.

    ![Příkazy Gitu pro vrácení kódu se změnami](./media/code-test/git_check_in.PNG)

    ![Nejnovější práce v Azure DevOps](./media/code-test/git_check_in_most_recent_work.PNG)

1. Nastavení automatického sestavení a testování v Azure DevOps:

    a. V úložišti projektu vyberte **sestavení a vydání** a potom vyberte **+ Nový** pro vytvoření nového procesu sestavení.

    ![Výběry pro spuštění nového procesu sestavení](./media/code-test/create_new_build.PNG)

    b. Podle pokynů vyberte umístění zdrojového kódu, název projektu, úložiště a informace o větvi.
    
    ![Informace o zdroji, názvu, úložišti a větvi](./media/code-test/fill_in_build_info.PNG)

    c. Vyberte šablonu. Vzhledem k tomu, že neexistuje žádná šablona projektu v Pythonu, Začněte výběrem možnosti **prázdný proces**. 

    ![Seznam šablon a tlačítko "prázdný proces"](./media/code-test/start_empty_process_template.PNG)

    d. Pojmenujte sestavení a vyberte agenta. Můžete zvolit výchozí, pokud chcete použít DSVM k dokončení procesu sestavení. Další informace o nastavení agentů najdete v tématu [agenti sestavení a vydání](/azure/devops/pipelines/agents/agents?view=vsts).
    
    ![Sestavení a výběr agentů](./media/code-test/select_agent.PNG)

    e. **+** V levém podokně vyberte, pokud chcete přidat úlohu pro tuto fázi sestavení. Vzhledem k tomu, že spustíme skript Pythonu **test1.py** pro dokončení všech kontrol, tato úloha používá příkaz prostředí PowerShell ke spouštění kódu Pythonu.
    
    ![Podokno přidat úkoly s vybraným prostředím PowerShell](./media/code-test/add_task_powershell.PNG)

    f. V podrobnostech PowerShellu zadejte požadované informace, například název a verzi PowerShellu. Jako typ vyberte **vložený skript** . 
    
    Do pole v části **vložený skript** můžete zadat **Python test1.py**. Ujistěte se, že je správně nastavená proměnná prostředí pro Python. Pokud potřebujete jinou verzi nebo jádro Pythonu, můžete explicitně zadat cestu, jak je znázorněno na obrázku: 
    
    ![Podrobnosti PowerShellu](./media/code-test/powershell_scripts.PNG)

    například Pokud chcete dokončit proces kanálu sestavení, vyberte **uložit & frontu** .

    ![Tlačítko "Uložit & front"](./media/code-test/save_and_queue_build_definition.PNG)

Nyní se při každém vložení nového potvrzení do úložiště kódu automaticky spustí proces sestavení. Můžete definovat libovolnou větev. Proces spustí soubor **test1.py** v počítači agenta, aby bylo zajištěno, že vše definované v kódu bude spuštěno správně. 

Pokud jsou výstrahy správně nastavené, budete po dokončení sestavení upozorněni na e-mail. Můžete také zjistit stav sestavení v Azure DevOps. Pokud dojde k chybě, můžete si prohlédnout podrobnosti o sestavení a zjistit, který kus je poškozený.

![E-mailové oznámení o úspěchu sestavení](./media/code-test/email_build_succeed.PNG)

![Oznámení o úspěchu sestavení v Azure DevOps](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Další kroky
* V tématu věnovaném testování částí v případě scénářů pro datové vědy si prohlédněte [úložiště předpovědi pro předpověď výnosů](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) .
* Sledujte předchozí osnovu a příklady ve scénáři prediktivní příjem výnosů ve vašich vlastních projektech pro datové vědy.

## <a name="references"></a>Odkazy
* [Vědecké zpracování týmových dat](./index.yml)
* [Testovací nástroje sady Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Prostředky testování Azure DevOps](https://www.visualstudio.com/team-services/)
* [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
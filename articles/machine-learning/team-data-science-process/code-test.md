---
title: Data science kód testování v Azure pomocí UCI dospělé příjem předpovědi datové sady – vědecké zpracování týmových dat a Visual Studio Team Services
description: Data science kód testování s daty předpovědi pro dospělé příjem UCI
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
ms.openlocfilehash: 7d9d63d6c3d5c8ccf1777a46832457670d307d4a
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970855"
---
# <a name="data-science-code-testing-with-the-uci-adult-income-prediction-dataset"></a>Data science kód testování s datovou sadou UCI dospělé příjem predikcí
Tento článek obsahuje pokyny pro předběžné pro testování kódu v pracovní postup datových věd. Testování poskytuje datovým vědcům systematicky a efektivní způsob, jak zkontrolovat kvality a očekávaný výsledek svůj kód. Používáme zpracování týmových dat vědy (TDSP) [projekt, který používá datovou sadu pro dospělé příjem UCI](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) , kterou jsme publikovali dříve ukazují, jak kód testování lze provést. 

## <a name="introduction-on-code-testing"></a>Úvod k testování kódu
"Testování částí" je dlouhodobě postupem pro vývoj softwaru. Ale pro datové vědy, není často jasné, to znamená, že a jak byste měli otestovat kód pro různé fáze životního cyklu datové vědy, jako:

* Příprava dat
* Zkoumání dat kvality
* Modelování
* Nasazení modelu 

Tento článek nahradí termín "unit testing" s "testování kódu." Odkazuje na testování jako funkce, které pomáhají vyhodnotit, jestli kód pro krok životního cyklu datové vědy je vytváření výsledků "podle očekávání." Osoba, která zapisuje testu definuje, co je "podle očekávání," v závislosti na výsledek funkce – například, kontrola kvality dat nebo modelování.

Tento článek obsahuje odkazy na jako užitečné zdroje informací.

## <a name="visual-studio-team-services-for-the-testing-framework"></a>Visual Studio Team Services pro testovací rozhraní
Tento článek popisuje, jak provádět a automatizovat testování pomocí Visual Studio Team Services (VSTS). Můžete se rozhodnout používat alternativní nástroje. Také vám ukážeme, jak nastavit automatické sestavení pomocí VSTS a agenty sestavení. Agenti sestavení se používá Azure virtuální počítače pro datové vědy (datové).

## <a name="flow-of-code-testing"></a>Tok testování kódu
Celkový pracovní postup testování kódu v projektu datové vědy vypadá takto: 

![Vývojový diagram testování kódu](./media/code-test/test-flow-chart.PNG)

    
## <a name="detailed-steps"></a>Podrobné kroky

Použijte následující postup k nastavení a spuštění kódu, testování a automatizované sestavování pomocí agenta sestavení a VSTS:

1. Vytvořte projekt aplikace klasické pracovní plochy Visual Studio:

    !["Vytvořit nový projekt" obrazovky v aplikaci Visual Studio](./media/code-test/create_project.PNG)

   Po vytvoření projektu, najdete ho v Průzkumníku řešení v pravém podokně:
    
    ![Kroky pro vytvoření projektu](./media/code-test/create_python_project_in_vs.PNG)

    ![Průzkumník řešení](./media/code-test/solution_explorer_in_vs.PNG)

3. Informační kanál projekt kódu do úložiště kódu projektu VSTS: 

    ![Úložiště kódu projektu](./media/code-test/create_repo.PNG)

4. Předpokládejme, že jste provedli některé práci na přípravě dat, jako je příjem dat, vytváření funkcí a vytváření sloupce popisek. Chcete zajistit, aby že váš kód generuje výsledky, které jste očekávali. Zde je kód, který můžete použít k ověření, zda správnou funkci kódu zpracování dat:

    * Zkontrolujte, že názvy sloupců jsou nejvhodnější:
    
      ![Kód pro odpovídající názvy sloupců](./media/code-test/check_column_names.PNG)

    * Zkontrolujte, že jsou správné odpovědi úrovně:

      ![Kód pro odpovídající úrovně](./media/code-test/check_response_levels.PNG)

    * Zkontrolujte, že je přijatelné procento odpovědí:

      ![Kód pro procento odpovědí](./media/code-test/check_response_percentage.PNG)

    * Chybějící míru každého sloupce v datech zkontrolujte:
    
      ![Kód pro chybějící kurz](./media/code-test/check_missing_rate.PNG)


5. Poté, co jste provedli zpracovávaných dat a technické práce na funkcích a natrénovali dobrý model, ujistěte se, že model, který jste školení může správně skóre nové datové sady. Ke kontrole úrovně předpovědi a distribuci hodnot popisek můžete použít následující dva testy:

    * Zkontrolujte předpovědi úrovně:
    
      ![Kód pro kontrolu úrovně predikcí](./media/code-test/check_prediction_levels.PNG)

    * Zkontrolujte distribuci předpovědi hodnot:

      ![Kód pro kontrolu hodnoty predikcí](./media/code-test/check_prediction_values.PNG)

6. Vložit všechny dohromady testovací funkce do skriptu v jazyce Python volá **test_funcs.py**:

    ![Skript v jazyce Python pro testování funkcí](./media/code-test/create_file_test_func.PNG)


7. Po testu kódy jsou připravené, můžete nastavit testovací prostředí v sadě Visual Studio.

   Vytvořte soubor Pythonu s názvem **test1.py**. V tomto souboru vytvořte třídu, která obsahuje všechny testy, které chcete provést. Následující příklad ukazuje šest testy připravit:
    
    ![Soubor se seznamem testů ve třídě v jazyce Python](./media/code-test/create_file_test1_class.PNG)

8. Tyto testy lze automaticky zjistit když vložíte **codetest.testCase** za názvem své třídy. V pravém podokně otevřete Průzkumníka testů a vyberte **spustit všechny**. Všechny testy se spouští sekvenčně a dozvíte se, pokud test je úspěšný, nebo ne.

    ![Spuštění testů](./media/code-test/run_tests.PNG)

9. Vrácení kódu se změnami do úložiště projektu pomocí příkazů Gitu. Nejnovější práce se projeví za chvíli ve VSTS.

    ![Příkazy Gitu pro vrácení kódu se změnami](./media/code-test/git_check_in.PNG)

    ![Nejnovější práce ve VSTS](./media/code-test/git_check_in_most_recent_work.PNG)

10. Nastavení automatické sestavení a testování ve VSTS:

    a. V úložišti projektů vyberte **sestavení a vydání**a pak vyberte **+ nová** k vytvoření nového procesu sestavení.

       ![Výběry pro spuštění nového procesu sestavení](./media/code-test/create_new_build.PNG)

    b. Postupujte podle výzev a vyberte umístění zdrojového kódu, název projektu, úložiště a větve informace.
    
       ![Zdroj, název, úložiště a větve informace](./media/code-test/fill_in_build_info.PNG)

    c. Vyberte šablonu. Protože neexistuje žádná šablona projektu Pythonu, začněte výběrem **prázdný proces**. 

       ![Seznam šablon a tlačítko "Prázdný procesu"](./media/code-test/start_empty_process_template.PNG)

    d. Název sestavení a vyberte agenta. Výchozí Tady můžete zvolit, pokud chcete použít DSVM pro dokončení procesu sestavení. Další informace o nastavení agentů najdete v tématu [agenti sestavení a vydání](https://docs.microsoft.com/vsts/build-release/concepts/agents/agents?view=vsts).
    
       ![Vybrané sestavení a agenta](./media/code-test/select_agent.PNG)

    e. Vyberte **+** v levém podokně, chcete-li přidat úlohu v této fázi sestavení. Protože jsme si teď spustíme skript Pythonu **test1.py** dokončit všechny kontroly, tato úloha je pomocí příkazu Powershellu na spuštění kódu Pythonu.
    
       !["Přidat úkoly" podokno s vybrané prostředí PowerShell](./media/code-test/add_task_powershell.PNG)

    f. V podrobnostech o prostředí PowerShell zadejte požadované informace, jako jsou název a verze prostředí PowerShell. Zvolte **zpracování vloženého skriptu** jako typ. 
    
       Do pole pod **zpracování vloženého skriptu**, můžete zadat **python test1.py**. Ujistěte se, že je proměnná prostředí je správně nastavené pro Python. Pokud potřebujete jinou verzi nebo jádra jazyka Python, můžete explicitně určit cestu, jak je znázorněno na obrázku: 
    
       ![Podrobnosti o prostředí PowerShell](./media/code-test/powershell_scripts.PNG)

    g. Vyberte **Uložit & frontu** k dokončení definice procesu sestavení.

       ![Tlačítko "Uložit & fronty"](./media/code-test/save_and_queue_build_definition.PNG)

Nyní pokaždé, když nové potvrzení se vloží do úložiště kódu, proces sestavení se spustí automaticky. (Tady používáme hlavní jako úložiště, ale můžete definovat žádné větve.) Proces je spuštěn **test1.py** souboru v počítači agenta, abyste měli jistotu, že všechno, co je definována v kódu pracuje správně. 

Pokud výstrahy nejsou nastaveny správně, budete upozorněni v e-mailu po dokončení sestavení. Můžete také zkontrolovat stav sestavení ve VSTS. Pokud selže, můžete zkontrolovat informace o sestavení a zjistit, jaký je poškozený.

![E-mailové oznámení úspěch sestavení](./media/code-test/email_build_succeed.PNG)

![VSTS oznámení úspěch sestavení](./media/code-test/vs_online_build_succeed.PNG)

## <a name="next-steps"></a>Další postup
* Najdete v článku [UCI příjem předpovědi úložiště](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) konkrétní příklady testů jednotek pro datové vědy scénáře.
* Postupujte podle předchozích osnovy a příklady z scénáře UCI příjem předpovědi ve své vlastní projekty datových věd.

## <a name="references"></a>Odkazy
* [Vědecké zpracování týmových dat](https://aka.ms/tdsp)
* [Testovací nástroje sady Visual Studio](https://www.visualstudio.com/vs/features/testing-tools/)
* [Testování prostředků VSTS](https://www.visualstudio.com/team-services/)
* [Virtuální počítače pro datové vědy](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)
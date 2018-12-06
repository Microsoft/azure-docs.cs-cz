---
title: Runbook Output and Messages ve službě Azure Automation
description: Popisuje postup vytvoření a načtení výstupu a chybové zprávy ze sady runbook ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cc1ef2a3ab09ec5b86d1dc0b4c139afd43ba356d
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52969120"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Sada Runbook výstup a zprávy ve službě Azure Automation
Většina runbooků služeb automatizace Azure mají určitou formu výstupu. Tento výstup může být chybová zpráva pro uživatele nebo složitý objekt, že máte v úmyslu používat s jinou sadou runbook. Prostředí Windows PowerShell poskytuje [různých datových proudů](/powershell/module/microsoft.powershell.core/about/about_redirection) odesílat výstup ze skriptu nebo pracovního postupu. Azure Automation funguje s každou z těchto datových proudů jinak. Postupujte podle osvědčené postupy pro jejich používání při vytváření sady runbook.

Následující tabulka obsahuje stručný popis jednotlivých datových proudů a jejich chování na webu Azure Portal pro publikované sady runbook a kdy [testování runbooku](automation-testing-runbook.md). Další podrobnosti o jednotlivých datových proudech jsou k dispozici v předchozích částech.

| Datový proud | Popis | Publikováno | Test |
|:--- |:--- |:--- |:--- |
| Výstup |Objekty, které mají zpracovávat jiné runbooky. |Zapíšou se do historie úlohy. |Zobrazí v podokně výstup testu. |
| Upozornění |Upozornění určené pro uživatele. |Zapíšou se do historie úlohy. |Zobrazí v podokně výstup testu. |
| Chyba |Chybová zpráva určená pro uživatele. Na rozdíl od výjimky runbook pokračuje po chybové zprávě ve výchozím nastavení. |Zapíšou se do historie úlohy. |Zobrazí v podokně výstup testu. |
| Podrobný |Zprávy, které poskytují obecné nebo ladicí informace. |Zapíšou se do historie úlohy, jenom v případě, že je pro runbook vypnuté podrobné protokolování. |V podokně výstup testu zobrazí jenom v případě, že $VerbosePreference nastavená na pokračovat v sadě runbook. |
| Průběh |Záznamy automaticky generované před a za každou aktivitu v sadě runbook. Runbook by se neměl pokoušet vytvořit vlastní záznamy průběhu, protože jsou určené pro interaktivního uživatele. |Zapíšou se do historie úlohy, jenom v případě, že je pro runbook vypnuté protokolování průběhu. |Nezobrazuje se v podokně výstup testu. |
| Ladění |Zprávy určené pro interaktivního uživatele. Nesmí se používat v runboocích. |Nezapíše se do historie úlohy. |Nezapíše se do podokna výstup testu. |

## <a name="output-stream"></a>Výstupní stream
Výstupní datový proud je určený pro výstup objektů, které jsou vytvořeny pomocí skriptu nebo pracovního postupu při správném spuštění. Ve službě Azure Automation, tento datový proud používá primárně u objektů, který se má používat podle [nadřazené sady runbook, které volají aktuální runbook](automation-child-runbooks.md). Pokud jste [voláte přiřazený runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) z nadřízeného runbooku, vrátí data z výstupního datového proudu na nadřazený prvek. Výstupní datový proud lze použijte pouze ke sdělování informací uživateli, pokud víte, že sada runbook je nikdy volat žádný jiný runbook. Jako osvědčený postup, ale obvykle používejte [podrobné Stream](#verbose-stream) ke sdělování informací uživateli.

Umožňuje zápis dat do výstupního datového proudu pomocí [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) nebo vložením objektu na samostatném řádku v sadě runbook.

```PowerShell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Výstup z funkce
Při zápisu do výstupního datového proudu ve funkci, která je zahrnutá ve vašem runbooku, výstup se předá zpátky do runbooku. Pokud runbook přidá tento výstup k proměnné, nezapíše se do výstupního datového proudu. Zápis do jiných datových proudů z v rámci funkce zapíše do odpovídajícího datového proudu pro runbook.

Vezměte v úvahu následující ukázkové sady runbook:

```PowerShell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Výstupní datový proud pro runbook by byl:

```output
Output inside of function
Output outside of function
```

Podrobný datový proud pro runbook by byl:

```output
Verbose outside of function
Verbose inside of function
```

Po publikování runbooku a než ho začnete, je třeba také zapnout podrobné protokolování, v nastavení sady runbook k získání výstupu podrobný datový proud.

### <a name="declaring-output-data-type"></a>Deklarující výstupní datový typ
Pracovní postup může určovat datový typ svého výstupu pomocí [atributu OutputType](https://technet.microsoft.com/library/hh847785.aspx). Tento atribut nemá žádný vliv za běhu, ale poskytuje údaj autorovi runbooku v době návrhu očekávaný výstup runbooku. Sada nástrojů pro runbooky stále vyvíjí a význam deklarování výstupních datových typů v době návrhu zvýší se význam. V důsledku toho je nejlepší zahrnout tuto deklaraci ve vytvářených runbooků.

Tady je seznam příklad výstupní typy:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Následující vzorový runbook výstup objektu řetězce a zahrnuje deklaraci jeho typu výstupu. Pokud vaše sada runbook jako výstup pole určitého typu, byste měli stále zadat typ na rozdíl od pole typu.

```PowerShell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Chcete-li deklarovat výstupní typ v sadách runbook grafický nebo grafický Powershellový pracovní postup, můžete vybrat **vstupní a výstupní** nabídky a zadejte název výstupního typu. Doporučuje se, že používáte úplný název třídy .NET, aby ho jednoduše rozpoznatelným názvem při odkazování z nadřízeného runbooku. To zpřístupní všechny vlastnosti této třídy do datové sběrnice v sadě runbook a poskytuje mnoho flexibilitu při jejich používání pro podmíněnou logiku, protokolování a odkazování na ně jako hodnoty pro další aktivity v sadě runbook.<br> ![Možnost vstup z Runbooku a výstup](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

V následujícím příkladu máte dvě grafických runbooků pro ukázku této funkce. Pokud použijete model návrhu modulární sady runbook, budete mít jeden runbook, který slouží jako *šablonu sady Runbook ověřování* správu ověřování s Azure pomocí účtu spustit jako. Naše druhý runbook, který by obvykle provést základní logiku pro automatizaci daném scénáři, v tomto případě se to provést *šablonu sady Runbook ověřování* a zobrazit výsledky do vaší **Test** Podokno výstup. Za normálních okolností byste měli tuto sadu runbook udělat něco, s prostředkem využití výstup podřízeného runbooku.

Tady je základní logiku **AuthenticateTo Azure** sady runbook.<br> ![Ověření šablony sad Runbook příklad](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Obsahuje typ výstupu *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, která vrací ověřování vlastnosti profilu.<br> ![Příklad typu výstup Runbooku](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Zatímco tato sada runbook je snadná, je jednu položku konfigurace, které vyvolají tady. Poslední aktivita provádí **Write-Output** rutiny a zapisuje data profilu k proměnné $_ Powershellový výraz pro použití **Inputobject** parametru, která je potřebná pro tuto rutinu.  

Pro druhou sadu runbook v tomto příkladu, s názvem *testovací ChildOutputType*, jednoduše máte dvě aktivity.<br> ![Příklad podřízených výstupní typ Runbooku](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

První volání aktivity **AuthenticateTo Azure** sady runbook a druhá aktivita běží **Write-Verbose** rutinu s **zdroj dat** z  **Výstup aktivity** a hodnotu **cesta pole** je **Context.Subscription.SubscriptionName**, což je určení kontextu výstup  **AuthenticateTo Azure** sady runbook.<br> ![Rutina Write-Verbose parametr zdroje dat](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

Výsledný výstup je název předplatného.<br> ![Výsledky testu ChildOutputType sady Runbook](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

Jeden poznámek o chování ovládacího prvku typu výstupu. Když zadáte hodnotu v poli Typ výstupu v okně vlastností vstup a výstup, budete muset klikněte na tlačítko mimo ovládací prvek po zadání, aby se položka podle ovládacího prvku.  

## <a name="message-streams"></a>Datové proudy zprávy
Na rozdíl od do výstupního datového proudu jsou datové proudy zprávy určené ke sdělování informací uživateli. Existují různé datové proudy zpráv pro různé druhy informací a každý jsou zpracována jinak než Azure Automation.

### <a name="warning-and-error-streams"></a>Datové proudy upozornění a chyby
Datové proudy upozornění a chyb jsou určené k protokolování problémů, ke kterým dochází v sadě runbook. Pokud sada runbook spouští a jsou zahrnuty v podokně výstup testu na portálu Azure portal, při testování sady runbook jsou zapsané do historie úlohy. Ve výchozím nastavení sada runbook bude provádět i po upozornění a chyby. Můžete určit, že sada runbook pozastaví při varování nebo chybě nastavením [preferenční proměnné](#preference-variables) v runbooku před vytvořením zprávy. Například chcete-li způsobit, že se runbook v případě chyby jako výjimky, nastavte **$ErrorActionPreference** k zastavení.

Vytvořte upozornění nebo chybovou zprávu pomocí [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) nebo [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) rutiny. Do těchto datových proudů můžou zapisovat taky aktivity.

```PowerShell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Podrobný datový proud
Obecné informace o činnosti runbooku je datový proud podrobnou zprávu. Vzhledem k tomu, [Stream ladění](#debug-stream) není k dispozici v sadě runbook, podrobné zprávy se použije pro ladicí informace. Ve výchozím nastavení podrobné zprávy z publikovaných runbooků nejsou uloženy v historii úlohy. Pokud chcete tyto zprávy uložit, nakonfigurujte publikované runbooky na protokolovat podrobné záznamy na kartě Konfigurace sady runbook na portálu Azure portal. Ve většině případů byste měli zachovat výchozí nastavení neprotokolování podrobných záznamů pro runbook z důvodů výkonu. Zapněte tuto možnost jenom pro řešení problémů nebo ladění runbooku.

Když [testování runbooku](automation-testing-runbook.md), podrobné zprávy nezobrazují, i když je runbook nakonfigurovaný na protokolování podrobných záznamů. Chcete-li zobrazit podrobné zprávy při [testování runbooku](automation-testing-runbook.md), je nutné nastavit proměnnou $VerbosePreference na pokračovat. Pomocí této proměnné sady podrobné zprávy se zobrazují v podokně výstup testu na portálu Azure Portal.

Vytvoření podrobné zprávy použijte [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) rutiny.

```PowerShell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Datový proud ladění
Datový proud ladění je určena pro použití s interaktivním uživatelem a nesmí se používat v runboocích.

## <a name="progress-records"></a>Záznamy o průběhu
Pokud nakonfigurujete záznamy sady runbook a protokolování průběhu (na kartě Konfigurace sady runbook na portálu Azure portal) a záznam bude zapsána do historie úlohy, před a po spuštění každé aktivity. Ve většině případů byste měli zachovat výchozí nastavení neprotokolování záznamů o průběhu pro runbook aby se maximalizoval výkon. Zapněte tuto možnost jenom pro řešení problémů nebo ladění runbooku. Při testování runbooku se zprávy o průběhu nezobrazují, i když je runbook nakonfigurovaný na protokolování záznamů o průběhu.

[Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) rutina není platný v sadě runbook, protože tato rutina je určena pro použití s interaktivním uživatelem.

## <a name="preference-variables"></a>Proměnné předvoleb
Prostředí Windows PowerShell používá [proměnné předvoleb](https://technet.microsoft.com/library/hh847796.aspx) určit, jak reagovat na data odeslaná do různých výstupních datových proudů. Tyto proměnné můžete nastavit v sadě runbook řídit, jak reagovat na data zasílaná do různých datových proudů.

Následující tabulka obsahuje seznam proměnných předvoleb, které lze použít v runboocích používat s platnými a výchozími hodnotami. Tato tabulka obsahuje jenom hodnoty, které jsou platné v runbooku. Další hodnoty jsou platné pro proměnné předvoleb při použití v prostředí Windows PowerShell mimo Azure Automation.

| Proměnná | Výchozí hodnota | Platné hodnoty |
|:--- |:--- |:--- |
| WarningPreference |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| ErrorActionPreference |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Zastavit<br>Pokračovat<br>SilentlyContinue |

Následující tabulka uvádí chování pro hodnoty proměnných předvoleb, které jsou platné v sadách runbook.

| Hodnota | Chování |
|:--- |:--- |
| Pokračovat |Zaprotokoluje zprávu a pokračuje v provádění runbooku. |
| SilentlyContinue |Pokračuje v provádění runbooku bez protokolování zprávy. Tato hodnota je ignorování zprávy. |
| Zastavit |Zaprotokoluje zprávu a pozastaví runbook. |

## <a name="runbook-output"></a>Načítají se výstup a zprávy runbooku
### <a name="azure-portal"></a>portál Azure
Podrobnosti úlohy runbooku můžete zobrazit na webu Azure Portal na kartě úlohy sady runbook. Souhrn úlohy zobrazí vstupní parametry a [výstupní Stream](#output-stream) také obecné informace o úloze a případné výjimky, pokud k nim došlo. Zahrnuje zprávy z historie [výstupní Stream](#output-stream) a [upozornění a chyby datové proudy](#warning-and-error-streams) i na [podrobné Stream](#verbose-stream) a [záznamů o průběhu](#progress-records) Pokud je runbook nakonfigurovaný na protokolování podrobných záznamů a záznamy o průběhu.

### <a name="windows-powershell"></a>Windows PowerShell
V prostředí Windows PowerShell můžete načítat výstup a zprávy z runbooku pomocí [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) rutiny. Tato rutina vyžaduje ID úlohy a má parametr nazvaný Stream, kde můžete určit, který datový proud se vraťte. Můžete zadat **jakékoli** vrátit všechny datové proudy úlohy.

Následující příklad spouští vzorový runbook a potom čeká na její dokončení. Po dokončení je jeho výstupní datový proud shromáždí z úlohy.

```PowerShell
$job = Start-AzureRmAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzureRmAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzureRmAutomationJobOutput to Get-AzureRmAutomationJobOutputRecord
Get-AzureRmAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzureRmAutomationJobOutputRecord
``` 

### <a name="graphical-authoring"></a>Vytváření grafického obsahu
Grafické runbooky dodatečné protokolování je k dispozici ve formě trasování na úrovni aktivity. Existují dvě úrovně trasování: Basic a Detailed. V základní trasování, zobrazí se počáteční a koncový čas každé aktivity v sadě runbook a informace týkající se jakékoli aktivity opakování. Některé příklady jsou počtem pokusů a počáteční čas aktivity. V podrobné trasování, získáte plus základní trasování vstupní a výstupní data pro každou aktivitu. Aktuálně záznamy trasování jsou zapsány pomocí podrobný datový proud, takže je potřeba povolit podrobné protokolování, když je povoleno trasování. Grafické runbooky s povoleným trasováním není nutné na protokolování záznamů o průběhu. Základní trasování slouží stejnému účelu a je více informacemi.

![Grafické vytváření úlohy streamování zobrazení](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Vidíte na předchozím snímku obrazovky, že pokud povolíte podrobné protokolování a trasování pro grafické runbooky, mnohem více informace jsou k dispozici v produkčním prostředí zobrazit datové proudy úlohy. Tyto dodatečné informace může být nezbytné pro řešení potíží s problémy v produkčním prostředí pomocí sady runbook, a proto byste měli povolit pouze ho k tomuto účelu a ne jako obecně platí. Může být zvláště mnoha záznamy trasování. Pomocí trasování grafického runbooku můžete získat dva až čtyři záznamy na aktivitu v závislosti na tom, jestli jste nakonfigurovali základním nebo podrobném trasování. Pokud potřebujete tyto informace můžete sledovat postup prací sady runbook pro řešení potíží, můžete chtít zachovat trasování vypnuté.

**Pokud chcete povolit trasování na úrovni aktivity, postupujte následovně:**

1. Na webu Azure Portal otevřete účet Automation.
2. V části **automatizace procesů**vyberte **sady Runbook** otevřete seznam runbooků.
3. Na stránce sady Runbook kliknutím vyberte grafický runbook ze seznamu sad runbook.
4. V části **nastavení**, klikněte na tlačítko **protokolování a trasování**.
5. Na protokolování a trasování stránce v části protokolovat podrobné záznamy, klikněte na tlačítko **na** zapnout podrobné protokolování a v části sledování na úrovni aktivity, změnit úroveň trasování pro **základní** nebo **podrobné** vychází z úrovně trasování požadavku.<br>
   
   ![Grafické vytváření obsahu protokolování a trasování stránky](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-log-analytics"></a>Microsoft Azure Log Analytics
Automatizace můžete odeslat runbook datové proudy úlohy stavu a úlohu do pracovního prostoru Log Analytics. Pomocí služby Log Analytics je to možné,

* Získejte přehled o vašich úloh služby Automation 
* Aktivační událost e-mailem nebo výstrahy založené na váš stav úlohy runbooku (například chybných nebo pozastavených) 
* Zápis upřesňující dotazy napříč vaší datové proudy úlohy 
* Propojte úlohy napříč účty služby Automation 
* Vizualizovat historii úloh v čase    

Další informace o tom, jak konfigurovat integraci s Log Analytics shromažďovat, korelovat a reagovat na data úlohy najdete v tématu [předávání stavu úlohy a datové proudy úlohy ze služby Automation do Log Analytics](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Další postup
* Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
* Návrh a použít podřízené runbooky najdete v tématu [podřízené runbooky ve službě Azure Automation](automation-child-runbooks.md)


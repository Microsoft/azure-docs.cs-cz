---
title: Výstup a zprávy v Azure Automation Runbooku
description: Popisuje postup vytvoření a načtení výstupní zařízení a chybové zprávy ze sady runbook ve službě Azure Automation.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: d4931c710bebc5e6c3ee23fb58e1432bb86da4a5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Výstup a zprávy ve službě Azure Automation Runbooku
Většina runbooků služeb automatizace Azure má určitou formu výstupu, jako je chybová zpráva pro uživatele nebo složitý objekt určené pro jiný pracovní postup. Prostředí Windows PowerShell poskytuje [víc datových proudů](http://blogs.technet.com/heyscriptingguy/archive/2014/03/30/understanding-streams-redirection-and-write-host-in-powershell.aspx) pro odeslání výstupu z skript nebo pracovního postupu. Služby Azure Automation funguje s každou z těchto datových proudů jinak a postupujte podle osvědčené postupy pro jejich používání při vytváření sady runbook.

Následující tabulka obsahuje stručný popis jednotlivých datových proudů a jejich chování na portálu Azure při spuštění publikovaného runbooku a při [testování runbooku](automation-testing-runbook.md). Další podrobnosti o jednotlivých datových proudech jsou uvedeny v následujících částech.

| Stream | Popis | Publikováno | Test |
|:--- |:--- |:--- |:--- |
| Výstup |Objekty, které mají zpracovávat jiné runbooky. |Zapíšou se do historie úlohy. |Zobrazí v podokně výstup testu. |
| Upozornění |Upozornění určené pro uživatele. |Zapíšou se do historie úlohy. |Zobrazí v podokně výstup testu. |
| Chyba |Chybová zpráva určená pro uživatele. Na rozdíl od výjimky runbook pokračuje po chybovou zprávu ve výchozím nastavení. |Zapíšou se do historie úlohy. |Zobrazí v podokně výstup testu. |
| Podrobné |Zprávy, které poskytují informace o obecných nebo ladění. |Zapíšou se do historie úlohy, jenom v případě, že je pro runbook vypnuté podrobné protokolování. |Zobrazí v podokně výstup testu, jen pokud $VerbosePreference je nastavena na pokračovat v sadě runbook. |
| Průběh |Záznamy automaticky generované před a po každé aktivitě v sadě runbook. Runbook se neměli pokoušet vytvořit vlastní záznamy průběhu, protože ty jsou určené pro interaktivního uživatele. |Zapíšou se do historie úlohy, jenom v případě, že je pro runbook vypnuté protokolování průběhu. |Nezobrazuje se v podokně výstup testu. |
| Ladění |Zprávy určené pro interaktivního uživatele. Nesmí se používat v runboocích. |Nezapíše se do historie úlohy. |Nezapíše se do podokna výstup testu. |

## <a name="output-stream"></a>Výstupní stream
Výstupní datový proud je určený pro výstup objektů vytvořených skript nebo pracovního postupu při správném spuštění. Ve službě Azure Automation, tento datový proud používá primárně u objektů, které mají být využívány službou [nadřazené sady runbook, které volají aktuální runbook](automation-child-runbooks.md). Pokud jste [voláte přiřazený runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) z nadřízeného runbooku, vrátí data z výstupního datového proudu do nadřazené. Výstupní datový proud byste měli používat jenom ke sdělování informací uživateli, pokud víte, že sada runbook je nikdy volány jinou sadou runbook. Jako osvědčený postup, ale by měl obvykle použijete [podrobný datový proud](#Verbose) ke sdělování informací uživateli.

Můžete zapsat data do výstupního datového proudu pomocí [Write-Output](http://technet.microsoft.com/library/hh849921.aspx) nebo vložením objektu na vlastním řádku v sadě runbook.

```PowerShell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Výstup z funkce
Při zápisu do výstupního datového proudu ve funkci, která je zahrnutá ve vašem runbooku, výstup se předá zpět do runbooku. Pokud runbook přidá tento výstup do proměnné, nezapíše se do výstupního datového proudu. Zápis do jiných datových proudů z v rámci funkce zapíše do odpovídajícího datového proudu pro runbook.

Vezměte v úvahu následující vzorový runbook:

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

    Output inside of function
    Output outside of function

Podrobný datový proud pro runbook by byl:

    Verbose outside of function
    Verbose inside of function

Po publikování sady runbook a než ho začnete, je nutné také zapnout podrobné protokolování v nastavení sady runbook, aby bylo možné získat výstup podrobný datový proud.

### <a name="declaring-output-data-type"></a>Deklarující výstupní datový typ
Pracovní postup může určovat datový typ svého výstupu pomocí [atributu OutputType](http://technet.microsoft.com/library/hh847785.aspx). Tento atribut nemá žádný vliv za běhu, ale indikuje autorovi runbooku v době návrhu očekávaný výstup runbooku. Jako sada nástrojů pro runbooky stále vyvíjí, zvýší se význam deklarování výstupních datových typů v době návrhu se. V důsledku toho je nejlepší zahrnout tuto deklaraci v jakékoli sady runbook, který vytvoříte.

Tady je seznam příklad výstupu typy:

* System.String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Následující vzorový runbook výstup objektu řetězce a zahrnuje deklaraci jeho typu výstupu. Pokud runbook jako výstup pole určitého typu, byste měli stále zadat typ a pole typu.

```PowerShell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Chcete-li deklarovat výstupní typ v sadách runbook grafický nebo grafické prostředí PowerShell Workflow, můžete vybrat **vstup a výstup** nabídky možnost a zadejte název výstupní typ. Doporučuje se, že používáte úplný název třídy rozhraní .NET, aby bylo snadno identifikovat při odkazování z nadřízeného runbooku. To zpřístupní všechny vlastnosti třídy do datové sběrnice v sadě runbook a poskytuje mnohem flexibilitu při jejich používání pro podmíněnou logiku, protokolování a odkazování na jako hodnoty pro další aktivity v sadě runbook.<br> ![Možnost Runbook vstup a výstup](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

V následujícím příkladu máte dvě grafické runbooky k předvedení tuto funkci. Pokud použijete model návrhu modulární runbook, máte jedné sady runbook, která slouží jako *šablony sad Runbook ověřování* správu ověřování s Azure pomocí účtu spustit jako. Druhý runbook, která by za normálních okolností provést základní logika pro automatizaci v daném scénáři, v takovém případě se bude spuštěn *šablony sad Runbook ověřování* a zobrazit výsledky do vaší **Test** podokno výstup. Za normálních okolností byste měli tuto sadu runbook dělat něco proti prostředek využití výstup podřízeného runbooku.    

Tady je základní logiku **AuthenticateTo Azure** sady runbook.<br> ![Ověření šablony sad Runbook příklad](media/automation-runbook-output-and-messages/runbook-authentication-template.png).  

Obsahuje výstupní typ *Microsoft.Azure.Commands.Profile.Models.PSAzureContext*, která vrací ověřování vlastnosti profilu.<br> ![Příklad výstupu Runbook typu](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png) 

Tato sada runbook je přímo dál, není jednu položku konfigurace vyvolávající sem. Poslední aktivita spouští **Write-Output** rutiny a zapisuje data profilu $_ proměnné pomocí prostředí PowerShell výrazu pro **Inputobject** parametr, který je vyžadován pro tuto rutinu.  

Druhý sady runbook v tomto příkladu, s názvem *Test ChildOutputType*, stačí dvě aktivity.<br> ![Příklad podřízených výstupní typ Runbooku](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png) 

První volání aktivity **AuthenticateTo Azure** runbook a druhá aktivita běží **Write-Verbose** rutiny s **zdroj dat** z **výstup aktivity** a hodnota **cesta pole** je **Context.Subscription.SubscriptionName**, což je určení kontextu výstup z **AuthenticateTo Azure** sady runbook.<br> ![Rutiny Write-Verbose parametr zdroje dat](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)    

Výsledný výstup je název odběru.<br> ![Výsledky testu ChildOutputType sady Runbook](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

Jeden Poznámka o chování ovládacího prvku typu výstupu. Když zadáte hodnotu v poli typu výstupu v okně vlastností vstup a výstup, budete muset klikněte na tlačítko mimo ovládací prvek po, zadejte v pořadí pro zadání rozpoznala ovládacího prvku.  

## <a name="message-streams"></a>Datové proudy zprávy
Na rozdíl od do výstupního datového proudu jsou datové proudy zprávy určené ke sdělování informací uživateli. Existují různé datové proudy zpráv pro různé typy informací a každý jinak zpracovávaných Azure Automation.

### <a name="warning-and-error-streams"></a>Datové proudy upozornění a chyb
Datové proudy upozornění a chyb jsou určené k protokolování problémů, ke kterým došlo v sadě runbook. Když runbook se spustí a jsou zahrnuty v podokně výstup testu na portálu Azure při testování sady runbook jsou zapsané do historie úlohy. Ve výchozím nastavení bude sada runbook pokračovat v provádění po upozornění a chyby. Můžete určit, že sada runbook by měla být pozastavena na upozornění nebo chyby [preferenční proměnné](#PreferenceVariables) v runbooku před vytvořením zprávy. Například, aby se runbook pozastavil při chybě stejně jako v výjimku, nastavte **$ErrorActionPreference** k zastavení.

Vytvořte upozornění nebo chybovou zprávu pomocí [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) nebo [Write-Error](http://technet.microsoft.com/library/hh849962.aspx) rutiny. Do těchto datových proudů můžou zapisovat taky aktivity.

```PowerShell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Podrobný datový proud
Datový proud podrobnou zprávu je obecné informace o činnosti runbooku. Vzhledem k tomu [datový proud ladění](#Debug) není k dispozici v sadě runbook, podrobné zprávy se mají použít pro informace o ladění. Ve výchozím nastavení podrobné zprávy z publikovaných runbooků neukládá do historie úlohy. Pokud chcete tyto zprávy uložit, nakonfigurujte publikované runbooky na protokolovat podrobné záznamy na kartě Konfigurace sady runbook na portálu Azure. Ve většině případů byste měli mít ve výchozím nastavení není protokolování podrobných záznamů pro runbook z důvodů výkonu. Zapněte tato možnost jenom pro vyřešení problémů nebo ladění runbooku.

Když [testování runbooku](automation-testing-runbook.md), podrobné zprávy nezobrazují, i když je runbook nakonfigurovaný na protokolování podrobných záznamů. Chcete-li zobrazit podrobné zprávy při [testování runbooku](automation-testing-runbook.md), je nutné nastavit proměnnou $VerbosePreference na pokračovat. Pomocí tohoto nastavení proměnné podrobné zprávy se zobrazují v podokně výstup testu na portálu Azure.

Vytvoření podrobné zprávy použijte [Write-Verbose](http://technet.microsoft.com/library/hh849951.aspx) rutiny.

```PowerShell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Datový proud ladění
Datový proud ladění je určený pro použití s interaktivním uživatelem a nesmí se používat v runboocích.

## <a name="progress-records"></a>Záznamů o průběhu
Pokud nakonfigurujete sady runbook do protokolu průběh zaznamenává (na kartě Konfigurace sady runbook na portálu Azure) a záznam budou zapisovat do historie úlohy před a po spuštění každé aktivity. Ve většině případů byste měli mít ve výchozím nastavení není protokolování záznamů o průběhu pro sady runbook, aby maximalizovat výkon. Zapněte tato možnost jenom pro vyřešení problémů nebo ladění runbooku. Při testování runbooku se zprávy o průběhu nezobrazují, i když je runbook nakonfigurovaný na protokolování záznamů o průběhu.

[Write-Progress](http://technet.microsoft.com/library/hh849902.aspx) rutiny není platný v sadě runbook, protože je určená pro použití s interaktivním uživatelem.

## <a name="preference-variables"></a>Proměnné předvoleb
Prostředí Windows PowerShell používá [proměnné předvoleb](http://technet.microsoft.com/library/hh847796.aspx) určit, jak reagovat na data odeslaná do různých výstupních datových proudů. Tyto proměnné můžete nastavit v sadě runbook řídit, jak reagovat na data zasílaná do různých datových proudů.

Následující tabulka obsahuje seznam proměnných předvoleb, které mohou být používány sady runbook s platnými a výchozími hodnotami. Tato tabulka obsahuje jenom hodnoty, které jsou platné v runbooku. Další hodnoty jsou platné pro proměnné předvoleb při použití v prostředí Windows PowerShell mimo Azure Automation.

| Proměnná | Výchozí hodnota | Platné hodnoty |
|:--- |:--- |:--- |
| WarningPreference |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| ErrorActionPreference |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Zastavit<br>Pokračovat<br>SilentlyContinue |

Následující tabulka uvádí chování pro hodnoty proměnných předvoleb, které jsou platné v sadách runbook.

| Hodnota | Chování |
|:--- |:--- |
| Pokračovat |Zaprotokoluje zprávu a pokračuje v provádění runbooku. |
| SilentlyContinue |Pokračuje v provádění runbooku bez protokolování zprávy. Výsledkem je ignorování zprávy. |
| Zastavit |Zaprotokoluje zprávu a pozastaví runbook. |

## <a name="retrieving-runbook-output-and-messages"></a>Načítání výstup a zprávy runbooku
### <a name="azure-portal"></a>Azure Portal
Podrobnosti úlohy runbooku můžete zobrazit na webu Azure portal na kartě úlohy sady runbook. Souhrn úlohy zobrazí vstupní parametry a [výstupního datového proudu](#Output) kromě obecné informace o úloze a případných výjimkách k nim došlo. Historie zahrnuje zprávy z [výstupního datového proudu](#Output) a [upozornění a chyby datové proudy](#WarningError) kromě [podrobný datový proud](#Verbose) a [záznamů o průběhu](#Progress) Pokud je runbook nakonfigurovaný na protokolování podrobných záznamů a záznamů o průběhu.

### <a name="windows-powershell"></a>Windows PowerShell
V prostředí Windows PowerShell můžete načítat výstup a zprávy z runbooku pomocí [Get-AzureAutomationJobOutput](https://msdn.microsoft.com/library/mt603476.aspx) rutiny. Tato rutina vyžaduje ID úlohy a má parametr nazvaný datového proudu, kde můžete určit, který datový proud se vrátit. Můžete zadat **žádné** vrátit všechny datové proudy úlohy.

Následující příklad spouští vzorový runbook a potom počká, než na její dokončení. Po dokončení je jeho výstupní datový proud shromáždí z úlohy.

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
Pro grafické runbooky velmi protokolování je k dispozici ve formě trasování na úrovni aktivity. Existují dvě úrovně trasování: Basic a podrobné. V základní trasování, uvidíte spuštění a čas ukončení každé aktivity v runbooku a informace týkající se žádné opakování aktivity, jako je počet pokusů o zadání a počáteční čas aktivity. V podrobného trasování získat plus základní trasování vstupní a výstupní data pro každou aktivitu. Aktuálně záznamy trasování jsou zapsány pomocí podrobný datový proud, takže musíte povolit podrobné protokolování, pokud povolíte trasování. Pro grafické runbooky pomocí trasování povoleno je potřeba protokolování záznamů o průběhu, protože základní trasování slouží ke stejnému účelu a je informativnější.

![Grafické vytváření úlohy datové proudy zobrazení](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Uvidíte z předchozí snímek obrazovky, když povolíte podrobné protokolování a trasování pro grafické runbooky, mnohem víc informace jsou k dispozici v produkčním prostředí zobrazení datové proudy úlohy. Tyto doplňující informace může být nezbytné pro řešení potíží s provozním problémům s sady runbook, a proto byste měli povolit pouze ho k tomuto účelu a ne jako obecně. Záznamy trasování může být obzvláště množství. Pomocí trasování grafického runbooku můžete získat dva až čtyři záznamy na aktivitu v závislosti na tom, jestli jste nakonfigurovali základním nebo podrobném trasování. Pokud budete potřebovat tyto informace, které chcete sledovat průběh sady runbook pro řešení potíží, můžete chtít zachovat trasování vypnutý.

**Pokud chcete povolit trasování na úrovni aktivity, proveďte následující kroky:**

1. Na webu Azure Portal otevřete účet Automation.
2. V části **automatizace procesu**, vyberte **Runbooky** otevřete seznam runbooků.
3. Na stránce sady Runbook klikněte na vybrat grafický runbook v seznamu sad runbook.
4. V části **nastavení**, klikněte na tlačítko **protokolování a trasování**.
5. V protokolování a trasování v seznamu protokolovat podrobné záznamy, klikněte na tlačítko **na** povolit podrobné protokolování a v části trasování na úrovni aktivity, změnit úroveň trasování pro **základní** nebo **podrobné** založená na úrovni trasování požadavku.<br>
   
   ![Grafické vytváření protokolování a trasování okno](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-log-analytics"></a>Analýza protokolů Microsoft Azure
Automatizace můžete odeslat runbook datové proudy úlohy stavu a úlohu do pracovního prostoru analýzy protokolů. Pomocí analýzy protokolů je možné,

* Pohled na vaše úlohy automatizace 
* Aktivační událost e-mailem nebo výstrahy podle runbook stav úlohy (například chybných nebo pozastavených) 
* Zápis pokročilými dotazy napříč vaše datové proudy úlohy 
* Vazbu mezi úlohy v účtech Automation 
* Vizualizace historii úlohy v čase    

Další informace o tom, jak nakonfigurovat integraci s analýzy protokolů ke shromažďování, korelaci a fungovat na data úlohy najdete v tématu [předávání zpráv o stavu úlohy a datové proudy úlohy ze služby Automation k analýze protokolů](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Další postup
* Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
* Chcete-li pochopit, jak k vytváření a používání podřízené runbooky, přečtěte si téma [podřízené runbooky ve službě Azure Automation](automation-child-runbooks.md)


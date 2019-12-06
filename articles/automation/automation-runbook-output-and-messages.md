---
title: Výstup a zprávy Runbooku v Azure Automation
description: Popisuje, jak vytvořit a načíst výstup a chybové zprávy z runbooků v Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: af199439fedddaef5b1bd3b219a60db697fb25ab
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849645"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Výstup a zprávy Runbooku v Azure Automation

Většina sad Runbook Azure Automation má nějaký formu výstupu. Tento výstup může být chybová zpráva uživateli nebo složitému objektu, který chcete použít s jinou sadou Runbook. Prostředí Windows PowerShell poskytuje [více datových proudů](/powershell/module/microsoft.powershell.core/about/about_redirection) pro odeslání výstupu ze skriptu nebo pracovního postupu. Azure Automation funguje s každým z těchto datových proudů odlišně. Při vytváření Runbooku byste měli dodržovat osvědčené postupy pro použití.

Následující tabulka uvádí stručný popis každého z datových proudů a jejich chování v Azure Portal publikovaných runbooků a při [testování sady Runbook](automation-testing-runbook.md). Další podrobnosti o jednotlivých datových proudech jsou k dispozici v pozdějších částech.

| Datový proud | Popis | Publikováno | Testování |
|:--- |:--- |:--- |:--- |
| Výstup |Objekty, které mají zpracovávat jiné runbooky. |Zapíšou se do historie úlohy. |Zobrazí v podokně výstup testu. |
| Upozornění |Upozornění určené pro uživatele. |Zapíšou se do historie úlohy. |Zobrazí v podokně výstup testu. |
| Chyba |Chybová zpráva určená pro uživatele. Na rozdíl od výjimky runbook pokračuje po chybové zprávě ve výchozím nastavení. |Zapíšou se do historie úlohy. |Zobrazí v podokně výstup testu. |
| Podrobnosti |Zprávy, které poskytují obecné nebo ladicí informace. |Zapíšou se do historie úlohy, jenom v případě, že je pro runbook vypnuté podrobné protokolování. |Zobrazí se v podokně Výstup testu, jenom pokud je proměnná $VerbosePreference v Runbooku nastavená na Continue. |
| Průběh |Záznamy automaticky generované před a za každou aktivitu v sadě runbook. Runbook by se neměl pokoušet vytvořit vlastní záznamy o průběhu, protože jsou určené pro interaktivního uživatele. |Zapíšou se do historie úlohy, jenom v případě, že je pro runbook vypnuté protokolování průběhu. |Nezobrazuje se v podokně výstup testu. |
| Ladění |Zprávy určené pro interaktivního uživatele. Neměl by se používat v sadách Runbook. |Nezapíše se do historie úlohy. |Nezapíše se do podokna výstup testu. |

## <a name="output-stream"></a>Výstupní datový proud
Výstupní datový proud je určen pro výstup objektů, které jsou vytvořeny pomocí skriptu nebo pracovního postupu, pokud je spuštěn správně. V Azure Automation se tento datový proud používá hlavně pro objekty, které mají být použity [nadřazenými Runbooky, které volají aktuální sadu Runbook](automation-child-runbooks.md). Pokud jste [voláte přiřazený runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution) z nadřízeného runbooku, vrátí data z výstupního datového proudu na nadřazený prvek. Pokud víte, že sada Runbook nikdy nevolá jiná sada Runbook, použijte výstupní datový proud pro sdělování obecných informací zpět uživateli. Jako osvědčený postup, ale obvykle používejte [podrobné Stream](#verbose-stream) ke sdělování informací uživateli.

Umožňuje zápis dat do výstupního datového proudu pomocí [Write-Output](https://technet.microsoft.com/library/hh849921.aspx) nebo vložením objektu na samostatném řádku v sadě runbook.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="output-from-a-function"></a>Výstup z funkce

Při zápisu do výstupního datového proudu ve funkci, která je součástí Runbooku, se výstup předává zpět do sady Runbook. Pokud runbook přidá tento výstup k proměnné, nezapíše se do výstupního datového proudu. Zápis do jiných datových proudů v rámci funkce zapisuje do odpovídajícího datového proudu pro sadu Runbook.

Vezměte v úvahu následující vzorový Runbook:

```powershell
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

Po publikování Runbooku a před jeho spuštěním musíte zapnout podrobné protokolování v nastavení Runbooku a získat tak podrobný výstup streamu.

### <a name="declaring-output-data-type"></a>Deklarace výstupního datového typu

Pracovní postup může určovat datový typ svého výstupu pomocí [atributu OutputType](https://technet.microsoft.com/library/hh847785.aspx). Tento atribut nemá žádný vliv za běhu, ale poskytuje údaj autorovi runbooku v době návrhu očekávaný výstup runbooku. Jak se sada nástrojů pro Runbooky stále vyvíjí, důležitost deklarací výstupních datových typů v době návrhu se zvyšuje důležitostí. V důsledku toho je osvědčeným postupem zahrnutí této deklarace do všech runbooků, které vytvoříte.

Tady je seznam výstupních typů:

* System. String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

Následující vzorový runbook výstup objektu řetězce a zahrnuje deklaraci jeho typu výstupu. Pokud vaše sada runbook jako výstup pole určitého typu, byste měli stále zadat typ na rozdíl od pole typu.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

Chcete-li deklarovat typ výstupu v grafickém nebo grafickém Runbooky pracovních postupů PowerShellu, můžete vybrat možnost nabídky **vstup a výstup** a zadat název výstupního typu. Doporučuje se použít úplný název třídy .NET k tomu, aby bylo možné ho snadno identifikovat při odkazování z nadřazené sady Runbook. Tím se zveřejňují všechny vlastnosti této třídy pro datovou sběrnici v sadě Runbook a poskytuje značnou flexibilitu při jejich použití pro podmíněnou logiku, protokolování a odkazování jako na hodnoty pro jiné aktivity v sadě Runbook.<br> ![možnost vstupu a výstupu Runbooku](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

V následujícím příkladu máte dvě grafické Runbooky, které tuto funkci ukazují. Pokud použijete modulární návrhový model Runbooku, máte jednu sadu Runbook, která slouží jako *Šablona sady Runbook pro ověřování* , která spravuje ověřování pomocí Azure pomocí účtu Spustit jako. Náš druhý Runbook, který by normálně prováděl základní logiku k automatizaci daného scénáře, v tomto případě se spustí *Šablona sady Runbook pro ověřování* a zobrazí výsledky v podokně výstup **testu** . Za normálních okolností by tato sada Runbook měla nějakou akci s prostředkem, který využívá výstup z podřízeného Runbooku.

Tady je základní logika Runbooku **AuthenticateTo-Azure** .<br> ](media/automation-runbook-output-and-messages/runbook-authentication-template.png)ukázka ![ověření šablony Runbooku.

Obsahuje výstupní typ *Microsoft. Azure. Commands. Profile. Models. PSAzureContext*, který vrací vlastnosti profilu ověřování.<br> Příklad typu výstupu Runbooku ![](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

I když je tato sada Runbook rovnou dopředná, existuje jedna položka konfigurace, kterou můžete zavolat. Poslední aktivita spouští rutinu **Write-Output** a zapisuje data profilu do proměnné $ _ pomocí výrazu PowerShellu pro parametr **položky InputObject** , který je pro tuto rutinu požadován.

Pro druhý Runbook v tomto příkladu s názvem *test-ChildOutputType*stačí mít dvě aktivity.<br> ![příklad podřízeného typu výstupu Runbooku](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

První aktivita volá Runbook **AuthenticateTo-Azure** a druhá aktivita spouští rutinu **Write-verbose** se **zdrojem dat** **výstupu aktivity** a hodnotou pro **cestu k poli** je **Context. Subscription. Subscription**, který určuje kontextový výstup z Runbooku **AuthenticateTo-Azure** .<br> ![zdroj dat parametru rutiny Write-verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Výsledný výstup je název předplatného.<br> ![Výsledky Runbooku test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

> [!NOTE]
> Po zadání hodnoty do pole **Typ výstupu** v podokně **vlastnosti vstupu a výstupu** musíte kliknout mimo ovládací prvek tak, aby bylo možné tuto položku rozpoznat pomocí ovládacího prvku.

## <a name="message-streams"></a>Datové proudy zprávy

Na rozdíl od do výstupního datového proudu jsou datové proudy zprávy určené ke sdělování informací uživateli. Existuje více datových proudů zpráv pro různé druhy informací a každý z nich je zpracován jiným způsobem Azure Automation.

### <a name="warning-and-error-streams"></a>Datové proudy upozornění a chyb

Datové proudy upozornění a chyb jsou určené k protokolování problémů, ke kterým dochází v sadě runbook. Jsou zapisovány do historie úlohy při spuštění sady Runbook a jsou zahrnuty do podokna výstup testu v Azure Portal při testování sady Runbook. Ve výchozím nastavení sada runbook bude provádět i po upozornění a chyby. Můžete určit, že sada runbook pozastaví při varování nebo chybě nastavením [preferenční proměnné](#preference-variables) v runbooku před vytvořením zprávy. Například pokud chcete, aby sada Runbook mohla pozastavit na chybu, protože by došlo k výjimce, nastavte **$ErrorActionPreference** zastavit.

Vytvořte upozornění nebo chybovou zprávu pomocí [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) nebo [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) rutiny. Do těchto datových proudů můžou zapisovat taky aktivity.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="verbose-stream"></a>Podrobný datový proud

Obecné informace o činnosti runbooku je datový proud podrobnou zprávu. Vzhledem k tomu, že [proud ladění](#debug-stream) není v Runbooku k dispozici, měly by se pro ladicí informace použít podrobné zprávy. Ve výchozím nastavení se podrobné zprávy z publikovaných runbooků neukládají do historie úlohy. Chcete-li uložit podrobné zprávy, nakonfigurujte publikované Runbooky pro protokolování podrobných záznamů na kartě Konfigurace sady Runbook v Azure Portal. Ve většině případů byste měli zachovat výchozí nastavení neprotokolování podrobných záznamů pro runbook z důvodů výkonu. Zapněte tuto možnost jenom pro řešení problémů nebo ladění runbooku.

Při [testování Runbooku](automation-testing-runbook.md)se nezobrazují podrobné zprávy, a to ani v případě, že je Runbook nakonfigurovaný k protokolování podrobných záznamů. Chcete-li zobrazit podrobné zprávy při [testování sady Runbook](automation-testing-runbook.md), je nutné nastavit proměnnou $VerbosePreference, aby pokračovala. V této sadě proměnných se zobrazí podrobné zprávy v podokně výstup testu Azure Portal.

Vytvoření podrobné zprávy použijte [Write-Verbose](https://technet.microsoft.com/library/hh849951.aspx) rutiny.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

### <a name="debug-stream"></a>Datový proud ladění

Datový proud ladění je určena pro použití s interaktivním uživatelem a nesmí se používat v runboocích.

## <a name="progress-records"></a>Záznamy o průběhu

Pokud sadu Runbook nakonfigurujete tak, aby protokoloval záznamy o průběhu (na kartě Konfigurace sady Runbook ve Azure Portal), záznam se zapíše do historie úlohy před a po spuštění každé aktivity. Ve většině případů byste měli zachovat výchozí nastavení neprotokolování záznamů o průběhu pro runbook aby se maximalizoval výkon. Zapněte tuto možnost jenom pro řešení problémů nebo ladění runbooku. Při testování runbooku se zprávy o průběhu nezobrazují, i když je runbook nakonfigurovaný na protokolování záznamů o průběhu.

Rutina [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) není v sadě Runbook platná, protože tato rutina je určena pro použití s interaktivním uživatelem.

## <a name="preference-variables"></a>Proměnné předvoleb

Prostředí Windows PowerShell používá [proměnné předvoleb](https://technet.microsoft.com/library/hh847796.aspx) určit, jak reagovat na data odeslaná do různých výstupních datových proudů. Tyto proměnné můžete nastavit v sadě Runbook, abyste mohli řídit, jak reaguje na data odesílaná do různých datových proudů.

Následující tabulka obsahuje seznam proměnných předvoleb, které lze použít v runboocích používat s platnými a výchozími hodnotami. Tato tabulka obsahuje jenom hodnoty, které jsou platné v Runbooku. Další hodnoty jsou platné pro proměnné předvoleb při použití v prostředí Windows PowerShell mimo Azure Automation.

| Proměnná | Výchozí hodnota | Platné hodnoty |
|:--- |:--- |:--- |
| WarningPreference |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| ErrorActionPreference |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Zastavit<br>Pokračovat<br>SilentlyContinue |

Následující tabulka uvádí chování pro hodnoty proměnných předvoleb, které jsou platné v sadách runbook.

| Hodnota | Chování |
|:--- |:--- |
| Pokračovat |Zaprotokoluje zprávu a pokračuje v provádění runbooku. |
| SilentlyContinue |Pokračuje v provádění runbooku bez protokolování zprávy. Tato hodnota má vliv na ignorování zprávy. |
| Zastavit |Zaprotokoluje zprávu a pozastaví runbook. |

## <a name="runbook-output"></a>Načítání výstupu a zpráv Runbooku

### <a name="azure-portal"></a>Portál Azure

Podrobnosti o úloze Runbooku můžete zobrazit v Azure Portal na kartě Jobs (úlohy) Runbooku. Souhrn úlohy zobrazuje vstupní parametry a [výstupní datový proud](#output-stream) a navíc Obecné informace o úloze a případné výjimky, pokud k nim došlo. Historie obsahuje zprávy z [výstupního streamu](#output-stream) a [datových proudů upozornění a chyb a](#warning-and-error-streams) také [podrobný datový proud](#verbose-stream) a [záznamy o průběhu](#progress-records) , pokud je sada Runbook nakonfigurovaná tak, aby Protokolovat podrobné záznamy a záznamy o průběhu.

### <a name="windows-powershell"></a>Windows PowerShell

V prostředí Windows PowerShell můžete načíst výstup a zprávy z Runbooku pomocí rutiny [Get-AzureAutomationJobOutput](https://docs.microsoft.com/powershell/module/servicemanagement/azure/get-azureautomationjoboutput) . Tato rutina vyžaduje ID úlohy a má parametr nazvaný Stream, kde určujete, který datový proud se má vrátit. Můžete zadat **jakékoli** vrátit všechny datové proudy úlohy.

Následující příklad spouští vzorový runbook a potom čeká na její dokončení. Po dokončení je jeho výstupní datový proud shromáždí z úlohy.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    –AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  –AutomationAccountName "MyAutomationAccount" -Id $job.JobId –Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="graphical-authoring"></a>Vytváření grafických

Pro grafické Runbooky je k dispozici dodatečné protokolování ve formě trasování na úrovni aktivity. K dispozici jsou dvě úrovně trasování: základní a podrobné. V části základní trasování uvidíte čas zahájení a ukončení každé aktivity v sadě Runbook plus informace související s případnými pokusy o aktivitu. Mezi příklady patří počet pokusů a čas spuštění aktivity. V podrobném trasování získáte základní trasování plus vstupní a výstupní data pro každou aktivitu. Záznamy trasování jsou aktuálně zapisovány pomocí podrobného datového proudu, takže pokud povolíte trasování, je nutné povolit podrobné protokolování. Pro grafické Runbooky s povoleným trasováním není nutné protokolovat záznamy o průběhu. Základní trasování slouží ke stejnému účelu a je více informativní.

![Zobrazení datových proudů úlohy grafického vytváření](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Můžete vidět z předchozího snímku obrazovky, když povolíte podrobné protokolování a trasování pro grafické Runbooky, v zobrazení streamů provozních úloh je k dispozici mnohem více informací. Tyto další informace můžou být zásadní pro řešení potíží s produkčními problémy s runbookm, takže byste je měli povolit jenom pro tento účel, a ne jako obecný postup. Záznamy trasování mohou být obzvláště mnoho. Pomocí grafického trasování Runbooku můžete pro každou aktivitu získat dva až čtyři záznamy podle toho, jestli jste nakonfigurovali základní nebo podrobné trasování. Pokud nepotřebujete tyto informace ke sledování průběhu Runbooku při řešení potíží, můžete chtít sledovat, že je trasování vypnuté.

**Chcete-li povolit trasování na úrovni aktivity, proveďte následující kroky:**

1. Na webu Azure Portal otevřete účet Automation.
2. V části **Automatizace procesu**vyberte **Runbooky** a otevřete seznam runbooků.
3. Na stránce sady Runbook kliknutím vyberte grafický Runbook ze seznamu runbooků.
4. V části **Nastavení**klikněte na **protokolování a trasování**.
5. Na stránce protokolování a trasování v části Protokolovat podrobné záznamy klikněte na **zapnout** , pokud chcete povolit podrobné protokolování a v části trasování na úrovni aktivity, změňte úroveň trasování na **základní** nebo **podrobnou** podle úrovně trasování, které požadujete.<br>

   ![Stránka protokolování a trasování grafického vytváření](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="microsoft-azure-monitor-logs"></a>Microsoft Azure monitorování protokolů

Automatizace může odesílat streamy stavů a úloh Runbooku do vašeho pracovního prostoru Log Analytics. Díky protokolům Azure Monitor můžete,

* Získání přehledu o úlohách automatizace
* Aktivace e-mailu nebo výstrahy na základě stavu úlohy Runbooku (například selhání nebo pozastaveno)
* Zápis pokročilých dotazů napříč datovými proudy úloh
* Korelace úloh mezi účty Automation
* Vizualizace historie úloh v průběhu času

Další informace o tom, jak nakonfigurovat integraci s protokoly Azure Monitor ke shromáždění, korelaci a zpracování dat úloh, najdete v tématu [přeposílání stavů úloh a datových proudů úloh z automatizace do Azure monitor protokolů](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Další kroky

* Další informace o spouštění runbooků, postupy při monitorování úloh runbooků a další technické podrobnosti najdete v článku [Sledování úlohy runbooku](automation-runbook-execution.md).
* Pokud chcete pochopit, jak navrhovat a používat podřízené Runbooky, přečtěte si téma [podřízené Runbooky v Azure Automation](automation-child-runbooks.md)
* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](/powershell/scripting/overview).

---
title: Konfigurace výstupu Runbooku a datových proudů zpráv
description: V tomto článku se dozvíte, jak implementovat logiku zpracování chyb a popisuje výstupní a výstupní proudy zpráv v Azure Automation Runbooky.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.openlocfilehash: bb14d5227204a69f8a2ef9e0bf2da05bd7bde51c
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106169092"
---
# <a name="configure-runbook-output-and-message-streams"></a>Konfigurace výstupu Runbooku a datových proudů zpráv

Většina sad Runbook Azure Automation má nějaký formu výstupu. Tento výstup může obsahovat chybovou zprávu pro uživatele nebo složitý objekt určený k použití s jinou sadou Runbook. Prostředí Windows PowerShell poskytuje [více datových proudů](/powershell/module/microsoft.powershell.core/about/about_redirection) pro odeslání výstupu ze skriptu nebo pracovního postupu. Azure Automation funguje s každým z těchto datových proudů odlišně. Při vytváření Runbooku byste měli postupovat podle osvědčených postupů pro použití datových proudů.

Následující tabulka stručně popisuje každý datový proud s chováním v Azure Portal publikovaných runbooků a při [testování sady Runbook](./manage-runbooks.md). Výstupní datový proud je hlavní datový proud, který se používá pro komunikaci mezi sadami Runbook. Ostatní datové proudy jsou klasifikovány jako datové proudy zpráv určené k sdělování informací uživateli.

| Stream | Description | Publikováno | Test |
|:--- |:--- |:--- |:--- |
| Chyba |Chybová zpráva určená pro uživatele. Na rozdíl od s výjimkou, sada Runbook ve výchozím nastavení pokračuje i po chybové zprávě. |Zapsáno do historie úlohy |Zobrazuje se v podokně výstup testu. |
| Ladění |Zprávy určené pro interaktivního uživatele. Neměl by se používat v sadách Runbook. |Nepíše se do historie úlohy. |Nezobrazuje se v podokně výstup testu. |
| Výstup |Objekty, které mají zpracovávat jiné Runbooky. |Zapsáno do historie úlohy |Zobrazuje se v podokně výstup testu. |
| Průběh |Záznamy automaticky generované před a po každé aktivitě v Runbooku. Runbook by se neměl pokoušet vytvořit vlastní záznamy o průběhu, protože jsou určené pro interaktivního uživatele. |Zapsáno do historie úlohy pouze v případě, že je protokolování průběhu zapnuto pro sadu Runbook |Nezobrazuje se v podokně výstup testu. |
| Verbose |Zprávy, které poskytují obecné nebo ladicí informace. |Zapsáno do historie úlohy pouze v případě, že je pro sadu Runbook zapnuto podrobné protokolování |Zobrazuje se v podokně výstup testu pouze v případě, že `VerbosePreference` je proměnná nastavena na pokračovat v sadě Runbook. |
| Upozornění |Upozornění určené pro uživatele. |Zapsáno do historie úlohy |Zobrazuje se v podokně výstup testu. |

## <a name="use-the-output-stream"></a>Použití výstupního datového proudu

Výstupní datový proud se používá pro výstup objektů vytvořených skriptem nebo pracovním postupem, pokud je správně spuštěn. Azure Automation primárně používá tento datový proud pro objekty, které mají být spotřebovány nadřazenými Runbooky, které volají [aktuální sadu Runbook](automation-child-runbooks.md). Když nadřazený objekt [volá vloženou sadu Runbook](automation-child-runbooks.md#invoke-a-child-runbook-using-inline-execution), podřízená položka vrátí data z výstupního datového proudu do nadřazeného.

Sada Runbook používá výstupní datový proud ke sdělování obecných informací klientovi pouze v případě, že není nikdy volána jinou sadou Runbook. Osvědčeným postupem je však, že Runbooky by obvykle měly použít [podrobný datový proud](#write-output-to-verbose-stream) ke sdělování obecných informací uživateli.

Zapište svůj Runbook do výstupního datového proudu pomocí [Write-Output](/powershell/module/microsoft.powershell.utility/write-output). Alternativně můžete objekt umístit do svého samostatného řádku ve skriptu.

```powershell
#The following lines both write an object to the output stream.
Write-Output -InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>Zpracování výstupu funkce

Když funkce Runbooku zapisuje do výstupního datového proudu, výstup se předává zpět do Runbooku. Pokud sada Runbook přiřadí tento výstup do proměnné, výstup není zapsán do výstupního datového proudu. Zápis do jiných datových proudů v rámci funkce zapisuje do odpovídajícího datového proudu pro sadu Runbook. Vezměte v úvahu následující vzorový Runbook pracovního postupu PowerShellu.

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

Výstupní datový proud pro úlohu Runbooku je:

```output
Output inside of function
Output outside of function
```

Podrobný datový proud pro úlohu Runbooku je:

```output
Verbose outside of function
Verbose inside of function
```

Po publikování Runbooku a před jeho spuštěním musíte zapnout podrobné protokolování v nastavení Runbooku a získat tak podrobný výstup streamu.

### <a name="declare-output-data-type"></a>Deklarace výstupního datového typu

Následují příklady výstupních datových typů:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Deklarace výstupního datového typu v pracovním postupu

Pracovní postup Určuje datový typ výstupu pomocí [atributu OutputType](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute). Tento atribut nemá žádný vliv na modul runtime, ale poskytuje indikaci v době návrhu očekávaného výstupu Runbooku. Protože sada nástrojů pro Runbooky se stále vyvíjí, je důležité deklarovat výstupní datové typy v době návrhu. Proto je osvědčeným postupem zahrnutí této deklarace do všech sad Runbook, které vytvoříte.

Následující vzorový Runbook poskytuje výstup objektu řetězce a zahrnuje deklaraci jeho typu výstupu. Pokud má Runbook jako výstup pole určitého typu, měli byste specifikovat opačný typ, než je typ pole.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Deklarace výstupního datového typu v grafickém Runbooku

Chcete-li deklarovat typ výstupu v grafickém nebo grafickém Runbooku pracovního postupu PowerShellu, můžete vybrat možnost nabídky **vstup a výstup** a zadat typ výstupu. Doporučuje se použít úplný název třídy .NET k tomu, aby byl typ snadno identifikovatelný, když na něj odkazuje nadřazený Runbook. Použití úplného názvu zpřístupňuje všechny vlastnosti třídy datové sběrnice v sadě Runbook a zvyšuje flexibilitu při použití vlastností pro podmíněnou logiku, protokolování a odkazování jako hodnoty pro jiné aktivity sady Runbook.<br> ![Možnost vstupu a výstupu Runbooku](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Po zadání hodnoty do pole **Typ výstupu** v podokně vlastnosti vstupu a výstupu nezapomeňte kliknout mimo ovládací prvek tak, aby rozpoznal vaši položku.

Následující příklad ukazuje dvě grafické Runbooky k předvedení vstupní a výstupní funkce. Když použijete modulární návrhový model sady Runbook, máte jednu sadu Runbook, která umožňuje spravovat ověřování pomocí Azure pomocí účtu Spustit jako v rámci šablony sady Runbook pro ověření. Druhá sada Runbook, která obvykle provádí základní logiku pro automatizaci daného scénáře, v tomto případě provede šablonu ověřit sadu Runbook. Zobrazí výsledky v podokně výstup testu. Za normálních okolností by tato sada Runbook měla nějakou akci s prostředkem, který využívá výstup z podřízeného Runbooku.

Tady je základní logika Runbooku **AuthenticateTo-Azure** .<br> ![Příklad ověření šablony Runbooku ](media/automation-runbook-output-and-messages/runbook-authentication-template.png)

Sada Runbook obsahuje typ výstupu `Microsoft.Azure.Commands.Profile.Models.PSAzureContext` , který vrací vlastnosti profilu ověřování.<br> ![Ukázka výstupního typu Runbooku](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

I když je tato sada Runbook jednoduchá, existuje jedna položka konfigurace, kterou můžete zavolat. Poslední aktivita spustí `Write-Output` rutinu pro zápis dat profilu do proměnné pomocí výrazu PowerShellu pro `Inputobject` parametr. Tento parametr je vyžadován pro `Write-Output` .

Druhá sada Runbook v tomto příkladu s názvem **test-ChildOutputType** jednoduše definuje dvě aktivity.<br> ![Příklad podřízeného typu výstupu Runbooku](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

První aktivita volá Runbook **AuthenticateTo-Azure** . Druhá aktivita spustí `Write-Verbose` rutinu se **zdrojem dat** nastaveným na **výstup aktivity**. Také **cesta k poli** je nastavená na **Context. Subscription. Subscription**, výstup kontextu z Runbooku **AuthenticateTo-Azure** .<br> ![Zdroj dat parametru rutiny Write-verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Výsledný výstup je název předplatného.<br> ![Výsledky Test-ChildOutputType sady Runbook](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>Práce s datovými proudy zpráv

Na rozdíl od výstupního datového proudu zpráva vysílá uživatelům informace o komunikaci. Existuje více datových proudů zpráv pro různé druhy informací a Azure Automation zpracovává každý datový proud jinak.

### <a name="write-output-to-warning-and-error-streams"></a>Zápis výstupu do datových proudů upozornění a chyb

Upozornění a chyby streamují problémy, ke kterým dochází v Runbooku. Azure Automation zapisuje tyto datové proudy do historie úlohy při spuštění Runbooku. Automatizace zahrnuje streamy v podokně výstup testu v Azure Portal při testování Runbooku.

Ve výchozím nastavení se sada Runbook pokračuje v provádění po upozornění nebo chybě. Můžete určit, že se má sada Runbook pozastavit na upozornění nebo chyby tím, že sada Runbook před vytvořením zprávy napředá sadu [předvoleb](#work-with-preference-variables) . Například chcete-li, aby se sada Runbook mohla pozastavit na chybu, protože má výjimku, nastavte `ErrorActionPreference` proměnnou na hodnotu zastavit.

Vytvořte upozornění nebo chybovou zprávu pomocí rutiny [Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) nebo [Write-Error](/powershell/module/microsoft.powershell.utility/write-error) . Aktivity mohou také zapisovat do datových proudů upozornění a chyb.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning -Message "This is a warning message."
Write-Error -Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>Zapsat výstup do ladicího streamu

Azure Automation používá pro interaktivní uživatele Stream zpráv ladění. Ve výchozím nastavení Azure Automation nezachycují žádná data streamu ladění, budou zachycena pouze data o výstupech, chybách a upozorněních, pokud je sada Runbook nakonfigurována k jejímu zachycení.

Aby bylo možné zachytit data streamu ladění, je třeba provést dvě akce v sadách Runbook:

1. Nastavte proměnnou `$GLOBAL:DebugPreference="Continue"` , která oznamuje, že prostředí PowerShell bude pokračovat pokaždé, když se objeví zpráva ladění.  **$Global:** část oznamuje prostředí PowerShell, aby to provedl v globálním rozsahu, nikoli v době, kdy je tento skript spuštěný.

1. Přesměrujte Stream ladění, který nezachycujeme do datového proudu, který zachytíme jako *výstup*. To se provádí nastavením přesměrování prostředí PowerShell na příkaz, který se má provést. Další informace o přesměrování prostředí PowerShell najdete v tématu [o přesměrování](/powershell/module/microsoft.powershell.core/about/about_redirection).

#### <a name="examples"></a>Příklady

V tomto příkladu je sada Runbook nakonfigurována pomocí `Write-Output` `Write-Debug` rutin a s úmyslem umístit dva různé datové proudy.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

Pokud by tato sada Runbook byla vykonána tak, jak je, bude podokno výstup pro úlohu sady Runbook obsahovat datový proud následujícího výstupu:

```output
This is an output message.
```

V tomto příkladu je sada Runbook nakonfigurované podobně jako v předchozím příkladu s výjimkou příkazu, který `$GLOBAL:DebugPreference="Continue"` je součástí sčítání `5>&1` na konci `Write-Debug` příkazu.

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

Pokud by se tato sada Runbook spustila, má podokno výstup pro úlohu Runbooku následující výstup:

```output
This is an output message.
This is a debug message.
```

K tomu dochází `$GLOBAL:DebugPreference="Continue"` , protože příkaz oznamuje prostředí PowerShell, aby zobrazoval zprávy ladění a přidání `5>&1` na konec `Write-Debug` příkazu instruuje prostředí PowerShell k přesměrování streamu 5 (ladění) na datový proud 1 (výstup).

### <a name="write-output-to-verbose-stream"></a>Zapsat výstup do podrobného datového proudu

Podrobný datový proud zpráv podporuje obecné informace o operaci sady Runbook. Vzhledem k tomu, že datový proud ladění není pro sadu Runbook k dispozici, by měl Runbook použít podrobné zprávy pro ladicí informace.

Ve výchozím nastavení historie úlohy neukládá podrobné zprávy z publikovaných runbooků z důvodů výkonu. Chcete-li uložit podrobné zprávy, použijte kartu Azure Portal **Konfigurace** s nastavením **podrobné záznamy protokolu** pro konfiguraci publikovaných runbooků pro protokolování podrobných zpráv. Tuto možnost zapněte jenom pro vyřešení problémů nebo ladění Runbooku. Ve většině případů byste měli zachovat výchozí nastavení neprotokolování podrobných záznamů.

Při [testování Runbooku](./manage-runbooks.md)se nezobrazují podrobné zprávy, a to ani v případě, že je Runbook nakonfigurovaný k protokolování podrobných záznamů. Chcete-li zobrazit podrobné zprávy při [testování sady Runbook](./manage-runbooks.md), je nutné nastavit `VerbosePreference` proměnnou tak, aby pokračovala. V této sadě proměnných se zobrazí podrobné zprávy v podokně výstup testu Azure Portal.

Následující kód vytvoří podrobnou zprávu pomocí rutiny [Write-verbose](/powershell/module/microsoft.powershell.utility/write-verbose) .

```powershell
#The following line creates a verbose message.

Write-Verbose -Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>Zpracování záznamů o průběhu

Pomocí karty **konfigurace** Azure Portal můžete nakonfigurovat Runbook, aby protokolovat záznamy o průběhu. Výchozí nastavení znamená, že se záznamy neprotokolují, aby se maximalizoval výkon. Ve většině případů byste měli zachovat výchozí nastavení. Tuto možnost zapněte jenom pro vyřešení problémů nebo ladění Runbooku.

Pokud povolíte protokolování záznamů průběhu, sada Runbook zapíše záznam do historie úlohy před a po spuštění každé aktivity. Testování Runbooku nezobrazuje zprávy o průběhu ani v případě, že je sada Runbook nakonfigurovaná tak, aby protokolovat záznamy o průběhu.

>[!NOTE]
>Rutina [Write-Progress](/powershell/module/microsoft.powershell.utility/write-progress) není v sadě Runbook platná, protože tato rutina je určena pro použití s interaktivním uživatelem.

## <a name="work-with-preference-variables"></a>Práce s proměnnými předvoleb

Můžete nastavit určité [proměnné předvoleb](/powershell/module/microsoft.powershell.core/about/about_preference_variables) Windows PowerShellu v sadách Runbook, abyste mohli řídit reakci na data odesílaná do různých výstupních proudů. V následující tabulce jsou uvedeny proměnné předvoleb, které lze použít v sadách Runbook s jejich výchozími a platnými hodnotami. Další hodnoty jsou k dispozici pro proměnné předvoleb při použití v prostředí Windows PowerShell mimo Azure Automation.

| Proměnná | Výchozí hodnota | Platné hodnoty |
|:--- |:--- |:--- |
| `WarningPreference` |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| `ErrorActionPreference` |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Zastavit<br>Pokračovat<br>SilentlyContinue |

V následující tabulce jsou uvedeny vlastnosti pro hodnoty proměnných předvoleb, které jsou platné v sadách Runbook.

| Hodnota | Chování |
|:--- |:--- |
| Pokračovat |Zaprotokoluje zprávu a pokračuje v provádění Runbooku. |
| SilentlyContinue |Pokračuje v provádění runbooku bez protokolování zprávy. Tato hodnota má vliv na ignorování zprávy. |
| Zastavit |Zaprotokoluje zprávu a pozastaví Runbook. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Načíst výstup a zprávy Runbooku

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Načíst výstup a zprávy Runbooku v Azure Portal

Podrobnosti o úloze Runbooku můžete zobrazit v Azure Portal pomocí karty **úlohy** pro sadu Runbook. V souhrnu úlohy se zobrazí vstupní parametry a [výstupní datový proud](#use-the-output-stream), kromě obecných informací o úloze a všech výjimkách, ke kterým došlo. Historie úlohy obsahuje zprávy z výstupního datového proudu a [Upozornění a chybových proudů](#write-output-to-warning-and-error-streams). Obsahuje taky zprávy z [podrobného datového proudu](#write-output-to-verbose-stream) a [záznamů o průběhu](#handle-progress-records) , pokud je sada Runbook nakonfigurovaná tak, aby Protokolovat podrobné záznamy a záznamy o průběhu.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Načtení výstupu a zpráv Runbooku v prostředí Windows PowerShell

V prostředí Windows PowerShell můžete načíst výstup a zprávy z Runbooku pomocí rutiny [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) . Tato rutina vyžaduje ID úlohy a má parametr nazvaný, `Stream` ve kterém se má zadat datový proud, který se má načíst. Pro tento parametr můžete zadat hodnotu any, aby se načetly všechny datové proudy pro úlohu.

V následujícím příkladu se spouští vzorový Runbook a pak se čeká na jeho dokončení. Jakmile sada Runbook dokončí provádění, skript shromáždí výstupní datový proud Runbooku z úlohy.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    -AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Načtení výstupu a zpráv Runbooku v grafických sadách Runbook

Pro grafické Runbooky je k dispozici dodatečné protokolování výstupu a zpráv ve formě trasování na úrovni aktivity. K dispozici jsou dvě úrovně trasování: základní a podrobné. Základní trasování zobrazuje počáteční a koncový čas každé aktivity v sadě Runbook a navíc informace týkající se opakovaných pokusů o aktivitu. Mezi příklady patří počet pokusů a čas zahájení aktivity. Podrobné trasování obsahuje základní funkce trasování a protokolování vstupních a výstupních dat pro každou aktivitu. 

Aktuálně trasování na úrovni aktivity zapisuje záznamy pomocí podrobného datového proudu. Proto je nutné povolit podrobné protokolování, pokud povolíte trasování. Pro grafické Runbooky s povoleným trasováním není nutné protokolovat záznamy o průběhu. Základní trasování slouží ke stejnému účelu a je více informativní.

![Zobrazení datových proudů úlohy grafického vytváření](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Můžete vidět z obrázku, který umožňuje podrobné protokolování a trasování pro grafické Runbooky, takže v zobrazení **streamů** produkčních úloh bude k dispozici mnohem více informací. Tyto další informace můžou být zásadní pro řešení potíží s produkčními problémy s Runbooku. 

Nicméně pokud nepožadujete, aby tyto informace sledovaly průběh Runbooku při řešení problémů, můžete chtít, aby bylo trasování vypnuto jako obecný postup. Záznamy trasování mohou být obzvláště mnoho. Pomocí grafického trasování Runbooku můžete pro každou aktivitu získat dva až čtyři záznamy v závislosti na konfiguraci základního nebo podrobného trasování.

**Postup při povolení trasování na úrovni aktivity:**

1. Na webu Azure Portal otevřete účet Automation.
2. V části **Automatizace procesu** vyberte **Runbooky** a otevřete seznam runbooků.
3. Na stránce sady Runbook vyberte grafický Runbook ze seznamu sad Runbook.
4. V části **Nastavení** klikněte na **protokolování a trasování**.
5. Na stránce protokolování a trasování v části **Protokolovat podrobné záznamy** klikněte na **zapnuto** , aby se povolilo podrobné protokolování.
6. V části **trasování na úrovni aktivity** změňte úroveň trasování na **základní** nebo **podrobnou** na základě úrovně trasování, které požadujete.<br>

   ![Stránka protokolování a trasování grafického vytváření](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Načíst výstup a zprávy Runbooku v Microsoft Azure monitorování protokolů

Azure Automation může odesílat datové proudy úloh sady Runbook a streamování úloh do pracovního prostoru Log Analytics. Azure Monitor podporuje protokoly, které vám umožňují:

* Získejte přehled o úlohách automatizace.
* Aktivace e-mailu nebo výstrahy na základě stavu úlohy Runbooku, například chyba nebo pozastaveno.
* Zapište pokročilé dotazy napříč datovými proudy úloh.
* Korelujte úlohy mezi účty Automation.
* Vizualizujte historii úloh.

Další informace o konfiguraci integrace s protokoly Azure Monitor ke shromáždění, korelaci a jednání s daty úlohy najdete v tématu [přeposílání stavů úloh a datových proudů úloh z automatizace do Azure monitor protokolů](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Další kroky

* Informace o práci se sadami Runbook najdete [v tématu Správa runbooků v Azure Automation](manage-runbooks.md).
* Pokud neznáte skriptování prostředí PowerShell, přečtěte si dokumentaci [PowerShellu](/powershell/scripting/overview) .
* Referenční informace k rutině Azure Automation PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).

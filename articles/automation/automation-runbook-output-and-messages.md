---
title: Výstup a zprávy Runbooku v Azure Automation
description: Popisuje, jak vytvořit a načíst výstup a chybové zprávy z runbooků v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: ede607191604fbedd4b36523fae18ef1a7a5a2e0
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2020
ms.locfileid: "78924914"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Výstup a zprávy Runbooku v Azure Automation

Většina sad Runbook Azure Automation má nějaký formu výstupu. Tento výstup může obsahovat chybovou zprávu pro uživatele nebo složitý objekt určený k použití s jinou sadou Runbook. Prostředí Windows PowerShell poskytuje [více datových proudů](/powershell/module/microsoft.powershell.core/about/about_redirection) pro odeslání výstupu ze skriptu nebo pracovního postupu. Azure Automation funguje s každým z těchto datových proudů odlišně. Při vytváření Runbooku byste měli postupovat podle osvědčených postupů pro použití datových proudů.

Následující tabulka stručně popisuje každý datový proud s chováním v Azure Portal publikovaných runbooků a při [testování sady Runbook](automation-testing-runbook.md). Výstupní datový proud je hlavní datový proud, který se používá pro komunikaci mezi sadami Runbook. Ostatní datové proudy jsou klasifikovány jako datové proudy zpráv určené k sdělování informací uživateli. 

| Stream | Popis | Publikované | Test |
|:--- |:--- |:--- |:--- |
| Chyba |Chybová zpráva určená pro uživatele. Na rozdíl od s výjimkou, sada Runbook ve výchozím nastavení pokračuje i po chybové zprávě. |Zapsáno do historie úlohy |Zobrazuje se v podokně výstup testu. |
| Ladit |Zprávy určené pro interaktivního uživatele. Neměl by se používat v sadách Runbook. |Nepíše se do historie úlohy. |Nezobrazuje se v podokně výstup testu. |
| Výstup |Objekty, které mají zpracovávat jiné runbooky. |Zapsáno do historie úlohy |Zobrazuje se v podokně výstup testu. |
| Průběh |Záznamy automaticky generované před a za každou aktivitu v sadě runbook. Runbook by se neměl pokoušet vytvořit vlastní záznamy o průběhu, protože jsou určené pro interaktivního uživatele. |Zapsáno do historie úlohy pouze v případě, že je protokolování průběhu zapnuto pro sadu Runbook |Nezobrazuje se v podokně výstup testu. |
| Podrobnosti |Zprávy, které poskytují obecné nebo ladicí informace. |Zapsáno do historie úlohy pouze v případě, že je pro sadu Runbook zapnuto podrobné protokolování |Zobrazuje se v podokně výstup testu pouze v případě, že je v sadě Runbook nastavená *$VerbosePreference* na **pokračovat** . |
| Upozornění |Upozornění určené pro uživatele. |Zapsáno do historie úlohy |Zobrazuje se v podokně výstup testu. |

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="output-stream"></a>Výstupní datový proud

Výstupní datový proud se používá pro výstup objektů vytvořených skriptem nebo pracovním postupem, pokud je správně spuštěn. Azure Automation primárně používá tento datový proud pro objekty, které mají být spotřebovány nadřazenými Runbooky, které volají [aktuální sadu Runbook](automation-child-runbooks.md). Když nadřazený objekt [volá vloženou sadu Runbook](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution), podřízená položka vrátí data z výstupního datového proudu do nadřazeného. 

Sada Runbook používá výstupní datový proud ke sdělování obecných informací klientovi pouze v případě, že není nikdy volána jinou sadou Runbook. Osvědčeným postupem je však, že Runbooky by obvykle měly použít [podrobný datový proud](#verbose-stream) ke sdělování obecných informací uživateli.

Zapište svůj Runbook do výstupního datového proudu pomocí [Write-Output](https://technet.microsoft.com/library/hh849921.aspx). Alternativně můžete objekt umístit do svého samostatného řádku ve skriptu.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Zpracování výstupu funkce

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

### <a name="declaring-output-data-type"></a>Deklarace výstupního datového typu

Následují příklady výstupních datových typů:

* System. String
* System.Int32
* System.Collections.Hashtable
* Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine

#### <a name="declare-output-data-type-in-a-workflow"></a>Deklarace výstupního datového typu v pracovním postupu

Pracovní postup Určuje datový typ výstupu pomocí [atributu OutputType](https://technet.microsoft.com/library/hh847785.aspx). Tento atribut nemá žádný vliv na modul runtime, ale poskytuje indikaci v době návrhu očekávaného výstupu Runbooku. Protože sada nástrojů pro Runbooky se stále vyvíjí, je důležité deklarovat výstupní datové typy v době návrhu. Proto je osvědčeným postupem zahrnutí této deklarace do všech sad Runbook, které vytvoříte.

Následující vzorový runbook výstup objektu řetězce a zahrnuje deklaraci jeho typu výstupu. Pokud vaše sada runbook jako výstup pole určitého typu, byste měli stále zadat typ na rozdíl od pole typu.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Deklarace výstupního datového typu v grafickém Runbooku

Chcete-li deklarovat typ výstupu v grafickém nebo grafickém Runbooku pracovního postupu PowerShellu, můžete vybrat možnost nabídky **vstup a výstup** a zadat typ výstupu. Doporučuje se použít úplný název třídy .NET k tomu, aby byl typ snadno identifikovatelný, když na něj odkazuje nadřazený Runbook. Použití úplného názvu zpřístupňuje všechny vlastnosti třídy datové sběrnice v sadě Runbook a zvyšuje flexibilitu při použití vlastností pro podmíněnou logiku, protokolování a odkazování jako hodnoty pro jiné aktivity sady Runbook.<br> ![možnost vstupu a výstupu Runbooku](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Po zadání hodnoty do pole **Typ výstupu** v podokně vlastnosti vstupu a výstupu nezapomeňte kliknout mimo ovládací prvek tak, aby rozpoznal vaši položku.

Následující příklad ukazuje dvě grafické Runbooky k předvedení vstupní a výstupní funkce. Když použijete modulární návrhový model sady Runbook, máte jednu sadu Runbook, která umožňuje spravovat ověřování pomocí Azure pomocí účtu Spustit jako v rámci šablony sady Runbook pro ověření. Druhá sada Runbook, která obvykle provádí základní logiku pro automatizaci daného scénáře, v tomto případě provede šablonu ověřit sadu Runbook. Zobrazí výsledky v podokně výstup testu. Za normálních okolností by tato sada Runbook měla nějakou akci s prostředkem, který využívá výstup z podřízeného Runbooku.

Tady je základní logika Runbooku **AuthenticateTo-Azure** .<br> ](media/automation-runbook-output-and-messages/runbook-authentication-template.png)ukázka ![ověření šablony Runbooku.

Sada Runbook obsahuje typ výstupu `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`, který vrátí vlastnosti profilu ověřování.<br> Příklad typu výstupu Runbooku ![](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

I když je tato sada Runbook jednoduchá, existuje jedna položka konfigurace, kterou můžete zavolat. Poslední aktivita spustí rutinu **Write-Output** k zápisu dat profilu do proměnné pomocí výrazu PowerShellu pro parametr *položky InputObject* . Tento parametr je vyžadován pro **Write-Output**.

Druhá sada Runbook v tomto příkladu s názvem **test-ChildOutputType**jednoduše definuje dvě aktivity.<br> ![příklad podřízeného typu výstupu Runbooku](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

První aktivita volá Runbook **AuthenticateTo-Azure** . Druhá aktivita spustí rutinu **Write-verbose** se **zdrojem dat** nastaveným na **výstup aktivity**. Také **cesta k poli** je nastavená na **Context. Subscription. Subscription**, výstup kontextu z Runbooku **AuthenticateTo-Azure** .<br> ![zdroj dat parametru rutiny Write-verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Výsledný výstup je název předplatného.<br> ![Výsledky Runbooku test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Datové proudy zprávy

Na rozdíl od výstupního datového proudu zpráva vysílá uživatelům informace o komunikaci. Existuje více datových proudů zpráv pro různé druhy informací a Azure Automation zpracovává každý datový proud jinak.

### <a name="warning-and-error-streams"></a>Datové proudy upozornění a chyb

Upozornění a chyby streamují problémy, ke kterým dochází v Runbooku. Azure Automation zapisuje tyto datové proudy do historie úlohy při spuštění Runbooku. Automatizace zahrnuje streamy v podokně výstup testu v Azure Portal při testování Runbooku. 

Ve výchozím nastavení se sada Runbook pokračuje v provádění po upozornění nebo chybě. Můžete určit, že se má sada Runbook pozastavit na upozornění nebo chyby tím, že sada Runbook před vytvořením zprávy napředá sadu [předvoleb](#preference-variables) . Například pokud chcete, aby se Runbook zastavil při chybě, protože má výjimku, nastavte proměnnou *$ErrorActionPreference* na **zastavit**.

Vytvořte upozornění nebo chybovou zprávu pomocí rutiny [Write-Warning](https://technet.microsoft.com/library/hh849931.aspx) nebo [Write-Error](https://technet.microsoft.com/library/hh849962.aspx) . Aktivity mohou také zapisovat do datových proudů upozornění a chyb.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Datový proud ladění

Azure Automation používá pro interaktivní uživatele Stream zpráv ladění. Neměl by se používat v sadách Runbook.

### <a name="verbose-stream"></a>Podrobný datový proud

Podrobný datový proud zpráv podporuje obecné informace o operaci sady Runbook. Vzhledem k tomu, že datový proud ladění není pro sadu Runbook k dispozici, by měl Runbook použít podrobné zprávy pro ladicí informace. 

Ve výchozím nastavení historie úlohy neukládá podrobné zprávy z publikovaných runbooků z důvodů výkonu. Chcete-li uložit podrobné zprávy, použijte kartu Azure Portal **Konfigurace** s nastavením **podrobné záznamy protokolu** pro konfiguraci publikovaných runbooků pro protokolování podrobných zpráv. Zapněte tuto možnost jenom pro řešení problémů nebo ladění runbooku. Ve většině případů byste měli zachovat výchozí nastavení neprotokolování podrobných záznamů.

Při [testování Runbooku](automation-testing-runbook.md)se nezobrazují podrobné zprávy, a to ani v případě, že je Runbook nakonfigurovaný k protokolování podrobných záznamů. Chcete-li zobrazit podrobné zprávy při [testování sady Runbook](automation-testing-runbook.md), je nutné nastavit proměnnou $VerbosePreference, aby pokračovala. V této sadě proměnných se zobrazí podrobné zprávy v podokně výstup testu Azure Portal.

Pomocí rutiny [Write-verbose](https://technet.microsoft.com/library/hh849951.aspx) vytvořte podrobnou zprávu.

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Záznamy o průběhu

Pomocí karty **konfigurace** Azure Portal můžete nakonfigurovat Runbook, aby protokolovat záznamy o průběhu. Výchozí nastavení znamená, že se záznamy neprotokolují, aby se maximalizoval výkon. Ve většině případů byste měli zachovat výchozí nastavení. Zapněte tuto možnost jenom pro řešení problémů nebo ladění runbooku. 

Pokud povolíte protokolování záznamů průběhu, sada Runbook zapíše záznam do historie úlohy před a po spuštění každé aktivity. Testování Runbooku nezobrazuje zprávy o průběhu ani v případě, že je sada Runbook nakonfigurovaná tak, aby protokolovat záznamy o průběhu.

>[!NOTE]
>Rutina [Write-Progress](https://technet.microsoft.com/library/hh849902.aspx) není v sadě Runbook platná, protože tato rutina je určena pro použití s interaktivním uživatelem.

## <a name="preference-variables"></a>Proměnné předvoleb

Můžete nastavit určité [proměnné předvoleb](https://technet.microsoft.com/library/hh847796.aspx) Windows PowerShellu v sadách Runbook, abyste mohli řídit reakci na data odesílaná do různých výstupních proudů. V následující tabulce jsou uvedeny proměnné předvoleb, které lze použít v sadách Runbook s jejich výchozími a platnými hodnotami. Další hodnoty jsou k dispozici pro proměnné předvoleb při použití v prostředí Windows PowerShell mimo Azure Automation.

| Proměnná | Výchozí hodnota | Platné hodnoty |
|:--- |:--- |:--- |
| WarningPreference |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| ErrorActionPreference |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| VerbosePreference |SilentlyContinue |Zastavit<br>Pokračovat<br>SilentlyContinue |

V následující tabulce jsou uvedeny vlastnosti pro hodnoty proměnných předvoleb, které jsou platné v sadách Runbook.

| Hodnota | Chování |
|:--- |:--- |
| Pokračovat |Zaprotokoluje zprávu a pokračuje v provádění runbooku. |
| SilentlyContinue |Pokračuje v provádění runbooku bez protokolování zprávy. Tato hodnota má vliv na ignorování zprávy. |
| Zastavit |Zaprotokoluje zprávu a pozastaví runbook. |

## <a name="runbook-output"></a>Načítání výstupu a zpráv Runbooku

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Načíst výstup a zprávy Runbooku v Azure Portal

Podrobnosti o úloze Runbooku můžete zobrazit v Azure Portal pomocí karty **úlohy** pro sadu Runbook. V souhrnu úlohy se zobrazí vstupní parametry a [výstupní datový proud](#output-stream), kromě obecných informací o úloze a všech výjimkách, ke kterým došlo. Historie úlohy obsahuje zprávy z výstupního datového proudu a [Upozornění a chybových proudů](#warning-and-error-streams). Obsahuje taky zprávy z [podrobného datového proudu](#verbose-stream) a [záznamů o průběhu](#progress-records) , pokud je sada Runbook nakonfigurovaná tak, aby Protokolovat podrobné záznamy a záznamy o průběhu.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Načtení výstupu a zpráv Runbooku v prostředí Windows PowerShell

V prostředí Windows PowerShell můžete načíst výstup a zprávy z Runbooku pomocí rutiny [Get-AzAutomationJobOutput](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) . Tato rutina vyžaduje ID úlohy a má parametr s názvem *Stream* , ve kterém se má zadat datový proud, který se má načíst. Pro tento parametr můžete zadat hodnotu **Any** , aby se načetly všechny datové proudy pro úlohu.

Následující příklad spouští vzorový runbook a potom čeká na její dokončení. Jakmile sada Runbook dokončí provádění, skript shromáždí výstupní datový proud Runbooku z úlohy.

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
4. V části **Nastavení**klikněte na **protokolování a trasování**.
5. Na stránce protokolování a trasování v části **Protokolovat podrobné záznamy**klikněte na **zapnuto** , aby se povolilo podrobné protokolování.
6. V části **trasování na úrovni aktivity**změňte úroveň trasování na **základní** nebo **podrobnou**na základě úrovně trasování, které požadujete.<br>

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

* Další informace o spuštění sady Runbook, monitorování úloh runbooků a další technické podrobnosti najdete v tématu [sledování úlohy Runbooku](automation-runbook-execution.md).
* Pokud chcete pochopit, jak navrhovat a používat podřízené Runbooky, přečtěte si téma [podřízené Runbooky v Azure Automation](automation-child-runbooks.md).
* Další informace o PowerShellu, včetně referenčních modulů jazyka a výukových modulů, najdete v [dokumentaci k PowerShellu](/powershell/scripting/overview).

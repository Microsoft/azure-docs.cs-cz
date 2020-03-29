---
title: Výstup sady Runbook a zprávy v Azure Automation
description: Popisuje, jak vytvořit a načíst výstupní a chybové zprávy ze sad Runbook v Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2018
ms.topic: conceptual
ms.openlocfilehash: 457b2d2211ea1ba5fa36cec4b7e9a214f5bcad77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367087"
---
# <a name="runbook-output-and-messages-in-azure-automation"></a>Výstup sady Runbook a zprávy v Azure Automation

Většina runbooků Azure Automation má nějakou formu výstupu. Tento výstup může být chybová zpráva pro uživatele nebo složitý objekt určený k použití s jiným runbookem. Prostředí Windows PowerShell poskytuje [více datových proudů](/powershell/module/microsoft.powershell.core/about/about_redirection) pro odesílání výstupu ze skriptu nebo pracovního postupu. Azure Automation pracuje s každým z těchto datových proudů jinak. Při vytváření runbooku byste měli postupovat podle osvědčených postupů pro používání datových proudů.

Následující tabulka stručně popisuje každý datový proud s jeho chováním na portálu Azure pro publikované sady Runbook a během [testování sady Runbook](automation-testing-runbook.md). Výstupní datový proud je hlavní datový proud používaný pro komunikaci mezi runbooky. Ostatní datové proudy jsou klasifikovány jako datové proudy zpráv, určené ke sdělování informací uživateli. 

| Datový proud | Popis | Publikováno | Test |
|:--- |:--- |:--- |:--- |
| Chyba |Chybová zpráva určená pro uživatele. Na rozdíl od výjimky, runbook pokračuje po chybové zprávě ve výchozím nastavení. |Zapsáno do historie práce |Zobrazeno v podokně Výstup testu |
| Ladění |Zprávy určené pro interaktivního uživatele. Neměl by se používat v runbookech. |Není zapsáno do historie práce |Nezobrazuje se v podokně Výstup testu |
| Výstup |Objekty, které mají zpracovávat jiné Runbooky. |Zapsáno do historie práce |Zobrazeno v podokně Výstup testu |
| Průběh |Záznamy automaticky generované před a po každé aktivitě v Runbooku. Runbook by se neměl pokoušet vytvořit vlastní záznamy průběhu, protože jsou určeny pro interaktivního uživatele. |Zapsat do historie úloh pouze v případě, že je pro runbook zapnuto protokolování průběhu |Nezobrazuje se v podokně Výstup testu |
| Verbose |Zprávy, které poskytují obecné nebo ladicí informace. |Zapsat do historie úloh pouze v případě, že je pro runbook zapnuto podrobné protokolování |Zobrazeno v podokně `VerbosePreference` Výstup testu pouze v případě, že je proměnná nastavena na Pokračovat v sadě Runbook |
| Upozornění |Upozornění určené pro uživatele. |Zapsáno do historie práce |Zobrazeno v podokně Výstup testu |

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="output-stream"></a>Výstupní datový proud

Výstupní datový proud se používá pro výstup objektů vytvořených skriptem nebo pracovním postupem při správném spuštění. Azure Automation používá tento datový proud především pro objekty, které mají být spotřebovány nadřazené runbooky, které volají [aktuální runbook](automation-child-runbooks.md). Když nadřazený [volá inbook runbook ,](automation-child-runbooks.md#invoking-a-child-runbook-using-inline-execution)podřízený vrátí data z výstupního datového proudu na nadřazený. 

Aplikace Runbook používá výstupní datový proud ke komunikaci obecných informací klientovi pouze v případě, že jej nikdy nevolaná jiným souborem Runbook. Jako osvědčený postup však sady Runbook by měly obvykle používat [datový proud Verbose](#verbose-stream) ke sdělování obecných informací uživateli.

Chcete mít svůj runbook zapisovat data do výstupního datového proudu pomocí [zápisu a výstupu](https://technet.microsoft.com/library/hh849921.aspx). Případně můžete umístit objekt na vlastní řádek ve skriptu.

```powershell
#The following lines both write an object to the output stream.
Write-Output –InputObject $object
$object
```

### <a name="handling-output-from-a-function"></a>Manipulace s výstupem z funkce

Když funkce runbooku zapíše do výstupního datového proudu, výstup je předán zpět do runbooku. Pokud runbook přiřadí tento výstup proměnné, výstup není zapsán do výstupního datového proudu. Zápis do jiných datových proudů z funkce zapisuje do odpovídajícího datového proudu pro runbook. Vezměte v úvahu následující ukázku runbooku pracovního postupu prostředí PowerShell.

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

Výstupní datový proud pro úlohu runbooku je:

```output
Output inside of function
Output outside of function
```

Datový proud Podrobné pro úlohu runbooku je:

```output
Verbose outside of function
Verbose inside of function
```

Po publikování runbooku a před spuštěním musíte také zapnout podrobné protokolování v nastavení runbooku, abyste získali výstup datového proudu Verbose.

### <a name="declaring-output-data-type"></a>Deklarování výstupního datového typu

Následují příklady výstupních datových typů:

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>Deklarování výstupního datového typu v pracovním postupu

Pracovní postup určuje datový typ výstupu pomocí [atributu OutputType](https://technet.microsoft.com/library/hh847785.aspx). Tento atribut nemá žádný vliv během běhu, ale poskytuje indikaci v době návrhu očekávaného výstupu runbooku. Vzhledem k tomu, že sada nástrojů pro sady Runbook se nadále vyvíjí, zvyšuje se důležitost deklarování výstupních datových typů v době návrhu. Proto je osvědčeným postupem zahrnout tuto deklaraci do všech sad Runbook, které vytvoříte.

Následující vzorový Runbook poskytuje výstup objektu řetězce a zahrnuje deklaraci jeho typu výstupu. Pokud má Runbook jako výstup pole určitého typu, měli byste specifikovat opačný typ, než je typ pole.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
 ```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>Deklarování výstupního datového typu v grafické matné knize runbooku

Chcete-li deklarovat typ výstupu v grafické nebo grafické runbooku pracovního postupu prostředí PowerShell, můžete vybrat možnost nabídky **Vstup a Výstup** a zadat typ výstupu. Doporučujeme použít úplný název třídy .NET, aby byl typ snadno identifikovatelný, když na něj odkazuje nadřazený soubor Runbook. Použití celého názvu zpřístupňuje všechny vlastnosti třídy datové sběrnici v runbooku a zvyšuje flexibilitu při použití vlastností pro podmíněnou logiku, protokolování a odkazování jako hodnoty pro jiné aktivity runbooku.<br> ![Vstup a výstup sady Runbook, volba](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

>[!NOTE]
>Po zadání hodnoty do pole **Typ výstupu** v podokně Vlastnosti vstup a výstup nezapomeňte klepnout mimo ovládací prvek, aby rozpoznal vaši položku.

Následující příklad ukazuje dvě grafické runbooky pro zobrazení funkce Vstup a Výstup. Při použití modulárního modelu návrhu sady Runbook máte jednu runbook jako šablonu Authenticate Runbook, která spravuje ověřování pomocí Azure pomocí účtu Spustit jako. Druhý soubor Runbook, který obvykle provádí základní logiku pro automatizaci daného scénáře, v tomto případě provede šablonu authenticate runbook. Zobrazí výsledky do podokna výstupu test. Za normálních okolností byste měli tento runbook udělat něco proti prostředek využití výstupu z podřízené runbook.

Tady je základní logika runbooku **AuthenticateTo-Azure.**<br> ![Ověřit příklad šablony](media/automation-runbook-output-and-messages/runbook-authentication-template.png)runbooku .

Soubor Runbook obsahuje `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`typ výstupu , který vrací vlastnosti profilu ověřování.<br> ![Příklad typu výstupu runbooku](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

I když je tato runbook přímočará, je tu jedna položka konfigurace, kterou můžete volat zde. Poslední aktivita provede `Write-Output` rutinu pro zápis dat profilu do proměnné pomocí `Inputobject` výrazu Prostředí PowerShell pro parametr. Tento parametr je `Write-Output`vyžadován pro .

Druhý runbook v tomto příkladu s názvem **Test-ChildOutputType**jednoduše definuje dvě aktivity.<br> ![Příklad podřízeného typu výstupu Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

První aktivita volá **runbook AuthenticateTo-Azure.** Druhá aktivita `Write-Verbose` spustí rutinu se **zdrojem dat** nastaveným na **výstup Aktivity**. Cesta **pole** je také nastavena na **Context.Subscription.SubscriptionName**, výstup kontextu z **runbooku AuthenticateTo-Azure.**<br> ![Zdroj dat parametru rutiny zápisu-verbose](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

Výsledný výstup je název předplatného.<br> ![Výsledky runbooku Test-ChildOutputType](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="message-streams"></a>Datové proudy zpráv

Na rozdíl od výstupního datového proudu datové proudy zpráv sdělují informace uživateli. Existuje více datových proudů zpráv pro různé druhy informací a Azure Automation zpracovává každý datový proud jinak.

### <a name="warning-and-error-streams"></a>Výstražné a chybové proudy

Upozornění a chyba proudy protokolů problémy, ke kterým dochází v runbooku. Azure Automation zapisuje tyto datové proudy do historie úloh při provádění sady Runbook. Automatizace zahrnuje datové proudy v podokně Výstup test na portálu Azure při testování sady Runbook. 

Ve výchozím nastavení se runbook nadále spouští po upozornění nebo chybě. Můžete určit, že vaše sada Runbook by měla pozastavit na upozornění nebo chybu tím, že sada runbook nastavit [proměnnou předvoleb](#preference-variables) před vytvořením zprávy. Chcete-li například způsobit, že sada Runbook pozastaví chybu `ErrorActionPreference` stejně jako u výjimky, nastavte proměnnou na Stop.

Vytvořte upozornění nebo chybovou zprávu pomocí rutiny [Upozornění na zápis](https://technet.microsoft.com/library/hh849931.aspx) nebo Chyba [zápisu.](https://technet.microsoft.com/library/hh849962.aspx) Aktivity můžete také zapisovat do datových proudů upozornění a chyby.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning –Message "This is a warning message."
Write-Error –Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="debug-stream"></a>Datový proud ladění

Azure Automation používá datový proud ladicích zpráv pro interaktivní uživatele. Neměl by být používán v sadách Runbook.

### <a name="verbose-stream"></a>Podrobný proud

Datový proud zpráv Verbose podporuje obecné informace o operaci runbooku. Vzhledem k tomu, že datový proud ladění není k dispozici pro runbook, váš runbook by měl používat podrobné zprávy pro informace o ladění. 

Ve výchozím nastavení historie úloh neukládá podrobné zprávy z publikovaných sad Runbook z důvodů výkonu. Chcete-li ukládat podrobné zprávy, použijte kartu **Konfigurace** portálu Azure s nastavením **Log Verbose Records** a nakonfigurujte publikované sady runbooků tak, aby zaznamenávaly podrobné zprávy. Tuto možnost zapněte jenom pro vyřešení problémů nebo ladění Runbooku. Ve většině případů byste měli zachovat výchozí nastavení není protokolování podrobné záznamy.

Při [testování runbooku](automation-testing-runbook.md)se podrobné zprávy nezobrazují ani v případě, že je runbook nakonfigurován tak, aby protokoloval podrobné záznamy. Chcete-li zobrazit podrobné zprávy při testování sady `VerbosePreference` [Runbook](automation-testing-runbook.md), je nutné nastavit proměnnou pokračovat. S tuto sadu proměnných podrobné zprávy se zobrazí v podokně výstup test na portálu Azure.

Následující kód vytvoří podrobnou zprávu pomocí rutiny [Write-Verbose.](https://technet.microsoft.com/library/hh849951.aspx)

```powershell
#The following line creates a verbose message.

Write-Verbose –Message "This is a verbose message."
```

## <a name="progress-records"></a>Záznamy průběhu

Kartu **Konfigurace** na webu Azure Portal můžete použít ke konfiguraci sady Runbook pro protokolování záznamů průběhu. Výchozí nastavení je není protokolovat záznamy, maximalizovat výkon. Ve většině případů byste měli zachovat výchozí nastavení. Tuto možnost zapněte jenom pro vyřešení problémů nebo ladění Runbooku. 

Pokud povolíte protokolování záznamů průběhu, runbook zapíše záznam do historie úloh před a po spuštění každé aktivity. Testování runbooku nezobrazuje zprávy o průběhu, i když je nakonfigurována pro protokolování záznamů průběhu.

>[!NOTE]
>Rutina [Průběh zápisu](https://technet.microsoft.com/library/hh849902.aspx) není v aplikaci Runbook platná, protože tato rutina je určena pro použití s interaktivním uživatelem.

## <a name="preference-variables"></a>Proměnné předvoleb

Můžete nastavit určité [proměnné předvoleb](https://technet.microsoft.com/library/hh847796.aspx) prostředí Windows PowerShell v sadách Runbook, které řídí odpověď na data odeslaná do různých výstupních datových proudů. V následující tabulce jsou uvedeny proměnné předvoleb, které lze použít v sadách Runbook s jejich výchozími a platnými hodnotami. Další hodnoty jsou k dispozici pro proměnné předvoleb při použití v prostředí Windows PowerShell mimo Azure Automation.

| Proměnná | Výchozí hodnota | Platné hodnoty |
|:--- |:--- |:--- |
| `WarningPreference` |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| `ErrorActionPreference` |Pokračovat |Zastavit<br>Pokračovat<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |Zastavit<br>Pokračovat<br>SilentlyContinue |

V další tabulce je uvedeno chování hodnot proměnných předvoleb, které jsou platné v sadách Runbook.

| Hodnota | Chování |
|:--- |:--- |
| Pokračovat |Zaprotokoluje zprávu a pokračuje v provádění Runbooku. |
| SilentlyContinue |Pokračuje v provádění runbooku bez protokolování zprávy. Tato hodnota má za následek ignorování zprávy. |
| Zastavit |Zaprotokoluje zprávu a pozastaví Runbook. |

## <a name="retrieving-runbook-output-and-messages"></a><a name="runbook-output"></a>Načítání výstupu a zpráv runbooku

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Načtení výstupu sady Runbook a zpráv na webu Azure Portal

Podrobnosti o úloze runbooku můžete zobrazit na webu Azure Portal pomocí karty **Úlohy** pro runbook. Souhrn úlohy zobrazuje vstupní parametry a [výstupní datový proud](#output-stream), kromě obecných informací o úloze a všech výjimek, ke kterým došlo. Historie úloh obsahuje zprávy z výstupního datového proudu [a datových proudů upozornění a chyb](#warning-and-error-streams). Obsahuje také zprávy z [podrobného datového proudu](#verbose-stream) a [průběh záznamů,](#progress-records) pokud je nakonfigurován pro protokol podrobné a průběh záznamy.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Načtení výstupu a zpráv runbooku v prostředí Windows PowerShell

V prostředí Windows PowerShell můžete načíst výstup a zprávy z runbooku pomocí rutiny [Get-AzAutomationJobOutput.](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationJobOutput?view=azps-3.5.0) Tato rutina vyžaduje ID úlohy a má `Stream` parametr, ve kterém chcete zadat datový proud načíst. Můžete zadat hodnotu Any pro tento parametr pro načtení všech datových proudů pro úlohu.

V následujícím příkladu se spouští vzorový Runbook a pak se čeká na jeho dokončení. Jakmile sada Runbook dokončí spuštění, skript shromažďuje výstupní datový proud runbooku z úlohy.

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

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>Načtení výstupu sady Runbook a zpráv v grafických sadách Runbook

Pro grafické sady Runbook je k dispozici další protokolování výstupu a zpráv ve formě trasování na úrovni aktivity. Existují dvě úrovně trasování: Základní a Podrobné. Základní trasování zobrazuje počáteční a koncový čas pro každou aktivitu v runbooku a informace související s opakováním všech aktivit. Některé příklady jsou počet pokusů a čas zahájení aktivity. Podrobné trasování zahrnuje základní funkce trasování a protokolování vstupních a výstupních dat pro každou aktivitu. 

V současné době trasování na úrovni aktivity zapisuje záznamy pomocí datového proudu Verbose. Proto je nutné povolit podrobné protokolování při povolení trasování. U grafických runbooků s povoleným trasováním není nutné zaznamenávat záznamy průběhu. Základní trasování slouží stejnému účelu a je více informativní.

![Grafické vytváření zobrazení datových proudů úloh](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

Z obrázku můžete vidět, že povolení podrobného protokolování a trasování pro grafické sady Runbook zpřístupňuje mnohem více informací v zobrazení **produkčních datových proudů úloh.** Tyto další informace mohou být nezbytné pro řešení problémů s produkčním prostředím s runbookem. 

Pokud však tyto informace nepotřebujete ke sledování průběhu řešení potíží v souboru Runbook, můžete chtít trasování vypnout jako obecnou praxi. Záznamy trasování mohou být obzvláště četné. Pomocí grafického trasování runbooku můžete získat dva až čtyři záznamy na aktivitu v závislosti na konfiguraci základního nebo podrobného trasování.

**Povolení trasování na úrovni aktivity:**

1. Na webu Azure Portal otevřete účet Automation.
2. Vyberte **Runbookv** části **Automatizace procesů,** chcete-li otevřít seznam runbooků.
3. Na stránce Runbook vyberte grafickou runbook ze seznamu runbooků.
4. V části **Nastavení**klepněte na **položku Protokolování a trasování**.
5. Na stránce Protokolování a trasování v části **Protokolovat podrobné záznamy**povolte podrobné protokolování klepnutím **na tlačítko Zapnuto.**
6. V části **Trasování na úrovni aktivity**změňte úroveň trasování na **Základní** nebo **Podrobná**na základě úrovně trasování, kterou požadujete.<br>

   ![Grafická stránka Protokolování a sledování grafiky](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Načtení výstupu sady Runbook a zpráv v protokolech programu Microsoft Azure Monitor

Azure Automation můžete odesílat stav úlohy runbooku a toky úloh do pracovního prostoru Log Analytics. Azure Monitor podporuje protokoly, které umožňují:

* Získejte přehled o svých úlohách automatizace.
* Aktivujte e-mail nebo výstrahu na základě stavu úlohy runbooku, například se nezdařilo nebo pozastaveno.
* Pište pokročilé dotazy napříč datovými proudy úloh.
* Korelujte úlohy napříč účty automatizace.
* Vizualizujte historii úloh.

Další informace o konfiguraci integrace s protokoly Azure Monitor pro shromažďování, korelovat a pracovat na data úloh, najdete v [tématu předávání stavu úlohy a toky úloh z automatizace do protokolů Azure Monitor](automation-manage-send-joblogs-log-analytics.md).

## <a name="next-steps"></a>Další kroky

* Další informace o spouštění runbooků, monitorování úloh runbooku a dalších technických podrobnostech najdete [v tématu Sledování úlohy runbooku](automation-runbook-execution.md).
* Informace o tom, jak navrhovat a používat podřízené runbooky, najdete [v tématu Podřízené runbooky v Azure Automation](automation-child-runbooks.md).
* Další informace o Prostředí PowerShell, včetně jazykových referencí a výukových modulů, najdete v [tématu Dokumenty prostředí PowerShell](/powershell/scripting/overview).

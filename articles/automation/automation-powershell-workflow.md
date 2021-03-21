---
title: Informace o pracovním postupu PowerShellu pro Azure Automation
description: V tomto článku se dozvíte o rozdílech mezi pracovním postupem PowerShellu a prostředím PowerShell a koncepty platnými pro Runbooky Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 483d4c16f1b77bf7328857eb25e1571a741d144e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896914"
---
# <a name="learn-powershell-workflow-for-azure-automation"></a>Informace o pracovním postupu PowerShellu pro Azure Automation

Sady Runbook v Azure Automation jsou implementovány jako pracovní postupy prostředí Windows PowerShell, skripty prostředí Windows PowerShell, které používají programovací model Windows Workflow Foundation. Pracovní postup je pořadí naprogramovaných, propojených kroků, které provádějí dlouhodobé úlohy nebo vyžadují koordinaci více kroků v rámci více zařízení nebo spravovaných uzlů. 

Když je pracovní postup zapsaný pomocí syntaxe Windows PowerShellu a spustí se v prostředí Windows PowerShell, zpracovává se programovací model Windows Workflow Foundation. Výhody pracovního postupu v rámci normálního skriptu zahrnují souběžný výkon akce proti více zařízením a automatické obnovení při selhání. 

> [!NOTE]
> Skript PowerShellového pracovního postupu je velmi podobný skriptu prostředí Windows PowerShell, ale obsahuje některé významné rozdíly, které mohou být matoucí pro nového uživatele. Proto doporučujeme napsat své Runbooky pomocí pracovního postupu PowerShellu jenom v případě, že potřebujete použít [kontrolní body](#use-checkpoints-in-a-workflow). 

Podrobné informace o tématech v tomto článku najdete v tématu [Začínáme s pracovním postupem prostředí Windows PowerShell](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj134242(v=ws.11)).

## <a name="use-workflow-keyword"></a>Použít klíčové slovo pracovního postupu

Prvním krokem k převedení skriptu PowerShellu na pracovní postup PowerShellu je jeho orámování s `Workflow` klíčovým slovem. Pracovní postup začíná `Workflow` klíčovým slovem následovaným textem skriptu uzavřeným ve složených závorkách. Název pracovního postupu následuje za `Workflow` klíčovým slovem, jak je znázorněno v následující syntaxi:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Název pracovního postupu musí odpovídat názvu Runbooku služby Automation. Pokud se sada Runbook importuje, musí název souboru odpovídat názvu pracovního postupu a musí končit **příponou. ps1**.

K přidání parametrů do pracovního postupu použijte `Param` klíčové slovo stejně jako ve skriptu.

## <a name="learn-differences-between-powershell-workflow-code-and-powershell-script-code"></a>Informace o rozdílech mezi kódem pracovního postupu PowerShellu a kódem skriptu PowerShellu

Kód pracovního postupu PowerShellu vypadá téměř stejně jako kód skriptu PowerShellu s výjimkou některých významných změn. Následující části popisují změny, které je třeba provést v PowerShellovém skriptu, aby je bylo možné spustit v pracovním postupu.

### <a name="activities"></a>Aktivity

Aktivita je konkrétní úloha v pracovním postupu, která se provádí v rámci sekvence. Pracovní postup prostředí Windows PowerShell automaticky převádí mnoho rutin prostředí Windows PowerShell na aktivity při spuštění pracovního postupu. Když zadáte jednu z těchto rutin v Runbooku, odpovídající aktivita se spustí programovací model Windows Workflow Foundation. 

Pokud rutina nemá odpovídající aktivitu, pracovní postup prostředí Windows PowerShell automaticky spustí rutinu v aktivitě [InlineScript](#use-inlinescript) . Některé rutiny jsou vyloučené a nelze je použít v pracovním postupu, pokud je výslovně nezahrnete do bloku InlineScript. Další informace najdete v tématu [použití aktivit ve skriptových pracovních postupech](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574194(v=ws.11)).

Aktivity pracovních postupů sdílejí sadu společných parametrů ke konfiguraci svého provozu. Viz [about_WorkflowCommonParameters](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="positional-parameters"></a>Poziční parametry

V pracovním postupu nemůžete použít poziční parametry s aktivitami a rutinami. Proto je nutné použít názvy parametrů. Vezměte v úvahu následující kód, který získá všechny běžící služby:

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Pokud se pokusíte tento kód spustit v pracovním postupu, obdržíte zprávu, kterou je třeba `Parameter set cannot be resolved using the specified named parameters.` pro tento problém opravit, zadejte název parametru, jak je uvedeno v následujícím příkladu:

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deserializovatelné objekty

Objekty v pracovních postupech jsou deserializovány, což znamená, že jejich vlastnosti jsou stále k dispozici, ale nikoli jejich metody.  Zvažte například následující kód PowerShellu, který zastaví službu pomocí `Stop` metody `Service` objektu.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Pokud se pokusíte tento postup spustit v pracovním postupu, zobrazí se chybová zpráva. `Method invocation is not supported in a Windows PowerShell Workflow.`

Jednou z možností je zabalit tyto dva řádky kódu do [InlineScript](#use-inlinescript) bloku. V tomto případě `Service` představuje objekt služby v rámci bloku.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Další možností je použít jinou rutinu, která má stejnou funkci jako metoda, pokud je k dispozici. V našem příkladu `Stop-Service` rutina poskytuje stejné funkce jako `Stop` Metoda a můžete použít následující kód pro pracovní postup.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="use-inlinescript"></a>Použití InlineScript

Tato `InlineScript` aktivita je užitečná v případě, že potřebujete spustit jeden nebo více příkazů jako tradiční powershellový skript namísto pracovního postupu PowerShellu.  Přesto jsou příkazy v pracovním postupu odesílány ke zpracování do programovacího modelu Windows Workflow Foundation, příkazy v bloku InlineScript jsou zpracovány prostředím Windows PowerShell.

InlineScript používá níže uvedenou syntaxi.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Můžete vrátit výstup z InlineScript přiřazením výstupu k proměnné. Následující příklad zastaví službu a pak vypíše název služby.

```powershell
Workflow Stop-MyService
{
    $Output = InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

Můžete předat hodnoty do bloku InlineScript, ale je nutné použít **$using** modifikátor rozsahu.  Následující příklad je stejný jako předchozí příklad s tím rozdílem, že název služby je poskytován proměnnou.

```powershell
Workflow Stop-MyService
{
    $ServiceName = "MyService"

    $Output = InlineScript {
        $Service = Get-Service -Name $Using:ServiceName
        $Service.Stop()
        $Service
    }

    $Output.Name
}
```

I když aktivity InlineScript můžou být v určitých pracovních postupech kritické, nepodporují konstrukce pracovního postupu. Používejte je pouze v případě potřeby z následujících důvodů:

* V bloku InlineScript nemůžete použít [kontrolní body](#use-checkpoints-in-a-workflow) . Pokud dojde k selhání v rámci bloku, musí pokračovat od začátku bloku.
* [Paralelní provádění](#use-parallel-processing) nelze použít uvnitř InlineScript bloku.
* InlineScript má vliv na škálovatelnost pracovního postupu, protože obsahuje relaci prostředí Windows PowerShell pro celou délku bloku InlineScript.

Další informace o použití InlineScript najdete v tématu [spuštění příkazů prostředí Windows PowerShell v pracovním postupu](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574197(v=ws.11)) a [about_InlineScript](/powershell/module/psworkflow/about/about_inlinescript).

## <a name="use-parallel-processing"></a>Použít paralelní zpracování

Jednou z výhod pracovních postupů prostředí Windows PowerShell je schopnost provádět sadu příkazů paralelně namísto postupně jako v případě typického skriptu.

Klíčové slovo můžete použít `Parallel` k vytvoření bloku skriptu s více příkazy, které běží souběžně. K tomu se používá následující syntaxe. V tomto případě Activity1 a "Activity2" spustí ve stejnou dobu. Activity3 se spustí až po dokončení Activity1 a "Activity2".

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Zvažte například následující příkazy PowerShellu, které kopírují více souborů do cílového umístění v síti. Tyto příkazy jsou spouštěny postupně, aby jeden soubor musel dokončit kopírování před dalším spuštěním.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Následující pracovní postup spouští tyto stejné příkazy paralelně, takže všechny spouštějí kopírování současně.  Po všech zkopírovaných se zobrazí zpráva o dokončení.

```powershell
Workflow Copy-Files
{
    Parallel
    {
        Copy-Item -Path "C:\LocalPath\File1.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File2.txt" -Destination "\\NetworkPath"
        Copy-Item -Path "C:\LocalPath\File3.txt" -Destination "\\NetworkPath"
    }

    Write-Output "Files copied."
}
```

Můžete použít `ForEach -Parallel` konstrukci ke zpracování příkazů pro každou položku v kolekci současně. Položky v kolekci jsou zpracovávány paralelně, zatímco příkazy v bloku skriptu se spouštějí postupně. Tento proces používá níže uvedenou syntaxi. V tomto případě Activity1 spustí ve stejnou dobu pro všechny položky v kolekci. Pro každou položku "Activity2" začíná po dokončení Activity1. Activity3 se spustí až po dokončení Activity1 i "Activity2" pro všechny položky. Parametr používáme `ThrottleLimit` k omezení paralelismu. Příliš vysoký `ThrottleLimit` může způsobit problémy. Ideální hodnota pro `ThrottleLimit` parametr závisí na mnoha faktorech ve vašem prostředí. Začněte s nízkou hodnotou a zkuste různé hodnoty zvětšit, dokud nezjistíte, které funguje pro konkrétní situaci.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Následující příklad je podobný předchozímu příkladu, který kopíruje soubory paralelně.  V tomto případě se pro každý soubor po zkopírování zobrazí zpráva.  Pouze po zkopírování je zobrazena zpráva o konečném dokončení.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel -ThrottleLimit 10 ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Nedoporučujeme paralelní spouštění podřízených runbooků, protože se ukázalo, že poskytuje nespolehlivé výsledky. Výstup z podřízené sady Runbook se někdy nezobrazuje a nastavení v jedné podřízené sadě Runbook mohou ovlivnit ostatní paralelní podřízené Runbooky. Proměnné, jako například `VerbosePreference` , `WarningPreference` a jiné, se nemusí šířit do podřízených runbooků. A pokud podřízený Runbook změní tyto hodnoty, nemusí být po vyvolání správně obnoveny.

## <a name="use-checkpoints-in-a-workflow"></a>Použití kontrolních bodů v pracovním postupu

Kontrolní bod je snímek aktuálního stavu pracovního postupu, který obsahuje aktuální hodnoty proměnných a všechny výstupy vygenerované do tohoto bodu. Pokud pracovní postup skončí chybou nebo je pozastaven, začne při příštím spuštění od posledního kontrolního bodu, a ne od začátku. 

Kontrolní bod můžete v pracovním postupu nastavit pomocí `Checkpoint-Workflow` aktivity. Azure Automation má funkci s názvem [spravedlivá sdílená](automation-runbook-execution.md#fair-share)sada Runbook, která běží na tři hodiny, aby bylo možné spouštět jiné Runbooky. Nakonec se uvolněná sada Runbook znovu načte. V takovém případě pokračuje v provádění z posledního kontrolního bodu pořízeného v sadě Runbook.

Chcete-li zaručit, že se sada Runbook nakonec dokončí, je nutné přidat kontrolní body v intervalech, které jsou spuštěny méně než tři hodiny. Při každém spuštění se přidá nový kontrolní bod. Pokud je sada Runbook po třech hodinách vyřazena z důvodu chyby, sada Runbook se obnoví neomezeně.

V následujícím příkladu dojde k výjimce po "Activity2", což způsobí ukončení pracovního postupu. Po opětovném spuštění pracovního postupu se spustí spuštěním "Activity2", protože tato aktivita byla hned za poslední nastavenou kontrolní sadu.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Nastavte kontrolní body v pracovním postupu po aktivitách, které by mohly být náchylné k výjimce a neměly by být opakované, pokud je pracovní postup obnovený. Pracovní postup může například vytvořit virtuální počítač. Můžete nastavit kontrolní bod před a za příkazy k vytvoření virtuálního počítače. Pokud se vytvoření nepovede, příkazy se zopakují, pokud se pracovní postup spustí znovu. Pokud pracovní postup po úspěšném vytvoření selže, virtuální počítač se po obnovení pracovního postupu znovu nevytvoří.

Následující příklad zkopíruje více souborů do umístění v síti a nastaví kontrolní bod za každým souborem.  Pokud dojde ke ztrátě síťového umístění, pracovní postup skončí chybou.  Až se znovu spustí, obnoví se u posledního kontrolního bodu. Přeskočeny jsou pouze soubory, které již byly zkopírovány.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
        Checkpoint-Workflow
    }

    Write-Output "All files copied."
}
```

Vzhledem k tomu, že přihlašovací údaje uživatelského jména nejsou po volání aktivity [pozastavit-Workflow](/powershell/module/psworkflow/about/about_suspend-workflow) nebo po posledním kontrolním bodu zachovány, je třeba nastavit přihlašovací údaje na hodnotu null a pak je znovu načíst z úložiště Asset po `Suspend-Workflow` volání nebo zadání kontrolního bodu.  V opačném případě se může zobrazit následující chybová zpráva: `The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Následující stejný kód ukazuje, jak tuto situaci zpracovat ve vašich sadách Runbook pracovního postupu PowerShellu.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzAutomationCredential -Name "MyCredential"
    $null = Connect-AzAccount -Credential $Cred

    $VmsToCreate = Get-AzAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzVM -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzAutomationCredential -Name "MyCredential"
        $null = Connect-AzAccount -Credential $Cred
        }
}
```

> [!NOTE]
> Pro negrafické Runbooky PowerShellu `Add-AzAccount` a `Add-AzureRMAccount` jsou aliasy pro [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Tyto rutiny můžete použít nebo můžete [své moduly](automation-update-azure-modules.md) v účtu Automation aktualizovat na nejnovější verze. Vaše moduly možná budete muset aktualizovat i v případě, že jste právě vytvořili nový účet Automation. Pokud ověřujete pomocí účtu Spustit jako nakonfigurovaného s instančním objektem, není použití těchto rutin nutné.

Další informace o kontrolních bodech najdete v tématu [Přidání kontrolních bodů do pracovního postupu skriptu](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj574114(v=ws.11)).

## <a name="next-steps"></a>Další kroky

* Další informace o Runbooky pracovních postupů PowerShellu najdete v tématu [kurz: vytvoření Runbooku pracovního postupu PowerShellu](learn/automation-tutorial-runbook-textual.md).

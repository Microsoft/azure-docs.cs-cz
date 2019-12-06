---
title: Školicí pracovní postup prostředí PowerShell pro Azure Automation
description: Tento článek je určený jako Rychlá lekce pro autory, kteří znají prostředí PowerShell, aby porozuměli konkrétním rozdílům mezi PowerShellem a pracovním postupem PowerShellu a koncepty platnými pro Runbooky Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 12/14/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d656e97448bebe7019a63824b9de6e322b787a92
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850733"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Principy konceptu pracovního postupu prostředí Windows PowerShell pro výuku pro Runbooky služby Automation

Runbooky v Azure Automation jsou implementované jako pracovní postupy prostředí Windows PowerShell.  Pracovní postup prostředí Windows PowerShell je podobný skriptu prostředí Windows PowerShell, ale obsahuje některé významné rozdíly, které mohou být matoucí pro nového uživatele.  Tento článek je určený k tomu, aby vám usnadnil psaní runbooků pomocí pracovního postupu PowerShellu. Pokud nepotřebujete kontrolní body, doporučujeme psát Runbooky pomocí prostředí PowerShell.  K dispozici je několik rozdílů syntaxe při vytváření runbooků pracovního postupu PowerShellu a tyto rozdíly vyžadují pro zápis efektivních pracovních postupů trochu větší práci.

Pracovní postup je pořadí naprogramovaných, propojených kroků, které provádějí dlouhotrvající úlohy nebo vyžadují koordinaci více kroků v rámci více zařízení nebo spravovaných uzlů. Mezi výhody pracovního postupu oproti běžnému skriptu patří schopnost současně provádět akci vůči několika zařízením a schopnost automaticky zotavit po selhání. Pracovní postup prostředí Windows PowerShell je skript Windows PowerShellu, který používá programovací model Windows Workflow Foundation. I když pracovní postup je napsané pomocí syntaxe prostředí Windows PowerShell a spuštění pomocí prostředí Windows PowerShell, je zpracován programovacím modelem Windows Workflow Foundation.

Kompletní informace o tématech v tomto článku najdete v tématu [Začínáme s pracovním postupem prostředí Windows PowerShell](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Základní struktura pracovního postupu

Prvním krokem k převedení skriptu PowerShellu na PowerShellový pracovní postup je jeho uzavření s klíčovým slovem **pracovního postupu** .  Pracovní postup začíná klíčovým slovem **Workflow** následovaným textem skriptu uzavřeným ve složených závorkách. Název pracovního postupu následuje po klíčovém slově **Workflow** , jak je znázorněno v následující syntaxi:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Název pracovního postupu musí odpovídat názvu runbooku Automation. Pokud se Runbook naimportuje, název souboru se musí shodovat s názvem pracovního postupu a musí končit *. ps1*.

K přidání parametrů do pracovního postupu použijte klíčové slovo **param** stejně jako na skript.

## <a name="code-changes"></a>Změny kódu

Kód pracovního postupu PowerShellu vypadá téměř stejně jako kód skriptu PowerShellu s výjimkou některých významných změn.  Následující části popisují změny, které je třeba provést v PowerShellovém skriptu, aby je bylo možné spustit v pracovním postupu.

### <a name="activities"></a>Aktivity

Aktivita je specifická úloha v pracovním postupu. Stejně jako se skript skládá z jednoho nebo více příkazů, pracovní postup se skládá z jedné nebo více aktivit, které se provádějí v pořadí. Pracovní postup prostředí Windows PowerShell automaticky převádí mnoho rutin prostředí Windows PowerShell na aktivity při spuštění pracovního postupu. Když zadáte jednu z těchto rutin v Runbooku, odpovídající aktivita se spustí programovací model Windows Workflow Foundation. Případě rutin bez odpovídající aktivity pracovní postup prostředí Windows PowerShell automaticky spustí rutinu v rámci [InlineScript](#inlinescript) aktivity. K dispozici je sada rutin, které jsou vyloučeny a nemohou být použity v pracovním postupu, pokud je výslovně nezahrnete do bloku aktivity InlineScript. Další podrobnosti o těchto konceptech najdete v tématu [pomocí aktivity ve skriptových pracovních postupech](https://technet.microsoft.com/library/jj574194.aspx).

Aktivity pracovních postupů sdílejí sadu společných parametrů ke konfiguraci svého provozu. Podrobnosti o společných parametrech pracovního postupu najdete v tématu [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Poziční parametry

V pracovním postupu nemůžete použít poziční parametry s aktivitami a rutinami.  To znamená, že je nutné použít názvy parametrů.

Zvažte například následující kód, který získá všechny běžící služby.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Pokud se pokusíte spustit stejný kód v pracovním postupu, zobrazí se zpráva typu "sada parametrů nelze přeložit pomocí zadaných pojmenovaných parametrů".  Pokud to chcete opravit, zadejte název parametru jako v následujícím seznamu.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deserializovatelné objekty

Objekty v pracovních postupech jsou deserializovány.  To znamená, že jejich vlastnosti jsou stále k dispozici, ale ne jejich metody.  Zvažte například následující kód PowerShellu, který zastaví službu pomocí metody Stop objektu Service.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Pokud se pokusíte spustit tento postup v pracovním postupu, zobrazí se chybová zpráva "volání metody se v pracovním postupu prostředí Windows PowerShell nepodporuje."

Jednou z možností je zabalit tyto dva řádky kódu do [InlineScript](#inlinescript) bloku, v takovém případě by $Service by byl objekt služby v rámci bloku.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Další možností je použít jinou rutinu, která provádí stejnou funkci jako metoda, pokud je k dispozici.  V naší ukázce rutina Stop-Service poskytuje stejné funkce jako metoda stop a pro pracovní postup můžete použít následující.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

Aktivita **InlineScript** je užitečná v případě, že potřebujete spustit jeden nebo více příkazů jako tradiční powershellový skript namísto pracovního postupu PowerShellu.  Zatímco příkazy v pracovním postupu jsou odesílány na modelu Windows Workflow Foundation pro zpracování, příkazy v bloku InlineScript jsou zpracovány pomocí prostředí Windows PowerShell.

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

I když aktivity InlineScript můžou být v určitých pracovních postupech kritické, nepodporují konstrukce pracovního postupu a měly by se v případě potřeby používat jenom v následujících případech:

* V bloku InlineScript nemůžete použít [kontrolní body](#checkpoints) . Pokud dojde k selhání v rámci bloku, musí být obnovena od začátku bloku.
* [Paralelní provádění](#parallel-processing) nelze použít uvnitř InlineScriptBlock.
* InlineScript má vliv na škálovatelnost pracovního postupu, protože obsahuje relaci prostředí Windows PowerShell pro celou délku bloku InlineScript.

Další informace o použití InlineScript najdete v tématu [spuštění příkazů prostředí Windows PowerShell v pracovním postupu](https://technet.microsoft.com/library/jj574197.aspx) a [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Paralelní zpracování

Jednou z výhod pracovních postupů prostředí Windows PowerShell je schopnost provádět sadu příkazů paralelně namísto postupně jako v případě typického skriptu.

Pomocí klíčového slova **Parallel** můžete vytvořit blok skriptu s více příkazy, které běží souběžně. K tomu se používá následující syntaxe. V tomto případě Activity1 a "Activity2" spustí ve stejnou dobu. Activity3 se spustí až po dokončení Activity1 a "Activity2".

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Zvažte například následující příkazy PowerShellu, které kopírují více souborů do cílového umístění v síti.  Tyto příkazy jsou spouštěny postupně, aby jeden soubor musel dokončit kopírování před dalším spuštěním.

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

Můžete použít **ForEach-Parallel** konstrukce ke zpracování příkazů pro každou položku v kolekci současně. Položky v kolekci se zpracovávají paralelně, zatímco příkazy v bloku skriptu spouští sekvenčně. K tomu se používá následující syntaxe. V tomto případě Activity1 spustí ve stejnou dobu pro všechny položky v kolekci. Pro každou položku "Activity2" začíná po dokončení Activity1. Activity3 se spustí až po dokončení Activity1 i "Activity2" pro všechny položky. K omezení paralelismus používáme parametr `ThrottleLimit`. Příliš vysoká `ThrottleLimit` může způsobovat problémy. Ideální hodnota pro parametr `ThrottleLimit` závisí na mnoha faktorech ve vašem prostředí. Měli byste zkusit začít s nízkou hodnotou a vyzkoušet jiné hodnoty, dokud nezjistíte, které funguje pro konkrétní situaci.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Následující příklad je podobný předchozímu příkladu, který kopíruje soubory paralelně.  V tomto případě se pro každý soubor po zkopírování zobrazí zpráva.  Po úplném zkopírování je zobrazena zpráva o konečném dokončení.

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
> Nedoporučujeme paralelní spouštění podřízených runbooků, protože se ukázalo, že poskytuje nespolehlivé výsledky. Výstup z podřízené sady Runbook se někdy nezobrazuje a nastavení v jedné podřízené sadě Runbook mohou ovlivnit ostatní paralelní podřízené Runbooky. Proměnné jako $VerbosePreference, $WarningPreference a jiné nemusí být šířeny do podřízených runbooků. A pokud podřízený Runbook změní tyto hodnoty, nemusí být po vyvolání správně obnoveny.

## <a name="checkpoints"></a>Kontrolní body

*Kontrolní bod* je snímek aktuálního stavu pracovního postupu, který obsahuje aktuální hodnotu proměnných a všechny výstupy vygenerované do tohoto bodu. Pokud pracovní postup skončí chybou nebo je pozastaven, pak se při příštím spuštění spustí z posledního kontrolního bodu místo na začátku pracovního postupu.  Kontrolní bod můžete nastavit v pracovním postupu se **Checkpoint-Workflow** aktivity. Azure Automation má funkci s názvem [spravedlivá sdílená](automation-runbook-execution.md#fair-share)sada Runbook, která se spouští po dobu 3 hodin, aby bylo možné spouštět jiné Runbooky. Nakonec se uvolněná sada Runbook znovu načte a v případě, že bude, bude pokračovat v provádění z posledního kontrolního bodu, který byl proveden v sadě Runbook. Aby bylo zajištěno, že sada Runbook bude nakonec dokončena, je nutné přidat kontrolní body v intervalech, které jsou spuštěny méně než 3 hodiny. Při každém spuštění se přidá nový kontrolní bod, a pokud se sada Runbook po 3 hodinách vyřadí z důvodu chyby, sada Runbook se obnoví neomezeně.

V následujícím ukázkovém kódu dojde k výjimce po "Activity2" způsob ukončení pracovního postupu. Po opětovném spuštění pracovního postupu se spustí spuštěním "Activity2", protože se jednalo o hned za poslední nastavenou kontrolní sadu.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Kontrolní body v pracovním postupu byste měli nastavit po aktivitách, které by mohly být náchylné k výjimce a neměly by se opakovat, pokud je pracovní postup obnovený. Pracovní postup může například vytvořit virtuální počítač. Můžete nastavit kontrolní bod před i po příkazech, které k vytvoření virtuálního počítače. Pokud se vytvoření nepovede, příkazy se zopakují, pokud se pracovní postup spustí znovu. Pokud pracovní postup po úspěšném vytvoření selže, pak se virtuální počítač po obnovení pracovního postupu znovu nevytvoří.

Následující příklad zkopíruje více souborů do umístění v síti a nastaví kontrolní bod za každým souborem.  Pokud dojde ke ztrátě síťového umístění, pracovní postup skončí chybou.  Po opětovném spuštění bude pokračovat v posledním kontrolním bodu, což znamená, že jsou přeskočeny pouze soubory, které již byly zkopírovány.

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

Vzhledem k tomu, že přihlašovací údaje uživatelského jména nejsou trvalé po volání aktivity [pozastavit-Workflow](https://technet.microsoft.com/library/jj733586.aspx) nebo po posledním kontrolním bodu, je nutné nastavit přihlašovací údaje na hodnotu null a pak je znovu načíst z úložiště Asset po volání metody **Suspend-Workflow** nebo kontrolní bod.  V opačném případě se může zobrazit následující chybová zpráva: *úlohu pracovního postupu nelze obnovit, protože data trvalosti nelze uložit úplně nebo jsou uložená data trvaly poškozena. Je nutné restartovat pracovní postup.*

Následující stejný kód ukazuje, jak tento postup zpracovat ve vašich sadách Runbook pracovního postupu PowerShellu.

```powershell
workflow CreateTestVms
{
    $Cred = Get-AzureAutomationCredential -Name "MyCredential"
    $null = Connect-AzureRmAccount -Credential $Cred

    $VmsToCreate = Get-AzureAutomationVariable -Name "VmsToCreate"

    foreach ($VmName in $VmsToCreate)
        {
        # Do work first to create the VM (code not shown)

        # Now add the VM
        New-AzureRmVm -VM $Vm -Location "WestUs" -ResourceGroupName "ResourceGroup01"

        # Checkpoint so that VM creation is not repeated if workflow suspends
        $Cred = $null
        Checkpoint-Workflow
        $Cred = Get-AzureAutomationCredential -Name "MyCredential"
        $null = Connect-AzureRmAccount -Credential $Cred
        }
}
```

> [!IMPORTANT]
> **Add-AzureRmAccount** je teď alias pro **Connect-AzureRmAccount**. Pokud se při hledání položek knihovny nezobrazí **příkaz Connect-AzureRMAccount**, můžete použít příkaz **Add-AzureRMAccount**nebo můžete aktualizovat moduly v účtu Automation.

Toto není vyžadováno, pokud ověřujete pomocí účtu Spustit jako nakonfigurovaného s objektem služby.

Další informace o kontrolních bodech najdete v tématu [přidání kontrolních bodů do pracovního postupu skriptu](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Další kroky

* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).


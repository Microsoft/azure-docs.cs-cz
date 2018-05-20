---
title: Učení pracovní postup prostředí PowerShell pro Azure Automation.
description: Tento článek je určený jako rychlé lekce pro autory znají PowerShell pochopit určité rozdíly mezi prostředí PowerShell a pracovní postup prostředí PowerShell a koncepty pro runbooky služby automatizace.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 05/04/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9e9f3cc89dfc6dda18d40950207ea4523f5212c0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Učení klíčové koncepty pracovního postupu prostředí Windows PowerShell pro automatizaci sady runbook

Runbooky ve službě Azure Automation se implementují jako pracovní postupy prostředí Windows PowerShell.  Pracovní postup prostředí Windows PowerShell je podobná skript prostředí Windows PowerShell, ale existují některé významné rozdíly, které mohou být pro nového uživatele matoucí.  Když tento článek je určený k usnadnění psaní sady runbook pomocí prostředí PowerShell. pracovní postup, doporučujeme, abyste že zápisu sady runbook pomocí prostředí PowerShell, pokud potřebujete kontrolní body.  Existuje několik rozdílů v syntaxi při autorizaci sad runbook PowerShell Workflow a tyto rozdíly vyžaduje trochu další práci psaní efektivní pracovních postupů.

Pracovní postup je pořadí naprogramovaných, propojených kroků, které provádějí dlouhodobé úlohy nebo vyžadují koordinaci více kroků v rámci více zařízení nebo spravovaných uzlů. Mezi výhody pracovního postupu oproti běžnému skriptu patří schopnost současně provádět akci vůči několika zařízením a schopnost automaticky zotavit po selhání. Pracovní postup prostředí Windows PowerShell je skript prostředí Windows PowerShell, který používá Windows Workflow Foundation. Když pracovní postup je napsané pomocí syntaxe prostředí Windows PowerShell a spuštění pomocí prostředí Windows PowerShell, je zpracován programovacím modelem Windows Workflow Foundation.

Kompletní informace na témata v tomto článku najdete v tématu [Začínáme s pracovním postupem prostředí Windows PowerShell](http://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Základní struktura pracovního postupu

Prvním krokem k převodu skript prostředí PowerShell do pracovního postupu prostředí PowerShell je uveden s **pracovního postupu** – klíčové slovo.  Pracovní postup začíná **pracovního postupu** – klíčové slovo následovaným tělem skriptu uzavřené do složených závorek. Následuje název pracovního postupu **pracovního postupu** – klíčové slovo, jak je znázorněno v následující syntaxi:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Název pracovního postupu musí odpovídat názvu sady Automation runbook. Pokud sada runbook importována, pak název souboru musí odpovídat názvu pracovního postupu a musí končit *.ps1*.

Chcete-li přidat parametry do pracovního postupu, použijte **Param** – klíčové slovo stejně jako na skript.

## <a name="code-changes"></a>Změny kódu

Kód pracovního postupu Powershellu vypadá téměř shodné s kód skriptu prostředí PowerShell s výjimkou několik významných změn.  Následující části popisují změny, které je potřeba udělat do skriptu prostředí PowerShell pro něj spustit v pracovním postupu.

### <a name="activities"></a>Aktivity

Aktivita je specifická úloha v pracovním postupu. Stejně jako se skript skládá z jednoho nebo více příkazů, pracovní postup se skládá z jedné nebo více aktivit, které se provádějí v pořadí. Pracovní postup prostředí Windows PowerShell automaticky převádí mnoho rutin prostředí Windows PowerShell na aktivity při spuštění pracovního postupu. Pokud určíte jednu z těchto rutin ve vašem runbooku, bude odpovídající aktivita běží modelem Windows Workflow Foundation. Těchto rutin bez odpovídající aktivity pracovního postupu prostředí Windows PowerShell automaticky spustí rutinu v rámci [InlineScript](#inlinescript) aktivity. Existuje sada rutin, které jsou vyloučeny a nejde ho použít v pracovním postupu, pokud je výslovně nezahrnete v bloku InlineScript. Další informace o těchto pojmech najdete v části [pomocí aktivity ve skriptových pracovních postupech](http://technet.microsoft.com/library/jj574194.aspx).

Aktivity pracovních postupů sdílejí sadu společných parametrů ke konfiguraci svého provozu. Podrobnosti o společných parametrech pracovního postupu najdete v tématu [about_WorkflowCommonParameters](http://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Poziční parametry

Poziční parametry nelze použít s rutinami v pracovním postupu a aktivity.  To znamená je, že je nutné použít názvy parametrů.

Zvažte například následující kód, který získá všechny spuštěné služby.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Pokud se pokusíte spustit tento stejný kód v pracovním postupu, obdržíte zprávu jako "sadu parametrů nelze přeložit pomocí zadaných pojmenovaných parametrů."  Chcete-li vyřešit tento problém, zadejte název parametru jako v následujícím příkladu.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deserializovaná objekty

Objekty v pracovních postupech jsou deserializovat.  To znamená, že jejich vlastnosti jsou stále k dispozici, ale není své metody.  Zvažte například následující kód prostředí PowerShell, který zastaví službu pomocí metody Stop objektu služby.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Pokud se pokusíte spustit to v pracovním postupu, se zobrazí chyba s oznámením "volání metody není podporováno v pracovním postupu Windows PowerShell".

Jednou z možností je zabalit tyto dva řádky kódu [InlineScript](#inlinescript) blokovat v takovém případě $Service bude objekt služby v rámci bloku.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Další možností je použít jiná rutina, která provádí stejné funkce jako metodu, pokud je k dispozici.  Naše ukázka rutinu Stop-Service poskytuje stejné funkce jako metoda Stop a můžete použít následující pro pracovní postup.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

**InlineScript** aktivity je užitečné, když je potřeba spustit jeden nebo více příkazů jako tradiční skript prostředí PowerShell namísto pracovního postupu Powershellu.  Zatímco příkazy v pracovním postupu jsou odesílány ke zpracování modelu Windows Workflow Foundation, příkazy v bloku InlineScript jsou zpracovány prostředím Windows PowerShell.

Aktivita InlineScript používá následující syntaxe uvedená níže.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Můžete se vrátit výstup ze InlineScript podle přiřazení výstup k proměnné. Následující příklad zastaví službu a poté uloží název služby.

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

Můžete předat hodnoty do bloku InlineScript, ale musíte použít **$Using** modifikátor oboru.  Následující příklad je stejný jako předchozí příklad, s tím rozdílem, že je název služby poskytované proměnné.

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

Aktivity InlineScript může být důležité v určitých pracovních postupech, nepodporují konstrukce pracovního postupu a musí být použit pouze v případě potřeby z následujících důvodů:

* Nemůžete použít [kontrolní body](#checkpoints) uvnitř bloku InlineScript. Pokud v rámci bloku dojde k selhání, musí pokračovat znovu od začátku bloku.
* Nemůžete použít [paralelní provádění](#parallel-processing) uvnitř InlineScriptBlock.
* InlineScript ovlivňuje škálovatelnost pracovního postupu, protože drží relaci prostředí Windows PowerShell pro celou délku bloku InlineScript.

Další informace o používání InlineScript najdete v tématu [spouštění příkazů prostředí Windows PowerShell v pracovním postupu](http://technet.microsoft.com/library/jj574197.aspx) a [about_InlineScript](http://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Paralelní zpracování

Jednou z výhod pracovních postupů prostředí Windows PowerShell je schopnost provádět sadu příkazů paralelně namísto postupně jako v případě typického skriptu.

Můžete použít **paralelní** – klíčové slovo vytvořit blok skriptu s více příkazy, které běží souběžně. Používá následující syntaxe uvedená níže. V takovém případě aktivity "activity1" a "activity2" spustí ve stejnou dobu. Aktivita "activity3" spustí až po dokončení aktivity "activity1" a "activity2".

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Zvažte například následující příkazy prostředí PowerShell, které zkopírovat soubory do cílového umístění v síti.  Tyto příkazy se spouštějí postupně, že jeden soubor musí dokončit kopírování před zahájením na další.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Následující pracovní postup spouští tyto stejné příkazy paralelně, aby všechny spuštění kopírování ve stejnou dobu.  Až poté, co jsou všechny zkopírovat se dokončení zobrazí zpráva.

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

Můžete použít **ForEach-Parallel** konstrukce proces příkazy pro každou položku v kolekci současně. Položky v kolekci se zpracovávají paralelně, zatímco příkazy v bloku skriptu spouští sekvenčně. Používá následující syntaxe uvedená níže. V takovém případě se aktivity "activity1" spustí ve stejnou dobu pro všechny položky v kolekci. Pro každou položku "activity2" spustí po dokončení aktivity "activity1". Aktivita "activity3" spustí až po dokončení aktivity "activity1" a "activity2" pro všechny položky.

```powershell
ForEach -Parallel ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

V následujícím příkladu je podobně jako v předchozím příkladu kopírování souborů paralelně.  V takovém případě se zobrazí zpráva pro každý soubor, poté, co se zkopíruje.  Až poté, co jsou všechny úplně zkopírovat je konečné dokončení zobrazí zpráva.

```powershell
Workflow Copy-Files
{
    $files = @("C:\LocalPath\File1.txt","C:\LocalPath\File2.txt","C:\LocalPath\File3.txt")

    ForEach -Parallel ($File in $Files)
    {
        Copy-Item -Path $File -Destination \\NetworkPath
        Write-Output "$File copied."
    }

    Write-Output "All files copied."
}
```

> [!NOTE]
> Nedoporučujeme systémem podřízené runbooky paralelně, protože to se ukázalo nespolehlivé výsledky. Výstup podřízeného runbooku někdy nezobrazuje a nastavení v jedné podřízené sady runbook může ovlivnit další paralelní podřízené runbooky. Proměnné jako $VerbosePreference, $WarningPreference a dalších nemusí rozšíří do podřízených runbooků. A pokud podřízené sady runbook změní tyto hodnoty, se nemusí správně po volání.

## <a name="checkpoints"></a>Kontrolní body

A *kontrolního bodu* je snímek aktuálního stavu pracovního postupu, který obsahuje aktuální hodnotu proměnných a jakéhokoli výstupu generovaného do tohoto bodu. Pokud pracovní postup končí chybu nebo je pozastaveno, pak při příštím spuštění zahájí se od svého posledního kontrolního bodu místo spuštění pracovního postupu.  V pracovním postupu se můžete nastavit kontrolní bod **Checkpoint-Workflow** aktivity.

V následujícím vzorovém kódu dojde k výjimce po "activity2" způsobí ukončení pracovního postupu. Při dalším spuštění pracovního postupu, spustí se spuštěním aktivity Activity2, protože byla poslední po nastavení posledního kontrolního bodu.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

V pracovním postupu byste měli nastavit kontrolní body, po aktivity, které mohou být náchylné k výjimce a neměla by být opakovat, pokud je pracovní postup byl obnoven. Pracovní postup může například vytvořit virtuální počítač. Můžete nastavit kontrolní bod před i po příkazů pro vytvoření virtuálního počítače. Pokud se vytváření nezdaří, bude příkazy opakovat, pokud je pracovní postup spustit znovu. Pokud pracovní postup selže po je vytvoření úspěšné, pak virtuální počítač nebude znovu vytvořen při obnovení pracovního postupu.

Následující příklad zkopíruje více souborů do umístění v síti a nastaví kontrolní bod po jednotlivých souborů.  Pokud dojde ke ztrátě umístění v síti, pracovním postupem končí v chybě.  Když spustíte znovu, bude pokračovat od posledního kontrolního bodu, což znamená, že jsou přeskočeny pouze soubory, které již byly zkopírovány.

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

Protože uživatelské jméno pověření nejsou trvalé po zavolání metody [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) aktivity nebo po poslední kontrolní bod, je nutné nastavit pověření, která mají hodnotu null a potom je znovu načíst z úložiště asset po **Suspend-Workflow** nebo je vyvolán kontrolní bod.  Jinak, zobrazí se následující chybová zpráva: *úlohy pracovního postupu nelze pokračovat, buď protože trvalosti dat nelze uložit zcela nebo uložit trvalosti dat je poškozeno. Je nutné restartovat pracovní postup.*

Následující stejný kód ukazuje, jak to postarají ve vašich runboocích pracovního postupu Powershellu.

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

Není požadováno v případě se ověřujete pomocí nakonfigurovaný s hlavní službou účet Spustit jako.

Další informace o kontrolních bodech najdete v tématu [přidání kontrolních bodů do pracovního postupu skriptu](http://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Další postup

* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).

---
title: Učení pracovní postup prostředí PowerShell pro Azure Automation
description: Tento článek slouží jako rychlý lekce pro autory zkušenosti s prostředím PowerShell znát konkrétní rozdíly mezi Powershellu a pracovní postup prostředí PowerShell a koncepty pro runbooky služby Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 12/14/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: b60e1639a1c32763c4759720fe61b0e571fc9dd1
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53437091"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Učení klíčové koncepty pracovního postupu Windows Powershellu pro automatizaci sady runbook

Runbooky ve službě Azure Automation se implementují jako pracovní postupy prostředí Windows PowerShell.  V pracovním postupu Windows Powershellu se podobá do skriptu Windows Powershellu, ale má několik významných rozdílů, které mohou být matoucí pro nového uživatele.  Přestože tento článek je určený k vám usnadní vytváření sad runbook pomocí prostředí PowerShell workflowu, doporučujeme, abyste že zápisu sady runbook pomocí prostředí PowerShell, pokud je nepotřebujete kontrolní body.  Existuje několik rozdílů v syntaxi při autorizaci sad runbook pracovního postupu Powershellu a o něco více práce pro zápis efektivní pracovní postupy vyžadují tyto rozdíly.

Pracovní postup je pořadí naprogramovaných, propojených kroků, které provádějí dlouhotrvající úlohy nebo vyžadují koordinaci více kroků v rámci více zařízení nebo spravovaných uzlů. Mezi výhody pracovního postupu oproti běžnému skriptu patří schopnost současně provádět akci vůči několika zařízením a schopnost automaticky zotavit po selhání. V pracovním postupu Windows Powershellu je skript prostředí Windows PowerShell, který používá model Windows Workflow Foundation. I když pracovní postup je napsané pomocí syntaxe prostředí Windows PowerShell a spuštění pomocí prostředí Windows PowerShell, je zpracován programovacím modelem Windows Workflow Foundation.

Kompletní informace o tématech v tomto článku najdete v tématu [Začínáme s pracovním postupem prostředí Windows PowerShell](https://technet.microsoft.com/library/jj134242.aspx).

## <a name="basic-structure-of-a-workflow"></a>Základní struktura pracovního postupu

Prvním krokem při převádění skript prostředí PowerShell v pracovním postupu Powershellu je uzavírající ji **pracovního postupu** – klíčové slovo.  Pracovní postup začíná **pracovního postupu** – klíčové slovo následovaným tělem skriptu, které jsou uzavřeny ve složených závorkách. Následuje název pracovního postupu **pracovního postupu** – klíčové slovo, jak je znázorněno v následující syntaxi:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Název pracovního postupu musí odpovídat názvu runbooku Automation. Pokud je sada runbook importována, pak název souboru musí odpovídat názvu pracovního postupu a musí končit *.ps1*.

Chcete-li přidat parametry do pracovního postupu, použijte **Param** – klíčové slovo stejně jako na skript.

## <a name="code-changes"></a>Změny kódu

Kód pracovního postupu Powershellu vypadá téměř stejný jako kód skriptu prostředí PowerShell s výjimkou několik významných změn.  Následující části popisují změny, které je třeba provést skript prostředí PowerShell pro něj spustit v pracovním postupu.

### <a name="activities"></a>Aktivity

Aktivita je specifická úloha v pracovním postupu. Stejně jako se skript skládá z jednoho nebo více příkazů, pracovní postup se skládá z jedné nebo více aktivit, které se provádějí v pořadí. Pracovní postup prostředí Windows PowerShell automaticky převádí mnoho rutin prostředí Windows PowerShell na aktivity při spuštění pracovního postupu. Pokud zadáte jednu z těchto rutin ve vašem runbooku, bude odpovídající aktivita běží modelem Windows Workflow Foundation. Případě rutin bez odpovídající aktivity pracovní postup prostředí Windows PowerShell automaticky spustí rutinu v rámci [InlineScript](#inlinescript) aktivity. Existuje sada rutin, které jsou vyloučeny a nelze jej použít v pracovním postupu, pokud je výslovně nezahrnete do bloku InlineScript. Další podrobnosti o těchto konceptech najdete v tématu [pomocí aktivity ve skriptových pracovních postupech](https://technet.microsoft.com/library/jj574194.aspx).

Aktivity pracovních postupů sdílejí sadu společných parametrů ke konfiguraci svého provozu. Podrobnosti o společných parametrech pracovního postupu najdete v tématu [about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Poziční parametry

Poziční parametry nelze použít s aktivity a rutin v pracovním postupu.  To znamená je, že musíte použít názvy parametrů.

Zvažte například následující kód, který získá všechny služby spuštěné.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Pokud se pokusíte spustit tento stejný kód v pracovním postupu, se zobrazí zpráva jako "sadu parametrů nejde rozeznat zadané pojmenované parametry."  To pokud chcete opravit, zadejte název parametru, viz následující příklad.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Deserializovaná objekty

Objekty v pracovních postupech jsou deserializovat.  To znamená, že jejich vlastnosti jsou stále k dispozici, ale nikoli jejich metody.  Zvažte například následující kód Powershellu, který zastaví službu pomocí metody Stop objekt služby.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Pokud se pokusíte spustit v pracovním postupu, se zobrazí chyba s oznámením "volání metody není podporováno v pracovním postupu Windows Powershellu."

Jednou z možností je zabalit tyto dva řádky kódu ve [InlineScript](#inlinescript) blok, ve které $Service by se objekt služby v rámci bloku.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Další možností je použít jiná rutina, která provádí stejné funkce jako metodu, pokud je k dispozici.  V naší ukázce rutinu Stop-Service poskytuje stejné funkce jako metoda Stop a můžete použít následující pracovní postup.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>InlineScript

**InlineScript** aktivity je užitečné, když je potřeba spustit jeden nebo více příkazů jako tradiční skript prostředí PowerShell místo pracovního postupu Powershellu.  Zatímco příkazy v pracovním postupu jsou odesílány na modelu Windows Workflow Foundation pro zpracování, příkazy v bloku InlineScript jsou zpracovány pomocí prostředí Windows PowerShell.

Aktivita InlineScript používá syntaxi je uvedeno níže.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Může vrátit výstup z InlineScript přiřazením výstup k proměnné. Následující příklad zastaví službu a potom vypíše název služby.

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

Hodnoty můžete předat do bloku InlineScript, ale je nutné použít **$Using** modifikátor oboru.  Následující příklad je stejný jako předchozí příklad, s tím rozdílem, že je zadaný název služby pomocí proměnné.

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

Aktivity InlineScript můžou být důležité v určitých pracovních postupů, nepodporují konstrukce pracovního postupu a by měla sloužit pouze pokud je to nezbytné z následujících důvodů:

* Nemůžete použít [kontrolní body](#checkpoints) uvnitř bloku InlineScript. Pokud v rámci bloku dojde k selhání, musí pokračovat znovu od začátku bloku.
* Nemůžete použít [paralelní provádění](#parallel-processing) uvnitř InlineScriptBlock.
* InlineScript má vliv na škálovatelnost pracovního postupu, protože drží relaci prostředí Windows PowerShell pro celou délku bloku InlineScript.

Další informace o použití InlineScript, naleznete v tématu [spouštění příkazů prostředí Windows PowerShell v pracovním postupu](https://technet.microsoft.com/library/jj574197.aspx) a [o skriptu inlinescript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Paralelní zpracování

Jednou z výhod pracovních postupů prostředí Windows PowerShell je schopnost provádět sadu příkazů paralelně namísto postupně jako v případě typického skriptu.

Můžete použít **paralelní** – klíčové slovo Chcete-li vytvořit blok skriptu s více příkazů, na kterých běží souběžně. Tady se používá následující syntaxi uvedenou níže. V takovém případě aktivity "activity1" a "activity2" spustí ve stejnou dobu. Aktivita "activity3" spustí až po dokončení aktivit "activity1" a "activity2".

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Představte si třeba následující příkazy Powershellu, které zkopírovat víc souborů cílové sítě.  Tyto příkazy se spouští postupně, takže jeden soubor musí dokončit kopírování před spuštěním další.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Následující pracovní postup spouští tyto stejné příkazy paralelně tak, aby všechny zahájit kopírování ve stejnou dobu.  Pouze po jsou všechny zkopírovat zpráva o dokončení zobrazí.

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

Můžete použít **ForEach-Parallel** konstrukce ke zpracování příkazů pro každou položku v kolekci současně. Položky v kolekci se zpracovávají paralelně, zatímco příkazy v bloku skriptu spouští sekvenčně. Tady se používá následující syntaxi uvedenou níže. V takovém případě se aktivity "activity1" spustí ve stejnou dobu pro všechny položky v kolekci. Pro každou položku "activity2" spustí po dokončení aktivit "activity1". Aktivita "activity3" spustí až po dokončení aktivit "activity1" a "activity2" pro všechny položky. Používáme `ThrottleLimit` parametr omezte paralelismus. Příliš vysokou `ThrottleLimit` může způsobit problémy. Ideální hodnotu `ThrottleLimit` parametr závislá na mnoha faktorech ve vašem prostředí. By měl akci spuštění s nízkou hodnotu a zkuste jiné zvýšení hodnoty, dokud nenajdete funguje pro konkrétní situaci.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Následující příklad je podobné jako v předchozím příkladu kopírování souborů paralelně.  V takovém případě se zobrazí zpráva pro každý soubor, poté, co se zkopíruje.  Pouze po všech zcela zkopírovat je úplného dokončení zobrazí zpráva.

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
> Nedoporučujeme podřízené runbooky spouštějí paralelně, protože to se ukázalo k nespolehlivým výsledkům. Výstup podřízeného runbooku někdy nezobrazuje a nastavení v jedné podřízené sady runbook může ovlivnit jiné paralelní podřízené runbooky. Proměnné, jako je například $VerbosePreference $WarningPreference a ostatními nemusí rozšíří na podřízené runbooky. A Pokud podřízený runbook změní tyto hodnoty, se nemusí být obnovena správně po volání.

## <a name="checkpoints"></a>Kontrolní body

A *kontrolního bodu* je snímek aktuálního stavu pracovního postupu, který obsahuje aktuální hodnotu proměnných a jakéhokoli výstupu generovaného do tohoto bodu. Pokud pracovní postup skončí s chybou nebo je pozastavená, pak při příštím spuštění spustí od svého posledního kontrolního bodu namísto spuštění pracovního postupu.  Kontrolní bod můžete nastavit v pracovním postupu se **Checkpoint-Workflow** aktivity.

V následujícím vzorovém kódu dojde k výjimce po "activity2" způsobí ukončení pracovního postupu. Při spuštění pracovního postupu znova spustí spuštěním aktivity Activity2, protože byla poslední po nastavení posledního kontrolního bodu.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Po opakovaných aktivit, které mohou být náchylné k výjimce a neměla by být, pokud pracovní postup obnovení byste měli nastavit kontrolní body v pracovním postupu. Váš pracovní postup může například vytvořit virtuální počítač. Můžete nastavit kontrolní bod před i po příkazech, které k vytvoření virtuálního počítače. Pokud se vytváření nezdaří, bude příkazy opakovat, pokud je pracovní postup spustit znovu. Pokud po vytvoření úspěšné, pracovní postup se nezdaří, pak virtuální počítač nebude znovu vytvořen při obnovení pracovního postupu.

Následující příklad zkopíruje více souborů do umístění v síti a nastaví kontrolní bod po jednotlivých souborů.  Pokud dojde ke ztrátě síťové umístění, pracovní postup skončí s chybou.  Při spuštění znovu, bude pokračovat v posledního kontrolního bodu, což znamená, že jsou vynechány pouze soubory, které již byly zkopírovány.

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

Protože uživatelské jméno pověření nejsou trvale uložena po volání [Suspend-Workflow](https://technet.microsoft.com/library/jj733586.aspx) aktivity nebo po poslední kontrolní bod, je nutné nastavit přihlašovací údaje s hodnotou null a poté je znovu načíst z úložiště asset po  **Suspend-Workflow** nebo kontrolního bodu je volána.  V opačném případě může zobrazit následující chybová zpráva: *Nelze obnovit úlohu pracovního postupu, buď persistentních dat nelze uložit zcela nebo uložil persistentních dat byla poškozena. Je nutné restartovat pracovního postupu.*

Následující stejný kód ukazuje, jak o to postarají v runboocích pracovního postupu Powershellu.

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
> **Add-AzureRmAccount** je nyní alias pro **Connect-AzureRMAccount**. Při vyhledávání knihovny položky, pokud se nezobrazí **Connect-AzureRMAccount**, můžete použít **Add-AzureRmAccount**, nebo ve vašem účtu Automation můžete aktualizovat moduly.

To není potřeba pokud ověřujete pomocí účtu spustit jako nakonfigurovali instanční objekt.

Další informace o kontrolních bodech najdete v tématu [přidání kontrolních bodů do pracovního postupu skriptu](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Další postup

* První kroky s runbooky pracovních postupů PowerShellu najdete v článku [Můj první runbook pracovního postupu PowerShellu](automation-first-runbook-textual.md).

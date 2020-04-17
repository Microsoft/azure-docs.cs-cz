---
title: Pracovní postup prostředí Learning PowerShell pro automatizaci Azure
description: Tento článek je určen jako rychlá lekce pro autory obeznámené s prostředím PowerShell, aby pochopili konkrétní rozdíly mezi powershellovým a powershellovým pracovním postupem a koncepty platnými pro runbooky automatizace.
services: automation
ms.subservice: process-automation
ms.date: 12/14/2018
ms.topic: conceptual
ms.openlocfilehash: 1b275239c19584bc11472711a32972aa3ebea1ab
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457531"
---
# <a name="learning-key-windows-powershell-workflow-concepts-for-automation-runbooks"></a>Koncepty pracovního postupu windows powershellu pro runbooky automatizace

Runbooky v Azure Automation se implementují jako pracovní postupy Prostředí Windows PowerShell.  Pracovní postup prostředí Windows PowerShell je podobný skriptu prostředí Windows PowerShell, ale má některé významné rozdíly, které mohou být matoucí pro nového uživatele.  Zatímco tento článek je určen k tomu, aby vám pomohl psát runbooky pomocí pracovního postupu prostředí PowerShell, doporučujeme psát runbooky pomocí prostředí PowerShell, pokud nepotřebujete kontrolní body.  Při vytváření runbooků pracovního postupu prostředí PowerShell existuje několik rozdílů syntaxe a tyto rozdíly vyžadují trochu více práce při psaní efektivních pracovních postupů.

Pracovní postup je pořadí naprogramovaných, propojených kroků, které provádějí dlouhodobé úlohy nebo vyžadují koordinaci více kroků v rámci více zařízení nebo spravovaných uzlů. Mezi výhody pracovního postupu oproti běžnému skriptu patří schopnost současně provádět akci vůči několika zařízením a schopnost se automaticky zotavit po selhání. Pracovní postup prostředí Windows PowerShell je skript prostředí Windows PowerShell, který používá službu Windows Workflow Foundation. Přestože je pracovní postup napsán pomocí syntaxe prostředí Windows PowerShell a spuštěn tímto prostředím, je zpracován programovacím modelem Windows Workflow Foundation.

Úplné podrobnosti o tématech v tomto článku naleznete v [tématu Začínáme s pracovním postupem prostředí Windows PowerShell](https://technet.microsoft.com/library/jj134242.aspx).

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="basic-structure-of-a-workflow"></a>Základní struktura pracovního postupu

Prvním krokem k převodu skriptu prostředí PowerShell na pracovní `Workflow` postup prostředí PowerShell je jeho uzavření s klíčovým slovem.  Pracovní postup začíná `Workflow` klíčovým slovem následovaným textem skriptu uzavřeného ve složených závorkách. Název pracovního postupu se `Workflow` řídí klíčovým slovem znázorněno v následující syntaxi:

```powershell
Workflow Test-Workflow
{
    <Commands>
}
```

Název pracovního postupu se musí shodovat s názvem runbooku automatizace. Pokud se soubor Runbook importuje, musí se název souboru shodovat s názvem pracovního postupu a musí končit v *souboru PS1*.

Chcete-li do pracovního postupu `Param` přidat parametry, použijte klíčové slovo stejně jako ve skriptu.

## <a name="code-changes"></a>Změny kódu

Kód pracovního postupu Prostředí PowerShell vypadá téměř identicky s kódem skriptu PowerShellu s výjimkou několika významných změn.  Následující části popisují změny, které je třeba provést ve skriptu prostředí PowerShell, aby mohl být spuštěn v pracovním postupu.

### <a name="activities"></a>Aktivity

Aktivita je specifická úloha v pracovním postupu. Stejně jako se skript skládá z jednoho nebo víc příkazů, pracovní postup se skládá z jedné nebo víc aktivit, které se provádějí v uvedeném pořadí. Pracovní postup prostředí Windows PowerShell automaticky převádí mnoho rutin prostředí Windows PowerShell na aktivity při spuštění pracovního postupu. Když zadáte jednu z těchto rutin v aplikaci Runbook, odpovídající aktivitu spustí Nadace pracovního postupu systému Windows. U těchto rutin bez odpovídající aktivity pracovní postup prostředí Windows PowerShell automaticky spustí rutinu v rámci aktivity [InlineScript.](#inlinescript) Existuje sada rutin, které jsou vyloučeny a nelze je použít v pracovním postupu, pokud je explicitně nezahrnete do bloku InlineScript. Další podrobnosti o těchto konceptech naleznete [v tématu Použití aktivit v pracovních postupech skriptu](https://technet.microsoft.com/library/jj574194.aspx).

Aktivity pracovních postupů sdílejí sadu společných parametrů ke konfiguraci svého provozu. Podrobnosti o běžných parametrech pracovního postupu naleznete [v tématu about_WorkflowCommonParameters](https://technet.microsoft.com/library/jj129719.aspx).

### <a name="positional-parameters"></a>Poziční parametry

Polohové parametry nelze použít s aktivitami a rutinami v pracovním postupu.  To vše znamená, že je nutné použít názvy parametrů.

Zvažte například následující kód, který získá všechny spuštěné služby.

```azurepowershell-interactive
Get-Service | Where-Object {$_.Status -eq "Running"}
```

Pokud se pokusíte spustit stejný kód v pracovním postupu, zobrazí se zpráva jako "Sada parametrů nelze vyřešit pomocí zadaných pojmenovaných parametrů."  Chcete-li tento problém opravit, zadejte název parametru jako v následujícím textu.

```powershell
Workflow Get-RunningServices
{
    Get-Service | Where-Object -FilterScript {$_.Status -eq "Running"}
}
```

### <a name="deserialized-objects"></a>Rekonstruované objekty

Objekty v pracovních postupech jsou dekonstruovány.  To znamená, že jejich vlastnosti jsou stále k dispozici, ale ne jejich metody.  Zvažte například následující kód prostředí PowerShell, který zastaví službu pomocí metody Stop objektu Service.

```azurepowershell-interactive
$Service = Get-Service -Name MyService
$Service.Stop()
```

Pokud se pokusíte spustit v pracovním postupu, zobrazí se chyba "Vyvolání metody není podporováno v pracovním postupu prostředí Windows PowerShell."

Jednou z možností je zalomit tyto dva řádky kódu v bloku [InlineScript,](#inlinescript) v takovém případě $Service by být objekt služby v rámci bloku.

```powershell
Workflow Stop-Service
{
    InlineScript {
        $Service = Get-Service -Name MyService
        $Service.Stop()
    }
}
```

Další možností je použít jinou rutinu, která provádí stejné funkce jako metoda, pokud je k dispozici.  V naší ukázce rutina Stop-Service poskytuje stejné funkce jako Stop metoda a můžete použít následující pro pracovní postup.

```powershell
Workflow Stop-MyService
{
    $Service = Get-Service -Name MyService
    Stop-Service -Name $Service.Name
}
```

## <a name="inlinescript"></a>Vložený skript

Aktivita`InlineScript` je užitečná, když potřebujete spustit jeden nebo více příkazů jako tradiční skript prostředí PowerShell namísto pracovního postupu Prostředí PowerShell.  Přesto jsou příkazy v pracovním postupu odesílány ke zpracování do programovacího modelu Windows Workflow Foundation, příkazy v bloku InlineScript jsou zpracovány prostředím Windows PowerShell.

InlineScript používá následující syntaxi uvedenou níže.

```powershell
InlineScript
{
    <Script Block>
} <Common Parameters>
```

Výstup z inlinescriptu můžete vrátit přiřazením výstupu proměnné. Následující příklad zastaví službu a potom vyprodnásleduje název služby.

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

Hodnoty můžete předat do bloku InlineScript, ale musíte použít **$Using** modifikátor oboru.  Následující příklad je shodný s předchozím příkladem s tím rozdílem, že název služby je poskytován proměnnou.

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

Zatímco aktivity InlineScript mohou být v určitých pracovních postupech kritické, nepodporují konstrukce pracovního postupu a měly by být používány pouze v případě potřeby z následujících důvodů:

* [Kontrolní body](#checkpoints) nelze použít uvnitř bloku InlineScript. Pokud dojde k selhání v rámci bloku, musí být obnovena od začátku bloku.
* Paralelní [spuštění](#parallel-processing) nelze použít uvnitř inlineScriptblocku.
* InlineScript ovlivňuje škálovatelnost pracovního postupu, protože obsahuje relaci prostředí Windows PowerShell po celou délku bloku InlineScript.

Další informace o používání jazyka InlineScript naleznete v [tématu Spuštění příkazů prostředí Windows PowerShell v pracovním postupu](https://technet.microsoft.com/library/jj574197.aspx) a [about_InlineScript](https://technet.microsoft.com/library/jj649082.aspx).

## <a name="parallel-processing"></a>Paralelní zpracování

Jednou z výhod pracovních postupů prostředí Windows PowerShell je schopnost provádět sadu příkazů paralelně namísto postupně jako v případě typického skriptu.

Pomocí klíčového `Parallel` slova můžete vytvořit blok skriptu s více příkazy, které běží souběžně. To používá následující syntaxi uvedenou níže. V tomto případě Activity1 a Activity2 začíná ve stejnou dobu. Activity3 spustí až po activity1 a Activity2 byly dokončeny.

```powershell
Parallel
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Zvažte například následující příkazy prostředí PowerShell, které kopírují více souborů do cíle sítě.  Tyto příkazy jsou spouštěny postupně, takže jeden soubor musí dokončit kopírování před dalším spuštěním.

```azurepowershell-interactive
Copy-Item -Path C:\LocalPath\File1.txt -Destination \\NetworkPath\File1.txt
Copy-Item -Path C:\LocalPath\File2.txt -Destination \\NetworkPath\File2.txt
Copy-Item -Path C:\LocalPath\File3.txt -Destination \\NetworkPath\File3.txt
```

Následující pracovní postup spustí tyto stejné příkazy paralelně tak, aby všechny začít kopírování ve stejnou dobu.  Teprve po jejich zkopírování je zobrazena zpráva o dokončení.

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

`ForEach -Parallel` Konstrukce můžete použít ke zpracování příkazů pro každou položku v kolekci současně. Položky v kolekci jsou zpracovávány paralelně, zatímco příkazy v bloku skriptu se spouštějí postupně. To používá následující syntaxi uvedenou níže. V tomto případě Activity1 začíná současně pro všechny položky v kolekci. Pro každou položku Activity2 začíná po Activity1 je dokončena. Activity3 spustí pouze po Activity1 a Activity2 byly dokončeny pro všechny položky. Používáme `ThrottleLimit` parametr k omezení paralelismu. Příliš vysoká `ThrottleLimit` může způsobit problémy. Ideální hodnota parametru `ThrottleLimit` závisí na mnoha faktorech ve vašem prostředí. Měli byste zkusit začít s nízkou hodnotou a zkuste různé rostoucí hodnoty, dokud nenajdete ten, který pracuje pro vaši konkrétní situaci.

```powershell
ForEach -Parallel -ThrottleLimit 10 ($<item> in $<collection>)
{
    <Activity1>
    <Activity2>
}
<Activity3>
```

Následující příklad je podobný předchozímu příkladu kopírování souborů paralelně.  V tomto případě se zobrazí zpráva pro každý soubor po jeho zkopírování.  Teprve poté, co jsou všechny zcela zkopírovány, je zobrazena zpráva o konečném dokončení.

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
> Nedoporučujeme spouštět podřízené runbooky paralelně, protože se ukázalo, že to poskytuje nespolehlivé výsledky. Výstup z podřízeného runbooku se někdy nezobrazuje a nastavení v jednom podřízeném runbooku může ovlivnit ostatní paralelní podřízené runbooky. Proměnné jako $VerbosePreference, $WarningPreference a jiné nemusí být šířeny do podřízených runbooků. A pokud podřízený runbook tyto hodnoty změní, nemusí být po vyvolání správně obnoveny.

## <a name="checkpoints"></a>Kontrolní body

*Kontrolní bod* je snímek aktuálního stavu pracovního postupu, který obsahuje aktuální hodnotu pro proměnné a veškerý výstup generovaný do tohoto bodu. Pokud pracovní postup skončí omylem nebo je pozastaven, při příštím spuštění se spustí z posledního kontrolního bodu namísto začátku pracovního postupu.  Můžete nastavit kontrolní bod v `Checkpoint-Workflow` pracovním postupu s aktivitou. Azure Automation má funkci nazvanou [spravedlivé sdílení](automation-runbook-execution.md#fair-share), kde je uvolněna všechna runbook, která běží po dobu 3 hodin, aby bylo možné spustit jiné sady Runbook. Nakonec uvolněná runbook bude znovu načtena a když je, bude pokračovat v provádění z posledního kontrolního bodu v runbooku. Chcete-li zaručit, že sada Runbook bude nakonec dokončena, je nutné přidat kontrolní body v intervalech, které běží méně než 3 hodiny. Pokud při každém spuštění je přidán nový kontrolní bod a pokud se runbook vyřazuje po 3 hodinách z důvodu chyby, bude runbook obnoven a to po neomezenou dobu.

V následujícím ukázkovém kódu dojde k výjimce po Activity2 způsobuje ukončení pracovního postupu. Při spuštění pracovního postupu znovu spustí spuštěním Activity2 protože to bylo těsně po poslední sadu kontrolního bodu.

```powershell
<Activity1>
Checkpoint-Workflow
<Activity2>
<Exception>
<Activity3>
```

Měli byste nastavit kontrolní body v pracovním postupu po aktivitách, které mohou být náchylné k výjimce a neměly by se opakovat, pokud je pracovní postup obnoven. Váš pracovní postup může například vytvořit virtuální počítač. Můžete nastavit kontrolní bod před i po příkazech, které slouží k vytvoření virtuálního počítače. Pokud se vytvoření nezdaří, příkazy se budou opakovat, pokud bude pracovní postup znovu spuštěn. Pokud se pracovní postup po úspěšném vytvoření nezdaří, virtuální počítač nebude znovu vytvořen při obnovení pracovního postupu.

Následující příklad zkopíruje více souborů do síťového umístění a za každým souborem nastaví kontrolní bod.  Pokud dojde ke ztrátě síťového umístění, pracovní postup skončí omylem.  Při spuštění znovu bude pokračovat na poslední kontrolní bod, což znamená, že jsou přeskočeny pouze soubory, které již byly zkopírovány.

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

Vzhledem k tomu, že pověření uživatelského jména nejsou trvalé po volání [aktivity pozastavit pracovní postup](https://technet.microsoft.com/library/jj733586.aspx) nebo po posledním kontrolním bodu, je třeba nastavit pověření na hodnotu null a potom je znovu načíst z úložiště majetku po `Suspend-Workflow` nebo kontrolní bod je volána.  V opačném případě se může zobrazit následující chybová zpráva:`The workflow job cannot be resumed, either because persistence data could not be saved completely, or saved persistence data has been corrupted. You must restart the workflow.`

Následující stejný kód ukazuje, jak to zpracovat ve vašich runbookech pracovního postupu prostředí PowerShell.

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
> Pro negrafické sady Runbook `Add-AzAccount` `Add-AzureRMAccount` prostředí PowerShell a jsou aliasy pro [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Můžete použít tyto rutiny nebo můžete [aktualizovat moduly](automation-update-azure-modules.md) v účtu Automation na nejnovější verze. Možná budete muset aktualizovat moduly, i když jste právě vytvořili nový účet Automatizace.


To není nutné, pokud ověřujete pomocí účtu Spustit jako nakonfigurovaného s instančním objektem.

Další informace o kontrolních bodech naleznete [v tématu Přidání kontrolních bodů do pracovního postupu skriptu](https://technet.microsoft.com/library/jj574114.aspx).

## <a name="next-steps"></a>Další kroky

* Pokud chcete začít s runbooky pracovních postupů Prostředí PowerShell, [přečtěte si první runbook pracovního postupu PowerShellu.](automation-first-runbook-textual.md)


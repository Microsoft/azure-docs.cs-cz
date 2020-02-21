---
title: Podřízené runbooky v Azure Automation
description: Popisuje různé metody spuštění sady Runbook v Azure Automation z jiné sady Runbook a sdílení informací mezi nimi.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 6acf66e01c4f7b4bd2735687f542a0dbf472cfb4
ms.sourcegitcommit: 0a9419aeba64170c302f7201acdd513bb4b346c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77500199"
---
# <a name="child-runbooks-in-azure-automation"></a>Podřízené runbooky v Azure Automation

Jedná se o doporučený postup v Azure Automation k zápisu opakovaně použitelných modulárních runbooků se samostatnou funkcí volanou jinými sadami Runbook. Nadřazený Runbook často volá jednu nebo více podřízených runbooků, aby provedl požadované funkce. Existují dva způsoby, jak volat podřízenou sadu Runbook, a existují různé rozdíly, které byste měli pochopit, abyste mohli určit, který nejvhodnější je pro vaše scénáře nejvhodnější.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Vyvolání podřízeného Runbooku pomocí vloženého spuštění

Chcete-li vyvolání runbooku přiřazeného z jiného runbooku, použijte název sady runbook a zadejte hodnoty jeho parametrů stejně, jako byste používali aktivitu nebo rutinu.  Všechny Runbooky ve stejném účtu Automation jsou k dispozici všem ostatním, aby je bylo možné používat tímto způsobem. Nadřazený Runbook čeká na dokončení podřízeného Runbooku před přechodem na další řádek a jakýkoliv výstup je vrácen přímo nadřazenému objektu.

Když vyvoláte přiřazený runbook, spustí se ve stejné úloze jako nadřízený runbook. V historii úloh podřízeného Runbooku není žádný náznak. Všechny výjimky a jakékoli výstupy streamu z podřízeného Runbooku jsou přidružené k nadřazenému. Výsledkem tohoto chování je méně úloh a snazší sledování a řešení potíží.

Při publikování Runbooku musí být všechny podřízené Runbooky, které volá, už publikované. Důvodem je to, že Azure Automation při kompilování Runbooku sestaví přidružení s jakýmikoli podřízenými Runbooky. Pokud podřízené Runbooky ještě nejsou publikované, zdá se, že se nadřazený Runbook správně publikuje, ale při jeho spuštění vygeneruje výjimku. Pokud k tomu dojde, můžete nadřazený Runbook znovu publikovat, aby správně odkazoval na podřízené Runbooky. Nemusíte znovu publikovat nadřazený Runbook, pokud se změní kterákoli podřízená sada Runbook, protože přidružení již bylo vytvořeno.

Parametry podřízeného Runbooku s názvem inline můžou být libovolného datového typu, včetně složitých objektů. Není k dispozici [serializace JSON](start-runbooks.md#runbook-parameters), protože při spuštění sady runbook pomocí Azure Portal nebo pomocí rutiny [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) je k dispozici.

### <a name="runbook-types"></a>Typy runbooků

Které typy runbooků se můžou vzájemně volat?

* [Powershellový Runbook](automation-runbook-types.md#powershell-runbooks) a [Grafická sada Runbook](automation-runbook-types.md#graphical-runbooks) můžou zavolat každý další vložený, protože obě jsou založené na PowerShellu.
* [Runbook pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks) a powershellový Runbook pracovního postupu PowerShellu můžou zavolat každé další vložené, protože obě jsou založené na pracovním postupu PowerShellu.
* Typy PowerShellu a typy pracovních postupů PowerShellu nemůžou volat každý druhý vložený a musí používat rutinu **Start-AzAutomationRunbook**.

Kdy se týká pořadí publikování?

Pořadí publikování runbooků pouze pro PowerShellový pracovní postup a runbooky PowerShellu pro grafické pracovní postupy.

Když vaše sada Runbook zavolá podřízenou sadu Runbook pracovního postupu PowerShellu pomocí vloženého spuštění, použije název Runbooku. Název musí začínat řetězcem ".\\– určuje, že se skript nachází v místním adresáři.

### <a name="example"></a>Příklad

Následující příklad spustí test podřízeného Runbooku, který přijímá složitý objekt, celočíselnou hodnotu a logickou hodnotu. Výstup podřízeného runbooku se přiřadí k proměnné. V tomto případě je podřízená sada Runbook pracovní postup PowerShellu.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Toto je stejný příklad jako podřízená rutina PowerShellového Runbooku.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Spuštění podřízeného Runbooku pomocí rutiny

> [!IMPORTANT]
> Vyvolá-li vaše sada Runbook podřízenou sadu Runbook s rutinou **Start-AzAutomationRunbook** s parametrem *Wait* a podřízená sada Runbook vytvoří výsledek objektu, může při operaci dojít k chybě. Chcete-li obejít tuto chybu, přečtěte si téma [podřízené Runbooky s výstupem objektu](troubleshoot/runbooks.md#child-runbook-object) , kde se dozvíte, jak implementovat logiku pro dotazování na výsledky pomocí rutiny [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

Pomocí rutiny **Start-AzAutomationRunbook** můžete spustit sadu Runbook, jak je popsáno v tématu [spuštění sady Runbook pomocí prostředí Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Existují dva režimy použití této rutiny. V jednom režimu rutina vrátí ID úlohy při vytvoření podřízené úlohy pro podřízenou sadu Runbook. V jiném režimu, který váš skript umožňuje zadáním parametru *Wait* , rutina počká, až se dokončí podřízená úloha, a vrátí výstup z podřízeného Runbooku.

Úloha z podřízeného Runbooku začala s rutinou spuštěnou v samostatné úloze z nadřazené úlohy Runbooku. Výsledkem tohoto chování je více úloh než spuštění sady Runbook inline a díky tomu se úlohy budou obtížnější sledovat. Nadřazená sada Runbook může spustit více než jednu podřízenou sadu Runbook, aniž by bylo nutné čekat na jejich dokončení. Pro toto paralelní spuštění volání podřízených runbooků musí nadřazená sada Runbook používat [klíčové slovo Parallel](automation-powershell-workflow.md#parallel-processing).

Výstup podřízené sady Runbook se nevrátí do nadřazeného Runbooku spolehlivě z důvodu časování. Kromě toho proměnné, jako $VerbosePreference, $WarningPreference a jiné, nemusí být šířeny do podřízených runbooků. Chcete-li se těmto problémům vyhnout, můžete spustit podřízené Runbooky jako samostatné úlohy automatizace pomocí parametru **Start-AzAutomationRunbook** s parametrem *Wait* . Tento postup zablokuje nadřazenou sadu Runbook, dokud nebude dokončena podřízená sada Runbook.

Pokud nechcete, aby se nadřazená sada Runbook zablokovala při čekání, můžete spustit podřízenou sadu Runbook pomocí parametru **Start-AzAutomationRunbook** bez parametru *Wait* . V takovém případě musí sada Runbook čekat na dokončení úlohy pomocí [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) . K načtení výsledků musí také použít příkaz [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) a [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) .

Parametry pro podřízený Runbook spuštěný pomocí rutiny jsou k dispozici jako zatřiďovací tabulka, jak je popsáno v tématu [parametry Runbooku](start-runbooks.md#runbook-parameters). Lze použít pouze jednoduché datové typy. Pokud má runbook parametr komplexního datového typu, pak ho musí být volaný jako přiřazený.

Kontext předplatného může být ztracen při spouštění podřízených runbooků jako samostatných úloh. Aby mohla podřízená sada Runbook spustit příkaz AZ Module rutiny proti konkrétnímu předplatnému Azure, musí se podřízená sada Runbook ověřit pro toto předplatné nezávisle na nadřazeném Runbooku.

Pokud úlohy v rámci stejného účtu Automation pracují s více než jedním předplatným, může výběr předplatného v jedné úloze změnit aktuálně vybraný kontext předplatného pro jiné úlohy. Chcete-li se této situaci vyhnout, použijte `Disable-AzContextAutosave –Scope Process` na začátku každé sady Runbook. Tato akce uloží pouze kontext do tohoto spuštění sady Runbook.

### <a name="example"></a>Příklad

Následující příklad spustí podřízený Runbook s parametry a potom počká, než se dokončí pomocí rutiny **Start-AzAutomationRunbook** s parametrem *Wait* . Po dokončení příklad shromáždí výstup rutiny z podřízeného Runbooku. Aby bylo možné použít **příkaz Start-AzAutomationRunbook**, musí se skript ověřit u vašeho předplatného Azure.

```azurepowershell-interactive
# Ensure that the runbook does not inherit an AzContext
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Get-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzureContext `
    –Parameters $params –Wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Porovnání metod volání podřízeného Runbooku

Následující tabulka shrnuje rozdíly mezi dvěma způsoby volání Runbooku z jiné sady Runbook.

|  | vložené | Rutina |
|:--- |:--- |:--- |
| Úloha |Podřízené runbooky spuštěné ve stejné úloze jako nadřazený. |Pro podřízený runbook se vytvoří samostatná úloha. |
| Spouštěcí |Nadřízený runbook čeká na dokončení podřízeného runbooku před pokračováním. |Nadřazená sada Runbook pokračuje ihned po spuštění podřízeného Runbooku *nebo* se nadřazený Runbook počká, až se podřízená úloha dokončí. |
| Výstup |Nadřazená sada runbook může získat výstup přímo z podřízeného runbooku. |Nadřazená sada Runbook musí načíst výstup z podřízené úlohy Runbooku *nebo* může z nadřazeného Runbooku získat výstup přímo z podřízeného Runbooku. |
| Parametry |Hodnoty pro parametry podřízeného runbooku se zadávají samostatně a můžete použít libovolného datového typu. |Hodnoty pro parametry podřízeného Runbooku musí být sloučeny do jedné zatřiďovací tabulky. Tato zatřiďovací tabulka může zahrnovat pouze jednoduché datové typy Array a Object, které používají serializaci JSON. |
| Účet služby Automation |Nadřazená sada Runbook může použít pouze podřízený Runbook ve stejném účtu Automation. |Nadřazené Runbooky můžou použít podřízený Runbook z libovolného účtu Automation, ze stejného předplatného Azure a dokonce z jiného předplatného, ke kterému máte připojení. |
| Publikování |Podřízené sady runbook musí být zveřejněna před publikováním nadřízeného runbooku. |Podřízená sada Runbook je publikovaná kdykoli před spuštěním nadřazeného Runbooku. |

## <a name="next-steps"></a>Další kroky

* [Spuštění Runbooku v Azure Automation](start-runbooks.md)
* [Výstup a zprávy Runbooku v Azure Automation](automation-runbook-output-and-messages.md)
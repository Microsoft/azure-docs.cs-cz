---
title: Podřízené runbooky v Azure Automation
description: Popisuje různé metody spuštění sady Runbook v Azure Automation z jiné sady Runbook a sdílení informací mezi nimi.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e7341a8c270d16497430a70c2a1b21354a775787
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850444"
---
# <a name="child-runbooks-in-azure-automation"></a>Podřízené runbooky v Azure Automation

Jedná se o doporučený postup v Azure Automation k zápisu opakovaně použitelných modulárních runbooků se samostatnou funkcí, kterou používají jiné sady Runbook. Nadřazený Runbook často volá jednu nebo více podřízených runbooků, aby provedl požadované funkce. Existují dva způsoby, jak zavolat podřízenou sadu Runbook, přičemž každá z nich má odlišné rozdíly, které byste měli pochopit, abyste mohli určit, který z nich nejlépe vyhovuje vašim různým scénářům.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Vyvolání podřízeného Runbooku pomocí vloženého spuštění

Chcete-li vyvolání runbooku přiřazeného z jiného runbooku, použijte název sady runbook a zadejte hodnoty jeho parametrů stejně, jako byste používali aktivitu nebo rutinu.  Všechny Runbooky ve stejném účtu Automation jsou k dispozici všem ostatním, aby je bylo možné používat tímto způsobem. Nadřazený runbook bude čekat na dokončení podřízeného runbooku před přechodem na další řádek a jakýkoliv výstup se vrátí přímo do nadřazeného objektu.

Když vyvoláte přiřazený runbook, spustí se ve stejné úloze jako nadřízený runbook. V historii úloh podřízeného Runbooku, který běžel, není žádný náznak. Všechny výjimky a výstup datového proudu z podřízeného Runbooku jsou přidružené k nadřazenému. Výsledkem tohoto chování je méně úloh a snazší sledování a řešení potíží, protože jakékoli výjimky vyvolané podřízenou sadou Runbook a jakýkoliv z jeho výstupů streamu jsou přidruženy k nadřazené úloze.

Při publikování Runbooku musí být všechny podřízené Runbooky, které volá, už publikované. Důvodem je, že při kompilaci sady Runbook sestaví Azure Automation sestavení přidružení se všemi podřízenými Runbooky. Pokud ne, zdá se, že se nadřazený Runbook správně publikuje, ale při jeho spuštění vygeneruje výjimku. Pokud k tomu dojde, můžete nadřazený Runbook znovu publikovat, aby správně odkazoval na podřízené Runbooky. Nemusíte znovu publikovat nadřazený Runbook, pokud se změní některá z podřízených runbooků, protože přidružení již bylo vytvořeno.

Parametry podřízeného Runbooku s názvem inline můžou být libovolný datový typ, včetně složitých objektů. Při spuštění sady Runbook pomocí Azure Portal nebo pomocí rutiny Start-AzureRmAutomationRunbook není k dispozici žádná [serializace JSON](start-runbooks.md#runbook-parameters) .

### <a name="runbook-types"></a>Typy runbooků

Které typy mohou navzájem volat:

* [Powershellový Runbook](automation-runbook-types.md#powershell-runbooks) a [grafické Runbooky](automation-runbook-types.md#graphical-runbooks) můžou zavolat každé další vložené (jsou založené na PowerShellu).
* [Runbook pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks) a runbooky grafického pracovního postupu PowerShellu můžou zavolat každé další vložené (oba jsou založené na pracovním postupu PowerShellu).
* Typy PowerShellu a typy pracovních postupů PowerShellu nemůžou volat každý druhý vložený a musí používat rutinu Start-AzureRmAutomationRunbook.

Kdy má pořadí publikování:

* Pořadí publikování runbooků pouze pro PowerShellový pracovní postup a runbooky PowerShellu pro grafické pracovní postupy.

Když zavoláte podřízený Runbook pracovního postupu PowerShellu nebo pomocí vloženého spuštění, použijete název sady Runbook.  Při volání podřízeného Runbooku prostředí PowerShell musíte spustit jeho název s *příponou.\\* , abyste určili, že se skript nachází v místním adresáři.

### <a name="example"></a>Příklad:

Následující příklad spustí test podřízeného Runbooku, který přijímá tři parametry, komplexní objekt, celé číslo a logickou hodnotu. Výstup podřízeného runbooku se přiřadí k proměnné.  V tomto případě je podřízená sada Runbook pracovní postup PowerShellu.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Následuje stejný příklad jako podřízená rutina PowerShellového Runbooku.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Spuštění podřízeného Runbooku pomocí rutiny

> [!IMPORTANT]
> Pokud vyvoláte podřízený Runbook s rutinou `Start-AzureRmAutomationRunbook` s přepínačem `-Wait` a výsledky podřízeného Runbooku je objekt, může dojít k chybám. Chcete-li obejít tuto chybu, přečtěte si téma [podřízené Runbooky s výstupem objektu](troubleshoot/runbooks.md#child-runbook-object) , kde se dozvíte, jak implementovat logiku pro dotazování na výsledky a použít [příkaz Get-AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord) .

Pomocí rutiny [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) můžete spustit sadu Runbook, jak je popsáno v tématu [spuštění sady Runbook pomocí prostředí Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Existují dva režimy použití této rutiny.  V jednom režimu rutina vrátí ID úlohy při vytvoření podřízené úlohy pro podřízenou sadu Runbook.  V jiném režimu, který povolíte zadáním parametru **-Wait** , rutina počká, až se dokončí podřízená úloha, a vrátí výstup z podřízeného Runbooku.

Úloha z podřízeného Runbooku začala s rutinou spuštěnou v samostatné úloze z nadřazené sady Runbook. Výsledkem tohoto chování je více úloh než spuštění sady Runbook inline a jejich sledování bude obtížnější. Nadřazená sada Runbook může spustit více než jednu podřízenou sadu Runbook, aniž by bylo nutné čekat na jejich dokončení. Pro stejný druh paralelního provádění volání přiřazených runbooků muselo, by bylo potřeba použít nadřazená sada runbook [paralelní klíčové slovo](automation-powershell-workflow.md#parallel-processing).

Výstup podřízených runbooků se nevrátí do nadřazené sady Runbook spolehlivě z důvodu časování. Také některé proměnné, jako $VerbosePreference, $WarningPreference a jiné, nesmí být šířeny do podřízených runbooků. Chcete-li se těmto problémům vyhnout, můžete podřízené Runbooky spustit jako samostatné úlohy automatizace pomocí rutiny `Start-AzureRmAutomationRunbook` s přepínačem `-Wait`. Tím se zablokuje nadřazená sada Runbook, dokud nebude dokončena podřízená sada Runbook.

Pokud nechcete, aby byl nadřazený Runbook při čekání zablokován, můžete spustit podřízenou sadu Runbook pomocí rutiny `Start-AzureRmAutomationRunbook` bez přepínače `-Wait`. Pak je nutné použít `Get-AzureRmAutomationJob` pro čekání na dokončení úlohy a `Get-AzureRmAutomationJobOutput` a `Get-AzureRmAutomationJobOutputRecord` k načtení výsledků.

Parametry pro podřízený runbook spuštěný pomocí rutiny se poskytují jako zatřiďovací tabulka, jak je popsáno v [parametry Runbooku](start-runbooks.md#runbook-parameters). Lze použít pouze jednoduché datové typy. Pokud má runbook parametr komplexního datového typu, pak ho musí být volaný jako přiřazený.

Kontext předplatného může být ztracen při spouštění podřízených runbooků jako samostatných úloh. Aby mohla podřízená sada runbook spouštět rutiny Azure RM v rámci konkrétního předplatného Azure, musí se podřízená sada Runbook ověřit pro toto předplatné nezávisle na nadřazeném Runbooku.

Pokud úlohy v rámci stejného účtu Automation fungují s více než jedním předplatným, může se při výběru předplatného v jedné úloze změnit aktuálně vybraný kontext předplatného pro jiné úlohy. Chcete-li se tomuto problému vyhnout, použijte `Disable-AzureRmContextAutosave –Scope Processsave` na začátku každé sady Runbook. Tato akce uloží pouze kontext do tohoto spuštění sady Runbook.

### <a name="example"></a>Příklad:

Následující příklad spustí podřízený Runbook s parametry a potom počká, než se dokončí, pomocí parametru Start-AzureRmAutomationRunbook-Wait. Po dokončení se jeho výstup shromáždí z podřízeného Runbooku. Pokud chcete použít `Start-AzureRmAutomationRunbook`, musíte se ověřit ve svém předplatném Azure.

```azurepowershell-interactive
# Ensures you do not inherit an AzureRMContext in your runbook
Disable-AzureRmContextAutosave –Scope Process

# Connect to Azure with RunAs account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzureContext = Select-AzureRmSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzureRmAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzureRMContext $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Porovnání metod volání podřízeného Runbooku

Následující tabulka shrnuje rozdíly mezi dvěma způsoby volání runbooku z jiného runbooku.

|  | vložené | Rutina |
|:--- |:--- |:--- |
| Úloha |Podřízené runbooky spuštěné ve stejné úloze jako nadřazený. |Pro podřízený runbook se vytvoří samostatná úloha. |
| Provedení |Nadřízený runbook čeká na dokončení podřízeného runbooku před pokračováním. |Nadřazená sada Runbook pokračuje ihned po spuštění podřízeného Runbooku *nebo* se nadřazený Runbook počká, až se podřízená úloha dokončí. |
| Výstup |Nadřazená sada runbook může získat výstup přímo z podřízeného runbooku. |Nadřazená sada Runbook musí načíst výstup z podřízené úlohy Runbooku *nebo* může z nadřazeného Runbooku získat výstup přímo z podřízeného Runbooku. |
| Parametry |Hodnoty pro parametry podřízeného runbooku se zadávají samostatně a můžete použít libovolného datového typu. |Hodnoty pro parametry podřízeného Runbooku musí být sloučeny do jedné zatřiďovací tabulky. Tato zatřiďovací tabulka může zahrnovat pouze jednoduché datové typy Array a Object, které používají serializaci JSON. |
| Účet Automation |Nadřazená sada Runbook může použít pouze podřízený Runbook ve stejném účtu Automation. |Nadřazené Runbooky můžou použít podřízený Runbook z libovolného účtu Automation ze stejného předplatného Azure a dokonce i jiného předplatného, ke kterému máte připojení. |
| Publikování |Podřízené sady runbook musí být zveřejněna před publikováním nadřízeného runbooku. |Podřízená sada Runbook se musí publikovat kdykoli před spuštěním nadřazeného Runbooku. |

## <a name="next-steps"></a>Další kroky

* [Spuštění Runbooku v Azure Automation](start-runbooks.md)
* [Výstup a zprávy Runbooku v Azure Automation](automation-runbook-output-and-messages.md)


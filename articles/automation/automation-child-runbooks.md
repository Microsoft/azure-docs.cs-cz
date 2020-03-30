---
title: Podřízené runbooky v Azure Automation
description: Popisuje různé metody pro spuštění sady Runbook v Azure Automation z jiného runbooku a sdílení informací mezi nimi.
services: automation
ms.subservice: process-automation
ms.date: 01/17/2019
ms.topic: conceptual
ms.openlocfilehash: 42362a170f493afd51a5d4ee139620ad25b54e79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367359"
---
# <a name="child-runbooks-in-azure-automation"></a>Podřízené runbooky v Azure Automation

V Azure Automation je doporučeno zapisovat opakovaně použitelné modulární runbooky s diskrétní funkcí, která se nazývá jinými runbooky. Nadřazená kniha Runbook často volá jednu nebo více podřízených runbooků, aby provedla požadované funkce. Existují dva způsoby, jak volat podřízený runbook a existují zřetelné rozdíly, které byste měli pochopit, abyste mohli určit, které je nejlepší pro vaše scénáře.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Vyvolání podřízeného runbooku pomocí inline spuštění

Chcete-li vyvolat vsazení sady Runbook z jiné sady Runbook, použijte název sady Runbook a zadejte hodnoty pro jeho parametry, stejně jako byste použili aktivitu nebo rutinu. Všechny sady Runbook ve stejném účtu Automation jsou k dispozici všem ostatním pro použití tímto způsobem. Nadřazená sada Runbook čeká na dokončení podřízeného runbooku před přesunutím na další řádek a veškerý výstup se vrátí přímo nadřazenému.

Když vyvoláte přiřazený Runbook, spustí se ve stejné úloze jako nadřazený Runbook. V historii úloh podřízeného runbooku není žádný údaj. Všechny výjimky a všechny výstupy datového proudu z podřízeného runbooku jsou přidruženy k nadřazené. Toto chování má za následek méně úloh a usnadňuje jejich sledování a řešení potíží.

Při publikování sady Runbook musí být již publikovány všechny podřízené runbooky, které volá. Důvodem je, že Azure Automation vytvoří přidružení s všechny podřízené runbooky při kompilaci runbook. Pokud podřízené sady Runbook ještě nebyly publikovány, zobrazí se nadřazená runbook správně publikovat, ale při spuštění vygeneruje výjimku. Pokud k tomu dojde, můžete znovu publikovat nadřazený runbook a správně odkazovat na podřízené runbooky. Pokud dojde ke změně podřízeného runbooku, není nutné znovu publikovat nadřazený runbook, protože přidružení již bylo vytvořeno.

Parametry podřízeného runbooku volaného jako inline mohou být libovolného datového typu, včetně složitých objektů. Neexistuje žádná [serializace JSON](start-runbooks.md#runbook-parameters), jako je při spuštění sady Runbook pomocí portálu Azure nebo s [rutinou Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook)

### <a name="runbook-types"></a>Typy runbooků

Které typy runbooků si můžou navzájem volat?

* [Runbook prostředí PowerShell](automation-runbook-types.md#powershell-runbooks) a [grafická runbook](automation-runbook-types.md#graphical-runbooks) můžete volat navzájem vložená, protože obě jsou založeny na prostředí PowerShell.
* [Runbook pracovního postupu prostředí PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) a grafická runbook pracovního postupu prostředí PowerShell se mohou vzájemně volat, protože obě jsou založeny na pracovním postupu prostředí PowerShell.
* Typy Prostředí PowerShell a typy pracovního postupu Prostředí PowerShell u `Start-AzAutomationRunbook`sebe nemohou vzájemně volat a musí používat .

Kdy záleží na pořadí publikování?

Pořadí publikování sad runbook záleží jenom pro pracovní postupy prostředí PowerShell a grafické runbooky pracovního postupu prostředí PowerShell.

Když vaše runbook volá podřízenou runbook grafického nebo PowerShellworkflow pomocí vsazení, použije název runbooku. Název musí `.\\` začínat, aby bylo nutné určit, že skript je umístěn v místním adresáři.

### <a name="example"></a>Příklad

Následující příklad spustí testovací podřízený runbook, který přijímá komplexní objekt, hodnotu celéčíslo a logickou hodnotu. Výstup podřízeného Runbooku je přiřazený k proměnné. V tomto případě je podřízený runbook runbook runbook Pracovní postup prostředí PowerShell.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Tady je stejný příklad, který používá runbook prostředí PowerShell jako podřízený.

```azurepowershell-interactive
$vm = Get-AzVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-a-cmdlet"></a>Spuštění podřízeného runbooku pomocí rutiny

> [!IMPORTANT]
> Pokud váš runbook vyvolá podřízený `Start-AzAutomationRunbook` runbook s `Wait` rutinou s parametrem a podřízený runbook vytvoří výsledek objektu, může dojít k chybě operace. Chcete-li chybu obejít, přečtěte si [informace](troubleshoot/runbooks.md#child-runbook-object) o tom, jak implementovat logiku pro dotazování na výsledky pomocí rutiny [Get-AzAutomationJobOutputRecord.](/powershell/module/az.automation/get-azautomationjoboutputrecord)

Můžete použít `Start-AzAutomationRunbook` ke spuštění runbooku, jak je popsáno v [Chcete-li spustit runbook s Prostředím Windows PowerShell](start-runbooks.md#start-a-runbook-with-powershell). Pro tuto rutinu existují dva režimy použití. V jednom režimu rutina vrátí ID úlohy při vytvoření úlohy pro podřízený runbook. V jiném režimu, který skript umožňuje zadáním *Wait* parametr, rutina čeká, dokud podřízená úloha dokončí a vrátí výstup z podřízeného runbooku.

Úloha z podřízeného runbooku spuštěná rutinou se spouští odděleně od nadřazené úlohy runbooku. Toto chování má za následek více úloh než spuštění vestavěné runbooku a ztěžuje sledování úloh. Nadřazený můžete spustit více než jeden podřízený runbook asynchronně bez čekání na každý k dokončení. Pro toto paralelní spuštění volání podřízené runbook y inline, nadřazené runbook musí použít [paralelní klíčové slovo](automation-powershell-workflow.md#parallel-processing).

Podřízený výstup runbooku se nevrátí do nadřazeného runbooku spolehlivě z důvodu časování. Kromě toho proměnné `$VerbosePreference`jako `$WarningPreference`, , a jiné nemusí být rozšířeny do podřízených runbooků. Chcete-li se těmto problémům vyhnout, můžete spustit `Start-AzAutomationRunbook` podřízené `Wait` runbooky jako samostatné úlohy automatizace pomocí parametru. Tato technika blokuje nadřazenou runbook, dokud není podřízený runbook dokončen.

Pokud nechcete, aby byl nadřazený runbook blokován při čekání, můžete spustit podřízený runbook pomocí `Start-AzAutomationRunbook` bez parametru. `Wait` V takovém případě musí váš runbook použít [Get-AzAutomationJob](/powershell/module/az.automation/get-azautomationjob) a čekat na dokončení úlohy. Musí také použít [Get-AzAutomationJobOutput](/powershell/module/az.automation/get-azautomationjoboutput) a [Get-AzAutomationJobOutputRecord](/powershell/module/az.automation/get-azautomationjoboutputrecord) k načtení výsledků.

Parametry pro podřízený runbook spuštěný rutinou jsou k dispozici jako hodnotitelná hodnota hash, jak je popsáno v [parametrech runbooku](start-runbooks.md#runbook-parameters). Lze použít pouze jednoduché datové typy. Pokud má Runbook parametr komplexního datového typu, musí být volaný jako přiřazený.

Kontext předplatného může dojít ke ztrátě při spuštění podřízené sady Runbook jako samostatné úlohy. Pro podřízené runbook spustit rutiny modulu Az proti konkrétní předplatné Azure, podřízené musí ověřit na toto předplatné nezávisle na nadřazené runbook.

Pokud úlohy v rámci stejného účtu automatizace pracovat s více než jedno předplatné, výběr předplatného v jedné úloze můžete změnit aktuálně vybraný kontext předplatného pro jiné úlohy. Chcete-li se `Disable-AzContextAutosave –Scope Process` této situaci vyhnout, použijte na začátku každého runbooku. Tato akce ukládá pouze kontext pro spuštění tohoto runbooku.

### <a name="example"></a>Příklad

Následující příklad spustí podřízený runbook s parametry a pak `Start-AzAutomationRunbook` čeká na jeho `Wait` dokončení pomocí rutiny s parametrem. Po dokončení příklad shromažďuje výstup rutiny z podřízeného runbooku. Chcete-li použít `Start-AzAutomationRunbook`, skript musí ověřit na vaše předplatné Azure.

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

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Porovnání metod pro volání podřízeného runbooku

Následující tabulka shrnuje rozdíly mezi dvěma způsoby volání runbooku z jiného runbooku.

|  | Přiřazený | Rutina |
|:--- |:--- |:--- |
| Úloha |Podřízené Runbooky spuštěné ve stejné úloze jako nadřízený. |Pro podřízený Runbook se vytvoří samostatná úloha. |
| Spouštěcí |Nadřízený Runbook čeká na dokončení podřízeného. Teprve potom pokračuje. |Nadřazená runbook pokračuje ihned po spuštění podřízeného runbooku *nebo* po dokončení podřízené úlohy. |
| Výstup |Nadřízený Runbook může získat výstup přímo z podřízeného. |Nadřazená runbook musí načíst výstup z podřízené úlohy *runbooku nebo* nadřazené runbook můžete přímo získat výstup z podřízené runbook. |
| Parametry |Hodnoty pro parametry podřízeného Runbooku se zadávají samostatně a můžou používat jakýkoli datový typ. |Hodnoty pro podřízené parametry runbooku musí být sloučeny do jedné hodnoty hashtable. Tato hashtable může zahrnovat pouze jednoduché, matice a objekt datové typy, které používají Serializace JSON. |
| Účet Automation |Nadřazená runbook může používat podřízené runbooky pouze ve stejném účtu Automation. |Nadřazené runbooky můžou používat podřízený runbook z libovolného účtu Automation, ze stejného předplatného Azure a dokonce i z jiného předplatného, ke kterému máte připojení. |
| Publikování |Podřízený Runbook je potřeba publikovat před publikováním nadřízeného. |Podřízený runbook je publikován kdykoli před spuštěním nadřazeného runbooku. |

## <a name="next-steps"></a>Další kroky

* [Spuštění sady Runbook v Azure Automation](start-runbooks.md)
* [Výstup sady Runbook a zprávy v Azure Automation](automation-runbook-output-and-messages.md)
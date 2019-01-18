---
title: Podřízené runbooky ve službě Azure Automation
description: Popisuje různé metody pro spuštění sady runbook ve službě Azure Automation z jiného runbooku a sdílení informací mezi nimi.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 01/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fc9e9cbf3e385a43eee8ce63d2f2fa6b863e95cc
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54391341"
---
# <a name="child-runbooks-in-azure-automation"></a>Podřízené runbooky ve službě Azure Automation

Je doporučeným postupem ve službě Azure Automation zapisovat opakovaně použitelné modulární runbooky se samostatnou funkcí, které je jiné sady runbook. Nadřízený runbook často volá jeden nebo víc runbooků k provedení požadované funkce. Existují dva způsoby, jak volat podřízené runbooky, a každá má významné rozdíly, které byste měli rozumět tak, aby bylo možné určit, která je nejvhodnější pro různé scénáře.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Vyvolání podřízeného runbooku pomocí přiřazeného provedení

Chcete-li vyvolání runbooku přiřazeného z jiného runbooku, použijte název sady runbook a zadejte hodnoty jeho parametrů stejně, jako byste používali aktivitu nebo rutinu.  Všechny sady runbook v rámci stejného účtu Automation jsou k dispozici pro všechny ostatní pro použití tímto způsobem. Nadřazený runbook bude čekat na dokončení podřízeného runbooku před přechodem na další řádek a jakýkoliv výstup se vrátí přímo do nadřazeného objektu.

Když vyvoláte přiřazený runbook, spustí se ve stejné úloze jako nadřízený runbook. Neexistuje žádné označení do historie úlohy podřízené sady runbook, který ji spustil. Jakékoli výjimky a žádné výstupní datový proud z podřízeného runbooku je přidružené k nadřazenému. Toto chování výsledkem míň úloh a jejich jednodušší sledování a řešení potíží od veškeré výjimky vyvolané příkazem podřízené sady runbook a některé z jeho datového proudu výstupy jsou přidruženy k nadřazené úloze.

Při publikování runbooku musí všechny podřízené runbooky, které volá již publikována. Je to proto, že Azure Automation vytvoří přidružení se všemi podřízenými runbooky při kompilaci sady runbook. Pokud nejsou, nadřízený runbook správně publikuje se zobrazí, ale vygeneruje výjimku, když je spuštěno. Pokud k tomu dojde, můžete znovu publikovat nadřízený runbook správně odkazovat podřízené runbooky. Nemusíte znovu publikovat nadřízený runbook, pokud některý z podřízených runbooků se změní, protože přidružení už byl vytvořen.

Parametry podřízeného runbooku s přiřazeným voláním můžou být libovolného datového typu včetně složitých objektů. Neexistuje žádná [serializace JSON](automation-starting-a-runbook.md#runbook-parameters) je při spuštění runbooku pomocí portálu Azure portal nebo pomocí rutiny Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Typy runbooků

Typy, které můžete volat mezi sebou:

* A [Powershellového runbooku](automation-runbook-types.md#powershell-runbooks) a [grafické runbooky](automation-runbook-types.md#graphical-runbooks) můžete volat každou další (obojí jsou na základě prostředí PowerShell).
* A [runbook Powershellového Workflow](automation-runbook-types.md#powershell-workflow-runbooks) a sady runbook grafický Powershellový pracovní postup můžete volat každou další (obojí jsou na základě pracovního postupu Powershellu)
* Typy prostředí PowerShell a typy pracovních postupů Powershellu nelze volat mezi sebou a musí používat Start-AzureRmAutomationRunbook.

Při publikování pořadí věci:

* Publikovat záleží na pořadí, sad runbook pouze pro runbooky pracovního postupu Powershellu a grafický Powershellový pracovní postup.

Při volání pomocí přiřazeného provedení podřízeného runbooku grafický nebo pracovních postupů Powershellu, použijte název sady runbook.  Při volání podřízeného runbooku Powershellu, musíte spustit stejný název jako *.\\*  k určení, že skript je umístěn v místním adresáři.

### <a name="example"></a>Příklad:

Následující příklad spouští podřízený runbook test, který přijímá tři parametry, komplexní objekt, celé číslo a logickou hodnotu. Výstup podřízeného runbooku se přiřadí k proměnné.  V takovém případě podřízeného runbooku se runbook pracovního postupu Powershellu.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true
```

Toto je stejný příklad pomocí Powershellového runbooku jako podřízený.

```azurepowershell-interactive
$vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
$output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true
```

## <a name="starting-a-child-runbook-using-cmdlet"></a>Spouštění podřízeného runbooku pomocí rutiny

> [!IMPORTANT]
> Pokud jsou vyvolání podřízeného runbooku se `Start-AzureRmAutomationRunbook` rutinu s `-Wait` přepínače a výsledky podřízeného runbooku je objekt, pravděpodobně dojde k chybám. Pokud chcete chybu obejít, přečtěte si téma [podřízené sady runbook s výstupem objekt](troubleshoot/runbooks.md#child-runbook-object) se naučíte implementovat logiku pro dotazování na výsledky a použít [Get AzureRmAutomationJobOutputRecord](/powershell/module/azurerm.automation/get-azurermautomationjoboutputrecord)

Můžete použít [Start-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Start-AzureRmAutomationRunbook) ke spuštění sady runbook, jak je popsáno v [spuštění runbooku pomocí prostředí Windows PowerShell](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Existují dva způsoby použití pro tuto rutinu.  V jednom režimu rutina vrátí úlohu s id při vytvoření podřízené úlohy pro podřízený runbook.  V jiných režimu, které povolíte tak, že zadáte **– počkejte** parametr, rutina čeká na podřízené úlohy dokončí a vrátí její výstup z podřízeného runbooku.

Úloha z podřízeného runbooku spuštěná pomocí rutiny běží v samostatné úloze nezávisle na nadřízeném runbooku. Toto chování má za následek víc úloh než při spuštění přiřazený runbook a provede jejich obtížnější sledování. Nadřazené můžete spustit více než jeden podřízený runbook asynchronně bez čekání na dokončení. Pro stejný druh paralelního provádění volání přiřazených runbooků muselo, by bylo potřeba použít nadřazená sada runbook [paralelní klíčové slovo](automation-powershell-workflow.md#parallel-processing).

Výstup podřízené runbooky se nevrátíte do nadřízeného runbooku spolehlivě kvůli časování. Také určité proměnné jako $VerbosePreference $WarningPreference, a ostatními nemusí být předány podřízené runbooky. K těmto potížím vyhnout, můžete začít podřízené runbooky jako pomocí samostatných úloh služby Automation `Start-AzureRmAutomationRunbook` rutinu s `-Wait` přepnout. Nadřazená sada runbook to blokuje až do dokončení podřízeného runbooku.

Pokud nechcete, aby nadřízený runbook zablokuje na čekání, můžete začít podřízeného runbooku pomocí `Start-AzureRmAutomationRunbook` rutiny bez `-Wait` přepnout. Pak je třeba použít `Get-AzureRmAutomationJob` čekání na dokončení úlohy a `Get-AzureRmAutomationJobOutput` a `Get-AzureRmAutomationJobOutputRecord` k načtení výsledků.

Parametry pro podřízený runbook spuštěný pomocí rutiny se poskytují jako zatřiďovací tabulka, jak je popsáno v [parametry Runbooku](automation-starting-a-runbook.md#runbook-parameters). Je možné jenom jednoduché datové typy. Pokud má runbook parametr komplexního datového typu, pak ho musí být volaný jako přiřazený.

Kontext předplatného mohou být ztraceny při spouštění runbooků v podobě samostatné úlohy. Aby podřízené sady runbook pro spouštění rutin Azure RM pro konkrétní předplatné Azure podřízené sady runbook musí ověřovat pro toto předplatné nezávisle na nadřazený runbook.

Pokud úlohy v rámci stejného účtu Automation pracovat s více než jedno předplatné, když se vybere předplatné jedna úloha může změnit kontext aktuálně vybraném předplatném pro jiné úlohy. Chcete-li tomuto problému vyhnout, použijte `Disable-AzureRmContextAutosave –Scope Processsave` na začátku každé sady runbook. Tato akce uloží pouze kontext spuštění této sady runbook.

### <a name="example"></a>Příklad:

Následující příklad spouští podřízený runbook s parametry a potom počká na dokončení pomocí Start-AzureRmAutomationRunbook – počkejte parametru. Po dokončení se shromáždí výstup podřízeného runbooku z podřízeného runbooku. Chcete-li použít `Start-AzureRmAutomationRunbook`, je třeba ověřit ke svému předplatnému Azure.

```azurepowershell-interactive
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
    -DefaultProfile $AzureContext `
    –Parameters $params –wait
```

## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Porovnání metod pro volání podřízeného runbooku

Následující tabulka shrnuje rozdíly mezi dvěma způsoby volání runbooku z jiného runbooku.

|  | vložené | Rutiny |
|:--- |:--- |:--- |
| Úlohy |Podřízené runbooky spuštěné ve stejné úloze jako nadřazený. |Pro podřízený runbook se vytvoří samostatná úloha. |
| Spuštění |Nadřízený runbook čeká na dokončení podřízeného runbooku před pokračováním. |Nadřízený runbook pokračuje hned po spuštění podřízeného runbooku *nebo* nadřízený runbook čeká na dokončení úlohy podřízené. |
| Výstup |Nadřazená sada runbook může získat výstup přímo z podřízeného runbooku. |Nadřízený runbook musí načíst výstup z úlohy podřízeného runbooku *nebo* nadřazená sada runbook může získat výstup přímo z podřízeného runbooku. |
| Parametry |Hodnoty pro parametry podřízeného runbooku se zadávají samostatně a můžete použít libovolného datového typu. |Hodnoty pro parametry podřízeného runbooku musí a nelze jej zkombinovat do jedné zatřiďovací tabulky. Tato zatřiďovací tabulky můžete pouze zahrnout jednoduché, pole a objektu datové typy, které používají serializace JSON. |
| Účet Automation |Nadřízený runbook lze použít pouze podřízené sady runbook v rámci stejného účtu automation. |Nadřízené runbooky pomocí podřízeného runbooku z jakékoli účtu automation s předplatným Azure a dokonce i jiné předplatné, které máte připojení k. |
| Publikování |Podřízené sady runbook musí být zveřejněna před publikováním nadřízeného runbooku. |Podřízený musí být publikovaný kdykoli před nadřazená sada runbook spuštěna. |

## <a name="next-steps"></a>Další postup

* [Spuštění runbooku ve službě Azure Automation](automation-starting-a-runbook.md)
* [Sada Runbook výstup a zprávy ve službě Azure Automation](automation-runbook-output-and-messages.md)

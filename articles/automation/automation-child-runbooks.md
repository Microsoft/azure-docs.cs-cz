---
title: Podřízené runbooky ve službě Azure Automation
description: Popisuje různé metody pro spuštění sady runbook ve službě Azure Automation z jiného runbooku a sdílení informací mezi nimi.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 587a6badd57aad839b4b03ca9da1b62d97b38f82
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="child-runbooks-in-azure-automation"></a>Podřízené runbooky ve službě Azure Automation
Je osvědčeným postupem ve službě Azure Automation zapisovat opakovaně použitelné modulární runbooky se samostatnou funkcí, které můžete používat ostatní runbooky. Nadřazená sada runbook bude často volat podřízené runbooky provést požadovanou funkci. Existují dva způsoby, jak volat podřízené runbooky, a každá z nich má významné rozdíly, kterým byste měli porozumět, aby mohla určit, která bude vhodné pro různé scénáře.

## <a name="invoking-a-child-runbook-using-inline-execution"></a>Vyvolání podřízeného runbooku pomocí vložené spouštění
Chcete-li vyvolání runbooku přiřazeného z jiného runbooku, musíte použít název sady runbook a zadejte hodnoty jeho parametrů stejně, jako byste používali aktivitu nebo rutinu.  Všechny runbooky ve stejném účtu Automation jsou k dispozici pro všechny ostatní pro použití tímto způsobem. Nadřazená sada runbook bude čekat podřízené sady runbook až po dokončení Přesun na další řádek a jakýkoliv výstup se vrátí přímo do nadřazeného.

Když vyvoláte přiřazený runbook běží ve stejné úloze jako nadřazený runbook. Budou existovat žádná informace do historie úlohy podřízeného runbooku, který byl spuštěn. Jakékoli výjimky a všechny výstupní datový proud z podřízeného runbooku budou přidružené k nadřazenému. To má za následek méně úloh a jejich jednodušší sledování a řešení problémů, protože jakékoli výjimky vyvolané podřízeným runbookem a jakýkoli jejich výstup datového proudu jsou přidružené nadřazené úloze.

Při publikování runbooku musí být všechny podřízené runbooky, které volá již publikován. Je to proto, že Azure Automation vytvoří přidružení se všemi podřízenými runbooky při runbooku. Pokud nejsou, nadřazená sada runbook se objeví správně publikuje, ale vygeneruje výjimku, když je spuštěno. V takovém případě můžete nadřazený runbook znovu publikovat aby správně odkazoval na podřízené runbooky. Nemusíte znovu publikovat nadřízený runbook, pokud některý z podřízených runbooků se změnit, protože přidružení se již byly vytvořeny.

Parametry podřízeného runbooku s přiřazeným voláním můžou být jakéhokoli datového typu včetně složitých objektů a neexistuje žádný [serializace JSON](automation-starting-a-runbook.md#runbook-parameters) není při spuštění runbooku pomocí portálu Azure nebo pomocí Rutina Start-AzureRmAutomationRunbook.

### <a name="runbook-types"></a>Typy runbooků
Typy, které můžete volat vzájemně:

* A [Powershellový runbook](automation-runbook-types.md#powershell-runbooks) a [grafické runbooky](automation-runbook-types.md#graphical-runbooks) můžete volat každý další vložené (obě jsou na základě prostředí PowerShell).
* A [runbook pracovního postupu Powershellu](automation-runbook-types.md#powershell-workflow-runbooks) a runbooky pracovních postupů grafické prostředí PowerShell můžete volat každý další vložené (obě jsou na základě pracovního postupu Powershellu)
* Prostředí PowerShell typy a typy pracovního postupu Powershellu nelze volat navzájem vložené a musí používat Start AzureRmAutomationRunbook.

Při publikování pořadí věci:

* Pro sady runbook PowerShell Workflow a grafické prostředí PowerShell Workflow pouze záleží na pořadí publikování sad runbook.

Při volání pomocí vložené spouštění podřízeného runbooku grafický nebo pracovního postupu Powershellu se právě používá název sady runbook.  Při volání podřízeného runbooku prostředí PowerShell, musí před jeho název s *.\\* k určení, že se skript nachází v místním adresáři. 

### <a name="example"></a>Příklad:
Následující příklad popisuje vyvolání podřízeného testovacího runbooku, který přijímá tři parametry, komplexní objekt, celé číslo a logickou hodnotu. Výstup podřízeného runbooku je přiřazený k proměnné.  V takovém případě podřízené sady runbook je runbook pracovního postupu Powershellu

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = PSWF-ChildRunbook –VM $vm –RepeatCount 2 –Restart $true

Následuje příkladě pomocí prostředí PowerShell runbook jako podřízený objekt.

    $vm = Get-AzureRmVM –ResourceGroupName "LabRG" –Name "MyVM"
    $output = .\PS-ChildRunbook.ps1 –VM $vm –RepeatCount 2 –Restart $true


## <a name="starting-a-child-runbook-using-cmdlet"></a>Spouštění podřízeného runbooku pomocí rutiny
Můžete použít [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) rutiny spuštění sady runbook, jak je popsáno v [spuštění runbooku pomocí prostředí Windows PowerShell](automation-starting-a-runbook.md#starting-a-runbook-with-windows-powershell). Existují dva režimy použití pro tuto rutinu.  V jednom režimu rutina vrátí id úlohy při vytvoření podřízené úlohy pro podřízený runbook.  V jiných režimu, který povolíte zadáním **-počkejte** parametr rutiny bude odložena podřízené úlohy dokončení a vrátí výstup z podřízené sady runbook.

Úloha z podřízeného runbooku spuštěná pomocí rutiny poběží v samostatné úloze nezávisle na nadřízeném runbooku. To má za následek víc úloh než při vyvolání přiřazený runbook a dává je obtížné sledovat. Nadřazený může asynchronně spustit víc podřízených runbooků bez čekání na dokončení každého. Pro stejný druh paralelního provedení by volání přiřazených runbooků, bude třeba použít nadřazená sada runbook [paralelní klíčové slovo](automation-powershell-workflow.md#parallel-processing).

Parametry pro podřízený runbook spuštěný pomocí rutiny jsou uvedeny jako zatřiďovací tabulky, jak je popsáno v [parametry Runbooku](automation-starting-a-runbook.md#runbook-parameters). Můžete použít jenom jednoduché datové typy. Pokud má runbook parametr komplexního datového typu, pak se musí být volaný jako přiřazený.

### <a name="example"></a>Příklad:
Následující příklad spouští podřízený runbook s parametry a potom počká, než na její dokončení pomocí Start-AzureRmAutomationRunbook-počkejte parametr. Po dokončení její výstup se shromažďují z podřízené sady runbook.

    $params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true} 
    $joboutput = Start-AzureRmAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-ChildRunbook" -ResourceGroupName "LabRG" –Parameters $params –wait


## <a name="comparison-of-methods-for-calling-a-child-runbook"></a>Porovnání metod pro volání podřízeného runbooku
Následující tabulka shrnuje rozdíly mezi tyto dvě metody pro volání sady runbook z jiné sady runbook.

|  | Vložené | Rutina |
|:--- |:--- |:--- |
| Úloha |Podřízené runbooky spuštěné ve stejné úloze jako nadřazený. |Pro podřízený runbook se vytvoří samostatná úloha. |
| Provedení |Nadřízený runbook čeká na dokončení podřízeného runbooku než budete pokračovat. |Nadřízený runbook pokračuje hned po spuštění podřízeného runbooku *nebo* nadřízený runbook čeká na dokončení úlohy podřízené. |
| Výstup |Nadřízený runbook může získat výstup přímo z podřízeného runbooku. |Nadřízený runbook musí načíst výstup z úlohy podřízeného runbooku *nebo* nadřízený runbook může získat výstup přímo z podřízeného runbooku. |
| Parametry |Hodnoty pro parametry podřízeného runbooku se zadávají samostatně a můžete použít jakéhokoli datového typu. |Hodnoty pro parametry podřízeného runbooku musí zkombinovat do jedné zatřiďovací tabulky a můžou zahrnovat jenom jednoduché, pole a objektu datové typy, které využívají serializaci JSON. |
| Účet Automation |Nadřízený runbook lze použít pouze podřízené sady runbook ve stejném účtu automation. |Pokud máte připojení k němu, můžete použít nadřazená sada runbook podřízeného runbooku z jakékoli účet automation ze stejného předplatného Azure a i jiné předplatné. |
| Publikování |Podřízené sady runbook musí publikovat před publikováním nadřazené sady runbook. |Podřízené sady runbook musí být publikovaný kdykoli před nadřízeného runbooku. |

## <a name="next-steps"></a>Další postup
* [Spuštění sady runbook ve službě Azure Automation](automation-starting-a-runbook.md)
* [Výstup a zprávy ve službě Azure Automation Runbooku](automation-runbook-output-and-messages.md)


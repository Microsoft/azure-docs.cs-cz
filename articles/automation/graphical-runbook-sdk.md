---
title: Přehled runbooku Azure Automation Grpahical SDK
description: Tento článek popisuje, jak použít SDK grafického Runbooku Azure Automation
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 07/20/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 702af8311887afc94e7127704d3377e944503324
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239853"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Použití Azure Automation grafický runbook SDK

[Grafické runbooky](automation-graphical-authoring-intro.md) jsou runbooky, které pomáhají spravovat složitosti základního kódu Windows Powershellu nebo pracovním postupu Powershellu. Microsoft Azure Automation grafické vytváření SDK umožňuje vývojářům vytvářet a upravovat grafické Runbooky pro použití se službou Azure Automation. Následující fragmenty kódu ukazují základní postup vytvoření grafického runbooku z vašeho kódu.

## <a name="pre-requisites"></a>Požadavky

Pokud chcete začít, importovat `Microsoft.Azure.Automation.GraphicalRunbook.Model` balíčku do vašeho projektu.

## <a name="create-a-runbook-object-instance"></a>Vytvoření instance objektu sady runbook

Odkaz `Orchestrator.GraphRunbook.Model` sestavení a vytvoření instance `Orchestrator.GraphRunbook.Model.GraphRunbook` třídy:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Přidat parametry runbooku

Vytvoření instance `Orchestrator.GraphRunbook.Model.Parameter` objektů a jejich přidání do sady runbook:

```csharp
runbook.AddParameter(
 new Parameter("YourName") {
  TypeName = typeof(string).FullName,
   DefaultValue = "World",
   Optional = true
 });

runbook.AddParameter(
 new Parameter("AnotherParameter") {
  TypeName = typeof(int).FullName, ...
 });
```

## <a name="add-activities-and-links"></a>Přidání aktivity a odkazy

Vytvoření instance aktivity odpovídající typy a jejich přidání do sady runbook:

```csharp
var writeOutputActivityType = new CommandActivityType {
 CommandName = "Write-Output",
  ModuleName = "Microsoft.PowerShell.Utility",
 InputParameterSets = new [] {
  new ParameterSet {
   Name = "Default",
    new [] {
     new Parameter("InputObject"), ...
    }
  },
  ...
 }
};

var outputName = runbook.AddActivity(
 new CommandActivity("Output name", writeOutputActivityType) {
  ParameterSetName = "Default",
   Parameters = new ActivityParameters {
    {
     "InputObject",
     new RunbookParameterValueDescriptor("YourName")
    }
   },
   PositionX = 0,
   PositionY = 0
 });

var initializeRunbookVariable = runbook.AddActivity(
 new WorkflowScriptActivity("Initialize variable") {
  Process = "$a = 0",
   PositionX = 0,
   PositionY = 100
 });
```

Aktivity jsou implementované následující třídy v `Orchestrator.GraphRunbook.Model` obor názvů:

|Třída  |Aktivita  |
|---------|---------|
|CommandActivity     | Vyvolá příkaz prostředí PowerShell (rutiny, funkce atd.).        |
|InvokeRunbookActivity     | Vyvolá jiný přiřazený runbook.        |
|JunctionActivity     | Čeká na dokončení všech příchozí větví.        |
|WorkflowScriptActivity     | Provede blok kódu Powershellu nebo pracovním postupu Powershellu (v závislosti na typu runbook) v rámci dané sady runbook. Jedná se o výkonný nástroj, ale je nadměrně: uživatelské rozhraní se zobrazí tento blok skriptu jako text. prováděcí modul bude považovat za zadaný blok černé políčko a způsobí, že žádné pokusy o analyzovat obsah, s výjimkou kontrola základní syntaxe. Pokud chcete vyvolat jediný příkaz Powershellu, raději CommandActivity.        |

> [!NOTE]
> Vaše vlastní aktivity není odvozena od třídy zadané: Azure Automation, nebude možné používat vlastní aktivity sady runbook.

Parametry CommandActivity a InvokeRunbookActivity musí zadat jako hodnotu popisovače, ne přímým přístupem hodnoty. Hodnota popisovače zadejte vytváření skutečný parametr hodnoty. Následující hodnota popisovače jsou aktuálně k dispozici:


|Popisovač  |Definice  |
|---------|---------|
|ConstantValueDescriptor     | Odkazuje na pevně zakódované konstantní hodnotu.        |
|RunbookParameterValueDescriptor     | Odkazuje na parametr sady runbook podle názvu.        |
|ActivityOutputValueDescriptor     | Odkazuje na upstreamovou aktivitou výstup, povolení jednu aktivitu "" přihlaste se k odběru data vytvořená aktivitou jiný.        |
|AutomationVariableValueDescriptor     | Odkazuje na Automation variabilní prostředek podle názvu.         |
|AutomationCredentialValueDescriptor     | Odkazuje na prostředek certifikátu Automation podle názvu.        |
|AutomationConnectionValueDescriptor     | Odkazuje na prostředek připojení Automation podle názvu.        |
|PowerShellExpressionValueDescriptor     | Určuje Powershellový výraz volného tvaru, který se vyhodnotí pouze před vyvoláním aktivity.  <br/>Jedná se o výkonný nástroj, ale je nadměrně: uživatelské rozhraní se zobrazí tento výraz jako text. prováděcí modul bude považovat za zadaný blok černé políčko a způsobí, že žádné pokusy o analyzovat obsah, s výjimkou kontrola základní syntaxe. Pokud je to možné, upřednostňují konkrétnější hodnotu popisovače.      |

> [!NOTE]
> Vlastní hodnota popisovače není odvozena od třídy zadané: použití sad runbook s vlastní hodnotou popisovače typů nebude možné Azure Automation.

Vytvoření instance odkazy připojení aktivit a jejich přidání do sady runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Sadu runbook uložte do souboru

Použití `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` k serializaci sady runbook a řetězec:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Tento řetězec můžete uložit do souboru s **.graphrunbook** rozšíření a tento soubor může být importován do služby Azure Automation.
Serializovaný formát může změnit v budoucích verzích `Orchestrator.GraphRunbook.Model.dll`. Slibujeme zpětnou kompatibilitu: libovolné sady runbook serializovat s příznakem starší verzi `Orchestrator.GraphRunbook.Model.dll` lze deserializovat jakékoli novější verzi. Dopředná kompatibilita není zaručena: nemusí být deserializaci ve starších verzích sady runbook serializované pomocí novější verze.

## <a name="next-steps"></a>Další postup

Další informace o grafické Runbooky ve službě Azure Automation najdete v tématu [vytváření grafického obsahu Úvod](automation-graphical-authoring-intro.md)

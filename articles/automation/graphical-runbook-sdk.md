---
title: Přehled Azure Automation Grpahical sady Runbook SDK
description: Tento článek popisuje, jak použít sadu Azure Automation grafické sady Runbook SDK
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: d4dcf6681ade977847c204dd1237f7cd7a67775e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75418247"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Použití Azure Automation grafické sady Runbook SDK

[Grafické Runbooky](automation-graphical-authoring-intro.md) jsou Runbooky, které vám pomůžou se správou složitých kódů pracovního postupu Windows PowerShellu nebo PowerShellu. Sada SDK pro grafické vytváření Microsoft Azure Automation umožňuje vývojářům vytvářet a upravovat grafické Runbooky pro použití se službou Azure Automation. Následující fragmenty kódu ukazují základní tok vytváření grafického Runbooku z vašeho kódu.

## <a name="pre-requisites"></a>Požadavky

Začněte tím, že do svého projektu naimportujete balíček `Microsoft.Azure.Automation.GraphicalRunbook.Model`.

## <a name="create-a-runbook-object-instance"></a>Vytvoření instance objektu sady Runbook

Odkaz na `Orchestrator.GraphRunbook.Model` sestavení a vytvoření instance `Orchestrator.GraphRunbook.Model.GraphRunbook` třídy:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Přidat parametry Runbooku

Vytvoření instance `Orchestrator.GraphRunbook.Model.Parameter` objektů a jejich přidání do sady Runbook:

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

## <a name="add-activities-and-links"></a>Přidat aktivity a odkazy

Vytvořte instanci aktivit příslušných typů a přidejte je do Runbooku:

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

Aktivity jsou implementovány následujícími třídami v oboru názvů `Orchestrator.GraphRunbook.Model`:

|Třída  |Aktivita  |
|---------|---------|
|CommandActivity     | Vyvolá příkaz prostředí PowerShell (rutina, funkce atd.).        |
|InvokeRunbookActivity     | Vyvolá jinou vloženou sadu Runbook.        |
|JunctionActivity     | Počká, až se všechny příchozí větve dokončí.        |
|WorkflowScriptActivity     | Spustí blok kódu pracovního postupu PowerShellu nebo PowerShellu (v závislosti na typu Runbooku) v kontextu Runbooku. Toto je výkonný nástroj, ale nezahrnuje ho: uživatelské rozhraní zobrazí tento blok skriptu jako text. spouštěcí modul zpracuje poskytnutý blok jako černý rámeček a neprovede žádné pokusy o analýzu obsahu, s výjimkou základní kontroly syntaxe. Pokud potřebujete jenom vyvolat jeden příkaz prostředí PowerShell, preferovat CommandActivity.        |

> [!NOTE]
> Neodvozujte vlastní aktivity ze zadaných tříd: Azure Automation nebude moci používat sady Runbook s vlastními typy aktivit.

Parametry CommandActivity a InvokeRunbookActivity musí být zadány jako popisovače hodnot, nikoli přímé hodnoty. Popisovače hodnot určují, jak by měly být vytvořeny skutečné hodnoty parametrů. V současné době jsou k dispozici následující popisovače hodnot:


|Popisovač  |Definice  |
|---------|---------|
|ConstantValueDescriptor     | Odkazuje na pevně zakódovaný konstantní hodnotu.        |
|RunbookParameterValueDescriptor     | Odkazuje na parametr Runbooku podle názvu.        |
|ActivityOutputValueDescriptor     | Odkazuje na výstup nadřazeného aktivity, který umožňuje, aby se jedna aktivita přihlásila k datům vytvořeným jinou aktivitou.        |
|AutomationVariableValueDescriptor     | Odkazuje na prostředek variabilní automatizace podle názvu.         |
|AutomationCredentialValueDescriptor     | Odkazuje na prostředek certifikátu Automation podle názvu.        |
|AutomationConnectionValueDescriptor     | Odkazuje na prostředek připojení Automation podle názvu.        |
|PowerShellExpressionValueDescriptor     | Určuje bezplatný výraz PowerShellu, který se vyhodnotí těsně před vyvoláním aktivity.  <br/>Toto je výkonný nástroj, ale nezahrnuje ho: uživatelské rozhraní zobrazí tento výraz jako text. spouštěcí modul zpracuje poskytnutý blok jako černý rámeček a neprovede žádné pokusy o analýzu obsahu, s výjimkou základní kontroly syntaxe. Pokud je to možné, preferovat konkrétnější popisovače hodnot.      |

> [!NOTE]
> Neodvozujte vlastní popisovače hodnot od poskytovaných tříd: Azure Automation nebude moci používat sady Runbook s vlastními typy popisovačů hodnot.

Vytvoření instance odkazů spojujících aktivity a jejich přidání do sady Runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Uložit Runbook do souboru

Použití `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` k serializaci sady Runbook do řetězce:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Tento řetězec může být uložen do souboru s příponou **. graphrunbook** a tento soubor lze importovat do Azure Automation.
Serializovaný formát se může v budoucích verzích `Orchestrator.GraphRunbook.Model.dll`změnit. Poskytujeme zpětnou kompatibilitu: Jakákoli sada Runbook serializovaná se starší verzí `Orchestrator.GraphRunbook.Model.dll` může deserializovat jakákoli novější verze. Dopředná kompatibilita není zaručena: sadu Runbook serializovanou s novější verzí nelze deserializovat staršími verzemi.

## <a name="next-steps"></a>Další kroky

Další informace o grafických sadách Runbook v Azure Automation najdete v tématu [Úvod do grafického vytváření](automation-graphical-authoring-intro.md) .


---
title: Použití Azure Automation grafické sady Runbook SDK (Preview)
description: V tomto článku se dozvíte, jak používat sadu Azure Automation grafické sady Runbook SDK (Preview).
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 969e60cd08a65adb1dd731aa7c6c3f9872e288fd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "83835032"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk-preview"></a>Použití Azure Automation grafické sady Runbook SDK (Preview)

[Grafické Runbooky](automation-graphical-authoring-intro.md) vám pomůžou spravovat složitosti základního kódu pracovního postupu prostředí Windows PowerShell nebo PowerShell. Sada SDK pro grafické vytváření Microsoft Azure Automation umožňuje vývojářům vytvářet a upravovat grafické Runbooky pro použití s Azure Automation. Tento článek popisuje základní kroky při vytváření grafického Runbooku z vašeho kódu.

## <a name="prerequisites"></a>Předpoklady

Importujte `Orchestrator.GraphRunbook.Model.dll` balíček stažením [sady SDK](https://www.microsoft.com/download/details.aspx?id=50734).

## <a name="create-a-runbook-object-instance"></a>Vytvoření instance objektu sady Runbook

Odkázat na `Orchestrator.GraphRunbook.Model` sestavení a vytvořit instanci `Orchestrator.GraphRunbook.Model.GraphRunbook` třídy:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Přidat parametry Runbooku

Vytvořte instanci `Orchestrator.GraphRunbook.Model.Parameter` objektů a přidejte je do sady Runbook:

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

Aktivity jsou implementovány pomocí následujících tříd v `Orchestrator.GraphRunbook.Model` oboru názvů.

|Třída  |Aktivita  |
|---------|---------|
|CommandActivity     | Vyvolá příkaz prostředí PowerShell (rutina, funkce atd.).        |
|InvokeRunbookActivity     | Vyvolá jinou vloženou sadu Runbook.        |
|JunctionActivity     | Počká, až se všechny příchozí větve dokončí.        |
|WorkflowScriptActivity     | Spustí blok kódu pracovního postupu PowerShellu nebo PowerShellu (v závislosti na typu Runbooku) v kontextu Runbooku. Toto je výkonný nástroj, ale nezahrnuje ho: uživatelské rozhraní zobrazí tento blok skriptu jako text. spouštěcí modul zpracuje poskytnutý blok jako černý rámeček a neprovede žádné pokusy o analýzu obsahu, s výjimkou základní kontroly syntaxe. Pokud potřebujete jenom vyvolat jeden příkaz prostředí PowerShell, preferovat CommandActivity.        |

> [!NOTE]
> Neodvozujte vlastní aktivity ze zadaných tříd. Azure Automation nemůžou používat Runbooky s vlastními typy aktivit.

Je nutné zadat `CommandActivity` a `InvokeRunbookActivity` parametry jako popisovače hodnot, nikoli přímé hodnoty. Popisovače hodnot určují, jak se mají vydávat skutečné hodnoty parametrů. V současné době jsou k dispozici následující popisovače hodnot:


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
> Neodvozujte své vlastní popisovače hodnot od poskytovaných tříd. Azure Automation nemůže používat sady Runbook s vlastními typy popisovačů hodnot.

Vytvoření instance odkazů spojujících aktivity a jejich přidání do sady Runbook:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Uložit Runbook do souboru

Použijte `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` k serializaci sady Runbook do řetězce:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Tento řetězec můžete uložit do souboru s příponou **. graphrunbook** . Odpovídající sadu Runbook lze importovat do Azure Automation.
Serializovaný formát se může v budoucích verzích nástroje změnit `Orchestrator.GraphRunbook.Model.dll` . Proslibuje zpětnou kompatibilitu: Jakákoli sada Runbook serializovaná se starší verzí sady `Orchestrator.GraphRunbook.Model.dll` může být deserializována jakoukoli novější verzí. Dopředná kompatibilita není zaručena: sadu Runbook serializovanou s novější verzí nelze deserializovat staršími verzemi.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu [vytváření grafických runbooků v Azure Automation](automation-graphical-authoring-intro.md).
---
title: Použití sady Azure Automation grafické sady Runbook SDK
description: Tento článek popisuje, jak používat grafickou sadku Runbook SDK Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 07/20/2018
ms.topic: conceptual
ms.openlocfilehash: 21f6ff8078d5a1db88b2fde33c9063a56b3ee43a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682903"
---
# <a name="use-the-azure-automation-graphical-runbook-sdk"></a>Použití sady Azure Automation grafické sady Runbook SDK

[Grafické sady Runbook](automation-graphical-authoring-intro.md) pomáhají spravovat složitosti základního kódu pracovního postupu prostředí Windows PowerShell nebo PowerShell. Sada SDK pro vytváření grafických sad Microsoft Azure Automation umožňuje vývojářům vytvářet a upravovat grafické sady Runbook pro použití s Azure Automation. Tento článek popisuje základní kroky při vytváření grafického runbooku z vašeho kódu.

## <a name="prerequisites"></a>Požadavky

Importujte `Microsoft.Azure.Automation.GraphicalRunbook.Model` balíček do projektu.

## <a name="create-a-runbook-object-instance"></a>Vytvoření instance objektu runbooku

Odkaz `Orchestrator.GraphRunbook.Model` na sestavení a vytvořit `Orchestrator.GraphRunbook.Model.GraphRunbook` instanci třídy:

```csharp
using Orchestrator.GraphRunbook.Model;
using Orchestrator.GraphRunbook.Model.ExecutableView;

var runbook = new GraphRunbook();
```

## <a name="add-runbook-parameters"></a>Přidání parametrů runbooku

Vytvořte susisku `Orchestrator.GraphRunbook.Model.Parameter` a přidejte je do runbooku:

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

## <a name="add-activities-and-links"></a>Přidání aktivit a odkazů

Vytvořte instanci aktivit vhodných typů a přidejte je do runbooku:

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

Aktivity jsou implementovány následujícími třídami v oboru `Orchestrator.GraphRunbook.Model` názvů.

|Třída  |Aktivita  |
|---------|---------|
|PříkazAktivita     | Vyvolá příkaz Prostředí PowerShell (rutina, funkce atd.).        |
|InvokeRunbookActivity     | Vyvolá další vestavěnou položku runbooku.        |
|JunctionActivity     | Čeká na dokončení všech příchozích větví.        |
|WorkflowScriptActivity     | Spustí blok kódu pracovního postupu prostředí PowerShell nebo PowerShell (v závislosti na typu runbooku) v kontextu runbooku. Jedná se o výkonný nástroj, ale nepoužívejte jej nadměrně: uI zobrazí tento blok skriptu jako text; Spuštění motoru bude považovat zadaný blok jako černé pole a nebude provádět žádné pokusy o analýzu jeho obsahu, s výjimkou základní kontroly syntaxe. Pokud potřebujete vyvolat pouze jeden příkaz PowerShellu, upřednostňujte CommandActivity.        |

> [!NOTE]
> Neodvodvozujte vlastní aktivity z poskytnutých tříd. Azure Automation nelze použít runbooky s vlastní typy aktivit.

Je nutné `CommandActivity` `InvokeRunbookActivity` zadat a parametry jako popisovače hodnot, nikoli přímé hodnoty. Popisovače hodnot určují způsob vytvoření skutečných hodnot parametrů. V současné době jsou k dispozici následující popisovače hodnot:


|Popisovač  |Definice  |
|---------|---------|
|ConstantValueDescriptor     | Odkazuje na pevně zakódovanou konstantní hodnotu.        |
|Popisovač Hodnota_runbookParameterValue     | Odkazuje na parametr runbookpodle názvu.        |
|ActivityOutputOutputValueDescriptor     | Odkazuje na výstup aktivity upstream, který umožňuje jedné aktivitě "přihlásit" k odběru dat vytvořených jinou aktivitou.        |
|AutomationVariableValueDescriptor     | Odkazuje na prostředek proměnné automatizace podle názvu.         |
|AutomationCredentialValueDescriptor     | Odkazuje na prostředek certifikátu automatizace podle názvu.        |
|AutomationConnectionValueDescriptor     | Odkazuje na prostředek automation připojení podle názvu.        |
|Popisovač hodnoty PowerShellExpressionValue     | Určuje volný výraz prostředí PowerShell, který bude vyhodnocen těsně před vyvoláním aktivity.  <br/>Jedná se o výkonný nástroj, ale nepoužívejte jej nadměrně: uI zobrazí tento výraz jako text; Spuštění motoru bude považovat zadaný blok jako černé pole a nebude provádět žádné pokusy o analýzu jeho obsahu, s výjimkou základní kontroly syntaxe. Pokud je to možné, upřednostňujte konkrétnější popisovače hodnot.      |

> [!NOTE]
> Neodvodvozujte vlastní popisovače hodnot z poskytnutých tříd. Azure Automation nelze použít runbooky s typy vlastních typů popisovačů hodnot.

Vytvořte instance propojení spojujícíaktivity a přidejte je do runbooku:

```csharp
runbook.AddLink(new Link(activityA, activityB, LinkType.Sequence));

runbook.AddLink(
 new Link(activityB, activityC, LinkType.Pipeline) {
  Condition = string.Format("$ActivityOutput['{0}'] -gt 0", activityB.Name)
 });
```

## <a name="save-the-runbook-to-a-file"></a>Uložení souboru Runbook do souboru

Slouží `Orchestrator.GraphRunbook.Model.Serialization.RunbookSerializer` k serializaci runbooku na řetězec:

```csharp
var serialized = RunbookSerializer.Serialize(runbook);
```

Tento řetězec můžete uložit do souboru s příponou **.graphrunbook.** Odpovídající runbook lze importovat do Azure Automation.
Serializovaný formát se může v budoucích `Orchestrator.GraphRunbook.Model.dll`verzích aplikace změnit. Slibujeme zpětnou kompatibilitu: jakýkoli runbook `Orchestrator.GraphRunbook.Model.dll` serializovaný se starší verzí lze rekonstruovat libovolnou novější verzí. Kompatibilita vpřed není zaručena: sada Runbook serializovaná s novější verzí nemusí být rekonstruovatelná staršími verzemi.

## <a name="next-steps"></a>Další kroky

Další informace o grafických runbookech v Azure Automation najdete v [tématu Úvod pro vytváření grafických chodů](automation-graphical-authoring-intro.md).
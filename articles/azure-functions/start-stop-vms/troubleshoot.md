---
title: Řešení potíží s spuštěním nebo zastavením virtuálních počítačů (Preview)
description: V tomto článku se dozvíte, jak řešit problémy zjištěné pomocí funkce spustit nebo zastavit virtuální počítače (Preview) pro vaše virtuální počítače Azure.
services: azure-functions
ms.subservice: ''
ms.date: 03/31/2021
ms.topic: conceptual
ms.openlocfilehash: 3c379c1eb36fc19368630188f1b584e1d8a7b8ad
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111565"
---
# <a name="troubleshoot-common-issues-with-startstop-vms-preview"></a>Řešení běžných potíží s virtuálními počítači spustit/zastavit (Preview)

Tento článek poskytuje informace o řešení potíží a řešení problémů, ke kterým může dojít při pokusu o instalaci a konfiguraci virtuálních počítačů spustit/zastavit (Preview). Obecné informace najdete v tématu [Přehled spuštění/zastavení virtuálních počítačů](overview.md).

## <a name="general-validation-and-troubleshooting"></a>Obecné ověřování a řešení potíží

V této části se dozvíte, jak řešit obecné problémy s plánováním scénářů a pomoct identifikovat hlavní příčinu.

### <a name="azure-dashboard"></a>Řídicí panel Azure

Můžete začít kontrolou sdíleného řídicího panelu Azure. Sdílený řídicí panel Azure nasazený jako součást spouštění/zastavování virtuálních počítačů v2 (Preview) představuje rychlý a snadný způsob, jak ověřit stav každé operace provedené na vašich virtuálních počítačích. Podívejte se na dlaždici nedávno prováděné **Akce na virtuálních počítačích** , kde uvidíte všechny poslední operace provedené na vašich virtuálních počítačích. V případě, že se data budou zobrazovat v sestavě, při vyžádání dat z Application Insights prostředku se zobrazí některá latence přibližně pět minut.

### <a name="logic-apps"></a>Logic Apps

V závislosti na tom, která Logic Apps jste povolili podporu svého scénáře spuštění/zastavení, si můžete projít historii spuštění a zjistit, proč se naplánovaný scénář spuštění nebo vypnutí neúspěšně dokončil u jednoho nebo více cílových virtuálních počítačů. Další informace o tom, jak si podrobně projít, najdete v tématu [Logic Apps historie spuštění](../../logic-apps/monitor-logic-apps.md#review-runs-history).

### <a name="azure-storage"></a>Azure Storage

Můžete si prohlédnout podrobnosti o operacích provedených na virtuálních počítačích, které jsou zapsány do tabulky **requestsstoretable** v účtu služby Azure Storage, který se používá pro spuštění nebo zastavení virtuálních počítačů v2 (Preview). Tyto záznamy zobrazíte provedením následujících kroků.

1. Přejděte do účtu úložiště v Azure Portal a v účtu v levém podokně vyberte * * Průzkumník služby Storage (Preview).
1. Vyberte **tabulky** a pak vyberte **requeststoretable**.
1. Každý záznam v tabulce představuje akci spuštění/zastavení prováděnou s virtuálním počítačem Azure na základě cílového oboru definovaného ve scénáři aplikace logiky. Výsledky můžete filtrovat podle libovolné vlastnosti záznamu (například časové RAZÍTKo, akce nebo TARGETTOPLEVELRESOURCENAME).

### <a name="azure-functions"></a>Azure Functions

Nejnovější podrobnosti o voláních můžete zobrazit pro kterýkoli z Azure Functions zodpovědných za spuštění virtuálního počítače a jeho zastavení. Nejdřív si prohlédněte tok provádění.

Tok spouštění pro **plánovaný** i **sekvenční** scénář je řízen stejnou funkcí. Schéma datové části určuje, který scénář je proveden. V případě **naplánovaných** scénářů je tok spouštění > fronty **VirtualMachineRequestExecutor** ve frontě **http >** **VirtualMachineRequestOrchestrator** .

Z aplikace logiky je **naplánovaná** funkce http vyvolána pomocí schématu datové části. Jakmile **naplánovaná** funkce http obdrží požadavek, pošle tyto informace do funkce Queue **Orchestrator** , která zase vytvoří několik front pro každý virtuální počítač, aby provedla tuto akci.

Chcete-li zobrazit podrobnosti o vyvolání, proveďte následující kroky.

1. V Azure Portal přejděte na **Azure Functions**.
1. V seznamu vyberte App Function (spustit nebo zastavit virtuální počítače v2) (Preview).
1. V levém podokně vyberte **funkce** .
1. V seznamu se zobrazí několik funkcí přidružených pro každý scénář. Vyberte **naplánovanou** funkci http.
1. V levém podokně vyberte **monitor** .
1. Vyberte nejnovější trasování spuštění, abyste zobrazili podrobnosti o vyvolání a část zprávy pro podrobné protokolování.
1. Opakujte stejný postup u každé funkce popsané jako součást kontroly toku spuštění dříve.

Další informace o monitorování Azure Functions najdete v tématu [analýza Azure Functions telemetrie v Application Insights](../../azure-functions/analyze-telemetry-data.md).

## <a name="next-steps"></a>Další kroky

Další informace o monitorování Azure Functions a Logic Apps:

* [Azure Functions monitorování](../../azure-functions/functions-monitoring.md).

* [Jak nakonfigurovat monitorování pro Azure Functions](../../azure-functions/configure-monitoring.md).

* [Monitorujte aplikace logiky](../../logic-apps/monitor-logic-apps.md).
---
title: Vývoj a ladění úloh Azure Stream Analytics místně
description: Zjistěte, jak vyvíjet a testovat úlohy Azure Stream Analytics na místním počítači, než je spustíte na webu Azure Portal.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 736fce1d4b347e36ad5c10ca89ad0627104a0232
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879840"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Vývoj a ladění úloh Azure Stream Analytics místně

I když můžete vytvářet a testovat úlohy Azure Stream Analytics na webu Azure Portal, mnoho vývojářů preferuje prostředí pro místní vývoj. Stream Analytics usnadňuje použití oblíbeného editoru kódu a vývojových nástrojů k vytváření a testování úloh pomocí živých streamů událostí z Azure Event Hub, IoT Hub a Blob Storage pomocí plně funkčního místního runtime jednoho uzlu. Úlohy můžete také odeslat do Azure přímo z místního vývojového prostředí.

## <a name="local-development-environments"></a>Prostředí místního rozvoje

Způsob vývoje úloh Služby Stream Analytics v místním počítači závisí na předvolbách nástrojů a dostupnosti funkcí. Podívejte se na [porovnání funkcí Azure Stream Analytics](feature-comparison.md) a zjistěte, jaké funkce jsou podporovány pro každé vývojové prostředí.

Prostředí v následující tabulce podporují místní rozvoj:

|Prostředí                              |Popis    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| Rozšíření [Nástroje Azure Stream Analytics](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) pro kód Visual Studia umožňuje vytvářet, spravovat a testovat úlohu analýzy Streamu místně i v cloudu s bohatou službou IntelliSense a nativní správou zdrojových zdrojů. Podporuje vývoj na Linuxu, MacOS a Windows. Další informace najdete [v tématu Vytvoření úlohy Azure Stream Analytics v kódu Visual Studia](quick-create-vs-code.md).|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Nástroje Pro analýzu Stream Analytics jsou součástí úloh vývoje a ukládání a zpracování dat Azure ve Visual Studiu. Pomocí sady Visual Studio můžete psát vlastní uživatelem definované funkce jazyka C# a deserializátory. Další informace najdete [v tématu Vytvoření úlohy Azure Stream Analytics pomocí Visual Studia](stream-analytics-quick-create-vs.md).|
|[Příkazový řádek nebo terminál](stream-analytics-tools-for-visual-studio-cicd.md)|Balíček NuGet služby Azure Stream Analytics CI/CD poskytuje nástroje pro sestavení projektu Visual studio, místní testování na libovolném počítači. Balíček Azure Stream Analytics CI/CD npm poskytuje nástroje pro sestavení projektu Visual Studio Code (který generuje šablonu Azure Resource Manager) na libovolném počítači.|

## <a name="next-steps"></a>Další kroky

* [Test Stream Analytics dotazy místně s ukázkovými daty pomocí kódu Sady Visual Studio](visual-studio-code-local-run.md)
* [Test Stream Analytics dotazy místně proti vstupu živého datového proudu pomocí kódu Sady Visual Studio](visual-studio-code-local-run-live-input.md)
* [Test Stream Analytics dotazy místně s Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testování živých dat místně pomocí nástrojů Azure Stream Analytics pro Visual Studio](stream-analytics-live-data-local-testing.md)

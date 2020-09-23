---
title: Místní vývoj a ladění úloh Azure Stream Analytics
description: Naučte se vyvíjet a testovat úlohy Azure Stream Analytics v místním počítači, než je spustíte v Azure Portal.
ms.author: mamccrea
author: mamccrea
ms.topic: conceptual
ms.date: 03/31/2020
ms.service: stream-analytics
ms.openlocfilehash: 797577ce976f71f41ece0d103bd0145775d48b56
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90885590"
---
# <a name="develop-and-debug-azure-stream-analytics-jobs-locally"></a>Místní vývoj a ladění úloh Azure Stream Analytics

I když máte možnost vytvářet a testovat úlohy Azure Stream Analytics v Azure Portal, mnoho vývojářů dává přednost místnímu vývojovému prostředí. Stream Analytics usnadňuje použití vašeho oblíbeného editoru kódu a vývojářských nástrojů k vytváření a testování úloh pomocí živých streamů událostí z centra událostí Azure, IoT Hub a Blob Storage pomocí plně funkčního místního prostředí runtime s jedním uzlem. Do Azure můžete také odesílat úlohy přímo z místního vývojového prostředí.

## <a name="local-development-environments"></a>Místní vývojová prostředí

Způsob, jakým vyvíjíte Stream Analytics úlohy na místním počítači, závisí na vašich volbách nástrojů a dostupnosti funkcí. Informace o tom, jaké funkce jsou podporovány pro každé vývojové prostředí, najdete v tématu [porovnání funkcí Azure Stream Analytics](feature-comparison.md) .

Prostředí v následující tabulce podporují místní vývoj:

|Prostředí                              |Description    |
|-----------------------------------------|------------|
|[Visual Studio Code](visual-studio-code-explore-jobs.md)| [Rozšíření Azure Stream Analytics nástrojů](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) pro Visual Studio Code umožňuje vytvářet, spravovat a testovat úlohu Stream Analytics jak místně, tak v cloudu pomocí bohatých technologií IntelliSense a nativního správy zdrojového kódu. Podporuje vývoj v systémech Linux, MacOS a Windows. Další informace najdete v tématu [Vytvoření úlohy Azure Stream Analytics v Visual Studio Code](quick-create-visual-studio-code.md). Rozšíření podporuje také [Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/) , což je vývojářské prostředí hostované v cloudu.|
|[Visual Studio 2019](stream-analytics-tools-for-visual-studio-install.md) |Stream Analytics nástroje jsou součástí vývoje Azure a úloh úložiště a zpracování dat v aplikaci Visual Studio. Můžete použít Visual Studio k psaní vlastních uživatelsky definovaných funkcí C# a deserializace. Další informace najdete v tématu [Vytvoření úlohy Azure Stream Analytics pomocí sady Visual Studio](stream-analytics-quick-create-vs.md).|
|[Příkazový řádek nebo terminál](stream-analytics-tools-for-visual-studio-cicd.md)|Balíček NuGet pro Azure Stream Analytics CI/CD poskytuje nástroje pro sestavení projektu sady Visual Studio, místní testování na libovolném počítači. Balíček Azure Stream Analytics CI/CD npm poskytuje nástroje pro Visual Studio Code sestavení projektů (které vygenerují šablonu Azure Resource Manager) na libovolném počítači.|

## <a name="next-steps"></a>Další kroky

* [Test Stream Analytics dotazy místně s použitím ukázkových dat pomocí Visual Studio Code](visual-studio-code-local-run.md)
* [Test Stream Analytics dotazy místně proti vstupu živého datového proudu pomocí Visual Studio Code](visual-studio-code-local-run-live-input.md)
* [Testování Stream Analytics dotazů v místním prostředí se sadou Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Místní testování živých dat pomocí Azure Stream Analyticsch nástrojů pro Visual Studio](stream-analytics-live-data-local-testing.md)

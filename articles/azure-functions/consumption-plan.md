---
title: Azure Functions hostování plánu spotřeby
description: Přečtěte si, jak hostující plán spotřeby funkcí Azure umožňuje spustit kód v prostředí, které se dynamicky škáluje, ale platíte jenom za prostředky použité během provádění.
ms.date: 8/31/2020
ms.topic: conceptual
ms.openlocfilehash: d292a70a8dfaa4cebdb99f2bcb5420c8b8ab9cd8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98760536"
---
# <a name="azure-functions-consumption-plan-hosting"></a>Azure Functions hostování plánu spotřeby

Pokud používáte plán spotřeby, instance Azure Functions hostitele se dynamicky přidávají a odstraňují na základě počtu příchozích událostí. Plán spotřeby je plně možnost hostování bez <em>serveru</em> pro Azure Functions.

## <a name="benefits"></a>Výhody

Plán spotřeby se automaticky škáluje, a to i během období vysokého zatížení. Při spouštění funkcí v plánu spotřeby se vám budou účtovat výpočetní prostředky jenom v případě, že jsou vaše funkce spuštěné. V plánu Spotřeba po nastavené době vyprší časový limit spuštění funkce.

Porovnání plánu spotřeby s jiným typem plánování a hostování najdete v tématu [možnosti škálování a hostování funkcí](functions-scale.md).

## <a name="billing"></a>Fakturace

Fakturace vychází z počtu spuštění, doby spuštění a použité paměti. Použití je agregované napříč všemi funkcemi v rámci aplikace Function App. Další informace najdete na stránce s [cenami Azure Functions](https://azure.microsoft.com/pricing/details/functions/).

Další informace o odhadu nákladů při spuštění v plánu spotřeby najdete v tématu [porozumění nákladům na plán spotřeby](functions-consumption-costs.md).

## <a name="create-a-consumption-plan-function-app"></a>Vytvoření aplikace funkcí plánu spotřeby

Když vytvoříte aplikaci funkcí v Azure Portal, bude výchozím plánem spotřeby. Pokud k vytvoření aplikace Function App používáte rozhraní API, nemusíte nejdřív vytvořit plán App Service, protože používáte prémiové a vyhrazené plány.

Pomocí následujících odkazů se dozvíte, jak vytvořit aplikaci funkcí bez serveru v plánu spotřeby, a to buď programově, nebo v Azure Portal:

+ [Azure CLI](./scripts/functions-cli-create-serverless.md)
+ [Azure Portal](./functions-get-started.md)
+ [Šablona Azure Resource Manageru](functions-create-first-function-resource-manager.md)

Můžete také vytvořit aplikace Function App v plánu spotřeby při publikování projektu Functions z [Visual Studio Code](./create-first-function-vs-code-csharp.md#publish-the-project-to-azure) nebo sady [Visual Studio](functions-create-your-first-function-visual-studio.md#publish-the-project-to-azure).

## <a name="multiple-apps-in-the-same-plan"></a>Více aplikací ve stejném plánu

Aplikace Function App ve stejné oblasti se dají přiřadit ke stejnému plánu spotřeby. Neexistuje žádný nevýhodou ani dopad na to, aby ve stejném plánu spotřeby běželo víc aplikací. Přiřazení více aplikací ke stejnému plánu spotřeby nemá žádný vliv na odolnost, škálovatelnost nebo spolehlivost každé aplikace.

## <a name="next-steps"></a>Další kroky

+ [Azure Functions možností hostování](functions-scale.md)
+ [Škálování založené na událostech v Azure Functions](event-driven-scaling.md)

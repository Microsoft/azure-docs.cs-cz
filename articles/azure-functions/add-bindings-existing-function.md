---
title: Připojení funkcí k ostatním službám Azure
description: Naučte se, jak přidat vazby, které se připojují k jiným službám Azure, k existující funkci v projektu Azure Functions.
ms.topic: how-to
ms.date: 04/29/2020
ms.openlocfilehash: d1c6f5bb8ca5fcf995b8a8d326abbec96f1d2e35
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99258197"
---
# <a name="connect-functions-to-azure-services-using-bindings"></a>Připojení funkcí ke službám Azure pomocí vazeb

Při vytváření funkce se do projektu přidá kód triggeru specifický pro jazyk ze sady šablon triggerů. Pokud chcete svou funkci připojit k jiným službám pomocí vstupních nebo výstupních vazeb, musíte do své funkce přidat konkrétní definice vazeb. Další informace o vazbách naleznete v tématu [Azure Functions triggery a koncepty vazeb](functions-triggers-bindings.md).

## <a name="local-development"></a>Místní vývoj       

Při vývoji funkcí v místním prostředí je nutné aktualizovat kód funkce a přidat vazby. Použití Visual Studio Code může usnadnit přidávání vazeb do funkce.  

### <a name="visual-studio-code"></a>Visual Studio Code

Když použijete Visual Studio Code k vytvoření funkce a funkce používá function.jsv souboru, může rozšíření Azure Functions automaticky přidat vazbu na existující function.jssouboru. Další informace najdete v tématu [Přidání vstupních a výstupních vazeb](functions-develop-vs-code.md#add-input-and-output-bindings).   

### <a name="manually-add-bindings-based-on-examples"></a>Ruční přidání vazeb na základě příkladů

Při přidávání vazby k existující funkci budete potřebovat aktualizovat kód funkce a function.jsv konfiguračním souboru, pokud ho používá váš jazyk. Knihovna tříd .NET i funkce jazyka Java používají atributy namísto function.jsv, takže je třeba je aktualizovat.

Pomocí následující tabulky můžete najít příklady specifických typů vazeb, které vám pomůžou při aktualizaci existující funkce. Nejprve vyberte kartu jazyk, která odpovídá vašemu projektu. 

[!INCLUDE [functions-bindings-code-example-chooser](../../includes/functions-bindings-code-example-chooser.md)]

## <a name="azure-portal"></a>portál Azure

Při vývoji funkcí v [Azure Portal](https://portal.azure.com)přidáte vstupní a výstupní vazby na kartě **Integration** pro danou funkci. Nové vazby jsou přidány do function.jsv souboru nebo do atributů metody v závislosti na vašem jazyce. Následující články ukazují příklady přidávání vazeb k existující funkci na portálu:

+ [Výstupní vazba služby Queue Storage](functions-integrate-storage-queue-output-binding.md)
+ [Výstupní vazba Azure Cosmos DB](functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="next-steps"></a>Další kroky

+ [Koncepty triggerů a vazeb Azure Functions](functions-triggers-bindings.md).

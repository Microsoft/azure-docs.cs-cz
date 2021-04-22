---
title: Azure Functions Event Grid místní ladění
description: Naučte se místně ladit Azure Functions aktivované událostí Event Grid
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 7483a097b188b9f96221a13964992c7b02332258
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107891950"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Funkce Azure Function Event Grid aktivovat místní ladění

Tento článek ukazuje, jak ladit místní funkci, která zpracovává událost Azure Event Grid vyvolanou účtem úložiště. 

## <a name="prerequisites"></a>Požadavky

- Vytvoření nebo použití existující aplikace Function App
- Vytvoření nebo použití existujícího účtu úložiště
- Stáhněte si [ngrok](https://ngrok.com/) , aby mohl Azure volat místní funkci.

## <a name="create-a-new-function"></a>Vytvoření nové funkce

Otevřete aplikaci Function App v aplikaci Visual Studio, klikněte pravým tlačítkem na název projektu v Průzkumník řešení a klikněte na **přidat > nové funkce Azure Functions**.

V *novém okně funkce Azure* vyberte **aktivační událost Event Grid** a klikněte na **OK**.

![Vytvoření nové funkce](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Po vytvoření funkce otevřete soubor kódu a zkopírujte na začátek souboru poznámku URL. Toto umístění se používá při konfiguraci triggeru Event Grid.

![Kopírovat umístění](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Potom nastavte zarážku na řádku, který začíná na `log.LogInformation` .

![Nastavit zarážku](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Potom **stisknutím klávesy F5** spusťte ladicí relaci.

[!INCLUDE [functions-event-grid-local-dev](../../includes/functions-event-grid-local-dev.md)]

## <a name="debug-the-function"></a>Ladění funkce

Jakmile Event Grid rozpozná, že se do kontejneru úložiště nahraje nový soubor, je bod přerušení v místní funkci.

![Spustit ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v rámci tohoto článku, odstraňte z účtu úložiště kontejner **testu** .

## <a name="next-steps"></a>Další kroky

- [Automatizace změny velikosti nahraných obrázků s využitím služby Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Aktivační událost Event Grid pro Azure Functions](./functions-bindings-event-grid.md)

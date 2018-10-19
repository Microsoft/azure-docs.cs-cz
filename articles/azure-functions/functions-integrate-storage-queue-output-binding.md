---
title: Přidání zpráv do fronty Azure Storage pomocí služby Functions | Dokumentace Microsoftu
description: Pomocí služby Azure Functions vytvoříte funkci bez serveru, kterou vyvolá požadavek HTTP a která vytvoří zprávu ve frontě služby Azure Storage.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 09/19/2017
ms.author: glenga
ms.custom: mvc
ms.openlocfilehash: e48eac4cdc1e98e21a122850b1dc7d3e8f4efe07
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854520"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Přidání zpráv do fronty Azure Storage pomocí funkcí

Vstupní a výstupní vazby ve službě Azure Functions poskytují deklarativní způsob zpřístupnění k dat z externích služeb. V tomto rychlém startu použijete výstupní vazbu k vytvoření zprávy ve frontě, když požadavek HTTP aktivuje funkci. K zobrazení zpráv fronty, které vaše funkce vytvoří, použijete Průzkumníka služby Microsoft Azure Storage:

![Zpráva fronty zobrazená v Průzkumníku služby Storage](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

* Postupujte podle pokynů v části [Vytvoření první funkce na webu Azure Portal](functions-create-first-azure-function.md) a neprovádějte krok **Vyčištění prostředků**. Tento rychlý start vytvoří aplikaci funkcí a funkci, kterou tady použijete.

* Nainstalujte [Průzkumníka služby Microsoft Azure Storage](http://storageexplorer.com/). Tento nástroj použijete k prozkoumání zpráv fronty, které vytvoří vaše výstupní vazba.

## <a name="add-binding"></a>Přidání výstupní vazby

V této části použijete uživatelské rozhraní portálu pro přidání výstupní vazby úložiště front do funkce, kterou jste vytvořili dříve. Tato vazba umožní k vytvoření zprávy ve frontě napsat jen minimum kódu. Nemusíte psát kód pro úlohy, jako je otevření připojení úložiště, vytvoření fronty nebo získání odkazu na frontu. O tyto věci se z vás postarají modul runtime Azure Functions a výstupní vazba fronty.

1. Na webu Azure Portal otevřete stránku pro aplikaci funkcí, kterou jste vytvořili v tématu [Vytvoření první funkce na webu Azure Portal](functions-create-first-azure-function.md). Stačí vybrat **Všechny služby > Aplikace funkcí** a pak vybrat vaši aplikaci funkcí.

1. Vyberte funkci, kterou jste vytvořili v tomto dřívějším rychlém startu.

1. Vyberte **Integrovat > Nový výstup > Azure Queue Storage**.

1. Klikněte na **Vybrat**.

    ![Přidejte výstupní vazbu Queue Storage do funkce na webu Azure Portal.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

1. Pokud se zobrazí zpráva **Rozšíření nejsou nainstalovaná**, výběrem možnosti **Instalovat** nainstalujte do aplikace funkcí rozšíření vazby služby Storage. Tento proces může trvat jednu či dvě minuty.

    ![Instalace rozšíření vazby služby Storage](./media/functions-integrate-storage-queue-output-binding/functions-integrate-install-binding-extension.png)

1. V části **výstupu Azure Queue Storage** použijte nastavení uvedená v tabulce za tímto snímkem obrazovky: 

    ![Přidejte výstupní vazbu Queue Storage do funkce na webu Azure Portal.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | Nastavení      |  Navrhovaná hodnota   | Popis                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název parametru zprávy** | outputQueueItem | Název výstupního parametru vazby. | 
    | **Připojení k účtu úložiště** | AzureWebJobsStorage | Můžete použít připojení k účtu úložiště, které už používá vaše aplikace Function App, nebo můžete vytvořit nové.  |
    | **Název fronty**   | outqueue    | Název fronty, ke které se připojíte ve svém účtu úložiště. |

1. Kliknutím na **Uložit** přidejte vazbu.

Teď máte definovanou výstupní vazbu a je potřeba aktualizovat kód tak, aby tuto vazbu využíval k přidávání zpráv do fronty.  

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

V této části přidáte kód, který zapíše zprávu do výstupní fronty. Zpráva obsahuje hodnotu, která se předala triggeru HTTP v řetězci dotazu. Pokud například řetězec dotazu obsahuje `name=Azure`, zpráva fronty bude *Name passed to the function: Azure*.

1. Vybráním určité funkce zobrazíte kód této funkce v editoru.

1. Aktualizujte kód funkce v závislosti na jazyku funkce:

    # <a name="ctabcsharp"></a>[C\#](#tab/csharp)

    Přidejte parametr **outputQueueItem** do podpisu metody, jak ukazuje následující obrázek.

    ```cs
    public static async Task<IActionResult> Run(HttpRequest req,
        ICollector<string> outputQueueItem, ILogger log)
    {
        ...
    }
    ```

    V těle funkce bezprostředně před příkazem `return` zadejte kód, který použije tento parametr k vytvoření zprávy fronty.

    ```cs
    outputQueueItem.Add("Name passed to the function: " + name);
    ```

    # <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

    Přidejte kód, který používá výstupní vazbu objektu `context.bindings` k vytvoření zprávy fronty. Vložte tento kód před příkaz `context.done`.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. Změny uložíte tak, že vyberete **Uložit**.

## <a name="test-the-function"></a>Testování funkce

1. Po uložení změn kódu vyberte **Spustit**. 

    ![Přidejte výstupní vazbu Queue Storage do funkce na webu Azure Portal.](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

    Všimněte si, že **text žádosti** jako `name` obsahuje hodnotu *Azure*. Tato hodnota se zobrazí ve zprávě fronty, která se vytvoří při vyvolání této funkce.
    
    Jako alternativu k volbě **Spustit** můžete tuto funkci volat také tak, že zadáte adresu URL do prohlížeče a v řetězci dotazu uvedete hodnotu `name`. Metodu s použitím prohlížeče najdete v [předchozím rychlém startu](functions-create-first-azure-function.md#test-the-function).

2. Zkontrolujte protokoly a zkontrolujte, jestli se spuštění funkce zdařilo. 

Při prvním použití výstupní vazby vytvoří modul runtime Functions v účtu úložiště novou frontu s názvem **outqueue**. K ověření, že se vytvořila fronta a zpráva v ní, použijete Průzkumníka služby Storage.

### <a name="connect-storage-explorer-to-your-account"></a>Propojení Průzkumníka služby Storage s vaším účtem

Pokud jste si už nainstalovali Průzkumníka služby Storage a propojili jste ho k účtu úložiště, který v tomto rychlém startu používáte, přeskočte tuto část.

2. Spusťte [Microsoft Azure Storage Explorer](http://storageexplorer.com/), vlevo vyberte ikonu připojení, zvolte **Použít název a klíč účtu úložiště** a vyberte **Další**.

    ![Spusťte nástroj Průzkumník účtu úložiště.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)

1. Na stránce aplikace funkcí na webu Azure Portal vyberte vaši funkci a potom vyberte **Integrovat**.

1. Vyberte výstupní vazbu **Azure Queue Storage**, kterou jste přidali v předchozím kroku.

1. Rozbalte oddíl **Dokumentace** v dolní části stránky. 

   Na portálu se zobrazí přihlašovací údaje, které můžete v Průzkumníku služby Storage použít pro připojení k účtu úložiště.

   ![Získejte přihlašovací údaje účtu úložiště.](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

1. Zkopírujte z portálu hodnotu **Název účtu** a vložte ji do pole **Název účtu** v Průzkumníku služby Storage.
 
1. Kliknutím na ikonu zobrazení/skrytí vedle pole **Klíč účtu** zobrazte tuto hodnotu a potom zkopírujte hodnotu z pole **Klíč účtu** a vložte ji do pole **Klíč účtu** v Průzkumníku služby Storage.
  
3. Vyberte **Další > Připojit**.

   ![Vložte přihlašovací údaje úložiště a připojte se.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

### <a name="examine-the-output-queue"></a>Prozkoumání výstupní fronty

4. V Průzkumníku služby Storage vyberte účet úložiště, který používáte pro tento rychlý start.

1. Rozbalte uzel **Fronty** a potom vyberte frontu s názvem **outqueue**. 

   Tato fronta obsahuje zprávu, kterou vytvořila výstupní vazba fronty při spuštění funkce aktivované protokolem HTTP. Pokud jste tuto funkci volali s výchozí hodnotou `name` (*Azure*), zpráva fronty je *Name passed to the function: Azure*.

    ![Zpráva fronty zobrazená v Průzkumníku služby Storage](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)

2. Spusťte tuto funkci znovu. Ve frontě se zobrazí nová zpráva.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přidali k existující funkci výstupní vazbu. Další informace o vazbách do úložiště front najdete v tématu [Vazby front úložiště služby Azure Functions](functions-bindings-storage-queue.md). 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

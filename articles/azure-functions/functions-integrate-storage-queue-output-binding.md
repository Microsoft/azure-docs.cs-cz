---
title: Přidání zpráv do fronty Azure Storage pomocí funkcí
description: Pomocí služby Azure Functions vytvoříte funkci bez serveru, kterou vyvolá požadavek HTTP a která vytvoří zprávu ve frontě služby Azure Storage.
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.topic: how-to
ms.date: 04/24/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 9c635b01bcd04bd03191fca2590b0189bad0f544
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90982011"
---
# <a name="add-messages-to-an-azure-storage-queue-using-functions"></a>Přidání zpráv do fronty Azure Storage pomocí funkcí

Vstupní a výstupní vazby ve službě Azure Functions poskytují deklarativní způsob zpřístupnění k dat z externích služeb. V tomto rychlém startu použijete výstupní vazbu k vytvoření zprávy ve frontě, když požadavek HTTP aktivuje funkci. Pomocí kontejneru Azure Storage zobrazíte zprávy fronty, které funkce vytvoří.

## <a name="prerequisites"></a>Požadavky

K provedení kroků v tomto kurzu Rychlý start je potřeba:

- Předplatné Azure. Pokud ho nemáte, než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Postupujte podle pokynů v části [Vytvoření první funkce na webu Azure Portal](functions-create-first-azure-function.md) a neprovádějte krok **Vyčištění prostředků**. Tento rychlý start vytvoří aplikaci funkcí a funkci, kterou tady použijete.

## <a name="add-an-output-binding"></a><a name="add-binding"></a>Přidání výstupní vazby

V této části použijete uživatelské rozhraní portálu pro přidání výstupní vazby úložiště front do funkce, kterou jste vytvořili dříve. Tato vazba umožňuje napsat minimální kód pro vytvoření zprávy ve frontě. Nemusíte psát kód pro úlohy, jako je otevření připojení úložiště, vytvoření fronty nebo získání odkazu na frontu. O tyto věci se z vás postarají modul runtime Azure Functions a výstupní vazba fronty.

1. Na webu Azure Portal otevřete stránku pro aplikaci funkcí, kterou jste vytvořili v tématu [Vytvoření první funkce na webu Azure Portal](functions-create-first-azure-function.md). Stránku otevřete tak, že vyhledáte a vyberete **Function App**. Pak vyberte svou aplikaci Function App.

1. Vyberte aplikaci Function App a potom vyberte funkci, kterou jste vytvořili v předchozím rychlém startu.

1. Vyberte **integrace**a pak vyberte **+ Přidat výstup**.

   :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding.png" alt-text="Vytvořte pro svou funkci výstupní vazbu." border="true":::

1. Vyberte typ vazby **Azure Queue Storage** a přidejte nastavení, jak je uvedeno v tabulce, která následuje po tomto snímku obrazovky: 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-create-output-binding-details.png" alt-text="Přidejte výstupní vazbu Queue Storage do funkce na webu Azure Portal." border="true":::
    
    | Nastavení      |  Navrhovaná hodnota   | Popis                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Název parametru zprávy** | outputQueueItem | Název výstupního parametru vazby. | 
    | **Název fronty**   | outqueue  | Název fronty, ke které se připojíte ve svém účtu úložiště. |
    | **Připojení k účtu úložiště** | AzureWebJobsStorage | Můžete použít připojení k účtu úložiště, které už používá vaše aplikace Function App, nebo můžete vytvořit nové.  |

1. Vyberte **OK** a přidejte vazbu.

Teď máte definovanou výstupní vazbu a je potřeba aktualizovat kód tak, aby tuto vazbu využíval k přidávání zpráv do fronty.  

## <a name="add-code-that-uses-the-output-binding"></a>Přidání kódu, který používá výstupní vazbu

V této části přidáte kód, který zapíše zprávu do výstupní fronty. Zpráva obsahuje hodnotu, která se předala triggeru HTTP v řetězci dotazu. Pokud například řetězec dotazu obsahuje `name=Azure`, zpráva fronty bude *Name passed to the function: Azure*.

1. Ve své funkci vyberte **Code + test** pro zobrazení kódu funkce v editoru.

1. Aktualizujte kód funkce v závislosti na jazyku funkce:

    # <a name="c"></a>[R\#](#tab/csharp)

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

    # <a name="javascript"></a>[JavaScript](#tab/nodejs)

    Přidejte kód, který používá výstupní vazbu objektu `context.bindings` k vytvoření zprávy fronty. Vložte tento kód před příkaz `context.done`.

    ```javascript
    context.bindings.outputQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ---

1. Změny uložíte tak, že vyberete **Uložit**.

## <a name="test-the-function"></a>Testování funkce

1. Po uložení změn kódu vyberte **test**.
1. Potvrďte, že test odpovídá imagi níže, a pak vyberte **Spustit**. 

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png" alt-text="Otestujte vazbu úložiště fronty v Azure Portal." border="true":::

    Všimněte si, že **text žádosti** jako `name` obsahuje hodnotu *Azure*. Tato hodnota se zobrazí ve zprávě fronty, která se vytvoří při vyvolání této funkce.
    
    Jako alternativu k volbě **Spustit** můžete tuto funkci volat také tak, že zadáte adresu URL do prohlížeče a v řetězci dotazu uvedete hodnotu `name`. Metodu s použitím prohlížeče najdete v [předchozím rychlém startu](functions-create-first-azure-function.md#test-the-function).

1. Zkontrolujte protokoly a zkontrolujte, jestli se spuštění funkce zdařilo. 

Při prvním použití výstupní vazby vytvoří modul runtime Functions v účtu úložiště novou frontu s názvem **outqueue**. Pomocí účtu úložiště ověříte, že se vytvořila fronta a zpráva v ní.

### <a name="find-the-storage-account-connected-to-azurewebjobsstorage"></a>Vyhledání účtu úložiště připojeného k AzureWebJobsStorage


1. Přejít do aplikace Function App a vybrat položku **Konfigurace**.

1. V části **nastavení aplikace**vyberte **AzureWebJobsStorage**.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-find-storage-account.png" alt-text="Snímek obrazovky zobrazující stránku konfigurace s AzureWebJobsStorage vybranými." border="true":::

1. Vyhledejte a poznamenejte si název účtu.

    :::image type="content" source="./media/functions-integrate-storage-queue-output-binding/function-storage-account-name.png" alt-text="Vyhledejte účet úložiště připojený k AzureWebJobsStorage." border="true":::

### <a name="examine-the-output-queue"></a>Prozkoumání výstupní fronty

1. Ve skupině prostředků aplikace Function App vyberte účet úložiště, který používáte pro tento rychlý Start.

1. V části **Služba front**vyberte **fronty** a vyberte frontu s názvem **proqueue**. 

   Tato fronta obsahuje zprávu, kterou vytvořila výstupní vazba fronty při spuštění funkce aktivované protokolem HTTP. Pokud jste tuto funkci volali s výchozí hodnotou `name` (*Azure*), zpráva fronty je *Name passed to the function: Azure*.

1. Spusťte tuto funkci znovu. Ve frontě se zobrazí nová zpráva.  

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [Clean up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste přidali k existující funkci výstupní vazbu. Další informace o vazbách do úložiště front najdete v tématu [Vazby front úložiště služby Azure Functions](functions-bindings-storage-queue.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps-2.md)]

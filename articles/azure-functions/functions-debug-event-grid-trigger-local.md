---
title: Funkce Event Grid místní ladění Azure
description: Zjistěte, jak místní ladění Azure functions aktivaci událostí služby Event Grid
services: functions
documentationcenter: na
author: craigshoemaker
manager: jeconnoc
keywords: Azure functions, functions, architektury bez serveru
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 7a3468d9a0d128bd51ae742189e60bb8e2af6c0e
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2018
ms.locfileid: "50097800"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Funkce Azure Event Grid aktivovat místní ladění

Tento článek ukazuje, jak ladit místní funkce, která zpracovává Azure Event Grid události vyvolané službou účtu úložiště. 

## <a name="prerequisites"></a>Požadavky

- Vytvořit nebo použít existující aplikaci function app
- Vytvořit nebo použít existující účet úložiště
- Stáhněte si [ngrok](https://ngrok.com/) umožní zavolat lokální funkce Azure

## <a name="create-a-new-function"></a>Vytvoření nové funkce

Otevřete aplikaci funkcí v sadě Visual Studio, klikněte pravým tlačítkem na název projektu v Průzkumníku řešení a klikněte na tlačítko **Přidat > Nová funkce Azure Functions**.

V *novou funkci Azure Functions* okně **trigger služby Event Grid** a klikněte na tlačítko **OK**.

![Vytvoření nové funkce](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Po vytvoření funkce, otevřete soubor kódu a kopii, kterou adrese URL označené jako komentář v horní části souboru. Toto umístění se používá při konfiguraci triggeru služby Event Grid.

![Umístění pro kopírované](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Nastavte zarážku na řádek, který začíná `log.LogInformation`.

![nastavit zarážku](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Dále **stisknutím klávesy F5** spustíte relaci ladění.

## <a name="allow-azure-to-call-your-local-function"></a>Povolit Azure pro volání místní funkce

Ke vstupu do funkce laděného na svém počítači, je nutné povolit způsob, jak Azure ke komunikaci s vaší místní funkce z cloudu.

[Ngrok](https://ngrok.com/) nástroj poskytuje způsob, jak Azure pro volání funkce, které běží na vašem počítači. Spustit *ngrok* pomocí následujícího příkazu:

```bash
ngrok http -host-header=localhost 7071
```
Jak je nastavit nástroj, příkazové okno by měl vypadat podobně jako na následujícím snímku obrazovky:

![Spustit ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Kopírovat **HTTPS** URL generována v případě *ngrok* běží. Tato hodnota se používá při konfiguraci koncový bod event grid událostí.

 ## <a name="add-a-storage-event"></a>Přidání úložiště událostí

Otevřete na webu Azure portal a přejděte do účtu úložiště a klikněte **události** možnost.

![Přidat událost účtu úložiště](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

V *události* okna, klikněte na **odběr události** tlačítko. V *ještě předplatné* okna, klikněte na *typ koncového bodu* rozevírací seznam a vyberte **Webhook**.

![Vyberte typ předplatného](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Jakmile je nakonfigurovaný koncový bod typu, klikněte na **vyberte koncový bod** ke konfiguraci hodnoty koncového bodu.

![Vyberte typ koncového bodu](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

*Koncový bod odběratele* hodnota se skládá ze tří různých hodnot. Předpona, která je adresa URL HTTPS generovaných *ngrok*. Zbývající část adresy URL pochází z adresu URL najdete v souboru kódu funkce s názvem funkce na konec přidána číslice. Počínaje adresu URL souboru kódu funkce *ngrok* nahradí adresu URL `http://localhost:7071` a funkce, název nahradí `{functionname}`.

Následující snímek obrazovky ukazuje, jak by měl vypadat konečná adresa URL:

![Výběr koncového bodu](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Po zadání příslušné hodnoty, klikněte na tlačítko **potvrďte výběr**.

> [!IMPORTANT]
> Pokaždé, když spustíte *ngrok*, vygeneruje znovu adresu URL HTTPS a hodnoty. Proto musíte vytvořit nový odběr událostí v pokaždé, když zveřejníte funkce k Azure přes *ngrok*.

## <a name="upload-a-file"></a>Nahrání souboru

Teď můžete nahrát soubor do vašeho účtu úložiště pro aktivaci události služby Event Grid pro vaše místní funkci pro zpracování. 

Otevřít [Průzkumníka služby Storage](https://azure.microsoft.com/features/storage-explorer/) a připojte se k účtu úložiště. 

- Rozbalte **kontejnery objektů Blob** 
- Klikněte pravým tlačítkem a vyberte **vytvořit kontejner objektů Blob**.
- Název kontejneru **testování**
- Vyberte *testování* kontejneru
- Klikněte na tlačítko **nahrát** tlačítko
- Klikněte na tlačítko **nahrání souborů**
- Vyberte soubor a nahrajte ho do kontejneru objektů blob

## <a name="debug-the-function"></a>Funkce ladění

Po služby Event Grid rozpozná, že nahraje nový soubor do kontejneru úložiště, dosáhnete bodu přerušení ve vaší místní funkci.

![Spustit ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto článku, odstranit **testování** kontejneru v účtu úložiště.

## <a name="next-steps"></a>Další postup

- [Automatizace změny velikosti nahraných obrázků s využitím služby Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Trigger služby Event Grid pro službu Azure Functions](./functions-bindings-event-grid.md)

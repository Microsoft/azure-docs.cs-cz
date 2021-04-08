---
title: Azure Functions Event Grid místní ladění
description: Naučte se místně ladit Azure Functions aktivované událostí Event Grid
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: be05d237d2799404c3fd8b5733464e23eeb49aa3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "94833057"
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

## <a name="allow-azure-to-call-your-local-function"></a>Povolení volání místní funkce v Azure

Aby bylo možné přerušit funkci laděnou na počítači, je třeba povolit, aby Azure komunikoval s místní funkcí z cloudu.

Nástroj [ngrok](https://ngrok.com/) poskytuje způsob, jak může Azure volat funkci běžící na vašem počítači. Spusťte *ngrok* pomocí následujícího příkazu:

```bash
ngrok http -host-header=localhost 7071
```
Při nastavení nástroje by okno příkazového řádku mělo vypadat podobně jako na následujícím snímku obrazovky:

![Snímek obrazovky, který zobrazí příkazový řádek po spuštění nástroje "ngrok".](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Zkopírujte adresu URL **protokolu HTTPS** vygenerovanou při spuštění *ngrok* . Tato hodnota se používá při konfiguraci koncového bodu události Event gridu.

## <a name="add-a-storage-event"></a>Přidat událost úložiště

Otevřete Azure Portal a přejděte do účtu úložiště a klikněte na možnost **události** .

![Přidat událost účtu úložiště](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

V okně *události* klikněte na tlačítko **odběr události** . V okně *odběr událostí* klikněte na rozevírací seznam *Typ koncového bodu* a vyberte **Webhook**.

![Vybrat typ předplatného](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Po nakonfigurování typu koncového bodu klikněte na **Vybrat koncový bod** a nakonfigurujte hodnotu koncového bodu.

![Vybrat typ koncového bodu](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

Hodnota *koncového bodu odběratele* se skládá ze tří různých hodnot. Předpona je adresa URL protokolu HTTPS vygenerovaná *ngrok*. Zbývající adresa URL pochází z adresy URL, která se nachází v souboru s kódem funkce, s názvem funkce přidaným na konci. Počínaje adresou URL ze souboru kódu funkce nahrazuje adresa URL *ngrok* `http://localhost:7071` a nahrazuje název funkce `{functionname}` .

Následující snímek obrazovky ukazuje, jak by měla poslední adresa URL vypadat:

![Výběr koncového bodu](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Po zadání příslušné hodnoty klikněte na **potvrdit výběr**.

> [!IMPORTANT]
> Pokaždé, když začnete *ngrok*, adresa URL protokolu HTTPS se znovu vygeneruje a změní se hodnota. Proto musíte vytvořit nové předplatné událostí pokaždé, když vystavíte funkci do Azure prostřednictvím *ngrok*.

## <a name="upload-a-file"></a>Nahrání souboru

Teď můžete do svého účtu úložiště nahrát soubor a aktivovat tak Event Grid událost, která má vaše místní funkce zpracovat. 

Otevřete [Průzkumník služby Storage](https://azure.microsoft.com/features/storage-explorer/) a připojte se k účtu úložiště. 

- Rozbalit **kontejnery objektů BLOB** 
- Klikněte pravým tlačítkem a vyberte **vytvořit kontejner objektů BLOB**.
- Pojmenování **testu** kontejneru
- Výběr kontejneru *testů*
- Klikněte na tlačítko **nahrát** .
- Klikněte na **nahrát soubory** .
- Vyberte soubor a nahrajte ho do kontejneru objektů BLOB.

## <a name="debug-the-function"></a>Ladění funkce

Jakmile Event Grid rozpozná, že se do kontejneru úložiště nahraje nový soubor, je bod přerušení v místní funkci.

![Spustit ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v rámci tohoto článku, odstraňte z účtu úložiště kontejner **testu** .

## <a name="next-steps"></a>Další kroky

- [Automatizace změny velikosti nahraných obrázků s využitím služby Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Aktivační událost Event Grid pro Azure Functions](./functions-bindings-event-grid.md)

---
title: Místní ladění mřížky událostí Azure Functions
description: Naučte se místně ladit funkce Azure spouštěné událostí Grid událostí
author: craigshoemaker
ms.topic: reference
ms.date: 10/18/2018
ms.author: cshoe
ms.openlocfilehash: 97509001aa66c2c1bf0c91b6b2a5ab25f9d6ec88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74227072"
---
# <a name="azure-function-event-grid-trigger-local-debugging"></a>Mřížka událostí Azure aktivační událost místní ladění

Tento článek ukazuje, jak ladit místní funkce, která zpracovává událost Azure Event Grid vyvolané účet úložiště. 

## <a name="prerequisites"></a>Požadavky

- Vytvoření nebo použití existující aplikace pro funkce
- Vytvoření nebo použití existujícího účtu úložiště
- Stažením [ngrok](https://ngrok.com/) umožníte Azure volat místní funkci

## <a name="create-a-new-function"></a>Vytvoření nové funkce

Otevřete aplikaci funkce v Sadě Visual Studio a klikněte pravým tlačítkem myši na název projektu v Průzkumníku řešení a klikněte na **Přidat > novou funkci Azure**.

V okně *Nová funkce Azure* vyberte aktivační událost **a** klepněte na tlačítko **OK**.

![Vytvoření nové funkce](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-function.png)

Po vytvoření funkce otevřete soubor kódu a zkopírujte adresu URL, která byla v horní části souboru komentována. Toto umístění se používá při konfiguraci aktivační události Mřížka událostí.

![Kopírovat umístění](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-copy-location.png)

Potom nastavte zarážku na řádku, `log.LogInformation`který začíná .

![Nastavení zarážky](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-set-breakpoint.png)


Potom **stisknutím klávesy F5** spusťte relaci ladění.

## <a name="allow-azure-to-call-your-local-function"></a>Povolení azure k volání místní funkce

Chcete-li přerušit funkci, která se ladí na vašem počítači, musíte povolit způsob, jak Azure komunikovat s místní funkcí z cloudu.

Nástroj [ngrok](https://ngrok.com/) poskytuje způsob, jak Azure volat funkci spuštěnou na vašem počítači. Začněte *ngrok* pomocí následujícího příkazu:

```bash
ngrok http -host-header=localhost 7071
```
Při nastavení nástroje by příkazové okno mělo vypadat podobně jako následující snímek obrazovky:

![Spustit ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-ngrok.png)

Zkopírujte adresu **URL HTTPS** vygenerovanou při spuštění *ngrok.* Tato hodnota se používá při konfiguraci koncového bodu události mřížky událostí.

## <a name="add-a-storage-event"></a>Přidání události úložiště

Otevřete portál Azure, přejděte na účet úložiště a klikněte na možnost **Události.**

![Přidat událost účtu úložiště](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-add-event.png)

V okně *Události* klikněte na tlačítko **Odběr událostí.** V okně *Sudé odběry* klikněte na rozevírací okno *Typ koncového bodu* a vyberte **webový hák**.

![Vybrat typ předplatného](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-type.png)

Po konfiguraci typu koncového bodu klikněte na **Vybrat koncový bod** pro konfiguraci hodnoty koncového bodu.

![Vybrat typ koncového bodu](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint.png)

Hodnota *koncového bodu odběratele* je tvořena třemi různými hodnotami. Předpona je adresa URL HTTPS vygenerovaná *společností ngrok*. Zbytek adresy URL pochází z adresy URL nalezené v souboru kódu funkce s názvem funkce přidaným na konci. Počínaje adresou URL ze souboru kódu funkce `http://localhost:7071` nahrazuje adresu URL `{functionname}` *ngrok* a nahradí název funkce .

Následující snímek obrazovky ukazuje, jak by měla vypadat konečná adresa URL:

![Výběr koncového bodu](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-event-subscription-endpoint-selection.png)

Po zadání příslušné hodnoty klepněte na **tlačítko Potvrdit výběr**.

> [!IMPORTANT]
> Při každém spuštění *ngrok*, HTTPS URL je regenerována a hodnota se změní. Proto je nutné vytvořit nové předplatné událostí pokaždé, když vystavit svou funkci Azure přes *ngrok*.

## <a name="upload-a-file"></a>Nahrání souboru

Nyní můžete nahrát soubor do účtu úložiště a aktivovat událost Event Grid, kterou bude vaše místní funkce zpracovávat. 

Otevřete [Průzkumníka úložiště](https://azure.microsoft.com/features/storage-explorer/) a připojte se ke svému účtu úložiště. 

- Rozbalit **kontejnery objektů blob** 
- Klepněte pravým tlačítkem myši a vyberte **vytvořit kontejner objektů blob**.
- Pojmenujte **test** kontejneru
- Výběr *testovacího* kontejneru
- Klikněte na tlačítko **Nahrát.**
- Klikněte na **Nahrát soubory.**
- Vyberte soubor a nahrajte ho do kontejneru objektů blob.

## <a name="debug-the-function"></a>Ladění funkce

Jakmile event grid rozpozná nový soubor je odeslán do kontejneru úložiště, bod přerušení je přístupů v místní funkci.

![Spustit ngrok](./media/functions-debug-event-grid-trigger-local/functions-debug-event-grid-trigger-local-breakpoint.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li vyčistit prostředky vytvořené v tomto článku, odstraňte **testovací** kontejner v účtu úložiště.

## <a name="next-steps"></a>Další kroky

- [Automatizace změny velikosti nahraných obrázků s využitím služby Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Aktivační událost mřížky událostí pro funkce Azure](./functions-bindings-event-grid.md)

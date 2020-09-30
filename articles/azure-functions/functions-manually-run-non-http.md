---
title: Ruční spuštění neaktivovaného Azure Functions bez protokolu HTTP
description: Použití požadavku HTTP ke spuštění Azure Functions triggeru bez protokolu HTTP
author: craigshoemaker
ms.topic: article
ms.date: 04/23/2020
ms.author: cshoe
ms.openlocfilehash: 79aebf7ed80fea370ff7a5d5cc40911da4144414
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537697"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Ruční spuštění funkce neaktivované protokolem HTTP

Tento článek ukazuje, jak ručně spustit neaktivovanou funkci nezaloženou na protokolu HTTP prostřednictvím speciálně formátovaného požadavku HTTP.

V některých kontextech je možné, že budete muset spustit funkci Azure, která je nepřímo aktivovaná na vyžádání.  Příklady nepřímých triggerů zahrnují [funkce podle plánu](./functions-create-scheduled-function.md) nebo funkcí, které se spouštějí jako výsledek [Akce jiného prostředku](./functions-create-storage-blob-triggered-function.md). 

Odesílací [modul se používá](https://www.getpostman.com/) v následujícím [příkladu, ale](https://curl.haxx.se/)k odeslání požadavků HTTP můžete použít [Fiddler](https://www.telerik.com/fiddler) nebo jakýkoli jiný nástroj jako.

## <a name="define-the-request-location"></a>Definujte umístění žádosti.

Pokud chcete spustit funkci, která není aktivovaná přes protokol HTTP, potřebujete způsob, jak odeslat žádost do Azure ke spuštění funkce. Adresa URL použitá k provedení tohoto požadavku má určitý tvar.

![Zadejte umístění žádosti: název hostitele + cesta ke složce + název funkce](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Název hostitele:** Veřejné umístění aplikace Function App, které se skládá z názvu aplikace Function App a *azurewebsites.NET* nebo vaší vlastní domény.
- **Cesta ke složce:** Pokud chcete získat přístup k funkcím aktivovaným přes protokol HTTP prostřednictvím požadavku HTTP, je nutné odeslat žádost prostřednictvím složky *admin/Functions*.
- **Název funkce:** Název funkce, kterou chcete spustit.

Toto umístění požadavku použijete v poli post společně s hlavním klíčem funkce v žádosti do Azure ke spuštění funkce.

> [!NOTE]
> Při místním spuštění není hlavní klíč funkce vyžadován. Funkci můžete přímo [volat](#call-the-function) vynechání `x-functions-key` hlavičky.

## <a name="get-the-functions-master-key"></a>Získat hlavní klíč funkce

1. V [Azure Portal](https://portal.azure.com)přejděte do aplikace Function App, vyberte možnost **klíče aplikace**a pak `_master` klíč. 

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key.png" alt-text="Vyhledejte hlavní klíč ke zkopírování." border="true":::

1. V části **upravit klíč** Zkopírujte hodnotu klíče do schránky a pak vyberte **OK**.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-copy.png" alt-text="Vyhledejte hlavní klíč ke zkopírování." border="true":::

1. Po zkopírování *_Master* klíče vyberte **kód + test**a pak vyberte **protokoly**. Po ručním spuštění funkce z post se zobrazí zprávy z funkce, kterou jste si zaznamenali.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-function-log.png" alt-text="Vyhledejte hlavní klíč ke zkopírování." border="true":::

> [!CAUTION]  
> Vzhledem ke zvýšeným oprávněním v aplikaci Function App udělené hlavním klíčem byste tento klíč neměli sdílet s třetími stranami nebo ho distribuovat do aplikace. Klíč by měl být odeslán pouze do koncového bodu HTTPS.

## <a name="call-the-function"></a>Volání funkce

Otevřete post a postupujte takto:

1. **Do textového pole Adresa URL zadejte umístění žádosti**.
1. Zajistěte, aby byla metoda HTTP nastavena na **post**.
1. Vyberte kartu **hlavičky** .
1. Jako první klíč zadejte **x-Functions-Key** a vložte hlavní klíč (ze schránky) jako hodnotu.
1. Jako druhý klíč zadejte typ **Content-Type** a jako hodnotu zadejte **Application/JSON** .

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png" alt-text="Vyhledejte hlavní klíč ke zkopírování." border="true":::

1. Vyberte kartu **tělo** .
1. Zadejte **{"Input": "test"}** jako text pro požadavek.

    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png" alt-text="Vyhledejte hlavní klíč ke zkopírování." border="true":::

1. Vyberte **Poslat**.
        
    :::image type="content" source="./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png" alt-text="Vyhledejte hlavní klíč ke zkopírování." border="true":::

    Po vygenerování zprávy se zobrazí stav **202 přijato**.

1. Potom se vraťte k funkci v Azure Portal. Zkontrolujte protokoly a zobrazí se zprávy přicházející z ručního volání do funkce.

    :::image type="content" source="./media/functions-manually-run-non-http/azure-portal-functions-master-key-logs.png" alt-text="Vyhledejte hlavní klíč ke zkopírování." border="true":::

## <a name="next-steps"></a>Další kroky

- [Strategie testování kódu ve službě Azure Functions](./functions-test-a-function.md)
- [Funkce Azure Function Event Grid aktivovat místní ladění](./functions-debug-event-grid-trigger-local.md)

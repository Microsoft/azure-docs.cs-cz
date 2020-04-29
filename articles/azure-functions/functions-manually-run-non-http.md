---
title: Ruční spuštění neaktivovaného Azure Functions bez protokolu HTTP
description: Použití požadavku HTTP ke spuštění Azure Functions triggeru bez protokolu HTTP
author: craigshoemaker
ms.topic: article
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 6571482d738549d2708fd8ab23eaf8c9f6fb1f70
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80892355"
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

V Azure Portal přejděte na svou funkci a klikněte na **Správa** a vyhledejte část **klíče hostitele** . Kliknutím na tlačítko **Kopírovat** na řádku *_Master* zkopírujete hlavní klíč do schránky.

![Kopírovat hlavní klíč z obrazovky správy funkcí](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Po zkopírování hlavního klíče klikněte na název funkce a vraťte se do okna soubor kódu. Potom klikněte na kartu **protokoly** . Po ručním spuštění funkce z post se zobrazí zprávy z funkce, kterou jste si zaznamenali.

> [!CAUTION]  
> Vzhledem ke zvýšeným oprávněním v aplikaci Function App udělené hlavním klíčem byste tento klíč neměli sdílet s třetími stranami nebo ho distribuovat do aplikace.

## <a name="call-the-function"></a>Volání funkce

Otevřete post a postupujte takto:

1. **Do textového pole Adresa URL zadejte umístění žádosti**.
2. Zajistěte, aby byla metoda HTTP nastavena na **post**.
3. **Klikněte** na kartu **hlavičky** .
4. Jako první **klíč** zadejte **x-Functions-Key** a vložte hlavní klíč (ze schránky) do pole Value ( **hodnota** ).
5. Jako druhý **klíč** zadejte **typ Content** a jako **hodnotu**zadejte **Application/JSON** .

    ![Nastavení hlaviček pro publikování](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Klikněte** na kartu **tělo** .
7. Jako tělo žádosti zadejte **{"Input": "test"}** .

    ![Nastavení těla příspěvku](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Klikněte na **Odeslat**.

    ![Odeslání žádosti pomocí metody post](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Po vygenerování zprávy se zobrazí stav **202 přijato**.

Potom se vraťte k funkci v Azure Portal. Vyhledejte okno *protokoly* a zobrazí se zprávy přicházející z ručního volání do funkce.

![Výsledky protokolu funkce z ručního volání](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Další kroky

- [Strategie testování kódu ve službě Azure Functions](./functions-test-a-function.md)
- [Funkce Azure Function Event Grid aktivovat místní ladění](./functions-debug-event-grid-trigger-local.md)

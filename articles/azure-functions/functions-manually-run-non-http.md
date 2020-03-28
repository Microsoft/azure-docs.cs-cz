---
title: Ruční spuštění funkcí Azure s neaktivovanou protokolem HTTP
description: Spuštění funkcí Azure s neaktivoaktivoaktivovaných protokolem Azure
author: craigshoemaker
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 4ce7b8590e4718585fe841921466e049dc204928
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75769128"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Ruční spuštění funkce neaktivované protokolem HTTP

Tento článek ukazuje, jak ručně spustit funkci s pouštěnou protokolem HTTP prostřednictvím speciálně formátovaného požadavku HTTP.

V některých kontextech může být nutné spustit "na vyžádání" funkce Azure, která je nepřímo spuštěna.  Příklady nepřímých aktivačních událostí zahrnují [funkce podle plánu](./functions-create-scheduled-function.md) nebo funkce, které jsou spuštěny v důsledku akce [jiného zdroje](./functions-create-storage-blob-triggered-function.md). 

[Pošťák](https://www.getpostman.com/) se používá v následujícím příkladu, ale můžete použít [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) nebo jakýkoli jiný podobný nástroj k odesílání požadavků HTTP.

## <a name="define-the-request-location"></a>Definování umístění požadavku

Chcete-li spustit funkci s aktivací protokolu HTTP, potřebujete způsob, jak odeslat požadavek do Azure ke spuštění funkce. Adresa URL použitá k vytvoření tohoto požadavku má určitý formulář.

![Definovat umístění požadavku: název hostitele + cesta ke složce + název funkce](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Název hostitele:** Veřejné umístění aplikace funkce, které se skládá z názvu aplikace funkce a *azurewebsites.net* nebo vlastní doménou.
- **Cesta ke složce:** Chcete-li získat přístup k funkcím s neaktivovaným protokolem HTTP prostřednictvím požadavku HTTP, musíte odeslat požadavek prostřednictvím složek *admin/functions*.
- **Název funkce:** Název funkce, kterou chcete spustit.

Toto umístění požadavku v Postman spolu s hlavní klíč funkce v požadavku na Azure ke spuštění funkce.

> [!NOTE]
> Při místním spuštění není vyžadován hlavní klíč funkce. Můžete přímo [volat funkci](#call-the-function) vynechání `x-functions-key` záhlaví.

## <a name="get-the-functions-master-key"></a>Získání hlavního klíče funkce

Přejděte na svou funkci na webu Azure Portal a klikněte na **Spravovat** a najděte sekci **Klíče hostitele.** Kliknutím na tlačítko **Kopírovat** v *řádku _master* zkopírujte hlavní klíč do schránky.

![Kopírovat hlavní klíč z obrazovky Správa funkcí](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Po zkopírování hlavního klíče se kliknutím na název funkce vrátíte do okna souboru kódu. Dále klikněte na kartu **Protokoly.** Zobrazí se zprávy z funkce zaznamenané zde při ručním spuštění funkce z Postman.

> [!CAUTION]  
> Vzhledem ke zvýšeným oprávněním v aplikaci funkcí udělených hlavním klíčem byste neměli tento klíč sdílet s třetími stranami ani jej distribuovat v aplikaci.

## <a name="call-the-function"></a>Volání funkce

Otevřete pošťáka a postupujte takto:

1. Do **textového pole URL zadejte umístění požadavku**.
2. Ujistěte se, že je metoda HTTP nastavena na **post**.
3. **Klikněte** na kartu **Záhlaví.**
4. Zadejte **x-functions-key** jako první **klíč** a vložte hlavní klíč (ze schránky) do pole **hodnot.**
5. Jako druhý **klíč** zadejte **Typ obsahu** a jako **hodnotu**zadejte **application/json** .

    ![Nastavení záhlaví pošťáka](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

6. **Klikněte** na kartu **Tělo.**
7. Zadejte **{ "input": "test" }** jako tělo pro požadavek.

    ![Nastavení těla pošťáka](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

8. Klikněte na **Odeslat**.

    ![Odeslání žádosti s Pošťákem](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Pošťák pak hlásí stav **202 Přijato**.

Dále se vraťte k funkci na webu Azure Portal. Vyhledejte okno *Protokoly* a zobrazí se zprávy přicházející z ručního volání funkce.

![Výsledky protokolu funkcí z ručního volání](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Další kroky

- [Strategie testování kódu ve službě Azure Functions](./functions-test-a-function.md)
- [Mřížka událostí Azure aktivační událost místní ladění](./functions-debug-event-grid-trigger-local.md)

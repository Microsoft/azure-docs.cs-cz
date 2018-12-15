---
title: Ruční spuštění Azure Functions bez aktivovanou protokolem HTTP
description: Funkce Azure aktivované pomocí požadavku HTTP ke spuštění jiným protokolem než HTTP
services: functions
keywords: ''
author: craigshoemaker
manager: jeconnoc
ms.service: azure-functions
ms.topic: tutorial
ms.date: 12/12/2018
ms.author: cshoe
ms.openlocfilehash: 00a72c8c7fb42c763a8b0bad1fa3914ac27c496f
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53406926"
---
# <a name="manually-run-a-non-http-triggered-function"></a>Ruční spuštění funkce neaktivované protokolem HTTP

Tento článek ukazuje, jak ručně spustit jiných funkci aktivovanou protokolem HTTP pomocí speciálně formátovaného požadavku HTTP.

V některých kontextech, budete muset spouštění "na vyžádání" funkce Azure, která je nepřímo aktivována.  Příklady nepřímé triggery [funkcí v plánu](./functions-create-scheduled-function.md) nebo funkce, které spouští jako výsledek [jiný prostředek akce](./functions-create-storage-blob-triggered-function.md). 

[Postman](https://www.getpostman.com/) se používá v následujícím příkladu, ale můžete použít [cURL](https://curl.haxx.se/), [Fiddler](https://www.telerik.com/fiddler) nebo jakékoli jiné například nástroj pro odesílání požadavků HTTP.

## <a name="define-the-request-location"></a>Definujte žádost o umístění

Ke spuštění jiných funkci aktivovanou protokolem HTTP, potřebujete způsob, jak odeslat žádost o Azure ke spuštění funkce. Adresa URL sloužící k této žádosti trvá konkrétní tvar.

![Definujte umístění žádosti: název a cestu ke složce + funkce hostování název](./media/functions-manually-run-non-http/azure-functions-admin-url-anatomy.png)

- **Název hostitele:** Aplikace function app veřejné umístění, která se skládá z názvu aplikace function app, plus *azurewebsites.net* nebo vaší vlastní doméně.
- **Cesta ke složce:** Pro přístup k jiné funkce aktivované protokolem HTTP pomocí požadavku HTTP, budete muset odeslat žádost ve složkách *správce/funkce*.
- **Název funkce:** Název funkce, do které chcete spustit.

Tato žádost o umístění v nástroji Postman spolu s hlavní klíč funkce v žádosti o Azure použijete ke spuštění funkce.

## <a name="get-the-functions-master-key"></a>Získat funkce hlavní klíč

Přejděte do funkce na webu Azure Portal a klikněte na **spravovat** a najít **klíče hostitele** oddílu. Klikněte na **kopírování** tlačítko *_hlavní* řádku, který má hlavní klíč zkopírujte do schránky.

![Zkopírujte hlavní klíč z obrazovky Management – funkce](./media/functions-manually-run-non-http/azure-portal-functions-master-key.png)

Po zkopírování hlavního klíče, klikněte na název funkce se vraťte do okna soubor kódu. Potom kliknutím na **protokoly** kartu. Zobrazí zprávy z funkce tady protokolují, když ručně spustíte funkci z Postman.

> [!CAUTION]  
> Z důvodu vyšší úroveň oprávnění v aplikaci function app udělit pomocí hlavního klíče by neměly sdílet tento klíč s třetími stranami nebo distribuovat v aplikaci.

## <a name="call-the-function"></a>Volání funkce

Otevřete nástroj Postman a postupujte podle těchto kroků:

1. Zadejte **žádost o umístění v textovém poli Adresa URL**. 
2. **Klikněte na tlačítko** na **záhlaví** kartu.
3. Zadejte **x-functions-key** jako první **klíč** a vložte do hlavního klíče (ze schránky) **hodnotu** pole.
4. Zadejte **Content-Type** jako druhý **klíč** a zadejte **application/json** jako **hodnota**.

    ![Nastavení nástroje postman záhlaví](./media/functions-manually-run-non-http/functions-manually-run-non-http-headers.png)

5. **Klikněte na tlačítko** na **tělo** kartu.
6. Zadejte **{"vstup": "test"}** jako text žádosti.

    ![Nastavení nástroje postman textu](./media/functions-manually-run-non-http/functions-manually-run-non-http-body.png)

7. Klikněte na tlačítko **odeslat**.

    ![Posílá se žádost o pomocí nástroje Postman](./media/functions-manually-run-non-http/functions-manually-run-non-http-send.png)

Postman hlásí stav **202 přijato**.

Pak se vraťte do funkce na webu Azure Portal. Vyhledejte *protokoly* okno a uvidíte zprávy přicházející z ručního volání funkce.

![Funkce protokolu způsobují to ruční zavolání metody](./media/functions-manually-run-non-http/azure-portal-function-log.png)

## <a name="next-steps"></a>Další postup

- [Strategie pro testování kódu ve službě Azure Functions](./functions-test-a-function.md)
- [Funkce Azure Event Grid aktivovat místní ladění](./functions-debug-event-grid-trigger-local.md)

---
title: 'Rychlý Start: vytvoření indexu vyhledávání pomocí rozhraní REST API'
titleSuffix: Azure Cognitive Search
description: V tomto rychlém startu REST API se dozvíte, jak volat rozhraní REST API Azure Kognitivní hledání pomocí post nebo Visual Studio Code.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714153"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Rychlý Start: vytvoření indexu služby Azure Kognitivní hledání pomocí rozhraní REST API

Tento článek vysvětluje, jak pomocí [rozhraní REST API služby Azure kognitivní hledání](/rest/api/searchservice) a klienta API pro posílání a přijímání požadavků formulovat požadavky na REST API. Pomocí klienta rozhraní API a těchto pokynů můžete odesílat žádosti a zobrazovat odpovědi před zápisem kódu.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem. 

## <a name="next-steps"></a>Další kroky

Když teď víte, jak provádět základní úlohy, můžete přejít vpřed pomocí dalších REST API volání pro pokročilejší funkce, jako jsou indexery nebo [Nastavení kanálu pro rozšíření](cognitive-search-tutorial-blob.md) , který přidává transformace obsahu k indexování. V dalším kroku doporučujeme následující odkaz:

> [!div class="nextstepaction"]
> [Kurz: použití REST a AI k vygenerování prohledávatelných obsahu z objektů blob Azure](cognitive-search-tutorial-blob.md)
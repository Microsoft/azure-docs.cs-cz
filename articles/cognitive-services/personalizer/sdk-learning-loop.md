---
title: 'Úvodní příručka: Vytvoření a použití výukové smyčky pomocí sady SDK – personalizátor'
description: Tento rychlý start ukazuje, jak vytvořit a spravovat znalostní bázi pomocí sady SDK klienta.
ms.topic: quickstart
ms.date: 01/15/2020
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 7ebe22227b4323b2e6b1c3fc9ca31e171d1d97cd
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77524865"
---
# <a name="quickstart-personalizer-client-library"></a>Úvodní příručka: Klientská knihovna personalisty

Zobrazení přizpůsobeného obsahu v tomto rychlém startu pomocí služby Personalizace.

Začínáme s klientskou knihovnou Personalista. Následujícím postupem nainstalujte balíček a vyzkoušejte ukázkový kód pro základní úkoly.

 * Rozhraní API pořadí – vybere nejlepší položku z položek obsahu na základě informací o obsahu a kontextu v reálném čase.
 * Bonus API - Určíte skóre odměny na základě vašich obchodních potřeb a pak je odešlete personalistovi s tímto rozhraním API. Toto skóre může být jedna hodnota, například 1 pro dobré a 0 pro špatné nebo algoritmus, který vytvoříte na základě vašich obchodních potřeb.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit a odebrat předplatné služeb Cognitive Services, můžete odstranit prostředek nebo skupinu prostředků. Odstraněním skupiny prostředků také odstraníte všechny další prostředky, které jsou s ní spojené.

* [Portál](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Jak služba Personalizace funguje](how-personalizer-works.md)

* [Co je služba Personalizace?](what-is-personalizer.md)
* [Kde se dá služba Personalizace využít?](where-can-you-use-personalizer.md)
* [Řešení potíží](troubleshooting.md)
* Zdrojový kód pro tuto ukázku lze nalézt na [GitHubu](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).

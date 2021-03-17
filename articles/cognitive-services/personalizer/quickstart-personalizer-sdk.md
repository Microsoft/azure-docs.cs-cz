---
title: 'Rychlý Start: vytvoření a použití výukového cyklu pomocí sady SDK – přizpůsobování'
description: V tomto rychlém startu se dozvíte, jak vytvořit a spravovat znalostní bázi pomocí klientské knihovny pro přizpůsobování.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: quickstart
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: přizpůsobování, přizpůsobování v Azure, Machine Learning
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 3acfd8134a2a65e6ea9f019baff24b8b8149f7f3
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91777319"
---
# <a name="quickstart-personalizer-client-library"></a>Rychlý Start: Klientská knihovna pro přizpůsobování

Zobrazit přizpůsobený obsah v tomto rychlém startu pomocí služby pro přizpůsobení.

Začínáme s klientskou knihovnou pro přizpůsobování Pomocí těchto kroků nainstalujete balíček a vyzkoušíte ukázkový kód pro základní úlohy.

 * Rozhraní API pro řazení – vybere nejlepší položku z akcí na základě informací v reálném čase, které poskytnete o obsahu a kontextu.
 * API pro odměnu – na základě vašich obchodních potřeb určíte skóre odměňování a pak ho pošlete do přizpůsobeného rozhraní API. Toto skóre může být jedna hodnota, například 1 pro dobrý, a 0 pro špatný nebo algoritmus, který vytvoříte na základě vašich obchodních potřeb.

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

Pokud chcete vyčistit a odebrat předplatné Cognitive Services, můžete prostředek nebo skupinu prostředků odstranit. Odstraněním skupiny prostředků se odstraní také všechny další prostředky, které jsou k ní přidružené.

* [Azure Portal](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Jak přizpůsobovat práci](how-personalizer-works.md) 
>  [Kde používat přizpůsobeného přizpůsobování?](where-can-you-use-personalizer.md)

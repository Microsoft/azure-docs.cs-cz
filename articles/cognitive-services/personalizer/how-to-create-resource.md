---
title: Vytvořit prostředek personalizátoru
description: Konfigurace služby zahrnuje, jak služba zachází s odměnami, jak často služba zkoumá, jak často je model přeškolen a kolik dat je uloženo.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: adb97db53d1fc0b6f0cdb14b697c82ec52501b84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336064"
---
# <a name="create-a-personalizer-resource"></a>Vytvoření prostředku personalikátora

Personalizátor zdroj je totéž jako personalizace učení smyčky. Pro každou doménu předmětu nebo oblast obsahu, kterou máte, je vytvořen jeden prostředek nebo učební smyčka. Nepoužívejte více oblastí obsahu ve stejné smyčce, protože to bude zmást učení smyčky a poskytují špatné předpovědi.

Pokud chcete, aby personalizátor vybral nejlepší obsah pro více než jednu oblast obsahu webové stránky, použijte pro každou z nich jinou učební smyčku.


## <a name="create-a-resource-in-the-azure-portal"></a>Vytvoření prostředku na webu Azure Portal

Vytvořte prostředek personalikátorpro každou zpětnou vazbu.

1. Přihlaste se k [portálu Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Předchozí odkaz přejdete na stránku **Vytvořit** pro službu Personalizátor.
1. Zadejte název služby, vyberte předplatné, umístění, cenovou úroveň a skupinu prostředků.

    > [!div class="mx-imgBorder"]
    > ![Pomocí portálu Azure vytvořte prostředek personalisty, který se také nazývá učební smyčka.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Chcete-li zdroj vytvořit, vyberte **vytvořit.**

1. Po nasazení prostředku vyberte tlačítko **Přejít na prostředek** a přejděte na prostředek personalikátoru.

1. Vyberte stránku **Rychlý start** pro váš prostředek a zkopírujte hodnoty pro koncový bod a klíč. Chcete-li používat api pro hodnocení a odměny, potřebujete koncový bod prostředku i klíč.

1. Vyberte stránku **Konfigurace** pro nový prostředek pro [konfiguraci výukové smyčky](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Vytvoření prostředku pomocí příkazového příkazového příkazu k Řešení<

1. Přihlaste se k azure cli pomocí následujícího příkazu:

    ```azurecli-interactive
    az login
    ```

1. Vytvořte skupinu prostředků, logické seskupení pro správu všech prostředků Azure, které chcete použít s prostředek personalizace.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Vytvořte nový prostředek _personalizátoru, výukové smyčky_, s následujícím příkazem pro existující skupinu prostředků.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Vrátí to objekt JSON, který zahrnuje **koncový bod prostředku**.

1. Pomocí následujícího příkazu Azure CLI získejte **klíč prostředku**.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Chcete-li používat api pro hodnocení a odměny, potřebujete koncový bod prostředku i klíč.

## <a name="next-steps"></a>Další kroky

* [Konfigurace](how-to-settings.md) Evidenční vzdělávací smyčka
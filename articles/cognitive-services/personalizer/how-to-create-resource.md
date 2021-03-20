---
title: Vytvořit prostředek přizpůsobeného pro přizpůsobování
description: V tomto článku se dozvíte, jak vytvořit prostředek pro přizpůsobení v Azure Portal pro každou smyčku zpětné vazby.
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 03/26/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0c75d917f1abe72af2f4aa56b0f67dbb7bcd24a1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91303552"
---
# <a name="create-a-personalizer-resource"></a>Vytvoření prostředku pro přizpůsobování

Prostředek přizpůsobeného přizpůsobování je stejný jako smyčka učení pro přizpůsobení. Pro každou doménu subjektu nebo oblast obsahu, kterou máte, se vytvoří jeden prostředek nebo smyčka učení. Nepoužívejte ve stejné smyčce více oblastí obsahu, protože to Zaměňujte výukovou smyčku a zajistěte špatnou předpovědi.

Pokud chcete, aby přidaný obsah vybral pro více než jednu oblast obsahu webové stránky, můžete pro každý z nich použít jinou výukovou smyčku.


## <a name="create-a-resource-in-the-azure-portal"></a>Vytvoření prostředku na portálu Azure Portal

Vytvořte prostředek pro přizpůsobení pro každou smyčku zpětné vazby.

1. Přihlaste se k [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Předchozí odkaz vás přesměruje na stránku pro **Vytvoření** služby pro přizpůsobení.
1. Zadejte název služby, vyberte předplatné, umístění, cenovou úroveň a skupinu prostředků.

    > [!div class="mx-imgBorder"]
    > ![Použijte Azure Portal k vytvoření prostředku přizpůsobeného nástroji, označovaného také jako výuková smyčka.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Vyberte **vytvořit** a vytvořte prostředek.

1. Po nasazení prostředku kliknutím na tlačítko **Přejít k prostředku** přejdete na prostředek pro přizpůsobení.

1. Vyberte pro svůj prostředek stránku **rychlý Start** a pak zkopírujte hodnoty pro svůj koncový bod a klíč. Pro použití rozhraní API pro řazení a měnu potřebujete jak koncový bod prostředku, tak i klíč.

1. Vyberte stránku **Konfigurace** pro nový prostředek a [nakonfigurujte smyčku učení](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Vytvoření prostředku pomocí Azure CLI

1. Přihlaste se k Azure CLI pomocí následujícího příkazu:

    ```azurecli-interactive
    az login
    ```

1. Vytvořte skupinu prostředků, logické seskupení pro správu všech prostředků Azure, které máte v úmyslu používat s prostředkem přizpůsobeného.


    ```azurecli-interactive
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Vytvořte nový prostředek pro přizpůsobování, _výukovou smyčku_, pomocí následujícího příkazu pro existující skupinu prostředků.

    ```azurecli-interactive
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```

    Tím se vrátí objekt JSON, který obsahuje váš **koncový bod prostředku**.

1. Pomocí následujícího příkazu rozhraní příkazového řádku Azure Získejte **klíč prostředku**.

    ```azurecli-interactive
        az cognitiveservices account keys list \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group
    ```

    Pro použití rozhraní API pro řazení a měnu potřebujete jak koncový bod prostředku, tak i klíč.

## <a name="next-steps"></a>Další kroky

* [Konfigurace](how-to-settings.md) Výuková smyčka pro přizpůsobení

---
title: Vytvořit prostředek přizpůsobeného pro přizpůsobování
description: Konfigurace služby zahrnuje způsob, jakým služba zpracovává ceny, jak často se služba zkoumá, jak často se model překládá a kolik dat se ukládá.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: cb14415f3a5950ad1534d9eb8da94198a41f4f91
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624228"
---
# <a name="create-a-personalizer-resource"></a>Vytvoření prostředku pro přizpůsobování

Prostředek přizpůsobeného přizpůsobování je stejný jako smyčka učení pro přizpůsobení. Pro každou doménu subjektu nebo oblast obsahu, kterou máte, se vytvoří jeden prostředek nebo smyčka učení. Nepoužívejte ve stejné smyčce více oblastí obsahu, protože to Zaměňujte výukovou smyčku a zajistěte špatnou předpovědi.

Pokud chcete, aby přidaný obsah vybral pro více než jednu oblast obsahu webové stránky, můžete pro každý z nich použít jinou výukovou smyčku.


## <a name="create-a-resource-in-the-azure-portal"></a>Vytvoření prostředku v Azure Portal

Vytvořte prostředek pro přizpůsobení pro každou smyčku zpětné vazby.

1. Přihlaste se k [portálu Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). Předchozí odkaz vás přesměruje na stránku pro **Vytvoření** služby pro přizpůsobení.
1. Zadejte název služby, vyberte předplatné, umístění, cenovou úroveň a skupinu prostředků.

    > [!div class="mx-imgBorder"]
    > ![použít Azure Portal k vytvoření prostředku pro přizpůsobení, označovaného také jako výuková smyčka.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Vyberte **vytvořit** a vytvořte prostředek.

1. Po nasazení prostředku kliknutím na tlačítko **Přejít k prostředku** přejdete na prostředek pro přizpůsobení. Chcete-li [Konfigurovat výukovou smyčku](how-to-settings.md), klikněte na stránku **Konfigurace** nového prostředku.

## <a name="create-a-resource-with-the-azure-cli"></a>Vytvoření prostředku pomocí Azure CLI

1. Přihlaste se k Azure CLI pomocí následujícího příkazu:

    ```bash
    az login
    ```

1. Vytvořte skupinu prostředků, logické seskupení pro správu všech prostředků Azure, které máte v úmyslu používat s prostředkem přizpůsobeného.


    ```bash
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Vytvořte nový prostředek pro přizpůsobování, _výukovou smyčku_, pomocí následujícího příkazu pro existující skupinu prostředků.

    ```bash
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```
## <a name="next-steps"></a>Další kroky

* [Konfigurace](how-to-settings.md) Výuková smyčka pro přizpůsobení
---
title: Kurz – publikování verzí rozhraní API pomocí Azure API Management
description: Pomocí kroků v tomto kurzu se dozvíte, jak publikovat více verzí rozhraní API v API Management.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/30/2020
ms.author: apimpm
ms.openlocfilehash: e6afa26c65f097683a5b471dc34621cca38c01e6
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377370"
---
# <a name="tutorial-publish-multiple-versions-of-your-api"></a>Kurz: publikování několika verzí vašeho rozhraní API 

Existují situace, kdy je nepraktické, aby všichni volající na rozhraní API používali přesně stejnou verzi. Když volající chtějí upgradovat na novější verzi, chtějí mít přístup, který je snadno srozumitelný. Jak je znázorněno v tomto kurzu, je možné v Azure API Management poskytnout více *verzí* . 

V případě pozadí si přečtěte téma [verze & revize](https://azure.microsoft.com/blog/versions-revisions/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání nové verze stávajícího rozhraní API
> * Výběr schématu verze
> * Přidání verze do produktu
> * Zobrazení verze na portálu pro vývojáře

:::image type="content" source="media/api-management-getstarted-publish-versions/azure-portal.png" alt-text="Verze zobrazená v Azure Portal":::

## <a name="prerequisites"></a>Předpoklady

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Dokončete následující rychlý Start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="add-a-new-version"></a>Přidání nové verze

1. V [Azure Portal](https://portal.azure.com)přejděte k instanci API Management.
1. Vyberte **Rozhraní API**.
1. V seznamu rozhraní API vyberte rozhraní **Demo Conference API**. 
1. Vyberte kontextovou nabídku ( **...** ) vedle pole **demo konferenčního rozhraní API**.
1. Vyberte **Přidat verzi**.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version-menu.png" alt-text="Místní nabídka rozhraní API – přidat verzi":::


> [!TIP]
> Verze je možné povolit i při vytváření nového rozhraní API. Na obrazovce **Přidat rozhraní API** vyberte možnost **verze tohoto rozhraní API?**.

## <a name="choose-a-versioning-scheme"></a>Výběr schématu vytváření verzí

V Azure API Management zvolíte způsob, jakým volající určuje verzi rozhraní API, výběrem *schématu správy verzí* : **cesta, záhlaví** nebo **řetězec dotazu**. V následujícím příkladu se jako schéma správy verzí používá *cesta* .

Zadejte hodnoty z následující tabulky. Pak vyberte **vytvořit** a vytvořte svou verzi.

:::image type="content" source="media/api-management-getstarted-publish-versions/add-version.png" alt-text="Přidat verzi – okno":::



|Nastavení   |Hodnota  |Popis  |
|---------|---------|---------|
|**Název**     |  *Ukázka – konferenční rozhraní API – v1*       |  Jedinečný název v instanci API Management.<br/><br/>Vzhledem k tomu, že verze je ve skutečnosti nové rozhraní API založené na [revizi](api-management-get-started-revise-api.md)rozhraní API, toto nastavení je nový název rozhraní API.   |
|**Schéma správy verzí**     |  **Cesta**       |  Způsob, jakým volající určí verzi rozhraní API.     |
|**Identifikátorem verze**     |  *V1*       |  Indikátor verze konkrétního schématu. Pro **cestu** je přípona cesty URL rozhraní API. <br/><br/> Pokud je vybraná možnost **záhlaví** nebo **řetězec dotazu** , zadejte další hodnotu: název záhlaví nebo parametr řetězce dotazu.<br/><br/> Zobrazí se příklad použití.        |
|**Produkty**     |  **Unlimited**       |  Volitelně jeden nebo více produktů, ke kterým je přiřazena verze rozhraní API. Pokud chcete rozhraní API publikovat, musíte ho přidružit k produktu. [Verzi produktu](#add-the-version-to-a-product) můžete také přidat později.      |

Po vytvoření verze se teď v seznamu rozhraní API zobrazí pod **ukázkovou konferenční místností API** . Teď vidíte dvě rozhraní API: **původní** a **v1**.

![Verze uvedené pod rozhraním API na webu Azure Portal](media/api-management-getstarted-publish-versions/version-list.png)

Nyní můžete upravit a nakonfigurovat **v1** jako rozhraní API, které je oddělené od **původního**. Změny jedné verze nemají vliv na jinou verzi.

> [!Note]
> Pokud přidáte verzi do rozhraní API bez verze, bude automaticky vytvořen i **původní** . Tato verze reaguje na výchozí adresu URL. Vytvořením původní verze se zajistí, že proces přidávání verze neruší žádné existující volající. Pokud vytvoříte nové rozhraní API s verzemi, které jsou na začátku povolené, původní verze se nevytvoří.

## <a name="add-the-version-to-a-product"></a>Přidání verze do produktu

Aby se volajícím zobrazila nová verze, musí se přidat do *produktu*. Pokud jste ještě nepřidali verzi k produktu, můžete ji kdykoli přidat do produktu.

Například pro přidání verze do **neomezeného** produktu:
1. V Azure Portal přejděte k instanci API Management.
1. Vyberte možnost **produkty**  >  **neomezené**  >  **rozhraní API**  >  **+ Přidat**.
1. Vyberte **ukázková konferenční konference API** verze **v1**.
1. Klikněte na **Vybrat**.

:::image type="content" source="media/api-management-getstarted-publish-versions/08-add-multiple-versions-03-add-version-product.png" alt-text="Přidat verzi k produktu":::

## <a name="browse-the-developer-portal-to-see-the-version"></a>Zobrazení verze na portálu pro vývojáře

Pokud jste si vyzkoušeli [portál pro vývojáře](api-management-howto-developer-portal-customize.md), můžete zobrazit verze rozhraní API.

1. V horní nabídce vyberte **Portál pro vývojáře**.
2. Vyberte **Rozhraní API** a pak rozhraní **Demo Conference API**.
3. Vedle názvu rozhraní API byste měli vidět rozevírací seznam s více verzemi.
4. Vyberte **v1**.
5. Všimněte si **adresy URL požadavku** první operace na seznamu. Ukazuje, že cesta URL rozhraní API zahrnuje **v1**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání nové verze stávajícího rozhraní API
> * Výběr schématu verze 
> * Přidání verze do produktu
> * Zobrazení verze na portálu pro vývojáře

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Přizpůsobení stylu stránek portálu pro vývojáře](api-management-customize-styles.md)

---
title: Kurz – vytvoření a publikování produktu v Azure API Management
description: V tomto kurzu vytvoříte a publikujete produkt v Azure API Management. Po publikování můžou vývojáři začít používat rozhraní API produktu.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 2f298f240d8aa7a38b42a8c78ee3c90fe3423d10
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91630550"
---
# <a name="tutorial-create-and-publish-a-product"></a>Kurz: vytvoření a publikování produktu  

[*Produkt*](api-management-terminology.md#term-definitions) v Azure API Management obsahuje jednu nebo více rozhraní API a také kvótu využití a podmínek použití. Jakmile je projekt publikovaný, vývojáři se můžou přihlásit k jeho odběru a začít používat jeho rozhraní API.  

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření a publikování produktu
> * Přidání rozhraní API do produktu

:::image type="content" source="media/api-management-howto-add-products/added-product.png" alt-text="API Management produktů na portálu":::


## <a name="prerequisites"></a>Požadavky

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Dokončete následující rychlý Start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="create-and-publish-a-product"></a>Vytvoření a publikování produktu

1. Přihlaste se k Azure Portal a přejděte k instanci API Management.
1. V levém navigačním panelu vyberte **produkty**  >  **+ Přidat**.
1.  V okně **Přidat produkt** zadejte hodnoty popsané v následující tabulce pro vytvoření produktu.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="API Management produktů na portálu":::

    | Název                     | Popis                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Zobrazované jméno             | Název, který se má zobrazit na [portálu pro vývojáře](api-management-howto-developer-portal.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Popis              | Poskytněte informace o produktu, jako je jeho účel, rozhraní API, ke kterým poskytuje přístup, a další podrobnosti.                                                                                                                                               |
    | Stav                    | Vyberte možnost **Publikováno** , pokud chcete produkt publikovat. Předtím, než bude možné volat rozhraní API v produktu, je nutné produkt publikovat. Ve výchozím nastavení jsou nové produkty publikovány a jsou viditelné pouze pro skupinu  **správců** .                                                                                      |
    | Vyžaduje předplatné    | Vyberte, jestli se uživatel musí přihlásit k odběru produktu.                                                                                                                                                                                                                                   |
    | Vyžaduje schválení        | Tuto možnost vyberte, pokud chcete, aby správce zkontroloval a přijal nebo odmítl pokusy o přihlášení k tomuto produktu. Pokud není vybraná, pokusy o přihlášení k odběru se automaticky schvalují.                                                                                                                         |
    | Omezení počtu předplatných | Volitelně můžete omezit počet více souběžných předplatných,.                                                                                                                                                                                                                                |
    | Právní podmínky              | Pro produkt můžete zahrnout podmínky použití, které musí předplatitelé přijmout, aby mohli produkt využívat.                                                                                                                                                                                                             |
    | Rozhraní API                     | Vyberte jedno nebo více rozhraní API. Po vytvoření produktu můžete také přidat rozhraní API. Další informace najdete v části [Přidání rozhraní API k produktu](#add-apis-to-a-product) dále v tomto článku. |

3. Vyberte **vytvořit** a vytvořte nový produkt.

### <a name="add-more-configurations"></a>Přidání více konfigurací

Pokračovat v konfiguraci produktu po jeho uložení. V API Management instanci vyberte produkt z okna **produkty** . Přidat nebo aktualizovat:


|Položka   |Popis  |
|---------|---------|
|Nastavení     |    Metadata a stav produktu     |
|Rozhraní API     |  Rozhraní API přidružená k produktu       |
|[Zásady](api-management-howto-policies.md)     |  Zásady použité pro rozhraní API produktu      |
|Řízení přístupu     |  Viditelnost produktů pro vývojáře nebo hosty       |
|[Předplatná](api-management-subscriptions.md)    |    Předplatitelé produktů     |

## <a name="add-apis-to-a-product"></a>Přidání rozhraní API do produktu

Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Během vytváření produktu můžete přidat jedno nebo několik existujících rozhraní API. Rozhraní API můžete do produktu přidat také později, a to buď na stránce **Nastavení** produktů, nebo při vytváření rozhraní API.

Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci služby APIM, již jste správcem, takže jste ve výchozím nastavení přihlášeni k odběru všech produktů.

### <a name="add-an-api-to-an-existing-product"></a>Přidání rozhraní API do existujícího produktu


1. V levém navigačním panelu instance API Management vyberte **produkty**.
1. Vyberte produkt a pak vyberte **rozhraní API**.
1. Vyberte **+ Přidat**.
1. Vyberte jedno nebo více rozhraní API a pak **Vyberte**.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="API Management produktů na portálu":::

> [!TIP]
> Můžete vytvořit nebo aktualizovat předplatné uživatele na produkt s vlastními klíči předplatného pomocí příkazu [REST API](/rest/api/apimanagement/2019-12-01/subscription/createorupdate) nebo PowerShellu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření a publikování produktu
> * Přidání rozhraní API do produktu

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Vytvoření prázdného rozhraní API a napodobení odpovědí rozhraní API](mock-api-responses.md)

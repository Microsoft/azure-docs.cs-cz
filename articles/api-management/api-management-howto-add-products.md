---
title: Kurz – vytvoření a publikování produktu v Azure API Management
description: V tomto kurzu vytvoříte a publikujete produkt v Azure API Management. Po publikování můžou vývojáři začít používat rozhraní API produktu.
author: mikebudzynski
ms.service: api-management
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: d0420b92fc94e0a1a9c8a4057f419a57a9909223
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100545152"
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

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Přihlaste se k Azure Portal a přejděte k instanci API Management.
1. V levém navigačním panelu vyberte **produkty**  >  **+ Přidat**.
1.  V okně **Přidat produkt** zadejte hodnoty popsané v následující tabulce pro vytvoření produktu.

    :::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-01.png" alt-text="Přidat produkt na portálu":::

    | Název                     | Popis                                                                                                                                                                                                                                                                                                             |
    |--------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Zobrazované jméno             | Název, který se má zobrazit na [portálu pro vývojáře](api-management-howto-developer-portal.md)                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Popis              | Poskytněte informace o produktu, jako je jeho účel, rozhraní API, ke kterým poskytuje přístup, a další podrobnosti.                                                                                                                                               |
    | State                    | Vyberte možnost **Publikováno** , pokud chcete produkt publikovat. Předtím, než bude možné volat rozhraní API v produktu, je nutné produkt publikovat. Ve výchozím nastavení jsou nové produkty publikovány a jsou viditelné pouze pro skupinu  **správců** .                                                                                      |
    | Vyžaduje předplatné    | Vyberte, jestli se uživatel musí přihlásit k odběru produktu.                                                                                                                                                                                                                                   |
    | Vyžaduje schválení        | Tuto možnost vyberte, pokud chcete, aby správce zkontroloval a přijal nebo odmítl pokusy o přihlášení k tomuto produktu. Pokud není vybraná, pokusy o přihlášení k odběru se automaticky schvalují.                                                                                                                         |
    | Omezení počtu předplatných | Volitelně můžete omezit počet více souběžných předplatných.                                                                                                                                                                                                                                |
    | Právní podmínky              | Pro produkt můžete zahrnout podmínky použití, které musí předplatitelé přijmout, aby mohli produkt využívat.                                                                                                                                                                                                             |
    | Rozhraní API                     | Vyberte jedno nebo více rozhraní API. Po vytvoření produktu můžete také přidat rozhraní API. Další informace najdete v části [Přidání rozhraní API k produktu](#add-apis-to-a-product) dále v tomto článku. |

3. Vyberte **vytvořit** a vytvořte nový produkt.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li začít používat rozhraní příkazového řádku Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Pokud chcete vytvořit produkt, spusťte příkaz [AZ APIM Product Create](/cli/azure/apim/product#az_apim_product_create) :

```azurecli
az apim product create --resource-group apim-hello-word-resource-group \
    --product-name "Contoso product" --product-id contoso-product \
    --service-name apim-hello-world --subscription-required true \
    --state published --description "This is a test."
```

Pro svůj produkt můžete zadat různé hodnoty:

   | Parametr | Popis |
   |-----------|-------------|
   | `--product-name` | Název, který se má zobrazit na [portálu pro vývojáře](api-management-howto-developer-portal.md) |
   | `--description`  | Poskytněte informace o produktu, jako je jeho účel, rozhraní API, ke kterým poskytuje přístup, a další podrobnosti. |
   | `--state`        | Vyberte možnost **Publikováno** , pokud chcete produkt publikovat. Předtím, než bude možné volat rozhraní API v produktu, je nutné produkt publikovat. Ve výchozím nastavení jsou nové produkty publikovány a jsou viditelné pouze pro skupinu  **správců** . |
   | `--subscription-required` | Vyberte, jestli se uživatel musí přihlásit k odběru produktu. |
   | `--approval-required` | Tuto možnost vyberte, pokud chcete, aby správce zkontroloval a přijal nebo odmítl pokusy o přihlášení k tomuto produktu. Pokud není vybraná, pokusy o přihlášení k odběru se automaticky schvalují. |
   | `--subscriptions-limit` | Volitelně můžete omezit počet více souběžných předplatných.|
   | `--legal-terms`         | Pro produkt můžete zahrnout podmínky použití, které musí předplatitelé přijmout, aby mohli produkt využívat. |

Aktuální produkty zobrazíte pomocí příkazu [AZ APIM Product list](/cli/azure/apim/product#az_apim_product_list) :

```azurecli
az apim product list --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --output table
```

Produkt můžete odstranit pomocí příkazu [AZ APIM Product Delete](/cli/azure/apim/product#az_apim_product_delete) :

```azurecli
az apim product delete --product-id contoso-product \
    --resource-group apim-hello-word-resource-group \
    --service-name apim-hello-world --delete-subscriptions true
```

---

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

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. V levém navigačním panelu instance API Management vyberte **produkty**.
1. Vyberte produkt a pak vyberte **rozhraní API**.
1. Vyberte **+ Přidat**.
1. Vyberte jedno nebo více rozhraní API a pak **Vyberte**.

:::image type="content" source="media/api-management-howto-add-products/02-create-publish-product-02.png" alt-text="Přidat rozhraní API do existujícího produktu":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Pokud chcete zobrazit spravovaná rozhraní API, použijte příkaz [AZ APIM API list](/cli/azure/apim/api#az_apim_api_list) :

   ```azurecli
   az apim api list --resource-group apim-hello-word-resource-group \
       --service-name apim-hello-world --output table
   ```

1. Pokud chcete do svého produktu přidat rozhraní API, spusťte příkaz [AZ APIM Product API Add](/cli/azure/apim/product/api#az_apim_product_api_add) :

   ```azurecli
   az apim product api add --resource-group apim-hello-word-resource-group \
       --api-id demo-conference-api --product-id contoso-product \
       --service-name apim-hello-world
   ```

1. Pomocí příkazu [AZ APIM Product API list](/cli/azure/apim/product/api#az_apim_product_api_list) ověřte přidání:

   ```azurecli
   az apim product api list --resource-group apim-hello-word-resource-group \
       --product-id contoso-product --service-name apim-hello-world --output table
   ```

Rozhraní API můžete z produktu odebrat pomocí příkazu [AZ APIM Product API Delete](/cli/azure/apim/product/api#az_apim_product_api_delete) :

```azurecli
az apim product api delete --resource-group apim-hello-word-resource-group \
    --api-id demo-conference-api --product-id contoso-product \
    --service-name apim-hello-world
```

---

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

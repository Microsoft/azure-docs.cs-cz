---
title: Kurz – špičkové odezvy rozhraní API v API Management-Azure Portal | Microsoft Docs
description: V tomto kurzu použijete API Management k nastavení zásad pro rozhraní API tak, aby vracelo napodobnou odpověď, pokud není k dispozici back-end, který by odesílal skutečné odpovědi.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 02/09/2021
ms.author: apimpm
ms.openlocfilehash: 75727d139242e1b537505d2ed907ae20fc5479f8
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547235"
---
# <a name="tutorial-mock-api-responses"></a>Kurz: napodobení odpovědí rozhraní API

Back-endové rozhraní API je možné importovat do rozhraní API pro API Management (APIM) nebo je vytvořit a spravovat ručně. V krocích v tomto kurzu se dozvíte, jak pomocí APIM vytvořit prázdné rozhraní API a ručně ho spravovat, a pak nastavit zásadu na rozhraní API tak, aby vracela napodobnou odpověď. Tato metoda umožňuje vývojářům pokračovat v implementaci a testování instance služby APIM i v případě, že není k dispozici back-end, který by odesílal skutečné odpovědi. 

Možnost napodobení odpovědí může být užitečná v různých scénářích:

+ Když se jako první navrhuje fasáda rozhraní API a back-endová implementace proběhne později. Případně vývoj back-endu probíhá paralelně.
+ Když back-end dočasně není v provozu nebo neumožňuje škálování.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření testovacího rozhraní API 
> * Přidání operace do testovacího rozhraní API
> * Povolení napodobování odpovědí
> * Test imitace rozhraní API


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Napodobná odpověď rozhraní API":::

## <a name="prerequisites"></a>Požadavky

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Seznamte se s [konceptem zásad ve službě Azure API Management](api-management-howto-policies.md).
+ Dokončete následující rychlý Start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Vytvoření testovacího rozhraní API 

Postup v této části ukazuje, jak vytvořit prázdné rozhraní API bez jakéhokoli back-endu. 


1. Přihlaste se k Azure Portal a přejděte k instanci API Management.
1. Vyberte **rozhraní API**  >  **+ Přidat rozhraní** API  >  **prázdné rozhraní API**.
1. V okně **vytvořit prázdné rozhraní API** vyberte **plná**.
1. Zadejte *testovací rozhraní API* pro **zobrazované jméno**.
1. Pro **produkty** vyberte **neomezeno** .
1. Zajistěte, aby byl v **bráně** vybraný **spravovaný** .
1. Vyberte **Vytvořit**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Vytvoření prázdného rozhraní API":::

## <a name="add-an-operation-to-the-test-api"></a>Přidání operace do testovacího rozhraní API

Rozhraní API zpřístupňuje jednu nebo více operací. V této části přidejte operaci do prázdného rozhraní API, které jste vytvořili. Zavolání operace po dokončení kroků v této části způsobí chybu. Po dokončení kroků později v části povolení napodobení [odpovědi](#enable-response-mocking) se nezobrazí žádné chyby.

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
1. Vyberte **+ Přidat operaci**.
1. V okně **front-end** zadejte následující hodnoty.

     | Nastavení             | Hodnota                             | Popis                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Zobrazovaný název**    | *Test call*                       | Název, který se zobrazí na [portálu pro vývojáře](api-management-howto-developer-portal.md).                                                                                                                                       |
    | **Adresa URL** (příkaz HTTP) | GET                               | Vyberte jeden z předdefinovaných příkazů HTTP.                                                                                                                                         |
    | **Adresa URL**             | */test*                           | Cesta URL k rozhraní API.                                                                                                                                                                       |
    | **Popis**     |                                   |  Nepovinný popis operace, který se používá k poskytnutí dokumentace na portálu pro vývojáře vývojářům pomocí tohoto rozhraní API.                                                    |
    
1. Vyberte kartu **odpovědi** , která se nachází pod poli Adresa URL, zobrazovaný název a popis. Zadáním nastavení na této kartě můžete definovat stavové kódy odpovědí, typy obsahu, příklady a schémata.
1. Vyberte **+ Přidat odpověď** a v seznamu vyberte **200 OK** .
1. Pod záhlavím **Reprezentace** na pravé straně vyberte **+ Přidat reprezentaci**.
1. Do vyhledávacího pole zadejte *Application/JSON* a vyberte typ obsahu **Application/JSON** .
1. Do textového pole **Ukázka** zadejte `{ "sampleField" : "test" }`.
1. Vyberte **Uložit**.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Přidat operaci rozhraní API" border="false":::

I když se v tomto příkladu nevyžaduje, další nastavení pro operaci rozhraní API můžete nakonfigurovat na dalších kartách, včetně těchto:


|Karta      |Popis  |
|---------|---------|
|**Dotaz**     |  Přidejte parametry dotazu. Kromě zadání názvu a popisu můžete zadat hodnoty, které jsou přiřazeny parametru dotazu. Jedna z hodnot může být označená jako výchozí (volitelné).        |
|**Žádost**     |  Definujte typy obsahu, příklady a schémata požadavku.       |

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Chcete-li začít používat rozhraní příkazového řádku Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Chcete-li přidat operaci do testovacího rozhraní API, spusťte příkaz [AZ APIM API operace CREATE](/cli/azure/apim/api/operation#az_apim_api_operation_create) :

```azurecli
az apim api operation create --resource-group apim-hello-word-resource-group \
    --display-name "Test call" --api-id test-api --method GET \
    --url-template /test --service-name apim-hello-world 
```

Spuštěním příkazu [AZ APIM API Operations list](/cli/azure/apim/api/operation#az_apim_api_operation_list) zobrazíte všechny operace pro rozhraní API:

```azurecli
az apim api operation list --resource-group apim-hello-word-resource-group \
    --api-id test-api --service-name apim-hello-world --output table
```

Pokud chcete operaci odebrat, použijte příkaz [AZ APIM API Operation Delete](/cli/azure/apim/api/operation#az_apim_api_operation_delete) . Získá ID operace z předchozího příkazu.

```azurecli
az apim api operation delete --resource-group apim-hello-word-resource-group \
    --api-id test-api --operation-id 00000000000000000000000000000000 \
    --service-name apim-hello-world
```

Tuto operaci ponechte pro použití ve zbývající části tohoto článku.

---

## <a name="enable-response-mocking"></a>Povolení napodobování odpovědí

1. Vyberte rozhraní API, které jste vytvořili v části [Vytvoření testovacího rozhraní API](#create-a-test-api).
1. Vyberte testovací operaci, kterou jste přidali.
1. V okně na pravé straně se ujistěte, že je vybraná karta **Návrh** .
1. V okně **příchozí zpracování** vyberte **+ Přidat zásadu**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Přidat zásady zpracování" border="false":::

1. Z Galerie vyberte **přípravou odezvu**  .

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Dlaždice zásady Napodobení odpovědí" border="false":::

1. Do textového pole **Odpověď služby API Management** zadejte **200 OK, application/json**. Tento výběr určuje, že by vaše rozhraní API mělo vracet ukázku odpovědi, kterou jste definovali v předchozí části.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Nastavit napodobnou odezvu":::

1. Vyberte **Uložit**.

    > [!TIP]
    > Žlutý pruh s **napsaným textem je** pro vaše rozhraní API k dispozici, což znamená, že odpovědi vrácené z API Management jsou vytvářené pomocí [zásad](api-management-advanced-policies.md#mock-response) pro napodobování a nejsou vytvářeny back-end.

## <a name="test-the-mocked-api"></a>Test imitace rozhraní API

1. Vyberte rozhraní API, které jste vytvořili v části [Vytvoření testovacího rozhraní API](#create-a-test-api).
1. Vyberte kartu **Test**.
1. Ujistěte se, že je vybrané rozhraní API **Test call**. Vyberte **Odeslat** a proveďte testovací volání.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Test imitace rozhraní API":::

1. V **odpovědi HTTP** se zobrazí JSON zadaný jako ukázka v první části tohoto kurzu.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Napodobná odpověď HTTP":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření testovacího rozhraní API
> * Přidání operace do testovacího rozhraní API
> * Povolení napodobování odpovědí
> * Test imitace rozhraní API

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)

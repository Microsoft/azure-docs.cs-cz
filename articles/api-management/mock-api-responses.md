---
title: Kurz – špičkové odezvy rozhraní API v API Management-Azure Portal | Microsoft Docs
description: V tomto kurzu použijete API Management k nastavení zásad pro rozhraní API tak, aby vracelo napodobnou odpověď, pokud není k dispozici back-end, který by odesílal skutečné odpovědi.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 78743c5f045f2544cafe88414ed996d08bacd2a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91631084"
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
1. Vyberte **rozhraní API**  >  **+ Přidat rozhraní**API  >  **prázdné rozhraní API**.
1. V okně **vytvořit prázdné rozhraní API** vyberte **plná**.
1. Zadejte *testovací rozhraní API* pro **zobrazované jméno**.
1. Pro **produkty**vyberte **neomezeno** .
1. Zajistěte, aby byl v **bráně**vybraný **spravovaný** .
1. Vyberte **Vytvořit**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Napodobná odpověď rozhraní API":::

## <a name="add-an-operation-to-the-test-api"></a>Přidání operace do testovacího rozhraní API

Rozhraní API zpřístupňuje jednu nebo více operací. V této části přidejte operaci do prázdného rozhraní API, které jste vytvořili. Zavolání operace po dokončení kroků v této části způsobí chybu. Po dokončení kroků později v části povolení napodobení [odpovědi](#enable-response-mocking) se nezobrazí žádné chyby.

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
1. Vyberte **+ Přidat odpověď**a v seznamu vyberte **200 OK** .
1. Pod záhlavím **Reprezentace** na pravé straně vyberte **+ Přidat reprezentaci**.
1. Do vyhledávacího pole zadejte *Application/JSON* a vyberte typ obsahu **Application/JSON** .
1. Do textového pole **Ukázka** zadejte `{ "sampleField" : "test" }`.
1. Vyberte **Uložit**.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Napodobná odpověď rozhraní API" border="false":::

I když se v tomto příkladu nevyžaduje, další nastavení pro operaci rozhraní API můžete nakonfigurovat na dalších kartách, včetně těchto:


|Karta      |Popis  |
|---------|---------|
|**Dotaz**     |  Přidejte parametry dotazu. Kromě zadání názvu a popisu můžete zadat hodnoty, které jsou přiřazeny parametru dotazu. Jedna z hodnot může být označená jako výchozí (volitelné).        |
|**Žádost**     |  Definujte typy obsahu, příklady a schémata požadavku.       |

## <a name="enable-response-mocking"></a>Povolení napodobování odpovědí

1. Vyberte rozhraní API, které jste vytvořili v části [Vytvoření testovacího rozhraní API](#create-a-test-api).
1. Vyberte testovací operaci, kterou jste přidali.
1. V okně na pravé straně se ujistěte, že je vybraná karta **Návrh** .
1. V okně **příchozí zpracování** vyberte **+ Přidat zásadu**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Napodobná odpověď rozhraní API" border="false":::

1. Z Galerie vyberte **přípravou odezvu**  .

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Napodobná odpověď rozhraní API" border="false":::

1. Do textového pole **Odpověď služby API Management** zadejte **200 OK, application/json**. Tento výběr určuje, že by vaše rozhraní API mělo vracet ukázku odpovědi, kterou jste definovali v předchozí části.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Napodobná odpověď rozhraní API":::

1. Vyberte **Uložit**.

    > [!TIP]
    > Žlutý pruh s **napodobou textu je povolený** pro vaše rozhraní API označuje, že odpovědi vrácené z API Management odesílají napodobné zásady a nikoli skutečnou odpověď back-endu.

## <a name="test-the-mocked-api"></a>Test imitace rozhraní API

1. Vyberte rozhraní API, které jste vytvořili v části [Vytvoření testovacího rozhraní API](#create-a-test-api).
1. Vyberte kartu **Test**.
1. Ujistěte se, že je vybrané rozhraní API **Test call**. Vyberte **Odeslat** a proveďte testovací volání.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Napodobná odpověď rozhraní API":::

1. V **odpovědi HTTP** se zobrazí JSON zadaný jako ukázka v první části tohoto kurzu.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Napodobná odpověď rozhraní API":::

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

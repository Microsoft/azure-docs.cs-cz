---
title: Importování Function App jako rozhraní API pomocí portálu Azure Portal | Microsoft Docs
description: V tomto kurzu se dozvíte, jak importovat Function App jako rozhraní API pomocí API Managementu (APIM).
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/15/2018
ms.author: apimpm
ms.openlocfilehash: 0ee83446bb08e66c7f325bdd5585b8cc0484a74e
ms.sourcegitcommit: e32ea47d9d8158747eaf8fee6ebdd238d3ba01f7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39090924"
---
# <a name="import-a-function-app-as-an-api"></a>Importování Function App jako rozhraní API

Tento článek popisuje, jak importovat Function App jako rozhraní API. Tento článek také ukazuje, jak otestovat rozhraní API služby APIM.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Importování Function App jako rozhraní API
> * Testovat rozhraní API na portálu Azure Portal
> * Testovat rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Požadavky

+ Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
+ Zkontrolujte, jestli máte v předplatném Azure Function App. Další informace najdete ve [Vytvoření Function App](../azure-functions/functions-create-first-azure-function.md#create-a-function-app).
+ [Vytvoření definice OpenAPI](../azure-functions/functions-openapi-definition.md) pro Azure Function App

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Import a publikování back-endového rozhraní API

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. Vyberte **Function App** ze seznamu **Přidat nové rozhraní API**.

    ![Function App](./media/import-function-app-as-api/function-app-api.png)
3. Stisknutím **Procházet** zobrazte seznam aplikací Function App ve vašem předplatném.
4. Vyberte aplikaci. APIM najde swagger spojený s vybranou aplikací, načte ho a naimportuje. 
5. Přidejte příponu adresy URL rozhraní API. Přípona je název, který identifikuje toto konkrétní rozhraní API v této instanci APIM. Musí být v této instanci APIM jedinečná.
6. Publikujte rozhraní API jeho přidružením k produktu. V tomto případě se použije produkt *Unlimited*.  Pokud chcete, aby bylo rozhraní API publikované a k dispozici pro vývojáře, přidejte ho k produktu. Můžete to udělat při vytváření rozhraní API nebo nastavit později.

    Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci služby APIM, již jste správcem, takže jste ve výchozím nastavení přihlášeni k odběru všech produktů.

    Ve výchozím nastavení každá instance služby API Management obsahuje dva ukázkové produkty:

    * **Starter**
    * **Unlimited**   
7. Vyberte **Vytvořit**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Naplnění klíčů Azure Functions ve službě Azure API Management

Pokud jsou importované Azure Functions chráněné klíčem, Azure API Management pro ně automaticky vytvoří **Pojmenované hodnoty**, ale nenaplní položky tajnými kódy. Pro každou položku je potřeba provést následující postup.  

1. Přejděte na kartu **Pojmenované hodnoty** v instanci API Managementu.
2. Klikněte na položku a stiskněte na bočním panelu **Zobrazit hodnotu**.

    ![Pojmenované hodnoty](./media/import-function-app-as-api/apim-named-values.png)

3. Pokud se obsah podobá *kódu pro {název funkce Azure Functions}*, přistupte k importované aplikaci Azure Functions a přejděte na svoji funkci Azure Functions.
4. Spusťte **spravovanou** sekci požadované funkce Azure Functions a zkopírujte příslušný klíč podle metody ověřování funkce Azure Functions.

    ![Function App](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Vložte klíč do textového pole v části **Pojmenované hodnoty** a klikněte na **Uložit**.

    ![Function App](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testování nového rozhraní API služby APIM na portálu Azure Portal

Operaci můžete volat přímo z portálu Azure Portal, který nabízí pohodlný způsob pro zobrazení a testování operací v rozhraní API.  

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
2. Klikněte na kartu **Test**.
3. Vyberte nějakou operaci.

    Stránka zobrazí pole pro parametry dotazu a pole pro hlavičky. Jednou z hlaviček je klíč „Ocp-Apim-Subscription-Key“. Je to klíč pro přihlášení k odběru produktu, který je k tomuto rozhraní API přidružený. Pokud jste vytvořili instanci služby APIM, jste už správcem a klíč se tedy vyplní automaticky. 
1. Stiskněte **Odeslat**.

    Back-end předá odpověď **200 OK** a nějaká data.

## <a name="call-operation"></a>Volání operace z portálu pro vývojáře

Pokud chcete otestovat rozhraní API, můžete operace volat také z **Portálu pro vývojáře**. 

1. Vyberte rozhraní API, které jste vytvořili v kroku Import a publikování back-endového rozhraní API.
2. Stiskněte **Portál pro vývojáře**.

    Otevře se web Portál pro vývojáře.
3. Vyberte **rozhraní API**, které jste vytvořili.
4. Klikněte na operaci, kterou chcete testovat.
5. Stiskněte **Vyzkoušet**.
6. Stiskněte **Odeslat**.
    
    Po vyvolání operace portál pro vývojáře zobrazí **Stav odpovědi**, **Hlavičky odpovědi** a jakýkoli **Obsah odpovědi**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)
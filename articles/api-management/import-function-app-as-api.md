---
title: Importování aplikace Azure Functions jako rozhraní API pomocí webu Azure Portal | Microsoft Docs
description: V tomto kurzu se dozvíte, jak importovat aplikaci Azure Functions jako rozhraní API pomocí Azure API Managementu.
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
ms.openlocfilehash: 670fa58de7155028b0f72f1f819b9f269e07b9eb
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239048"
---
# <a name="import-an-azure-functions-app-as-an-api"></a>Import aplikace Azure Functions jako rozhraní API

Tento článek popisuje, jak importovat aplikaci Azure Functions jako rozhraní API. Tento článek také ukazuje, jak otestovat rozhraní API služby Azure API Management.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Import aplikace Functions jako rozhraní API
> * Testovat rozhraní API na portálu Azure Portal
> * Testování rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Požadavky

+ Projděte si rychlý start [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
+ Zkontrolujte, jestli máte v předplatném aplikaci Azure Functions. Další informace najdete v tématu [Vytvoření aplikace Functions](../azure-functions/functions-create-first-azure-function.md#create-a-function-app).
+ [Vytvořte definici OpenAPI](../azure-functions/functions-openapi-definition.md) své aplikace Azure Functions.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"></a>Import a publikování back-endového rozhraní API

1. V části **API MANAGEMENT** vyberte **Rozhraní API**.
2. V seznamu **Přidat nové rozhraní API** vyberte **Aplikace Functions**.

    ![Aplikace Functions](./media/import-function-app-as-api/function-app-api.png)
3. Výběrem položky **Procházet** zobrazte seznam aplikací Functions ve vašem předplatném.
4. Vyberte aplikaci. Služba API Management vyhledá swagger spojený s vybranou aplikací, načte ho a importuje. 
5. Přidejte příponu adresy URL rozhraní API. Přípona je název, který identifikuje toto konkrétní rozhraní API v této instanci služby API Management. Přípona musí být v této instanci služby API Management jedinečná.
6. Publikujte rozhraní API jeho přidružením k produktu. V tomto případě se použije produkt **Unlimited**. Pokud chcete, aby bylo rozhraní API publikované a k dispozici pro vývojáře, přidejte ho k produktu. Rozhraní API můžete přidat k produktu ve chvíli, kdy rozhraní API vytváříte, nebo později.

    Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získá vývojář klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci služby API Management, znamená to, že jste správce. Ve výchozím nastavení jsou správci přihlášení k odběru všech produktů.

    Ve výchozím nastavení každá instance služby API Management obsahuje dva ukázkové produkty:

    * **Starter**
    * **Unlimited**   
7. Vyberte **Vytvořit**.

## <a name="populate-azure-functions-keys-in-azure-api-management"></a>Naplnění klíčů Azure Functions ve službě Azure API Management

Pokud jsou importované aplikace Azure Functions chráněné klíčem, Azure API Management pro ně automaticky vytvoří *pojmenované hodnoty*. API Management nenaplní položky tajnými kódy. Pro každou položku proveďte následující kroky:  

1. V instanci služby API Management vyberte kartu **Pojmenované hodnoty**.
2. Vyberte položku a potom na bočním panelu vyberte **Zobrazit hodnotu**.

    ![Pojmenované hodnoty](./media/import-function-app-as-api/apim-named-values.png)

3. Pokud se text zobrazený v poli **Hodnota** podobá **kódu pro \<název Azure Functions\>**, přejděte na **Aplikace Functions** a potom na **Functions**.
4. Vyberte **Spravovat** a potom zkopírujte příslušný klíč podle metody ověřování vaší aplikace.

    ![Aplikace Functions – kopírování klíčů](./media/import-function-app-as-api/azure-functions-app-keys.png)

5. Vložte klíč do pole **Hodnota** a potom vyberte **Uložit**.

    ![Aplikace Functions – vložení hodnot klíče](./media/import-function-app-as-api/apim-named-values-2.png)

## <a name="test-the-new-api-management-api-in-the-azure-portal"></a>Testování nového rozhraní API služby API Management na webu Azure Portal

Operace můžete volat přímo z webu Azure Portal. Web Azure Portal nabízí pohodlný způsob zobrazení a testování operací v rozhraní API.  

1. Vyberte rozhraní API, které jste vytvořili v předchozí části.
2. Vyberte kartu **Test**.
3. Vyberte operaci.

    Stránka zobrazí pole pro parametry dotazu a pole pro hlavičky. Jednou z hlaviček je klíč **Ocp-Apim-Subscription-Key**. Je to klíč pro přihlášení k odběru produktu, který je k tomuto rozhraní API přidružený. Pokud jste vytvořili instanci služby API Management, jste už správcem a klíč se tedy vyplní automaticky. 
4. Vyberte **Poslat**.

    Back-end předá odpověď **200 OK** a nějaká data.

## <a name="call-operation"></a>Volání operace z portálu pro vývojáře

Operace pro testování rozhraní API můžete volat taky z portálu pro vývojáře. 

1. Vyberte rozhraní API, které jste vytvořili v kroku [Import a publikování back-endového rozhraní API](#create-api).
2. Vyberte **Portál pro vývojáře**.

    Otevře se web Portál pro vývojáře.
3. Vyberte **rozhraní API**, které jste vytvořili.
4. Vyberte operaci, kterou chcete otestovat.
5. Vyberte **Vyzkoušet**.
6. Vyberte **Poslat**.
    
    Po vyvolání operace portál pro vývojáře zobrazí **Stav odpovědi**, **Hlavičky odpovědi** a jakýkoli **Obsah odpovědi**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)
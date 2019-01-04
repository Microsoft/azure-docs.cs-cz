---
title: Import aplikace logiky jako rozhraní API pomocí webu Azure Portal | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí služby API Management (APIM) importovat aplikaci logiky jako rozhraní API.
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
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 76a509c1cb9277ac72f99ec9ebfc239bfd71390c
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/02/2019
ms.locfileid: "53969178"
---
# <a name="import-a-logic-app-as-an-api"></a>Import aplikace logiky jako rozhraní API

Tento článek popisuje, jak importovat aplikaci logiky jako rozhraní API. Tento článek také ukazuje, jak otestovat rozhraní API služby APIM.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Import aplikace logiky jako rozhraní API
> * Testovat rozhraní API na portálu Azure Portal
> * Testovat rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Požadavky

* Projděte si následující rychlý start: [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md)
* Ujistěte se, že v rámci vašeho předplatného, který zpřístupňuje koncový bod HTTP aplikace logiky. Další informace najdete [aktivovat pracovní postupy pomocí koncových bodů HTTP](../logic-apps/logic-apps-http-endpoint.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Import a publikování back-endového rozhraní API

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. Ze seznamu **Přidat nové rozhraní API** vyberte **Aplikace logiky**.

    ![Aplikace logiky](./media/import-logic-app-as-api/logic-app-api.png)
3. Stisknutím klávesy **Procházet** zobrazíte seznam volatelná aplikacemi Logic Apps v rámci vašeho předplatného.
4. Vyberte aplikaci. APIM najde swagger spojený s vybranou aplikací, načte ho a naimportuje. 
5. Přidejte příponu adresy URL rozhraní API. Přípona je název, který identifikuje toto konkrétní rozhraní API v této instanci APIM. Musí být v této instanci APIM jedinečná.
6. Publikujte rozhraní API jeho přidružením k produktu. V tomto případě se použije produkt *Unlimited*.  Pokud chcete, aby bylo rozhraní API publikované a k dispozici pro vývojáře, přidejte ho k produktu. Můžete to udělat při vytváření rozhraní API nebo nastavit později.

    Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci služby APIM, již jste správcem, takže jste ve výchozím nastavení přihlášeni k odběru všech produktů.

    Ve výchozím nastavení každá instance služby API Management obsahuje dva ukázkové produkty:

    * **Starter**
    * **Unlimited**   
7. Vyberte **Vytvořit**.

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

>[!NOTE]
> Každá aplikace logiky obsahuje operaci **manual-invoke**. Pokud chcete složit rozhraní API z několika aplikací logiky, je potřeba funkci přejmenovat, aby se zabránilo kolizi.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)
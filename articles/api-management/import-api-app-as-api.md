---
title: Import aplikace API jako rozhraní API pomocí webu Azure Portal | Microsoft Docs
description: V tomto kurzu se dozvíte, jak pomocí služby API Management (APIM) importovat aplikaci API jako rozhraní API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: bf4039b661ac7e0ee9630b86b9e15d1388991431
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2019
ms.locfileid: "74108501"
---
# <a name="import-an-api-app-as-an-api"></a>Import aplikace API jako rozhraní API

Tento článek popisuje, jak importovat aplikaci API jako rozhraní API. Tento článek také ukazuje, jak otestovat rozhraní API služby APIM.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Import aplikace API jako rozhraní API
> * Testovat rozhraní API na portálu Azure Portal
> * Testovat rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Požadavky

+ Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
+ Zkontrolujte, jestli ve svém předplatném máte aplikaci API. Další informace najdete v [dokumentaci k App Service](https://docs.microsoft.com/azure/app-service/) .

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Import a publikování back-endového rozhraní API

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. Ze seznamu **Přidat nové rozhraní API** vyberte **Aplikace API**.

    ![Aplikace API](./media/import-api-app-as-api/api-app.png)
3. Stisknutím **Procházet** zobrazte seznam aplikací API ve vašem předplatném.
4. Vyberte aplikaci. APIM vyhledá swagger přidružený k vybrané aplikaci, načte ho a naimportuje. 

    V případě, že API swagger nenajde, zveřejní rozhraní API jako předávací rozhraní API. 
5. Přidejte příponu adresy URL rozhraní API. Přípona je název, který identifikuje toto konkrétní rozhraní API v této instanci APIM. Musí být v této instanci APIM jedinečná.
6. Publikujte rozhraní API jeho přidružením k produktu. V tomto případě se použije produkt *Unlimited*.  Pokud chcete, aby bylo rozhraní API publikované a k dispozici pro vývojáře, přidejte ho k produktu. Můžete to udělat při vytváření rozhraní API nebo nastavit později.

    Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci služby APIM, již jste správcem, takže jste ve výchozím nastavení přihlášeni k odběru všech produktů.

    Ve výchozím nastavení každá instance služby API Management obsahuje dva ukázkové produkty:

    * **Starter**
    * **Unlimited**   
7. Vyberte **Vytvořit**.

## <a name="test-the-new-api-in-the-azure-portal"></a>Otestujte nové rozhraní API v Azure Portal

Operaci můžete volat přímo z portálu Azure Portal, který nabízí pohodlný způsob pro zobrazení a testování operací v rozhraní API.  

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
2. Klikněte na kartu **Test**.
3. Vyberte nějakou operaci.

    Stránka zobrazí pole pro parametry dotazu a pole pro hlavičky. Jednou z hlaviček je klíč „Ocp-Apim-Subscription-Key“. Je to klíč pro přihlášení k odběru produktu, který je k tomuto rozhraní API přidružený. Pokud jste vytvořili instanci služby APIM, jste už správcem a klíč se tedy vyplní automaticky. 
1. Stiskněte **Odeslat**.

    Back-end předá odpověď **200 OK** a nějaká data.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)

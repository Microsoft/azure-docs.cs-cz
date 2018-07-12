---
title: Import rozhraní API protokolu SOAP a převod na REST pomocí webu Azure Portal | Microsoft Docs
description: Zjistěte, jak pomocí služby API Management importovat rozhraní API protokolu SOAP a převést ho na REST.
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
ms.openlocfilehash: 940756917c8f377e7d134818409e6287a4031e15
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38239804"
---
# <a name="import-a-soap-api-and-convert-to-rest"></a>Import rozhraní API protokolu SOAP a převod na REST

Tento článek popisuje, jak importovat rozhraní API protokolu SOAP a převést ho na REST. Tento článek také ukazuje, jak otestovat rozhraní API služby APIM.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Import rozhraní API protokolu SOAP a převod na REST
> * Testovat rozhraní API na portálu Azure Portal
> * Testovat rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Požadavky

Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Import a publikování back-endového rozhraní API

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. Vyberte **WSDL** ze seznamu **Přidat nové rozhraní API**.

    ![Rozhraní API protokolu SOAP](./media/restify-soap-api/wsdl-api.png)
3. Do pole **Specifikace WSDL** zadejte adresu URL s vaším rozhraním API protokolu SOAP.
4. Klikněte na přepínač **Ze SOAP do REST**. Po kliknutí na tuto možnost se služba APIM pokusí provést automatickou transformaci mezi XML a JSON. V tomto případě by příjemci měli rozhraní API volat jako rozhraní RESTful API, které vrací JSON. Služba APIM převádí každý požadavek na volání SOAP.

    ![Ze SOAP do REST](./media/restify-soap-api/soap-to-rest.png)

5. Stiskněte tabulátor.

    Následující pole se vyplní informacemi z rozhraní API SOAP: Zobrazovaný název, Název, Popis.
6. Přidejte příponu adresy URL rozhraní API. Přípona je název, který identifikuje toto konkrétní rozhraní API v této instanci APIM. Musí být v této instanci APIM jedinečná.
9. Publikujte rozhraní API jeho přidružením k produktu. V tomto případě se použije produkt *Unlimited*.  Pokud chcete, aby bylo rozhraní API publikované a k dispozici pro vývojáře, přidejte ho k produktu. Můžete to udělat při vytváření rozhraní API nebo nastavit později.

    Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci služby APIM, již jste správcem, takže jste ve výchozím nastavení přihlášeni k odběru všech produktů.

    Ve výchozím nastavení každá instance služby API Management obsahuje dva ukázkové produkty:

    * **Starter**
    * **Unlimited**   
10. Vyberte **Vytvořit**.

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
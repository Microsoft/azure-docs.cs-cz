---
title: Importování rozhraní API SOAP pomocí portálu Azure Portal | Microsoft Docs
description: Zjistěte, jak importovat rozhraní API SOAP pomocí API Managementu.
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
ms.openlocfilehash: 108758751b7c8ef5906cb55495a2604f918b2714
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="import-soap-api"></a>Importování rozhraní API SOAP

Tento článek popisuje, jak importovat standardní reprezentaci XML rozhraní API SOAP. Tento článek také ukazuje, jak otestovat rozhraní API služby APIM.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Importování rozhraní API SOAP
> * Testovat rozhraní API na portálu Azure Portal
> * Testovat rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Požadavky

Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Import a publikování back-endového rozhraní API

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. Vyberte **WSDL** ze seznamu **Přidat nové rozhraní API**.

    ![API SOAP](./media/import-soap-api/wsdl-api.png)
3. Do **Specifikace WSDL** zadejte adresu URL, kde se nachází rozhraní API SOAP.
4. Ve výchozím nastavení je vybraný přepínač **Průchod SOAP**. S tímto výběrem bude rozhraní API prezentované jako SOAP. Příjemce musí používat pravidla SOAP. Pokud chcete rozhraní API převést na REST, postupujte podle kroků v tématu o [importování rozhraní API SOAP a jeho převedení na REST](restify-soap-api.md).

    ![Průchod](./media/import-soap-api/pass-through.png)
5. Stiskněte klávesu Tab.

    Následující pole se vyplní informacemi z rozhraní API SOAP: Zobrazovaný název, Název, Popis.
6. Přidejte příponu adresy URL rozhraní API. Přípona je název, který identifikuje toto konkrétní rozhraní API v této instanci APIM. Musí být v této instanci APIM jedinečná.
9. Publikujte rozhraní API jeho přidružením k produktu. V tomto případě se použije produkt *Unlimited*.  Pokud chcete, aby bylo rozhraní API publikované a k dispozici pro vývojáře, přidejte ho k produktu. Můžete to udělat při vytváření rozhraní API nebo nastavit později.

    Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci služby APIM, již jste správcem, takže jste ve výchozím nastavení přihlášeni k odběru všech produktů.

    Ve výchozím nastavení každá instance služby API Management obsahuje dva ukázkové produkty:

    * **Starter**
    * **Unlimited**   
10. Vyberte **Vytvořit**.

### <a name="test-the-new-apim-api-in-the-administrative-portal"></a>Otestování nového rozhraní API APIM na portálu pro správu

Operaci můžete volat přímo z portálu pro správu, který nabízí pohodlný způsob pro zobrazení a testování operací v rozhraní API.  

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
2. Klikněte na kartu **Test**.
3. Vyberte nějakou operaci.

    Stránka zobrazí pole pro parametry dotazu a pole pro hlavičky. Jednou z hlaviček je klíč „Ocp-Apim-Subscription-Key“. Je to klíč pro přihlášení k odběru produktu, který je k tomuto rozhraní API přidružený. Pokud jste vytvořili instanci služby APIM, jste už správcem a klíč se tedy vyplní automaticky. 
1. Stiskněte **Odeslat**.

    Back-end předá odpověď **200 OK** a nějaká data.

### <a name="call-operation"></a>Volání operace z portálu pro vývojáře

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
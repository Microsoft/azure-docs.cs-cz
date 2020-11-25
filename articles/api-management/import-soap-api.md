---
title: Importování rozhraní API SOAP pomocí portálu Azure Portal | Microsoft Docs
description: Přečtěte si, jak importovat standardní reprezentace XML rozhraní SOAP API a pak otestovat rozhraní API na portálech Azure a vývojářích.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: 81ded79ee72fb7c2d89898595602cb3e6d7ae5e2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011060"
---
# <a name="import-soap-api"></a>Importování rozhraní API SOAP

Tento článek popisuje, jak importovat standardní reprezentaci XML rozhraní API SOAP. Článek také ukazuje, jak otestovat rozhraní API Management API.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Importování rozhraní API SOAP
> * Testovat rozhraní API na portálu Azure Portal
> * Testovat rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Požadavky

Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Import a publikování back-endu rozhraní API

1. V Azure Portal přejděte do služby API Management a v nabídce vyberte **rozhraní API** .
2. Vyberte **WSDL** ze seznamu **Přidat nové rozhraní API**.

    ![API SOAP](./media/import-soap-api/wsdl-api.png)
3. Do **Specifikace WSDL** zadejte adresu URL, kde se nachází rozhraní API SOAP.
4. Ve výchozím nastavení je vybraný přepínač **Průchod SOAP**. S tímto výběrem bude rozhraní API prezentované jako SOAP. Příjemce musí používat pravidla SOAP. Pokud chcete rozhraní API převést na REST, postupujte podle kroků v tématu o [importování rozhraní API SOAP a jeho převedení na REST](restify-soap-api.md).

    ![Snímek obrazovky se zobrazí v dialogovém okně vytvořit z W S D L, kde můžete zadat specifikaci W S D L.](./media/import-soap-api/pass-through.png)
5. Stiskněte klávesu Tab.

    Následující pole se vyplní informacemi z rozhraní API SOAP: Zobrazovaný název, Název, Popis.
6. Přidejte příponu adresy URL rozhraní API. Přípona je název, který identifikuje toto konkrétní rozhraní API v této instanci služby API Management. Musí být v této instanci API Management jedinečný.
7. Publikujte rozhraní API jeho přidružením k produktu. V tomto případě se použije produkt *Unlimited*.  Pokud chcete, aby bylo rozhraní API publikované a k dispozici pro vývojáře, přidejte ho k produktu. Můžete to udělat při vytváření rozhraní API nebo nastavit později.

    Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci API Management, jste už správce, takže se ve výchozím nastavení přihlásíte k odběru všech produktů.

    Ve výchozím nastavení každá instance služby API Management obsahuje dva ukázkové produkty:

    * **Starter**
    * **Unlimited**   
8. Zadejte další nastavení rozhraní API. Hodnoty můžete nastavit během vytváření nebo je nakonfigurovat později tak, že na kartu **Nastavení** kliknete. Nastavení jsou vysvětlena v kurzu [importu a publikování prvního rozhraní API](import-and-publish.md#import-and-publish-a-backend-api) .
9. Vyberte **Vytvořit**.

### <a name="test-the-new-api-in-the-administrative-portal"></a>Testování nového rozhraní API na portálu pro správu

Operaci můžete volat přímo z portálu pro správu, který nabízí pohodlný způsob pro zobrazení a testování operací v rozhraní API.  

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
2. Klikněte na kartu **Test**.
3. Vyberte nějakou operaci.

    Stránka zobrazí pole pro parametry dotazu a pole pro hlavičky. Jednou z hlaviček je klíč „Ocp-Apim-Subscription-Key“. Je to klíč pro přihlášení k odběru produktu, který je k tomuto rozhraní API přidružený. Pokud jste vytvořili instanci služby API Management, jste už správcem a klíč se tedy vyplní automaticky. 
1. Stiskněte **Odeslat**.

    Back-end předá odpověď **200 OK** a nějaká data.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)
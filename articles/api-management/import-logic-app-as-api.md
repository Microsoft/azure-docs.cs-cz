---
title: Import aplikace logiky jako rozhraní API pomocí webu Azure Portal | Microsoft Docs
description: V tomto článku se dozvíte, jak pomocí API Management (APIM) importovat aplikace logiky jako rozhraní API.
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
ms.openlocfilehash: 0dd460bce01b86c72d0b8fd70351fdcc85c69bd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146956"
---
# <a name="import-a-logic-app-as-an-api"></a>Import aplikace logiky jako rozhraní API

Tento článek ukazuje, jak importovat aplikaci logiky jako rozhraní API a otestovat importované rozhraní API.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
>
> -   Import aplikace logiky jako rozhraní API
> -   Testovat rozhraní API na portálu Azure Portal
> -   Testovat rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Požadavky

-   Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)
-   Ujistěte se, že ve vašem předplatném je aplikace logiky, která zveřejňuje koncový bod HTTP. Další informace najdete v [pracovních postupech s koncovými body http](../logic-apps/logic-apps-http-endpoint.md) .

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-and-publish-a-back-end-api"></a><a name="create-api"> </a>Import a publikování back-endu rozhraní API

1. V Azure Portal přejděte do služby API Management a v nabídce vyberte **rozhraní API** .
2. Ze seznamu **Přidat nové rozhraní API** vyberte **Aplikace logiky**.

    ![Aplikace logiky](./media/import-logic-app-as-api/logic-app-api.png)

3. Kliknutím na **Procházet** zobrazte seznam Logic Apps s triggerem http v předplatném. (Všimněte si, že Logic Apps bez triggeru HTTP se v seznamu nezobrazí.)
4. Vyberte aplikaci. API Management najde Swagger přidružený k vybrané aplikaci, načte ho a naimportuje.
5. Přidejte příponu adresy URL rozhraní API. Přípona je název, který identifikuje toto konkrétní rozhraní API v této instanci služby API Management. Musí být v této instanci API Management jedinečný.
6. Publikujte rozhraní API jeho přidružením k produktu. V tomto případě se použije produkt _Unlimited_. Pokud chcete, aby bylo rozhraní API publikované a k dispozici pro vývojáře, přidejte ho k produktu. Můžete to udělat při vytváření rozhraní API nebo nastavit později.

    Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci API Management, jste už správce, takže se ve výchozím nastavení přihlásíte k odběru všech produktů.

    Ve výchozím nastavení každá instance služby API Management obsahuje dva ukázkové produkty:

    - **Starter**
    - **Unlimited**

7. Zadejte další nastavení rozhraní API. Hodnoty můžete nastavit během vytváření nebo je nakonfigurovat později tak, že na kartu **Nastavení** kliknete. Nastavení jsou vysvětlena v kurzu [importu a publikování prvního rozhraní API](import-and-publish.md#import-and-publish-a-backend-api) .
8. Vyberte **Vytvořit**.

## <a name="test-the-api-in-the-azure-portal"></a>Testovat rozhraní API na portálu Azure Portal

Operaci můžete volat přímo z portálu Azure Portal, který nabízí pohodlný způsob pro zobrazení a testování operací v rozhraní API.

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
2. Klikněte na kartu **Test**.
3. Vyberte nějakou operaci.

    Stránka zobrazí pole pro parametry dotazu a pole pro hlavičky. Jednou z hlaviček je klíč „Ocp-Apim-Subscription-Key“. Je to klíč pro přihlášení k odběru produktu, který je k tomuto rozhraní API přidružený. Pokud jste vytvořili instanci služby API Management, jste už správcem a klíč se tedy vyplní automaticky.

4. Stiskněte **Odeslat**.

    Back-end předá odpověď **200 OK** a nějaká data.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

>[!NOTE]
>Každá aplikace logiky obsahuje operaci **manual-invoke**. Pokud chcete složit rozhraní API z několika aplikací logiky, je potřeba funkci přejmenovat, aby se zabránilo kolizi.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)

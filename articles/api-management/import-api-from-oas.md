---
title: Import specifikace OpenAPI pomocí webu Azure Portal | Microsoft Docs
description: Zjistěte, jak pomocí služby API Management importovat specifikaci OpenAPI.
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
ms.openlocfilehash: f5132215b1fda93c62c1fbea46c3266fcc44ec46
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33936547"
---
# <a name="import-an-openapi-specification"></a>Import specifikace OpenAPI

Tento článek ukazuje, jak importovat back-endové rozhraní API „specifikace OpenAPI“ nacházející se na stránce http://conferenceapi.azurewebsites.net?format=json. Toto back-endové rozhraní API poskytuje Microsoft a hostuje ho na Azure. Tento článek také ukazuje, jak otestovat rozhraní API služby APIM.

> [!IMPORTANT]
> Důležité informace a tipy týkající se importu OpenAPI najdete v tomto [dokumentu](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/).

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Import back-endového rozhraní API „specifikace OpenAPI“
> * Testovat rozhraní API na portálu Azure Portal
> * Testovat rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Požadavky

Projděte si následující rychlý start: [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Import a publikování back-endového rozhraní API

1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. Ze seznamu **Přidat nové rozhraní API** vyberte **Specifikace OpenAPI**.
    ![Specifikace OpenAPI](./media/import-api-from-oas/oas-api.png)
3. Zadejte odpovídající nastavení. Všechny hodnoty rozhraní API můžete nastavit během vytváření. Alternativně můžete některé z nich nastavit později na kartě **Nastavení**. <br/> Pokud stisknete **tabulátor**, do některých (nebo do všech) polí se vyplní informace ze zadané back-endové služby.

    ![Vytvoření rozhraní API](./media/api-management-get-started/create-api.png)

    |Nastavení|Hodnota|Popis|
    |---|---|---|
    |**Specifikace OpenAPI**|http://conferenceapi.azurewebsites.net?format=json|Odkazuje na službu implementující rozhraní API. Služba API Management na tuto adresu směruje požadavky.|
    |**Zobrazovaný název**|*Demo Conference API*|Pokud po zadání adresy URL služby stisknete tabulátor, služba APIM toto pole vyplní podle informací ze souboru JSON. <br/>Tento název se zobrazí na portálu pro vývojáře.|
    |**Název**|*demo-conference-api*|Jedinečný název pro rozhraní API. <br/>Pokud po zadání adresy URL služby stisknete tabulátor, služba APIM toto pole vyplní podle informací ze souboru JSON.|
    |**Popis**|Volitelný popis pro rozhraní API.|Pokud po zadání adresy URL služby stisknete tabulátor, služba APIM toto pole vyplní podle informací ze souboru JSON.|
    |**Přípona adresy URL rozhraní API**|*conference*|Přípona se připojí k základní adrese URL služby API Management. Služba API Management rozlišuje rozhraní API podle jejich přípony, proto musí být přípona jedinečná pro každé rozhraní API daného vydavatele.|
    |**Schéma URL**|*HTTPS*|Určuje, které protokoly je možné použít pro přístup k rozhraní API. |
    |**Produkty**|*Unlimited*| Publikujte rozhraní API jeho přidružením k produktu. Volitelně můžete toto nové rozhraní API přidat k produktu zadáním názvu produktu. Pokud chcete rozhraní API přiřadit k více produktům, můžete tento krok opakovat několikrát.<br/>Produkty jsou sdruženími jednoho nebo více rozhraní API. Můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. Vývojáři se nejprve musí přihlásit k odběru produktu, a teprve pak získají přístup k rozhraní API. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. Pokud jste vytvořili instanci služby APIM, již jste správcem, takže jste ve výchozím nastavení přihlášeni k odběru všech produktů.<br/> Ve výchozím nastavení každá instance služby API Management obsahuje dva ukázkové produkty: **Starter** a **Unlimited**. |

4. Vyberte **Vytvořit**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testování nového rozhraní API služby APIM na portálu Azure Portal

Operaci můžete volat přímo z portálu Azure Portal, který nabízí pohodlný způsob pro zobrazení a testování operací v rozhraní API.

1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku.
2. Klikněte na kartu **Test**.

    ![Test rozhraní API](./media/api-management-get-started/test-api.png)
1. Klikněte na možnost **GetSpeakers**.

    Stránka zobrazí pole pro parametry dotazu. V tomto případě ale žádné neexistují. Stránka také zobrazí pole pro hlavičky. Jednou z hlaviček je klíč „Ocp-Apim-Subscription-Key“. Je to klíč pro přihlášení k odběru produktu, který je k tomuto rozhraní API přidružený. Pokud jste vytvořili instanci služby APIM, jste už správcem a klíč se tedy vyplní automaticky.
4. Stiskněte **Odeslat**.

    Back-end předá odpověď **200 OK** a nějaká data.

## <a name="call-operation"> </a>Volání operace z portálu pro vývojáře

Pokud chcete otestovat rozhraní API, můžete operace volat také z **Portálu pro vývojáře**.

1. Vyberte rozhraní API, které jste vytvořili v kroku Import a publikování back-endového rozhraní API.
2. Stiskněte **Portál pro vývojáře**.

    ![Test na portálu pro vývojáře](./media/api-management-get-started/developer-portal.png)

    Otevře se web Portál pro vývojáře.
3. Vyberte **Rozhraní API**.
4. Vyberte **Demo Conference API**.
5. Klikněte na **GetSpeakers**.

    Stránka zobrazí pole pro parametry dotazu. V tomto případě ale žádné neexistují. Stránka také zobrazí pole pro hlavičky. Jednou z hlaviček je klíč „Ocp-Apim-Subscription-Key“. Je to klíč pro přihlášení k odběru produktu, který je k tomuto rozhraní API přidružený. Pokud jste vytvořili instanci služby APIM, jste už správcem a klíč se tedy vyplní automaticky.
6. Stiskněte **Vyzkoušet**.
7. Stiskněte **Odeslat**.

    Po vyvolání operace portál pro vývojáře zobrazí **Stav odpovědi**, **Hlavičky odpovědi** a jakýkoli **Obsah odpovědi**.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)

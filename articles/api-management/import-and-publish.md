---
title: Import a publikování vašeho prvního rozhraní API ve službě Azure API Management | Microsoft Docs
description: Zjistěte, jak importovat a publikovat své první rozhraní API ve službě API Management.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/15/2017
ms.author: apimpm
ms.openlocfilehash: a72e870b1c98ec363b217030cf1e2f61ceaad5d9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
---
# <a name="import-and-publish-your-first-api"></a>Import a publikování vašeho prvního rozhraní API 

Tento kurz ukazuje, jak importovat back-endové rozhraní API „specifikace OpenAPI“ nacházející se na stránce http://conferenceapi.azurewebsites.net?format=json. Toto back-endové rozhraní API poskytuje Microsoft a hostuje ho na Azure. 

Po importu back-endového rozhraní API do služby API Management (APIM) se rozhraní API služby APIM stane průčelím pro back-endové rozhraní API. V době importu back-endového rozhraní API je zdrojové rozhraní API i rozhraní API služby APIM identické. Služba APIM umožňuje přizpůsobit průčelí vašim potřebám bez zásahu do back-endového rozhraní API. Další informace najdete v tématu [Transformace a ochrana vašeho rozhraní API](transform-api.md). 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Importovat vaše první rozhraní API
> * Testovat rozhraní API na portálu Azure Portal
> * Testovat rozhraní API na portálu pro vývojáře

![Nové rozhraní API](./media/api-management-get-started/created-api.png)

## <a name="prerequisites"></a>Požadavky

Projděte si následující rychlý start: [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-api"> </a>Import a publikování back-endového rozhraní API

Tato část ukazuje, jak importovat a publikovat back-endové rozhraní API specifikace OpenAPI.
 
1. V části **API MANAGEMENT** vyberte **rozhraní API**.
2. Vyberte ze seznamu **Specifikace OpenAPI**.

    ![Vytvoření rozhraní API](./media/api-management-get-started/create-api.png)

    Hodnoty rozhraní API můžete nastavit při vytváření nebo později na kartě **Nastavení**. Červená hvězdička vedle pole značí, že dané pole musíte vyplnit.

    |Nastavení|Hodnota|Popis|
    |---|---|---|
    |**Specifikace OpenAPI**|http://conferenceapi.azurewebsites.net?format=json|Odkazuje na službu implementující rozhraní API. Služba API Management na tuto adresu směruje požadavky.|
    |**Zobrazovaný název**|*Demo Conference API*|Pokud po zadání adresy URL služby stisknete tabulátor, služba APIM toto pole vyplní podle informací ze souboru JSON. <br/>Tento název se zobrazí na portálu pro vývojáře.|
    |**Název**|*demo-conference-api*|Jedinečný název pro rozhraní API. <br/>Pokud po zadání adresy URL služby stisknete tabulátor, služba APIM toto pole vyplní podle informací ze souboru JSON.|
    |**Popis**|Volitelný popis pro rozhraní API.|Pokud po zadání adresy URL služby stisknete tabulátor, služba APIM toto pole vyplní podle informací ze souboru JSON.|
    |**Schéma URL**|*HTTPS*|Určuje, které protokoly je možné použít pro přístup k rozhraní API. |
    |**Přípona adresy URL rozhraní API**|*conference*|Přípona se připojí k základní adrese URL služby API Management. Služba API Management rozlišuje rozhraní API podle jejich přípony, proto musí být přípona jedinečná pro každé rozhraní API daného vydavatele.|
    |**Produkty**|*Unlimited*|Produkty jsou sdruženími jednoho nebo více rozhraní API. Mezi produkty můžete zahrnout několik rozhraní API a nabídnout je vývojářům prostřednictvím portálu pro vývojáře. <br/>Rozhraní API publikujete tak, že ho přidružíte k produktu (v tomto případě *Unlimited*). Pokud chcete toto nové rozhraní API přidružit k produktu, zadejte název tohoto produktu (můžete to také udělat později na stránce **Nastavení**). Pokud chcete rozhraní API přiřadit k více produktům, můžete tento krok opakovat několikrát.<br/>Přístup k rozhraní API získají vývojáři až po přihlášení k odběru produktu. Po přihlášení k odběru získají klíč předplatného, který je možné použít pro jakékoli rozhraní API v příslušném produktu. <br/> Pokud jste vytvořili instanci služby APIM, jste už správcem a jste tedy přihlášení k odběru všech produktů.<br/> Ve výchozím nastavení každá instance služby API Management obsahuje dva ukázkové produkty: **Starter** a **Unlimited**. |
    |Chcete vytvořit verzi tohoto rozhraní API?||Více informací o správě verzí najdete v tématu [Publikování několika verzí vašeho rozhraní API](api-management-get-started-publish-versions.md).|
    
    >[!NOTE]
    > Pokud chcete rozhraní API publikovat, musíte ho přidružit k produktu. Můžete to provést ze **stránky Nastavení**.
    
3. Vyberte **Vytvořit**.

## <a name="test-the-new-apim-api-in-the-azure-portal"></a>Testování nového rozhraní API služby APIM na portálu Azure Portal

Operaci můžete volat přímo z portálu Azure Portal, který nabízí pohodlný způsob pro zobrazení a testování operací v rozhraní API.  
1. Vyberte rozhraní API, které jste vytvořili v předchozím kroku (z karty **Rozhraní API**).
2. Klikněte na kartu **Test**.  ![Test rozhraní API](./media/api-management-get-started/test-api.png)
3. Klikněte na možnost **GetSpeakers**.
    Stránka zobrazí pole pro parametry dotazu. V tomto případě ale žádné neexistují. Stránka také zobrazí pole pro hlavičky. Jednou z hlaviček je klíč „Ocp-Apim-Subscription-Key“. Je to klíč pro přihlášení k odběru produktu, který je k tomuto rozhraní API přidružený. Klíč se vyplní automaticky.
4. Stiskněte **Odeslat**.

    Back-end předá odpověď **200 OK** a nějaká data.

## <a name="call-operation"></a>Volání operace z portálu pro vývojáře

Pokud chcete otestovat rozhraní API, je možné operace také vyvolat z **Portálu pro vývojáře**. 

1. Vyberte **Demo Conference API**.
2. Klikněte na **GetSpeakers**.
    
    Stránka zobrazí pole pro parametry dotazu. V tomto případě ale žádné neexistují. Stránka také zobrazí pole pro hlavičky. Jednou z hlaviček je klíč „Ocp-Apim-Subscription-Key“. Je to klíč pro přihlášení k odběru produktu, který je k tomuto rozhraní API přidružený. Pokud jste vytvořili instanci služby APIM, jste už správcem a klíč se tedy vyplní automaticky.
3. Stiskněte **Vyzkoušet**.
4. Stiskněte **Odeslat**.
    
    Po vyvolání operace zobrazí portál pro vývojáře odpovědi.  

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Importovat vaše první rozhraní API
> * Testovat rozhraní API na portálu Azure Portal
> * Testovat rozhraní API na portálu pro vývojáře

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Vytvoření a publikování produktu](api-management-howto-add-products.md)

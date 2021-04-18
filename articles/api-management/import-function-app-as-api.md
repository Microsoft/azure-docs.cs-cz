---
title: Import Function App Azure jako rozhraní API v API Management
titleSuffix: Azure API Management
description: V tomto článku se dozvíte, jak naimportovat Function App Azure do Azure API Management jako rozhraní API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/16/2021
ms.author: apimpm
ms.openlocfilehash: 8e8047fc6865bab8f4aac2315b269bf7526b1e42
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2021
ms.locfileid: "107599225"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Naimportujete aplikaci Azure Functions jako rozhraní API ve službě Azure API Management

Azure API Management podporuje import aplikací Azure Functions jako nových rozhraní API nebo jejich připojení k existujícím rozhraním API. Tento proces v aplikaci Azure Functions automaticky vygeneruje klíč hostitele, který se pak přiřadí k pojmenované hodnotě ve službě Azure API Management.

Tento článek vás provede importem a testováním Function App Azure jako rozhraní API ve službě Azure API Management. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Import aplikace Azure Function App jako rozhraní API
> * Připojení aplikace Azure Function App k rozhraní API
> * Zobrazení klíče hostitele nové aplikace Azure Function App a pojmenované hodnoty ve službě Azure API Management
> * Testovat rozhraní API na portálu Azure Portal

## <a name="prerequisites"></a>Požadavky

* Dokončete průvodce [vytvořením instance služby Azure API Management](get-started-create-service-instance.md) .
* Zkontrolujte, jestli máte v předplatném aplikaci Azure Functions. Další informace najdete v tématu [Vytvoření aplikace Azure Function App](../azure-functions/functions-get-started.md). Funkce musí mít aktivační událost HTTP a úroveň autorizace nastavenou na *anonymní* nebo *funkční*.

> [!NOTE]
> K importu a správě rozhraní API můžete použít rozšíření API Management pro Visual Studio Code. Pomocí [kurzu rozšíření API Management](visual-studio-code-tutorial.md) nainstalujte a začněte.

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a>Import aplikace Azure Function App jako nového rozhraní API

Podle následujícího postupu vytvoříte z aplikace Azure Function App nové rozhraní API.

1. V Azure Portal přejděte do služby API Management a v nabídce vyberte **rozhraní API** .

2. V seznamu **Přidat nové rozhraní API** vyberte **Function App**.

    :::image type="content" source="./media/import-function-app-as-api/add-01.png" alt-text="Snímek obrazovky, který zobrazuje dlaždici Function App.":::

3. Klikněte na **Procházet** a vyberte funkce, které chcete importovat.

    :::image type="content" source="./media/import-function-app-as-api/add-02.png" alt-text="Snímek obrazovky, který zvýrazní tlačítko pro procházení.":::

4. Kliknutím na část **Function App** zobrazte seznam dostupných aplikací Function App, ze kterých si můžete vybrat.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Snímek obrazovky, který zvýrazní oddíl Function App.":::

5. Vyhledejte aplikaci Function App, ze které chcete importovat funkce, klikněte na ni a stiskněte **Vybrat**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Snímek obrazovky, který zvýrazní Function App chcete importovat funkce a tlačítko vybrat.":::

6. Vyberte funkce, které chcete importovat, a klikněte na **Vybrat**.
    * Můžete importovat jenom funkce založené na triggeru HTTP s možnostmi *anonymní* *nebo autorizační* úrovně.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Snímek obrazovky, který zvýrazní funkce, které se mají importovat, a tlačítko pro výběr.":::

7. Přepněte do **úplného** zobrazení a přiřaďte **Produkt** k novému rozhraní API. 
8. V případě potřeby zadejte další pole během vytváření nebo je nakonfigurujte později prostřednictvím karty **Nastavení** . 
    * Nastavení jsou vysvětlena v kurzu [importu a publikování prvního rozhraní API](import-and-publish.md#import-and-publish-a-backend-api) .

    >[!NOTE]
    > Produkty jsou asociace jednoho nebo více rozhraní API nabízených vývojářům prostřednictvím portálu pro vývojáře. Nejdřív se vývojáři musí přihlásit k odběru produktu, aby získali přístup k rozhraní API. Po přihlášení k odběru Získá klíč předplatného pro jakékoli rozhraní API v tomto produktu. Jako autor instance API Management jste správce a přihlásili jste se k odběru každého produktu ve výchozím nastavení.
    >
    > Každá instance API Management obsahuje dvě výchozí ukázkové produkty:
    > - **Starter**
    > - **Unlimited**

9. Klikněte na **Vytvořit**.

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a>Připojení aplikace Azure Function App k existujícímu rozhraní API

Podle následujícího postupu připojíte aplikaci Azure Function App k existujícímu rozhraní API.

1. Ve vaší instanci služby **Azure API Management** vyberte v nabídce na levé straně **Rozhraní API**.

2. Zvolte rozhraní API, do kterého chcete importovat aplikaci Azure Function App. Klikněte na **...** a v místní nabídce vyberte **Importovat**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-1.png" alt-text="Snímek obrazovky, který zvýrazní možnost nabídky Import":::

3. Klikněte na dlaždici **Function App**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-2.png" alt-text="Snímek obrazovky, který zvýrazní dlaždici Function App.":::

4. V automaticky otevíraném okně klikněte na **Procházet**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-3.png" alt-text="Snímek obrazovky zobrazující tlačítko pro procházení":::

5. Kliknutím na část **Function App** zobrazte seznam dostupných aplikací Function App, ze kterých si můžete vybrat.

    :::image type="content" source="./media/import-function-app-as-api/add-03.png" alt-text="Snímek obrazovky, který zvýrazní seznam aplikací Function App":::

6. Vyhledejte aplikaci Function App, ze které chcete importovat funkce, klikněte na ni a stiskněte **Vybrat**.

    :::image type="content" source="./media/import-function-app-as-api/add-04.png" alt-text="Snímek obrazovky, který zvýrazní Function App chcete importovat funkce.":::

7. Vyberte funkce, které chcete importovat, a klikněte na **Vybrat**.

    :::image type="content" source="./media/import-function-app-as-api/add-05.png" alt-text="Snímek obrazovky, který zvýrazní funkce, které byste chtěli importovat.":::

8. Klikněte na **importovat**.

    :::image type="content" source="./media/import-function-app-as-api/append-function-api-4.png" alt-text="Připojení z aplikace Function App":::

## <a name="authorization"></a><a name="authorization"></a> Udělován

Při importu aplikace Azure Function App se automaticky vygeneruje:

* Klíč hostitele v rámci Function App s názvem APIM-{*název instance služby Azure API Management*}
* Pojmenovaná hodnota v rámci instance služby Azure API Management s názvem {*název instance azure Function App*} – klíč, který obsahuje vytvořený klíč hostitele.

Pro rozhraní API vytvořená po 4. dubnu 2019 se hostitelský klíč předává v požadavcích HTTP od API Management do Function App v hlavičce. Starší rozhraní API předejte klíč hostitele jako [parametr dotazu](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). Toto chování můžete změnit prostřednictvím `PATCH Backend` [volání REST API](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) na entitě *back-end* , která je přidružená k Function App.

> [!WARNING]
> Když odeberete nebo změníte hodnotu klíče hostitele Azure Function App nebo pojmenovaná hodnota Azure API Management, přeruší se komunikace mezi službami. Tyto hodnoty se nesynchronizují automaticky.
>
> Pokud potřebujete obměnit klíč hostitele, nezapomeňte upravit také pojmenovanou hodnotu ve službě Azure API Management.

### <a name="access-azure-function-app-host-key"></a>Přístup ke klíči hostitele aplikace Azure Function App

1. Přejděte k vaší instanci aplikace Azure Function App.

    :::image type="content" source="./media/import-function-app-as-api/keys-01.png" alt-text="Snímek obrazovky, který zvýrazní výběr instance aplikace Function App":::

2. V části **funkce** v navigační nabídce vedle sebe vyberte možnost **klíče aplikace**.

    :::image type="content" source="./media/import-function-app-as-api/keys-02b.png" alt-text="Snímek obrazovky, který zvýrazní možnost nastavení aplikací Function App":::

3. Klíče najdete v části **klíče hostitele** .

    :::image type="content" source="./media/import-function-app-as-api/keys-03.png" alt-text="Snímek obrazovky, který zvýrazní oddíl klíčů hostitele.":::

### <a name="access-the-named-value-in-azure-api-management"></a>Přístup k pojmenované hodnotě ve službě Azure API Management

Přejděte k vaší instanci služby Azure API Management a v nabídce na levé straně vyberte **Pojmenované hodnoty**. Tady je uložený klíč aplikace Azure Function App.

:::image type="content" source="./media/import-function-app-as-api/api-named-value.png" alt-text="Přidání z aplikace Function App":::

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Otestujte nové rozhraní API v Azure Portal

Operace můžete volat přímo z webu Azure Portal. Web Azure Portal nabízí pohodlný způsob zobrazení a testování operací v rozhraní API.  

:::image type="content" source="./media/import-function-app-as-api/test-api.png" alt-text="Snímek obrazovky, který zvýrazní postup testu.":::

1. Vyberte rozhraní API, které jste vytvořili v předchozí části.

2. Vyberte kartu **Test**.

3. Vyberte operaci, kterou chcete otestovat.

    * Stránka zobrazí pole pro parametry dotazu a záhlaví. 
    * Jedna z hlaviček je "OCP-APIM-Subscription-Key" pro klíč předplatného produktu, který je přidružený k tomuto rozhraní API. 
    * Jako tvůrce instance API Management jste už správce, takže se klíč vyplní automaticky. 

4. Vyberte **Odeslat**.

    * Po úspěšném dokončení testu odpoví back-end s **200 OK** a dalšími daty.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)

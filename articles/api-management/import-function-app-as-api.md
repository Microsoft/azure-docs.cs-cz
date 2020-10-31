---
title: Import Function App Azure jako rozhraní API v API Management
titleSuffix: Azure API Management
description: V tomto kurzu se dozvíte, jak importovat aplikaci Azure Function App do služby Azure API Management jako rozhraní API.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/22/2020
ms.author: apimpm
ms.openlocfilehash: e53d0aa8d76d32127d5fde4eaf5dbe7a455dc3aa
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100775"
---
# <a name="import-an-azure-function-app-as-an-api-in-azure-api-management"></a>Naimportujete aplikaci Azure Functions jako rozhraní API ve službě Azure API Management

Azure API Management podporuje import aplikací Azure Functions jako nových rozhraní API nebo jejich připojení k existujícím rozhraním API. Tento proces v aplikaci Azure Functions automaticky vygeneruje klíč hostitele, který se pak přiřadí k pojmenované hodnotě ve službě Azure API Management.

Tento článek vás provede importem aplikace Azure Function App jako rozhraní API ve službě Azure API Management. Také popisuje proces testování.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Import aplikace Azure Function App jako rozhraní API
> * Připojení aplikace Azure Function App k rozhraní API
> * Zobrazení klíče hostitele nové aplikace Azure Function App a pojmenované hodnoty ve službě Azure API Management
> * Testovat rozhraní API na portálu Azure Portal
> * Testování rozhraní API na portálu pro vývojáře

## <a name="prerequisites"></a>Předpoklady

* Projděte si rychlý start [Vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
* Zkontrolujte, jestli máte v předplatném aplikaci Azure Functions. Další informace najdete v tématu [Vytvoření aplikace Azure Function App](../azure-functions/functions-create-first-azure-function.md#create-a-function-app). Musí obsahovat funkce s triggerem HTTP a úrovní autorizace nastavenou na *Anonymní* nebo *Funkce* .

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="import-an-azure-function-app-as-a-new-api"></a><a name="add-new-api-from-azure-function-app"></a>Import aplikace Azure Function App jako nového rozhraní API

Podle následujícího postupu vytvoříte z aplikace Azure Function App nové rozhraní API.

1. V Azure Portal přejděte do služby API Management a v nabídce vyberte **rozhraní API** .

2. V seznamu **Přidat nové rozhraní API** vyberte **Function App** .

    ![Snímek obrazovky, který zobrazuje dlaždici Function App.](./media/import-function-app-as-api/add-01.png)

3. Klikněte na **Procházet** a vyberte funkce, které chcete importovat.

    ![Snímek obrazovky, který zvýrazní tlačítko pro procházení.](./media/import-function-app-as-api/add-02.png)

4. Kliknutím na část **Function App** zobrazte seznam dostupných aplikací Function App, ze kterých si můžete vybrat.

    ![Snímek obrazovky, který zvýrazní oddíl Function App.](./media/import-function-app-as-api/add-03.png)

5. Vyhledejte aplikaci Function App, ze které chcete importovat funkce, klikněte na ni a stiskněte **Vybrat** .

    ![Snímek obrazovky, který zvýrazní Function App chcete importovat funkce a tlačítko vybrat.](./media/import-function-app-as-api/add-04.png)

6. Vyberte funkce, které chcete importovat, a klikněte na **Vybrat** .

    ![Snímek obrazovky, který zvýrazní funkce, které se mají importovat, a tlačítko pro výběr.](./media/import-function-app-as-api/add-05.png)

    > [!NOTE]
    > Můžete importovat pouze funkce, které jsou založené na triggeru HTTP a mají úroveň autorizace nastavenou na *Anonymní* nebo *Funkce* .

7. Přepněte do **úplného** zobrazení a přiřaďte **Produkt** k novému rozhraní API. V případě potřeby zadejte další pole během vytváření nebo je nakonfigurujte později tak, že na kartu **Nastavení** nakonfigurujete. Nastavení jsou vysvětlena v kurzu [importu a publikování prvního rozhraní API](import-and-publish.md#import-and-publish-a-backend-api) .
8. Klikněte na **Vytvořit** .

## <a name="append-azure-function-app-to-an-existing-api"></a><a name="append-azure-function-app-to-api"></a>Připojení aplikace Azure Function App k existujícímu rozhraní API

Podle následujícího postupu připojíte aplikaci Azure Function App k existujícímu rozhraní API.

1. Ve vaší instanci služby **Azure API Management** vyberte v nabídce na levé straně **Rozhraní API** .

2. Zvolte rozhraní API, do kterého chcete importovat aplikaci Azure Function App. Klikněte na **...** a v místní nabídce vyberte **Importovat** .

    ![Snímek obrazovky, který zvýrazní možnost nabídky Import](./media/import-function-app-as-api/append-01.png)

3. Klikněte na dlaždici **Function App** .

    ![Snímek obrazovky, který zvýrazní dlaždici Function App.](./media/import-function-app-as-api/append-02.png)

4. V automaticky otevíraném okně klikněte na **Procházet** .

    ![Snímek obrazovky zobrazující tlačítko pro procházení](./media/import-function-app-as-api/append-03.png)

5. Kliknutím na část **Function App** zobrazte seznam dostupných aplikací Function App, ze kterých si můžete vybrat.

    ![Snímek obrazovky, který zvýrazní seznam aplikací Function App](./media/import-function-app-as-api/add-03.png)

6. Vyhledejte aplikaci Function App, ze které chcete importovat funkce, klikněte na ni a stiskněte **Vybrat** .

    ![Snímek obrazovky, který zvýrazní Function App chcete importovat funkce.](./media/import-function-app-as-api/add-04.png)

7. Vyberte funkce, které chcete importovat, a klikněte na **Vybrat** .

    ![Snímek obrazovky, který zvýrazní functnios, která se má importovat](./media/import-function-app-as-api/add-05.png)

8. Klikněte na **importovat** .

    ![Připojení z aplikace Function App](./media/import-function-app-as-api/append-04.png)

## <a name="authorization"></a><a name="authorization"></a> Udělován

Při importu aplikace Azure Function App se automaticky vygeneruje:

* Klíč hostitele v rámci Function App s názvem APIM-{ *název instance služby Azure API Management* }
* Pojmenovaná hodnota v rámci instance služby Azure API Management s názvem { *název instance azure Function App* } – klíč, který obsahuje vytvořený klíč hostitele.

Pro rozhraní API vytvořená po 4. dubnu 2019 se hostitelský klíč předává v požadavcích HTTP od API Management do Function App v hlavičce. Starší rozhraní API předejte klíč hostitele jako [parametr dotazu](../azure-functions/functions-bindings-http-webhook-trigger.md#api-key-authorization). Toto chování může být změněno pomocí `PATCH Backend` [REST API volání](/rest/api/apimanagement/2019-12-01/backend/update#backendcredentialscontract) na entitu *back-end* , která je přidružená k Function App.

> [!WARNING]
> Odebráním nebo změnou hodnoty klíče hostitele aplikace Azure Function App nebo pojmenované hodnoty ve službě Azure API Management se přeruší komunikace mezi službami. Tyto hodnoty se nesynchronizují automaticky.
>
> Pokud potřebujete obměnit klíč hostitele, nezapomeňte upravit také pojmenovanou hodnotu ve službě Azure API Management.

### <a name="access-azure-function-app-host-key"></a>Přístup ke klíči hostitele aplikace Azure Function App

1. Přejděte k vaší instanci aplikace Azure Function App.

2. V přehledu vyberte **Nastavení aplikace Function App** .

    ![Snímek obrazovky, který zvýrazní možnost nastavení aplikací Function App](./media/import-function-app-as-api/keys-02-a.png)

3. Klíč se nachází v části **Klíče hostitele** .

    ![Snímek obrazovky, který zvýrazní oddíl klíčů hostitele.](./media/import-function-app-as-api/keys-02-b.png)

### <a name="access-the-named-value-in-azure-api-management"></a>Přístup k pojmenované hodnotě ve službě Azure API Management

Přejděte k vaší instanci služby Azure API Management a v nabídce na levé straně vyberte **Pojmenované hodnoty** . Tady je uložený klíč aplikace Azure Function App.

![Přidání z aplikace Function App](./media/import-function-app-as-api/keys-01.png)

## <a name="test-the-new-api-in-the-azure-portal"></a><a name="test-in-azure-portal"></a> Otestujte nové rozhraní API v Azure Portal

Operace můžete volat přímo z webu Azure Portal. Web Azure Portal nabízí pohodlný způsob zobrazení a testování operací v rozhraní API.  

1. Vyberte rozhraní API, které jste vytvořili v předchozí části.

2. Vyberte kartu **Test** .

3. Vyberte operaci.

    Stránka zobrazí pole pro parametry dotazu a pole pro hlavičky. Jedna z hlaviček je **OCP-APIM-Subscription-Key** , pro klíč předplatného produktu, který je spojený s tímto rozhraním API. Pokud jste vytvořili instanci služby API Management, jste už správcem a klíč se tedy vyplní automaticky. 

4. Vyberte **Poslat** .

    Back-end předá odpověď **200 OK** a nějaká data.

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Transformace a ochrana publikovaného rozhraní API](transform-api.md)

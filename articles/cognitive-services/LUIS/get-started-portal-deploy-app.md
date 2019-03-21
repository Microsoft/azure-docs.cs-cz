---
title: 'Rychlý start: Nasazení aplikací s využitím služby LUIS portálu'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Jakmile bude aplikace připravená utterance predikcí vrátit klientské aplikace, jako je chatovací robot, budete muset nasadit aplikaci do koncového bodu předpovědi. V tomto rychlém startu zjistíte, jak nasadit aplikaci vytvořením prostředku koncového bodu predikcí, přiřazení k aplikaci, školení aplikace a publikování aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 0cf7464188bb502d77c673284cba226ce91a5725
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259716"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Rychlý start: Nasazení aplikace na portálu služby LUIS

Jakmile bude aplikace připravená utterance predikcí vrátit klientské aplikace, jako je chatovací robot, budete muset nasadit aplikaci do koncového bodu předpovědi. 

V tomto rychlém startu zjistíte, jak nasadit aplikaci vytvořením prostředku koncového bodu predikcí, přiřazení k aplikaci, školení aplikace a publikování aplikace. 

## <a name="prerequisites"></a>Požadavky

* [Předplatné Azure](https://azure.microsoft.com/free).
* Dokončení [předchozím rychlém startu portal](get-started-portal-build-app.md) nebo [stáhnout a naimportovat ji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json). 


## <a name="create-endpoint-resource"></a>Vytvoření prostředku koncového bodu

Prostředek endpoint predikcí se vytvoří na webu Azure Portal. Tento prostředek by měla sloužit pouze pro dotazy předpovědi s koncový bod. Tento prostředek nepoužívají vytváření změny do aplikace. 

1. Přihlaste se k  **[webu Azure portal](https://ms.portal.azure.com/)**. 

1. Vyberte zelené **+** přihlásit v horním levém panelu a vyhledejte `Cognitive Services` na webu Marketplace, pak ho vyberte. 

1. Nakonfigurování odběru s následujícím nastavením:

    |Nastavení|Hodnota|Účel|
    |--|--|--|
    |Název|`my-cognitive-service-resource`|Název prostředku Azure. Když přiřadíte prostředek aplikace portálu služby LUIS musíte tento název.|
    |Předplatné|Vaše předplatné|Vyberte jednu z předplatných přidružených k vašemu účtu.|
    |Umístění|**Západní USA**|Oblast azure pro tento prostředek.|
    |Cenová úroveň|**S0**|Výchozí cenová úroveň pro tento prostředek.|
    |Skupina prostředků|`my-cognitive-service-resource-group`|Vytvořte novou skupinu prostředků pro všechny vaše prostředky služby cognitive Services. Jakmile budete hotovi s prostředky, můžete odstranit skupinu prostředků pro vyčištění vašeho předplatného. | 

    ![Volbou Azure API](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png) 

1. Vyberte **vytvořit** a vytvoří prostředek služby Azure. 

    V další části se dozvíte, jak se připojit k aplikaci LUIS na portálu služby LUIS tento nový prostředek. 

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Klíč prostředku přiřadit aplikace LUIS na portálu služby LUIS

Pokaždé, když vytvoříte nový prostředek pro LUIS, musíte přiřadit zdroj na aplikaci LUIS. Po přiřazení, nebudete muset znovu proveďte tento krok, není-li vytvořit nový prostředek. Můžete vytvořit nový prostředek rozbalte oblasti vaší aplikace nebo pro podporu větší počet dotazy předpovědi. 

1. Přihlaste se k [LUIS portál](https://www.luis.ai), zvolte **myEnglishApp** aplikace ze seznamu aplikací.

1. Vyberte **spravovat** v pravé horní nabídce vyberte **klíče a koncových bodů**.

1. Chcete-li přidat LUIS, vybrat **přiřazení prostředků +**.

    [![Přiřadit prostředek vaší aplikace](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Vyberte název vašeho tenanta, předplatné a prostředků pak vyberte **přiřazení prostředků**. 

    ![Přiřadit prostředek vaší aplikace](./media/get-started-portal-deploy-app/assign-resource.png)

1. Nalezení nový řádek v tabulce a zkopírujte adresu URL koncového bodu. Je vytvořen správně provést požadavek HTTP GET na koncový bod rozhraní LUIS API pro předpověď. 

## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace 

By měl trénování vždy, když budete chtít otestovat. Vždy, když chcete právě natrénovaného verze k dispozici pro klientské aplikace z modulu runtime koncový bod predikcí, měli byste publikovat aplikace. 

1. Pokud je aplikace Nezkušený, vyberte **Train** z horní části, klikněte pravým tlačítkem myši nabídky.

1. Vyberte **publikovat** z horní části, klikněte pravým tlačítkem myši nabídky. Přijměte výchozí nastavení prostředí a vyberte **publikovat**.

1. Jakmile se zobrazí zelená úspěch oznamovací pruh v horní části okna prohlížeče, vyberte **najdete v seznamu koncových bodů** odkaz. 

    ![Oznamovací pruh publikování proběhlo úspěšně aplikace v prohlížeči](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Tím přejdete **klíče a koncových bodů nastavení** stránky. V seznamu přiřazených zdrojů a odpovídající koncový bod adresy URL je v dolní části stránky. 

1. Vyberte adresu URL koncového bodu, který je přidružený k nový název prostředku. Tím se otevře webový prohlížeč s adresou URL správně vytvořený provádět **získat** požadavek na koncový bod runtime předpovědi. 

1. `q=` Na konci adresy URL je zkratka pro **dotazu** a kde utterance uživatele se připojí požadavek GET. Po `q=`, zadejte stejný uživatel utterance použit na koncích předchozím rychlém startu:

    ```Is there a form named hrf-234098```

    Prohlížeč odpověď je stejný JSON, obdrží klientské aplikace:

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    Tato odpověď získáte další informace než výchozí **Test** podokno v předchozím kurzu. Pokud chcete zobrazit tento stejnou úroveň informací v testovací podokno, aplikace se musí publikovat. Vyberte v podokně testu **porovnat s publikované**. Použití **zobrazení zobrazit JSON** v podokně publikovaná ve testu chcete zobrazit ve stejném formátu JSON jako v předchozím kroku. To umožňuje porovnat aktuální aplikaci, kterou právě pracujete a aplikace, která se publikuje na koncový bod.

    [![Porovnání právě se upravuje oproti publikované verze aplikace](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Po dokončení tohoto rychlého startu zvolte **Moje aplikace** v horní navigační nabídce. Poté vyberte zaškrtávací políčko vlevo aplikace ze seznamu a vyberte **odstranit** z kontextu nástrojů nad seznamem. 

[![Odstranit aplikaci ze seznamu aplikací](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Identifikovat běžné záměry a entity](luis-tutorial-prebuilt-intents-entities.md)
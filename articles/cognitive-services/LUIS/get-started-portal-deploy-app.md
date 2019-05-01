---
title: 'Rychlý start: Nasazení aplikace pomocí portálu služby LUIS'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Informace o nasazení aplikace LUIS ke koncovému bodu predikcí, až bude aplikace připravená utterance predikcí vrátí do klientské aplikace, jako je chatovací robot. Tento rychlý start vás provede vytvořením prostředku koncového bodu predikcí, přiřazení k aplikaci, školení aplikace a publikování aplikace nasazení aplikace.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 9a54cad9212bdb514f7742121909221863ba75e3
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64713517"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Rychlý start: Nasazení aplikace na portálu služby LUIS

Když aplikace LUIS je připraven k předpovědi utterance vrátit klientské aplikaci (například chatovací robot), budete muset nasadit aplikaci do koncového bodu předpovědi.

V tomto rychlém startu zjistíte, jak nasadit aplikaci. Vytvoření predikcí koncový bod prostředku, prostředek přiřadit aplikaci, trénování aplikace a publikování aplikace.

## <a name="prerequisites"></a>Požadavky

* Získat [předplatného Azure](https://azure.microsoft.com/free).
* Dokončení [předchozím rychlém startu portal](get-started-portal-build-app.md) nebo [stáhnout a naimportovat ji](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Vytvoření prostředku koncového bodu

Na webu Azure Portal vytvoříte koncový bod prostředku předpovědi. Tento prostředek by měla sloužit pouze pro dotazy předpovědi s koncový bod. Tento prostředek nepoužívají vytváření změny do aplikace.

1. Přihlaste se k webu [Azure Portal](https://ms.portal.azure.com/).

1. Vyberte zelené **+** Přihlaste se na levém panelu. Vyhledejte `Cognitive Services` na webu Marketplace a vyberte ji.

1. Nakonfigurování odběru s následujícím nastavením:

   |Nastavení|Hodnota|Účel|
   |--|--|--|
   |Název|`my-cognitive-service-resource`|Název prostředku Azure. Když přiřadíte prostředek aplikace portálu služby LUIS musíte tento název.|
   |Předplatné|Vaše předplatné|Vyberte jednu z předplatných přidružených k vašemu účtu.|
   |Location|**Západní USA**|Oblast Azure pro tento prostředek.|
   |Cenová úroveň|**S0**|Výchozí cenová úroveň pro tento prostředek.|
   |Skupina prostředků|`my-cognitive-service-resource-group`|Vytvořte novou skupinu prostředků pro všechny vaše prostředky služby cognitive Services. Jakmile budete hotovi s prostředky, můžete odstranit skupinu prostředků pro vyčištění vašeho předplatného. |
   | | | |

   ![Volbou Azure API](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Vyberte **vytvořit** a vytvoří prostředek služby Azure.

   V další části se dozvíte, jak se připojit k aplikaci LUIS na portálu služby LUIS tento nový prostředek.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Klíč prostředku přiřadit aplikace LUIS na portálu služby LUIS

Pokaždé, když vytvoříte nový prostředek pro LUIS, musíte přiřadit zdroj na aplikaci LUIS. Po přiřazení, nebudete muset znovu proveďte tento krok, není-li vytvořit nový prostředek. Můžete vytvořit nový prostředek rozbalte oblasti vaší aplikace nebo pro podporu větší počet dotazy předpovědi.

1. Přihlaste se k [LUIS portál](https://www.luis.ai) a zvolte **myEnglishApp** aplikace ze seznamu aplikací.

1. Vyberte **spravovat** v pravé horní nabídce a potom vyberte **klíče a koncových bodů**.

1. Chcete-li přidat LUIS, **přiřazení prostředků +**.

   [![Přiřadit prostředek vaší aplikace](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Vyberte název vašeho tenanta, předplatné a prostředků. Vyberte **přiřazení prostředků**.

   ![Přiřadit prostředek vaší aplikace](./media/get-started-portal-deploy-app/assign-resource.png)

1. Nalezení nový řádek v tabulce a zkopírujte adresu URL koncového bodu. Je správně vytvořený provést `HTTP GET` požadavek na koncový bod rozhraní LUIS API pro předpověď.

## <a name="train-and-publish-the-app"></a>Trénování a publikování aplikace

Jakmile budete připraveni k testování, trénování aplikace. Publikování aplikace, pokud chcete, aby právě natrénovaného verze k dispozici pro klientské aplikace z modulu runtime předpovědi koncový bod.

1. Pokud je aplikace Nezkušený, vyberte **Train** z nabídky v pravém horním rohu.

1. Vyberte **publikovat** z nabídky. Přijměte výchozí nastavení prostředí a vyberte **publikovat**.

1. Jakmile se zobrazí zelená úspěch oznamovací pruh v horní části okna prohlížeče, vyberte **najdete v seznamu koncových bodů**.

   ![Oznamovací pruh publikování proběhlo úspěšně aplikace v prohlížeči](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. Na **klíče a koncových bodů nastavení** stránce, najdete v dolní části seznamu přiřazených zdrojů a odpovídající adresy URL koncových bodů.

1. Vyberte adresu URL koncového bodu, který je přidružený k nový název prostředku. Tato akce otevře webový prohlížeč s adresou URL správně vytvořený provádět `GET` požadavek na koncový bod runtime předpovědi.

1. `q=` Na konci adresy URL je zkratka pro **dotazu** a kde utterance uživatele se připojí požadavek GET. Po `q=`, zadejte stejný uživatel utterance použit na koncích předchozím rychlém startu:

    ```Is there a form named hrf-234098```

    Prohlížeč zobrazí odpověď, která je stejný JSON, obdrží klientské aplikace:

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

    Tato odpověď obsahuje bližší informace než výchozí testovací podokno v předchozím kurzu. Pokud chcete zobrazit tento stejnou úroveň informací v testovací podokno, musíte publikovat aplikace. Po publikování aplikace, vyberte **porovnat s publikované** v testovací podokno. Použití **zobrazení zobrazit JSON** v publikované testovací podokno, které chcete zobrazit ve stejném formátu JSON jako v předchozím kroku. Tímto způsobem můžete porovnat aktuální aplikaci, kterou právě pracujete s aplikací, který je publikován do koncového bodu.

    [![Porovnání právě se upravuje oproti publikované verze aplikace](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až to budete mít v tomto rychlém startu, vyberte **Moje aplikace** v horní navigační nabídce. Ze seznamu vybrat aplikace zaškrtněte políčko a potom vyberte **odstranit** z kontextu nástrojů nad seznamem.

[![Odstranit aplikaci ze seznamu aplikací](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Identifikovat běžné záměry a entity](luis-tutorial-prebuilt-intents-entities.md)

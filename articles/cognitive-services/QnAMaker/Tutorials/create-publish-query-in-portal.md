---
title: Vytvářet, publikovat a odpovědět v nástroje QnA Maker
titleSuffix: Azure Cognitive Services
description: Vytvoření nové znalostní báze pomocí otázek a odpovědí z veřejné nejčastějších dotazů založených na webu. Uložit, trénování a publikování znalostní báze. Po publikování znalostní báze dotaz odesílat a přijímat odpovědi pomocí příkazu cURL. Potom vytváření robotů a otestovat bot se stejnou otázku.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: a13e0cb0e594571344b16d007ef13475b384b73d
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692994"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Kurz: Z portálu QnA Maker vytvoření znalostní báze

Vytvoření nové znalostní báze pomocí otázek a odpovědí z veřejné nejčastějších dotazů založených na webu. Uložit, trénování a publikování znalostní báze. Po publikování znalostní báze dotaz odesílat a přijímat odpovědi pomocí příkazu cURL. Potom vytváření robotů a otestovat bot se stejnou otázku. 

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Vytvoření znalostní báze na portálu pro nástroj QnA Maker.
> * Prohlížení, uložení a trénování znalostní báze.
> * Publikování znalostní báze.
> * Používáme nástroj cURL k dotazování znalostní báze.
> * Vytváření robotů.
 

> [!NOTE]
> Programové verzi tohoto kurzu je k dispozici s tak získají kompletní řešení z [ **Azure – ukázky/cognitive-services – QnA maker csharp** úložiště GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje existující [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). 

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze 

1. Přihlaste se k portálu služby [QnA Maker](https://www.qnamaker.ai). 

1. V horní nabídce vyberte **Create a knowledge base** (Vytvořit znalostní bázi).

    ![Snímek obrazovky nástroje QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. První krok přeskočte, protože budete používat vaše stávající služba QnA Maker. 

1. Vyberte existující nastavení:  

    |Nastavení|Účel|
    |--|--|
    |ID adresáře Microsoft Azure|Toto ID je přidružená k účtu, který používáte k přihlášení na webu Azure portal a portál QnA Maker. |
    |Azure Subscription name (Název předplatného Azure)|Fakturační účet, ve které jste vytvořili prostředek QnA Maker.|
    |Azure QnA Service (Služba otázek a odpovědí Azure)|Prostředek vaší existující služby QnA Maker.|

    ![Snímek obrazovky nástroje QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Zadejte název vaší znalostní báze `My Tutorial kb`.

    ![Snímek obrazovky nástroje QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Naplnění znalostní báze s následujícím nastavením:  

    |Název nastavení|Hodnota nastavení|Účel|
    |--|--|--|
    |zprostředkovatele identity|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Obsah nejčastějších dotazů na této adrese URL je formátovaný jako otázka, za níž následuje odpověď. Služba QnA Maker může tento formát interpretovat a extrahovat otázky a přidružené odpovědi.|
    |File |_v tomto kurzu se nepoužívá_|Toto nastavení slouží k nahrávání souborů pro otázky a odpovědi. |
    |Chit-chat personality (Charakter konverzace)|Popisný|Díky tomu popisný a příležitostné [posouzení vašich osobnostních](../Concepts/best-practices.md#chit-chat) na běžné otázky a odpovědi. Tyto otázky a odpovědi můžete později upravit. |

    ![Snímek obrazovky nástroje QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Vyberte **Create your KB** (Vytvořit znalostní bázi) a dokončete proces vytváření.

    ![Snímek obrazovky nástroje QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Kontrola, uložení a trénování znalostní báze

1. Zkontrolujte otázky a odpovědi. První stránka obsahuje otázky a odpovědi z příslušné adresy URL. 

    ![Snímek obrazovky nástroje QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. V dolní části tabulky vyberte poslední stránku otázek a odpovědí. Na stránce se zobrazí otázky a odpovědi s charakterem konverzace. 

1. Vyberte z panelu nástrojů nad seznamem otázek a odpovědí **zobrazit možnosti** ikonu a pak vyberte **zobrazit metadata**. Zobrazí metadata značky pro každý otázek a odpovědí. Otázky Chit chat mají **redakční: chit chat** metadat již nastaven. Tato metadata se vrátí do klientské aplikace, spolu s vybranou odpovědí. Klientská aplikace, jako je chatovací robot, slouží k určení interakcí s uživatelem nebo další zpracování tohoto filtrovaných metadat.

    ![Snímek obrazovky nástroje QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. V horní nabídce vyberte **Save and train** (Uložit a natrénovat).

## <a name="publish-to-get-knowledge-base-endpoints"></a>Publikování na získání koncových bodů znalostní báze

V horní nabídce vyberte tlačítko **Publish** (Publikovat). Na stránce publikování vyberte **Publikovat**.

![Snímek obrazovky nástroje QnA Maker portal](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Po publikování znalostní báze se zobrazí na koncový bod.

![Snímek obrazovky nastavení koncového bodu](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Nezavírejte toto **publikovat** stránky. Budete potřebovat později v kurzu k vytváření robotů. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Používáme nástroj cURL k dotazu na odpověď – nejčastější dotazy

1. Vyberte kartu **Curl**. 

    ![Snímek obrazovky Curl kartu](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Zkopírujte text **Curl** kartu a spustíme ji v cURL povolené terminálu nebo příkazového řádku. Hodnota hlavičky autorizace obsahuje text `Endpoint`s koncovou mezeru a potom klávesu.

1. Nahraďte `<Your question>` za `How large can my KB be?` (Jak velká může být moje znalostní báze?). Tato otázka se podobá otázce `How large a knowledge base can I create?` (Jak velkou znalostní bázi můžu vytvořit?), ale není úplně stejná. Služba QnA Maker s využitím zpracování přirozeného jazyka určí, že se jedná o stejné otázky.     

1. Pomocí příkazu cURL, získáte odpověď JSON, a to včetně určení skóre a odpovědí. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   581  100   543  100    38    418     29  0:00:01  0:00:01 --:--:--   447{
      "answers": [
        {
          "questions": [
            "How large a knowledge base can I create?"
          ],
          "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
          "score": 42.81,
          "id": 2,
          "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
          "metadata": []
        }
      ]
    }
    
    ```

    Služba QnA Maker udává mírnou jistotu se skóre 42,81 %.  

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Používáme nástroj cURL k dotazu pro Chit chat odpovědí

1. V terminálu povolené cURL nahraďte `How large can my KB be?` příkazem ukončení konverzace bot od uživatele, jako například `Thank you`.   

1. Pomocí příkazu cURL, získáte odpověď JSON, a to včetně určení skóre a odpovědí. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   525  100   501  100    24    525     25 --:--:-- --:--:-- --:--:--   550{
      "answers": [
        {
          "questions": [
            "Thank you",
            "Thanks",
            "Thnx",
            "Kthx",
            "I appreciate it",
            "Thank you so much",
            "I thank you",
            "My sincere thank"
          ],
          "answer": "You're very welcome.",
          "score": 100.0,
          "id": 109,
          "source": "qna_chitchat_the_friend.tsv",
          "metadata": [
            {
              "name": "editorial",
              "value": "chitchat"
            }
          ]
        }
      ]
    }
   
    ```

    Vzhledem k tomu, že otázka `Thank you` (Děkuji) přesně odpovídá konverzační otázce, služba QnA Maker udává naprostou jistotu se skóre 100. Nástroj QnA Maker, vrátí všechny související dotazy, stejně jako vlastnost metadat obsahující informace o značku metadat Chit konverzace.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Používáme nástroj cURL k dotazu pro výchozí odpověď

Na každou otázku, která není jistí QnA Maker obdrží odpověď výchozí. Tuto odpověď můžete nakonfigurovat na webu Azure Portal. 

1. V terminálu povolené cURL nahraďte `Thank you` s `x`. 

1. Pomocí příkazu cURL, získáte odpověď JSON, a to včetně určení skóre a odpovědí. 

    ```TXT
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   186  100   170  100    16    272     25 --:--:-- --:--:-- --:--:--   297{
      "answers": [
        {
          "questions": [],
          "answer": "No good match found in KB.",
          "score": 0.0,
          "id": -1,
          "metadata": []
        }
      ]
    }
    ```
    
    Nástroj QnA Maker vrátil bodů `0`, což znamená, že bez obav. Také vrátí výchozí odpověď. 

## <a name="create-a-knowledge-base-bot"></a>Vytváření robotů znalostní báze

Další informace najdete v tématu [vytvořit chatovací robot s této znalostní báze](create-qna-bot.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s robotem znalostní báze knowledge base, odeberte skupinu prostředků, `my-tutorial-rg`, chcete-li odebrat všechny prostředky Azure vytvořeny během procesu robota.

Jakmile budete hotovi s znalostní báze na portálu pro nástroj QnA Maker vyberte **Moje znalostních bází**. Vyberte ve znalostní bázi **tento kurz kb**a vyberte ikonu Odstranit úplně vpravo v daném řádku.  

## <a name="next-steps"></a>Další postup

Další informace o podporovaných formátech souborů najdete v tématu [Podporované zdroje dat](../Concepts/data-sources-supported.md). 

Další informace o [charakterech](../Concepts/best-practices.md#chit-chat) konverzace.

Další informace o výchozí odpovědi najdete v části [Nebyla nalezena žádná odpovídající položka](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Vytvoření chatovací robot s této znalostní báze](create-qna-bot.md)
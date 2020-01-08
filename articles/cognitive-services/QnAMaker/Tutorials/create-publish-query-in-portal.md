---
title: 'Kurz: vytvoření, publikování a odpověď v QnA Maker'
titleSuffix: Azure Cognitive Services
description: V tomto kurzu se dozvíte, jak vytvořit novou znalostní bázi s dotazy a odpověďmi z veřejného webového nejčastějších dotazů. Uložte, výuku a publikujte znalostní bázi. Po publikování znalostní báze odešlete otázku a dostanete odpověď pomocí příkazu složeného. Pak vytvořte robot a vyzkoušejte si robota se stejnou otázkou.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 5b2f2decf3d070da2fb58bebd424b4209f353316
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75447389"
---
# <a name="tutorial-from-the-qna-maker-portal-create-a-knowledge-base"></a>Kurz: na portálu QnA Maker vytvořte znalostní bázi.

Vytvoření nové znalostní báze s dotazy a odpověďmi z veřejného webového nejčastějších dotazů. Uložte, výuku a publikujte znalostní bázi. Po publikování znalostní báze odešlete otázku a dostanete odpověď pomocí příkazu složeného. Pak vytvořte robot a vyzkoušejte si robota se stejnou otázkou. 

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Vytvořte znalostní bázi na portálu QnA Maker.
> * Přečtěte si, uložte a školení znalostní báze Knowledge Base.
> * Publikujte znalostní bázi.
> * K dotazování znalostní báze použijte oblé.
> * Vytvořte robota.
 

> [!NOTE]
> Programová verze tohoto kurzu je k dispozici s kompletním řešením z [úložiště GitHub **Azure-Samples/vnímání-Services-qnamakerem-CSharp** ](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje existující [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). 

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze 

1. Přihlaste se k portálu služby [QnA Maker](https://www.qnamaker.ai). 

1. V horní nabídce vyberte **Create a knowledge base** (Vytvořit znalostní bázi).

    ![Snímek obrazovky QnA Makerového portálu](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. Přeskočte první krok, protože budete používat stávající službu QnA Maker. 

1. Vyberte existující nastavení:  

    |Nastavení|Účel|
    |--|--|
    |ID adresáře Microsoft Azure|Toto ID je přidruženo k účtu, který používáte k přihlášení do Azure Portal a portálu QnA Maker. |
    |Azure Subscription name (Název předplatného Azure)|Fakturační účet, ve kterém jste vytvořili prostředek QnA Maker.|
    |Azure QnA Service (Služba otázek a odpovědí Azure)|Prostředek vaší existující služby QnA Maker.|

    ![Snímek obrazovky QnA Makerového portálu](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. Zadejte název znalostní báze `My Tutorial kb`.

    ![Snímek obrazovky QnA Makerového portálu](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. Naplňte znalostní bázi pomocí následujících nastavení:  

    |Název nastavení|Hodnota nastavení|Účel|
    |--|--|--|
    |Adresa URL|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Obsah nejčastějších dotazů na této adrese URL je formátovaný jako otázka, za níž následuje odpověď. Služba QnA Maker může tento formát interpretovat a extrahovat otázky a přidružené odpovědi.|
    |Soubor |_v tomto kurzu se nepoužívá_|Toto nastavení slouží k nahrávání souborů pro otázky a odpovědi. |
    |Chit-chat personality (Charakter konverzace)|Vhodná|To přináší uživatelsky přívětivé a příležitostné [preference](../Concepts/best-practices.md#chit-chat) běžných otázek a odpovědí. Tyto otázky a odpovědi můžete později upravit. |

    ![Snímek obrazovky QnA Makerového portálu](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Vyberte **Create your KB** (Vytvořit znalostní bázi) a dokončete proces vytváření.

    ![Snímek obrazovky QnA Makerového portálu](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-save-and-train-the-knowledge-base"></a>Kontrola, uložení a trénování znalostní báze

1. Zkontrolujte otázky a odpovědi. První stránka obsahuje otázky a odpovědi z příslušné adresy URL. 

    ![Snímek obrazovky QnA Makerového portálu](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. V dolní části tabulky vyberte poslední stránku otázek a odpovědí. Na stránce se zobrazí otázky a odpovědi s charakterem konverzace. 

1. Na panelu nástrojů nad seznamem otázek a odpovědí vyberte ikonu **Možnosti zobrazení** a pak vyberte **zobrazit metadata**. Zobrazí se značky metadat pro každou otázku a odpověď. Otázky funkce CHITEST-chat mají již nastavená metadata funkce **CHITEST-chat** . Tato metadata se vrátí do klientské aplikace spolu s vybranou odpovědí. Klientská aplikace, jako je například robot pro chat, může pomocí těchto filtrovaných metadat určit další zpracování nebo interakci s uživatelem.

    ![Snímek obrazovky QnA Makerového portálu](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)

1. V horní nabídce vyberte **Save and train** (Uložit a natrénovat).

## <a name="publish-to-get-knowledge-base-endpoints"></a>Publikování pro získání koncových bodů znalostní báze

V horní nabídce vyberte tlačítko **Publish** (Publikovat). Na stránce publikování vyberte **Publikovat**.

![Snímek obrazovky QnA Makerového portálu](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Po publikování znalostní báze se zobrazí koncový bod.

![Snímek obrazovky s nastavením koncového bodu](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Nezavírejte tuto stránku **publikování** . K vytvoření robota ho budete potřebovat později v tomto kurzu. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Použití prvku kudrlinkou k dotazování na odpověď na nejčastější dotazy

1. Vyberte kartu **Curl**. 

    ![Snímek obrazovky s kartou kudrlinkou](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Zkopírujte text na kartě **kudrlinkou** a spusťte ho v terminálu s povoleným oblým nebo na příkazovém řádku. Hodnota autorizační hlavičky zahrnuje text `Endpoint`s koncovým mezerou a klíčovým řetězcem.

1. Nahraďte `<Your question>` za `How large can my KB be?` (Jak velká může být moje znalostní báze?). Tato otázka se podobá otázce `How large a knowledge base can I create?` (Jak velkou znalostní bázi můžu vytvořit?), ale není úplně stejná. Služba QnA Maker s využitím zpracování přirozeného jazyka určí, že se jedná o stejné otázky.     

1. Spusťte příkaz kudrlinkou a přijímají odpověď JSON, včetně skóre a odpovědi. 

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Použití funkce kudrlinkou k dotazování na aplikaci CHITEST – chat – odpověď

1. V terminálu s povoleným kudrlinkou nahraďte `How large can my KB be?` voláním robota – koncovým příkazem od uživatele, například `Thank you`.   

1. Spusťte příkaz kudrlinkou a přijímají odpověď JSON, včetně skóre a odpovědi. 

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

    Vzhledem k tomu, že otázka `Thank you` (Děkuji) přesně odpovídá konverzační otázce, služba QnA Maker udává naprostou jistotu se skóre 100. QnA Maker také vrátil všechny související otázky a vlastnost metadata obsahující informace značky metadat CHITEST-chat.  

## <a name="use-curl-to-query-for-the-default-answer"></a>Pomocí oblé se dotázat na výchozí odpověď

Všechny otázky, které QnA Maker, neplatí pro příjem výchozí odpovědi. Tuto odpověď můžete nakonfigurovat na webu Azure Portal. 

1. V terminálu s povoleným kudrlinkou nahraďte `Thank you` `x`. 

1. Spusťte příkaz kudrlinkou a přijímají odpověď JSON, včetně skóre a odpovědi. 

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
    
    QnA Maker vrátilo skóre `0`, což znamená bez obav. Vrátila se také výchozí odpověď. 

## <a name="create-a-knowledge-base-bot"></a>Vytvoření robota znalostní báze

Další informace najdete v tématu [Vytvoření robota v konverzaci s touto znalostní bázi](create-qna-bot.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Až se dokončíte s robotem znalostní báze, odeberte skupinu prostředků, `my-tutorial-rg`, abyste odebrali všechny prostředky Azure vytvořené v procesu robota.

Až se znalostní báze dokončí, vyberte na portálu QnA Maker možnost **Moje znalostní**báze. Pak vyberte znalostní bázi, **můj kurz KB**a na pravé straně řádku vyberte ikonu Odstranit.  

## <a name="next-steps"></a>Další kroky

Další informace o podporovaných formátech souborů najdete v tématu [Podporované zdroje dat](../Concepts/data-sources-supported.md). 

Další informace o [charakterech](../Concepts/best-practices.md#chit-chat) konverzace.

Další informace o výchozí odpovědi najdete v části [Nebyla nalezena žádná odpovídající položka](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Vytvoření robota v chatu s touto znalostní bázi](create-qna-bot.md)
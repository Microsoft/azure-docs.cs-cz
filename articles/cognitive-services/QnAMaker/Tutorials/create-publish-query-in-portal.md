---
title: Vytvářet, publikovat, odpovědět v nástroje QnA Maker
titleSuffix: Azure Cognitive Services
description: Vytvoření nové znalostní báze pomocí otázek a odpovědí z veřejné nejčastějších dotazů založených na webu. Uložit, trénování a publikování znalostní báze. Po publikování znalostní báze dotaz odesílat a přijímat odpovědi pomocí příkazu CURL. Vytvořte robota a otestovat bot se stejnou otázku.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 85f8643a0936209c8f280498df92555a7b40c533
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149945"
---
# <a name="tutorial-from-qna-maker-portal-create-a-knowledge-base"></a>Kurz: Nástroj QnA Maker portal vytvořte znalostní báze

Vytvoření nové znalostní báze pomocí otázek a odpovědí z veřejné nejčastějších dotazů založených na webu. Uložit, trénování a publikování znalostní báze. Po publikování znalostní báze dotaz odesílat a přijímat odpovědi pomocí příkazu Curl. Vytvořte robota a otestovat bot se stejnou otázku. 

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Vytvoření znalostní báze na portálu služby QnA Maker
> * Kontrola, uložení a trénování znalostní báze
> * Publikování znalostní báze
> * Dotazování znalostní báze pomocí cURL
> * Vytváření robotů
> 
> [!NOTE]
> Programové verzi tohoto kurzu je k dispozici s tak získají kompletní řešení z [ **Azure – ukázky/cognitive-services – QnA maker csharp** úložiště GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-csharp/tree/master/documentation-samples/tutorials/create-publish-answer-knowledge-base).

## <a name="prerequisites"></a>Požadavky

Tento kurz vyžaduje existující [službu QnA Maker](../How-To/set-up-qnamaker-service-azure.md). 

## <a name="create-a-knowledge-base"></a>Vytvoření znalostní báze 

1. Přihlaste se k portálu služby [QnA Maker](https://www.qnamaker.ai). 

1. V horní nabídce vyberte **Create a knowledge base** (Vytvořit znalostní bázi).

    ![Proces vytváření znalostní báze – krok 1](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-1.png)

1. První krok přeskočte, protože použijete existující službu QnA Maker. 

1. V dalším kroku vyberte stávající nastavení:  

    |Nastavení|Účel|
    |--|--|
    |Microsoft Azure Directory Id (ID adresáře Microsoft Azure)|Vaše _ID adresáře Microsoft Azure_ je přidružený k účtu, který používáte pro přihlášení na webu Azure portal a portál QnA Maker. |
    |Azure Subscription name (Název předplatného Azure)|Váš fakturační účet, ve kterém jste vytvořili prostředek služby QnA Maker.|
    |Azure QnA Service (Služba otázek a odpovědí Azure)|Prostředek vaší existující služby QnA Maker.|

    ![Proces vytváření znalostní báze – krok 2](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-2.png)

1. V dalším kroku zadejte název znalostní báze `My Tutorial kb`.

    ![Proces vytváření znalostní báze – krok 3](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-3.png)

1. V dalším kroku vyplňte následující nastavení znalostní báze:  

    |Název nastavení|Hodnota nastavení|Účel|
    |--|--|--|
    |zprostředkovatele identity|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs` |Obsah nejčastějších dotazů na této adrese URL je formátovaný jako otázka, za níž následuje odpověď. Služba QnA Maker může tento formát interpretovat a extrahovat otázky a přidružené odpovědi.|
    |File |_v tomto kurzu se nepoužívá_|Toto nastavení slouží k nahrávání souborů pro otázky a odpovědi. |
    |Chit-chat personality (Charakter konverzace)|Popisný|Díky tomuto nastavení budou časté otázky a odpovědi popisné a neformální. Tyto otázky a odpovědi můžete později upravit. |

    ![Proces vytváření znalostní báze – krok 4](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-4.png)

1. Vyberte **Create your KB** (Vytvořit znalostní bázi) a dokončete proces vytváření.

    ![Proces vytváření znalostní báze – krok 5](../media/qnamaker-tutorial-create-publish-query-in-portal/create-kb-step-5.png)

## <a name="review-kb-save-and-train"></a>Kontrola, uložení a trénování znalostní báze

1. Zkontrolujte otázky a odpovědi. První stránka obsahuje otázky a odpovědi z příslušné adresy URL. 

    ![Uložení a natrénování](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb.png)

1. V dolní části tabulky vyberte poslední stránku otázek a odpovědí. Na stránce se zobrazí otázky a odpovědi s charakterem konverzace. 

1. Vyberte z panelu nástrojů nad seznamem otázek a odpovědí **zobrazit možnosti** ikonu, pak vyberte **zobrazit metadata**. Zobrazí metadata značky pro každý otázek a odpovědí. Otázky Chit chat mají **redakční: chit chat** metadat již nastaven. Tato metadata se vrátí do klientské aplikace spolu s vybranou odpovědí. Klientská aplikace, jako je chatovací robot, slouží k určení interakcí s uživatelem nebo další zpracování tohoto filtrovaných metadat.

    ![! [Zobrazit značky metadat] (.. / media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png)](../media/qnamaker-tutorial-create-publish-query-in-portal/save-and-train-kb-chit-chat.png#lightbox)

1. V horní nabídce vyberte **Save and train** (Uložit a natrénovat).

## <a name="publish-to-get-kb-endpoints"></a>Publikování a získání koncových bodů znalostní báze

V horní nabídce vyberte tlačítko **Publish** (Publikovat). Jakmile budete na stránce publikování, vyberte tlačítko **Publish** (Publikovat) vedle tlačítka **Cancel** (Zrušit).

![Publikování](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-1.png)

Po publikování znalostní báze se zobrazí koncový bod.

![Nastavení koncového bodu na stránce publikování](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-2.png)

Nezavírejte toto **publikovat** , bude ho používat k vytváření robotů v pozdější části kurzu. 

## <a name="use-curl-to-query-for-an-faq-answer"></a>Používáme nástroj Curl k dotazu na odpověď – nejčastější dotazy

1. Vyberte kartu **Curl**. 

    ![Příkaz cURL](../media/qnamaker-tutorial-create-publish-query-in-portal/publish-3-curl.png)

1. Zkopírujte text na kartě **Curl** a spusťte ho na příkazovém řádku nebo v terminálu s podporou cURL. Hodnota autorizační hlavičky obsahuje text `Endpoint` (s koncovou mezerou) následovaný klíčem.

1. Nahraďte `<Your question>` za `How large can my KB be?` (Jak velká může být moje znalostní báze?). Tato otázka se podobá otázce `How large a knowledge base can I create?` (Jak velkou znalostní bázi můžu vytvořit?), ale není úplně stejná. Služba QnA Maker s využitím zpracování přirozeného jazyka určí, že se jedná o stejné otázky.     

1. Spusťte příkaz Curl, získáte odpověď JSON, včetně skóre a odpovědí. 

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

## <a name="use-curl-to-query-for-a-chit-chat-answer"></a>Používáme nástroj Curl k dotazu pro Chit chat odpovědí

1. V terminálu povolené Curl nahraďte `How large can my KB be?` příkazem ukončení konverzace bot od uživatele, jako například `Thank you`.   

1. Spusťte příkaz Curl, získáte odpověď JSON, včetně skóre a odpovědí. 

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

## <a name="use-curl-to-query-for-the-default-answer"></a>Používáme nástroj Curl k dotazů pro výchozí odpověď

Na každou otázku, u které si služba QnA Maker není jistá odpovědí, se vrátí výchozí odpověď. Tuto odpověď můžete nakonfigurovat na webu Azure Portal. 

1. V terminálu s podporou cURL nahraďte `Thank you` (Děkuji) za `x`. 

1. Spusťte příkaz Curl, získáte odpověď JSON, včetně skóre a odpovědí. 

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
    
    Nástroj QnA Maker vrátil bodů `0`, což znamená, že bez obav, ale vrátí výchozí odpověď. 

## <a name="create-a-knowledge-base-bot"></a>Vytváření robotů znalostní báze

Další informace najdete v tématu [vytvořit chatovací robot s této znalostní báze](create-qna-bot.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi s robotem znalostní báze knowledge base, odeberte skupinu prostředků, `my-tutorial-rg`, chcete-li odebrat všechny prostředky Azure vytvořeny během procesu robota.

Jakmile budete hotovi s znalostní báze na portálu pro nástroj QnA Maker vyberte **Moje znalostních bází**, vyberte ve znalostní bázi **kb tento kurz**, vyberte ikonu Odstranit úplně vpravo v daném řádku.  

## <a name="next-steps"></a>Další postup

Další informace najdete v tématu [zdroje dat podporované](../Concepts/data-sources-supported.md) Další informace o podpoře formátů souborů. 

Další informace o [charakterech](../Concepts/best-practices.md#chit-chat) konverzace.

Další informace o výchozí odpovědi najdete v části [Nebyla nalezena žádná odpovídající položka](../Concepts/confidence-score.md#no-match-found). 

> [!div class="nextstepaction"]
> [Vytvoření chatovací robot s této znalostní báze](create-qna-bot.md)
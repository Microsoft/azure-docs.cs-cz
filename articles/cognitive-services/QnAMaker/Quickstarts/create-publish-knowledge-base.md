---
title: 'Rychlý start: Vytvoření KB – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Nástroj QnA Maker znalostní bázi (KB) můžete vytvořit vlastní obsah, jako jsou nejčastější dotazy nebo produktových příruček. Znalostní BÁZE QnA Maker v tomto příkladu je vytvořena z jednoduchá webová stránka nejčastější dotazy k odpovědi na otázky na obnovení klíče Bitlockeru.
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: diberry
ms.openlocfilehash: f7af86687a8a61fb7aed028d2868752faaa8045a
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44715089"
---
# <a name="create-train-and-publish-your-knowledge-base"></a>Vytváření, trénování a publikování znalostní báze

Nástroj QnA Maker znalostní bázi (KB) můžete vytvořit vlastní obsah, jako jsou nejčastější dotazy nebo produktových příruček. Znalostní BÁZE QnA Maker v tomto příkladu je vytvořena z jednoduchá webová stránka nejčastější dotazy k odpovědi na otázky na obnovení klíče Bitlockeru.

## <a name="prerequisite"></a>Požadavek

> [!div class="checklist"]
> * Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-qna-maker-knowledge-base"></a>Vytvoření znalostní báze QnA Maker

1. Přihlaste se k QnAMaker.ai pomocí přihlašovacích údajů Azure.

2. Na webu nástroje QnA Maker, vyberte **vytvoření znalostní báze**.

   ![Vytvořit nový KB](../media/qna-maker-create-kb.png)

3. Na **vytvořit** v kroku 1, vyberte na stránce **vytvořit službu QnA**. Budete přesměrováni [webu Azure portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) k nastavení služby QnA Maker v rámci vašeho předplatného. Pokud vyprší časový limit na webu Azure portal, vyberte **opakujte** v lokalitě. Po připojení se zobrazí řídicí panel Azure.

4. Jakmile úspěšně vytvořit novou službu QnA Maker v Azure, vraťte se na qnamaker.ai/create. Vyberte svoji službu QnA z rozevíracích seznamů v kroku 2. Pokud vytvoříte novou službu QnA, je nutné aktualizovat stránku.

   ![Vyberte službu QnA KB](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. V kroku 3 pojmenujte znalostní BÁZÍ **Moje KB QnA ukázka**.

6. Pokud chcete přidat obsah do znalostní BÁZÍ, vyberte tři typy datových zdrojů. V kroku 4, v části **naplnit znalostní BÁZÍ**, přidejte [nejčastější dotazy k obnovení nástroje BitLocker](https://docs.microsoft.com/en-us/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) adresu URL **adresy URL** pole.

   ![Vyberte službu QnA KB](../media/qnamaker-quickstart-kb/add-datasources.png)

7. V kroku 5 vyberte **vytvořit znalostní BÁZÍ**.

8. Když se vytváří KB, se zobrazí automaticky otevírané okno. Proces extrakce trvá několik minut, přečtěte si stránku HTML a identifikovat otázek a odpovědí.

9. Po úspěšném vytvoření KB **znalostní báze** otevře se stránka. Obsah znalostní BÁZE na této stránce můžete upravit.

10. V pravém horním rohu vyberte **QnA přidat pár** přidáte nový řádek v **redakční** část KB. V části **otázku**, zadejte **Dobrý den.** V části **odpovědí**, zadejte **Hello. Klást otázky bitlockeru.**

   ![Přidat pár QnA](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. V pravém horním rohu vyberte **uložit a jejich trénování** uložit provedené úpravy a trénování modelu QnA Maker. Úpravy nejsou zachovány, dokud jsou uloženy.

   ![Uložit a trénování](../media/qnamaker-quickstart-kb/add-qna-pair2.png)

12. V pravém horním rohu vyberte **testování** k testování, aby změny vstoupily v platnost. Zadejte **Dobrý den existuje** do pole a stiskněte Enter. Měli byste vidět odpovědi, kterou jste vytvořili jako odpověď.

13. Vyberte **zkontrolujte, jestli se** odpovědi podrobněji prozkoumat. V okně testu se používá k testování změny KB předtím, než se publikují.

   ![Test panelu](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. Vyberte **testovací** zavřete **Test** místní.

15. V nabídce vedle **upravit**vyberte **publikovat**. Pokud chcete potvrdit, vyberte **publikovat** na stránce.

16. Služba QnA Maker se úspěšně publikováno. Můžete použít koncový bod v aplikaci nebo kód robota.

   ![Publikování](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](../How-To/create-knowledge-base.md)

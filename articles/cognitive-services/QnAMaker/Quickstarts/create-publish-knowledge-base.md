---
title: Vytvoření, trénování a publikování znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Znalostní bázi služby QnA Maker můžete vytvořit s použitím vlastního obsahu, jako jsou nejčastější dotazy nebo příručky k produktům. Znalostní báze služby QnA Maker v tomto příkladu je vytvořená z jednoduché webové stránky s nejčastějšími dotazy a poskytuje odpovědi na otázky týkající se obnovení klíče nástroje BitLocker.
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 2ac6e6fcd73abddcee668b8f73184b923aeab5d3
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55876993"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>Vytvoření, trénování a publikování znalostní báze služby QnA Maker

Znalostní bázi služby QnA Maker můžete vytvořit s použitím vlastního obsahu, jako jsou nejčastější dotazy nebo příručky k produktům. Znalostní báze služby QnA Maker v tomto příkladu je vytvořená z jednoduché webové stránky s nejčastějšími dotazy a poskytuje odpovědi na otázky týkající se obnovení klíče nástroje BitLocker.

## <a name="prerequisite"></a>Požadavek

> [!div class="checklist"]
> * Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-qna-maker-knowledge-base"></a>Vytvoření znalostní báze služby QnA Maker

1. Přihlaste se k webu QnAMaker.ai pomocí svých přihlašovacích údajů Azure.

2. Na webu služby QnA Maker vyberte **Create a knowledge base** (Vytvořit znalostní bázi).

   ![Vytvoření nové znalostní báze](../media/qna-maker-create-kb.png)

3. Na stránce **Create** (Vytvořit) v kroku 1 vyberte **Create a QnA service** (Vytvořit službu otázek a odpovědí). Budete přesměrování na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), kde můžete nastavit službu QnA Maker ve vašem předplatném. Pokud vyprší časový limit webu Azure Portal, vyberte **Zkusit znovu**. Jakmile se připojíte, zobrazí se řídicí panel Azure.

4. Po úspěšném vytvoření nové služby QnA Maker v Azure se vraťte na web qnamaker.ai/create. V rozevíracích seznamech v kroku 2 vyberte vaši službu otázek a odpovědí. Pokud jste vytvořili novou službu otázek a odpovědí, nezapomeňte stránku aktualizovat.

   ![Výběr služby otázek a odpovědí pro znalostní bázi](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. V kroku 3 pojmenujte znalostní bázi **My Sample QnA KB**.

6. Při přidávání obsahu do znalostní báze máte na výběr ze tří typů zdrojů dat. V kroku 4 v části **Populate your KB** (Naplnění znalostní báze) do pole **URL** přidejte adresu URL s [nejčastějšími dotazy k obnovení nástroje BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq).

   ![Výběr služby otázek a odpovědí pro znalostní bázi](../media/qnamaker-quickstart-kb/add-datasources.png)

7. V kroku 5 vyberte **Create your KB** (Vytvořit znalostní bázi).

8. Zatímco se znalostní báze vytváří, zobrazí se automaticky otevírané okno. Procesu extrakce několik minut trvá načíst stránku HTML a identifikovat otázky a odpovědi.

9. Po úspěšném vytvoření znalostní báze se otevře stránka **Knowledge base** (Znalostní báze). Na této stránce můžete upravit obsah znalostní báze.

10. V pravém horním rohu vyberte **Add QnA pair** (Přidat pár otázky a odpovědi) a přidejte nový řádek do části **Editorial** (Redakční) znalostní báze. V části **Question** (Otázka) zadejte **Hi** (Dobrý den). V části **Answer** (Odpověď) zadejte **Hello. Ask me bitlocker questions.** (Dobrý den. Můžete mi klást otázky týkající se nástroje BitLocker).

   ![Přidání páru otázky a odpovědi](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. V pravém horním rohu vyberte **Save and train** (Uložit a natrénovat), aby se uložily provedené úpravy a natrénoval model služby QnA Maker. Úpravy se nezachovají, pokud se neuloží.

12. V pravém horním rohu vyberte **Test** (Otestovat) a otestujte, že se provedené změny projevily. Zadejte `hi there` do pole a stiskněte Enter. Jako odpověď by se měla zobrazit odpověď, kterou jste vytvořili.

13. Pokud chcete odpověď prozkoumat podrobněji, vyberte **Inspect** (Prozkoumat). Okno Test slouží k testování změn znalostní báze před publikováním.

   ![Panel Test](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. Znovu vyberte **Test** (Otestovat) a zavřete automaticky otevírané okno **Test**.

15. V nabídce vedle položky **Edit** (Upravit) vyberte **Publish** (Publikovat). Pak publikování potvrďte tím, že na stránce vyberete **Publish** (Publikovat).

16. Služba QnA Maker je teď úspěšně publikovaná. Koncový bod můžete použít v kódu aplikace nebo chatbota.

   ![Publikování](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](../How-To/create-knowledge-base.md)

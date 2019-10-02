---
title: 'Rychlý Start: vytvoření, výuka a publikování znalostní báze Knowledge Base – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Znalostní bázi služby QnA Maker můžete vytvořit s použitím vlastního obsahu, jako jsou nejčastější dotazy nebo příručky k produktům. Znalostní báze QnA Maker Knowledge Base v tomto příkladu se vytváří na základě jednoduchých webových stránek s nejčastějšími dotazy pro odpovědi na otázky týkající se obnovení klíče BitLockeru.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: cafc1e2f3f195301a6c0f9485ebaa10111b08c7d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803051"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Rychlý Start: vytvoření, výuka a publikování znalostní báze QnA Maker Knowledge Base

Znalostní bázi služby QnA Maker můžete vytvořit s použitím vlastního obsahu, jako jsou nejčastější dotazy nebo příručky k produktům. Tento článek obsahuje příklad vytvoření QnA Maker znalostní báze na jednoduché webové stránce s nejčastějšími dotazy a odpovědí na otázky týkající se obnovení klíče BitLockeru.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Požadavek

> [!div class="checklist"]
> * Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-qna-maker-knowledge-base"></a>Vytvoření znalostní báze služby QnA Maker

1. Přihlaste se k portálu [QnAMaker.AI](https://QnAMaker.ai) pomocí svých přihlašovacích údajů Azure.

1. Na portálu QnA Maker vyberte **vytvořit znalostní bázi**.

   ![Snímek obrazovky QnA Makerového portálu](../media/qna-maker-create-kb.png)

1. Na stránce **Create** (Vytvořit) v kroku 1 vyberte **Create a QnA service** (Vytvořit službu otázek a odpovědí). Budete přesměrování na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), kde můžete nastavit službu QnA Maker ve vašem předplatném. Pokud vyprší časový limit webu Azure Portal, vyberte **Zkusit znovu**. Jakmile se připojíte, zobrazí se řídicí panel Azure.

1. Po úspěšném vytvoření nové služby QnA Maker v Azure se vraťte na web qnamaker.ai/create. V rozevíracím seznamu v kroku 2 Vyberte svou službu QnA Maker. Pokud jste vytvořili novou službu QnA Maker, nezapomeňte aktualizovat stránku.

   ![Snímek obrazovky s výběrem znalostní báze služby QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. V kroku 3 pojmenujte svůj znalostní báze **Moje ukázková QNA KB**.

1. Pokud chcete přidat obsah do znalostní báze, vyberte tři typy zdrojů dat. V kroku 4 v části **Populate your KB** (Naplnění znalostní báze) do pole **URL** přidejte adresu URL s [nejčastějšími dotazy k obnovení nástroje BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq).

   ![Snímek obrazovky s přidáním zdrojů dat](../media/qnamaker-quickstart-kb/add-datasources.png)

1. V kroku 5 vyberte **Create your KB** (Vytvořit znalostní bázi).

1. Když QnA Maker vytvoří znalostní bázi, zobrazí se automaticky otevírané okno. Procesu extrakce několik minut trvá načíst stránku HTML a identifikovat otázky a odpovědi.

1. Jakmile QnA Maker úspěšně vytvoří znalostní bázi, otevře se stránka **znalostní báze** . Na této stránce můžete upravit obsah znalostní báze.

## <a name="edit-the-knowledge-base"></a>Upravit znalostní bázi

1. Na portálu QnA Maker v části **Upravit** vyberte **Přidat dvojici QnA** a přidejte do znalostní báze nový řádek. V části **Question** (Otázka) zadejte **Hi** (Dobrý den). V části **odpověď**zadejte **Hello. Zeptejte se mě na otázky nástroje BitLocker.**

    ![Snímek obrazovky QnA Makerového portálu](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. V pravém horním rohu vyberte **Save and train** (Uložit a natrénovat), aby se uložily provedené úpravy a natrénoval model služby QnA Maker. Úpravy se nezachovají, pokud se neuloží.

## <a name="test-the-knowledge-base"></a>Testování znalostní báze

1. Na portálu QnA Maker v pravém horním rohu vyberte **test** , abyste otestovali, že provedené změny se projevily. Do pole zadejte `hi there` a vyberte Enter. Jako odpověď by se měla zobrazit odpověď, kterou jste vytvořili.

1. Pokud chcete odpověď prozkoumat podrobněji, vyberte **Inspect** (Prozkoumat). Testovací okno slouží k otestování změn ve znalostní bázi před jejich publikováním.

    ![Snímek obrazovky se zkušebním panelem](../media/qnamaker-quickstart-kb/inspect.png)

1. Znovu vyberte **Test** (Otestovat) a zavřete automaticky otevírané okno **Test**.

## <a name="publish-the-knowledge-base"></a>Publikování znalostní báze

Když publikujete znalostní bázi, obsah otázky a odpovědi ve znalostní bázi se přesune z indexu testu do výrobního indexu ve službě Azure Search.

![Snímek obrazovky s přesunutím obsahu znalostní báze](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Na portálu QnA Maker klikněte v nabídce vedle **Upravit**na možnost **publikovat**. Pak publikování potvrďte tím, že na stránce vyberete **Publish** (Publikovat).

1. Služba QnA Maker je teď úspěšně publikovaná. Koncový bod můžete použít v kódu aplikace nebo chatbota.

    ![Snímek obrazovky s úspěšným publikováním](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Vytvoření robota

Po publikování můžete vytvořit robota ze stránky **publikování** : 

* Můžete rychle vytvořit několik robotyů, které ukazují na stejnou znalostní bázi pro různé oblasti nebo cenové tarify pro jednotlivé roboty. 
* Pokud potřebujete jenom jednu robota pro znalostní bázi, použijte **zobrazení všechny roboty na odkaz Azure Portal** k zobrazení seznamu aktuálního roboty. 

Když provedete změny ve znalostní bázi a znovu publikujete, nemusíte s robotem dělat další akce. Je už nakonfigurovaná pro práci se znalostní bázeí a funguje se všemi budoucími změnami ve znalostní bázi. Pokaždé, když publikujete znalostní bázi, jsou automaticky aktualizovány všechny roboty připojené k ní.

1. Na QnA Makerovém portálu na stránce **publikovat** vyberte **vytvořit robot**. Toto tlačítko se zobrazí až po publikování znalostní báze.

    ![Snímek obrazovky s vytvořením robota](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Otevře se nová karta prohlížeče pro Azure Portal se stránkou pro vytvoření Azure Bot Service. Nakonfigurujte službu Azure bot Service. Další informace o těchto nastaveních konfigurace najdete v tématu [Vytvoření QnA robota s Azure bot Service v4](../tutorials/create-qna-bot.md).
    
    * Při vytváření robota neměňte v Azure Portal následující nastavení. Jsou předem vyplněné pro stávající znalostní bázi: 
        * QnA ověřovací klíč
        * Plán a umístění služby App Service
        * Azure Storage
    * Robot a QnA Maker můžou sdílet plán služby Web App Service, ale nemůžou webovou aplikaci sdílet. To znamená, že **název aplikace** musí být jiný než název aplikace, který jste použili při vytváření služby QnA maker. 


## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](../How-To/create-knowledge-base.md)

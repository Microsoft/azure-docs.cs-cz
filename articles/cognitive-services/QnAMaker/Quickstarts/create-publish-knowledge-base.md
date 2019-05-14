---
title: Vytvoření, trénování a publikování znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Znalostní bázi služby QnA Maker můžete vytvořit s použitím vlastního obsahu, jako jsou nejčastější dotazy nebo příručky k produktům. Nástroj QnA Maker znalostní báze v tomto příkladu je vytvořena z jednoduchá webová stránka nejčastější dotazy k odpovědi na otázky na obnovení klíče Bitlockeru.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/10/2019
ms.author: diberry
ms.openlocfilehash: 1411576d078115921cb04b41347264bccdc390f4
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65593975"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>Vytvoření, trénování a publikování znalostní báze služby QnA Maker

Znalostní bázi služby QnA Maker můžete vytvořit s použitím vlastního obsahu, jako jsou nejčastější dotazy nebo příručky k produktům. Nástroj QnA Maker znalostní báze v tomto příkladu je vytvořena z jednoduchá webová stránka nejčastější dotazy k odpovědi na otázky na obnovení klíče Bitlockeru.

## <a name="prerequisite"></a>Požadavek

> [!div class="checklist"]
> * Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-qna-maker-knowledge-base"></a>Vytvoření znalostní báze služby QnA Maker

1. Přihlaste se k [QnAMaker.ai](https://QnAMaker.ai) portálu pomocí přihlašovacích údajů Azure.

1. Na portál QnA Maker, vyberte **vytvoření znalostní báze**.

   ![Vytvořit nový znalostní báze](../media/qna-maker-create-kb.png)

1. Na stránce **Create** (Vytvořit) v kroku 1 vyberte **Create a QnA service** (Vytvořit službu otázek a odpovědí). Budete přesměrování na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), kde můžete nastavit službu QnA Maker ve vašem předplatném. Pokud vyprší časový limit webu Azure Portal, vyberte **Zkusit znovu**. Jakmile se připojíte, zobrazí se řídicí panel Azure.

1. Po úspěšném vytvoření nové služby QnA Maker v Azure se vraťte na web qnamaker.ai/create. V rozevíracích seznamech v kroku 2 vyberte vaši službu otázek a odpovědí. Pokud jste vytvořili novou službu otázek a odpovědí, nezapomeňte stránku aktualizovat.

   ![Vyberte službu QnA znalostní báze](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. V kroku 3 pojmenujte znalostní báze **Moje KB QnA ukázka**.

1. Pokud chcete přidat obsah do znalostní báze, vyberte tři typy datových zdrojů. V kroku 4 v části **Populate your KB** (Naplnění znalostní báze) do pole **URL** přidejte adresu URL s [nejčastějšími dotazy k obnovení nástroje BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq).

   ![Vyberte službu QnA znalostní báze](../media/qnamaker-quickstart-kb/add-datasources.png)

1. V kroku 5 vyberte **Create your KB** (Vytvořit znalostní bázi).

1. Když se vytváří ve znalostní bázi, se zobrazí automaticky otevírané okno. Procesu extrakce několik minut trvá načíst stránku HTML a identifikovat otázky a odpovědi.

1. Po úspěšném vytvoření znalostní báze **znalostní báze** otevře se stránka. Obsah znalostní báze na této stránce můžete upravit.

## <a name="edit-the-knowledge-base"></a>Úprava znalostní báze

1. Na portálu pro nástroj QnA Maker na **upravit** části **QnA přidat pár** přidáte nový řádek do znalostní báze. V části **Question** (Otázka) zadejte **Hi** (Dobrý den). V části **Answer** (Odpověď) zadejte **Hello. Klást otázky Bitlockeru.**

    ![Přidání páru otázky a odpovědi](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. V pravém horním rohu vyberte **Save and train** (Uložit a natrénovat), aby se uložily provedené úpravy a natrénoval model služby QnA Maker. Úpravy se nezachovají, pokud se neuloží.

## <a name="test-the-knowledge-base"></a>Testování ve znalostní bázi

1. Na portálu QnA Maker v pravém horním rohu vyberte **testování** k testování, aby změny vstoupily v platnost. Zadejte `hi there` do pole a stiskněte Enter. Jako odpověď by se měla zobrazit odpověď, kterou jste vytvořili.

1. Pokud chcete odpověď prozkoumat podrobněji, vyberte **Inspect** (Prozkoumat). V okně testu se používá k testování změny ve znalostní bázi předtím, než se publikují.

    ![Panel Test](../media/qnamaker-quickstart-kb/inspect.png)

1. Znovu vyberte **Test** (Otestovat) a zavřete automaticky otevírané okno **Test**.

## <a name="publish-the-knowledge-base"></a>Publikování znalostní báze

Při publikování znalostní bázi otázek a odpovědí obsah znalostní báze přesune z index testu do produkčního prostředí indexu ve službě Azure search.

![Publikování index prod testu](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Nástroj QnA Maker portálu, v nabídce vedle **upravit**vyberte **publikovat**. Pak publikování potvrďte tím, že na stránce vyberete **Publish** (Publikovat).

1. Služba QnA Maker je teď úspěšně publikovaná. Koncový bod můžete použít v kódu aplikace nebo chatbota.

    ![Publikovat](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Vytváření robotů

Vytváření robota je vždy k dispozici po publikování, ze **publikovat** stránky. 

* Několik roboty můžete vytvořit rychle, všechny odkazující na stejné znalostní báze pro různé oblasti nebo cenové plány pro jednotlivé robotů. 
* Pokud chcete pouze jeden bot pro znalostní báze, použijte **svým robotům zobrazit na portálu Azure portal** odkaz zobrazíte seznam aktuální robotů. 
* Když provedete změny znalostní báze knowledge base a znovu, není potřeba provádět další akci s roboty. Již je nakonfigurováno pro práci s znalostní báze a bude fungovat u všech budoucích změn ve znalostní bázi. Při každém publikování znalostní báze – se automaticky aktualizují všechny roboty k ní připojená.

1. Na portálu pro nástroj QnA Maker na **publikovat** stránce **vytváření robotů**. Toto tlačítko se zobrazí pouze po publikování znalostní báze.

    ![Na portálu QnA Maker přejděte na stránku publikovat a publikovat znalostní báze. Vyberte vytváření robotů.](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Otevře se nová karta prohlížeče na webu Azure portal, Azure Bot Service vytvoření stránky. Konfigurace Azure bot service. Další informace o těchto nastaveních konfigurace najdete v tématu [vytváření QnA robotů pomocí služeb Azure Bot Service v4](../tutorials/create-qna-bot.md) kurzu.
    
    * Při vytváření robota, neměňte následující nastavení na portálu Azure portal. Jsou předem vyplněná pro existující znalostní báze: 
        * Nástroj QnA ověřovacím klíčem
        * Plán služby App Service / umístění
        * Azure Storage
    * Bot a nástroje QnA Maker můžete sdílet služba webové aplikace _plán_ , ale nesmí ho sdílet webové aplikace. To znamená, **název aplikace** musí být jiný než název aplikace, které jste použili při vytváření služby QnA Maker. 


## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Vytvoření znalostní báze](../How-To/create-knowledge-base.md)

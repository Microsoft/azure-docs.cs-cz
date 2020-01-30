---
title: 'Rychlý Start: vytvoření, výuka a publikování znalostní báze Knowledge Base – QnA Maker'
titleSuffix: Azure Cognitive Services
description: V tomto rychlém startu se dozvíte, jak vytvořit QnA Maker znalostní bázi Knowledge Base (KB) z vlastního obsahu, jako jsou nejčastější dotazy nebo Příručky k produktu. Znalostní báze QnA Maker Knowledge Base v tomto příkladu se vytváří na základě jednoduchých webových stránek s nejčastějšími dotazy pro odpovědi na otázky týkající se obnovení klíče BitLockeru.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: diberry
ms.openlocfilehash: 26c7996d6a955a183121a59254131dcb1dc6706f
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844258"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Rychlý Start: vytvoření, výuka a publikování znalostní báze QnA Maker Knowledge Base

Znalostní bázi služby QnA Maker můžete vytvořit s použitím vlastního obsahu, jako jsou nejčastější dotazy nebo příručky k produktům. Tento článek obsahuje příklad vytvoření QnA Maker znalostní báze na jednoduché webové stránce s nejčastějšími dotazy a odpovědí na otázky týkající se obnovení klíče BitLockeru.

Zahrňte do funkce CHITEST-chat preference, aby vaše znalosti uživatelů byly zajímavější.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Požadavek

> [!div class="checklist"]
> * Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="create-a-new-qna-maker-knowledge-base"></a>Vytvořit novou QnA Maker znalostní bázi Knowledge Base

1. Přihlaste se k portálu [QnAMaker.AI](https://QnAMaker.ai) pomocí svých přihlašovacích údajů Azure.

1. Na portálu QnA Maker vyberte **vytvořit znalostní bázi**.

1. Na stránce **vytvořit** vyberte možnost **vytvořit službu QnA**. Budete přesměrování na [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), kde můžete nastavit službu QnA Maker ve vašem předplatném.

1. V Azure Portal vytvořte prostředek. Mějte na paměti, že vaše Azure Active Directory ID, předplatné a název prostředku QnA jste zvolili při vytváření prostředku.
1. Vraťte se na portál QnA Maker, aktualizujte webovou stránku na portálu a pokračujte v vytváření znalostní báze.

   ![Snímek obrazovky s výběrem znalostní báze služby QnA Maker](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Pojmenujte svůj znalostní báze **můj vzor QNA KB**.

1. Přidat vzorový dokument aplikace Word jako adresu URL:

    `https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`

1. Vyberte `+ Add URL`.

1. Přidat  **_matematický_ program CHITEST – chat** do vaší znalostní báze

1. Vyberte **vytvořit znalostní BÁZÍ**.

    Proces extrakce trvá několik minut, než se dokument přečte, a Identifikujte otázky a odpovědi.

    Jakmile QnA Maker úspěšně vytvoří znalostní bázi, otevře se stránka **znalostní báze** . Na této stránce můžete upravit obsah znalostní báze.

## <a name="add-a-new-question-and-answer-set"></a>Přidat novou sadu otázek a odpovědí

1. Na portálu QnA Maker na stránce **Upravit** vyberte **+ Přidat dvojici QnA** z panelu nástrojů kontextu.
1. Přidejte následující otázku:

    `How many Azure services are used by a knowledge base?`

1. Přidejte odpověď formátovanou pomocí _Markdownu_:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Přidejte otázku jako text a odpověď formátovanou pomocí Markdownu.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    Symbol Markdownu `*`se používá pro body odrážek. `\n` se používá pro nový řádek.

    Na stránce pro **Úpravy** se zobrazí Markdownu. Když použijete **testovací** panel později, zobrazí se Markdownu správně.

## <a name="save-and-train"></a>Uložení a natrénování

V pravém horním rohu vyberte **Save and train** (Uložit a natrénovat), aby se uložily provedené úpravy a natrénoval model služby QnA Maker. Úpravy se nezachovají, pokud se neuloží.

## <a name="test-the-knowledge-base"></a>Testování znalostní báze

1. Na portálu QnA Maker v pravém horním rohu vyberte **test** , abyste otestovali, že provedené změny se projevily.
1. Do textového pole zadejte ukázkový dotaz uživatele.

    `How many Azure services are used by a knowledge base?`

    ![ Do textového pole zadejte ukázkový dotaz uživatele. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Pokud chcete odpověď prozkoumat podrobněji, vyberte **Inspect** (Prozkoumat). Testovací okno slouží k otestování změn ve znalostní bázi před publikováním znalostní báze.

1. Vyberte znovu **test** pro zavření **testovacího** panelu.

## <a name="publish-the-knowledge-base"></a>Publikování znalostní báze

Při publikování znalostní báze se obsah vaší znalostní báze přesune z indexu `test` do indexu `prod` ve službě Azure Search.

![Snímek obrazovky s přesunutím obsahu znalostní báze](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. Na portálu QnA Maker vyberte **publikovat**. Pak publikování potvrďte tím, že na stránce vyberete **Publish** (Publikovat).

    Služba QnA Maker je teď úspěšně publikovaná. Koncový bod můžete použít v kódu aplikace nebo chatbota.

    ![Snímek obrazovky s úspěšným publikováním](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Vytvoření robota

Po publikování můžete vytvořit robota ze stránky **publikování** :

* Můžete rychle vytvořit několik robotyů, které ukazují na stejnou znalostní bázi pro různé oblasti nebo cenové tarify pro jednotlivé roboty.
* Pokud potřebujete jenom jednu robota pro znalostní bázi, použijte **zobrazení všechny roboty na odkaz Azure Portal** k zobrazení seznamu aktuálního roboty.

Když provedete změny ve znalostní bázi a znovu publikujete, nemusíte s robotem dělat další akce. Je už nakonfigurovaná pro práci se znalostní bázeí a funguje se všemi budoucími změnami ve znalostní bázi. Pokaždé, když publikujete znalostní bázi, jsou automaticky aktualizovány všechny roboty připojené k ní.

1. Na QnA Makerovém portálu na stránce **publikovat** vyberte **vytvořit robot**. Toto tlačítko se zobrazí až po publikování znalostní báze.

    ![Snímek obrazovky s vytvořením robota](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Otevře se nová karta prohlížeče pro Azure Portal se stránkou pro vytvoření Azure Bot Service. Nakonfigurujte službu Azure bot Service. Robot a QnA Maker můžou sdílet plán služby Web App Service, ale nemůžou webovou aplikaci sdílet. To znamená, že **název aplikace** pro robot se musí lišit od názvu aplikace pro službu QnA maker.

    * **Do**
        * Změnit popisovač robota – Pokud není jedinečný
        * Vyberte jazyk sady SDK. Po vytvoření robota si můžete kód stáhnout do svého místního vývojového prostředí a pokračovat v procesu vývoje.
    * **Ne**
        * Při vytváření robota změňte následující nastavení v Azure Portal. Jsou předem vyplněné pro stávající znalostní bázi:
           * QnA ověřovací klíč
           * Plán a umístění služby App Service


1. Po vytvoření robota otevřete prostředek **služby bot** .
1. V části **Správa robota**vyberte **test na webu chat**.
1. Na příkazovém řádku chatu **Zadejte zprávu**:

    `Azure services?`

    Robotka chatu odpoví odpověď z vaší znalostní báze.

    ![Zadejte uživatelský dotaz do testovacího webového chatu.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="what-did-you-accomplish"></a>Co jste provedli?

Vytvořili jste novou znalostní bázi, Přidali jste do znalostní báze veřejnou adresu URL, Přidali jste vlastní QnA sadu, vyškolenou, testovaný a publikovaný základ znalostní báze.

Po zveřejnění znalostní báze jste vytvořili robota a otestovali robota.

To bylo provedeno za několik minut, aniž byste museli psát kód a vyčistit obsah.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Vyčistěte prostředky QnA Maker a bot Framework v Azure Portal.

## <a name="next-steps"></a>Další kroky

Další informace:

* [Formát Markdownu v odpovědích](../reference-markdown-format.md)
* QnA Maker [zdroje dat](../concepts/knowledge-base.md).
* [Nastavení konfigurace prostředků robota](../tutorials/create-qna-bot.md).

> [!div class="nextstepaction"]
> [Přidání otázek s metadaty](add-question-metadata-portal.md)

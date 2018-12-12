---
title: Migrace ve verzi preview znalostních bází – Qna Maker
titleSuffix: Azure Cognitive Services
description: Znalostních bází, které jsou vytvořené pomocí nástroje QnA Maker bezplatná verze Preview se musí migrovat na všeobecně dostupnou QnA Maker Nástroj QnA Maker ve verzi Preview se přestanou používat v lednu 2019.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 261d42a6c21681ee8015edfe73ad78b4458eb0ee
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53078803"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrace znalostní báze pomocí exportu importu
Nástroj QnA Maker oznámila všeobecné zpřístupnění 7. května 2018 v \\\build\ místa konání konference. Nástroj QnA Maker GA má nové architektury založené na Azure. Znalostních bází, které jsou vytvořené pomocí nástroje QnA Maker bezplatná verze Preview se musí migrovat na všeobecně dostupnou QnA Maker Nástroj QnA Maker ve verzi Preview se přestanou používat v lednu 2019. Další informace o změnách ve verzi GA QnA Maker, najdete v oznámení všeobecné dostupnosti nástroje QnA Maker [blogový příspěvek](https://aka.ms/qnamakerga-blog).

Nástroj QnA Maker má teď [cenový model](https://azure.microsoft.com/pricing/details/cognitive-services/qna-maker/).

Požadavky
> [!div class="checklist"]
> * Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
> * Nastavit novou [služba QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>Migrace znalostní báze na portálu Preview nástroje QnA Maker
1. Přejděte do [portál QnA Maker Preview](https://aka.ms/qnamaker-old-portal
) a klikněte na **Moje služby**.
2. Vyberte ve znalostní bázi, kterou chcete migrovat kliknutím na ikonu pro úpravy.

    ![Úprava znalostní báze](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. Klikněte na **stáhnout znalostní báze** chcete stáhnout soubor TSV, který obsahuje obsah znalostní báze – otázky, odpovědi, metadata, a názvy zdroje dat z jejich byly extrahovány.

    ![Stáhněte si znalostní báze](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Přihlaste se k [portál QnA Maker](https://qnamaker.ai) s azure přihlašovací údaje a klikněte na kartu **vytvoření znalostní báze**.
    
5. Pokud jste ještě nevytvořili služba QnA Maker, vyberte **vytvořit službu QnA**. Jinak klikněte na tlačítko služba QnA Maker z rozevírací nabídky v kroku 2. Vyberte službu QnA Maker, která bude hostovat ve znalostní bázi.

    ![Služba QnA instalace](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. Vytvořit prázdný znalostní báze 

    ![Nastavení zdroje dat](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Zadejte vaši službu **název.** Duplicitní názvy jsou podporovány a jsou také podporovány speciální znaky.
    - Přeskočte nahrávání souborů nebo adresy URL, jak chcete pracovat s daty ze znalostní báze ve verzi Preview. Teď vytvoříte prázdný znalostní báze.

7. Vyberte **Vytvořit**.

    ![Vytvoření znalostní BÁZE](../media/qnamaker-how-to-create-kb/create-kb.png)

8. V této nové znalostní báze, otevřete **nastavení** kartě a klikněte na **Import znalostní báze**. Importuje otázky, odpovědi a metadat a zachová názvy zdrojů dat, z nichž byly extrahovány.

   ![Import znalostní báze](../media/qnamaker-how-to-migrate-kb/Import.png)

9. **Test** nové znalostní báze pomocí panelu Test. Zjistěte, jak [testování znalostní báze](../How-To/test-knowledge-base.md).
10. **Publikování** znalostní báze. Zjistěte, jak [publikovat znalostní báze](../How-To/publish-knowledge-base.md).
11. Použijte koncový bod níže v aplikaci nebo robotovi kódu. Zde uvedený postup [vytváření robotů QnA](../Tutorials/create-qna-bot.md).

    ![Nástroj QnA Maker hodnoty](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

V tomto okamžiku všechny znalostní báze obsah – otázky a odpovědi metadat, společně s názvy zdrojových souborů a adresy URL, importují do nové znalostní báze. 

## <a name="chatlogs-and-alterations"></a>Chatlogs a změn
Změny (synonym) nejsou importovány automaticky. Použití [rozhraní API V2](https://aka.ms/qnamaker-v2-apis) export změny ze zásobníku ve verzi preview a [V4 API](https://aka.ms/qnamaker-v4-apis) k nahrazení v nový zásobník.

Neexistuje žádný způsob, jak migrovat chatlogs, protože nový zásobník používá pro ukládání chatlogs Application Insights. Ale můžete si stáhnout chatlogs z [portálu preview](https://aka.ms/qnamaker-old-portal).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-To/edit-knowledge-base.md)

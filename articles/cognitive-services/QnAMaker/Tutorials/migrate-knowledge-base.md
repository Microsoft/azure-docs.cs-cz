---
title: Postup importování znalostní báze – kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Postup importování znalostní bázi
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce8f98f9bdb37d5f326e942fe5b5e815e5272c56
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "35343883"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrace znalostní bázi knowledge base pomocí export-import
QnA Maker oznámeny na 7 může 2018 v obecné dostupnosti \\\build\ konference. QnA Maker GA má nové architektury postavené na platformě Azure. Znalostních bází, které jsou vytvořené pomocí QnA Maker bezplatnou verzi Preview bude nutné migrovat na všeobecné QnA Maker V listopadu 2018 přestanou QnA Maker Preview. Další informace o změnách v QnA Maker GA, najdete v části oznámení QnA Maker GA [příspěvku na blogu](https://aka.ms/qnamakerga-blog).

QnA Maker má teď [cenový model](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/qna-maker/).

Požadavky
> [!div class="checklist"]
> * Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
> * Instalace nové [služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>Migrace z portálu QnA Maker Preview znalostní báze
1. Přejděte na [portálu QnA Maker Preview](https://aka.ms/qnamaker-old-portal
) a klikněte na **Moje služby**.
2. Vyberte znalostní báze, které chcete migrovat kliknutím na ikonu pro úpravy.

    ![Upravit znalostní báze knowledge base](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. Klikněte na **stáhnout znalostní báze knowledge base** ke stažení souboru TSV, který obsahuje obsah znalostní báze – otázky, odpovědi, metadata, a zdroj dat názvy ze které se extrahují.

    ![Stáhněte si znalostní bázi knowledge base](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. Přihlaste se k [QnA Maker portál](https://qnamaker.ai) pomocí přihlašovacích údajů služby azure a kliknutím na tlačítko **vytvořit novou službu**.

    ![Vytvoření KB ](../media/qnamaker-how-to-create-kb/create-new-service.png)
    
5. Pokud jste ještě nevytvořili služby QnA Maker, vyberte **vytvoření služby QnA**. Jinak vyberte službu QnA Maker z rozevírací nabídky v kroku 2. Vyberte službu QnA Maker, který bude hostitelem znalostní bázi Knowledge Base.

    ![Nastavení služby QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. Vytvořit prázdný znalostní báze 

    ![Sada zdrojů dat](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Poskytnout služby **název.** Duplicitní názvy jsou podporované a jsou také podporovány speciální znaky.
    - Přeskočte nahrávání souborů nebo adresy URL, jak chcete použít data ze znalostní báze Preview. Teď vytvořte prázdný znalostní báze.

7. Vyberte **Vytvořit**.

    ![Vytvoření KB](../media/qnamaker-how-to-create-kb/create-kb.png)

8. V této nové znalostní bázi Knowledge base, otevřete **nastavení** a klikněte na **Import znalostní báze**. Importuje otázky, odpovědi a metadata a zachová názvy zdrojů dat, z nichž byly extrahovat.

   ![Import znalostní báze](../media/qnamaker-how-to-migrate-kb/Import.png)

9. **Test** nové znalostní báze pomocí panelu testu. Zjistěte, jak [testování znalostní báze](../How-To/test-knowledge-base.md).
10. **Publikování** znalostní bázi knowledge base. Zjistěte, jak [publikování znalostní báze](../How-To/publish-knowledge-base.md).
11. Použijte koncový bod níže v kódu aplikace nebo robota. Tady najdete postup [vytvořit robota QnA](../Tutorials/create-qna-bot.md).

    ![Hodnoty QnA Maker](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

Všechny znalostní báze knowledge base obsah - otázky, odpovědi a metadata, společně s názvy zdrojových souborů a adresy URL, v tomto okamžiku jsou importovány do nové znalostní báze. 

## <a name="chatlogs-and-alterations"></a>Chatlogs a změnách v podniku
Změny (synonyma) nejsou importovány automaticky. Použití [rozhraní API V2](https://aka.ms/qnamaker-v2-apis) export změny ze zásobníku preview a [V4 rozhraní API](https://aka.ms/qnamaker-v4-apis) nahradit v nové zásobníku.

Neexistuje žádný způsob, jak migrovat chatlogs, protože novým zásobníkem používá pro ukládání chatlogs Application Insights. Můžete ale stáhnout chatlogs z [portálu preview](https://aka.ms/qnamaker-old-portal).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Upravit znalostní bázi knowledge base](../How-To/edit-knowledge-base.md)

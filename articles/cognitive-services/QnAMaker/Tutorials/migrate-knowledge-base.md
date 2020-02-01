---
title: Migrace základů znalostní báze – QnA Maker
titleSuffix: Azure Cognitive Services
description: Migrace znalostní báze vyžaduje export z jedné znalostní báze a pak se importuje do jiného.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b441eb1e6531030a998fe628ae833b29a5d9fe5a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902048"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrace znalostní báze pomocí exportu importu

Migrace znalostní báze vyžaduje export z jedné znalostní báze a pak se importuje do jiného.

## <a name="prerequisites"></a>Požadavky

* Vytvoření [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) předtím, než začnete.
* Nastavení nové [služby QnA maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrace znalostní báze z QnA Maker
1. Přihlaste se k [portálu QnA maker](https://qnamaker.ai).
1. Vyberte znalostní bázi původu, kterou chcete migrovat.

1. Na stránce **Nastavení** vyberte **exportovat znalostní bázi** a Stáhněte soubor. TSV, který obsahuje obsah vaší zdrojové znalostní báze – otázky, odpovědi, metadata, následné výzvy a názvy zdrojů dat, ze kterých byly extrahovány.

1. V horní nabídce vyberte **vytvořit znalostní bázi** a pak vytvořte _prázdnou_ znalostní bázi. Je prázdná, protože když ji vytvoříte, nebudete přidávat žádné adresy URL ani soubory. Ty jsou přidány během kroku importu po vytvoření.

    Nakonfigurujte znalostní bázi. Nastavte jenom nový název znalostní báze. Duplicitní názvy jsou podporovány a jsou také podporovány speciální znaky.

    Nevybírejte vše z kroku 4, protože při importu souboru budou tyto hodnoty přepsány.

1. V kroku 5 vyberte **vytvořit**.

1. V této nové znalostní bázi otevřete kartu **Nastavení** a vyberte **importovat znalostní bázi**. Tím se importují otázky, odpovědi, metadata, následné výzvy a uchovávají se názvy zdrojů dat, ze kterých byly extrahovány.

   > [!div class="mx-imgBorder"]
   > [![importovat znalostní bázi Knowledge Base](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Test** nové znalostní báze pomocí panelu Test. Zjistěte, jak [testování znalostní báze](../How-To/test-knowledge-base.md).
1. **Publikování** znalostní báze. Zjistěte, jak [publikovat znalostní báze](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Použijte koncový bod v kódu aplikace nebo bot. Zde uvedený postup [vytváření robotů QnA](../Tutorials/create-qna-bot.md).

    ![Nástroj QnA Maker hodnoty](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    V tomto okamžiku všechny znalostní báze obsah – otázky a odpovědi metadat, společně s názvy zdrojových souborů a adresy URL, importují do nové znalostní báze.

## <a name="chat-logs-and-alterations"></a>Protokoly a změny chatu
Změny bez rozlišení velkých a malých písmen (synonym) se neimportují automaticky. Použijte [rozhraní v4 API](https://go.microsoft.com/fwlink/?linkid=2092179) pro přesun změn v nové znalostní bázi.

Neexistuje žádný způsob, jak migrovat protokoly konverzace, protože nová znalostní báze používá Application Insights pro ukládání protokolů chatu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-To/edit-knowledge-base.md)

---
title: Migrace znalostních bází – QnA Maker
titleSuffix: Azure Cognitive Services
description: Migrace znalostní báze vyžaduje export z jednoho znalostní báze knowledge base a potom importovat do jiného.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 04ee592122d7c76396f091f8d249518976682004
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446598"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrace znalostní báze pomocí exportu importu

Migrace znalostní báze vyžaduje export z jednoho znalostní báze knowledge base a potom importovat do jiného. 

## <a name="prerequisites"></a>Požadavky

* Vytvoření [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) předtím, než začnete.
* Nastavte nový [služba QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Znalostní báze migraci z nástroje QnA Maker
1. Přihlaste se k [portál QnA Maker](https://qnamaker.ai).
1. Vyberte ve znalostní bázi, kterou chcete migrovat.

1. Na **nastavení** stránce **exportovat znalostní báze knowledge base** chcete stáhnout soubor TSV, který obsahuje obsah znalostní báze – otázky, odpovědi, metadata, a ze které se názvy zdroje dat extrahovat.

1. Vyberte **vytvoření znalostní báze** v horní nabídce vytvořte prázdný znalostní báze. 

    ![Nastavení zdroje dat](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Zadejte vaši službu **název.** Duplicitní názvy jsou podporovány a jsou také podporovány speciální znaky.

1. Vyberte **Vytvořit**.

    ![Vytvoření znalostní BÁZE](../media/qnamaker-how-to-create-kb/create-kb.png)

1. V této nové znalostní báze knowledge base, otevřete **nastavení** kartě a vyberte **Import znalostní báze**. Importuje otázky, odpovědi a metadat a zachová názvy zdrojů dat, z nichž byly extrahovány.

   ![Import znalostní báze](../media/qnamaker-how-to-migrate-kb/Import.png)

1. **Test** nové znalostní báze pomocí panelu Test. Zjistěte, jak [testování znalostní báze](../How-To/test-knowledge-base.md).
1. **Publikování** znalostní báze. Zjistěte, jak [publikovat znalostní báze](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).
1. Použijte koncový bod v aplikaci nebo robotovi kódu. Zde uvedený postup [vytváření robotů QnA](../Tutorials/create-qna-bot.md).

    ![Nástroj QnA Maker hodnoty](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    V tomto okamžiku všechny znalostní báze obsah – otázky a odpovědi metadat, společně s názvy zdrojových souborů a adresy URL, importují do nové znalostní báze. 

## <a name="chat-logs-and-alterations"></a>Protokoly chatu a změn
Malá a velká písmena změny (synonym) nejsou importovány automaticky. Použití [V4 API](https://go.microsoft.com/fwlink/?linkid=2092179) přesunout změny v nové znalostní báze.

Neexistuje žádný způsob, jak migrovat chatu, protože nový znalostní báze pomocí Application Insights pro ukládání protokolů chatu. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-To/edit-knowledge-base.md)

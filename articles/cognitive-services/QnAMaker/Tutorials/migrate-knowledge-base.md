---
title: Migrace základů znalostní báze – QnA Maker
description: Migrace znalostní báze vyžaduje export z jedné znalostní báze a pak se importuje do jiného.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: how-to
ms.date: 11/09/2020
ms.openlocfilehash: 78e22a9ec150435c1bc83873a2fcf0a00560c0c1
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94375954"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrace znalostní báze pomocí exportu a importu

Migrace je proces vytvoření nové znalostní báze z existující znalostní báze. Můžete to udělat z několika důvodů:

* proces zálohování a obnovení
* Kanál CI/CD
* přesunout oblasti

Migrace znalostní báze vyžaduje export z existující znalostní báze a následně import do jiného.

> [!NOTE]
> Pomocí následujících pokynů Migrujte stávající znalostní bázi do nové QnA Maker spravované (Preview).

## <a name="prerequisites"></a>Požadavky

* Než začnete, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/cognitive-services/) .
* Nastavení nové [služby QnA maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrace znalostní báze z QnA Maker
1. Přihlaste se k [portálu QnA maker](https://qnamaker.ai).
1. Vyberte znalostní bázi původu, kterou chcete migrovat.

1. Na stránce **Nastavení** vyberte **exportovat znalostní bázi** a Stáhněte soubor. TSV, který obsahuje obsah vaší zdrojové znalostní báze – otázky, odpovědi, metadata, následné výzvy a názvy zdrojů dat, ze kterých byly extrahovány. ID QnA, která se exportují s dotazy a odpověďmi, se dají použít k aktualizaci konkrétní dvojice QnA pomocí [rozhraní API pro aktualizaci](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). ID QnA pro konkrétní dvojici QnA zůstane beze změny v rámci více operací exportu.

1. V horní nabídce vyberte **vytvořit znalostní bázi** a pak vytvořte _prázdnou_ znalostní bázi. Je prázdná, protože když ji vytvoříte, nebudete přidávat žádné adresy URL ani soubory. Ty jsou přidány během kroku importu po vytvoření.

    Nakonfigurujte znalostní bázi. Nastavte jenom nový název znalostní báze. Jsou podporovány duplicitní názvy a podporovány jsou také speciální znaky.

    Nevybírejte vše z kroku 4, protože při importu souboru budou tyto hodnoty přepsány.

1. V kroku 5 vyberte **vytvořit**.

1. V této nové znalostní bázi otevřete kartu **Nastavení** a vyberte **importovat znalostní bázi**. Tím se importují otázky, odpovědi, metadata, následné výzvy a uchovávají se názvy zdrojů dat, ze kterých byly extrahovány. **Páry QnA vytvořené v novém znalostní bázi musí mít stejné ID QnA, jaké je k dispozici v exportovaném souboru**. To vám pomůže vytvořit přesnou repliku znalostní báze.

   > [!div class="mx-imgBorder"]
   > [![Importovat znalostní bázi Knowledge Base](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Otestujte** novou znalostní bázi pomocí panelu Test. Naučte se [testovat znalostní bázi](../How-To/test-knowledge-base.md).

1. **Publikujte** znalostní bázi a vytvořte robota chatu. Naučte se [publikovat znalostní bázi](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Pomocí programové migrace znalostní báze z QnA Maker

Proces migrace je programově dostupný pomocí následujících rozhraní REST API:

**Export**

* [Stáhnout rozhraní API znalostní báze Knowledge Base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Import**

* [Nahradit rozhraní API (znovu načíst se stejným ID znalostní báze)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [Vytvořit rozhraní API (načíst s novým ID znalostní báze)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Protokoly a změny chatu
Změny bez rozlišení velkých a malých písmen (synonym) se neimportují automaticky. Použijte [rozhraní v4 API](https://go.microsoft.com/fwlink/?linkid=2092179) pro přesun změn v nové znalostní bázi.

Neexistuje žádný způsob, jak migrovat protokoly konverzace, protože nová znalostní báze používá Application Insights pro ukládání protokolů chatu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-To/edit-knowledge-base.md)

---
title: Migrace znalostních bází – QnA Maker
description: Migrace znalostní báze vyžaduje export z jedné znalostní báze a následný import do jiné databáze.
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258086"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>Migrace znalostní báze pomocí exportu a importu

Migrace je proces vytváření nové znalostní báze z existující znalostní báze. Můžete to udělat z několika důvodů:

* proces zálohování a obnovení
* Ci/CD potrubí
* přesunout regiony

Migrace znalostní báze vyžaduje export z existující znalostní báze a následný import do jiné databáze.

## <a name="prerequisites"></a>Požadavky

* Než začnete, vytvořte si [bezplatný účet.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Nastavení nové [služby QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>Migrace znalostní báze z QnA Makeru
1. Přihlaste se k [portálu QnA Maker](https://qnamaker.ai).
1. Vyberte znalostní bázi původu, kterou chcete migrovat.

1. Na stránce **Nastavení** vyberte **Exportovat znalostní bázi** a stáhněte soubor TSV, který obsahuje obsah vaší znalostní báze Origin – otázky, odpovědi, metadata, následné výzvy a názvy zdrojů dat, ze kterých byly extrahovány.

1. Vyberte **Vytvořit znalostní bázi** z horní nabídky a vytvořte _prázdnou_ znalostní bázi. Je prázdný, protože při jeho vytváření nebudete přidávat žádné adresy URL ani soubory. Ty jsou přidány během kroku importu, po vytvoření.

    Nakonfigurujte znalostní bázi. Nastavte pouze nový název znalostní báze. Duplicitní názvy jsou podporovány a jsou podporovány také speciální znaky.

    Nevybírejte nic z kroku 4, protože tyto hodnoty budou přepsány při importu souboru.

1. V kroku 5 vyberte **Vytvořit**.

1. V této nové znalostní bázi otevřete kartu **Nastavení** a vyberte **Importovat znalostní bázi**. Tím se importují otázky, odpovědi, metadata, výzvy zpracování a zachová se názvy zdrojů dat, ze kterých byly extrahovány.

   > [!div class="mx-imgBorder"]
   > [![Import znalostní báze](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. **Otestujte** novou znalostní bázi pomocí testovacího panelu. Přečtěte si, jak [otestovat znalostní bázi](../How-To/test-knowledge-base.md).

1. **Publikujte** znalostní bázi a vytvořte chatovacího robota. Přečtěte si, jak [publikovat znalostní bázi](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base).

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>Programově migrovat znalostní bázi z QnA Makeru

Proces migrace je programově k dispozici pomocí následujících rozhraní REST API:

**Export**

* [Stažení rozhraní API znalostní báze](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**Import**

* [Nahradit rozhraní API (znovu načíst se stejným ID znalostní báze)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [Vytvoření rozhraní API (načtení s novým ID znalostní báze)](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>Protokoly a změny chatu
Změny bez rozlišování velkých a malých písmen (synonyma) nejsou importovány automaticky. Pomocí [v4 API](https://go.microsoft.com/fwlink/?linkid=2092179) přesunout změny v nové znalostní bázi.

Neexistuje žádný způsob, jak migrovat protokoly chatu, protože nová znalostní báze používá Application Insights pro ukládání protokolů chatu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Úprava znalostní báze](../How-To/edit-knowledge-base.md)

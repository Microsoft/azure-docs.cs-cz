---
title: Připojení k Twitteru z aplikací Azure Logic Apps
description: Automatizujte úkoly a pracovní postupy, které monitorují a spravují tweety, a navíc získejte data o sledujících, sledovaných uživatelích, dalších uživatelích, časových osách a dalších pracovních postupech pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 8ffd0fd558cf759fadd912de9dff4acf49d9659f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789082"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Sledování a správa Twitteru pomocí Aplikací Azure Logic Apps

Pomocí aplikací Azure Logic Apps a konektoru Twitter můžete vytvářet automatizované úkoly a pracovní postupy, které monitorují a spravují data, která vás zajímají na Twitteru, jako jsou tweety, sledující, uživatelé a uživatelé, časové osy a další, spolu s dalšími akcemi, například:

* Sledujte, zveřejňujte a vyhledávejte tweety.
* Získejte data, jako jsou sledující, uživatelé sledující, časové osy a další.

Můžete použít aktivační události, které získají odpovědi z vašeho účtu Twitter a zpřístupní výstup dalším akcím. Akce, které provádějí úkoly s vaším účtem na Twitteru, můžete použít. Můžete také mít jiné akce použít výstup z akcí Twitter. Když se například objeví nový tweet s určitým hashtagem, můžete odesílat zprávy pomocí konektoru Slack. Pokud s aplikacemi logiky tečujete, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Váš účet na Twitteru a přihlašovací údaje uživatele

   Vaše přihlašovací údaje autorizují aplikaci logiky k vytvoření připojení a přístupu k účtu Twitter.

* Základní znalosti o [vytváření aplikací logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, kde chcete získat přístup ke svému účtu Twitter. Chcete-li začít s aktivační událostí Twitteru, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li použít akci Twitteru, spusťte aplikaci logiky s jinou aktivační událostí, například aktivační událostí **Opakování.**

## <a name="connect-to-twitter"></a>Připojení k Twitteru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [portálu Azure](https://portal.azure.com)a otevřete aplikaci logiky v Návrháři aplikací logiky, pokud už není otevřená.

1. Zvolte cestu: 

   * U prázdných aplikací logiky zadejte do vyhledávacího pole jako filtr "twitter". 
   V seznamu aktivačních událostí vyberte požadovanou aktivační událost. 

     -nebo-

   * Pro existující aplikace logiky: 
   
     * V posledním kroku, kam chcete přidat akci, zvolte **Nový krok**. 

       -nebo-

     * Mezi kroky, kam chcete přidat akci, přesuňte ukazatel myši na šipku mezi kroky. 
     Zvolte znaménko plus (**+**), které se zobrazí, a pak vyberte Přidat **akci**.
     
       Do vyhledávacího pole zadejte jako filtr "twitter". 
       V seznamu akcí vyberte požadovanou akci.

1. Pokud se zobrazí výzva k přihlášení na Twitter, přihlaste se teď, abyste mohli autorizovat přístup pro aplikaci logiky.

1. Poskytněte potřebné podrobnosti pro vybranou aktivační událost nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="examples"></a>Příklady

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter trigger: Když je vyslán nový tweet

Tato aktivační událost spustí pracovní postup aplikace logiky, když aktivační událost zjistí nový tweet, například s hashtagem #Seattle. Takže například, když jsou tyto tweety nalezeny, můžete přidat soubor s obsahem tweets 'do úložiště, jako je například účet Dropbox pomocí konektoru Dropbox. 

Volitelně můžete zahrnout podmínku, že způsobilé tweety musí pocházet od uživatelů s alespoň zadaným počtem sledujících.

**Enterprise example**: Tuto aktivační událost můžete použít ke sledování tweetů o vaší společnosti a nahrání obsahu tweetů do databáze SQL.

### <a name="twitter-action-post-a-tweet"></a>Twitter akce: Post pípání

Tato akce zveřejňuje tweet, ale můžete akci nastavit tak, aby tweet obsahoval obsah z tweetů nalezených dříve popsanou aktivační událostí. 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a limitech, které jsou popsány v popisu openapi (dříve Swagger) konektoru, projděte referenční [stránku konektoru](/connectors/twitterconnector/).

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech logic apps](../connectors/apis-list.md)

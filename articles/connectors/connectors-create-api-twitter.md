---
title: Připojení k Twitteru z Azure Logic Apps
description: Automatizujte úlohy a pracovní postupy, které sledují a spravují tweety, a navíc získáte data o sledujících, sledovaných uživatelích, dalších uživatelích, časových osách a dalších z účtu Twitteru pomocí Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: f2db6d614c3c12cb1be87724e79d79a16769d6b8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83829592"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Monitorování a správa Twitteru pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru Twitteru můžete vytvářet automatizované úlohy a pracovní postupy, které sledují a spravují data, která na Twitteru zajímáte, jako jsou tweety, sledující, uživatelé a uživatelé, časové osy a další, a další akce, například:

* Monitorování, odesílání a hledání tweety.
* Získejte data, jako jsou například sledující, sledované uživatele, časové osy a další.

Můžete použít triggery, které získávají odpovědi z vašeho účtu na Twitteru, a zpřístupnit výstup ostatním akcím. Můžete použít akce, které provádějí úlohy s vaším účtem Twitter. Můžete také použít další akce výstup z akcí Twitteru. Například když se objeví nový disk s určitým hashtagem, můžete odesílat zprávy pomocí konektoru časové rezervy. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Váš účet Twitteru a přihlašovací údaje uživatele

   Vaše přihlašovací údaje opravňují aplikaci logiky vytvořit připojení a přistupovat k vašemu účtu na Twitteru.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ke které chcete získat přístup k vašemu účtu na Twitteru. Pokud chcete začít s triggerem na Twitteru, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci Twitteru, spusťte aplikaci logiky s jinou triggerem, například triggerem **opakování** .

## <a name="connect-to-twitter"></a>Připojení k Twitteru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Zvolit cestu: 

   * Pro prázdné Logic Apps do vyhledávacího pole zadejte jako filtr "Twitter". 
   V seznamu triggery vyberte aktivační událost, kterou chcete. 

     -nebo-

   * Pro existující aplikace logiky: 
   
     * V posledním kroku, kam chcete přidat akci, vyberte možnost **Nový krok**. 

       -nebo-

     * Mezi kroky, do kterých chcete přidat akci, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
     Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.
     
       Do vyhledávacího pole zadejte jako filtr "Twitter". 
       V seznamu akce vyberte akci, kterou chcete.

1. Pokud se zobrazí výzva k přihlášení do Twitteru, přihlaste se hned, abyste mohli autorizovat přístup k vaší aplikaci logiky.

1. Zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="examples"></a>Příklady

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Spouštěč Twitteru: když se publikuje nový disk

Tato aktivační událost spustí pracovní postup aplikace logiky, když Trigger detekuje nový přímnožinu, například s hashtagem, #Seattle. Pokud se například najde tyto tweety, můžete do úložiště přidat soubor s obsahem tweety, jako je například účet Dropboxu pomocí konektoru Dropboxu. 

Volitelně můžete zahrnout podmínku, kterou musí tweety nárokovat od uživatelů s alespoň stanoveným počtem sledujících.

**Příklad v podniku**: pomocí této aktivační události můžete monitorovat tweety o vaší společnosti a nahrát obsah tweety do databáze SQL.

### <a name="twitter-action-post-a-tweet"></a>Akce Twitteru: odeslání proložit

Tato akce odešle soubor s přístavou, ale můžete nastavit akci tak, aby provedla obsah z tweety nalezeného dříve popsaným triggerem. 

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/twitterconnector/)konektoru.

## <a name="get-support"></a>Získání podpory

* Otázky najdete na stránce s [dotazem na&Microsoft Q pro Azure Logic Apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)

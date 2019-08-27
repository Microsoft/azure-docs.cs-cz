---
title: Připojení k Yammeru z Azure Logic Apps | Microsoft Docs
description: Automatizujte úlohy a pracovní postupy, které sledují, účtují a spravují zprávy, informační kanály a další ve službě Yammer pomocí Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 9228a94dcf27d8987b16e2caa2681cf973db0657
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050638"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Monitorování a Správa účtu Yammer pomocí Azure Logic Apps

Pomocí Azure Logic Apps a konektoru služby Yammer můžete vytvářet automatizované úlohy a pracovní postupy, které sledují a spravují zprávy, informační kanály a další informace ve vašem účtu Yammer, a také další akce, například:

* Sledovat, když se v sledovaných kanálech a skupinách zobrazí nové zprávy.
* Získejte zprávy, skupiny, sítě, podrobnosti uživatelů a další.
* Zprávy post a like.

Můžete použít triggery, které získávají odpovědi z vašeho účtu Yammer, a zpřístupnit výstup ostatním akcím. Můžete použít akce, které provádějí úlohy s vaším účtem Yammeru. V akcích služby Yammer můžete také použít další akce. Například když se nové zprávy zobrazí v části informační kanály nebo skupiny, můžete tyto zprávy sdílet s konektorem časové rezervy. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/). 

* Váš účet služby Yammer a přihlašovací údaje uživatele

   Vaše přihlašovací údaje autorizují vaši aplikaci logiky, aby vytvořila připojení a měl přístup k vašemu účtu Yammer.

* Základní znalosti o [tom, jak vytvářet aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikace logiky, ve které chcete získat přístup k vašemu účtu Yammer. Pokud chcete začít s triggerem Yammeru, [vytvořte prázdnou aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete použít akci Yammeru, spusťte aplikaci logiky s jinou triggerem, například triggerem **opakování** .

## <a name="connect-to-yammer"></a>Připojení k Yammeru

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Přihlaste se k [Azure Portal](https://portal.azure.com)a otevřete aplikaci logiky v návrháři aplikace logiky, pokud už není otevřený.

1. Zvolit cestu: 

   * Pro prázdné aplikace logiky zadejte do vyhledávacího pole "Yammer" jako filtr. 
   V seznamu triggery vyberte aktivační událost, kterou chcete. 

     -nebo-

   * Pro existující aplikace logiky: 
   
     * V posledním kroku, kam chcete přidat akci, vyberte možnost **Nový krok**. 

       -nebo-

     * Mezi kroky, do kterých chcete přidat akci, přesuňte ukazatel myši na šipku mezi jednotlivými kroky. 
     Vyberte symbol plus ( **+** ), který se zobrazí, a pak vyberte **přidat akci**.
     
       Do vyhledávacího pole zadejte jako filtr "Yammer". 
       V seznamu akce vyberte akci, kterou chcete.

1. Pokud se zobrazí výzva, abyste se přihlásili ke službě Yammer, přihlaste se hned, abyste mohli povolený přístup.

1. Zadejte potřebné podrobnosti pro vybraný Trigger nebo akci a pokračujte v vytváření pracovního postupu aplikace logiky.

## <a name="connector-reference"></a>Referenční informace ke konektorům

Technické podrobnosti o aktivačních událostech, akcích a omezeních, které jsou popsány v popisu OpenAPI konektoru (dříve Swagger), najdete na [referenční stránce](/connectors/yammer/)konektoru.

## <a name="get-support"></a>Získat podporu

* Pokud máte dotazy, navštivte [fórum Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Pokud chcete zanechat své nápady na funkce nebo hlasovat, navštivte [web zpětné vazby od uživatelů Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Další kroky

* Další informace o dalších [konektorech Logic Apps](../connectors/apis-list.md)
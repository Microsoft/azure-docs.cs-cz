---
title: Zprávy exchange pro scénáře podnikové integrace B2B
description: Příjem a odesílání zpráv B2B mezi obchodními partnery v Azure Logic Apps pomocí balíčku Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 01b2bd464db51e255930fe83a3f4321687322275
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77151103"
---
# <a name="receive-and-send-b2b-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Příjem a odesílání zpráv B2B pomocí Azure Logic Apps a Enterprise Integration Pack

Pokud máte integrační účet, který definuje obchodní partnery a smlouvy, můžete vytvořit automatizovaný pracovní postup mezi podniky (B2B), který si vyměňuje zprávy mezi obchodními partnery pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) s enterprise integration [packem](../logic-apps/logic-apps-enterprise-integration-overview.md). Azure Logic Apps pracuje s konektory, které podporují as2, X12, EDIFACT a RosettaNet oborové standardní protokoly. Tyto konektory můžete také kombinovat s dalšími [konektory dostupnými v Logic Apps](../connectors/apis-list.md), například Salesforce a Office 365 Outlook.

Tento článek ukazuje, jak vytvořit aplikaci logiky, která obdrží požadavek HTTP pomocí požadavku aktivační události, dekóduje obsah zprávy pomocí as2 a X12 akce a potom vrátí odpověď pomocí akce odpověď.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Prázdná aplikace logiky, takže můžete vytvořit pracovní postup B2B pomocí aktivační události [Požadavek,](../connectors/connectors-native-reqres.md) po které následují tyto akce:

  * [DEKÓDOVÁNÍ AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Podmínka](../logic-apps/logic-apps-control-flow-conditional-statement.md), která odešle [odpověď](../connectors/connectors-native-reqres.md) na základě toho, zda akce Dekódování AS2 je úspěšná nebo neúspěšná

  * [Dekódování zprávy X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Pokud s aplikacemi logiky teprve začínáte, přečtěte [si, co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) a [Úvodní příručka: Vytvořte první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Účet integrace,](../logic-apps/logic-apps-enterprise-integration-accounts.md) který je přidružený k vašemu předplatnému Azure a propojený s vaší aplikací logiky. Vaše aplikace logiky i účet integrace musí existovat ve stejném umístění nebo v oblasti Azure.

* Alespoň dva [obchodní partneři,](../logic-apps/logic-apps-enterprise-integration-partners.md) které jste již definovali ve svém integračním účtu spolu s [dohodami AS2 a X12](logic-apps-enterprise-integration-agreements.md) pro tyto partnery.

## <a name="add-request-trigger"></a>Přidat aktivační událost požadavku

Tento příklad používá Návrhář eposu logiky na webu Azure Portal, ale můžete postupovat podle podobných kroků pro Návrháře aplikací logiky v sadě Visual Studio.

1. Na [webu Azure Portal](https://portal.azure.com)otevřete prázdnou aplikaci logiky v Návrháři aplikací logiky.

1. Do vyhledávacího pole `when a http request`zadejte a vyberte **Při přijetí požadavku HTTP,** který chcete použít jako aktivační událost.

   ![Vyberte aktivační událost požadavku, chcete-li spustit pracovní postup aplikace logiky.](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Ponechte pole **Schéma JSON tělo požadavku** prázdné, protože zpráva X12 je plochý soubor.

   ![Ponechat "Požádat tělo JSON schéma" prázdné](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

   Tento krok generuje **adresu URL HTTP POST** pro odeslání požadavku, který aktivuje aplikaci logiky. Chcete-li tuto adresu URL zkopírovat, vyberte ikonu kopírování vedle adresy URL.

   ![Adresa URL vygenerovaná pro aktivační událost požadavku pro příjem hovorů](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>Přidat akci dekódování AS2

Nyní přidejte akce B2B, které chcete použít. Tento příklad používá akce AS2 a X12.

1. Pod aktivační událostí vyberte **Nový krok**. Chcete-li skrýt podrobnosti aktivační události, klikněte na záhlaví aktivační události.

   ![Přidání dalšího kroku do pracovního postupu aplikace logiky](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. V části **Zvolte akci**zadejte `as2 decode`do vyhledávacího pole a vyberte **DEkódování AS2 (v2).**

   ![Najít a vybrat "AS2 Decode (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Pro **message dekódovat** vlastnost, zadejte vstup, který má akce AS2 `body` dekódovat, což je obsah, který je přijat aktivační události požadavku HTTP. Tento obsah můžete zadat jako vstup několika způsoby, a to buď ze seznamu dynamického obsahu, nebo jako výraz:

   * Chcete-li vybrat ze seznamu, který zobrazuje dostupné výstupy aktivačních událostí, klikněte do pole **Zpráva, kterou chcete dekódovat.** Po zobrazení seznamu dynamického obsahu vyberte v části **Při přijetí požadavku HTTP**hodnotu vlastnosti **Body,** například:

     ![Vyberte hodnotu "Tělo" z aktivační události](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Chcete-li zadat výraz, který `body` odkazuje na výstup aktivační události, klepněte do pole **Zpráva, kterou chcete dekódovat.** Po zobrazení seznamu dynamického obsahu vyberte **výraz**. V editoru výrazů zadejte výraz sem a vyberte **OK**:

     `triggerOutputs()['body']`

     Nebo do pole **Zpráva k dekódování** přímo zadejte tento výraz:

     `@triggerBody()`

     Výraz překládá na **body** token.

     ![Přeložený tělesný výstup ze spouště](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Pro **vlastnost Záhlaví zprávy** zadejte všechna záhlaví požadovaná pro akci AS2, která jsou popsána `headers` obsahem přijatým aktivační událostí požadavku HTTP.

   Chcete-li zadat výraz, který `headers` odkazuje na výstup aktivační události, klepněte do pole **Záhlaví zprávy.** Po zobrazení seznamu dynamického obsahu vyberte **výraz**. V editoru výrazů zadejte výraz sem a vyberte **OK**:

   `triggerOutputs()['Headers']`

   Chcete-li tento výraz vyřešit jako tento token, přepněte mezi návrhářem a zobrazeníkódu, například:

   ![Přeložený výstup hlaviček z aktivační události](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Přidat akci Odpovědi pro oznámení o přijetí zprávy

Chcete-li oznámit obchodnímu partnerovi, že zpráva byla přijata, můžete vrátit odpověď, která obsahuje oznámení o dispozičním řešení zprávy AS2 (MDN) pomocí akce **Odpověď.** Přidáním této akce ihned po akci **DEKódování AS2,** pokud by tato akce selhala, aplikace logiky nepokračuje ve zpracování.

1. V části akce **Dekódování AS2** vyberte **Nový krok**.

1. V části **Zvolte akci**vyberte v položce hledání **položku Předdefinovaný .** Do vyhledávacího pole zadejte `condition`. V seznamu **Akce** vyberte **Možnost Podmínka**.

   ![Přidání akce Podmínka](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Nyní se zobrazí obrazec podmínky, včetně cest pro to, zda je podmínka splněna či nikoli.

   ![Tvar podmínky s cestami rozhodnutí](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Nyní zadejte podmínku, kterou chcete vyhodnotit. Do pole **Zvolte hodnotu** zadejte tento výraz:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   Ve středním poli zkontrolujte, zda je `is equal to`operace porovnání nastavena na . Do pole na pravé straně `Expected`zadejte hodnotu . Chcete-li získat výraz vyřešit jako tento token, přepněte mezi návrháře a zobrazení kódu.

   ![Tvar podmínky s cestami rozhodnutí](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Nyní zadejte odpovědi vrátit, zda **as2 decode** akce úspěšné nebo ne.

   1. Pro případ, kdy akce **DEKódování AS2** proběhne úspěšně, vyberte v obrazci **If true** možnost **Přidat akci**. V části **Zvolte akci**zadejte `response`do vyhledávacího pole a vyberte **Odpověď**.

      ![Vyhledání a výběr akce "Odpověď"](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Chcete-li získat přístup k as2 MDN z výstupu akce **DEKódovat AS2,** zadejte tyto výrazy:

      * Do vlastnosti **Záhlaví** akce **Odpověď** zadejte tento výraz:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Do vlastnosti **Tělo** akce **Odpověď** zadejte tento výraz:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Chcete-li výrazy vyřešit jako tokeny, přepněte mezi návrhářem a zobrazení kódu:

      ![Přeložený výraz pro přístup k AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. V případě, kdy akce **Dekódování AS2** selže, vyberte v obrazci **If false** **možnost Přidat akci**. V části **Zvolte akci**zadejte `response`do vyhledávacího pole a vyberte **Odpověď**. Nastavte akci **Odpověď** tak, aby se vrátil požadovaný stav a chyba.

1. Uložte svou aplikaci logiky.

## <a name="add-decode-x12-message-action"></a>Přidat akci zprávy Decode X12

1. Nyní přidejte akci **zprávy Decode X12.** V části **Akce Odpověď** vyberte **Přidat akci**.

1. V části **Zvolte akci**zadejte `x12 decode`do vyhledávacího pole a vyberte **Dekódovat zprávu X12**.

   ![Najít a vybrat akci "Dekódovat zprávu X12"](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Pokud vás akce X12 vyzve k zadání informací o připojení, zadejte název připojení, vyberte účet integrace, který chcete použít, a pak vyberte **Vytvořit**.

   ![Vytvořit připojení X12 k integračnímu účtu](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Nyní zadejte vstup pro akci X12. Tento příklad používá výstup z akce AS2, což je obsah zprávy, ale všimněte si, že tento obsah je ve formátu objektu JSON a je zakódován base64. Takže musíte převést tento obsah na řetězec.

   Do **pole X12 Flat file pro dekódování** zadejte tento výraz, chcete-li převést výstup AS2:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Chcete-li získat výraz vyřešit jako tento token, přepněte mezi návrháře a zobrazení kódu.

    ![Převod obsahu kódu base64 na řetězec](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Uložte svou aplikaci logiky.

   Pokud potřebujete další kroky pro tuto aplikaci logiky, například dekódovat obsah zprávy a výstup obsahu ve formátu objektu JSON, pokračujte v vytváření aplikace logiky.

Nyní jste hotovi nastavení aplikace logiky B2B. V aplikaci reálného světa můžete chtít uložit dekódovaná data X12 do obchodní aplikace nebo úložiště dat. Například v těchto článcích:

* [Připojení k systémům SAP z Azure Logic Apps](../logic-apps/logic-apps-using-sap-connector.md)
* [Sledování, vytváření a správa souborů SFTP pomocí Aplikací SSH a Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Chcete-li připojit vlastní obchodní aplikace a použít tato api ve vaší aplikaci logiky, můžete přidat další akce nebo [napsat vlastní api](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Další kroky

* [Příjem a odpovídání na příchozí volání HTTPS](../connectors/connectors-native-reqres.md)
* [Výměna zpráv AS2 pro podnikovou integraci B2B](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Zprávy Exchange X12 pro podnikovou integraci B2B](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Další informace o [balíčku Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
---
title: Zprávy Exchange pro scénáře integrace B2B Enterprise
description: Přijímání a posílání zpráv B2B mezi obchodními partnery v Azure Logic Apps pomocí Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: afae49cf6ee44b138a55f58f415fc761308b7894
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542372"
---
# <a name="receive-and-confirm--b2b-as2-messages-by-using-azure-logic-apps-and-enterprise-integration-pack"></a>Příjem a potvrzení zpráv B2B AS2 pomocí Azure Logic Apps a Enterprise Integration Pack

Když máte účet pro integraci, který definuje obchodní partnery a smlouvy, můžete vytvořit pracovní postup automatizovaného obchodování do firmy (B2B), který vyměňuje zprávy mezi obchodními partnery pomocí [Azure Logic Apps](../logic-apps/logic-apps-overview.md) s [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md). Azure Logic Apps funguje s konektory, které podporují standardní protokoly AS2, X12, EDIFACT a RosettaNet. Tyto konektory můžete kombinovat i s dalšími [konektory dostupnými v Logic Apps](../connectors/apis-list.md), například Salesforce a Office 365 Outlook.

Tento článek ukazuje, jak vytvořit aplikaci logiky, která přijímá požadavek HTTP pomocí triggeru žádosti, dekóduje obsah zprávy pomocí akcí AS2 a X12 a potom vrátí odpověď pomocí akce Response.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Prázdná aplikace logiky, abyste mohli pracovní postup B2B vytvořit pomocí triggeru [žádosti](../connectors/connectors-native-reqres.md) , na který následují tyto akce:

  * [Dekódování AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)

  * [Podmínka](../logic-apps/logic-apps-control-flow-conditional-statement.md), která pošle [odpověď](../connectors/connectors-native-reqres.md) na základě toho, jestli je akce dekódování AS2 úspěšná nebo selže

  * [Dekódovat zprávu X12](../logic-apps/logic-apps-enterprise-integration-x12.md) 

  Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps?](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Účet pro integraci](./logic-apps-enterprise-integration-create-integration-account.md) , který je přidružený k vašemu předplatnému Azure a propojený s vaší aplikací logiky. Vaše aplikace logiky i účet pro integraci musí existovat ve stejném umístění nebo oblasti Azure.

* Alespoň dva [obchodní partneři](../logic-apps/logic-apps-enterprise-integration-partners.md) , které jste už definovali v účtu pro integraci společně s [AS2 a X12 smlouvami](logic-apps-enterprise-integration-agreements.md) pro tyto partnery.

## <a name="add-request-trigger"></a>Přidat aktivační událost žádosti

Tento příklad používá návrháře aplikace logiky v Azure Portal, ale můžete postupovat podle podobných kroků v návrháři aplikace logiky v aplikaci Visual Studio.

1. V [Azure Portal](https://portal.azure.com)otevřete prázdnou aplikaci logiky v návrháři aplikace logiky.

1. Do vyhledávacího pole zadejte `when a http request` a vyberte, **když se přijme požadavek HTTP** , který se má použít jako Trigger.

   ![Pokud chcete spustit pracovní postup aplikace logiky, vyberte Trigger žádosti.](./media/logic-apps-enterprise-integration-b2b/select-http-request-trigger.png)

1. Pole **schématu JSON textu žádosti** nechte prázdné, protože zpráva X12 je plochý soubor.

   ![Ponechat "schéma JSON textu žádosti" prázdné](./media/logic-apps-enterprise-integration-b2b/receive-trigger-message-body-json-schema.png)

1. Až budete hotovi, na panelu nástrojů návrháře vyberte **Uložit**.

   Tento krok vygeneruje **adresu URL post protokolu HTTP** , která se má použít pro odeslání žádosti, která spouští aplikaci logiky. Tuto adresu URL můžete zkopírovat tak, že vyberete ikonu kopírování vedle adresy URL.

   ![Adresa URL vygenerovaná pro aktivační událost požadavku pro příjem volání](./media/logic-apps-enterprise-integration-b2b/generated-url-request-trigger.png)

## <a name="add-as2-decode-action"></a>Přidat akci dekódovat AS2

Nyní přidejte akce B2B, které chcete použít. V tomto příkladu se používají akce AS2 a X12.

1. V aktivační události vyberte **Nový krok**. Chcete-li skrýt podrobnosti triggeru, klikněte na záhlaví triggeru.

   ![Přidat další krok do pracovního postupu aplikace logiky](./media/logic-apps-enterprise-integration-b2b/add-new-action-under-trigger.png)

1. V části **zvolit akci**zadejte do vyhledávacího pole `as2 decode` a vyberte **AS2 Decode (v2)**.

   ![Vyhledejte a vyberte "AS2 dekódování (v2)"](./media/logic-apps-enterprise-integration-b2b/add-as2-decode-action.png)

1. Pro vlastnost **zpráva k dekódování** zadejte vstup, který má AS2 akce dekódovat, což je `body` obsah přijatý triggerem požadavku HTTP. Tento obsah můžete zadat několika způsoby, buď ze seznamu dynamického obsahu, nebo jako výraz:

   * Pokud chcete vybrat ze seznamu, který zobrazuje dostupné výstupy triggeru, klikněte do pole **zpráva k dekódování** . Až se zobrazí seznam dynamického obsahu, v části **když se přijme požadavek HTTP**, vyberte hodnotu vlastnosti **body** , například:

     ![Vybrat hodnotu "tělo" z triggeru](./media/logic-apps-enterprise-integration-b2b/select-body-content-from-trigger.png)

   * Pokud chcete zadat výraz, který odkazuje na výstup triggeru `body` , klikněte do pole **zpráva k dekódování** . Po zobrazení seznamu dynamický obsah vyberte **výraz**. V editoru výrazů zadejte tento výraz a vyberte **OK**:

     `triggerOutputs()['body']`

     Nebo do pole **zpráva k dekódování** zadejte tento výraz přímo:

     `@triggerBody()`

     Výraz se přeloží na token **těla** .

     ![Vyřešený výstup textu z triggeru](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-body-expression.png)

1. Pro vlastnost **záhlaví zpráv** zadejte všechna záhlaví požadovaná pro akci AS2, která jsou popsána v `headers` obsahu přijatém triggerem požadavku HTTP.

   Pokud chcete zadat výraz, který odkazuje na výstup triggeru `headers` , klikněte do pole **záhlaví zpráv** . Po zobrazení seznamu dynamický obsah vyberte **výraz**. V editoru výrazů zadejte tento výraz a vyberte **OK**:

   `triggerOutputs()['Headers']`

   Chcete-li získat tento výraz pro vyřešení tohoto tokenu, přepněte mezi návrhářem a zobrazením kódu, například:

   ![Vyhodnocený výstup hlaviček z triggeru](./media/logic-apps-enterprise-integration-b2b/resolved-trigger-outputs-headers-expression.png)

## <a name="add-response-action-for-message-receipt-notification"></a>Přidat akci odpovědi pro oznámení o přijetí zprávy

Chcete-li oznámit obchodnímu partnerovi, že byla zpráva přijata, můžete vrátit odpověď, která obsahuje oznámení o AS2i zprávy o zařazování (MDN) pomocí akce **Response** . Když tuto akci přidáte hned po akci **dekódování AS2** , v případě neúspěchu akce aplikace logiky nebude pokračovat ve zpracování.

1. V akci **dekódování AS2** vyberte **Nový krok**.

1. V části **zvolit akci**vyberte v poli Hledat možnost **předdefinované**. Do vyhledávacího pole zadejte `condition`. V seznamu **Akce** vyberte **Podmínka**.

   ![Přidat akci podmínka](./media/logic-apps-enterprise-integration-b2b/add-condition-action.png)

   Nyní se zobrazí obrazec podmínka, včetně cest pro to, zda je podmínka splněna nebo ne.

   ![Snímek obrazovky zobrazuje obrazec podmínky s prázdnými cestami.](./media/logic-apps-enterprise-integration-b2b/added-condition-action.png)

1. Nyní zadejte podmínku, která má být vyhodnocena. Do pole **zvolit hodnotu** zadejte tento výraz:

   `@body('AS2_Decode')?['AS2Message']?['MdnExpected']`

   V prostředním poli se ujistěte, že je operace porovnání nastavená na `is equal to` . Do pole na pravé straně zadejte hodnotu `Expected` . Chcete-li získat výraz pro vyřešení tohoto tokenu, přepínejte mezi návrhářem a zobrazením kódu.

   ![Snímek obrazovky zobrazuje obrazec podmínky s přidaným stavem.](./media/logic-apps-enterprise-integration-b2b/expression-for-evaluating-condition.png)

1. Nyní zadejte odpovědi, které mají být vráceny, pokud je akce **dekódování AS2** úspěšná nebo ne.

   1. Pro případ, že je akce **dekódování AS2** úspěšná, vyberte v obrazci **při hodnotě true** možnost **přidat akci**. V části **zvolit akci**zadejte do vyhledávacího pole `response` a vyberte možnost **odpověď**.

      ![Najde a vybere akci Response (odpověď).](./media/logic-apps-enterprise-integration-b2b/select-http-response-action.png)

   1. Pokud chcete získat přístup k AS2 MDN z výstupu akce **dekódovat z AS2** , zadejte tyto výrazy:

      * Do vlastnosti **Headers** Action ( **odpověď** ) zadejte tento výraz:

        `@body('AS2_Decode')?['OutgoingMdn']?['OutboundHeaders']`

      * Do vlastnosti **tělo** akce **odpovědi** zadejte tento výraz:

        `@body('AS2_Decode')?['OutgoingMdn']?['Content']`

   1. Chcete-li získat výrazy pro řešení jako tokeny, přepínejte mezi návrhářem a zobrazením kódu:

      ![Vyřešený výraz pro přístup k AS2 MDN](./media/logic-apps-enterprise-integration-b2b/response-action-success-resolved-expression.png)

   1. Pro případ, že se akce **dekódování AS2** nepovede, vyberte v obrazci **Při false** možnost **přidat akci**. V části **zvolit akci**zadejte do vyhledávacího pole `response` a vyberte možnost **odpověď**. Nastavte akci **odpovědi** , která vrátí stav a požadovanou chybu.

1. Uložte aplikaci logiky.

## <a name="add-decode-x12-message-action"></a>Přidat akci dekódovat zprávu X12

1. Nyní přidejte akci **dekódovat zprávu X12** . V akci **odpověď** vyberte **přidat akci**.

1. V části **zvolit akci**zadejte do vyhledávacího pole `x12 decode` a vyberte **dekódovat zprávu X12**.

   ![Najít a vybrat akci dekódovat zprávu X12](./media/logic-apps-enterprise-integration-b2b/add-x12-decode-action.png)

1. Pokud se akce X12 vyzve k zadání informací o připojení, zadejte název připojení, vyberte účet pro integraci, který chcete použít, a pak vyberte **vytvořit**.

   ![Vytvořit připojení X12 k účtu pro integraci](./media/logic-apps-enterprise-integration-b2b/create-x12-integration-account-connection.png)

1. Nyní zadejte vstup pro akci X12. V tomto příkladu se používá výstup z akce AS2, což je obsah zprávy, ale Všimněte si, že tento obsah je ve formátu objektu JSON a má kódování Base64. Proto je nutné tento obsah převést na řetězec.

   Do pole **zakódovat zprávu plochého souboru X12** zadejte tento výraz pro převod výstupu AS2:

   `@base64ToString(body('AS2_Decode')?['AS2Message']?['Content'])`

    Chcete-li získat výraz pro vyřešení tohoto tokenu, přepínejte mezi návrhářem a zobrazením kódu.

    ![Převést obsah kódovaný v kódování Base64 na řetězec](./media/logic-apps-enterprise-integration-b2b/x12-decode-message-content.png)

1. Uložte aplikaci logiky.

   Pokud pro tuto aplikaci logiky potřebujete další kroky, například k dekódování obsahu zprávy a výstupu obsahu ve formátu objektu JSON, pokračujte v sestavování aplikace logiky.

Teď jste dokončili nastavování vaší aplikace logiky B2B. V reálné aplikaci můžete ukládat dekódovat X12 data do obchodní aplikace (LOB) nebo do úložiště dat. Podívejte se například na tyto články:

* [Připojení k systémům SAP z Azure Logic Apps](../logic-apps/logic-apps-using-sap-connector.md)
* [Monitorování, vytváření a správa souborů SFTP pomocí SSH a Azure Logic Apps](../connectors/connectors-sftp-ssh.md)

Pokud chcete připojit své vlastní obchodní aplikace a používat tato rozhraní API ve vaší aplikaci logiky, můžete přidat další akce nebo [napsat vlastní rozhraní API](../logic-apps/logic-apps-create-api-app.md).

## <a name="next-steps"></a>Další kroky

* [Přijímat příchozí volání HTTPS a reagovat na ně](../connectors/connectors-native-reqres.md)
* [Zprávy Exchange AS2 pro integraci B2B Enterprise](../logic-apps/logic-apps-enterprise-integration-as2.md)
* [Zprávy Exchange X12 pro integraci B2B Enterprise](../logic-apps/logic-apps-enterprise-integration-x12.md)
* Další informace o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)

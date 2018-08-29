---
title: Ověření XML pro podnikovou integraci B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Ověření XML se schématy pro řešení B2B v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.date: 07/08/2016
ms.openlocfilehash: e25f4e633b8cf9030bb0ce395f093f630db59f38
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43121109"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Ověření XML pro podnikovou integraci B2B v Azure Logic Apps sadou Enterprise Integration Pack

Často v scénáře B2B, partneři v smlouvu musí ujistěte, že zprávy, které si vyměňují jsou platné před zahájením zpracování dat. Pomocí konektoru ověřování XML v Enterprise Integration Pack můžete ověřit dokumenty proti předdefinovaným schématem.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Ověřit dokument s konektorem ověření XML

1. Vytvoření aplikace logiky a [propojit účet integrace aplikace](../logic-apps/logic-apps-enterprise-integration-accounts.md "se naučíte, jak propojit účet integrace aplikace logiky") , který má schéma, které chcete použít pro ověřování dat XML.

2. Přidat **požadavek – přijetí požadavku HTTP při** trigger aplikace logiky.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Chcete-li přidat **ověření XML** akce, zvolte **přidat akci**.

4. Chcete-li filtrovat všechny akce, které ten, který chcete, zadejte *xml* do vyhledávacího pole. Zvolte **ověření XML**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Chcete-li určit obsah XML, který chcete ověřit, vyberte **obsah**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Vyberte značky body jako obsah, který chcete ověřit.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. Chcete-li určit schématu, kterou chcete použít pro ověření předchozí *obsah* vstup, zvolte **název schématu**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Uložte si práci  

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Teď budete hotovi s nastavením vašeho konektoru ověření. V reálné aplikaci může být vhodné ukládat ověřenými daty v aplikaci – obchodní (LOB) jako je SalesForce. Chcete-li ověřené posílat do služby Salesforce, přidáte akci.

Otestovat ověření akci, vytvořte žádost na koncový bod HTTP.

## <a name="next-steps"></a>Další postup
[Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack")   


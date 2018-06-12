---
title: Ověření XML - Azure Logic Apps | Microsoft Docs
description: Ověření XML s schémata pro scénáře Azure Logic Apps a B2B pomocí Enterprise integračního balíčku
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: d700588f-2d8a-4c92-93eb-e1e6e250e760
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 901f0e576d28ab163fe2c46dff0594a338fbaf73
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35299595"
---
# <a name="validate-xml-for-enterprise-integration"></a>Ověření XML pro integraci enterprise

Často v scénáře B2B partnery v smlouvu musí Ujistěte se, že zprávy, které si vyměňují jsou platné, než můžete začít zpracování dat. Dokumenty pro předdefinované schéma můžete ověřit pomocí použití konektoru ověření XML v podniku integračního balíčku.

## <a name="validate-a-document-with-the-xml-validation-connector"></a>Ověřit dokument s konektorem ověření XML

1. Vytvoření aplikace logiky, a [propojit aplikaci se účtem integrace](../logic-apps/logic-apps-enterprise-integration-accounts.md "zjistěte, jak lze propojit účet integrace aplikace logiky") má schéma, kterou chcete použít pro ověření dat XML.

2. Přidat **požadavku - obdrží žádost HTTP při** aktivační svou aplikaci logiky.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1.png)

3. Chcete-li přidat **ověření XML** akce, zvolte **přidat akci**.

4. Chcete-li všechny akce, které ten, který chcete filtrovat, zadejte *xml* do vyhledávacího pole. Zvolte **ověření XML**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-2.png)

5. Chcete-li určit obsah XML, který chcete ověřit, vyberte **obsah**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-1-5.png)

6. Vyberte značky body jako obsah, který chcete ověřit.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-3.png)

7. K určení schématu, kterou chcete použít pro ověření předchozí *obsah* vstup, zvolte **název schématu**.

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-4.png)

8. Uložte si práci  

    ![](./media/logic-apps-enterprise-integration-xml-validation/xml-5.png)

Nyní jste hotovi s nastavením vašeho konektoru ověření. V reálné aplikaci můžete chtít uložit ověřená data v aplikaci obchodní (LOB) jako SalesForce. Chcete-li odeslat ověřené výstup do služby Salesforce, přidáte akci.

Pokud chcete otestovat ověření akci, vytvořte žádost na koncový bod HTTP.

## <a name="next-steps"></a>Další postup
[Další informace o integračního balíčku Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku")   


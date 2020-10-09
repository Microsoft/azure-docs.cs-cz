---
title: Ověření XML pro B2B Enterprise Integration
description: Ověří XML pomocí schémat v Azure Logic Apps s Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74792160"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Ověřování XML pro podnikovou integraci B2B v Azure Logic Apps pomocí Enterprise Integration Packu

Ve scénářích B2B se často musí obchodní partneři v rámci smlouvy ujistit, že jsou zprávy, které vyměňují, platné předtím, než může začít jakékoli zpracování dat. Dokumenty můžete v předdefinovaném schématu ověřit pomocí akce ověřování XML, která je k dispozici ve Enterprise Integration Pack.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné, [Zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Prázdná nebo existující aplikace logiky, ve které chcete použít akci ověření XML. Pokud s Logic Apps začínáte, přečtěte si téma [co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a [rychlý Start: Vytvoření první aplikace logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) , který je přidružený k vašemu předplatnému Azure, je propojený s aplikací logiky, kde plánujete použít akci ověření XML, a obsahuje schéma, které chcete použít k ověření obsahu XML. Vaše aplikace logiky i účet pro integraci musí existovat ve stejném umístění nebo oblasti Azure.

## <a name="add-xml-validation-action"></a>Přidat akci ověřování XML

1. V [Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v návrháři aplikace logiky.

1. Pokud máte prázdnou aplikaci logiky, v návrháři aplikace logiky zadejte do vyhledávacího pole `HTTP request` jako filtr a vyberte, **když se přijme požadavek HTTP** . V opačném případě pokračujte k dalšímu kroku.

1. V posledním kroku pracovního postupu vyberte **Nový krok**.

   Chcete-li přidat akci mezi stávajícími kroky, přesuňte ukazatel myši na šipku, která spojuje tyto kroky, aby se zobrazilo znaménko plus ( **+** ). Vyberte znaménko plus a pak vyberte **přidat akci**.

1. V části **zvolit akci**vyberte **předdefinovaná**. Do vyhledávacího pole zadejte `xml validation` jako filtr. V seznamu akce vyberte **ověřování XML**.

   ![Najde a vybere akci ověřování XML.](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Chcete-li určit obsah XML, který chcete ověřit, klikněte do pole **obsahu** , aby se zobrazil seznam dynamického obsahu.

   ![Otevřít seznam dynamického obsahu](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   Seznam dynamického obsahu zobrazuje tokeny vlastností, které reprezentují výstupy z předchozích kroků v pracovním postupu. Pokud se v seznamu nezobrazuje očekávaná vlastnost, zkontrolujte, jestli je v záhlaví triggeru nebo akce vybraná možnost **Zobrazit další**.

1. V seznamu dynamický obsah vyberte vlastnost, která má obsah, který chcete ověřit.

   V tomto příkladu se vybere výstup **těla** z triggeru.

   ![Vyberte obsah, který se má ověřit.](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Chcete-li určit schéma, které chcete použít pro ověřování, otevřete seznam **název schématu** a vyberte schéma ověření, které jste přidali do propojeného účtu integrace.

   ![Vyberte schéma, které se má použít k ověření.](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Uložte aplikaci logiky.

   Nyní jste dokončili nastavení ověřování. V reálné aplikaci můžete ukládat ověřená data do obchodní aplikace (LOB), jako je třeba SalesForce. Pokud chcete odeslat ověřený výstup do Salesforce, přidejte akci.

1. K otestování akce ověřování můžete odeslat žádost o aktivaci pracovního postupu vaší aplikace logiky.

## <a name="next-steps"></a>Další kroky

* Další informace o [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
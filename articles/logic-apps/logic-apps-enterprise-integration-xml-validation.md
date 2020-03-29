---
title: Ověření xml pro podnikovou integraci B2B
description: Ověření XML pomocí schémat v aplikacích Azure Logic Apps s podnikovou integrací Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: ff21b059e712489c1914b2d12c6aa6a3d78d66d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792160"
---
# <a name="validate-xml-for-b2b-enterprise-integration-in-azure-logic-apps-with-enterprise-integration-pack"></a>Ověření integrace XML pro podniková integrace B2B v aplikacích Azure Logic Apps pomocí sady Enterprise Integration Pack

Často ve scénářích B2B, obchodní partneři v dohodě je třeba zajistit, že zprávy, které vyměňují, jsou platné před zahájením zpracování dat. Dokumenty můžete ověřit proti předdefinovanému schématu pomocí akce ověření XML, která je k dispozici v balíčku Enterprise Integration Pack.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* Prázdná nebo existující aplikace logiky, ve které chcete použít ověřovací akci XML. Pokud s aplikacemi logiky teprve začínáte, přečtěte [si, co je Azure Logic Apps](../logic-apps/logic-apps-overview.md) a úvodní [příručka: Vytvořte si první aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* [Účet integrace,](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) který je přidružený k vašemu předplatnému Azure, je propojený s aplikací logiky, kde plánujete použít ověřovací akci XML, a obsahuje schéma, které chcete použít pro ověřování obsahu XML. Vaše aplikace logiky i účet integrace musí existovat ve stejném umístění nebo v oblasti Azure.

## <a name="add-xml-validation-action"></a>Přidat ověřovací akci XML

1. Na [webu Azure Portal](https://portal.azure.com)otevřete aplikaci logiky v Návrháři aplikací logiky.

1. Pokud máte prázdnou aplikaci logiky, v návrháři aplikace `HTTP request` logiky, ve vyhledávacím poli, zadejte jako filtr a vyberte **Při přijetí požadavku HTTP** aktivační událost. V opačném případě pokračujte k dalšímu kroku.

1. V posledním kroku pracovního postupu vyberte **Nový krok**.

   Chcete-li přidat akci mezi existující kroky, přesuňte ukazatel myši na šipku, která tyto kroky spojuje, aby se znaménko plus (**+**) zobrazilo. Vyberte znaménko plus a pak vyberte **Přidat akci**.

1. V části **Zvolit akci**vyberte **Předdefinované**. Do vyhledávacího pole `xml validation` zadejte jako filtr. V seznamu akcí vyberte **možnost Ověření XML**.

   ![Najít a vybrat akci Ověření XML](./media/logic-apps-enterprise-integration-xml-validation/select-xml-validation-action.png)

1. Chcete-li určit obsah XML, který chcete ověřit, klepněte do pole **Obsah,** aby se zobrazil seznam dynamického obsahu.

   ![Otevřít seznam dynamického obsahu](./media/logic-apps-enterprise-integration-xml-validation/open-dynamic-content-list.png)

   Seznam dynamického obsahu zobrazuje tokeny vlastností, které představují výstupy z předchozích kroků v pracovním postupu. Pokud seznam nezobrazuje očekávanou vlastnost, zkontrolujte záhlaví aktivační události nebo akce, zda můžete vybrat **Zobrazit více**.

1. Ze seznamu dynamického obsahu vyberte vlastnost, která obsahuje obsah, který chcete ověřit.

   Tento příklad vybere **výstup body** z aktivační události.

   ![Výběr obsahu, který chcete ověřit](./media/logic-apps-enterprise-integration-xml-validation/select-content-to-validate.png)

1. Chcete-li určit schéma, které chcete použít pro ověření, otevřete seznam **Název schématu** a vyberte schéma ověření, které jste přidali do propojeného účtu integrace.

   ![Vyberte schéma, které se má použít pro ověření.](./media/logic-apps-enterprise-integration-xml-validation/select-validation-schema.png)

1. Uložte svou aplikaci logiky.

   Nyní jste hotovi s nastavením ověření. V aplikaci reálného světa můžete chtít uložit ověřená data v obchodní (LOB) aplikaci, jako je SalesForce. Chcete-li odeslat ověřený výstup do salesforce, přidejte akci.

1. Chcete-li otestovat akci ověření, můžete odeslat požadavek na aktivaci pracovního postupu aplikace logiky.

## <a name="next-steps"></a>Další kroky

* Další informace o [balíčku Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
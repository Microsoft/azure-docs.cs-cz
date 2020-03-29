---
title: Segmenty UNH 2.5 ve zprávách EDIFACT
description: Řešení zpráv EDIFACT se segmenty UNH2.5 v aplikacích Azure Logic Apps s podnikovou integrací
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792534"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Zpracování dokumentů EDIFACT se segmenty UNH2.5 v aplikacích Azure Logic Apps

Pokud v dokumentu EDIFACT existuje segment UNH2.5, použije se pro vyhledávání schématu. Například v této ukázkové zprávě EDIFACT `EAN008`je pole UNH:

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Chcete-li tuto zprávu zpracovat, postupujte takto:

1. Aktualizujte schéma.

1. Zkontrolujte nastavení smlouvy.

## <a name="update-the-schema"></a>Aktualizace schématu

Chcete-li zprávu zpracovat, musíte nasadit schéma, které má název kořenového uzlu UNH2.5. Kořenový název schématu pro ukázkové pole UNH `EFACT_D03B_ORDERS_EAN008`je například . Pro `D03B_ORDERS` každý, který má jiný segment UNH2.5, je třeba nasadit individuální schéma.

## <a name="add-schema-to-edifact-agreement"></a>Přidání schématu do smlouvy EDIFACT

### <a name="edifact-decode"></a>Dekódovat EDIFACT

Chcete-li dekódovat příchozí zprávu, nastavte schéma v nastavení příjmu smlouvy EDIFACT:

1. Na [webu Azure Portal](https://portal.azure.com)otevřete účet integrace.

1. Přidejte schéma do účtu integrace.

1. Nakonfigurujte schéma v nastavení příjmu smlouvy EDIFACT.

1. Vyberte smlouvu EDIFACT a vyberte **Upravit jako JSON**. Do `schemaReferences` oddílu Receive Agreement přidejte hodnotu UNH2.5:

   ![Přidat UNH2.5 pro získání dohody](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Edifact kódování

Chcete-li zakódovat příchozí zprávu, nakonfigurujte schéma ve smlouvě EDIFACT odeslat nastavení

1. Na [webu Azure Portal](https://portal.azure.com)otevřete účet integrace.

1. Přidejte schéma do účtu integrace.

1. Nakonfigurujte schéma v nastavení odesílání smlouvy EDIFACT.

1. Vyberte smlouvu EDIFACT a klepněte na tlačítko **Upravit jako JSON**.  Přidání hodnoty UNH2.5 do schématu Odeslat **smlouvu**

1. Vyberte smlouvu EDIFACT a vyberte **Upravit jako JSON**. Do `schemaReferences` části Smlouva o odeslání přidejte hodnotu UNH2.5:

   ![Přidat UNH2.5 pro odeslání smlouvy](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Další kroky

* Další informace o [smlouvách o integraci účtů](../logic-apps/logic-apps-enterprise-integration-agreements.md)
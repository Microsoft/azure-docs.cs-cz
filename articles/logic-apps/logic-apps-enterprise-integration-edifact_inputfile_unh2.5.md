---
title: Segmenty UNH 2,5 v EDIFACT zprávách
description: Řešení zpráv EDIFACT s segmenty UNH 2.5 v Azure Logic Apps s využitím Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: ad50cbb423f8c60f1caad159bc1a20cf96ed98aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "74792534"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Zpracování dokumentů EDIFACT s segmenty UNH 2.5 v Azure Logic Apps

Pokud v dokumentu EDIFACT existuje segment UNH 2.5, segment se používá pro vyhledávání schématu. Například v této ukázkové zprávě EDIFACT je pole UNH `EAN008` :

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

Chcete-li zpracovat tuto zprávu, postupujte podle následujících kroků popsaných níže:

1. Aktualizujte schéma.

1. Ověřte nastavení smlouvy.

## <a name="update-the-schema"></a>Aktualizace schématu

Chcete-li zpracovat zprávu, je nutné nasadit schéma, které má název kořenového uzlu UNH 2.5. Například kořenový název schématu pro pole Sample UNH je `EFACT_D03B_ORDERS_EAN008` . Pro každý `D03B_ORDERS` , který má jiný segment UNH 2.5, je nutné nasadit samostatné schéma.

## <a name="add-schema-to-edifact-agreement"></a>Přidání schématu do smlouvy EDIFACT

### <a name="edifact-decode"></a>Dekódování EDIFACT

Chcete-li dekódovat příchozí zprávu, nastavte schéma v nastavení pro příjem EDIFACT smlouvy:

1. V [Azure Portal](https://portal.azure.com)otevřete účet pro integraci.

1. Přidejte schéma do účtu pro integraci.

1. Nakonfigurujte schéma v nastaveních pro příjem EDIFACT smlouvy.

1. Vyberte smlouvu EDIFACT a vyberte **Upravit jako JSON**. Do části smlouvy pro příjem přidejte hodnotu UNH 2.5 `schemaReferences` :

   ![Přidat UNH 2.5 pro přijetí smlouvy](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Kódování EDIFACT

Pokud chcete zakódovat příchozí zprávu, nakonfigurujte schéma ve smlouvě EDIFACT nastavení odeslání.

1. V [Azure Portal](https://portal.azure.com)otevřete účet pro integraci.

1. Přidejte schéma do účtu pro integraci.

1. Nakonfigurujte schéma v nastaveních odeslání EDIFACT smlouvy.

1. Vyberte smlouvu EDIFACT a klikněte na **Upravit jako JSON**.  Přidat hodnotu UNH 2.5 do **schemaReferences** pro odeslání smlouvy

1. Vyberte smlouvu EDIFACT a vyberte **Upravit jako JSON**. Do části odeslání smlouvy přidejte hodnotu UNH 2.5 `schemaReferences` :

   ![Přidat UNH 2.5 k odeslání smlouvy](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Další kroky

* Další informace o [smlouvách k integračnímu účtu](../logic-apps/logic-apps-enterprise-integration-agreements.md)
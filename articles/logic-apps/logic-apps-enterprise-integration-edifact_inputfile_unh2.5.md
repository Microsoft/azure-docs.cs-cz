---
title: Zpracování zprávy EDIFACT s segmentů UNH 2.5 – Azure Logic Apps | Dokumentace Microsoftu
description: Vyřešit dokumentů EDIFACT se UNH2.5 segmenty v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.date: 04/27/2017
ms.openlocfilehash: 9c8b8611347840dcf49759dac51fb506815cd782
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43122004"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Zpracování dokumentů EDIFACT se UNH2.5 segmenty v Azure Logic Apps

Pokud je k dispozici v dokumentu EDIFACT UNH2.5, používá se pro schéma vyhledávání. 

Příklad: Pole UNH je **EAN008** do zprávy EDIFACT  
UNH + SSDD1 + OBJEDNÁVKY: D: 03B: ZRUŠENÍ:**EAN008**.  

Postup pro zpracování zprávy 
1. Aktualizace schématu
2. Zkontrolujte nastavení smlouvy  

## <a name="update-the-schema"></a>Aktualizace schématu
Zpracovat zprávu, budete muset nasadit schéma s názvem UNH2.5 kořenového uzlu.  Pro danou příklad, bude název kořenového adresáře schématu **EFACT_D03B_ORDERS_EAN008**  

Pro každý D03B_ORDERS se segmentem unh2.5 jiné je třeba nasadit jednotlivé schéma.  

## <a name="add-schema-to-the-edifact-agreement"></a>Přidat schéma smlouvy EDIFACT
### <a name="edifact-decode"></a>Dekódování EDIFACT
Dekódování příchozí zprávy, nakonfigurujte schématu v EDIFACT smlouvy obdrží nastavení
1. Přidat schéma účtu integrace    
2. Schéma konfigurace v EDIFACT smlouvy obdrží nastavení. 
3. Vyberte smlouvy EDIFACT a klikněte na tlačítko **upravit jako JSON**.  Přidejte hodnotu UNH2.5 ve smlouvě přijímat **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>Kódování EDIFACT
Ke kódování příchozí zprávy, nakonfigurujte schématu v nastavení odesílání smlouvy EDIFACT
1. Přidat schéma účtu integrace    
2. Schéma konfigurace v nastavení odesílání smlouvy EDIFACT. 
3. Vyberte smlouvy EDIFACT a klikněte na tlačítko **upravit jako JSON**.  Přidejte hodnotu UNH2.5 v dohodě o odeslání **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Další kroky
* [Další informace o smlouvy účtu integrace](../logic-apps/logic-apps-enterprise-integration-agreements.md "přečtěte si víc o smlouvách enterprise integration")  
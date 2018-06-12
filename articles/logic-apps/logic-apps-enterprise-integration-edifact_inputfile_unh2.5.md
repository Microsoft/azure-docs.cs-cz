---
title: B2B aplikace logiky edifact dekódovat vyřešit UNH2.5 - Azure Logic Apps | Microsoft Docs
description: Azure B2B aplikace logiky edifact dekódovat vyřešit UNH2.5
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: jeconnoc
editor: ''
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.openlocfilehash: 3e04e7515a8ddfe512ba2efbbd45c3d1b571c1fd
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297943"
---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>Postupy: zpracování EDIFACT dokumenty s UNH2.5 segmentu
Pokud je k dispozici v dokumentu EDIFACT UNH2.5, ho se používá pro vyhledávání schématu. 

Příklad: Pole UNH je **EAN008** ve zprávě EDIFACT  
UNH + SSDD1 + OBJEDNÁVKY: D: 03B: ZRUŠENÍ:**EAN008**.  

Postup pro zpracování zprávy 
1. Aktualizovat schéma.
2. Zkontrolujte nastavení smlouvy  

## <a name="update-the-schema"></a>Aktualizovat schéma.
Zpracovat zprávu, budete muset nasadit schématu s UNH2.5 název kořenového uzlu.  Pro danou příklad, název kořenového schématu by **EFACT_D03B_ORDERS_EAN008**  

Pro každý D03B_ORDERS různých UNH2.5 segmentu je třeba nasadit jednotlivých schématu.  

## <a name="add-schema-to-the-edifact-agreement"></a>Přidání schématu do smlouvy EDIFACT
### <a name="edifact-decode"></a>Dekódovat EDIFACT
Dekódování příchozí zprávy, se konfigurace schématu v EDIFACT smlouvy obdrží nastavení
1. Přidat schéma k účtu integrace    
2. Konfigurace schématu v EDIFACT smlouvy obdrží nastavení. 
3. Vyberte smlouvy EDIFACT a klikněte na **upravit jako JSON**.  Přidejte hodnotu UNH2.5 Smlouvy přijímat **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT kódování
Ke kódování příchozí zpráva, nakonfigurujte v nastavení odesílání smlouvy EDIFACT schéma
1. Přidat schéma k účtu integrace    
2. V nastavení odesílání smlouvy EDIFACT nakonfigurujte schématu. 
3. Vyberte smlouvy EDIFACT a klikněte na **upravit jako JSON**.  Přidejte hodnotu UNH2.5 Smlouvy odeslat **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Další kroky
* [Další informace o integraci účet smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md "Další informace o integraci smlouvy enterprise")  
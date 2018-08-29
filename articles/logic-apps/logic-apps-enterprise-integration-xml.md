---
title: Zprávy XML pro podnikovou integraci B2B – Azure Logic Apps | Dokumentace Microsoftu
description: Zpracování, ověřování, transformaci a obohacovat zprávy XML pro řešení B2B v Azure Logic Apps sadou Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.date: 02/27/2017
ms.openlocfilehash: 5f804bf656b423d6dbe8f5c2ed73ba54d8361000
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2018
ms.locfileid: "43124531"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Zprávy XML a plochých souborů v Azure Logic Apps sadou Enterprise Integration Pack

Pomocí logic apps, máte možnost zpracování zpráv XML, které můžete odesílat a přijímat. Tato funkce je součástí sady Enterprise Integration Pack. Pro tyto uživatele s pozadí BizTalk Server Enterprise Integration Pack poskytuje podobné možnosti transformace a ověření zprávy, pracovat s plochých souborů a dokonce i pomocí jazyka XPath rozšiřovat ani je nemohl extrahovat specifické vlastnosti ze zprávy. 

Pro uživatele, kteří se do tohoto prostoru tyto funkce Rozbalit, jak zpracovávat zprávy v rámci pracovního postupu. Například pokud máte ve scénáři business-to-business a pracovat s konkrétní schémat XML, Enterprise Integration Pack můžete použít ke zvýšení jak vaše společnost zpracovává tyto zprávy. 

Enterprise Integration Pack zahrnuje: 

* [Ověření XML](logic-apps-enterprise-integration-xml-validation.md "přečtěte si víc o ověření XML zprávy") – ověření příchozích nebo odchozích XML zprávy pro konkrétní schéma.
* [Transformace XML](../logic-apps/logic-apps-enterprise-integration-transform.md "Další informace o mapování a transformace XML zprávy") – převést nebo úprava zprávy XML na základě vašich požadavků nebo objemu partnera.
* [Plochého souboru kódování a dekódování plochého souboru](logic-apps-enterprise-integration-flatfile.md "přečtěte si víc o dekódování plochého souborukódování/") – kódování nebo dekódování plochého souboru. Například SAP přijímá a odesílá IDOC soubory ve formátu plochých souborů. Řada platforem integrace vytvářet zprávy XML, včetně Logic Apps. Ano můžete vytvořit aplikaci logiky, která používá kodér plochého souboru "převést" soubory XML do plochých souborů. 
* [Výraz XPath](https://msdn.microsoft.com/library/mt643789.aspx) – obohacení zprávu a extrahovat určité vlastnosti ze zprávy. Extrahované vlastnosti můžete poté zprávy směruje do cíle nebo zprostředkující koncový bod.

## <a name="try-it-out"></a>Vyzkoušet
[Nasazení aplikace logiky plně funkční ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (ukázka na Githubu) pomocí funkce XML v Azure Logic Apps.

## <a name="learn-more"></a>Další informace
[Další informace o Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "přečtěte si víc o Enterprise Integration Pack")

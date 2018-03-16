---
title: "Práce s zpráv XML ve svých pracovních postupech - Azure Logic Apps | Microsoft Docs"
description: "Zpracovat, ověřit, transformace a zlepšit komunikaci oddělení zprávy XML aplikace logiky a business-pro scénáře s využitím Enterprise integračního balíčku"
services: logic-apps
documentationcenter: .net,nodejs,java
author: divyaswarnkar
manager: anneta
editor: 
ms.assetid: 47672dc4-1caa-44e5-b8cb-68ec3a76b7dc
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 02/27/2017
ms.author: LADocs; divswa
ms.openlocfilehash: 495cb44219ee9d8ca20ea17c373545668839f4c5
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="validate-and-transform-xml-encode-and-decode-flat-files-and-enrich-messages-features-in-logic-apps"></a>Ověření a transformace XML, kódování a dekódování plochých souborů a rozšířit funkce zprávy v aplikacích logiky

Použití aplikace logiky, máte možnost zpracování zpráv XML, které odesílat a přijímat. Tato funkce je součástí integračního balíčku Enterprise. Pro tyto uživatele s pozadí BizTalk Server Enterprise integračního balíčku poskytuje podobné dalo transformace a ověření zprávy, práce s plochých souborů a i pomocí jazyka XPath zlepšit komunikaci oddělení nebo extrahuje specifické vlastnosti ze zprávy. 

Pro uživatele, kteří jsou nové pro tento prostor rozbalte tyto funkce, jak při zpracování zprávy v rámci pracovního postupu. Například pokud se v případě business-to-business a pracovat s konkrétní schémat XML, integrační balíček Enterprise můžete použít ke zvýšení jak vaše společnost zpracovává tyto zprávy. 

Integrační balíček Enterprise zahrnuje: 

* [Ověření XML](logic-apps-enterprise-integration-xml-validation.md "Další informace o ověření XML zprávy") -ověření příchozích nebo odchozích XML zprávy pro konkrétní schéma.
* [Transformace XML](../logic-apps/logic-apps-enterprise-integration-transform.md "Další informace o mapování a transformace zprávy XML") – převést nebo úprava zprávy XML na základě vaše požadavky nebo požadavky na partnera.
* [Plochý soubor kódování a dekódování plochý soubor](logic-apps-enterprise-integration-flatfile.md "Další informace o plochý soubor kódování a dekódování") – zakódování nebo dekódování plochý soubor. Například SAP přijímá a odesílá IDOC soubory ve formátu plochý soubor. Řada platforem integrace vytvořit XML zprávy, včetně Logic Apps. Ano můžete vytvořit aplikaci logiky, která používá kodér plochý soubor "převést" soubory XML do plochých souborů. 
* [Výraz XPath](https://msdn.microsoft.com/library/mt643789.aspx) – zlepšit komunikaci oddělení zprávu a extrahovat specifické vlastnosti zprávy. Potom můžete extrahované vlastnosti pro odesílání zpráv do cílového umístění, nebo zprostředkující koncový bod.

## <a name="try-it-out"></a>Vyzkoušet
[Nasazení aplikace logiky plně funkční ](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (ukázka Githubu) pomocí funkcí XML v Azure Logic Apps.

## <a name="learn-more"></a>Další informace
[Další informace o integračního balíčku Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Další informace o Enterprise integračního balíčku")

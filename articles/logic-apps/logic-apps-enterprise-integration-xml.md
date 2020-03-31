---
title: Zprávy XML a ploché soubory
description: Zpracování, ověřování a transformace zpráv XML v aplikacích Azure Logic Apps pomocí sady Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792148"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Zprávy XML a ploché soubory v logicových aplikacích Azure s podnikovou integrací

V [Azure Logic Apps](logic-apps-overview.md)můžete zpracovávat zprávy XML, které odesíláte a přijímáte pomocí sady Enterprise Integration Pack. Pokud jste použili BizTalk Server, enterprise integration pack poskytuje podobné funkce pro transformaci a ověření zpráv, práci s plochými soubory a dokonce použít XPath k obohacení nebo extrahování konkrétních vlastností ze zprávy. Pokud s tímto prostorem tečujete, tyto funkce rozšiřují způsob zpracování zpráv v pracovním postupu aplikace logiky. Například pokud máte business-to-business (B2B) scénář a pracovat s konkrétní schémata XML, můžete použít Enterprise Integration Pack vylepšit, jak vaše společnost zpracovává tyto zprávy.

Například sada Enterprise Integration Pack obsahuje tyto funkce:

* [Ověření XML](logic-apps-enterprise-integration-xml-validation.md): Ověření příchozí nebo odchozí zprávy XML proti určitému schématu.

* [Transformace XML](logic-apps-enterprise-integration-transform.md): Převeďte nebo přizpůsobte zprávu XML na základě vašich požadavků nebo požadavků partnera pomocí map.

* [Kódování plochých souborů a dekódování plochého souboru](logic-apps-enterprise-integration-flatfile.md): Zakódujte nebo dekódujte plochý soubor.

  Sap například přijímá a odesílá soubory IDOC ve formátu plochého souboru. Mnoho integračních platforem vytváří zprávy XML, včetně logic apps. Takže můžete vytvořit aplikaci logiky, která používá konkodéru plochého souboru k "převodu" souborů XML na ploché soubory.

* [XPath](workflow-definition-language-functions-reference.md#xpath): Obohatit zprávu a extrahovat specifické vlastnosti ze zprávy. Extrahované vlastnosti pak můžete použít ke směrování zprávy do cíle nebo zprostředkující koncový bod.

## <a name="sample"></a>Ukázka

[Nasazení plně funkční aplikace logiky](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (ukázka GitHubu) pomocí funkcí XML v Azure Logic Apps.

## <a name="next-steps"></a>Další kroky

Další informace o [balíčku Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

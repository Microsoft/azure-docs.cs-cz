---
title: Zprávy XML a ploché soubory
description: Zpracování, ověřování a transformace zpráv XML v Azure Logic Apps pomocí Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 02/27/2017
ms.openlocfilehash: 3cb3472572abbd891f8d36ea036b371c1224f38c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74792148"
---
# <a name="xml-messages-and-flat-files-in-azure-logic-apps-with-enterprise-integration-pack"></a>Zprávy XML a ploché soubory v Azure Logic Apps s Enterprise Integration Pack

V [Azure Logic Apps](logic-apps-overview.md)můžete zpracovávat zprávy XML, které odesíláte a přijímáte pomocí Enterprise Integration Pack. Pokud jste použili BizTalk Server, Enterprise Integration Pack poskytuje podobné možnosti pro transformaci a ověření zpráv, práci s plochými soubory a dokonce i k rozšíření nebo extrakci specifických vlastností ze zprávy pomocí XPath. Pokud s tímto místem začínáte, tyto funkce rozšiřují způsob zpracování zpráv v pracovním postupu vaší aplikace logiky. Například pokud máte scénář B2B (Business-to-Business) a pracujete s konkrétními schématy XML, můžete použít Enterprise Integration Pack k vylepšení způsobu, jakým vaše společnost zpracovává tyto zprávy.

Enterprise Integration Pack například zahrnuje tyto možnosti:

* [Ověření XML](logic-apps-enterprise-integration-xml-validation.md): ověří příchozí nebo odchozí zprávu XML proti konkrétnímu schématu.

* [Transformace XML](logic-apps-enterprise-integration-transform.md): převod nebo přizpůsobení zprávy XML na základě vašich požadavků nebo požadavků partnera pomocí map.

* [Kódování plochého souboru a dekódování plochého](logic-apps-enterprise-integration-flatfile.md)souboru: kódování nebo dekódování plochého souboru.

  Například SAP akceptuje a odesílá soubory IDOC ve formátu plochého souboru. Řada integračních platforem vytváří zprávy XML, včetně Logic Apps. Proto můžete vytvořit aplikaci logiky, která používá k převodu souborů XML do plochých souborů kodér plochý soubor.

* [XPath](workflow-definition-language-functions-reference.md#xpath): obohacení zprávy a extrakce specifických vlastností ze zprávy. Pak můžete pomocí extrahovaných vlastností směrovat zprávu do cílového nebo zprostředkujícího koncového bodu.

## <a name="sample"></a>Ukázka

[Nasazení plně operační aplikace logiky](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-veter-pipeline) (ukázka GitHubu) pomocí funkcí XML v Azure Logic Apps.

## <a name="next-steps"></a>Další kroky

Další informace o [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

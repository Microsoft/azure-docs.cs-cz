---
title: Integrace Enterprise pro B2B - Azure Logic Apps | Microsoft Docs
description: Vytváření pracovních postupů B2B a podpora podnikové scénáře integrace aplikace logiky s Enterprise integračního balíčku
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: jeconnoc
editor: cgronlun
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/08/2016
ms.author: LADocs; padmavc
ms.openlocfilehash: 25349d48d88b64568fef0f5a6bac5950da15196b
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298633"
---
# <a name="overview-b2b-scenarios-and-communication-with-the-enterprise-integration-pack"></a>Přehled: Scénáře B2B a komunikaci s Enterprise integračního balíčku

Pro pracovní postupy business-to-business (B2B) a bezproblémové komunikaci s Azure Logic Apps můžete povolit podnikové scénáře integrace s společnosti Microsoft cloudové řešení, Enterprise integračního balíčku. Organizace můžou vyměňovat zprávy elektronicky, i když používají různé protokoly a formáty. Sada transformuje různých formátech do formátu, který může organizace systémy interpretovat a zpracovat. Organizace si můžou vyměňovat zprávy přes standardní protokoly, včetně [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), a [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Také můžete zabezpečit zprávy pomocí šifrování a digitální podpisy.

Pokud jste obeznámeni s BizTalk Server nebo Microsoft Azure BizTalk Services, funkcí pro integraci Enterprise lze snadno použít, protože jsou podobné jako většina koncepty. Hlavní rozdíl je, že integrace Enterprise používá účty pro integraci zjednodušit úložiště a správu artefaktů použitých při komunikaci s B2B. 

Z pohledu architektury Enterprise integračního balíčku je založena na "účty integrace". Tyto účty jsou založené na cloudu kontejnery, které ukládají všechny artefaktů, jako je schémat, partnerů, certifikátů, mapy a smlouvy. Těchto artefaktů můžete použít k návrhu, nasazení a údržbě aplikace B2B a také k vytváření pracovních postupů B2B pro logic apps. Ale předtím, než budete moci použít tyto artefakty, je nutné nejprve propojit účtu integraci do aplikace logiky. Poté můžete svou aplikaci logiky přístup k účtu integrace artefakty.

## <a name="why-should-you-use-enterprise-integration"></a>Proč používat integraci podnikových?

* Díky integraci enterprise můžete uložit všechny artefakty na jednom místě, váš účet integrace.
* Můžete vytvářet pracovní postupy B2B a integrovat aplikacím třetích stran software jako služba (SaaS), místní aplikace a vlastních aplikací s použitím modulu Azure Logic Apps a všechny jeho konektory.
* Můžete vytvořit vlastní kód pro logic apps, díky Azure functions.

## <a name="how-to-get-started-with-enterprise-integration"></a>Jak začít pracovat s integrací enterprise?

Můžete vytvářet a spravovat aplikace B2B s Enterprise integračního balíčku pomocí návrháře logiku aplikace v **portál Azure**. Můžete také spravovat aplikace logiky s [prostředí PowerShell](https://msdn.microsoft.com/library/azure/mt652195.aspx "Logic apps prostředí PowerShell témata").

Zde jsou základní kroky, které je nutné provést před vytvořením aplikací na portálu Azure:

![Přehled image](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Jaké jsou některé běžné scénáře?

Integrace Enterprise podporuje tyto oborových standardů:

* EDI - Electronic Data Interchange
* EAI - integraci podnikových aplikací

## <a name="heres-what-you-need-to-get-started"></a>Tady je co potřebujete, abyste mohli začít

* Předplatné služby Azure pomocí účtu, integrace
* Visual Studio 2015 k vytvoření mapy a schémata
* [Microsoft Azure Logic Apps Enterprise integrace nástrojů pro Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>Vyzkoušet

[Nasazení plně funkční ukázka AS2 odesílání a příjem aplikace logiky](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) používající funkce B2B pro Azure Logic Apps.

## <a name="learn-more"></a>Další informace
* [Smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md "Další informace o integraci smlouvy enterprise")
* [Obchodní na obchodní scénáře (B2B)](../logic-apps/logic-apps-enterprise-integration-b2b.md "informace o vytváření aplikací logiky s funkcemi B2B ")  
* [Certifikáty](logic-apps-enterprise-integration-certificates.md "Další informace o integraci certifikáty pro rozlehlé sítě")
* [Plochý soubor kódování a dekódování](logic-apps-enterprise-integration-flatfile.md "zjistěte, jak ke kódování a dekódování obsah plochý soubor")  
* [Účty pro integraci](../logic-apps/logic-apps-enterprise-integration-accounts.md "Další informace o účty pro integraci")
* [Mapuje](../logic-apps/logic-apps-enterprise-integration-maps.md "Další informace o enterprise integrace mapy")
* [Partneři](logic-apps-enterprise-integration-partners.md "Další informace o partnery integrace enterprise")
* [Schémata](logic-apps-enterprise-integration-schemas.md "Další informace o integraci schémata enterprise")
* [Ověření XML zprávy](logic-apps-enterprise-integration-xml.md "postup ověření XML zprávy s Logic apps")
* [Transformace XML](logic-apps-enterprise-integration-transform.md "Další informace o enterprise integrace mapy")
* [Konektory pro integraci](../connectors/apis-list.md "Další informace o konektory pro integraci pack")
* [Integrace účet Metadata](../logic-apps/logic-apps-enterprise-integration-metadata.md "Další informace o integraci účet metadat")
* [Sledování zpráv B2B](logic-apps-monitor-b2b-message.md "Další informace o sledování zpráv B2B")
* [Sledování zpráv B2B na portálu OMS](logic-apps-track-b2b-messages-omsportal.md "Další informace o sledování zpráv B2B na portálu OMS")


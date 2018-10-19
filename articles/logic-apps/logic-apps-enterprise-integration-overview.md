---
title: Přehled integrace B2B enterprise – Azure Logic Apps | Dokumentace Microsoftu
description: Vytvářet automatizované pracovní postupy B2B pro podniková řešení integrace s Azure Logic Apps a Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.date: 09/08/2016
ms.openlocfilehash: d37d5cb2b89b82bd9741dee0946b3a77d456b22a
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49405748"
---
# <a name="overview-b2b-enterprise-integration-scenarios-in-azure-logic-apps-with-enterprise-integration-pack"></a>Přehled: B2B podnikové integrace scénáře v Azure Logic Apps sadou Enterprise Integration Pack

Pro pracovní postupy business-to-business (B2B) a bezproblémové komunikaci s Azure Logic Apps můžete aktivovat scénáře integrace enterprise od Microsoftu založené na cloudu řešení Enterprise Integration Pack. Organizace si mohou vyměňovat zprávy elektronicky, i když používají různé protokoly a formátů. Této sady transformuje do formátu, který organizace systémy dokážou interpretovat a zpracovat různé formáty. Organizace si mohou vyměňovat zprávy prostřednictvím standardních protokolů, jako jsou třeba [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md), a [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Můžete také zabezpečení zpráv pomocí šifrování a digitálním podpisům.

Pokud jste se seznámili s BizTalk serverem nebo Microsoft Azure BizTalk Services, funkce pro podnikovou integraci se snadno používají, protože většina koncepty jsou podobné. Hlavní rozdíl je, že pro podnikovou integraci používá účty pro integraci pro zjednodušení ukládání a správu artefaktů použít v rámci komunikace B2B. 

Enterprise Integration Pack architektonicky, je založená na "integračních účtů". Tyto účty jsou založené na cloudu kontejnery, které ukládají všechny artefakty, jako jsou schémata, partnerů, certifikátů, mapy a smlouvy. Tyto artefakty slouží k návrhu, nasazení a údržbě aplikace B2B a také umožňují vytvářet pracovní postupy B2B pro logic apps. Ale předtím, než budete moct použít tyto artefakty, musíte nejprve odkaz účtu pro integraci do aplikace logiky. Potom můžete svou aplikaci logiky získat přístup k artefaktům účtu integrace.

## <a name="why-should-you-use-enterprise-integration"></a>Proč byste měli použít pro podnikovou integraci?

* S podnikovou integrací můžete uložit všechny artefakty na jednom místě – účtu pro integraci.
* Můžete vytvářet pracovní postupy B2B a integraci s aplikací třetích stran software jako služba (SaaS), místních aplikací a vlastních aplikací pomocí modulu Azure Logic Apps a všechny jeho konektory.
* Můžete vytvořit vlastní kód pro logic apps s využitím Azure functions.

## <a name="how-to-get-started-with-enterprise-integration"></a>Jak začít s podnikovou integrací?

Můžete vytvářet a spravovat aplikace B2B sadou Enterprise Integration Pack prostřednictvím návrhář aplikace logiky v **webu Azure portal**. Můžete také spravovat aplikace logiky pomocí [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.logicapp "Logic apps Powershellu").

Tady jsou hlavní kroky, které je třeba provést před vytvořením aplikace na webu Azure Portal:

![Obrázek s přehledem](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>Jaké jsou uvedeny některé obvyklé scénáře?

Podniková integrace podporuje tyto oborové standardy:

* EDI - výměnu elektronických dat.
* Funkce – Enterprise Application Integration, EAI

## <a name="heres-what-you-need-to-get-started"></a>Zde je, co potřebujete, abyste mohli začít

* Předplatné Azure s účtu pro integraci
* Visual Studio 2015 a vytváření map a schémata
* [Nástroje Microsoft Azure Logic Apps Enterprise Integration pro Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>Vyzkoušet

[Nasazení plně funkční ukázkové AS2 odeslání a příjem aplikace logiky](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) , který využívá funkce B2B pro Azure Logic Apps.

## <a name="learn-more"></a>Další informace
* [Smlouvy o](../logic-apps/logic-apps-enterprise-integration-agreements.md "přečtěte si víc o smlouvách enterprise integration")
* [Scénáře B2b (B2B)](../logic-apps/logic-apps-enterprise-integration-b2b.md "zjistěte, jak vytvořit s B2B funkce Logic apps ")  
* [Certifikáty](logic-apps-enterprise-integration-certificates.md "přečtěte si víc o podnikové integraci certifikáty")
* [Plochého souboru kódování/dekódování](logic-apps-enterprise-integration-flatfile.md "zjistěte, jak kódování a dekódování plochého souboru obsahu")  
* [Účty pro integraci](../logic-apps/logic-apps-enterprise-integration-accounts.md "přečtěte si víc o účty pro integraci")
* [Mapuje](../logic-apps/logic-apps-enterprise-integration-maps.md "přečtěte si víc o podnikové integrace map")
* [Partneři](logic-apps-enterprise-integration-partners.md "přečtěte si víc o podnikové integrace partnerů")
* [Schémata](logic-apps-enterprise-integration-schemas.md "přečtěte si víc o podnikové integraci schémata")
* [Ověření XML zprávy](logic-apps-enterprise-integration-xml.md "zjistěte, jak ověřit XML zprávy službou Logic apps")
* [Transformace XML](logic-apps-enterprise-integration-transform.md "přečtěte si víc o podnikové integrace map")
* [Konektory Enterprise Integration](../connectors/apis-list.md "přečtěte si víc o konektory enterprise integration pack")
* [Integrace účtu metadat](../logic-apps/logic-apps-enterprise-integration-metadata.md "přečtěte si víc o metadata účtu integrace")
* [Monitorování zpráv B2B](logic-apps-monitor-b2b-message.md "Další informace o monitorování zpráv B2B")
* [Sledování zpráv B2B v Azure Log Analytics](logic-apps-track-b2b-messages-omsportal.md "Další informace o sledování zpráv B2B v Azure Log Analytics")


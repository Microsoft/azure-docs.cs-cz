---
title: Podniková integrace B2B
description: Další informace o vytváření automatizovaných pracovních postupů B2B pro podnikovou integraci pomocí Azure Logic Apps a Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: 9356cecb22672ae15505d14b5ac73d5baeef09e0
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77191373"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Řešení pro podnikovou integraci B2B s Azure Logic Apps a Enterprise Integration Pack

Pro řešení mezi podniky (B2B) a bezproblémovou komunikaci mezi organizacemi můžete vytvářet automatizované škálovatelné pracovní postupy podnikové integrace pomocí sady Enterprise Integration Pack (EIP) s [aplikacemi Azure Logic Apps](../logic-apps/logic-apps-overview.md). Přestože organizace používají různé protokoly a formáty, mohou si vyměňovat zprávy elektronicky. EIP transformuje různé formáty do formátu, který mohou systémy vaší organizace zpracovávat a podporuje standardní protokoly, včetně [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md)a [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Zabezpečení zpráv můžete také zlepšit pomocí šifrování i digitálních podpisů. EIP podporuje tyto [konektory pro podnikovou integraci](../connectors/apis-list.md#integration-account-connectors) a tyto průmyslové standardy:

* Elektronická výměna dat (EDI)
* Integrace podnikových aplikací (EAI)

Pokud jste obeznámeni s Microsoft BizTalk Server nebo Azure BizTalk Services, EIP se řídí podobnými koncepty, takže funkce se snadno používají. Nicméně, jeden hlavní rozdíl je, že EIP je architektonicky založen na "integraci účtů" zjednodušit ukládání a správu artefaktů používaných v B2B komunikace. Tyto účty jsou cloudové kontejnery, které ukládají všechny vaše artefakty, jako jsou partneři, smlouvy, schémata, mapy a certifikáty. 

## <a name="why-use-the-enterprise-integration-pack"></a>Proč používat sadu Enterprise Integration Pack?

* S EIP můžete ukládat všechny své artefakty na jednom místě - váš integrační účet.

* Pomocí aplikací Azure Logic Apps a konektorů můžete vytvářet pracovní postupy B2B a integrovat je s aplikacemi jako službou třetích stran (SaaS), místními aplikacemi a vlastními aplikacemi.

* Pomocí funkcí Azure můžete vytvořit vlastní kód pro aplikace logiky.

## <a name="how-do-i-get-started"></a>Jak mám začít?

Než začnete vytvářet pracovní postupy aplikace logiky B2B pomocí EIP, potřebujete tyto položky:

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet integrace](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) s artefakty, které chcete použít

* K vytváření map a schémat můžete použít [nástroje pro integraci Microsoft Azure Logic Apps Enterprise Integration Tools pro Visual Studio 2015 2.0](https://aka.ms/vsmapsandschemas) a Visual Studio 2015.

Po vytvoření účtu integrace a přidání artefaktů můžete začít vytvářet pracovní postupy B2B s těmito artefakty vytvořením aplikace logiky na webu Azure Portal. Pokud s aplikacemi logiky tečujete, zkuste [vytvořit základní aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Chcete-li pracovat s těmito artefakty, ujistěte se, že nejprve propojit účet integrace s aplikací logiky. Poté může vaše aplikace logiky přistupovat k vašemu účtu integrace. Můžete také vytvářet, spravovat a nasazovat aplikace logiky pomocí Visual Studia nebo [PowerShellu](https://docs.microsoft.com/powershell/module/az.logicapp).

Tady jsou kroky na vysoké úrovni, jak začít vytvářet aplikace logiky B2B:

![Požadavky pro vytváření aplikací logiky B2B](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Vyzkoušet

[Nasazení plně funkční ukázkové aplikace logiky, která odesílá a přijímá zprávy AS2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Další kroky

* [Vytvořit obchodní partnery](logic-apps-enterprise-integration-partners.md)
* [Vytvořit smlouvy](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Přidání schémat](logic-apps-enterprise-integration-schemas.md)
* [Přidávání map](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migrace ze služby BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md)

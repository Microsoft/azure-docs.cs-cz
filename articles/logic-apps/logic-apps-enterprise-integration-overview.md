---
title: Podniková integrace B2B
description: Přečtěte si, jak vytvářet automatizované pracovní postupy B2B pro podnikovou integraci pomocí Azure Logic Apps a Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, logicappspm
ms.topic: overview
ms.date: 03/24/2021
ms.openlocfilehash: 09d253aae4c054db5efdc252f62986044e1d366b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771852"
---
# <a name="b2b-enterprise-integration-solutions-with-azure-logic-apps-and-enterprise-integration-pack"></a>Řešení B2B Enterprise Integration s Azure Logic Apps a Enterprise Integration Pack

Pro řešení B2B (Business-to-Business) a bezproblémovou komunikaci mezi organizacemi můžete vytvářet automatizované škálovatelné pracovní postupy pro podnikovou integraci pomocí Enterprise Integration Pack (EIP) s [Azure Logic Apps](../logic-apps/logic-apps-overview.md). I když organizace používají různé protokoly a formáty, můžou vyměňovat zprávy elektronicky. EIP transformuje různé formáty do formátu, který mohou systémy vaší organizace zpracovávat, a podporuje standardní protokoly, včetně [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md)a [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). Můžete také zlepšit zabezpečení zpráv pomocí šifrování i digitálních podpisů. EIP podporuje tyto [podnikové integrační konektory](../connectors/managed.md#enterprise-connectors) a tyto oborové standardy:

* Elektronický výměna dat (EDI)
* Enterprise Application Integration (EAI)

Pokud jste obeznámeni se službou Microsoft BizTalk Server nebo Azure BizTalk Services, EIP sleduje podobné koncepty a usnadňuje tak použití funkcí. Jedním z podstatných rozdílů je, že EIP je architektonicky na základě "účtů pro integraci", které zjednodušují ukládání a správu artefaktů používaných v komunikacích B2B. Tyto účty jsou cloudové kontejnery, které ukládají všechny artefakty, jako jsou partneři, smlouvy, schémata, mapy a certifikáty. 

## <a name="why-use-the-enterprise-integration-pack"></a>Proč použít Enterprise Integration Pack?

* S EIP můžete ukládat všechny artefakty na jednom místě – váš účet pro integraci.

* Můžete vytvářet pracovní postupy B2B a integrovat je s aplikacemi SaaS (software jako služba) třetích stran, místními aplikacemi a vlastními aplikacemi pomocí Azure Logic Apps a konektorů.

* Pomocí služby Azure Functions můžete vytvořit vlastní kód pro vaše aplikace logiky.

## <a name="how-do-i-get-started"></a>Jak mám začít?

Než budete moct začít sestavovat pracovní postupy B2B Logic Apps s EIP, budete potřebovat tyto položky:

* Předplatné Azure. Pokud nemáte předplatné Azure, [zaregistrujte si bezplatný účet Azure](https://azure.microsoft.com/free/).

* [Účet pro integraci](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) s artefakty, které chcete použít

* Chcete-li vytvořit mapy a schémata, můžete použít nástroj [Microsoft Azure Logic Apps podniková integrace Tools for Visual studio 2015 2,0](https://aka.ms/vsmapsandschemas) a visual Studio 2015.

Po vytvoření účtu pro integraci a přidání artefaktů můžete začít sestavovat pracovní postupy B2B s těmito artefakty vytvořením aplikace logiky v Azure Portal. Pokud s Logic Apps začínáte, zkuste [vytvořit základní aplikaci logiky](../logic-apps/quickstart-create-first-logic-app-workflow.md). Pokud chcete s těmito artefakty pracovat, ujistěte se, že jste nejdřív provedli propojení účtu pro integraci s vaší aplikací logiky. Potom vaše aplikace logiky bude mít přístup k vašemu účtu pro integraci. Můžete také vytvářet, spravovat a nasazovat aplikace logiky pomocí sady Visual Studio nebo [prostředí PowerShell](/powershell/module/az.logicapp).

Tady jsou kroky vysoké úrovně, které vám pomůžou začít sestavovat aplikace B2B Logic Apps:

![Předpoklady pro vytváření aplikací B2B Logic Apps](./media/logic-apps-enterprise-integration-overview/overview.png)  

## <a name="try-now"></a>Vyzkoušet

[Nasazení plně funkční ukázkové aplikace logiky, která odesílá a přijímá zprávy AS2](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive)

## <a name="next-steps"></a>Další kroky

* [Vytváření obchodních partnerů](logic-apps-enterprise-integration-partners.md)
* [Vytvoření smluv](../logic-apps/logic-apps-enterprise-integration-agreements.md)
* [Přidání schémat](logic-apps-enterprise-integration-schemas.md)
* [Přidávání map](../logic-apps/logic-apps-enterprise-integration-maps.md)
* [Migrace ze služby BizTalk Services](../logic-apps/logic-apps-move-from-mabs.md)

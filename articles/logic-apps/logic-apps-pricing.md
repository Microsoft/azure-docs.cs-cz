---
title: Ceny a fakturace – Azure Logic Apps | Dokumentace Microsoftu
description: Zjistěte, jak funguje ceny a fakturace pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: 5f9147035c07bbe4fb3f38b74025015e70dd87b3
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2018
ms.locfileid: "47159551"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Cenový model pro Azure Logic Apps

Můžete vytvářet a spouštět pracovní postupy automatizované škálovatelné integrace v cloudu s Azure Logic Apps. Tady jsou uvedené podrobnosti o fakturaci a cenách fungování pro Logic Apps. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Cenový model spotřeby

Pro nové aplikace logiky, které vytvoříte pomocí služby Logic Apps veřejné nebo "globální" platíte jenom za využité. Tyto aplikace logiky použít plán založený na spotřebě a cenový model, což znamená, že měří všechny akce prováděné aplikace logiky se. Každý krok v definici aplikace logiky je akce, triggery, kroky toku řízení, volání k integrované akcím a volání pro konektory. Další informace najdete v tématu [ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Oprava cenový model

> [!NOTE]
> Prostředí integrační služby je v *ve verzi private preview*. Chcete-li požádat o přístup, [vytváření žádosti o připojení tady](https://aka.ms/iseprivatepreview).

Pro nové aplikace logiky, které vytvoříte pomocí [ *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), což je privátní samostatný Logic Apps instanci, která používá vyhrazené zdroje, platíte pevnou měsíční cenu pro integrované akce a konektory úrovně standard ISE s názvem bez přípony. Vaše ISE zahrnuje jeden konektor Enterprise ve výši, zatímco se účtuje další podnikové konektory podle cena spotřeby Enterprise. Další informace najdete v tématu [ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Aktivační události

Aktivační události představují zvláštní akce, které vytvoří instanci aplikace logiky, když dojde k určité události. Aktivační události fungují různými způsoby, které ovlivňují, jak se měří aplikace logiky.

* **Cyklického dotazování aktivační událost** – Tato aktivační událost neustále kontroluje koncový bod pro zprávy, které splňují kritéria pro instanci aplikace logiky vytváření a spouštění pracovního postupu. Každý požadavek dotazování se počítá jako spuštění a se měří, i když je vytvořena žádná instance aplikace logiky. Zadejte interval dotazování, nastavte aktivační událost pomocí návrháře aplikace logiky.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Trigger Webhooku** – této aktivační události čeká na klientovi umožní odeslat žádost o konkrétní koncový bod. Jednotlivé požadavky odeslané na koncový bod webhooku se počítá jako spuštění akce. Například aktivační událost požadavek a HTTP Webhook jsou obě triggerů webhooků.

* **Trigger opakování** – tento trigger vytvoří instanci aplikace logiky na základě intervalu opakování, které jste nastavili v aktivační události. Můžete například nastavit trigger opakování, která se spouští za tři dny nebo podle plánu složitější.

Spuštění aktivační události najdete v podokně s přehledem vaší aplikace logiky v části historie aktivačních událostí.

## <a name="actions"></a>Akce

Integrované akce, jako je například akce, které volání HTTP, Azure Functions nebo API Management a také řídit tok kroků se měří jako nativní akce, které mají jejich příslušného typu. Akce, které volají [konektory](https://docs.microsoft.com/connectors) typu "ApiConnection". Tyto konektory jsou klasifikovány jako standard nebo enterprise konektory, které se měří na jejich základě [ceny][pricing]. Podnikové konektory v *ve verzi Preview* se účtují jako konektory úrovně standard.

Všechny akce úspěšně a neúspěšně spuštění se počítá a měří jako spuštění akcí. Akce, které jsou vynechány, z důvodu nesplnění podmínky nebo akce, které nelze spustit, protože aplikace logiky byla ukončena před dokončením, ale nepočítají jako spuštění akcí. Zakázané logic apps nejde vytvořit instanci nové instance, takže se nebudou fakturované jsou zakázané.

> [!NOTE]
> Když zakážete, aplikace logiky, všechny aktuálně spuštěné instance může trvat nějakou dobu, než úplně zastavit.

Akce, které běží uvnitř smyčky se počítají za každý cyklus ve smyčce. Například jedna akce v smyčka "for each", který zpracovává seznam 10 položek se počítá vynásobením počtu položek seznamu (10) počet akcí ve smyčce (1) plus jeden pro spouštění smyčky. Ano, v tomto příkladu je výpočet (10 * 1) + 1, což vede k 11 provedení akcí.

## <a name="integration-account-usage"></a>Využití účtu integrace

Zahrnuje založenou na skutečné spotřebě využití [účtu pro integraci](logic-apps-enterprise-integration-create-integration-account.md) kde můžete prozkoumat, vývoj a testování [B2B/EDI](logic-apps-enterprise-integration-b2b.md) a [zpracování XML](logic-apps-enterprise-integration-xml.md) funkcí ve službě Logic Apps bez Další poplatky. Máte jeden účet integrace na oblasti a úložiště až po konkrétní [počet artefaktů](../logic-apps/logic-apps-limits-and-config.md), například obchodní partneři EDI a smlouvy, mapy, schémata, sestavení, certifikátů a konfigurací dávek.

Logic Apps nabízí také účty pro integraci basic a standard s podporovaná smlouvou SLA aplikace logiky. Účtů integrace úrovně basic můžete použít, když můžete použít pouze zpracování zprávy, nebo fungují jako partner malé firmy, která má vztah obchodního partnera s větší obchodní entitě. Účty integrace úrovně standard podporuje složitější vztahy B2B a zvýšit počet entit, které můžete spravovat. Další informace najdete v tématu [ceny za Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Další postup

* [Další informace o Logic Apps][whatis]
* [Vytvoření první aplikace logiky][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md


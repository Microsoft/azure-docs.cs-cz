---
title: Ceny a fakturace – Azure Logic Apps | Dokumentace Microsoftu
description: Zjistěte, jak funguje ceny a fakturace pro Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: logic-apps
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
manager: carmonm
ms.assetid: f8f528f5-51c5-4006-b571-54ef74532f32
ms.topic: article
ms.date: 10/16/2018
ms.openlocfilehash: 04fb86f9b8f8be2c013f9bd7449dd5a4b2bcf90c
ms.sourcegitcommit: 7804131dbe9599f7f7afa59cacc2babd19e1e4b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2018
ms.locfileid: "51854114"
---
# <a name="pricing-model-for-azure-logic-apps"></a>Cenový model pro Azure Logic Apps

Můžete vytvářet a spouštět automatizované integraci pracovních postupů, které je možné škálovat v cloudu, když použijete Azure Logic Apps. Tady jsou uvedené podrobnosti o fakturaci a cenách fungování pro Logic Apps. 

<a name="consumption-pricing"></a>

## <a name="consumption-pricing-model"></a>Cenový model spotřeby

Pro nové aplikace logiky, které běží ve službě Logic Apps veřejné nebo "globální" platíte jenom za využité. Tyto aplikace logiky použít plán založený na spotřebě a cenový model. V definici aplikace logiky je každý krok akce. Akce zahrnují aktivační událost, všechny kroky toku řízení, integrované akce a konektor volání. Logic Apps měření všechny akce, které běží ve vaší aplikaci logiky.  
Další informace najdete v tématu [ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="fixed-pricing"></a>

## <a name="fixed-pricing-model"></a>Oprava cenový model

Pro nové aplikace logiky, na kterých běží uvnitř [ *prostředí integrační služby* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), platíte za integrované akce a konektory úrovně standard s popiskem ISE pevnou měsíční cenu. ISE poskytuje způsob, jak můžete vytvořit a spouštět aplikace izolované logiky, které mají přístup k prostředkům ve virtuální síti Azure.  

Vaše ISE zahrnuje jeden konektor free Enterprise, který zahrnuje tolika připojení. Využití dalších podnikových konektorů se účtují podle cena spotřeby Enterprise. 

> [!NOTE]
> Prostředí integrační služby je v *ve verzi private preview*. Chcete-li požádat o přístup, [vytváření žádosti o připojení tady](https://aka.ms/iseprivatepreview). Další informace najdete v tématu [ceny Logic Apps](https://azure.microsoft.com/pricing/details/logic-apps).

<a name="triggers"></a>

## <a name="triggers"></a>Aktivační události

Aktivační události představují zvláštní akce, které vytvoří instanci aplikace logiky, když dojde k určité události. Aktivační události fungují různými způsoby, které ovlivňují, jak se měří aplikace logiky.

* **Cyklického dotazování aktivační událost** – Tato aktivační událost neustále kontroluje koncový bod pro zprávy, které splňují kritéria pro instanci aplikace logiky vytváření a spouštění pracovního postupu. I když žádné instanci aplikace logiky se vytvoří, Logic Apps měření každého požadavku dotazování služby jako spuštění. Zadejte interval dotazování, nastavte aktivační událost pomocí návrháře aplikace logiky.

  [!INCLUDE [logic-apps-polling-trigger-non-standard-metering](../../includes/logic-apps-polling-trigger-non-standard-metering.md)]

* **Trigger Webhooku** – této aktivační události čeká na klientovi umožní odeslat žádost o konkrétní koncový bod. Jednotlivé požadavky odeslané na koncový bod webhooku se počítá jako spuštění akce. Například aktivační událost požadavek a HTTP Webhook jsou obě triggerů webhooků.

* **Trigger opakování** – tento trigger vytvoří instanci aplikace logiky na základě intervalu opakování, které jste nastavili v aktivační události. Můžete například nastavit trigger opakování, která se spouští za tři dny nebo podle plánu složitější.

## <a name="actions"></a>Akce

Logic Apps měření integrované akce jako nativní akce. Například integrované akce zahrnují volání přes protokol HTTP, volání z Azure Functions nebo API Management a řízení toku kroky, jako je například smyčky a podmínky 
- každý má své vlastní typ akce. Akce, které volají [konektory](https://docs.microsoft.com/connectors) typu "ApiConnection". Tyto konektory jsou klasifikovány jako standard nebo enterprise konektory, které se měří na jejich základě [ceny][pricing]. Podnikové konektory v *ve verzi Preview* se účtují jako konektory úrovně standard.

Logic Apps měřiče všechno úspěšně a neúspěšně spouštění akcí jako spuštění akcí. Logic Apps se nebude monitorovat tyto akce: 

* Akce, které získáte přeskočeno z důvodu nesplnění podmínky
* Akce, které nechcete spustit, protože aplikace logiky zastavil před dokončením instalace

Zakázané logic apps se neúčtují při zakázán vzhledem k tomu, že nelze vytvořit nové instance.

> [!NOTE]
> Když zakážete, aplikace logiky, všechny aktuálně spuštěné instance může trvat nějakou dobu, než úplně zastavit.

Pro akce, které běží uvnitř smyčky Logic Apps se počítá každé akce / cyklus ve smyčce. Předpokládejme například, že máte smyčka "for each", který zpracovává seznam. Logic Apps měřiče akce v této smyčce vynásobením počtu seznam položek se počet akcí ve smyčce a přidá akci, která spustí smyčky. Výpočet seznam 10 položek je (10 * 1) + 1, což vede k 11 provedení akcí.

## <a name="integration-account-usage"></a>Využití účtu integrace

Založený na spotřebě, využití se vztahuje na [účty pro integraci](logic-apps-enterprise-integration-create-integration-account.md) kde můžete prozkoumat, vývoj a testování [B2B/EDI](logic-apps-enterprise-integration-b2b.md) a [zpracování XML](logic-apps-enterprise-integration-xml.md) funkcí ve službě Logic Apps bez Další poplatky. Může mít jeden účet pro integraci v jedné oblasti. Každý účet pro integraci můžete ukládat konkrétní [počet artefaktů](../logic-apps/logic-apps-limits-and-config.md), mezi které patří obchodní partnery, smlouvy, map, schémata, sestavení, certifikáty, konfigurace dávek a tak dále.

Logic Apps nabízí také účty pro integraci basic a standard s podporovaná smlouvou SLA aplikace logiky. Účtů integrace úrovně basic můžete použít při pouze chcete zpracování zpráv nebo fungují jako partner malé firmy, která má vztah obchodního partnera s větší obchodní entitě. Účty integrace úrovně standard podporuje složitější vztahy B2B a zvýšit počet entit, které můžete spravovat. Další informace najdete v tématu [ceny za Azure](https://azure.microsoft.com/pricing/details/logic-apps).

## <a name="next-steps"></a>Další postup

* [Další informace o Logic Apps][whatis]
* [Vytvoření první aplikace logiky][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: logic-apps-overview.md
[create]: quickstart-create-first-logic-app-workflow.md


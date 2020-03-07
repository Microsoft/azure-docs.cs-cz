---
title: Azure Event Grid koncepty
description: Popisuje Azure Event Grid a jeho koncepty. Definuje několik klíčových součástí Event Grid.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 0821c749a6cb718e1b8abb74a2925bc041850eaf
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359446"
---
# <a name="concepts-in-azure-event-grid"></a>Koncepty v Azure Event Grid

Tento článek popisuje hlavní koncepty v Azure Event Grid.

## <a name="events"></a>Události

Událost je nejmenší množství informací, které plně popisuje něco, co se stalo v systému. Každá událost má běžné informace, jako je zdroj události, čas, kdy došlo k události, a jedinečný identifikátor. Každá událost má také konkrétní informace, které jsou relevantní pouze pro konkrétní typ události. Například událost týkající se nového souboru vytvořeného v Azure Storage obsahuje podrobnosti o souboru, jako je například hodnota `lastTimeModified`. Nebo událost Event Hubs má adresu URL sběrného souboru. 

K události velikosti až 64 KB se vztahuje Obecná dostupnost (GA) smlouva SLA (SLA). Podpora pro událost velikosti až 1 MB je v současnosti ve verzi Preview. Události větší než 64 KB se účtují v přírůstcích po 64 až KB. 


Vlastnosti, které se odesílají v události, najdete v tématu [Azure Event Grid schéma událostí](event-schema.md).

## <a name="publishers"></a>Vydavatelé

Vydavatel je uživatel nebo organizace, které se rozhodly Odeslat události do Event Grid. Microsoft zveřejňuje události pro několik služeb Azure. Události můžete publikovat z vlastní aplikace. Organizace, které hostují služby mimo Azure, můžou publikovat události prostřednictvím Event Grid.

## <a name="event-sources"></a>Zdroje událostí

Zdroj události je místo, kde dojde k události. Každý zdroj události souvisí s jedním nebo více typy událostí. Například Azure Storage je zdrojem událostí pro události objektů blob, které se vytvořily. IoT Hub je zdroj události pro události vytvořené zařízením. Vaše aplikace je zdrojem událostí pro vlastní události, které definujete. Zdroje událostí jsou zodpovědné za odesílání událostí do Event Grid.

Informace o implementaci některého z podporovaných zdrojů Event Grid najdete v tématu [zdroje událostí v Azure Event Grid](event-sources.md).

## <a name="topics"></a>Témata

Téma Event Grid poskytuje koncový bod, ve kterém zdroj odesílá události. Vydavatel vytvoří téma Event Grid a rozhodne, zda zdroj události potřebuje jedno téma nebo více než jedno téma. Téma se používá pro kolekci souvisejících událostí. Pro reakci na určité typy událostí předplatitelé rozhodují, která témata se přihlásí k odběru.

Systémová témata jsou integrovaná témata poskytovaná službami Azure. V rámci vašeho předplatného Azure se nezobrazí systémová témata, protože Vydavatel je vlastníkem témat, ale můžete se k němu přihlásit. Chcete-li se přihlásit k odběru, zadejte informace o prostředku, ze kterého chcete přijímat události. Pokud máte přístup k prostředku, můžete se přihlásit k odběru jeho událostí.

Vlastní témata jsou témata aplikací a třetích stran. Když vytvoříte nebo máte přiřazený přístup k vlastnímu tématu, uvidíte, že ve vašem předplatném je vlastní téma.

Při navrhování aplikace máte flexibilitu při rozhodování, kolik témat se má vytvořit. Pro velká řešení vytvořte vlastní téma pro každou kategorii souvisejících událostí. Zvažte například aplikaci, která odesílá události související s úpravou uživatelských účtů a objednávek zpracování. Je nepravděpodobné, že žádná obslužná rutina události chce obě kategorie událostí. Vytvořte dvě vlastní témata a umožněte, aby obslužné rutiny událostí přihlásily k odběru ty, které je zajímají. Pro malá řešení můžete chtít odeslat všechny události do jednoho tématu. Předplatitelé události mohou filtrovat typy událostí, které chtějí.

## <a name="event-subscriptions"></a>Odběry událostí

Předplatné oznamuje Event Grid, které události v tématu vás zajímá. Při vytváření odběru zadáte koncový bod pro zpracování události. Můžete filtrovat události, které se odesílají do koncového bodu. Můžete filtrovat podle typu události nebo podle vzoru předmětu. Další informace najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).

Příklady vytváření předplatných najdete v tématech:

* [Ukázky v Azure CLI pro Event Grid](cli-samples.md)
* [Ukázky Azure PowerShell pro Event Grid](powershell-samples.md)
* [Šablony Azure Resource Manager pro Event Grid](template-samples.md)

Informace o získání aktuálních odběrů služby Event Grid najdete v tématu věnovaném [dotazům Event Grid odběry](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Vypršení platnosti odběru události
Po tomto datu automaticky vyprší platnost odběru událostí. Nastavte vypršení platnosti pro odběry událostí, které jsou potřeba jenom po omezenou dobu, a nechcete se starat o vyčištění těchto předplatných. Například při vytváření odběru události pro otestování scénáře můžete chtít nastavit vypršení platnosti. 

Příklad nastavení vypršení platnosti najdete v tématu [přihlášení k odběru s rozšířenými filtry](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Obslužné rutiny událostí

Z Event Grid perspektivy je obslužná rutina události místem, kde je událost odeslána. Obslužná rutina provede několik dalších akcí zpracování události. Event Grid podporuje několik typů obslužných rutin. Jako obslužnou rutinu můžete použít podporovanou službu Azure nebo vlastní Webhook. V závislosti na typu obslužné rutiny Event Grid podle různých mechanismů Zabezpečte doručení události. Pro obslužné rutiny událostí Webhooku protokolu HTTP se událost opakuje, dokud obslužná rutina nevrátí stavový kód `200 – OK`. V případě Azure Storage fronty se události zopakují, dokud Služba front úspěšně nezpracuje nabízenou zprávu do fronty.

Informace o implementaci kterékoli z podporovaných obslužných rutin Event Grid naleznete [v tématu obslužné rutiny událostí v Azure Event Grid](event-handlers.md).

## <a name="security"></a>Zabezpečení

Event Grid poskytuje zabezpečení pro přihlášení k odběru témat a témata týkající se publikování. Při přihlášení k odběru musíte mít odpovídající oprávnění v tématu prostředek nebo Event Grid. Při publikování musíte mít v tématu token SAS nebo ověřování klíčů. Další informace najdete v tématu [Event Grid zabezpečení a ověřování](security-authentication.md).

## <a name="event-delivery"></a>Doručení událostí

Pokud Event Grid nedokáže potvrdit, že koncový bod předplatitele událost přijal, znovu doručí událost. Další informace najdete v tématu [doručování zpráv Event Grid a opakování](delivery-and-retry.md).

## <a name="batching"></a>Dávkování

Při použití vlastního tématu musí být události vždy publikovány v poli. Může se jednat o dávku jednoho pro scénáře s nízkou propustností, ale pro případy vysokého využití se doporučuje vytvořit dávku několika událostí v rámci publikování, abyste dosáhli vyšší efektivity. Dávky mohou být až 1 MB. Každá událost by neměla být větší než 64 KB (Obecná dostupnost) nebo 1 MB (Preview).

> [!NOTE]
> K události velikosti až 64 KB se vztahuje Obecná dostupnost (GA) smlouva SLA (SLA). Podpora pro událost velikosti až 1 MB je v současnosti ve verzi Preview. Události větší než 64 KB se účtují v přírůstcích po 64 KB. 

## <a name="next-steps"></a>Další kroky

* Úvod do Event Gridu najdete v článku o [Event Gridu](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).

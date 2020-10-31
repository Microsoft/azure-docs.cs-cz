---
title: Azure Event Grid koncepty
description: Popisuje Azure Event Grid a jeho koncepty. Definuje několik klíčových součástí Event Grid.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 6cfb8b3aaf16a0080b9864ce5198b8a7232e8bc8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075105"
---
# <a name="concepts-in-azure-event-grid"></a>Koncepty v Azure Event Grid

Tento článek popisuje hlavní koncepty v Azure Event Grid.

## <a name="events"></a>Události

Událost je nejmenší množství informací, které plně popisuje něco, co se stalo v systému. Každá událost má běžné informace, jako je zdroj události, čas, kdy došlo k události, a jedinečný identifikátor. Každá událost má také konkrétní informace, které jsou relevantní pouze pro konkrétní typ události. Například událost související s vytvářením nového souboru v Azure Storage obsahuje podrobnosti o daném souboru, jako je hodnota `lastTimeModified`. Událost služby Event Hubs zase obsahuje adresu URL souboru Capture. 

K události velikosti až 64 KB se vztahuje Obecná dostupnost (GA) smlouva SLA (SLA). Podpora pro událost velikosti až 1 MB je v současnosti ve verzi Preview. Události větší než 64 KB se účtují v přírůstcích po 64 až KB. 


Vlastnosti, které se odesílají v události, najdete v tématu [Azure Event Grid schéma událostí](event-schema.md).

## <a name="publishers"></a>Vydavatelé

Vydavatel je uživatel nebo organizace, které se rozhodly Odeslat události do Event Grid. Microsoft zveřejňuje události pro několik služeb Azure. Můžete publikovat události z vaší vlastní aplikace. Organizace, které hostují služby mimo Azure, můžou publikovat události přes Event Grid.

## <a name="event-sources"></a>Zdroje událostí

Zdroj události je místo, kde dojde k události. Každý zdroj události souvisí s jedním nebo více typy událostí. Například Azure Storage je zdroj události pro události vytvořené objektem blob. IoT Hub je zdroj události pro události vytvořené zařízením. Vaše aplikace je zdroj události pro vlastní události, které definujete. Za odesílání událostí do Event Gridu zodpovídají zdroje událostí.

Informace o implementaci některého z podporovaných zdrojů Event Grid najdete v tématu [zdroje událostí v Azure Event Grid](overview.md#event-sources).

## <a name="topics"></a>Témata

Téma Event Grid poskytuje koncový bod, ve kterém zdroj odesílá události. Vydavatel vytvoří téma Event Grid a rozhodne, zda zdroj události potřebuje jedno téma nebo více než jedno téma. Téma se používá pro kolekci souvisejících událostí. Pro reakci na určité typy událostí předplatitelé rozhodují, která témata se přihlásí k odběru.

**Systémová témata** jsou integrovaná témata poskytované službami Azure, jako jsou Azure Storage, Azure Event Hubs a Azure Service Bus. V předplatném Azure můžete vytvořit systémová témata a přihlásit se k jejich odběru. Další informace najdete v tématu [Přehled systémových témat](system-topics.md). 

**Vlastní témata** jsou témata aplikací a třetích stran. Po vytvoření vlastního tématu nebo po přiřazení vašeho přístupu k vlastnímu tématu se dané vlastní téma zobrazí ve vašem předplatném. Další informace najdete v tématu [vlastní témata](custom-topics.md). Při navrhování aplikace máte flexibilitu při rozhodování, kolik témat se má vytvořit. Pro velká řešení vytvořte vlastní téma pro každou kategorii souvisejících událostí. Představme si například aplikaci, která odesílá události související s úpravami uživatelských účtů a zpracováním objednávek. Není pravděpodobné, že nějaká obslužná rutina události chce přijímat obě kategorie událostí. Vytvořte dvě vlastní témata a nechte obslužné rutiny událostí odebírat to téma, které je zajímá. Pro malá řešení můžete chtít odeslat všechny události do jednoho tématu. Předplatitelé události mohou filtrovat typy událostí, které chtějí.

K dispozici je jiný typ tématu: **Partnerská část** . Funkce [partnerských událostí](partner-events-overview.md) umožňuje poskytovateli SaaS třetí strany publikovat události ze svých služeb, aby byly dostupné pro uživatele, kteří se můžou přihlásit k odběru těchto událostí. Poskytovatel SaaS zpřístupňuje typ tématu, **Partnerská část** , kterou předplatitelé používají ke spotřebě událostí. Nabízí také čistý model Pub-sub, který odděluje obavy a vlastnictví prostředků, které používají vydavatelé a předplatitelé událostí.

## <a name="event-subscriptions"></a>Odběry událostí

Předplatné oznamuje Event Grid, které události v tématu vás zajímá. Při vytváření odběru zadáte koncový bod pro zpracování události. Můžete filtrovat události, které se odesílají do koncového bodu. Můžete filtrovat podle typu události nebo podle vzoru předmětu. Další informace najdete v tématu [schéma předplatného Event Grid](subscription-creation-schema.md).

Příklady vytváření předplatných najdete v tématech:

* [Ukázky v Azure CLI pro Event Grid](cli-samples.md)
* [Ukázky Azure PowerShellu pro Event Grid](powershell-samples.md)
* [Šablony Azure Resource Manageru pro Event Grid](template-samples.md)

Informace o získání aktuálních odběrů služby Event Grid najdete v tématu věnovaném [dotazům Event Grid odběry](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Vypršení platnosti odběru události
Po tomto datu automaticky vyprší platnost odběru událostí. Nastavte vypršení platnosti pro odběry událostí, které jsou potřeba jenom po omezenou dobu, a nechcete se starat o vyčištění těchto předplatných. Například při vytváření odběru události pro otestování scénáře můžete chtít nastavit vypršení platnosti. 

Příklad nastavení vypršení platnosti najdete v tématu [přihlášení k odběru s rozšířenými filtry](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Obslužné rutiny událostí

Z Event Grid perspektivy je obslužná rutina události místem, kde je událost odeslána. Obslužná rutina provede několik dalších akcí zpracování události. Event Grid podporuje několik typů obslužných rutin. Jako obslužnou rutinu můžete použít podporovanou službu Azure nebo vlastní Webhook. V závislosti na typu obslužné rutiny Event Grid podle různých mechanismů Zabezpečte doručení události. Pro obslužné rutiny událostí Webhooku protokolu HTTP se událost opakuje, dokud obslužná rutina nevrátí stavový kód `200 – OK` . V případě Azure Storage fronty se události zopakují, dokud Služba front úspěšně nezpracuje nabízenou zprávu do fronty.

Informace o implementaci kterékoli z podporovaných obslužných rutin Event Grid naleznete [v tématu obslužné rutiny událostí v Azure Event Grid](event-handlers.md).

## <a name="security"></a>Zabezpečení

Event Grid poskytuje zabezpečení pro přihlášení k odběru témat a témata týkající se publikování. Při přihlášení k odběru musíte mít odpovídající oprávnění v tématu prostředek nebo Event Grid. Při publikování musíte mít v tématu token SAS nebo ověřování klíčů. Další informace najdete v tématu [Event Grid zabezpečení a ověřování](security-authentication.md).

## <a name="event-delivery"></a>Doručování událostí

Pokud Event Grid nedokáže potvrdit, že koncový bod předplatitele událost přijal, znovu doručí událost. Další informace najdete v tématu [doručování zpráv Event Grid a opakování](delivery-and-retry.md).

## <a name="batching"></a>Dávkování

Při použití vlastního tématu musí být události vždy publikovány v poli. Může se jednat o dávku jednoho pro scénáře s nízkou propustností, ale pro případy vysokého využití se doporučuje vytvořit dávku několika událostí v rámci publikování, abyste dosáhli vyšší efektivity. Dávky mohou být až 1 MB. Každá událost by neměla být větší než 64 KB (Obecná dostupnost) nebo 1 MB (Preview).

> [!NOTE]
> K události velikosti až 64 KB se vztahuje Obecná dostupnost (GA) smlouva SLA (SLA). Podpora pro událost velikosti až 1 MB je v současnosti ve verzi Preview. Události větší než 64 KB se účtují v přírůstcích po 64 KB. 

## <a name="next-steps"></a>Další kroky

* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte si téma [Vytvoření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).

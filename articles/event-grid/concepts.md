---
title: Koncepty Azure Event Grid
description: Popisuje Azure Event Grid a jeho koncepty. Definuje několik klíčových součástí event gridu.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/03/2018
ms.author: spelluru
ms.openlocfilehash: 0821c749a6cb718e1b8abb74a2925bc041850eaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265061"
---
# <a name="concepts-in-azure-event-grid"></a>Koncepty v Azure Event Grid

Tento článek popisuje hlavní koncepty ve službě Azure Event Grid.

## <a name="events"></a>Události

Událost je nejmenší množství informací, které plně popisuje něco, co se stalo v systému. Každá událost má společné informace, jako je: zdroj události, čas události se konala, a jedinečný identifikátor. Každá událost má také konkrétní informace, které jsou relevantní pouze pro konkrétní typ události. Například událost související s vytvářením nového souboru v Azure Storage obsahuje podrobnosti o daném souboru, jako je hodnota `lastTimeModified`. Událost služby Event Hubs zase obsahuje adresu URL souboru Capture. 

Na událost o velikosti až 64 kB se vztahuje smlouva o úrovni služeb obecné dostupnosti (GA). Podpora pro událost o velikosti až 1 MB je aktuálně ve verzi Preview. Události nad 64 KB se účtují v krocích po 64 KB. 


Vlastnosti, které jsou odesílány v události, naleznete v [tématu Schéma událostí Sítě událostí Azure](event-schema.md).

## <a name="publishers"></a>Vydavatelé

Vydavatel je uživatel nebo organizace, která se rozhodne odeslat události do služby Event Grid. Microsoft publikuje události pro několik služeb Azure. Můžete publikovat události z vaší vlastní aplikace. Organizace, které hostují služby mimo Azure, můžou publikovat události přes Event Grid.

## <a name="event-sources"></a>Zdroje událostí

Zdroj události je místo, kde dojde k události. Každý zdroj události souvisí s jedním nebo více typy událostí. Například Azure Storage je zdroj události pro události vytvořené objektem blob. IoT Hub je zdroj události pro události vytvořené zařízením. Vaše aplikace je zdroj události pro vlastní události, které definujete. Za odesílání událostí do Event Gridu zodpovídají zdroje událostí.

Informace o implementaci některého z podporovaných zdrojů Event Grid najdete [v tématu Zdroje událostí v Azure Event Grid](event-sources.md).

## <a name="topics"></a>Témata

Téma mřížky událostí poskytuje koncový bod, kam zdroj odesílá události. Vydavatel vytvoří téma mřížky událostí a rozhodne, zda zdroj události potřebuje jedno téma nebo více než jedno téma. Téma se používá pro kolekci souvisejících událostí. Chcete-li reagovat na určité typy událostí, odběratelé rozhodnout, která témata se přihlásit k odběru.

Systémová témata jsou předdefinovaná témata, která poskytují služby Azure. Ve vašem předplatném Azure nevidíte systémová témata, protože tato témata vlastní vydavatel, ale můžete se přihlásit k jejich odběru. Při přihlášení k odběru zadáte informace o prostředku, ze kterého chcete přijímat události. Pokud máte k danému prostředku přístup, můžete se přihlásit k odběru jeho událostí.

Vlastní témata jsou témata aplikací a témata třetích stran. Po vytvoření vlastního tématu nebo po přiřazení vašeho přístupu k vlastnímu tématu se dané vlastní téma zobrazí ve vašem předplatném.

Při navrhování aplikace máte flexibilitu při rozhodování o tom, kolik témat chcete vytvořit. Pro velká řešení vytvořte vlastní téma pro každou kategorii souvisejících událostí. Představme si například aplikaci, která odesílá události související s úpravami uživatelských účtů a zpracováním objednávek. Není pravděpodobné, že nějaká obslužná rutina události chce přijímat obě kategorie událostí. Vytvořte dvě vlastní témata a nechte obslužné rutiny událostí odebírat to téma, které je zajímá. U malých řešení můžete raději odeslat všechny události na jedno téma. Odběratelé událostí mohou filtrovat typy událostí, které chtějí.

## <a name="event-subscriptions"></a>Odběry událostí

Předplatné informuje event grid, které události na téma, které máte zájem o příjem. Při vytváření předplatného zadáte koncový bod pro zpracování události. Můžete filtrovat události, které jsou odesílány do koncového bodu. Můžete filtrovat podle typu události nebo vzorku předmětu. Další informace naleznete v [tématu](subscription-creation-schema.md)Schéma odběru Event Grid .

Příklady vytváření předplatných najdete v těchto tématech:

* [Ukázky v Azure CLI pro Event Grid](cli-samples.md)
* [Ukázky Azure PowerShellu pro Event Grid](powershell-samples.md)
* [Šablony Azure Resource Manageru pro Event Grid](template-samples.md)

Informace o získání aktuálního předplatná mřížky událostí naleznete v [tématu Query Event Grid subscriptions](query-event-subscriptions.md).

## <a name="event-subscription-expiration"></a>Vypršení platnosti předplatného události
Po tomto datu automaticky vyprší platnost odběru událostí. Nastavte vypršení platnosti pro odběry událostí, které jsou potřeba pouze po omezenou dobu a nechcete se starat o vyčištění těchto předplatných. Například při vytváření odběru událostí k testování scénáře můžete nastavit vypršení platnosti. 

Příklad nastavení vypršení platnosti najdete v tématu [Přihlášení k odběru pomocí rozšířených filtrů](how-to-filter-events.md#subscribe-with-advanced-filters).

## <a name="event-handlers"></a>Obslužné rutiny událostí

Z hlediska mřížky událostí je obslužná rutina události místem, kam je událost odeslána. Obslužná rutina provede některé další akce ke zpracování události. Event Grid podporuje několik typů obslužné rutiny. Jako obslužnou rutinu můžete použít podporovanou službu Azure nebo vlastní webhook. V závislosti na typu obslužné rutiny Event Grid sleduje různé mechanismy zaručit doručení události. U obslužných rutin událostí http webhooku je událost `200 – OK`opakována, dokud obslužná rutina nevrátí stavový kód aplikace . Pro Azure Storage Queue události jsou opakovány, dokud služba Queue úspěšně zpracuje nabízený text zprávy do fronty.

Informace o implementaci některé z podporovaných obslužných rutin event gridu najdete [v tématu obslužné rutiny událostí v Azure Event Grid](event-handlers.md).

## <a name="security"></a>Zabezpečení

Event Grid poskytuje zabezpečení pro přihlášení k odběru témat a publikování témat. Při přihlášení k odběru musíte mít odpovídající oprávnění k tématu mřížky prostředků nebo událostí. Při publikování musíte mít token SAS nebo ověřování klíčů pro toto téma. Další informace naleznete v [tématu Zabezpečení a ověřování mřížky událostí](security-authentication.md).

## <a name="event-delivery"></a>Doručení události

Pokud Event Grid nemůže potvrdit, že událost byla přijata koncovým bodem odběratele, znovu dodá událost. Další informace naleznete v [tématu Message Message Event Grid and retry](delivery-and-retry.md).

## <a name="batching"></a>Dávkování

Při použití vlastního tématu musí být události vždy publikovány v poli. To může být dávka jednoho pro scénáře s nízkou propustností, ale pro případy použití s velkým objemem, doporučujeme dávky několik událostí společně na publikování k dosažení vyšší efektivity. Dávky mohou být až 1 MB. Každá událost by stále neměla být větší než 64 kB (obecná dostupnost) nebo 1 MB (náhled).

> [!NOTE]
> Na událost o velikosti až 64 kB se vztahuje smlouva o úrovni služeb obecné dostupnosti (GA). Podpora pro událost o velikosti až 1 MB je aktuálně ve verzi Preview. Události nad 64 KB se účtují v krocích po 64 KB. 

## <a name="next-steps"></a>Další kroky

* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat Event Grid, přečtěte [si tématu Vytváření a směrování vlastních událostí pomocí Azure Event Grid](custom-event-quickstart.md).

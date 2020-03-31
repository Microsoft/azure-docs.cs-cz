---
title: Koncepty – Azure Event Grid IoT Edge | Dokumenty společnosti Microsoft
description: Koncepty v mřížce událostí na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 73309e10e88c11e639e6ac6fd3bb061e1b5c685b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72992545"
---
# <a name="event-grid-concepts"></a>Koncepty Event Grid

Tento článek popisuje hlavní koncepty ve službě Azure Event Grid.

## <a name="events"></a>Události

Událost je nejmenší množství informací, které plně popisuje něco, co se stalo v systému. Každá událost má společné informace, jako je: zdroj události, čas události se konala, a jedinečný identifikátor. Každá událost má také konkrétní informace, které jsou relevantní pouze pro konkrétní typ události. Podpora pro událost o velikosti až 1 MB je aktuálně ve verzi Preview.

Vlastnosti, které jsou zahrnuty v události, naleznete v [tématu Schéma událostí Sítě událostí Azure](event-schemas.md).

## <a name="publishers"></a>Vydavatelé

Vydavatel je uživatel nebo organizace, která se rozhodne odeslat události do služby Event Grid. Můžete publikovat události z vaší vlastní aplikace.

## <a name="event-sources"></a>Zdroje událostí

Zdroj události je místo, kde dojde k události. Každý zdroj události souvisí s jedním nebo více typy událostí. Například Azure Storage je zdroj události pro události vytvořené objektem blob. Vaše aplikace je zdroj události pro vlastní události, které definujete. Za odesílání událostí do Event Gridu zodpovídají zdroje událostí.

## <a name="topics"></a>Témata

Téma mřížky událostí poskytuje koncový bod, kam zdroj odesílá události. Vydavatel vytvoří téma mřížky událostí a rozhodne, zda zdroj události potřebuje jedno téma nebo více než jedno téma. Téma se používá pro kolekci souvisejících událostí. Chcete-li reagovat na určité typy událostí, odběratelé rozhodnout, která témata se přihlásit k odběru.

Při navrhování aplikace máte možnost rozhodnout o tom, kolik témat chcete vytvořit. Pro velká řešení vytvořte vlastní téma pro každou kategorii souvisejících událostí. Představme si například aplikaci, která odesílá události související s úpravami uživatelských účtů a zpracováním objednávek. Není pravděpodobné, že nějaká obslužná rutina události chce přijímat obě kategorie událostí. Vytvořte dvě vlastní témata a nechte obslužné rutiny událostí odebírat to téma, které je zajímá. U malých řešení můžete raději odeslat všechny události na jedno téma. Odběratelé událostí mohou filtrovat typy událostí, které chtějí.

Informace o správě témat v Event Gridu najdete v dokumentaci k [rozhraní REST API.](api.md)

## <a name="event-subscriptions"></a>Odběry událostí

Předplatné informuje event grid, které události na téma, které máte zájem o příjem. Při vytváření předplatného zadáte koncový bod pro zpracování události. Můžete filtrovat události, které jsou odesílány do koncového bodu. 

Informace [o tom,](api.md) jak spravovat odběry v Event Gridu, najdete v dokumentaci k rozhraní REST API.

## <a name="event-handlers"></a>Obslužné rutiny událostí

Z hlediska mřížky událostí je obslužná rutina události místem, kam je událost odeslána. Obslužná rutina provede další kroky ke zpracování události. Event Grid podporuje několik typů obslužné rutiny. Jako obslužnou rutinu můžete použít podporovanou službu Azure nebo vlastní webový hák. V závislosti na typu obslužné rutiny Event Grid sleduje různé mechanismy zaručit doručení události. Pokud je obslužná rutina cílové události webovým hákem HTTP, bude událost zopakována, když obslužná rutina vrátí stavový kód aplikace `200 – OK`. Pro edge Hub, pokud je událost doručena bez výjimky, je považována za úspěšnou.

## <a name="security"></a>Zabezpečení

Event Grid poskytuje zabezpečení pro přihlášení k odběru témat a publikování témat. Další informace naleznete v [tématu Zabezpečení a ověřování mřížky událostí](security-authentication.md).

## <a name="event-delivery"></a>Doručení události

Pokud Event Grid nemůže potvrdit, že událost byla přijata koncovým bodem odběratele, znovu dodá událost. Další informace naleznete v [tématu Message Message Event Grid and retry](delivery-retry.md).

## <a name="batching"></a>Dávkování

Při použití vlastního tématu musí být události vždy publikovány v poli. Pro scénáře s nízkou propustností bude mít pole pouze jednu hodnotu. Pro případy použití s velkým objemem doporučujeme dávkovat několik událostí společně na publikování, abyste dosáhli vyšší efektivity. Dávky mohou být až 1 MB. Každá událost by stále neměla být větší než 1 MB (náhled).
---
title: Koncepty – Azure Event Grid IoT Edge | Microsoft Docs
description: Koncepty v Event Grid IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: b3babfe93012fae15e79362ba34f3f48856bc833
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86171733"
---
# <a name="event-grid-concepts"></a>Koncepty Event Grid

Tento článek popisuje hlavní koncepty v Azure Event Grid.

## <a name="events"></a>Události

Událost je nejmenší množství informací, které plně popisuje něco, co se stalo v systému. Každá událost má běžné informace, jako je zdroj události, čas, kdy došlo k události, a jedinečný identifikátor. Každá událost má také konkrétní informace, které jsou relevantní pouze pro konkrétní typ události. Podpora pro událost velikosti až 1 MB je v současnosti ve verzi Preview.

Vlastnosti, které jsou součástí události, najdete v tématu [Azure Event Grid schéma událostí](event-schemas.md).

## <a name="publishers"></a>Vydavatelé

Vydavatel je uživatel nebo organizace, které se rozhodly Odeslat události do Event Grid. Můžete publikovat události z vaší vlastní aplikace.

## <a name="event-sources"></a>Zdroje událostí

Zdroj události je místo, kde dojde k události. Každý zdroj události souvisí s jedním nebo více typy událostí. Například Azure Storage je zdroj události pro události vytvořené objektem blob. Vaše aplikace je zdroj události pro vlastní události, které definujete. Za odesílání událostí do Event Gridu zodpovídají zdroje událostí.

## <a name="topics"></a>Témata

Téma Event Grid poskytuje koncový bod, ve kterém zdroj odesílá události. Vydavatel vytvoří téma Event Grid a rozhodne, zda zdroj události potřebuje jedno téma nebo více než jedno téma. Téma se používá pro kolekci souvisejících událostí. Pro reakci na určité typy událostí předplatitelé rozhodují, která témata se přihlásí k odběru.

Při navrhování aplikace máte volnost v rozhodování o tom, kolik témat se má vytvořit. Pro velká řešení vytvořte vlastní téma pro každou kategorii souvisejících událostí. Představme si například aplikaci, která odesílá události související s úpravami uživatelských účtů a zpracováním objednávek. Není pravděpodobné, že nějaká obslužná rutina události chce přijímat obě kategorie událostí. Vytvořte dvě vlastní témata a nechte obslužné rutiny událostí odebírat to téma, které je zajímá. Pro malá řešení můžete chtít odeslat všechny události do jednoho tématu. Předplatitelé události mohou filtrovat typy událostí, které chtějí.

Postup správy témat v Event Grid najdete v [dokumentaci REST API](api.md) .

## <a name="event-subscriptions"></a>Odběry událostí

Předplatné oznamuje Event Grid, které události v tématu vás zajímá. Při vytváření odběru zadáte koncový bod pro zpracování události. Můžete filtrovat události, které se odesílají do koncového bodu. 

Informace o tom, jak spravovat předplatná v Event Grid, najdete v [dokumentaci REST API](api.md) .

## <a name="event-handlers"></a>Obslužné rutiny událostí

Z Event Grid perspektivy je obslužná rutina události místem, kde je událost odeslána. Obslužná rutina provede další akci zpracování události. Event Grid podporuje několik typů obslužných rutin. Jako obslužnou rutinu můžete použít podporovanou službu Azure nebo vlastní webový Hook. V závislosti na typu obslužné rutiny Event Grid podle různých mechanismů Zabezpečte doručení události. Pokud je cílová obslužná rutina události webovým zavěšením protokolu HTTP, událost se zopakuje, jakmile obslužná rutina vrátí stavový kód `200 – OK` . V případě hraničního centra, pokud je událost doručena bez výjimky, je považována za úspěšnou.

## <a name="security"></a>Zabezpečení

Event Grid poskytuje zabezpečení pro přihlášení k odběru témat a témata týkající se publikování. Další informace najdete v tématu [Event Grid zabezpečení a ověřování](security-authentication.md).

## <a name="event-delivery"></a>Doručování událostí

Pokud Event Grid nedokáže potvrdit, že koncový bod předplatitele událost přijal, znovu doručí událost. Další informace najdete v tématu [doručování zpráv Event Grid a opakování](delivery-retry.md).

## <a name="batching"></a>Dávkování

Při použití vlastního tématu musí být události vždy publikovány v poli. Pro scénáře s nízkou propustností bude pole obsahovat pouze jednu hodnotu. Pro případy s vysokým využitím doporučujeme, abyste v rámci publikování provedli několik událostí dohromady, abyste dosáhli vyšší efektivity. Dávky mohou být až 1 MB. Každá událost by neměla být větší než 1 MB (Preview).
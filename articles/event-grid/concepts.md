---
title: Koncepty Azure událostí mřížky
description: Popisuje Azure Event Grid a jeho koncepty. Definuje několik klíčových součástí událostí mřížky.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: tomfitz
ms.openlocfilehash: abc1302f0317c8d5ecdc7ddaf8ca6d3a9e82b582
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34626031"
---
# <a name="concepts-in-azure-event-grid"></a>Koncepty v mřížce Azure událostí

Tento článek popisuje hlavní koncepty v mřížce událostí Azure.

## <a name="events"></a>Události

Událost je nejmenší množství informací, která plně popisuje něco, kterým došlo v systému. Každý událost má běžné informace, jako jsou: Zdroj události, čas události trvalo místní a jedinečný identifikátor. Každé události má také konkrétní informace, které jsou pouze relevantní pro konkrétní typ události. Například událost o nový soubor vytváří ve službě Azure Storage obsahuje podrobnosti o souboru, například `lastTimeModified` hodnotu. Nebo událost Event Hubs má adresu URL souboru zachycení. 

Každá událost je omezená na 64 KB dat.

Vlastnosti, které se odesílají v události, najdete v části [schématu události mřížky událostí Azure](event-schema.md).

## <a name="publishers"></a>Vydavatelé

Vydavatel je uživatele nebo organizace, která se rozhodne odesílání událostí do mřížky událostí. Společnost Microsoft publikuje události pro několik služeb Azure. Události můžete publikovat z vlastní aplikace. Organizace, které jsou hostiteli služeb mimo Azure můžete publikovat události prostřednictvím událostí mřížky.

## <a name="event-sources"></a>Zdroje událostí

Zdroje událostí je, kde se stane události. Každý zdroj události se týká jeden nebo více typů událostí. Zdroj události pro vytvoření události objektu blob je například Azure Storage. Centrum IoT je zdroj události pro zařízení, které jsou vytvořeny události. Aplikace je zdroj události pro vlastní události, které definujete. Zdroje událostí jsou zodpovědní za odesílání událostí do mřížky událostí.

Informace o implementaci některé z podporovaných zdrojů mřížky událostí najdete v tématu [zdroje událostí v mřížce událostí Azure](event-sources.md).

## <a name="topics"></a>Témata

Téma mřížky událostí obsahuje koncový bod kde zdroj odesílá události. Vydavatel vytvoří téma mřížky události a rozhodne, zda zdroje událostí musí téma jeden nebo více než jeden. Téma se používá pro kolekci související události. Reagovat na určitých typech událostí, Odběratelé, kteří rozhodněte, které témata týkající se přihlásit k odběru.

Témata týkající se systému jsou předdefinované témata poskytovaném službami Azure. Nevidíte témata systému ve vašem předplatném Azure, protože vydavatele vlastní témata, ale k jejich odběru. K odběru, zadejte informace o prostředku, který chcete přijímat události z. Také mají přístup k prostředku, může přihlásit k jeho události.

Vlastní témata jsou aplikace a témata třetích stran. Při vytváření nebo přiřazené přístup k vlastní téma, zobrazí tento vlastní tématu v rámci vašeho předplatného.

Při navrhování vaší aplikace, budete mít flexibilitu při rozhodování o tom, kolik témata týkající se vytvoření. Pro velká řešení vytvořte vlastní heslo pro každou kategorii související události. Představte si třeba aplikaci, která odesílá události související s Změna uživatelských účtů a zpracování objednávky. Není pravděpodobné že žádné obslužné rutiny události chce, aby obě kategorie události. Vytvořte dva vlastní témata a nechat obslužné rutiny událostí přihlášení k odběru tomu, které je zajímají. Pro řešení pro malé dáváte přednost poslat jedno téma všechny události. Pro typy událostí, které chtějí můžete filtrovat události odběratele.

## <a name="event-subscriptions"></a>Odběry událostí

Předplatné informuje událostí mřížky, které události se na téma se zajímáte přijetí. Při vytváření odběru, je třeba zadat koncový bod pro zpracování události. Můžete filtrovat události, které se odesílají do koncového bodu. Můžete filtrovat podle typu události nebo vzor subjektu. Další informace najdete v tématu [schématu odběru událostí mřížky](subscription-creation-schema.md).

Příklady vytváření předplatná najdete v tématu:

* [Ukázek Azure CLI pro událost mřížky](cli-samples.md)
* [Ukázky pro událost mřížky Azure PowerShell](powershell-samples.md)
* [Šablony Azure Resource Manageru pro událost mřížky](template-samples.md)

Informace o získání aktuální událost mřížky předplatná najdete v tématu [mřížky událostí dotazu odběry](query-event-subscriptions.md).

## <a name="event-handlers"></a>Obslužné rutiny událostí

Z hlediska mřížce událost obslužné rutiny události je na místě, kde událost je odeslána. Obslužná rutina trvá některé další akce ke zpracování události. Událost mřížky podporuje více typy obslužných rutin. Podporované služby Azure nebo vlastní webhooku můžete použít jako obslužná rutina. V závislosti na typu obslužné rutiny události mřížky následuje různé mechanismy pro zaručit doručení události. Pro obslužné rutiny událostí webhooku protokolu HTTP, je událost opakovat, dokud obslužná rutina vrátí stavový kód `200 – OK`. Pro fronty Azure Storage jsou události opakovat, dokud služba fronty je schopna úspěšně zpracovat nabízené zprávy do fronty.

Informace o implementaci některé z podporovaných obslužných rutin událostí mřížky najdete v tématu [obslužné rutiny událostí v mřížce událostí Azure](event-handlers.md).

## <a name="security"></a>Zabezpečení

Událost mřížky zajišťuje zabezpečení registrace k tématům a publikování témata. Při přihlášení k odběru, musí mít odpovídající oprávnění k tématu mřížky prostředku nebo událostí. Při publikování, musíte mít tokenu SAS nebo ověření pomocí klíče pro téma. Další informace najdete v tématu [mřížky událostí zabezpečení a ověřování](security-authentication.md).

## <a name="event-delivery"></a>Události doručení

Pokud události mřížky nelze potvrdit, že událost byla přijata bodem odběratele, redelivers události. Další informace najdete v tématu [doručení zpráv událostí mřížky a zkuste to znovu](delivery-and-retry.md).

## <a name="next-steps"></a>Další postup

* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Chcete-li rychle začít používat událostí mřížky, přečtěte si téma [vytvořit a směrování vlastních událostí s Azure událostí mřížky](custom-event-quickstart.md).

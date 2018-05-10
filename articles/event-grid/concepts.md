---
title: Koncepty Azure událostí mřížky
description: Popisuje mřížky událostí Azure a jeho koncepty. Definuje několik klíčových součástí událostí mřížky.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/24/2018
ms.author: babanisa
ms.openlocfilehash: 8ddde98b448f4d6d6f24a2ee47acf9240593622c
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="concepts-in-azure-event-grid"></a>Koncepty v mřížce Azure událostí

Tento článek popisuje hlavní koncepty v mřížce událostí Azure.

## <a name="events"></a>Události

Událost je nejmenší množství informací, která plně popisuje něco, kterým došlo v systému. Každý událost má běžné informace, jako jsou: Zdroj události, čas události trvalo místní a jedinečný identifikátor. Každé události má také konkrétní informace, které jsou pouze relevantní pro konkrétní typ události. Například událost o nový soubor vytváří ve službě Azure Storage obsahuje podrobnosti o souboru, například `lastTimeModified` hodnotu. Nebo událost Event Hubs má adresu URL souboru zachycení. Každá událost je omezená na 64 KB dat.

## <a name="event-sourcespublishers"></a>Zdroje nebo zdroje událostí

Zdroje událostí je, kde se stane události. Zdroj události pro vytvoření události objektu blob je například Azure Storage. Prostředky infrastruktury Azure virtuálního počítače je zdroj události pro události se virtuální počítač. Zdroje událostí jsou zodpovědní za publikování událostí do mřížky událostí.

Informace o implementaci některé z podporovaných zdrojů mřížky událostí najdete v tématu [zdroje událostí v mřížce událostí Azure](event-sources.md).

## <a name="topics"></a>Témata

Vydavatelé zařadit do témata události. Téma mřížky událostí obsahuje koncový bod kde vydavatele odesílá události. Reagovat na určitých typech událostí, Odběratelé, kteří rozhodněte, které témata týkající se přihlásit k odběru. Témata také poskytují schématu událostí tak, aby odběratelé, kteří můžou zjistit, jak využívat události správně.

Témata týkající se systému jsou předdefinované témata poskytovaném službami Azure. Vlastní témata jsou aplikace a témata třetích stran.

Při navrhování vaší aplikace, budete mít flexibilitu při rozhodování o tom, kolik témata týkající se vytvoření. Pro velká řešení vytvořte vlastní heslo pro každou kategorii související události. Představte si třeba aplikaci, která odesílá události související s Změna uživatelských účtů a zpracování objednávky. Není pravděpodobné že žádné obslužné rutiny události chce, aby obě kategorie události. Vytvořte dva vlastní témata a nechat obslužné rutiny událostí přihlášení k odběru tomu, které je zajímají. Pro řešení pro malé dáváte přednost poslat jedno téma všechny události. Pro typy událostí, které chtějí můžete filtrovat události odběratele.

## <a name="event-subscriptions"></a>Odběry událostí

Předplatné dá pokyn událostí mřížky, na které události se na téma má zájem o přijetí odběratele. Předplatné také obsahuje informace o tom, jak by měla událostí doručit do odběratele.

## <a name="event-handlers"></a>Obslužné rutiny událostí

Z hlediska mřížce událost obslužné rutiny události je na místě, kde událost je odeslána. Obslužná rutina trvá některé další akce ke zpracování události. Událost mřížky podporuje více typů odběratele. V závislosti na typu odběratele následuje událostí mřížky různé mechanismy pro zaručit doručení události. Pro obslužné rutiny událostí webhooku protokolu HTTP, je událost opakovat, dokud obslužná rutina vrátí stavový kód `200 – OK`. Pro fronty Azure Storage jsou události opakovat, dokud služba fronty je schopna úspěšně zpracovat nabízené zprávy do fronty.

Informace o implementaci některé z podporovaných obslužných rutin událostí mřížky najdete v tématu [obslužné rutiny událostí v mřížce událostí Azure](event-handlers.md).

## <a name="filters"></a>Filtry

Když se přihlásíte k odběru téma události mřížky, můžete filtrovat události, které se odesílají do koncového bodu. Můžete filtrovat podle typu události nebo vzor subjektu. Další informace najdete v tématu [schématu odběru událostí mřížky](subscription-creation-schema.md).

## <a name="security"></a>Zabezpečení

Událost mřížky zajišťuje zabezpečení registrace k tématům a publikování témata. Při přihlášení k odběru, musí mít odpovídající oprávnění k tématu mřížky prostředku nebo událostí. Při publikování, musíte mít tokenu SAS nebo ověření pomocí klíče pro téma. Další informace najdete v tématu [mřížky událostí zabezpečení a ověřování](security-authentication.md).

## <a name="failed-delivery"></a>Neúspěšné doručení

Pokud události mřížky nelze potvrdit, že událost byla přijata bodem odběratele, redelivers události. Další informace najdete v tématu [doručení zpráv událostí mřížky a zkuste to znovu](delivery-and-retry.md).

## <a name="next-steps"></a>Další postup

* Úvod k mřížce událostí, naleznete v části [o mřížky událostí](overview.md).
* Chcete-li rychle začít používat událostí mřížky, přečtěte si téma [vytvořit a směrování vlastních událostí s Azure událostí mřížky](custom-event-quickstart.md).

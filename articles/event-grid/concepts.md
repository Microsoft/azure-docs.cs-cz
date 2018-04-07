---
title: Koncepty Azure událostí mřížky
description: Popisuje mřížky událostí Azure a jeho koncepty. Definuje několik klíčových součástí událostí mřížky.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 04/04/2018
ms.author: babanisa
ms.openlocfilehash: e55127e60470f8f95235893a14113b80e8d6565b
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="concepts-in-azure-event-grid"></a>Koncepty v mřížce Azure událostí

Hlavní koncepty v mřížce událostí Azure jsou:

## <a name="events"></a>Události

Událost je nejmenší množství informací, která plně popisuje něco, kterým došlo v systému.  Každý událost má běžné informace, jako jsou: Zdroj události, čas události trvalo místní a jedinečný identifikátor.  Každé události má také konkrétní informace, které jsou pouze relevantní pro konkrétní typ události. Například událost o nový soubor vytváří ve službě Azure Storage obsahuje podrobnosti o souboru, jako `lastTimeModified` hodnotu. Nebo událost o virtuální počítač restartování obsahuje název virtuálního počítače a důvody, proč restartování. Každá událost je omezená na 64 KB dat.

## <a name="event-sourcespublishers"></a>Zdroje nebo zdroje událostí

Zdroje událostí je, kde se stane události. Zdroj události pro vytvoření události objektu blob je například Azure Storage. Prostředky infrastruktury Azure virtuálního počítače je zdroj události pro události se virtuální počítač. Zdroje událostí jsou zodpovědní za publikování událostí do mřížky událostí.

## <a name="topics"></a>Témata

Vydavatelé zařadit do témata události. Téma obsahuje koncový bod kde vydavatele odesílá události. Reagovat na určitých typech událostí, Odběratelé, kteří rozhodněte, které témata týkající se přihlásit k odběru. Témata týkající se také nabízí schématu událostí, takže Odběratelé, kteří můžete zjistit, jak využívat události správně.

Témata týkající se systému jsou předdefinované témata poskytovaném službami Azure. Vlastní témata jsou aplikace a témata třetích stran.

Při navrhování vaší aplikace, vytvořte vlastní heslo pro každou kategorii související události. Představte si třeba aplikaci, která odesílá události související s Změna uživatelských účtů a zpracování objednávky. Není pravděpodobné že žádné obslužné rutiny události chce, aby obě kategorie události. Vytvořte dva vlastní témata a nechat obslužné rutiny událostí přihlášení k odběru tomu, které je zajímají. Když se přihlásíte k odběru vlastní tématu, obslužné rutiny události lze filtrovat podle typu události.

## <a name="event-subscriptions"></a>Odběry událostí

Předplatné dá pokyn událostí mřížky, na které události se na téma má zájem o přijetí odběratele. Předplatné také obsahuje informace o tom, jak by měla událostí doručit do odběratele.

## <a name="event-handlers"></a>Obslužné rutiny událostí

Z hlediska mřížce událost obslužné rutiny události je na místě, kde událost je odeslána. Obslužná rutina trvá některé další akce ke zpracování události.  Událost mřížky podporuje více typů odběratele. V závislosti na typu odběratele následuje událostí mřížky různé mechanismy pro zaručit doručení události.  Pro obslužné rutiny událostí webhooku protokolu HTTP, je událost opakovat, dokud obslužná rutina vrátí stavový kód `200 – OK`. Pro fronty Azure Storage jsou události opakovat, dokud služba fronty je schopna úspěšně zpracovat nabízené zprávy do fronty.

## <a name="filters"></a>Filtry

Když se přihlásíte k odběru téma, můžete filtrovat události, které se odesílají do koncového bodu. Můžete filtrovat podle typu události nebo vzor subjektu. Další informace najdete v tématu [schématu odběru událostí mřížky](subscription-creation-schema.md).

## <a name="security"></a>Zabezpečení

Událost mřížky zajišťuje zabezpečení registrace k tématům a publikování témata. Při přihlášení k odběru, musí mít odpovídající oprávnění na prostředek nebo téma. Při publikování, musíte mít tokenu SAS nebo ověření pomocí klíče pro téma. Další informace najdete v tématu [mřížky událostí zabezpečení a ověřování](security-authentication.md).

## <a name="failed-delivery"></a>Neúspěšné doručení

Když mřížky událostí nelze potvrdit, že událost byla přijata bodem odběratele, redelivers události. Další informace najdete v tématu [doručení zpráv událostí mřížky a zkuste to znovu](delivery-and-retry.md).

## <a name="next-steps"></a>Další postup

* Úvod k mřížce událostí, naleznete v části [o mřížky událostí](overview.md).
* Chcete-li rychle začít používat událostí mřížky, přečtěte si téma [vytvořit a směrování vlastních událostí s Azure událostí mřížky](custom-event-quickstart.md).

---
title: Domény událostí v Azure Event Grid
description: Tento článek popisuje, jak pomocí domén událostí spravovat tok vlastních událostí do různých obchodních organizací, zákazníků nebo aplikací.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: f6698f91d7659f9fc2c314a9291380301146f8ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898868"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Principy domén událostí pro správu témat služby Event Grid

Tento článek popisuje, jak pomocí domén událostí spravovat tok vlastních událostí do různých obchodních organizací, zákazníků nebo aplikací. Pomocí domén událostí můžete:

* Spravujte víceklientské architektury událostí ve velkém měřítku.
* Spravujte autorizaci a ověřování.
* Rozdělte témata bez správy jednotlivých.
* Vyhněte se individuálnípublikování na každý z koncových bodů tématu.

## <a name="event-domain-overview"></a>Přehled domény událostí

Doména událostí je nástroj pro správu pro velký počet témat event gridu souvisejících se stejnou aplikací. Můžete si to myslet jako meta-téma, které může mít tisíce jednotlivých témat.

Domény událostí vám zpřístupní stejnou architekturu, kterou používají služby Azure (jako je Storage a IoT Hub) k publikování svých událostí. Umožňují publikovat události na tisíce témat. Domény také poskytují oprávnění a ověřování kontrolu nad jednotlivými tématy, takže můžete rozdělit své klienty.

### <a name="example-use-case"></a>Příklad případu použití

Domény událostí lze nejsnadněji vysvětlit pomocí příkladu. Řekněme, že provozujete společnosti Contoso Construction Machinery, kde vyrábíte traktory, výkopové zařízení a další těžké stroje. V rámci provozování podniku můžete zákazníkům v reálném čase informace o údržbě zařízení, stavu systémů a aktualizacích smluv. Všechny tyto informace přejde do různých koncových bodů, včetně vaší aplikace, koncových bodů zákazníků a další infrastruktury, kterou zákazníci nastavili.

Domény událostí umožňují modelovat stavební stroje Contoso jako jednu entitu událostí. Každý z vašich zákazníků je reprezentován jako téma v rámci domény. Ověřování a autorizace se zpracovávají pomocí služby Azure Active Directory. Každý z vašich zákazníků se může přihlásit k odběru svého tématu a nechat si své události doručit. Spravovaný přístup prostřednictvím domény událostí zajišťuje, že mají přístup pouze ke svému tématu.

Poskytuje také jeden koncový bod, do kterého můžete publikovat všechny události zákazníků. Event Grid se postará o to, aby každé téma vědělo pouze o událostech, které jsou vymezeny jeho tenantovi.

![Příklad výstavby společnosti Contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Správa přístupu

S doménou získáte jemné oprávnění zrnitosti a kontrolu ověřování nad každým tématem prostřednictvím řízení přístupu azure na základě rolí (RBAC). Tyto role můžete použít k omezení každého klienta ve vaší aplikaci pouze na témata, ke kterým chcete udělit přístup.

RBAC v událostech domény funguje stejným způsobem [spravované řízení přístupu](security-authorization.md) funguje ve zbytku Event Grid a Azure. Pomocí RBAC vytvořte a vynucujte vlastní definice rolí v doménách událostí.

### <a name="built-in-roles"></a>Vytvořené role

Event Grid má dvě předdefinované definice rolí, které usnadňují RBAC pro práci s doménami událostí. Tyto role jsou **EventGrid EventSubscription Přispěvatel (Náhled)** a **EventGrid EventSubscription Reader (Preview)**. Tyto role přiřadíte uživatelům, kteří se potřebují přihlásit k odběru témat ve vaší doméně událostí. Přiřazení role můžete obor pouze téma, které uživatelé potřebují k odběru.

Informace o těchto rolích naleznete [v tématu Předdefinované role pro event grid](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Přihlášení k odběru témat

Přihlášení k odběru událostí na téma v doméně událostí je stejné jako [vytvoření odběru událostí na vlastní téma](./custom-event-quickstart.md) nebo přihlášení k odběru události ze služby Azure.

### <a name="domain-scope-subscriptions"></a>Odběry oboru domény

Domény událostí také umožňují odběry oboru domény. Odběr událostí v doméně událostí obdrží všechny události odeslané do domény bez ohledu na téma, na které jsou události odesílány. Odběry oboru domény může být užitečné pro účely správy a auditování.

## <a name="publishing-to-an-event-domain"></a>Publikování do domény událostí

Když vytvoříte doménu událostí, dostanete koncový bod publikování podobný, jako když jste vytvořili téma v Event Grid. 

Chcete-li publikovat události na libovolné téma v doméně událostí, posuňte události do koncového bodu domény [stejným způsobem jako u vlastního tématu](./post-to-custom-topic.md). Jediným rozdílem je, že je nutné zadat téma, do kterého chcete událost doručit.

Například publikování následujícího pole událostí by `"id": "1111"` odeslat událost s tématem, `foo` zatímco událost s `"id": "2222"` by být odeslány na téma `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Domény událostí zpracovávají publikování na témata za vás. Místo publikování událostí ke každému tématu, které spravujete jednotlivě, můžete publikovat všechny události do koncového bodu domény. Event Grid zajišťuje, že každá událost je odeslána na správné téma.

## <a name="limits-and-quotas"></a>Omezení a kvóty
Zde jsou limity a kvóty související s doménami událostí:

- 100 000 témat na doménu události 
- 100 domén událostí na jedno předplatné Azure 
- 500 odběrů událostí na téma v doméně událostí
- 50 odběrů oboru domény 
- Rychlost požití 5 000 událostí za sekundu (do domény)

Pokud vám tato omezení nevyhovují, oslovte produktový tým otevřením [askgrid@microsoft.com](mailto:askgrid@microsoft.com)lístku podpory nebo odesláním e-mailu na adresu . 

## <a name="pricing"></a>Ceny
Domény událostí používají stejné [provozní ceny,](https://azure.microsoft.com/pricing/details/event-grid/) jakoy používají všechny ostatní funkce v event gridu.

Operace fungují v doménách událostí stejně jako ve vlastních tématech. Každý příchozí přenos události do domény události je operace a každý pokus o doručení pro událost je operace.

## <a name="next-steps"></a>Další kroky

* Informace o nastavení domén událostí, vytváření témat, vytváření odběrů událostí a publikování událostí najdete v tématu [Správa domén událostí](./how-to-event-domains.md).

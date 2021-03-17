---
title: Domény událostí v Azure Event Grid
description: Tento článek popisuje, jak pomocí domén událostí spravovat tok vlastních událostí v různých obchodních organizacích, zákaznících nebo aplikacích.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 46a50a8ecc50bd1b80efcba41228564df1c36c9f
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198647"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Porozumění doménám událostí pro správu Event Grid témata

Tento článek popisuje, jak pomocí domén událostí spravovat tok vlastních událostí v různých obchodních organizacích, zákaznících nebo aplikacích. Domény událostí použijte k těmto akcím:

* Spravujte škálovatelné architektury pro víceklientské řízení událostí.
* Spravujte autorizaci a ověřování.
* Rozdělte témata, aniž byste je museli spravovat jednotlivě.
* Nevybírejte jednotlivě publikování do každého z vašich koncových bodů vašeho tématu.

## <a name="event-domain-overview"></a>Přehled domény událostí

Doména události je nástroj pro správu velkého počtu Event Gridch témat týkajících se stejné aplikace. Můžete si ho představit jako meta téma, které může obsahovat tisíce jednotlivých témat.

Domény událostí zpřístupňují stejnou architekturu, kterou používají služby Azure (například úložiště a IoT Hub) k publikování jejich událostí. Umožňují publikovat události do tisíců témat. Domény také poskytují autorizaci a kontrolu ověřování pro jednotlivá témata, abyste mohli rozdělit klienty na oddíly.

## <a name="example-use-case"></a>Příklad případu použití
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="access-management"></a>Správa přístupu

S doménou můžete pomocí řízení přístupu založeného na rolích Azure (Azure RBAC) získat podrobné řízení ověřování a ověřování pro každé téma. Pomocí těchto rolí můžete omezit každého tenanta ve vaší aplikaci jenom na témata, ke kterým chcete udělit přístup.

Funkce Azure RBAC v doménách událostí funguje stejným způsobem jako [spravované řízení přístupu](security-authorization.md) ve zbývajících Event Grid a Azure. Pomocí Azure RBAC můžete vytvářet a vymáhat definice vlastních rolí v doménách událostí.

### <a name="built-in-roles"></a>Předdefinované role

Event Grid má dvě předdefinované definice rolí, které usnadňují práci s doménami služby Azure RBAC. Tyto role jsou **EventGrid EventSubscription Přispěvatel (Preview)** a **EventGrid EventSubscription Reader (Preview)**. Tyto role přiřadíte uživatelům, kteří se potřebují přihlašovat k odběru témat v doméně událostí. Přiřadíte rozsah přiřazení role pouze k tématu, které uživatelé potřebují k přihlášení k odběru.

Informace o těchto rolích najdete v tématu [předdefinované role pro Event Grid](security-authorization.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Přihlášení k odběru témat

Přihlášení k odběru událostí v tématu v rámci domény události je stejné jako [Vytvoření odběru události pro vlastní téma](./custom-event-quickstart.md) nebo přihlášení k odběru události ze služby Azure.

### <a name="domain-scope-subscriptions"></a>Předplatné oboru domény

Domény událostí také umožňují odběry rozsahu domény. Odběr události v doméně události obdrží všechny události, které jsou odeslány do domény bez ohledu na téma, na které jsou události odesílány. Odběry oborů domény mohou být užitečné pro účely správy a auditování.

## <a name="publishing-to-an-event-domain"></a>Publikování do domény událostí

Když vytvoříte doménu události, budete mít k disEvent Grid koncový bod publikování podobný tomu, pokud jste vytvořili téma v. 

Pokud chcete publikovat události do libovolného tématu v doméně události, nahrajte události do koncového bodu domény [stejným způsobem jako u vlastního tématu](./post-to-custom-topic.md). Jediným rozdílem je, že musíte zadat téma, do kterého chcete událost doručit.

Publikování následujícího pole událostí by například odesílalo událost s `"id": "1111"` tématem v `foo` době, kdy se událost, která byla `"id": "2222"` odeslána do tématu `bar` :

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

Domény událostí zpracovávají publikování na témata za vás. Místo publikování událostí pro každé téma, které spravujete jednotlivě, můžete publikovat všechny události do koncového bodu domény. Event Grid zajišťují, aby se všechny události poslaly do správného tématu.

## <a name="limits-and-quotas"></a>Omezení a kvóty
Tady jsou limity a kvóty související s doménami událostí:

- témata 100 000 na doménu události 
- 100 domén událostí na předplatné Azure 
- 500 odběry událostí na téma v doméně události
- 50 rozsahů doménových předplatných 
- frekvence příjmu událostí 5 000 za sekundu (do domény)

Pokud vám tato omezení neodpovídají, můžete se obrátit na produktového týmu otevřením lístku podpory nebo odesláním e-mailu na adresu [askgrid@microsoft.com](mailto:askgrid@microsoft.com) . 

## <a name="pricing"></a>Ceny
Domény událostí používají stejné [ceny operací](https://azure.microsoft.com/pricing/details/event-grid/) jako u všech ostatních funkcí v Event Grid použít.

Operace fungují v doménách událostí stejně jako v uživatelských tématech. Každá příchozí událost události do domény události je operace a každý pokus o doručení události je operace.



## <a name="next-steps"></a>Další kroky

* Další informace o nastavení domén událostí, vytváření témat, vytváření odběrů událostí a publikování událostí najdete v tématu [Správa domén událostí](./how-to-event-domains.md).

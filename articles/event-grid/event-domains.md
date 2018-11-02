---
title: Událost domény ve službě Azure Event Grid
description: Popisuje, jak domén události se používají ke správě témata ve službě Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 11/01/2018
ms.openlocfilehash: b5a1e521f46da7feffd2eb556ad1662a9ccaee55
ms.sourcegitcommit: 799a4da85cf0fec54403688e88a934e6ad149001
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50913832"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Principy událostí domény pro správu tématy Event gridu

Tento článek popisuje, jak používat události domény ke správě toku vlastní události a různých obchodních organizací, zákazníkům nebo aplikace. Použití událostí domény:

* Správa architektury víceklientské zpracování událostí ve velkém měřítku.
* Spravujte ověřování a ověřování.
* Rozdělte témat bez nutnosti spravovat každou jednotlivě.
* Vyhněte se jednotlivě publikování u každého z vašich koncových bodů tématu.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="event-domain-overview"></a>Přehled událostí domény

Doménu událostí je nástroj pro správu pro velký počet služby Event Grid témat souvisejících s tu samou aplikaci. Jeho si můžete představit jako meta téma, které mají tisíce jednotlivých tématech.

Událost domény zpřístupňují stejnou architekturu používá služby Azure (třeba úložiště a služby IoT Hub) k publikování události. Umožňují vám k publikování událostí do tisíce témata. Domény také poskytují autorizaci a ověřování kontrolu nad každé téma tak můžete dělit vašim klientům.

### <a name="example-use-case"></a>Případy použití příklad

Událost domény jsou vysvětleny nejsnadněji využitím příkladu. Řekněme, že spustíte výbavu konstrukce Contoso, kde vyrábíte traktorů digging zařízení a dalších náročné stroje. Jako součást vedení firmy nahrajete do zákazníků týkající se údržby zařízení, stavu systému a aktualizace smlouvy informace v reálném čase. Všech těchto informací směruje do různých koncových bodů, včetně aplikace, koncové body zákazníka a další infrastrukturu, která jste nastavili zákazníků.

Událost domény umožňují model Contoso konstrukce stroje jako entita jeden zpracování událostí. Všechny vaše zákazníky je reprezentována jako téma v rámci domény. Ověřování a autorizaci provádí pomocí služby Azure Active Directory. Každý z vašich zákazníků můžete přihlášení k odběru jejich tématu a jejich události doručené na ně. Spravovaný přístup prostřednictvím služby domény události zajišťuje jejich přístup jenom k jejich tématu.

Také poskytuje jeden koncový bod, který můžete publikovat všechny události zákazníka. Event Grid se postará o ověříte, zda že je pouze o seznámen události s rozsahem v jeho tenantovi každého tématu.

![Příklad konstrukce contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Správa přístupu

V doméně získáte nich spočívá v jemné autorizaci a ověřování kontrolu nad každého tématu prostřednictvím řízení přístupu na základě rolí Azure (RBAC). Tyto role můžete použít k omezení každého klienta ve vaší aplikaci na témata, které chcete udělit jim přístup k.

RBAC v doménách událostí funguje stejným způsobem [spravovat řízení přístupu](security-authentication.md#management-access-control) funguje ve zbývající části služby Event Grid a Azure. Pomocí RBAC můžete vytvářet a vynucovat vlastní definice rolí v doménách události.

### <a name="built-in-roles"></a>Vestavěné role

Event Grid má dvě definice předdefinovanou roli RBAC snazší pro práci s doménami události. Tyto role jsou **EventGrid EventSubscription Přispěvatel (Preview)** a **EventGrid EventSubscription Čtenář (Preview)**. Přiřadíte tyto role uživatelů, kteří potřebují k odběru témat ve vaší doméně události. Můžete omezit rozsah přiřazení role pouze, kterou je potřeba uživatelům přihlásit se k odběru tématu.

Informace o těchto rolích najdete v tématu [předdefinované role pro Event Grid](security-authentication.md#built-in-roles).

## <a name="subscribing-to-topics"></a>Registrace k odběru témat

Přihlášení k odběru událostí k tématu v rámci domény události je stejná jako [vytvoření odběru událostí vlastního tématu](./custom-event-quickstart.md) nebo se přihlásíte k odběru události ze služby Azure.

### <a name="domain-scope-subscriptions"></a>Předplatná obor domény

Událost domény také povolit pro předplatná obor domény. Odběr událostí pro doménu události se zobrazí všechny události odeslané do domény bez ohledu na to, které se odesílají události do tématu. Předplatná obor domény může být užitečné pro účely auditování a správu.

## <a name="publishing-to-an-event-domain"></a>Publikování do domény události

Při vytváření domény služby event už daný podobně jako koncový bod publikování, pokud jste vytvořili téma ve službě Event Grid. 

K publikování událostí do libovolného tématu v doméně události, push události do koncového bodu doméně [stejně jako kdybyste vlastního tématu](./post-to-custom-topic.md). Jediným rozdílem je, že je nutné zadat byste chtěli událostí, který bude doručen do tématu.

Například publikování následující pole událostí bude posílat události s `"id": "1111"` téma `foo` současně přitom o událost s `"id": "2222"` by odeslané do tématu `bar`:

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

Událost domény zpracování publikování na témata za vás. Místo publikování událostí do každého tématu, která spravujete jednotlivě můžete publikovat všechny události do koncového bodu doméně. Event Grid je zajištěno, že každá událost je odeslána do správné téma.

## <a name="limits-and-quotas"></a>Omezení a kvóty

### <a name="control-plane"></a>Rovina řízení

Událost domény jsou ve verzi preview omezené na 1 000 témat v rámci domény a 50 odběry událostí na téma v rámci domény. Odběry událostí domény oboru jsou také omezené na 50.

### <a name="data-plane"></a>Rovina dat

Ve verzi preview bude omezená na stejné 5 000 událostí za druhé ingestování kurz, který vlastní témata jsou omezené na propustnost událostí pro událost doménu.

## <a name="pricing"></a>Ceny

Ve verzi preview, události domény použít stejné [operace ceny](https://azure.microsoft.com/pricing/details/event-grid/) , použít všechny ostatní funkce ve službě Event Grid.

Operace fungovat stejně v doménách události, stejně jako vlastní témata. Každého příchozího přenosu dat události k doméně událostí je operace a jednotlivé pokusy o doručení pro události je operace.

## <a name="next-steps"></a>Další postup

* Další informace o nastavení domén událostí najdete v tématu Vytvoření témata, odběry událostí vytvoření a publikování událostí, [spravovat události domény](./how-to-event-domains.md).
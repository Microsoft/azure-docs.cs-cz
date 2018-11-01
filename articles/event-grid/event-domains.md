---
title: Událost domény ve službě Azure Event Grid
description: Popisuje, jak domén události se používají ke správě témata ve službě Azure Event Grid.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: fe66ca8b8f5b4474290e302f73b35868dce68caa
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50633786"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Principy událostí domény pro správu témata Event gridu

Tento článek popisuje, jak používat události domény ke správě toku vlastních událostí pro různé obchodní organizace, zákazníkům nebo aplikace. Použití událostí domény:

* Správa architektury víceklientské zpracování událostí ve velkém měřítku.
* Spravujte ověřování a ověřování.
* Rozdělte témat bez nutnosti spravovat každou jednotlivě.
* Vyhněte se jednotlivě publikování u každého z vašich koncových bodů tématu.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]


## <a name="event-domains-overview"></a>Přehled služby Event domén

Doménu událostí je nástroj pro správu pro velký počet témata Event gridu související se stejná aplikace. Jeho si můžete představit jako meta téma, které mohou obsahovat tisíce jednotlivá témata.

Událost domény zkontrolujte architektura služby Azure, jako jsou úložiště a služby IoT Hub použít k publikování jejich událostí, které jsou k dispozici pro použití ve svém vlastním systému. Umožňují vám k publikování událostí do tisíce témata. Domény také poskytují autorizaci a ověřování kontrolu nad každé téma tak můžete dělit vašim klientům.

### <a name="example-use-case"></a>Případy použití příklad

Událost domény jsou vysvětleny nejsnadněji využitím příkladu. Řekněme, že spustíte výbavu konstrukce Contoso, kde vyrábíte traktorů digging zařízení a dalších náročné stroje. Jako součást vedení firmy, nahrajete do zákazníků v reálném čase informace týkající se údržby zařízení, stavu systémy smlouvy aktualizace atd. Všechny tyto informace přejde do různých koncových bodů, včetně aplikací, zákazník koncových bodů a dalších zákazníků infrastruktury mají nastavení.

Událost domény umožňuje model Contoso konstrukce stroje jako entita jeden zpracování událostí. Všechny vaše zákazníky je vyjádřena jako téma v rámci domény / ověřování a autorizaci provádí pomocí služby Azure Active Directory. Každý z vašich zákazníků můžete přihlášení k odběru jejich tématu a jejich události doručené na ně. Spravovaný přístup prostřednictvím služby domény události zajišťuje jejich přístup jenom k jejich tématu.

Také poskytuje jeden koncový bod, který můžete publikovat všechny události zákazníka. Event Grid se postará o ověříte, zda že je pouze o seznámen události s rozsahem v jeho tenantovi každého tématu.

![Příklad konstrukce contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>Správa přístupu

 V doméně získáte nich spočívá v jemné autorizaci a ověřování kontrolu nad každého tématu prostřednictvím Azure na základě Role přístupu zkontrolujte (RBAC). Tyto role můžete použít k omezení každého klienta ve vaší aplikaci na témata, které chcete udělit jim přístup k.

Na základě role přístupu zkontrolujte (RBAC) v doménách událostí funguje stejným způsobem [spravovat řízení přístupu](https://docs.microsoft.com/azure/event-grid/security-authentication#management-access-control) funguje ve zbývající části služby Event Grid a Azure. Pomocí RBAC můžete vytvářet a vynucovat vlastní definice rolí v doménách události.

### <a name="built-in-roles"></a>Vestavěné role

Event Grid má dvě definice předdefinovanou roli RBAC snazší:

#### <a name="eventgrid-eventsubscription-contributor-preview"></a>Přispěvatel EventSubscription EventGrid (Preview)

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

#### <a name="eventgrid-eventsubscription-reader-preview"></a>Čtenář EventSubscription EventGrid (Preview)

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="subscribing-to-topics"></a>Registrace k odběru témat

Přihlášení k odběru událostí k tématu v rámci domény události je stejná jako [vytvoření odběru událostí vlastního tématu](./custom-event-quickstart.md) nebo přihlášení k odběru na kteroukoli z předdefinovaných události vydavatele Azure nabízí.

### <a name="domain-scope-subscriptions"></a>Předplatná obor domény

Událost domény také povolit pro předplatná oboru domény. Odběr událostí pro doménu události se zobrazí všechny události odeslané do domény bez ohledu na to, které se odesílají události do tématu. Předplatná obor domény může být užitečné pro účely auditování a správu.

## <a name="publishing-to-an-event-domain"></a>Publikování do domény události

Při vytváření domény služby Event už daný podobně jako koncový bod publikování, pokud jste vytvořili téma ve službě Event Grid. 

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

Událost domény zpracování publikování na témata za vás. Místo publikování událostí do každého tématu, která spravujete jednotlivě, můžete publikovat všechny události do koncového bodu domény a služby Event Grid stará o to, že každá událost je odeslána do správné téma.

## <a name="limits-and-quotas"></a>Omezení a kvóty

### <a name="control-plane"></a>Rovina řízení

Ve verzi preview bude omezená na 1 000 témat v rámci domény a 50 odběry událostí na téma v rámci domény událostí domény. Odběry událostí domény oboru také bude omezená na 50.

### <a name="data-plane"></a>Rovina dat

Ve verzi preview bude omezená na stejné 5 000 událostí za druhé ingestování kurz, který vlastní témata jsou omezené na propustnost událostí pro událost doménu.

## <a name="pricing"></a>Ceny

Ve verzi preview, události domény budou používat stejné [operace ceny](https://azure.microsoft.com/pricing/details/event-grid/) , použít všechny ostatní funkce ve službě Event Grid.

Operace fungovat stejně v doménách události, stejně jako vlastní témata. Každého příchozího přenosu dat události k doméně událostí je operace a jednotlivé pokusy o doručení pro události je operace.

## <a name="next-steps"></a>Další postup

* Další informace o nastavení domén událostí najdete v tématu Vytvoření témata, odběry událostí vytvoření a publikování událostí, [Správa domén události](./how-to-event-domains.md).
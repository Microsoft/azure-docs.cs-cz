---
title: Připojení jako Azure Event Grid partner
description: Připojte se jako typ tématu Azure Event Grid partner. Seznamte se s modelem prostředků a s průběhem publikování pro témata partnerů.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 2a1f35b86e21099c9fdd0397ae8a3b20aed3cd5d
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758822"
---
# <a name="onboard-as-an-azure-event-grid-partner"></a>Připojení jako Azure Event Grid partner

Tento článek popisuje, jak soukromě používat Event Grid prostředky partnerů a jak se stát veřejně dostupným typem partnerského tématu.

Nepotřebujete zvláštní oprávnění, abyste mohli začít používat Event Grid typy prostředků přidružené k událostem publikování jako partner Event Grid. Ve skutečnosti je můžete používat v dnešní době k publikování událostí soukromě do vlastních předplatných Azure a k otestování modelu prostředků, Pokud zvažujete, že se stanete partnerem.

## <a name="becoming-an-event-grid-partner"></a>Stane se Event Grid partnerem

Pokud se zajímáte o veřejný partner Event Grid, začněte vyplněním [tohoto formuláře](https://aka.ms/gridpartnerform)a pak se obraťte na tým Event Grid na adrese [GridPartner@microsoft.com](mailto:gridpartner@microsoft.com) .

## <a name="how-partner-topics-work"></a>Jak fungují témata partnerů
Partnerská témata přebírají stávající architekturu, která Event Grid už používá v k publikování událostí z prostředků Azure, jako je například úložiště a IoT Hub, a zpřístupňuje tyto nástroje všem uživatelům, kteří je používají. Používání těchto nástrojů je standardně soukromé jenom pro vaše předplatné Azure. Pokud chcete, aby byly události veřejně dostupné, vyplňte výše uvedený formulář a [kontaktujte tým Event Grid](mailto:gridpartner@microsoft.com).

Témata o partnerovi umožňují publikovat události pro Azure Event Grid pro víceklientské využití.

### <a name="onboarding-and-event-publishing-overview"></a>Přehled zprovoznění a publikování událostí

#### <a name="partner-flow"></a>Partnerský tok

1. Vytvořte tenanta Azure, pokud ho ještě nemáte.
1. Pomocí rozhraní příkazového řádku vytvořte novou Event Grid `partnerRegistration` . Tento prostředek obsahuje informace, jako je zobrazované jméno, popis, identifikátor URI instalace atd.

    ![Vytvořit partnerský předmět](./media/partner-onboarding-how-to/create-partner-registration.png)

1. `partnerNamespaces`V každé oblasti, kterou chcete publikovat události, vytvořte jednu nebo více. V rámci této služby Event Grid služba zřídit koncový bod publikování (například `https://contoso.westus-1.eventgrid.azure.net/api/events` ) a přístupové klíče.

    ![Vytvořit obor názvů partnera](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Poskytněte zákazníkům způsob, jak se zaregistrovat v systému, který by měl jako Partnerská témata.
1. Obraťte se na tým Event Grid a sdělte nám, jak se má stát, že se Váš typ partnerského tématu stane veřejným.

#### <a name="customer-flow"></a>Tok zákazníka

1. Váš zákazník navštíví Azure Portal a poznamenejte si ID předplatného Azure a skupinu prostředků, které by chtěli jako partnerské téma vytvořené v.
1. Zákazník si vyžádá Partnerská témata prostřednictvím vašeho systému. V reakci vytvoříte tunelové propojení událostí pro váš partnerský obor názvů.
1. Event Grid vytvoří **nedokončené** partnerské téma v předplatném Azure a skupině prostředků zákazníka.

    ![Vytvoření kanálu událostí](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Zákazník aktivuje téma partnera prostřednictvím Azure Portal. Události teď můžou z vaší služby přesměrovat do předplatného Azure zákazníka.

    ![Aktivovat téma partnera](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Model prostředků

Níže je uvedený model prostředků pro témata o partnerovi.

### <a name="partner-registrations"></a>Registrace partnerů
* Partner`partnerRegistrations`
* Používá: partneři
* Popis: zachycuje globální metadata partnera SaaS (například název, zobrazovaný název, popis, identifikátor URI nastavení).
    
    Vytvoření nebo aktualizace registrace partnera je samoobslužná operace pro partnery. Tato možnost samoobslužné funkce umožňuje partnerům sestavit a otestovat kompletní tok na konci.
    
    Zákazníci můžou zjistit jenom partnerRegistrations schválené společností Microsoft.
* Obor: vytvořeno v rámci předplatného Azure partnera. Metadata viditelná pro zákazníky po veřejném.

### <a name="partner-namespaces"></a>Obory názvů partnerů
* Prostředek: partnerNamespaces
* Používá: partneři
* Popis: poskytuje regionální prostředek pro publikování událostí zákazníků. Každý partnerský obor názvů má koncový bod publikování a ověřovací klíče. Obor názvů je také způsob, jakým partner požaduje partnerské téma pro daného zákazníka a uvádí seznam aktivních zákazníků.
* Obor: životnost v rámci předplatného partnera.

### <a name="event-channel"></a>Kanál událostí
* Partner`partnerNamespaces/eventChannels`
* Používá: partneři
* Popis: tunely událostí jsou zrcadlem k tématu partnera zákazníka. Vytvořením tunelového propojení událostí a zadáním předplatného Azure a skupiny prostředků v metadatech budete signalizovat, Event Grid vytvořit partnerské téma pro zákazníka. Event Grid vydá volání ARM k vytvoření odpovídajícího partnerTopic v předplatném zákazníka. Téma partner se vytvoří ve stavu čeká na vyřízení. Mezi jednotlivými eventTunnel a partnerTopic se vytvoří propojení 1-1.
* Obor: životnost v rámci předplatného partnera.

### <a name="partner-topics"></a>Témata partnerů
* Partner`partnerTopics`
* Používá: zákazníci
* Popis: témata partnerů jsou podobná vlastnímu tématu a systémovému tématu v Event Grid. Každé partnerské téma je přidruženo ke konkrétnímu zdroji (například `Contoso:myaccount` ) a určitému typu tématu partnera (například contoso). Zákazníci vytvářejí odběry událostí v tématu partnera, aby mohli směrovat události do různých obslužných rutin událostí.

    Zákazníci nemůžou vytvořit tento prostředek přímo. Jediným způsobem, jak vytvořit partnerské téma, je prostřednictvím partnerské operace vytvářející tunelové propojení událostí.
* Rozsah: žije v předplatném zákazníka.

### <a name="partner-topic-types"></a>Typy partnerských témat
* Partner`partnerTopicTypes`
* Používá: zákazníci
* Popis: typy partnerských témat jsou typy prostředků v rámci tenanta, které zákazníkům umožňují zjistit seznam schválených typů partnerských témat. Adresa URL bude vypadat takto:https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Rozsah: globální

## <a name="publishing-events-to-event-grid"></a>Publikování událostí do Event Grid
Při vytváření partnerNamespace v oblasti Azure získáte místní koncový bod a odpovídající ověřovací klíče. Umožňuje publikovat dávky událostí do tohoto koncového bodu pro všechny tunely událostí zákazníka v tomto oboru názvů. V závislosti na poli "zdroj" v události bude Azure Event Grid namapovat každou událost pomocí odpovídajících partnerských témat.

### <a name="event-schema-cloudevents-v10"></a>Schéma události: CloudEvents v 1.0
Publikování událostí pro Azure Event Grid pomocí schématu CloudEvents 1,0. Event Grid podporuje jak strukturovaný, tak i dávkový režim. CloudEvents 1,0 je jediné podporované schéma událostí pro obory názvů partnerů.

### <a name="example-flow"></a>Ukázkový tok

1.  Služba publikování provede příspěvek HTTP na `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .
2.  V žádosti zahrňte hodnotu hlavičky s názvem AEG-SAS-Key obsahující klíč pro ověření. Tento klíč se zřídí během vytváření partnerNamespace. Platná hodnota hlavičky je například AEG-SAS-Key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg = =.
3.  Nastavte hlavičku Content-Type na "Application/cloudevents-Batch + JSON; charset = UTF-8.
4.  Proveďte příspěvek HTTP na výše uvedenou adresu URL pro publikování s dávkou událostí odpovídajících této oblasti. Příklad:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

Po odeslání do koncového bodu partnerNamespace obdržíte odpověď. Odpověď je standardní kód odpovědi HTTP. Mezi běžné odezvy patří:

| Výsledek                             | Odpověď              |
|------------------------------------|-----------------------|
| Úspěch                            | 200 OK                |
| Data události mají nesprávný formát.    | 400 Chybný požadavek       |
| Neplatný přístupový klíč                 | 401 Neautorizováno      |
| Nesprávný koncový bod                 | 404 Nenalezeno         |
| Pole nebo událost překračuje omezení velikosti. | datová část 413 je moc velká. |

## <a name="reference"></a>Referenční informace

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Šablona ARM](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)
  * [Schéma šablony ARM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [Rozhraní REST API](https://docs.microsoft.com/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [Rozšíření CLI](https://docs.microsoft.com/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>Sady SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Přejít](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Další kroky
- [Témata o partnerech – přehled](partner-topics-overview.md)
- [Formulář pro témata týkající se připojování k partnerům](https://aka.ms/gridpartnerform)
- [Téma Auth0 partner](auth0-overview.md)
- [Jak používat téma Auth0 partner](auth0-how-to.md)

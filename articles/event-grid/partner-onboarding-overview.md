---
title: Přehled připojování partnerů (Azure Event Grid)
description: Poskytuje přehled o tom, jak se dá připojit jako partner Event Grid.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 2a2e33395cabd368d5d5d870dd0461e4cbd37e0d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081191"
---
# <a name="partner-onboarding-overview-azure-event-grid"></a>Přehled připojování partnerů (Azure Event Grid)

Tento článek popisuje, jak soukromě používat Azure Event Grid prostředky partnerů a jak se stát veřejně dostupným typem partnerského tématu.

Nepotřebujete zvláštní oprávnění, abyste mohli začít používat Event Grid typy prostředků přidružené k událostem publikování jako partner Event Grid. Ve skutečnosti je můžete použít dnes k publikování událostí soukromě do vlastních předplatných Azure a k otestování modelu prostředků, Pokud zvažujete, že se stanete partnerem.

> [!NOTE]
> Podrobné pokyny týkající se toho, jak se připojit jako Event Grid partner pomocí Azure Portal, najdete v tématu [jak se připojit jako Event Grid partner (Azure Portal)](onboard-partner.md). 

## <a name="how-partner-events-work"></a>Jak fungují partnerské události
Funkce partnerských událostí převezme existující architekturu, kterou Event Grid už používá k publikování událostí z prostředků Azure, jako jsou Azure Storage a Azure IoT Hub, a zpřístupňuje tyto nástroje všem uživatelům, kteří je používají. Používání těchto nástrojů je standardně soukromé jenom pro vaše předplatné Azure. Pokud chcete, aby byly události veřejně dostupné, vyplňte formulář a [kontaktujte tým Event Grid](mailto:gridpartner@microsoft.com).

Funkce partnerských událostí umožňuje publikovat události pro Azure Event Grid pro víceklientské využití.

## <a name="onboarding-and-event-publishing-overview"></a>Přehled zprovoznění a publikování událostí

### <a name="partner-flow"></a>Partnerský tok

1. Vytvořte tenanta Azure, pokud ho ještě nemáte.
1. Pomocí rozhraní příkazového řádku Azure vytvořte novou Event Grid `partnerRegistration` . Tento prostředek obsahuje informace, jako je zobrazované jméno, popis, identifikátor URI instalace atd.

    ![Vytvoření tématu partnera](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Vytvořte jeden nebo více oborů názvů partnera v každé oblasti, kde chcete publikovat události. Služba Event Grid zřídí koncový bod publikování (například `https://contoso.westus-1.eventgrid.azure.net/api/events` ) a přístupové klíče.

    ![Vytvoření oboru názvů partnera](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Poskytněte zákazníkům možnost zaregistrovat se do vašeho systému, který chce o partnerovi.
1. Obraťte se na tým Event Grid a sdělte mu, že chcete, aby se Váš typ partnerského tématu stal veřejným.

### <a name="customer-flow"></a>Tok zákazníka

1. Zákazník navštíví Azure Portal a poznamenejte si ID předplatného Azure a skupinu prostředků, ve kterých se má partnerské téma vytvořit.
1. Zákazník požádá o partnerské téma prostřednictvím vašeho systému. V reakci vytvoříte tunelové propojení událostí pro svůj partnerský obor názvů.
1. Event Grid vytvoří **nedokončené** partnerské téma v předplatném Azure a skupině prostředků zákazníka.

    ![Vytvoření kanálu událostí](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Zákazník aktivuje téma partnera prostřednictvím Azure Portal. Události teď můžou z vaší služby přesměrovat do předplatného Azure zákazníka.

    ![Aktivovat téma partnera](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Model prostředků
Následující model prostředků je pro partnerské události.

### <a name="partner-registrations"></a>Registrace partnerů
* Partner `partnerRegistrations`
* Používá: partneři
* Popis: zachycuje globální metadata partnera software jako služby (SaaS) (například název, zobrazovaný název, popis, identifikátor URI nastavení).
    
    Vytvoření nebo aktualizace registrace partnera je samoobslužná operace pro partnery. Tato možnost samoobslužné funkce umožňuje partnerům sestavit a otestovat kompletní tok.
    
    Zákazníci můžou zjistit jenom registraci partnerů schválenou společností Microsoft.
* Obor: vytvořeno v rámci předplatného Azure partnera. Po zveřejnění jsou metadata viditelná pro zákazníky.

### <a name="partner-namespaces"></a>Obory názvů partnerů
* Partner `partnerNamespaces`
* Používá: partneři
* Popis: poskytuje regionální prostředek pro publikování událostí zákazníků. Každý partnerský obor názvů má koncový bod publikování a ověřovací klíče. Obor názvů je také způsob, jakým partner požaduje partnerské téma pro daného zákazníka a uvádí seznam aktivních zákazníků.
* Rozsah: žije v předplatném partnera.

### <a name="event-channel"></a>Kanál událostí
* Partner `partnerNamespaces/eventChannels`
* Používá: partneři
* Popis: kanály událostí jsou zrcadlem k tématu partnera zákazníka. Když vytvoříte kanál událostí a zadáte předplatné Azure a skupinu prostředků v metadatech, budete signálem Event Grid vytvořit partnerské téma pro zákazníka. Event Grid vydá Azure Resource Manager voláním k vytvoření odpovídajícího partnerského tématu v předplatném zákazníka. Téma partnerského serveru je vytvořeno ve stavu čeká na vyřízení. Mezi jednotlivými kanály událostí a partnerem můžete propojit 1:1.
* Rozsah: žije v předplatném partnera.

### <a name="partner-topics"></a>Témata pro partnery
* Partner `partnerTopics`
* Používá: zákazníci
* Popis: témata partnerů jsou podobná vlastním tématům a tématům systému v Event Grid. Každé téma partnera je přidruženo ke konkrétnímu zdroji (například `Contoso:myaccount` ) a určitému typu tématu partnera (například contoso). Zákazníci vytvářejí odběry událostí v tématu partnera, aby mohli směrovat události do různých obslužných rutin událostí.

    Zákazníci nemůžou vytvořit tento prostředek přímo. Jediným způsobem, jak vytvořit partnerské téma, je prostřednictvím partnerské operace, která vytváří kanál událostí.
* Rozsah: žije v předplatném zákazníka.

### <a name="partner-topic-types"></a>Typy partnerských témat
* Partner `partnerTopicTypes`
* Používá: zákazníci
* Popis: typy partnerských témat jsou typy prostředků v rámci tenanta, které zákazníkům umožňují zjistit seznam schválených typů partnerských témat. Adresa URL vypadá takto. https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Rozsah: globální

## <a name="publish-events-to-event-grid"></a>Publikovat události pro Event Grid
Když vytvoříte obor názvů partnera v oblasti Azure, získáte místní koncový bod a odpovídající klíče ověřování. Umožňuje publikovat dávky událostí do tohoto koncového bodu pro všechny kanály událostí zákazníka v daném oboru názvů. Na základě zdrojového pole v události Azure Event Grid mapuje každou událost s odpovídajícími tématy o partnerovi.

### <a name="event-schema-cloudevents-v10"></a>Schéma události: CloudEvents v 1.0
Publikujte události do Azure Event Grid pomocí schématu CloudEvents 1,0. Event Grid podporuje jak strukturovaný, tak i dávkový režim. CloudEvents 1,0 je jediné podporované schéma událostí pro obory názvů partnerů.

### <a name="example-flow"></a>Ukázkový tok

1.  Služba publikování provede příspěvek HTTP na `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .
1.  V žádosti zahrňte hodnotu hlavičky s názvem AEG-SAS-Key obsahující klíč pro ověření. Tento klíč se zřídí během vytváření oboru názvů partnera. Platná hodnota hlavičky je například AEG-SAS-Key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg = =.
1.  Nastavte hlavičku Content-Type na "Application/cloudevents-Batch + JSON; charset = UTF-8a.
1.  Spusťte dotaz HTTP POST na adresu URL pro publikování s dávkou událostí, které odpovídají dané oblasti. Například:

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

Po odeslání do koncového bodu oboru názvů partnera obdržíte odpověď. Odpověď je standardní kód odpovědi HTTP. Mezi běžné odezvy patří:

| Výsledek                             | Odpověď              |
|------------------------------------|-----------------------|
| Success                            | 200 OK                |
| Data události mají nesprávný formát.    | 400 – Chybný požadavek       |
| Neplatný přístupový klíč                 | 401 – Neautorizováno      |
| Nesprávný koncový bod                 | 404 Nenalezeno         |
| Pole nebo událost překračuje omezení velikosti. | datová část 413 je moc velká. |

## <a name="references"></a>Reference

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Šablona ARM](/azure/templates/microsoft.eventgrid/allversions)
  * [Schéma šablony ARM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [REST API](/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [Rozšíření CLI](/cli/azure/ext/eventgrid/)

### <a name="sdks"></a>Sady SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Přejít](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Další kroky
- [Témata o partnerech – přehled](partner-events-overview.md)
- [Formulář pro témata týkající se připojování k partnerům](https://aka.ms/gridpartnerform)
- [Téma Auth0 partner](auth0-overview.md)
- [Jak používat téma Auth0 partner](auth0-how-to.md)

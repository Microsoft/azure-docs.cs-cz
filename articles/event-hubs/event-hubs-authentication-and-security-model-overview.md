---
title: Model ověřování a zabezpečení – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek popisuje model ověřování a zabezpečení služby Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 6f4abd9f826864914abee0b5d513d5b1c530d416
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104148"
---
# <a name="azure-event-hubs---authentication-and-security-model"></a>Azure Event Hubs – ověřování a modelu zabezpečení

Model zabezpečení služby Azure Event Hubs splňuje následující požadavky:

* Pouze klienti, které představují platné přihlašovací údaje může odesílat data do centra událostí.
* Klient nemůže zosobnit jiného klienta.
* Podvodného klienta můžete blokovat odesílat data do centra událostí.

## <a name="client-authentication"></a>Ověření klienta

Model zabezpečení služby Event Hubs je založen na kombinaci [sdíleného přístupového podpisu (SAS)](../service-bus-messaging/service-bus-sas.md) tokeny a *zdroje událostí*. Na vydavatele událostí definuje virtuální koncový bod pro v Centru událostí. Vydavatel jde použít jenom pro odesílání zpráv do centra událostí. Není možné pro příjem zpráv z vydavatele.

Centra událostí obvykle používá jeden vydavatele za klienta. Všechny zprávy, které se odesílají do všech vydavatelů centra událostí jsou zařazených do fronty v rámci tohoto centra událostí. Vydavatelé povolit jemně odstupňované řízení přístupu a omezení šířky pásma.

Každý klient služby Event Hubs je přiřazen jedinečný token, který se nahraje do klienta. Tokeny jsou vytvořené tak, aby každý jedinečný token uděluje přístup do jiného vydavatele jedinečný. Klient, který se má token lze odeslat pouze jeden vydavatele, ale žádný vydavatel. Pokud více klientů sdílí stejný token, každý z nich sdílí vydavatele.

Však není doporučena, je možné z zařízení s tokeny, které udělit přímý přístup do centra událostí. Jakékoli zařízení, které obsahuje tento token může odesílat zprávy přímo do tohoto centra událostí. Tato zařízení nebude vztahovat omezení šířky pásma. Kromě toho zařízení nemůže být na seznamu zakázaných adres v odesílání do tohoto centra událostí.

Všechny tokeny jsou podepsány pomocí klíče SAS. Obvykle všechny tokeny jsou podepsány pomocí stejného klíče. Klienti nemají klíče; To zabrání dalším klientům výrobní tokeny.

### <a name="create-the-sas-key"></a>Vytvoření klíče SAS

Při vytváření oboru názvů Event Hubs, služba automaticky vygeneruje 256bitový SAS klíč s názvem **RootManageSharedAccessKey**. Toto pravidlo obsahuje přidruženým párem primárního a sekundárního klíče, které udělují odeslat, naslouchat a spravovat práva k oboru názvů. Můžete také vytvořit další klíče. Doporučuje se na základě klíče, že uděluje oprávnění Odeslat do konkrétní event hub. Pro zbývající část tohoto tématu, se předpokládá, že jste pojmenovali, aby tento klíč **EventHubSendKey**.

Následující příklad vytvoří klíče jen pro odesílání, při vytváření centra událostí:

```csharp
// Create namespace manager.
string serviceNamespace = "YOUR_NAMESPACE";
string namespaceManageKeyName = "RootManageSharedAccessKey";
string namespaceManageKey = "YOUR_ROOT_MANAGE_SHARED_ACCESS_KEY";
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, string.Empty);
TokenProvider td = TokenProvider.CreateSharedAccessSignatureTokenProvider(namespaceManageKeyName, namespaceManageKey);
NamespaceManager nm = new NamespaceManager(namespaceUri, namespaceManageTokenProvider);

// Create event hub with a SAS rule that enables sending to that event hub
EventHubDescription ed = new EventHubDescription("MY_EVENT_HUB") { PartitionCount = 32 };
string eventHubSendKeyName = "EventHubSendKey";
string eventHubSendKey = SharedAccessAuthorizationRule.GenerateRandomKey();
SharedAccessAuthorizationRule eventHubSendRule = new SharedAccessAuthorizationRule(eventHubSendKeyName, eventHubSendKey, new[] { AccessRights.Send });
ed.Authorization.Add(eventHubSendRule); 
nm.CreateEventHub(ed);
```

### <a name="generate-tokens"></a>Generování tokenů

Můžete generovat tokeny SAS klíč. Musíte vytvořit pouze jeden token za klienta. Tokeny je pak možné vytvořit pomocí následující metody. Všechny tokeny jsou generovány pomocí **EventHubSendKey** klíč. Každý token je přiřazen jedinečný identifikátor URI.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Při volání této metody, identifikátor URI by měl být zadán jako `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Pro všechny tokeny, identifikátor URI je shodné, s výjimkou produktů `PUBLISHER_NAME`, který by měl být různé pro každý token. V ideálním případě `PUBLISHER_NAME` představuje ID klienta, která bude přijímat tento token.

Tato metoda generuje token s následující strukturou:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

Během několika sekund od 1. ledna 1970 je zadaný čas vypršení platnosti tokenu. Následuje příklad tokenu:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Obvykle tokeny mají životnost, který se podobá nebo překračuje životnost klienta. Pokud má klient umožňuje získat nový token, je použít tokeny s kratší životnost.

### <a name="sending-data"></a>Odesílání dat

Po vytvoření tokeny každého klienta se služnou svůj vlastní jedinečný token.

Když klient odešle data do centra událostí, značky jeho odeslání žádosti s tokenem. Tak případnému útočníkovi odposlouchávání a krást token, musí probíhat komunikace mezi klientem a centra událostí na základě šifrovaný kanál.

### <a name="blacklisting-clients"></a>Blokované klienty

Pokud se vám ho někdo ukradne token ze strany útočníka, může útočník zosobnit klienta odcizen jehož token. Seznamů zakázaných klienta vykreslí tohoto klienta nepoužitelné až do obdržení nového tokenu, který používá jiného vydavatele.

## <a name="authentication-of-back-end-applications"></a>Ověřování back endovým aplikacím

K ověření back endovým aplikacím, které využívají data generovaná klientů služby Event Hubs, služby Event Hubs využívá model zabezpečení, který je podobný modelu, který se používá pro témata služby Service Bus. Skupiny uživatelů služby Event Hubs je ekvivalentní k přihlášení k odběru tématu služby Service Bus. Klienta můžete vytvořit skupinu uživatelů, pokud požadavek na vytvoření skupiny příjemců je doplněny tokenem, spravovat uděluje oprávnění pro Centrum událostí, nebo pro obor názvů, do které patří centra událostí. Klient může zpracovat data ze skupiny příjemců, pokud je požadavek na přijetí doplněny tokenem, který uděluje práva příjmu na této skupiny příjemců centra událostí a obor názvů, do které patří centra událostí.

Aktuální verze služby Service Bus nepodporuje SAS pravidla pro jednotlivá předplatná. To samé platí pro skupiny uživatelů služby Event Hubs. SAS podpora bude přidána v budoucnosti pro obě funkce.

Chybí SAS ověřování pro jednotlivé příjemce skupiny můžete použít klíče SAS pro všechny skupiny uživatelů zabezpečení pomocí společný klíč. Díky tomu aplikace využívají data z jakékoli skupiny uživatelů centra událostí.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs, najdete v následujících tématech:

* [Přehled služby Event Hubs]
* [Přehled sdílených přístupových podpisů]
* [Ukázkové aplikace, které používají službu Event Hubs]

[Přehled služby Event Hubs]: event-hubs-what-is-event-hubs.md
[Ukázkové aplikace, které používají službu Event Hubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Přehled sdílených přístupových podpisů]: ../service-bus-messaging/service-bus-sas.md


---
title: Přehled modelu zabezpečení a ověřování Azure Event Hubs | Microsoft Docs
description: Ověřování a zabezpečení modelu přehled služby Event Hubs.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 93841e30-0c5c-4719-9dc1-57a4814342e7
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/30/2018
ms.author: sethm
ms.openlocfilehash: 5264930dcb802c2a58abc179bdd0041acc9f58d0
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2018
ms.locfileid: "32311365"
---
# <a name="event-hubs-authentication-and-security-model-overview"></a>Ověřování a zabezpečení modelu přehled služby Event Hubs

Model zabezpečení služby Azure Event Hubs splňuje následující požadavky:

* Pouze klienti, kteří platné přihlašovací údaje k dispozici může odesílat data do centra událostí.
* Klient nemůže zosobnit jiného klienta.
* Podvodného klienta můžete blokovat odesílání dat do centra událostí.

## <a name="client-authentication"></a>Ověření klienta

Model zabezpečení služby Event Hubs je založen na kombinaci [sdíleného přístupového podpisu (SAS)](../service-bus-messaging/service-bus-sas.md) tokeny a *zdroje událostí*. Vydavatel události definuje virtuální koncový bod pro centra událostí. Vydavatel slouží jenom k odesílání zpráv do centra událostí. Není možné přijímat zprávy od vydavatele.

Centra událostí obvykle aktivuje jeden vydavatele za klienta. Všechny zprávy, které se odesílají do jakéhokoli z daných vydavatelů centra událostí jsou zařazených do fronty v rámci tohoto centra událostí. Vydavatelé povolte řízení podrobných přístupu a omezení.

Každý klient služby Event Hubs je přiřazen jedinečný token, který je nahrán do klienta. Tokeny vznikají tak, aby každý jedinečný token uděluje přístup na jiný jedinečný vydavatele. Klient, který má token lze odeslat pouze jeden vydavatele, ale žádný další vydavatel. Pokud více klientů sdílí stejný token, každý z nich sdílí vydavatel.

I když není doporučeno, je možné vybavit zařízení s tokeny, které udělit přímý přístup do centra událostí. Zprávy můžete přímo do tohoto centra událostí posílat jakékoli zařízení, která obsahuje tento token. Tato zařízení nejsou předmětem omezení. Kromě toho zařízení nemůže být zakázáno v odesílání do tohoto centra událostí.

Všechny tokeny jsou podepsané klíče SAS. Obvykle jsou všechny tokeny podepsané stejným klíčem. Klienti nemají informace o klíči; To zabrání tomu, aby ostatní klienty výrobní tokeny.

### <a name="create-the-sas-key"></a>Vytvoření klíče SAS

Při vytváření na obor názvů služby Event Hubs, služba automaticky vygeneruje SAS klíč 256 bitů s názvem **RootManageSharedAccessKey**. Toto pravidlo má k přidružené dvojice primární a sekundární klíče, která udělují odesílání, naslouchat a spravovat práva k oboru názvů. Můžete také vytvořit další klíče. Doporučuje se, že můžete vytvořit klíče, odeslat uděluje oprávnění k centru konkrétní události. Pro zbývající část tohoto tématu, předpokládá se, tento klíč s názvem **EventHubSendKey**.

Následující příklad vytvoří jen odesílání klíč při vytváření centra událostí:

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

Můžete vygenerovat tokeny pomocí klíče SAS. Musíte vytvořit jenom jeden token za klienta. Tokeny je pak možné vytvořit pomocí následující metodu. Všechny tokeny jsou generovány pomocí **EventHubSendKey** klíč. Každý token je přiřazen jedinečný identifikátor URI.

```csharp
public static string SharedAccessSignatureTokenProvider.GetSharedAccessSignature(string keyName, string sharedAccessKey, string resource, TimeSpan tokenTimeToLive)
```

Při volání této metody, identifikátor URI musí být zadány ve tvaru `//<NAMESPACE>.servicebus.windows.net/<EVENT_HUB_NAME>/publishers/<PUBLISHER_NAME>`. Pro všechny tokeny, identifikátor URI je identických, s výjimkou produktů `PUBLISHER_NAME`, což by mělo být různé pro každý token. V ideálním případě `PUBLISHER_NAME` reprezentuje ID klienta, který přijme tento token.

Tato metoda generuje token s následující strukturou:

```csharp
SharedAccessSignature sr={URI}&sig={HMAC_SHA256_SIGNATURE}&se={EXPIRATION_TIME}&skn={KEY_NAME}
```

V sekundách od 1. ledna 1970 je zadaný čas vypršení platnosti tokenu. Následuje příklad token:

```csharp
SharedAccessSignature sr=contoso&sig=nPzdNN%2Gli0ifrfJwaK4mkK0RqAB%2byJUlt%2bGFmBHG77A%3d&se=1403130337&skn=RootManageSharedAccessKey
```

Obvykle tokeny mají životnost, která se podobá nebo překračuje životnost klienta. Pokud má klient schopnost získat nový token, můžete použít tokeny s kratší životnost.

### <a name="sending-data"></a>Odeslání dat

Po vytvoření tokenů jednotlivých klientů je opatřen svůj vlastní jedinečný token.

Když klient odešle data do centra událostí, značky jeho odeslán požadavek pomocí tokenu. Aby zabránil útočníkovi z odposlouchávání a krádež token, musí dojít k komunikace mezi klientem a centra událostí přes šifrovaný kanál.

### <a name="blacklisting-clients"></a>Blokované klienty

V případě krádeže token uživatelem se zlými úmysly může útočník zosobnit klienta odcizen jejichž token. Seznamů zakázaných klienta vykreslí tohoto klienta nepoužitelný, dokud neobdrží nový token, který používá jiný vydavatel.

## <a name="authentication-of-back-end-applications"></a>Ověřování back-end aplikace

K ověření back-end aplikace, které využívají data generována klientů služby Event Hubs, Event Hubs využívá model zabezpečení, který je podobný modelu, který se používá pro témata Service Bus. Skupiny uživatelů služby Event Hubs je ekvivalentní k odběru do tématu Service Bus. Klienta můžete vytvořit skupiny příjemců, pokud požadavek na vytvoření skupiny uživatelů je doplněny tokenem, spravujete uděluje oprávnění pro centra událostí, nebo pro obor názvů, do které patří centra událostí. Klient může využívat data ze skupiny příjemců, pokud je požadavek na přijetí spolu s token, který uděluje práva receive na této skupiny příjemců, centra událostí nebo oboru názvů, do které patří centra událostí.

Aktuální verze služby Service Bus nepodporuje SAS pravidla pro jednotlivé odběry. To samé platí pro skupiny uživatelů služby Event Hubs. Podpora SAS se přidá pro obě funkce v budoucnu.

Chybí ověřování SAS pro jednotlivé příjemce skupiny můžete zabezpečit všechny skupiny příjemců běžné klíčem klíče SAS. Tento přístup umožňuje aplikaci využívat data ze všech skupin uživatelů centra událostí.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs naleznete v následujících tématech:

* [Přehled služby Event Hubs]
* [Přehled sdílených přístupových podpisů]
* [Ukázkové aplikace, které používají službu Event Hubs]

[Přehled služby Event Hubs]: event-hubs-what-is-event-hubs.md
[Ukázkové aplikace, které používají službu Event Hubs]: https://github.com/Azure/azure-event-hubs/tree/master/samples
[Přehled sdílených přístupových podpisů]: ../service-bus-messaging/service-bus-sas.md


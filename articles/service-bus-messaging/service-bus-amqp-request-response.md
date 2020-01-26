---
title: Operace požadavku nebo odpovědi AMQP 1,0 v Azure Service Bus
description: Tento článek definuje seznam AMQP operací na základě požadavků a odpovědí v Microsoft Azure Service Bus.
services: service-bus-messaging
documentationcenter: na
author: axisc
editor: spelluru
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: aschhab
ms.openlocfilehash: 338b411eade35fa357f64131c64b8ef2034c92b0
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761079"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1,0 v Microsoft Azure Service Bus: operace založené na odezvě na základě požadavků

Tento článek definuje seznam Microsoft Azure Service Bus operací na základě požadavků a odpovědí. Tyto informace vycházejí z pracovní konceptu AMQP Management verze 1,0.  
  
Podrobné úroveň protokolu AMQP 1.0 protokol průvodce, která vysvětluje, jak Service Bus implementuje a je založený na technické specifikace OASIS AMQP, najdete v článku [protokolu AMQP 1.0 v příručce protokol Azure Service Bus a Event Hubs][průvodce protokolem amqp 1.0].  
  
## <a name="concepts"></a>Koncepty  
  
### <a name="entity-description"></a>Popis entity  

Popis entity odkazuje buď na Service Bus [třídy QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription), třídy [TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription), nebo na objekt [třídy SubscriptionDescription](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription) .  
  
### <a name="brokered-message"></a>Zprostředkovaná zpráva  

Představuje zprávu v Service Bus, která je namapována na zprávu AMQP. Mapování je definováno v [Průvodci protokolem Service Bus AMQP](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Připojit k uzlu správy entit  

Všechny operace popsané v tomto dokumentu následují po vzorcích požadavků a odpovědí, které jsou vymezeny na entitu a vyžadují připojení k uzlu správy entit.  
  
### <a name="create-link-for-sending-requests"></a>Vytvořit odkaz pro odesílání žádostí  

Vytvoří odkaz na uzel správy pro odesílání požadavků.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Vytvořit odkaz pro příjem odpovědí  

Vytvoří odkaz pro příjem odpovědí z uzlu pro správu.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Přenést zprávu požadavku  

Převede zprávu požadavku.  
Stav transakce lze přidat volitelně pro operace, které podporují transakci.

```
requestLink.sendTransfer(
        Message(
                properties: {
                        message-id: <request id>,
                        reply-to: "<my response link unique address>"
                },
                application-properties: {
                        "operation" -> "<operation>",
                }
        ),
        [Optional] State = transactional-state: {
                txn-id: <txn-id>
        }
)
```
  
### <a name="receive-a-response-message"></a>Přijmout zprávu odpovědi  

Přijme zprávu odpovědi z odkazu na odpověď.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Zpráva odpovědi je v následujícím tvaru:
  
```
Message(
properties: {
        correlation-id: <request id>
    },
    application-properties: {
            "statusCode" -> <status code>,
            "statusDescription" -> <status description>,
           },
)

```
  
### <a name="service-bus-entity-address"></a>Adresa entity Service Bus  

Service Bus entit musí být řešeny následujícím způsobem:  
  
|Typ entity|Adresa|Příklad:|  
|-----------------|-------------|-------------|  
|fronta|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|téma|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|předplatné|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operace zpráv  
  
### <a name="message-renew-lock"></a>Zámek obnovení zprávy  

Prodlouží zámek zprávy o čas zadaný v popisu entity.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
 Tělo zprávy požadavku se musí skládat z části AMQP-Value obsahující mapu s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|pole UUID|Ano|Tokeny zámku zprávy pro obnovení.|  

> [!NOTE]
> Tokeny zámku jsou vlastností `DeliveryTag` u přijatých zpráv. Podívejte se na následující příklad v sadě [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) , který je načte. Token se může také zobrazit v ' DeliveryAnnotations ' jako ' x-opt-Lock-token ', ale není zaručený a `DeliveryTag` by měl být upřednostňovaný. 
> 
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak selhal.|  
|statusDescription|string|Ne|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části AMQP-Value obsahující mapu s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|vypršení platnosti|pole časového razítka|Ano|Token zámku zprávy nové vypršení platnosti odpovídající tokenům zámků žádosti|  
  
### <a name="peek-message"></a>Prohlížet zprávu  

Prohlédne zprávy bez zamykání.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|Long|Ano|Pořadové číslo, ze kterého se má začít prohlížet náhled|  
|`message-count`|int|Ano|Maximální počet zpráv, které se mají prohlížet|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – obsahuje více zpráv<br /><br /> 204: žádný obsah – žádné další zprávy|  
|statusDescription|string|Ne|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Ano|Seznam zpráv, ve kterých každá mapa představuje zprávu|  
  
Mapa, která představuje zprávu, musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|zpráva|pole bajtů|Ano|AMQP 1,0 – zpráva kódovaná pomocí drátu.|  
  
### <a name="schedule-message"></a>Naplánovat zprávu  

Naplánuje zprávy. Tato operace podporuje transakci.
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Ano|Seznam zpráv, ve kterých každá mapa představuje zprávu|  
  
Mapa, která představuje zprávu, musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|message-id|string|Ano|`amqpMessage.Properties.MessageId` jako řetězec|  
|ID relace|string|Ne|`amqpMessage.Properties.GroupId as string`|  
|Partition – klíč|string|Ne|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|pomocí klíčového oddílu|string|Ne|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|zpráva|pole bajtů|Ano|AMQP 1,0 – zpráva kódovaná pomocí drátu.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak selhal.|  
|statusDescription|string|Ne|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující mapu s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouhé|Ano|Pořadové číslo plánovaných zpráv Pořadové číslo se používá ke zrušení.|  
  
### <a name="cancel-scheduled-message"></a>Zrušit naplánovanou zprávu  

Zruší naplánované zprávy.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouhé|Ano|Pořadová čísla plánovaných zpráv, které mají být zrušeny.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak selhal.|  
|statusDescription|string|Ne|Popis stavu|   
  
## <a name="session-operations"></a>Operace relací  
  
### <a name="session-renew-lock"></a>Zámek obnovení relace  

Prodlouží zámek zprávy o čas zadaný v popisu entity.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|ID relace|string|Ano|ID relace|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – obsahuje více zpráv<br /><br /> 204: žádný obsah – žádné další zprávy|  
|statusDescription|string|Ne|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující mapu s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Vypršení platnosti|časové razítko|Ano|Nové vypršení platnosti.|  
  
### <a name="peek-session-message"></a>Náhled zprávy relace  

Prohlédne zprávy relace bez uzamčení.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|z-Sequence-Number|Long|Ano|Pořadové číslo, ze kterého se má začít prohlížet náhled|  
|počet zpráv|int|Ano|Maximální počet zpráv, které se mají prohlížet|  
|ID relace|string|Ano|ID relace|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – obsahuje více zpráv<br /><br /> 204: žádný obsah – žádné další zprávy|  
|statusDescription|string|Ne|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující mapu s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Ano|Seznam zpráv, ve kterých každá mapa představuje zprávu|  
  
 Mapa, která představuje zprávu, musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|zpráva|pole bajtů|Ano|AMQP 1,0 – zpráva kódovaná pomocí drátu.|  
  
### <a name="set-session-state"></a>Nastavit stav relace  

Nastaví stav relace.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|ID relace|string|Ano|ID relace|  
|stav relace|pole bajtů|Ano|Neprůhledná binární data.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|string|Ne|Popis stavu|  
  
### <a name="get-session-state"></a>Získat stav relace  

Získá stav relace.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|ID relace|string|Ano|ID relace|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|string|Ne|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|stav relace|pole bajtů|Ano|Neprůhledná binární data.|  
  
### <a name="enumerate-sessions"></a>Zobrazení výčtu relací  

Vytvoří výčet relací v entitě zasílání zpráv.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|last-updated-time|časové razítko|Ano|Filtr tak, aby zahrnoval pouze relace aktualizované po daném čase.|  
|přímo|int|Ano|Přeskočit několik relací.|  
|vrchol|int|Ano|Maximální počet relací.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – obsahuje více zpráv<br /><br /> 204: žádný obsah – žádné další zprávy|  
|statusDescription|string|Ne|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|přímo|int|Ano|Počet vynechaných relací, pokud je stavový kód 200.|  
|relace – identifikátory|pole řetězců|Ano|Pole ID relací, pokud je stavový kód 200.|  
  
## <a name="rule-operations"></a>Operace pravidla  
  
### <a name="add-rule"></a>Přidat pravidlo  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pravidlo – název|string|Ano|Název pravidla, včetně názvu předplatného a tématu|  
|pravidlo – popis|map|Ano|Popis pravidla, jak je uvedeno v následující části.|  
  
Mapa **popisu pravidla** musí zahrnovat následující položky, ve kterých se vzájemně vylučují filtry **SQL** a **Filtr korelace** :  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|filtr SQL|map|Ano|`sql-filter`, jak je uvedeno v následující části.|  
|filtr korelace|map|Ano|`correlation-filter`, jak je uvedeno v následující části.|  
|sql-rule-action|map|Ano|`sql-rule-action`, jak je uvedeno v následující části.|  
  
Mapa filtru SQL musí zahrnovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|expression|string|Ano|Výraz filtru SQL|  
  
Mapa **filtru korelace** musí zahrnovat aspoň jednu z následujících položek:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|ID korelace|string|Ne||  
|message-id|string|Ne||  
|na|string|Ne||  
|odpovědět na|string|Ne||  
|label|string|Ne||  
|ID relace|string|Ne||  
|odpovědi na relaci – ID|string|Ne||  
|Typ obsahu|string|Ne||  
|properties|map|Ne|Mapuje se Service Bus [BrokeredMessage. Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
Mapa **akcí SQL-Rule** musí zahrnovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|expression|string|Ano|Výraz akce SQL|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|string|Ne|Popis stavu|  
  
### <a name="remove-rule"></a>Odebrat pravidlo  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pravidlo – název|string|Ano|Název pravidla, včetně názvu předplatného a tématu|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|string|Ne|Popis stavu|  
  
### <a name="get-rules"></a>Získat pravidla

#### <a name="request"></a>Žádost

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:

|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  

Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|vrchol|int|Ano|Počet pravidel, která mají být načtena na stránce.|  
|přímo|int|Ano|Počet pravidel, která se mají přeskočit Definuje počáteční index (+ 1) v seznamu pravidel. | 

#### <a name="response"></a>Odpověď

Zpráva s odpovědí obsahuje následující vlastnosti:

|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|pravidla| pole mapy|Ano|Pole pravidel. Každé pravidlo představuje mapa.|

Každá položka mapování v poli obsahuje následující vlastnosti:

|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pravidlo – popis|pole popsaných objektů|Ano|`com.microsoft:rule-description:list` s popisem 0x0000013700000004 kódu AMQP| 

`com.microsoft.rule-description:list` je pole popsaných objektů. Pole obsahuje následující:

|Index|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
| 0 | pole popsaných objektů | Ano | `filter`, jak je uvedeno níže. |
| 1\. místo | pole popsaných objektů | Ano | `ruleAction`, jak je uvedeno níže. |
| 2 | string | Ano | název pravidla |

`filter` může mít některý z následujících typů:

| Název popisovače | Kód popisovače | Hodnota |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtr SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtr korelace |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Pravdivý filtr představující 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Filtr false představující 1 = 0 |

`com.microsoft:sql-filter:list` je popsané pole, které zahrnuje:

|Index|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
| 0 | string | Ano | Výraz filtru SQL |

`com.microsoft:correlation-filter:list` je popsané pole, které zahrnuje:

|Index (pokud existuje)|Typ hodnoty|Obsah hodnoty|  
|---------|----------------|--------------|
| 0 | string | ID korelace |
| 1\. místo | string | ID zprávy |
| 2 | string | až |
| 3 | string | Odpovědět |
| 4 | string | Štítek |
| 5 | string | ID relace |
| 6 | string | Odpověď na ID relace|
| 7 | string | Typ obsahu |
| 8 | Mapa | Mapa definovaných vlastností aplikace |

`ruleAction` může být jeden z následujících typů:

| Název popisovače | Kód popisovače | Hodnota |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Prázdná akce pravidla – neexistují žádné akce pravidla |
| `com.microsoft:sql-rule-action:list` | 0x0000013700000006 | Akce pravidla SQL |

`com.microsoft:sql-rule-action:list` je pole popsaných objektů, jejichž první položka je řetězec, který obsahuje výraz akce pravidla SQL.

## <a name="deferred-message-operations"></a>Odložené operace zpráv  
  
### <a name="receive-by-sequence-number"></a>Přijmout podle pořadového čísla  

Přijme odložené zprávy podle pořadového čísla.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouhé|Ano|Pořadová čísla.|  
|receiver-settle-mode|ubyte|Ano|Režim **vyrovnání přijímače** zadaný v AMQP Core v 1.0|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|string|Ne|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Ano|Seznam zpráv, kde každá mapa představuje zprávu|  
  
Mapa, která představuje zprávu, musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|zámek tokenu|Uuid|Ano|Token zámku, pokud je `receiver-settle-mode` 1.|  
|zpráva|pole bajtů|Ano|AMQP 1,0 – zpráva kódovaná pomocí drátu.|  
  
### <a name="update-disposition-status"></a>Aktualizovat stav dispozice  

Aktualizuje stav dispozice pro odložené zprávy. Tato operace podporuje transakce.
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|string|Ano|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|dispoziční stav|string|Ano|kompletní<br /><br /> zastaven<br /><br /> rozpuštěn|  
|tokeny Lock|pole UUID|Ano|Tokeny zámku zprávy pro aktualizaci stavu dispozice.|  
|nedoručené zprávy – důvod|string|Ne|Dá se nastavit, pokud je stav dispozice nastavený na **pozastaveno**.|  
|nedoručené zprávy – popis|string|Ne|Dá se nastavit, pokud je stav dispozice nastavený na **pozastaveno**.|  
|properties-to-modify|map|Ne|Seznam Service Bus vlastností zprostředkovaných zpráv, které se mají upravit|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|string|Ne|Popis stavu|

## <a name="next-steps"></a>Další kroky

Další informace o AMQP a Service Bus najdete na následujících odkazech:

* [Přehled Service Bus AMQP]
* [Průvodce protokolem AMQP 1.0]
* [AMQP v Service Bus pro Windows Server]

[Přehled Service Bus AMQP]: service-bus-amqp-overview.md
[Průvodce protokolem AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP v Service Bus pro Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)

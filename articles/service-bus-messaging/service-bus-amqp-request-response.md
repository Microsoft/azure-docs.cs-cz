---
title: Operace požadavku nebo odpovědi AMQP 1,0 v Azure Service Bus
description: Tento článek definuje seznam AMQP operací na základě požadavků a odpovědí v Microsoft Azure Service Bus.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: b845f4086ee1ac4fe868571c1754caf6d29b9021
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88064410"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1,0 v Microsoft Azure Service Bus: operace založené na odezvě na základě požadavků

Tento článek definuje seznam Microsoft Azure Service Bus operací na základě požadavků a odpovědí. Tyto informace vycházejí z pracovní konceptu AMQP Management verze 1,0.  
  
Podrobný průvodce protokolem AMQP 1,0, který vysvětluje, jak Service Bus implementuje a sestavuje v technické specifikaci AMQP OASIS, najdete v tématu [AMQP 1,0 v Azure Service Bus a Event Hubs Průvodce protokolem][AMQP 1,0 Průvodce protokolem].  
  
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
  
|Typ entity|Adresa|Příklad|  
|-----------------|-------------|-------------|  
|fronta|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|téma|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|předplatné|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operace zpráv  
  
### <a name="message-renew-lock"></a>Zámek obnovení zprávy  

Prodlouží zámek zprávy o čas zadaný v popisu entity.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
 Tělo zprávy požadavku se musí skládat z části AMQP-Value obsahující mapu s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|pole UUID|Yes|Tokeny zámku zprávy pro obnovení.|  

> [!NOTE]
> Tokeny zámku jsou `DeliveryTag` vlastností u přijatých zpráv. Podívejte se na následující příklad v sadě [.NET SDK](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) , který je načte. Token se může také zobrazit v ' DeliveryAnnotations ' jako ' x-opt-Lock-token ', ale není zaručený a `DeliveryTag` měl by být upřednostňovaný. 
> 
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak selhal.|  
|statusDescription|řetězec|No|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části AMQP-Value obsahující mapu s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|vypršení platnosti|pole časového razítka|Yes|Token zámku zprávy nové vypršení platnosti odpovídající tokenům zámků žádosti|  
  
### <a name="peek-message"></a>Prohlížet zprávu  

Prohlédne zprávy bez zamykání.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Yes|Pořadové číslo, ze kterého se má začít prohlížet náhled|  
|`message-count`|int|Yes|Maximální počet zpráv, které se mají prohlížet|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – obsahuje více zpráv<br /><br /> 204: žádný obsah – žádné další zprávy|  
|statusDescription|řetězec|No|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Yes|Seznam zpráv, ve kterých každá mapa představuje zprávu|  
  
Mapa, která představuje zprávu, musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|zpráva|pole bajtů|Yes|AMQP 1,0 – zpráva kódovaná pomocí drátu.|  
  
### <a name="schedule-message"></a>Naplánovat zprávu  

Naplánuje zprávy. Tato operace podporuje transakci.
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Yes|Seznam zpráv, ve kterých každá mapa představuje zprávu|  
  
Mapa, která představuje zprávu, musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|ID zprávy|řetězec|Yes|`amqpMessage.Properties.MessageId` jako řetězec|  
|ID relace|řetězec|No|`amqpMessage.Properties.GroupId as string`|  
|Partition – klíč|řetězec|No|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|pomocí klíčového oddílu|řetězec|No|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|zpráva|pole bajtů|Yes|AMQP 1,0 – zpráva kódovaná pomocí drátu.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak selhal.|  
|statusDescription|řetězec|No|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující mapu s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouhé|Yes|Pořadové číslo plánovaných zpráv Pořadové číslo se používá ke zrušení.|  
  
### <a name="cancel-scheduled-message"></a>Zrušit naplánovanou zprávu  

Zruší naplánované zprávy.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouhé|Yes|Pořadová čísla plánovaných zpráv, které mají být zrušeny.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak selhal.|  
|statusDescription|řetězec|No|Popis stavu|   
  
## <a name="session-operations"></a>Operace relací  
  
### <a name="session-renew-lock"></a>Zámek obnovení relace  

Prodlouží zámek zprávy o čas zadaný v popisu entity.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|ID relace|řetězec|Yes|ID relace|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – obsahuje více zpráv<br /><br /> 204: žádný obsah – žádné další zprávy|  
|statusDescription|řetězec|No|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující mapu s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|vypršení platnosti|časové razítko|Yes|Nové vypršení platnosti.|  
  
### <a name="peek-session-message"></a>Náhled zprávy relace  

Prohlédne zprávy relace bez uzamčení.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|z-Sequence-Number|long|Yes|Pořadové číslo, ze kterého se má začít prohlížet náhled|  
|počet zpráv|int|Yes|Maximální počet zpráv, které se mají prohlížet|  
|ID relace|řetězec|Yes|ID relace|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – obsahuje více zpráv<br /><br /> 204: žádný obsah – žádné další zprávy|  
|statusDescription|řetězec|No|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující mapu s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Yes|Seznam zpráv, ve kterých každá mapa představuje zprávu|  
  
 Mapa, která představuje zprávu, musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|zpráva|pole bajtů|Yes|AMQP 1,0 – zpráva kódovaná pomocí drátu.|  
  
### <a name="set-session-state"></a>Nastavit stav relace  

Nastaví stav relace.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|ID relace|řetězec|Yes|ID relace|  
|stav relace|pole bajtů|Yes|Neprůhledná binární data.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|řetězec|No|Popis stavu|  
  
### <a name="get-session-state"></a>Získat stav relace  

Získá stav relace.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|ID relace|řetězec|Yes|ID relace|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|řetězec|No|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|stav relace|pole bajtů|Yes|Neprůhledná binární data.|  
  
### <a name="enumerate-sessions"></a>Zobrazení výčtu relací  

Vytvoří výčet relací v entitě zasílání zpráv.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Poslední aktualizace – čas|časové razítko|Yes|Filtr tak, aby zahrnoval pouze relace aktualizované po daném čase.|  
|Přeskočit|int|Yes|Přeskočit několik relací.|  
|top|int|Yes|Maximální počet relací.|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – obsahuje více zpráv<br /><br /> 204: žádný obsah – žádné další zprávy|  
|statusDescription|řetězec|No|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Přeskočit|int|Yes|Počet vynechaných relací, pokud je stavový kód 200.|  
|relace – identifikátory|pole řetězců|Yes|Pole ID relací, pokud je stavový kód 200.|  
  
## <a name="rule-operations"></a>Operace pravidla  
  
### <a name="add-rule"></a>Přidat pravidlo  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pravidlo – název|řetězec|Yes|Název pravidla, včetně názvu předplatného a tématu|  
|pravidlo – popis|mapa|Yes|Popis pravidla, jak je uvedeno v následující části.|  
  
Mapa **popisu pravidla** musí zahrnovat následující položky, ve kterých se vzájemně vylučují filtry **SQL** a **Filtr korelace** :  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|filtr SQL|mapa|Yes|`sql-filter`, jak je uvedeno v následující části.|  
|filtr korelace|mapa|Yes|`correlation-filter`, jak je uvedeno v následující části.|  
|SQL – pravidlo – akce|mapa|Yes|`sql-rule-action`, jak je uvedeno v následující části.|  
  
Mapa filtru SQL musí zahrnovat následující položky:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|expression|řetězec|Yes|Výraz filtru SQL|  
  
Mapa **filtru korelace** musí zahrnovat aspoň jednu z následujících položek:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|correlation-id|řetězec|No||  
|ID zprávy|řetězec|No||  
|na|řetězec|No||  
|odpovědět na|řetězec|No||  
|label|řetězec|No||  
|ID relace|řetězec|No||  
|odpovědi na relaci – ID|řetězec|No||  
|typ obsahu|řetězec|No||  
|properties|mapa|No|Mapuje se Service Bus [BrokeredMessage. Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
Mapa **akcí SQL-Rule** musí zahrnovat následující položky:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|expression|řetězec|Yes|Výraz akce SQL|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|řetězec|No|Popis stavu|  
  
### <a name="remove-rule"></a>Odebrat pravidlo  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pravidlo – název|řetězec|Yes|Název pravidla, včetně názvu předplatného a tématu|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|řetězec|No|Popis stavu|  
  
### <a name="get-rules"></a>Získat pravidla

#### <a name="request"></a>Žádost

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:

|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  

Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|top|int|Yes|Počet pravidel, která mají být načtena na stránce.|  
|Přeskočit|int|Yes|Počet pravidel, která se mají přeskočit Definuje počáteční index (+ 1) v seznamu pravidel. | 

#### <a name="response"></a>Odpověď

Zpráva s odpovědí obsahuje následující vlastnosti:

|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|pravidla| pole mapy|Yes|Pole pravidel. Každé pravidlo představuje mapa.|

Každá položka mapování v poli obsahuje následující vlastnosti:

|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pravidlo – popis|pole popsaných objektů|Yes|`com.microsoft:rule-description:list` s AMQP popisem kódu 0x0000013700000004| 

`com.microsoft.rule-description:list` je pole popsaných objektů. Pole obsahuje následující:

|Index|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
| 0 | pole popsaných objektů | Yes | `filter` Jak je uvedeno níže. |
| 1 | pole popsaných objektů | Yes | `ruleAction` Jak je uvedeno níže. |
| 2 | řetězec | Yes | název pravidla |

`filter` může mít jeden z následujících typů:

| Název popisovače | Kód popisovače | Hodnota |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | Filtr SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Filtr korelace |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Pravdivý filtr představující 1 = 1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Filtr false představující 1 = 0 |

`com.microsoft:sql-filter:list` je popsané pole, které obsahuje:

|Index|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
| 0 | řetězec | Yes | Výraz filtru SQL |

`com.microsoft:correlation-filter:list` je popsané pole, které obsahuje:

|Index (pokud existuje)|Typ hodnoty|Obsah hodnoty|  
|---------|----------------|--------------|
| 0 | řetězec | ID korelace |
| 1 | řetězec | ID zprávy |
| 2 | řetězec | Záměr |
| 3 | řetězec | Odpovědět na |
| 4 | řetězec | Popisek |
| 5 | řetězec | ID relace |
| 6 | řetězec | Odpověď na ID relace|
| 7 | řetězec | Typ obsahu |
| 8 | Mapa | Mapa definovaných vlastností aplikace |

`ruleAction` může být jedním z následujících typů:

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
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouhé|Yes|Pořadová čísla.|  
|přijímač – vyrovnaný režim|ubyte|Yes|Režim **vyrovnání přijímače** zadaný v AMQP Core v 1.0|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|řetězec|No|Popis stavu|  
  
Tělo zprávy odpovědi se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Yes|Seznam zpráv, kde každá mapa představuje zprávu|  
  
Mapa, která představuje zprávu, musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|zámek tokenu|uuid|Yes|Token zámku `receiver-settle-mode` , pokud je 1.|  
|zpráva|pole bajtů|Yes|AMQP 1,0 – zpráva kódovaná pomocí drátu.|  
  
### <a name="update-disposition-status"></a>Aktualizovat stav dispozice  

Aktualizuje stav dispozice pro odložené zprávy. Tato operace podporuje transakce.
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|operation|řetězec|Yes|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|No|Časový limit provozního serveru v milisekundách|  
  
Tělo zprávy požadavku se musí skládat z části **AMQP-Value** obsahující **mapu** s následujícími položkami:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|dispoziční stav|řetězec|Yes|kompletní<br /><br /> zastaven<br /><br /> rozpuštěn|  
|tokeny Lock|pole UUID|Yes|Tokeny zámku zprávy pro aktualizaci stavu dispozice.|  
|nedoručené zprávy – důvod|řetězec|No|Dá se nastavit, pokud je stav dispozice nastavený na **pozastaveno**.|  
|nedoručené zprávy – popis|řetězec|No|Dá se nastavit, pokud je stav dispozice nastavený na **pozastaveno**.|  
|vlastnosti-k úpravě|mapa|No|Seznam Service Bus vlastností zprostředkovaných zpráv, které se mají upravit|  
  
#### <a name="response"></a>Odpověď  

Zpráva odpovědi musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Vyžadováno|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|statusCode|int|Yes|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak chyba|  
|statusDescription|řetězec|No|Popis stavu|

## <a name="next-steps"></a>Další kroky

Další informace o AMQP a Service Bus najdete na následujících odkazech:

* [Přehled Service Bus AMQP]
* [Průvodce protokolem AMQP 1.0]
* [AMQP v Service Bus pro Windows Server]

[Přehled Service Bus AMQP]: service-bus-amqp-overview.md
[Průvodce protokolem AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP v Service Bus pro Windows Server]: /previous-versions/service-bus-archive/dn282144(v=azure.100)
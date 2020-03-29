---
title: Operace požadavku a odpovědi AMQP 1.0 v Azure Service Bus
description: Tento článek definuje seznam operací založených na požadavcích AMQP a odpovědí chodmicrosoft Azure Service Bus.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76761079"
---
# <a name="amqp-10-in-microsoft-azure-service-bus-request-response-based-operations"></a>AMQP 1.0 v Microsoft Azure Service Bus: operace založené na odezvě na požadavcích

Tento článek definuje seznam operací microsoft azure service bus požadavek/odpověď založené na odpovědi. Tyto informace jsou založeny na pracovní pracovní pracovní verzi amqp management verze 1.0.  
  
Podrobný průvodce protokolem AMQP 1.0 na úrovni drátu, který vysvětluje, jak service bus implementuje a staví na technické specifikaci OASIS AMQP, najdete v tématu [AMQP 1.0 v průvodci protokolem Azure Service Bus a Event Hubs][AMQP 1.0].  
  
## <a name="concepts"></a>Koncepty  
  
### <a name="entity-description"></a>Popis entity  

Popis entity odkazuje na [třídu QueueDescription](/dotnet/api/microsoft.servicebus.messaging.queuedescription)sběrnice služby , [třídu TopicDescription](/dotnet/api/microsoft.servicebus.messaging.topicdescription)nebo objekt [třídy SubscriptionDescription.](/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription)  
  
### <a name="brokered-message"></a>Zprostředkovaná zpráva  

Představuje zprávu v service bus, který je mapován na zprávu AMQP. Mapování je definováno v [průvodci protokolem AMQP service bus](service-bus-amqp-protocol-guide.md).  
  
## <a name="attach-to-entity-management-node"></a>Připojit k uzlu správy entit  

Všechny operace popsané v tomto dokumentu postupujte podle vzoru požadavku/odpovědi, jsou vymezeny na entitu a vyžadují připojení k uzlu správy entit.  
  
### <a name="create-link-for-sending-requests"></a>Vytvořit odkaz pro odesílání požadavků  

Vytvoří odkaz na uzel správy pro odesílání požadavků.  
  
```
requestLink = session.attach(
role: SENDER,
    target: { address: "<entity address>/$management" },
    source: { address: ""<my request link unique address>" }
)

```
  
### <a name="create-link-for-receiving-responses"></a>Vytvořit odkaz pro příjem odpovědí  

Vytvoří odkaz pro příjem odpovědí z uzlu správy.  
  
```
responseLink = session.attach(
role: RECEIVER,
    source: { address: "<entity address>/$management" }
    target: { address: "<my response link unique address>" }
)

```
  
### <a name="transfer-a-request-message"></a>Přenos zprávy požadavku  

Přenese zprávu požadavku.  
Stav transakce lze přidat volitelně pro operace, které podporují transakce.

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
  
### <a name="receive-a-response-message"></a>Přijetí zprávy s odpovědí  

Obdrží zprávu odpovědi z odkazu odpovědi.  
  
```
responseMessage = responseLink.receiveTransfer()
```
  
Zpráva s odpovědí je v následujícím formuláři:
  
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
  
### <a name="service-bus-entity-address"></a>Adresa entity service bus  

Entity service bus musí být řešeny takto:  
  
|Typ entity|Adresa|Příklad|  
|-----------------|-------------|-------------|  
|fronta|`<queue_name>`|`“myQueue”`<br /><br /> `“site1/myQueue”`|  
|téma|`<topic_name>`|`“myTopic”`<br /><br /> `“site2/page1/myQueue”`|  
|předplatné|`<topic_name>/Subscriptions/<subscription_name>`|`“myTopic/Subscriptions/MySub”`|  
  
## <a name="message-operations"></a>Operace se zprávami  
  
### <a name="message-renew-lock"></a>Zámek obnovení zprávy  

Prodlouží zámek zprávy o čas určený v popisu entity.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:renew-lock`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
 Tělo zprávy požadavku se musí skládat z oddílu s hodnotou amqp obsahující ho s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|`lock-tokens`|pole uuid|Ano|Tokeny zámku zprávy, které chcete obnovit.|  

> [!NOTE]
> Tokeny zámku `DeliveryTag` jsou vlastnostna přijaté zprávy. Viz následující příklad v [.NET SDK,](https://github.com/Azure/azure-service-bus-dotnet/blob/6f144e91310dcc7bd37aba4e8aebd535d13fa31a/src/Microsoft.Azure.ServiceBus/Amqp/AmqpMessageConverter.cs#L336) který načte tyto. Token se může také zobrazit v "DeliveryAnnotations" jako "x-opt-lock-token", `DeliveryTag` ale to není zaručeno a by měla být upřednostňována. 
> 
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK - úspěch, jinak selhal.|  
|stavPopis|řetězec|Ne|Popis stavu.|  
  
Tělo zprávy odpovědi se musí skládat z oddílu s hodnotou amqp obsahující ho s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Vyprchávání|pole časového razítka|Ano|Token zámku zprávy nové vypršení platnosti odpovídající tokeny zámku požadavku.|  
  
### <a name="peek-message"></a>Náhled zprávy  

Podívá se na zprávy bez zamykání.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|`from-sequence-number`|long|Ano|Pořadové číslo, ze kterého chcete spustit náhled.|  
|`message-count`|int|Ano|Maximální počet zpráv ke shlédnutí.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK - má více zpráv<br /><br /> 204: Žádný obsah - žádné další zprávy|  
|stavPopis|řetězec|Ne|Popis stavu.|  
  
Tělo zprávy odpovědi se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Ano|Seznam zpráv, ve kterých každá mapa představuje zprávu.|  
  
Mapa představující zprávu musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|zpráva|pole bajtů|Ano|Zpráva kódovaná drátem AMQP 1.0.|  
  
### <a name="schedule-message"></a>Naplánovat zprávu  

Naplánuje zprávy. Tato operace podporuje transakce.
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:schedule-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Ano|Seznam zpráv, ve kterých každá mapa představuje zprávu.|  
  
Mapa představující zprávu musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|id zprávy|řetězec|Ano|`amqpMessage.Properties.MessageId`jako řetězec|  
|id relace|řetězec|Ne|`amqpMessage.Properties.GroupId as string`|  
|klíč oddílu|řetězec|Ne|`amqpMessage.MessageAnnotations.”x-opt-partition-key"`|
|via-partition-key|řetězec|Ne|`amqpMessage.MessageAnnotations."x-opt-via-partition-key"`|
|zpráva|pole bajtů|Ano|Zpráva kódovaná drátem AMQP 1.0.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK - úspěch, jinak selhal.|  
|stavPopis|řetězec|Ne|Popis stavu.|  
  
Tělo zprávy odpovědi se musí skládat z oddílu **s hodnotou amqp** obsahující ho s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouhých|Ano|Pořadové číslo naplánovaných zpráv. Pořadové číslo se používá ke zrušení.|  
  
### <a name="cancel-scheduled-message"></a>Zrušit naplánovanou zprávu  

Zruší naplánované zprávy.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:cancel-scheduled-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouhých|Ano|Pořadová čísla naplánovaných zpráv, které mají být zrušeny.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK - úspěch, jinak selhal.|  
|stavPopis|řetězec|Ne|Popis stavu.|   
  
## <a name="session-operations"></a>Operace relace  
  
### <a name="session-renew-lock"></a>Zámek obnovení relace  

Prodlouží zámek zprávy o čas určený v popisu entity.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:renew-session-lock`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|id relace|řetězec|Ano|ID relace.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK - má více zpráv<br /><br /> 204: Žádný obsah - žádné další zprávy|  
|stavPopis|řetězec|Ne|Popis stavu.|  
  
Tělo zprávy odpovědi se musí skládat z oddílu **s hodnotou amqp** obsahující ho s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Vypršení platnosti|časové razítko|Ano|Nová expirace.|  
  
### <a name="peek-session-message"></a>Náhled zprávy relace  

Prohlédne zprávy relace bez uzamčení.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:peek-message`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|od pořadové číslo|long|Ano|Pořadové číslo, ze kterého chcete spustit náhled.|  
|počet zpráv|int|Ano|Maximální počet zpráv ke shlédnutí.|  
|id relace|řetězec|Ano|ID relace.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK - má více zpráv<br /><br /> 204: Žádný obsah - žádné další zprávy|  
|stavPopis|řetězec|Ne|Popis stavu.|  
  
Tělo zprávy odpovědi se musí skládat z oddílu **s hodnotou amqp** obsahující ho s následujícími položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Ano|Seznam zpráv, ve kterých každá mapa představuje zprávu.|  
  
 Mapa představující zprávu musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|zpráva|pole bajtů|Ano|Zpráva kódovaná drátem AMQP 1.0.|  
  
### <a name="set-session-state"></a>Nastavit stav relace  

Nastaví stav relace.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:set-session-state`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|id relace|řetězec|Ano|ID relace.|  
|stav relace|pole bajtů|Ano|Neprůhledná binární data.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak neúspěšná|  
|stavPopis|řetězec|Ne|Popis stavu.|  
  
### <a name="get-session-state"></a>Získat stav relace  

Získá stav relace.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:get-session-state`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|id relace|řetězec|Ano|ID relace.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak neúspěšná|  
|stavPopis|řetězec|Ne|Popis stavu.|  
  
Tělo zprávy odpovědi se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|stav relace|pole bajtů|Ano|Neprůhledná binární data.|  
  
### <a name="enumerate-sessions"></a>Výčet relací  

Výčet relací v entitě zasílání zpráv.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:get-message-sessions`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|poslední aktualizovaný čas|časové razítko|Ano|Filtr obsahuje pouze relace aktualizované po daném čase.|  
|Přeskočit|int|Ano|Přeskočte několik relací.|  
|top|int|Ano|Maximální počet relací.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK - má více zpráv<br /><br /> 204: Žádný obsah - žádné další zprávy|  
|stavPopis|řetězec|Ne|Popis stavu.|  
  
Tělo zprávy odpovědi se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Přeskočit|int|Ano|Počet přeskočených relací, pokud je stavový kód 200.|  
|id relací|pole řetězců|Ano|Pole ID relace, pokud je stavový kód 200.|  
  
## <a name="rule-operations"></a>Operace s pravidly  
  
### <a name="add-rule"></a>Přidat pravidlo  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:add-rule`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|název pravidla|řetězec|Ano|Název pravidla, bez názvu předplatného a tématu.|  
|popis pravidla|map|Ano|Popis pravidla, jak je uvedeno v další části.|  
  
Mapa **popisu pravidla** musí obsahovat následující položky, kde **sql-filter** a **correlation-filter** se vzájemně vylučují:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|sql-filtr|map|Ano|`sql-filter`, jak je uvedeno v další části.|  
|korelační filtr|map|Ano|`correlation-filter`, jak je uvedeno v další části.|  
|sql-rule-action|map|Ano|`sql-rule-action`, jak je uvedeno v další části.|  
  
Mapa filtru SQL musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|výraz|řetězec|Ano|Výraz filtru SQL.|  
  
Mapa **filtru korelace** musí obsahovat alespoň jednu z následujících položek:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|correlation-id|řetězec|Ne||  
|id zprávy|řetězec|Ne||  
|na|řetězec|Ne||  
|odpověď na odpověď|řetězec|Ne||  
|label|řetězec|Ne||  
|id relace|řetězec|Ne||  
|id odpovědi na relaci|řetězec|Ne||  
|typ obsahu|řetězec|Ne||  
|properties|map|Ne|Mapy na service bus [brokeredMessage.Properties](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage).|  
  
Mapa **sql-rule-action** musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|výraz|řetězec|Ano|Výraz akce SQL.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak neúspěšná|  
|stavPopis|řetězec|Ne|Popis stavu.|  
  
### <a name="remove-rule"></a>Odebrat pravidlo  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:remove-rule`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|název pravidla|řetězec|Ano|Název pravidla, bez názvu předplatného a tématu.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak neúspěšná|  
|stavPopis|řetězec|Ne|Popis stavu.|  
  
### <a name="get-rules"></a>Získat pravidla

#### <a name="request"></a>Žádost

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:

|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:enumerate-rules`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  

Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|top|int|Ano|Počet pravidel, která se mají na číst na stránce.|  
|Přeskočit|int|Ano|Počet pravidel přeskočit. Definuje počáteční index (+1) v seznamu pravidel. | 

#### <a name="response"></a>Odpověď

Zpráva s odpovědí obsahuje následující vlastnosti:

|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak neúspěšná|  
|pravidla| pole mapy|Ano|Pole pravidel. Každé pravidlo je reprezentováno mapou.|

Každá položka mapy v poli obsahuje následující vlastnosti:

|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|popis pravidla|pole popsaných objektů|Ano|`com.microsoft:rule-description:list`s AMQP popsaným kódem 0x0000013700000004| 

`com.microsoft.rule-description:list`je pole popsaných objektů. Pole obsahuje následující:

|Index|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
| 0 | pole popsaných objektů | Ano | `filter`jak je uvedeno níže. |
| 1 | pole popsaného objektu | Ano | `ruleAction`jak je uvedeno níže. |
| 2 | řetězec | Ano | název pravidla. |

`filter`může být některého z následujících typů:

| Název popisovače | Kód popisu | Hodnota |
| --- | --- | ---|
| `com.microsoft:sql-filter:list` | 0x000001370000006 | FILTR SQL |
| `com.microsoft:correlation-filter:list` | 0x000001370000009 | Korelační filtr |
| `com.microsoft:true-filter:list` | 0x000001370000007 | Skutečný filtr představující 1=1 |
| `com.microsoft:false-filter:list` | 0x000001370000008 | Falešný filtr představující 1=0 |

`com.microsoft:sql-filter:list`je popsané pole, které zahrnuje:

|Index|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
| 0 | řetězec | Ano | Výraz filtru SQL |

`com.microsoft:correlation-filter:list`je popsané pole, které zahrnuje:

|Index (pokud existuje)|Typ hodnoty|Obsah hodnoty|  
|---------|----------------|--------------|
| 0 | řetězec | ID korelace |
| 1 | řetězec | ID zprávy |
| 2 | řetězec | Akce |
| 3 | řetězec | Odpovědět na |
| 4 | řetězec | Popisek |
| 5 | řetězec | ID relace |
| 6 | řetězec | Odpověď na ID relace|
| 7 | řetězec | Typ obsahu |
| 8 | Mapa | Mapa vlastností definovaných aplikací |

`ruleAction`může být některý z následujících typů:

| Název popisovače | Kód popisu | Hodnota |
| --- | --- | ---|
| `com.microsoft:empty-rule-action:list` | 0x0000013700000005 | Prázdná akce pravidla – není k dispozici žádná akce pravidla. |
| `com.microsoft:sql-rule-action:list` | 0x00000137000000006 | Akce pravidla SQL |

`com.microsoft:sql-rule-action:list`je pole popsaných objektů, jejichž první položka je řetězec, který obsahuje výraz akce pravidla SQL.

## <a name="deferred-message-operations"></a>Operace s odloženou zprávou  
  
### <a name="receive-by-sequence-number"></a>Příjem podle pořadové číslo  

Přijímá odložené zprávy podle pořadové číslo.  
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:receive-by-sequence-number`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|pořadová čísla|pole dlouhých|Ano|Čísla.|  
|režim přijímač-usadit|ubyte|Ano|**Režim vyrovnání přijímače,** jak je uvedeno v jádru AMQP v1.0.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak neúspěšná|  
|stavPopis|řetězec|Ne|Popis stavu.|  
  
Tělo zprávy odpovědi se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|cloud-zařízení|seznam map|Ano|Seznam zpráv, kde každá mapa představuje zprávu.|  
  
Mapa představující zprávu musí obsahovat následující položky:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|lock-token|Uuid|Ano|Token zámku, pokud `receiver-settle-mode` je 1.|  
|zpráva|pole bajtů|Ano|Zpráva kódovaná drátem AMQP 1.0.|  
  
### <a name="update-disposition-status"></a>Aktualizovat stav dispozice  

Aktualizuje dispoziční stav odložených zpráv. Tato operace podporuje transakce.
  
#### <a name="request"></a>Žádost  

Zpráva požadavku musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Operace|řetězec|Ano|`com.microsoft:update-disposition`|  
|`com.microsoft:server-timeout`|uint|Ne|Časový rozsah operačního serveru v milisekundách.|  
  
Tělo zprávy požadavku se musí skládat z oddílu **s hodnotou amqp** obsahující **ho s následujícími** položkami:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|dispoziční status|řetězec|Ano|Dokončena<br /><br /> Opuštěné<br /><br /> Pozastavena|  
|tokeny zámku|pole uuid|Ano|Tokeny zámku zprávy pro aktualizaci stavu dispozice.|  
|deadletter-důvod|řetězec|Ne|Může být nastavena, pokud je dispoziční stav nastaven na **pozastaveno**.|  
|mrtvý dopis-popis|řetězec|Ne|Může být nastavena, pokud je dispoziční stav nastaven na **pozastaveno**.|  
|vlastnosti- upravit|map|Ne|Seznam vlastností zprostředkované zprávy service bus upravit.|  
  
#### <a name="response"></a>Odpověď  

Zpráva s odpovědí musí obsahovat následující vlastnosti aplikace:  
  
|Klíč|Typ hodnoty|Požaduje se|Obsah hodnoty|  
|---------|----------------|--------------|--------------------|  
|Statuscode|int|Ano|Kód odpovědi HTTP [RFC2616]<br /><br /> 200: OK – úspěch, jinak neúspěšná|  
|stavPopis|řetězec|Ne|Popis stavu.|

## <a name="next-steps"></a>Další kroky

Další informace o službách AMQP a Service Bus naleznete na následujících odkazech:

* [Přehled služby AMQP sběrnice]
* [Průvodce protokolem AMQP 1.0]
* [AMQP v service bus pro Windows Server]

[Přehled služby AMQP sběrnice]: service-bus-amqp-overview.md
[Průvodce protokolem AMQP 1.0]: service-bus-amqp-protocol-guide.md
[AMQP v service bus pro Windows Server]: https://docs.microsoft.com/previous-versions/service-bus-archive/dn282144(v=azure.100)

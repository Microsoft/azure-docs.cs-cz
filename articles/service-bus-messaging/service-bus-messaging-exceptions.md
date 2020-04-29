---
title: Azure Service Bus – výjimky zasílání zpráv | Microsoft Docs
description: Tento článek poskytuje seznam výjimek zasílání zpráv Azure Service Bus a navrhovaných akcí, které se mají učinit, když dojde k výjimce.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/23/2020
ms.author: aschhab
ms.openlocfilehash: d04902a8d53397b7e7d9712a1c75ce44cc7aa7ad
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80880784"
---
# <a name="service-bus-messaging-exceptions"></a>Service Bus výjimky zasílání zpráv
Tento článek obsahuje seznam výjimek rozhraní .NET generovaných rozhraními API .NET Framework. 

## <a name="exception-categories"></a>Kategorie výjimek
Rozhraní API pro zasílání zpráv generují výjimky, které mohou být v následujících kategoriích, spolu s přidruženou akcí, kterou můžete provést při pokusu o jejich opravu. Význam a příčiny výjimky se mohou lišit v závislosti na typu entity zasílání zpráv:

1. Chyba kódování uživatele ([System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. Serialization. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Obecná akce: Zkuste opravit kód, než budete pokračovat.
2. Chyba nastavení/konfigurace ([Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Obecná akce: Zkontrolujte konfiguraci a v případě potřeby proveďte změny.
3. Přechodné výjimky ([Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. výjimka serverbusyexception](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Obecná akce: zkuste operaci zopakovat nebo upozorněte uživatele. `RetryPolicy` Třídu v klientské sadě SDK lze nakonfigurovat tak, aby zpracovávala opakované pokusy automaticky. Další informace najdete v tématu [pokyny pro opakování](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Další výjimky ([System. Transactions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Obecná akce: specifická pro typ výjimky; informace najdete v tabulce v následující části: 

## <a name="exception-types"></a>Typy výjimek
V následující tabulce jsou uvedeny typy výjimek zasílání zpráv a jejich příčiny a poznámky navrhovaná akce, které můžete provést.

| **Typ výjimky** | **Popis/příčiny/příklady** | **Navrhovaná akce** | **Poznámka k automatickému/okamžitému opakování** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Server neodpověděl na požadovanou operaci v zadaném čase, který je řízen [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Je možné, že server dokončil požadovanou operaci. K tomu může dojít z důvodu zpoždění sítě nebo jiné infrastruktury. |Ověřte konzistenci stavu systému a v případě potřeby akci opakujte. Viz [výjimky časového limitu](#timeoutexception). |V některých případech může být užitečné zkusit to znovu. Přidejte do kódu logiku opakování. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná operace uživatele není povolena v rámci serveru nebo služby. Podrobnosti najdete ve zprávě výjimky. Například [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) vygeneruje tuto výjimku, pokud byla zpráva přijata v režimu [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) . |Podívejte se na kód a dokumentaci. Ujistěte se, že požadovaná operace je platná. |Nemůžete to zkusit znovu. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Došlo k pokusu o vyvolání operace u objektu, který již byl uzavřen, přerušen nebo vyřazen. Ve výjimečných případech je ambientní transakce již uvolněna. |Zkontrolujte kód a ujistěte se, že nevyvolává operace u uvolněného objektu. |Nemůžete to zkusit znovu. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Objekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nemohl získat token, token je neplatný nebo token neobsahuje deklarace identity vyžadované k provedení operace. |Ujistěte se, že Poskytovatel tokenů je vytvořený se správnými hodnotami. Ověřte konfiguraci Access Control Service. |V některých případech může být užitečné zkusit to znovu. Přidejte do kódu logiku opakování. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Jeden nebo více argumentů dodaných metodě je neplatných.<br /> Identifikátor URI zadaný pro [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obsahuje segmenty cesty.<br /> Schéma identifikátoru URI zadané pro [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Vytvoření](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) je neplatné. <br />Hodnota vlastnosti je větší než 32 KB. |Zkontrolujte kód volajícího a ujistěte se, že jsou argumenty správné. |Nemůžete to zkusit znovu. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entita přidružená k operaci neexistuje nebo byla odstraněna. |Ujistěte se, že entita existuje. |Nemůžete to zkusit znovu. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Došlo k pokusu o přijetí zprávy s určitým pořadovým číslem. Tato zpráva se nenašla. |Ujistěte se, že zpráva již nebyla přijata. Zkontrolujte frontu nedoručených zpráv a podívejte se, zda byla zpráva deadlettered. |Nemůžete to zkusit znovu. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient nemůže navázat připojení k Service Bus. |Ujistěte se, že je zadaný název hostitele správný a že je hostitel dosažitelný. |Zkuste to znovu, pokud dojde k problémům s přerušovaným připojením. |
| [Výjimka serverbusyexception](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Služba v tuto chvíli nemůže zpracovat požadavek. |Klient může na určitou dobu počkat a pak operaci zopakovat. |Klient může po určitém intervalu opakovat pokus. Pokud výsledkem opakování dojde k jiné výjimce, ověřte chování této výjimky znovu. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Platnost tokenu zámku přidruženého ke zprávě vypršela nebo se token zámku nenašel. |Vyřadit zprávu. |Nemůžete to zkusit znovu. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Zámek přidružený k této relaci je ztracený. |Přerušit objekt [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |Nemůžete to zkusit znovu. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Obecná výjimka zasílání zpráv, která může být vyvolána v následujících případech:<p>Byl proveden pokus o vytvoření [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) pomocí názvu nebo cesty, která patří k jinému typu entity (například téma).</p><p>Byl proveden pokus o odeslání zprávy, která je větší než 256 KB. </p>U serveru nebo služby došlo k chybě během zpracování žádosti. Podrobnosti najdete ve zprávě výjimky. Obvykle se jedná o přechodnou výjimku.</p><p>Požadavek se ukončil, protože entita je omezená. Kód chyby: 50001, 50002, 50008. </p> | Zkontrolujte kód a zajistěte, aby se pro tělo zprávy používaly pouze serializovatelné objekty (nebo použijte vlastní serializátor). <p>Vyhledejte v dokumentaci podporované typy hodnot vlastností a používejte pouze podporované typy.</p><p> Ověřte vlastnost- [přechodný](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Pokud je to **pravda**, můžete operaci zopakovat. </p>| Pokud je výjimka způsobena omezením, počkejte několik sekund a operaci opakujte. Chování při opakování není definované a nemusí pomáhat v dalších scénářích.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Pokusí se vytvořit entitu s názvem, který už používá jiná entita v daném oboru názvů služby. |Odstraňte existující entitu nebo vyberte jiný název entity, která se má vytvořit. |Nemůžete to zkusit znovu. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Entita zasílání zpráv dosáhla maximální povolené velikosti nebo byl překročen maximální počet připojení k oboru názvů. |Přiřadíte místo v entitě příjem zpráv z entity nebo jejích podfront. Viz [QuotaExceededException](#quotaexceededexception). |Pokud se zprávy během této doby odstranily, může to zkusit znovu. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus vrátí tuto výjimku, pokud se pokusíte vytvořit neplatnou akci pravidla. Service Bus připojí tuto výjimku ke zprávě deadlettered, pokud dojde k chybě při zpracování akce pravidla pro tuto zprávu. |Ověřte správnost akce pravidla. |Nemůžete to zkusit znovu. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus vrátí tuto výjimku, pokud se pokusíte vytvořit neplatný filtr. Service Bus připojí tuto výjimku ke zprávě deadlettered, pokud došlo k chybě při zpracování filtru pro tuto zprávu. |Ověřte správnost filtru. |Nemůžete to zkusit znovu. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Došlo k pokusu o přijetí relace s určitým ID relace, ale relace je aktuálně uzamčena jiným klientem. |Ujistěte se, že je relace odemčena ostatními klienty. |Zkuste to znovu, pokud byla relace vydaná v provizorním režimu. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Transakce je příliš mnoho operací. |Snižte počet operací, které jsou součástí této transakce. |Nemůžete to zkusit znovu. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Požadavek na běhovou operaci u zakázané entity |Aktivujte entitu. |Zkuste to znovu, pokud se entita aktivovala průběžně. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus vrátí tuto výjimku, pokud odešlete zprávu do tématu s povoleným předfiltrem a žádný z filtrů neodpovídá. |Ujistěte se, že alespoň jeden filtr odpovídá. |Nemůžete to zkusit znovu. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Datová část zprávy překračuje limit 256 KB. Limit 256-KB je celková velikost zprávy, která může zahrnovat systémové vlastnosti a veškeré režie technologie .NET. |Snižte velikost datové části zprávy a potom operaci opakujte. |Nemůžete to zkusit znovu. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Ambientní transakce (*Transaction. Current*) je neplatná. Je možné, že byla dokončena nebo přerušena. Vnitřní výjimka může poskytovat další informace. | |Nemůžete to zkusit znovu. |
| [TransactionInDoubtException –](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Došlo k pokusu o provedení operace v transakci, která je nejistá, nebo dojde k pokusu o potvrzení transakce a transakce se stane nejistými. |Vaše aplikace musí tuto výjimku zpracovat (jako zvláštní případ), protože transakce již byla potvrzena. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indikuje, že došlo k překročení kvóty pro některou z entit.

### <a name="queues-and-topics"></a>Fronty a témata
U front a témat je často velikost fronty. Vlastnost chybová zpráva obsahuje další podrobnosti, jako v následujícím příkladu:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Zpráva uvádí, že téma překročilo omezení velikosti, v tomto případě 1 GB (výchozí omezení velikosti). 

### <a name="namespaces"></a>Jmenné prostory

Pro obory názvů může [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) značit, že aplikace překročila maximální počet připojení k oboru názvů. Příklad:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Běžné příčiny
Existují dva běžné příčiny této chyby: fronta nedoručených zpráv a přijímače nefunkčních zpráv.

1. **[Fronta nedoručených zpráv](service-bus-dead-letter-queues.md)** U čtecího modulu se nedaří dokončit zprávy a zprávy jsou po vypršení zámku vráceny do fronty nebo tématu. K tomu může dojít, pokud Čtenář narazí na výjimku, která zabrání v volání [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Po 10 časech se zpráva ve výchozím nastavení přesune do fronty nedoručených zpráv. Toto chování je řízeno vlastností [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) a má výchozí hodnotu 10. Vzhledem k tomu, že se zprávy dostanou ve frontě nedoručených zpráv, zabírají místo.
   
    Chcete-li tento problém vyřešit, přečtěte si a dokončete zprávy z fronty nedoručených zpráv, stejně jako z jakékoli jiné fronty. Můžete použít metodu [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) , která vám pomůžou naformátovat cestu fronty nedoručených zpráv.
2. **Přijímač byl zastaven**. Příjemce zastavil přijímání zpráv z fronty nebo odběru. Způsob, jak to zjistit, je podívat se na vlastnost [QueueDescription. MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , která zobrazuje úplné rozpis zpráv. Pokud je vlastnost [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) vysoká nebo roste, zprávy nejsou čteny tak rychle, jak jsou zapisovány.

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace iniciovaná uživatelem trvá déle, než je časový limit operace. 

Měli byste zaškrtnout hodnotu vlastnosti [Třída ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , protože po dosažení tohoto limitu může také dojít k [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Fronty a témata
V případě front a témat je časový limit zadán buď ve vlastnosti [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) jako součást připojovacího řetězce, nebo prostřednictvím [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Chybová zpráva se může lišit, ale vždy obsahuje hodnotu časového limitu zadanou pro aktuální operaci. 

## <a name="next-steps"></a>Další kroky
Kompletní referenční informace k rozhraní .NET API pro Service Bus najdete v [referenčních informacích k rozhraní Azure .NET API](/dotnet/api/overview/azure/service-bus).
Tipy pro řešení potíží najdete v [Průvodci odstraňováním potíží](service-bus-troubleshooting-guide.md) .

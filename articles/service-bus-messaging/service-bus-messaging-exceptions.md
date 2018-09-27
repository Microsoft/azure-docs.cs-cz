---
title: Výjimky zasílání zpráv Azure Service Bus | Dokumentace Microsoftu
description: Seznam výjimky zasílání zpráv služby Service Bus a doporučené akce.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/21/2018
ms.author: spelluru
ms.openlocfilehash: b645b130f75e5f7f15f5676d426ab0f4c86f2849
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47395793"
---
# <a name="service-bus-messaging-exceptions"></a>Výjimky zasílání zpráv Service Bus
Tento článek uvádí některé výjimky generované zasílání zpráv rozhraní API Microsoft Azure Service Bus. Tento odkaz se může změnit, proto zkontrolujte novinky.

## <a name="exception-categories"></a>Kategorie výjimek
API pro přenos zpráv generují výjimky, které můžete spadají do následujících kategorií, spolu s přidružené akce, které si můžete vyzkoušet a opravte je. Všimněte si, že význam a způsobí, že výjimky může lišit v závislosti na typu entity pro zasílání zpráv:

1. Chyba kódování uživatele ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Obecné akce: Zkuste opravit kód než budete pokračovat.
2. Chyba instalace/konfigurace ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Obecné akci: Zkontrolujte konfiguraci a v případě potřeby změnit.
3. Přechodným výjimkám ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Obecné akce: operaci opakovat nebo upozornit uživatele.
4. Ostatní výjimky ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Obecné akce: specifické pro daný typ výjimky Podrobnosti naleznete v tabulce v následující části. 

## <a name="exception-types"></a>Typy výjimek
Následující tabulka uvádí zasílání zpráv typy výjimek a jejich příčiny a poznámky navrhované akce, které můžete provést.

| **Typ výjimky** | **Popis a příčinu a příklady** | **Navrhované akce** | **Poznámka: na automatické/okamžité opakování** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Server neodpověděl na požadovanou operaci v zadaném čase, které řídí [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Server může dokončit požadovanou operaci. Může to být způsobeno sítě nebo jiné infrastruktury zpoždění. |Zkontrolujte stav systému pro zajištění konzistence a opakujte akci v případě potřeby. Zobrazit [výjimkám časového limitu](#timeoutexception). |V některých případech; může pomoci opakování Přidejte do kódu logiku opakování. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná uživatelská operace není povolena v rámci serveru nebo služby. Zobrazit zpráva o výjimce podrobnosti. Například [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) vygeneruje tuto výjimku, pokud byla zpráva přijata v [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) režimu. |Zkontrolujte kód a dokumentace. Ujistěte se, že požadovaná operace je platná. |Opakování nepomůže. |
| [OperationCanceledException –](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Je proveden pokus o vyvolání operace u objektu, který již byl uzavřen, byla přerušena nebo odstraněn. Ve výjimečných případech je již uvolněna okolí transakce. |Zkontrolujte kód a ujistěte se, že vyvolat operace na smazaném objektu. |Opakování nepomůže. |
| [UnauthorizedAccessException –](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objekt nebylo možné získat token, token je neplatný nebo token neobsahuje deklarace potřebné k provedení této operace. |Zajistěte, aby že poskytovatele tokenu, kterého se vytvoří s správné hodnoty. Zkontrolujte konfiguraci služby Řízení přístupu. |V některých případech; může pomoci opakování Přidejte do kódu logiku opakování. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Jeden nebo více argumentů zadaný pro metodu nejsou platné.<br /> Identifikátor URI dodaný [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) obsahuje segmenty cesty.<br /> Schéma identifikátoru URI předaná [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) je neplatný. <br />Hodnota vlastnosti je větší než 32KB. |Zkontrolujte volající kód a ujistěte se, že argumenty jsou správné. |Opakování nepomůže. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entity přidružené operaci neexistuje nebo byl odstraněn. |Ujistěte se, že entita existuje. |Opakování nepomůže. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Pokuste se zobrazí zpráva s konkrétní pořadovým číslem. Tato zpráva se nenašel. |Zajistěte, aby že již nebyl přijat zprávy. Zkontrolujte fronty nedoručených zpráv, které chcete zobrazit, pokud zpráva byla deadlettered. |Opakování nepomůže. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient není schopen navázat připojení ke službě Service Bus. |Ujistěte se, že je správný název zadaného hostitele a hostitel je dostupný. |Opakování může pomoci, pokud dojde k problémům přerušovaným připojením. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Službu není možné na zpracování žádosti. v tuto chvíli. |Klienta můžete počkat určitou dobu a pak zkuste operaci zopakovat. |Klient může po určité době opakujte. Pokud opakování vede k jiné výjimce, podívejte se na chování při opakování této výjimky. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Token uzamčení spojený se zprávou vypršela platnost, nebo nebyl nalezen token uzamčení. |Dispose zprávy. |Opakování nepomůže. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Zámek přidružené k této relaci se ztratí. |Přerušit [popsaným](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektu. |Opakování nepomůže. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Obecný zasílání zpráv výjimku, která může být vyvolána v následujících případech:<br /> Je proveden pokus o vytvoření [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) pomocí názvu nebo cesty, která patří k jiné entitě typu (například téma).<br />  Je proveden pokus o odeslání zprávy větší než 256KB. Server nebo službu došlo k chybě při zpracování požadavku. Podrobnosti naleznete ve zprávě podrobnosti. Obvykle se jedná o přechodnou výjimkou. |Zkontrolujte kód a ujistěte se, že pouze serializovatelné objekty se používají pro tělo zprávy (nebo použijte vlastní serializátor). V dokumentaci pro typy podporovaná hodnota vlastnosti a pouze použití podporované typy. Zkontrolujte, [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) vlastnost. Pokud je **true**, můžete operaci opakovat. |Chování při opakování není definována a nemusí nápovědy. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Pokuste se vytvořit entitu s názvem, který se už používá jiná entita v tomto oboru názvů služby. |Odstraňte existující entity nebo vyberte jiný název entity, který se má vytvořit. |Opakování nepomůže. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Entity pro zasílání zpráv dosáhl maximální povolenou velikost, nebo byl překročen maximální počet připojení na obor názvů. |Příjem zpráv z entity nebo jeho dílčí fronty k vytvoření prostoru v dané entitě. Zobrazit [QuotaExceededException](#quotaexceededexception). |Pokud do té doby byly odebrány zprávy, mohou pomoci při opakování. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus vrátí tuto výjimku při pokusu vytvořit akci neplatné pravidlo. Služby Service Bus připojí k zprávu deadlettered tuto výjimku, pokud dojde k chybě při zpracování akce pravidla pro tuto zprávu. |Zkontrolujte správnost akce pravidla. |Opakování nepomůže. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus vrátí tuto výjimku při pokusu vytvořit neplatný filtr. Služby Service Bus připojí k zprávu deadlettered tuto výjimku, pokud došlo k chybě při zpracování filtr pro tuto zprávu. |Zkontrolujte správnost filtru. |Opakování nepomůže. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Pokus o přijmout relaci s ID konkrétní relace, ale relace je aktuálně uzamčen jiného klienta. |Ujistěte se, že je relace odemknout pomocí jiných klientů. |Pokud relace byla uvolněna mezitím, mohou pomoci při opakování. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Příliš mnoho operací jsou součástí transakce. |Snížit počet operací, které jsou součástí této transakce. |Opakování nepomůže. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Žádost o operaci runtime na entitu, zakázané. |Aktivujte entity. |Pokud entita byla aktivována mezitím, mohou pomoci při opakování. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus vrátí tuto výjimku, pokud odešlete zprávu, která má povolené předem filtrování a neodpovídá žádná filtry. |Zajistěte, aby aspoň jeden filtr hledá shodu. |Opakování nepomůže. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Tělo zprávy překračuje limit 256 KB. Všimněte si, že limit 256 KB je celková velikost zpráv, který může obsahovat systémové vlastnosti a rezervy .NET. |Zmenšit velikost datové části zprávy a potom zkuste operaci zopakovat. |Opakování nepomůže. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Ambientní transakce (*Transaction.Current*) je neplatný. Může byla dokončena nebo zrušena. Vnitřní výjimku, kde mohou poskytnout další informace. | |Opakování nepomůže. |
| [Transactionindoubtexception –](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Pokus o operaci na transakci, která je nejistá, nebo je proveden pokus o potvrzení transakce a transakce stane nejistým výsledkem. |Ke zpracování potřeba aplikace tuto výjimku (jako speciální případ), protože transakce může již byly potvrzeny. |- |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) označuje, že byla překročena kvóta pro konkrétní entitu.

### <a name="queues-and-topics"></a>Fronty a témata
Pro fronty a témata je to často velikost fronty. Vlastnost chybová zpráva obsahuje další podrobnosti, jako v následujícím příkladu:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Zpráva hlásí, že tématu překročily limit velikosti, v tomto případu 1 GB (výchozí limit velikosti). 

### <a name="namespaces"></a>Obory názvů

Pro obory názvů [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) může znamenat, že aplikace byla překročena maximální počet připojení na obor názvů. Příklad:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Mezi běžné příčiny
Existují dvě běžné příčiny této chyby: fronty nedoručených zpráv a nefunkční příjemci zpráv.

1. **[Fronty nedoručených zpráv](service-bus-dead-letter-queues.md)**  čtečku se nedaří dokončit zprávy a zprávy se vrátíte do fronty nebo tématu, když vyprší platnost zámku. K tomu může dojít, pokud čtečka narazí na výjimku, která brání jeho volání [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Až se zpráva přečetla 10krát, přesune se do fronty nedoručených zpráv ve výchozím nastavení. Toto chování se řídí [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) vlastnost a má výchozí hodnotu 10. Podle zprávy hromadí ve frontě nedoručených zpráv, zabírají prostor.
   
    Chcete-li vyřešit tento problém, čtení a dokončete zprávy z fronty nedoručených zpráv, stejně jako v libovolné frontě. Můžete použít [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) metoda umožňující formát cesty fronty nedoručených zpráv.
2. **Příjemce zastavena** příjemce se zastavila příjem zpráv z fronty nebo odběru. Způsob identifikace to můžete se podívat na [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) vlastnost, která zobrazuje úplný přehled zprávy. Pokud [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) vlastnost má vysokou nebo rostoucí pak zprávy nejsou tak rychle, jak jsou zapisovaná čtená.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace iniciovaná uživatelem trvá déle, než je časový limit operace. 

Měli byste zkontrolovat hodnoty [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) vlastnost jako dosažení tohoto limitu může také způsobit [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Fronty a témata
Pro fronty a témata, časový limit zadaný v [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout) vlastnost jako součást připojovacího řetězce, nebo prostřednictvím [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Vlastní chybová zpráva se může lišit, ale vždy obsahuje hodnotu časového limitu zadaný pro aktuální operaci. 



## <a name="next-steps"></a>Další postup

Pro úplný odkaz na rozhraní .NET API služby Service Bus, najdete v článku [reference k rozhraní Azure .NET API](/dotnet/api/overview/azure/service-bus).

Další informace o [služby Service Bus](https://azure.microsoft.com/services/service-bus/), naleznete v následujících článcích:

* [Přehled přenosu zpráv ve službě Service Bus](service-bus-messaging-overview.md)
* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Architektura služby Service Bus](service-bus-architecture.md)


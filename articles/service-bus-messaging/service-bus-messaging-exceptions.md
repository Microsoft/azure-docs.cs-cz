---
title: Azure Service Bus – výjimky pro zasílání zpráv | Dokumenty společnosti Microsoft
description: Tento článek obsahuje seznam výjimek zasílání zpráv Azure Service Bus a navrhované akce, které mají být podniknuty, když dojde k výjimce.
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80880784"
---
# <a name="service-bus-messaging-exceptions"></a>Výjimky zasílání zpráv služby Service Bus
V tomto článku jsou uvedeny výjimky rozhraní .NET generované rozhraními API rozhraní .NET Framework. 

## <a name="exception-categories"></a>Kategorie výjimek
Zasílání zpráv API generovat výjimky, které mohou spadat do následujících kategorií, spolu s přidruženou akci, kterou můžete provést, aby se pokusili opravit. Význam a příčiny výjimky se mohou lišit v závislosti na typu entity zasílání zpráv:

1. Chyba kódování uživatele ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Obecná akce: pokuste se opravit kód před pokračováním.
2. Chyba instalace/konfigurace ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Obecná akce: zkontrolujte konfiguraci a v případě potřeby ji změňte.
3. Přechodné výjimky ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Obecná akce: opakujte operaci nebo upozorněte uživatele. Třídu `RetryPolicy` v klientské sadě SDK lze nakonfigurovat tak, aby automaticky zpracovávala opakování. Další informace naleznete v [tématu Pokyny k opakování](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Jiné výjimky ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Obecná akce: specifická pro typ výjimky; viz tabulka v následující části: 

## <a name="exception-types"></a>Typy výjimek
V následující tabulce jsou uvedeny typy výjimek zasílání zpráv a jejich příčiny a poznámky navrhované akce, které můžete provést.

| **Typ výjimky** | **Popis/Příčina/Příklady** | **Navrhovaná akce** | **Poznámka k automatickému/okamžitému opakování** |
| --- | --- | --- | --- |
| [Timeoutexception](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Server neodpověděl na požadovanou operaci v zadaném čase, který je řízen [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Server pravděpodobně dokončil požadovanou operaci. Může k tomu dojít z důvodu zpoždění sítě nebo jiné infrastruktury. |Zkontrolujte konzistenci stavu systému a v případě potřeby to opakujte. Viz [Výjimky časového opovencí](#timeoutexception). |V některých případech může pomoct opakování. přidejte logiku opakování do kódu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná operace uživatele není povolena v rámci serveru nebo služby. Podrobnosti naleznete ve zprávě o výjimce. Například [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) generuje tuto výjimku, pokud byla zpráva přijata v režimu [ReceiveAndDelete.](/dotnet/api/microsoft.azure.servicebus.receivemode) |Zkontrolujte kód a dokumentaci. Ujistěte se, že požadovaná operace je platná. |Opakování nepomůže. |
| [Operationcanceledexception](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Je proveden pokus o vyvolání operace na objektu, který již byl uzavřen, přerušen nebo vyřazen. Ve výjimečných případech okolí transakce je již uvolněna. |Zkontrolujte kód a ujistěte se, že nevyvolá operace na vyřazený objekt. |Opakování nepomůže. |
| [Unauthorizedaccessexception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objekt nemohl získat token, token je neplatný nebo token neobsahuje deklarace identity potřebné k provedení operace. |Ujistěte se, že zprostředkovatel tokenu je vytvořen se správnými hodnotami. Zkontrolujte konfiguraci služby řízení přístupu. |V některých případech může pomoct opakování. přidejte logiku opakování do kódu. |
| [Argumentexception](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Argumentnullexception](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Jeden nebo více argumentů dodaných do metody je neplatných.<br /> Identifikátor URI dodaný do [správce oboru namespacemanagernebo](/dotnet/api/microsoft.servicebus.namespacemanager) [create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obsahuje segmenty cesty.<br /> Schéma URI dodané programu [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) je neplatné. <br />Hodnota vlastnosti je větší než 32 KB. |Zkontrolujte volající kód a ujistěte se, že argumenty jsou správné. |Opakování nepomůže. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entita přidružená k operaci neexistuje nebo byla odstraněna. |Ujistěte se, že entita existuje. |Opakování nepomůže. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Pokus o přijetí zprávy s určitým pořadovým číslem. Tato zpráva nebyla nalezena. |Ujistěte se, že zpráva ještě nebyla přijata. Zkontrolujte frontu nedoručených zpráv a zjistěte, zda zpráva byla nedoručená. |Opakování nepomůže. |
| [Výjimka pro komunikaci zpráv](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient není schopen navázat připojení k service bus. |Ujistěte se, že zadaný název hostitele je správný a hostitel je dostupný. |Opakování může pomoci, pokud existují občasné problémy s připojením. |
| [Výjimka ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Služba není schopna zpracovat požadavek v tomto okamžiku. |Klient může čekat po určitou dobu a potom operaci opakovat. |Klient může opakovat po určité interval. Pokud opakování má za následek jinou výjimku, zkontrolujte chování opakování této výjimky. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Platnost tokenu zámku přidruženého ke zprávě vypršela nebo token zámku nebyl nalezen. |Zlikvidujte zprávu. |Opakování nepomůže. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Zámek přidružený k této relaci bude ztracen. |Přerušte objekt [MessageSession.](/dotnet/api/microsoft.servicebus.messaging.messagesession) |Opakování nepomůže. |
| [Výjimka zasílání zpráv](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Obecná výjimka zasílání zpráv, která může být vyvolána v následujících případech:<p>Je proveden pokus o vytvoření [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) pomocí názvu nebo cesty, která patří do jiného typu entity (například téma).</p><p>Došlo k pokusu o odeslání zprávy větší než 256 kB. </p>Server nebo služba zjistila chybu během zpracování požadavku. Podrobnosti naleznete ve zprávě o výjimce. Obvykle je to přechodná výjimka.</p><p>Požadavek byl ukončen, protože entita je omezena. Kód chyby: 50001, 50002, 50008. </p> | Zkontrolujte kód a ujistěte se, že pouze serializovatelné objekty se používají pro text zprávy (nebo použijte vlastní serializátor). <p>V dokumentaci naleznete podporované typy hodnot vlastností a používejte pouze podporované typy.</p><p> Zkontrolujte [istransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) vlastnost. Pokud je to **pravda**, můžete operaci zopakovat. </p>| Pokud je výjimka z důvodu omezení, počkejte několik sekund a opakujte operaci znovu. Opakování chování není definována a nemusí pomoci v jiných scénářích.|
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Pokuste se vytvořit entitu s názvem, který již používá jiná entita v tomto oboru názvů služby. |Odstraňte existující entitu nebo zvolte jiný název entity, která má být vytvořena. |Opakování nepomůže. |
| [Quotaexceededexception](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Entita zasílání zpráv dosáhla maximální povolené velikosti nebo byl překročen maximální počet připojení k oboru názvů. |Vytvořte místo v entitě příjemzpráv od entity nebo jejích podfront. Viz [KvótaExceededException](#quotaexceededexception). |Opakování může pomoci, pokud byly zprávy mezitím odebrány. |
| [Výjimka ruleactionexception](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Služba Service Bus vrátí tuto výjimku, pokud se pokusíte vytvořit neplatnou akci pravidla. Service Bus připojí tuto výjimku k nedoručené zprávy, pokud dojde k chybě při zpracování akce pravidla pro tuto zprávu. |Zkontrolujte správnost akce pravidla. |Opakování nepomůže. |
| [Výjimka filtru](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Služba Service Bus vrátí tuto výjimku, pokud se pokusíte vytvořit neplatný filtr. Service Bus připojí tuto výjimku k nedoručené zprávy, pokud došlo k chybě při zpracování filtru pro tuto zprávu. |Zkontrolujte správnost filtru. |Opakování nepomůže. |
| [SessionCannotLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Pokuste se přijmout relaci s určitým ID relace, ale relace je aktuálně uzamčena jiným klientem. |Ujistěte se, že relace je odemčena jinými klienty. |Opakování může pomoci, pokud relace byla uvolněna v mezidobí. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Součástí transakce je příliš mnoho operací. |Snižte počet operací, které jsou součástí této transakce. |Opakování nepomůže. |
| [Výjimka Pro zasílání zpráv](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Požadavek na operaci runtime na zakázané entitě. |Aktivujte entitu. |Opakování může pomoci, pokud byla entita aktivována v mezidobí. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Služba Service Bus vrátí tuto výjimku, pokud odešlete zprávu na téma, které má povoleno předběžné filtrování a žádný z filtrů se neshoduje. |Ujistěte se, že alespoň jeden filtr odpovídá. |Opakování nepomůže. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Datová část zprávy překračuje limit 256 KB. Limit 256 KB je celková velikost zprávy, která může zahrnovat vlastnosti systému a všechny režie .NET. |Zmenšete velikost datové části zprávy a opakujte operaci. |Opakování nepomůže. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Okolní transakce (*Transaction.Current*) je neplatná. Je možné, že byla dokončena nebo přerušena. Vnitřní výjimka může poskytnout další informace. | |Opakování nepomůže. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Operace je pokus o transakci, která je nejistá, nebo je proveden pokus o potvrzení transakce a transakce se stane pochybností. |Vaše aplikace musí zpracovat tuto výjimku (jako zvláštní případ), protože transakce již byla potvrzena. |- |

## <a name="quotaexceededexception"></a>Quotaexceededexception
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indikuje, že došlo k překročení kvóty pro některou z entit.

### <a name="queues-and-topics"></a>Fronty a témata
U front a témat je často velikost fronty. Vlastnost chybové zprávy obsahuje další podrobnosti, jako v následujícím příkladu:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: 'xxx-xxx-xxx'. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Zpráva uvádí, že téma překročilo limit velikosti, v tomto případě 1 GB (výchozí limit velikosti). 

### <a name="namespaces"></a>Jmenné prostory

Pro obory názvů [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) může znamenat, že aplikace překročila maximální počet připojení k oboru názvů. Příklad:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

### <a name="common-causes"></a>Běžné příčiny
Existují dvě běžné příčiny této chyby: fronty nedoručených zpráv a nefunkční příjemce zpráv.

1. **[Fronta nedoručených zpráv](service-bus-dead-letter-queues.md)** Čtenáři se nedaří dokončit zprávy a zprávy jsou vráceny do fronty nebo tématu při vypršení platnosti zámku. Může se stát, pokud čtenář narazí na výjimku, která mu brání v volání [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Po čtení zprávy 10 krát, přesune do fronty nedoručených zpráv ve výchozím nastavení. Toto chování je [řízeno QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) vlastnost a má výchozí hodnotu 10. Jak se zprávy hromadí ve frontě nedoručených zpráv, zabírají místo.
   
    Chcete-li tento problém vyřešit, přečtěte si a dokončete zprávy z fronty nedoručených zpráv stejně jako z jakékoli jiné fronty. Metodu [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) můžete použít k formátování cesty fronty nedoručených zpráv.
2. **Přijímač se zastavil**. Příjemce přestal přijímat zprávy z fronty nebo předplatného. Způsob, jak to identifikovat, je podívat se na [Vlastnost QueueDescription.MessageCountDetails,](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) která zobrazuje úplné rozdělení zpráv. Pokud [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) vlastnost je vysoká nebo rostoucí, pak zprávy nejsou čteny tak rychle, jak jsou zapsány.

## <a name="timeoutexception"></a>Timeoutexception
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace iniciovaná uživatelem trvá déle než časový časový režim operace. 

Měli byste zkontrolovat hodnotu [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) vlastnost, jako dosažení tohoto limitu může také způsobit [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Fronty a témata
Pro fronty a témata je časový limit zadán buď ve vlastnosti [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) jako součást připojovacího řetězce, nebo prostřednictvím [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Samotná chybová zpráva se může lišit, ale vždy obsahuje hodnotu časového času zadanou pro aktuální operaci. 

## <a name="next-steps"></a>Další kroky
Úplný odkaz rozhraní SERVICE BUS .NET API najdete v [tématu odkaz na rozhraní API Azure .NET](/dotnet/api/overview/azure/service-bus).
Tipy pro řešení potíží naleznete v [průvodci odstraňováním potíží.](service-bus-troubleshooting-guide.md)

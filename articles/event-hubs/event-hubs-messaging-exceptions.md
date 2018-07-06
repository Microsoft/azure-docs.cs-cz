---
title: Výjimky zasílání zpráv služby Azure Event Hubs | Dokumentace Microsoftu
description: Přehled služby Azure Event Hubs messaging výjimky a doporučené akce.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/03/2018
ms.author: sethm
ms.openlocfilehash: 22fcedfdb1921c941250b63f09bfa7fcff9c2f1b
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856701"
---
# <a name="event-hubs-messaging-exceptions"></a>Výjimky zasílání zpráv služby Event Hubs

Tento článek uvádí některé výjimky generované knihovnou služby Azure Service Bus pro zasílání zpráv rozhraní API, která zahrnuje rozhraní API .NET Framework Event Hubs. Tento odkaz se může změnit, proto zkontrolujte novinky.

## <a name="exception-categories"></a>Kategorie výjimek

Rozhraní API služby Event Hubs generovat výjimky, které můžete spadají do následujících kategorií, spolu s přidružené akce, které si můžete vyzkoušet a opravte je.

1. Chyba kódování uživatele: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Obecné akce: Zkuste opravit kód než budete pokračovat.
2. Chyba instalace/konfigurace: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [ System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Obecné akci: Zkontrolujte konfiguraci a v případě potřeby změnit.
3. Přechodným výjimkám: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [ Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Obecné akce: operaci opakovat nebo upozornit uživatele.
4. Ostatní výjimky: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Obecné akce: specifické pro daný typ výjimky Podrobnosti naleznete v tabulce v následující části. 

## <a name="exception-types"></a>Typy výjimek
Následující tabulka uvádí zasílání zpráv typy výjimek a jejich příčiny a poznámky navrhované akce, které můžete provést.

| **Typ výjimky** | **Popis a příčinu a příklady** | **Navrhované akce** | **Poznámka: na automatické/okamžité opakování** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Server neodpověděl na požadovanou operaci v zadaném čase, které řídí [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Server může dokončit požadovanou operaci. Může to být způsobeno sítě nebo jiné infrastruktury zpoždění. |Zkontrolujte stav systému pro zajištění konzistence a opakujte akci v případě potřeby.<br /> Zobrazit [TimeoutException](#timeoutexception). |V některých případech; může pomoci opakování Přidejte do kódu logiku opakování. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná uživatelská operace není povolena v rámci serveru nebo služby. Zobrazit zpráva o výjimce podrobnosti. Například [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) vygeneruje tuto výjimku, pokud byla zpráva přijata v [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu. |Zkontrolujte kód a dokumentace. Ujistěte se, že požadovaná operace je platná. |Opakování nepomůže. |
| [OperationCanceledException –](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Je proveden pokus o vyvolání operace u objektu, který již byl uzavřen, byla přerušena nebo odstraněn. Ve výjimečných případech je již uvolněna okolí transakce. |Zkontrolujte kód a ujistěte se, že vyvolat operace na smazaném objektu. |Opakování nepomůže. |
| [UnauthorizedAccessException –](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objekt nebylo možné získat token, token je neplatný nebo token neobsahuje deklarace potřebné k provedení této operace. |Zajistěte, aby že poskytovatele tokenu, kterého se vytvoří s správné hodnoty. Zkontrolujte konfiguraci služby Řízení přístupu. |V některých případech; může pomoci opakování Přidejte do kódu logiku opakování. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Jeden nebo více argumentů zadaný pro metodu nejsou platné. Identifikátor URI dodaný [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) obsahuje segmenty cesty. Schéma identifikátoru URI předaná [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) je neplatný. Hodnota vlastnosti je větší než 32KB. |Zkontrolujte volající kód a ujistěte se, že argumenty jsou správné. |Opakování nepomůže. |
| [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /> [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) |Entity přidružené operaci neexistuje nebo byl odstraněn. |Ujistěte se, že entita existuje. |Opakování nepomůže. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient není schopen navázat připojení do centra událostí. |Ujistěte se, že je správný název zadaného hostitele a hostitel je dostupný. |Opakování může pomoci, pokud dojde k problémům přerušovaným připojením. |
| [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) |Službu není možné na zpracování žádosti. v tuto chvíli. |Klienta můžete počkat určitou dobu a pak zkuste operaci zopakovat. <br /> Zobrazit [ServerBusyException](#serverbusyexception). |Klient může po určité době opakujte. Pokud opakování vede k jiné výjimce, podívejte se na chování při opakování této výjimky. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Obecná výjimka, která může být vyvolána v následujících případech pro zasílání zpráv: je proveden pokus o vytvoření [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) pomocí názvu nebo cesty, která patří k jiné entitě typu (například téma). Je proveden pokus o odeslání zprávy větší než 256KB. Server nebo službu došlo k chybě při zpracování požadavku. Podrobnosti naleznete ve zprávě podrobnosti. Obvykle se jedná o přechodnou výjimkou. |Zkontrolujte kód a ujistěte se, že pouze serializovatelné objekty se používají pro tělo zprávy (nebo použijte vlastní serializátor). V dokumentaci pro typy podporovaná hodnota vlastnosti a pouze použití podporované typy. Zkontrolujte, [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) vlastnost. Pokud je **true**, můžete operaci opakovat. |Chování při opakování není definována a nemusí nápovědy. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Pokuste se vytvořit entitu s názvem, který se už používá jiná entita v tomto oboru názvů služby. |Odstraňte existující entity nebo vyberte jiný název entity, který se má vytvořit. |Opakování nepomůže. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Entity pro zasílání zpráv dosáhl maximální povolenou velikost. To může nastat, pokud již byl otevřen maximální počet přijímačů (což je 5) na úrovni skupiny – příjemce. |Příjem zpráv z entity nebo jeho dílčí fronty k vytvoření prostoru v dané entitě. <br /> Zobrazit [QuotaExceededException](#quotaexceededexception) |Pokud do té doby byly odebrány zprávy, mohou pomoci při opakování. |
|  | | | |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |Žádost o operaci runtime na entitu, zakázané. |Aktivujte entity. |Pokud entita byla aktivována mezitím, mohou pomoci při opakování. |
| [Microsoft.ServiceBus.Messaging.MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /> [Microsoft.Azure.EventHubs.MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Tělo zprávy překračuje limit 256 kB. Všimněte si, že limit 256 kB je celková velikost zpráv, který může obsahovat systémové vlastnosti a rezervy .NET. |Zmenšit velikost datové části zprávy a potom zkuste operaci zopakovat. |Opakování nepomůže. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) označuje, že byla překročena kvóta pro konkrétní entitu.

To může nastat, pokud již byl otevřen maximální počet přijímačů (5) na úrovni skupiny – příjemce.

### <a name="event-hubs"></a>Event Hubs
Event Hubs má limit 20 skupiny uživatelů na Centrum událostí. Při pokusu o vytvoření další, se zobrazí [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace iniciovaná uživatelem trvá déle, než je časový limit operace. 

Časový limit pro službu Event Hubs je zadán buď jako součást připojovacího řetězce, nebo prostřednictvím [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Vlastní chybová zpráva se může lišit, ale vždy obsahuje hodnotu časového limitu zadaný pro aktuální operaci. 

### <a name="common-causes"></a>Mezi běžné příčiny
Existují dvě běžné příčiny této chyby: nesprávné konfigurace nebo k přechodné chybě.

1. **Nesprávná konfigurace** časový limit operace může být příliš malá pro provozní podmínky. Výchozí hodnota pro vypršení časového limitu operace v Klientská sada SDK je 60 sekund. Zkontrolujte, pokud chcete zobrazit, pokud kód má hodnotu nastavit na příliš malá. Všimněte si, že stav sítě a využití procesoru může ovlivnit čas potřebný pro určitou operaci dokončit, takže časový limit operace by neměla být nastavena na hodnotu velmi malé.
2. **Chyba služby přechodné** někdy služby Event Hubs může docházet k prodlevám při zpracování požadavků, například během období vysoké návštěvnosti. V takových případech můžete opakovat operaci po prodlevě, dokud je operace úspěšná. Pokud stejnou operaci nepodaří ani po několika pokusech, navštivte prosím [web stavu služeb Azure](https://azure.microsoft.com/status/) zobrazíte, pokud jsou známé nějaké výpadky služeb.

## <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) nebo [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) označuje, že je na serveru přetížena. Existují dva příslušných kódů chyb pro tuto výjimku.

### <a name="error-code-50002"></a>Kód chyby: 50002

Této chybě může dojít z jednoho ze dvou důvodů:

1. Zatížení se rovnoměrně mezi všechny oddíly v Centru událostí a omezení na jednotku propustnosti místní narazí na jeden oddíl.
    
    Řešení: Úprava distribuční strategie oddílu ani se nemusíte pokoušet [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) můžou pomoct.

2. Obor názvů služby Event Hubs nemá dostatek jednotek propustnosti (můžete zkontrolovat **metriky** obrazovky okno Hubs oboru názvů v události [webu Azure portal](https://portal.azure.com) potvrďte). Všimněte si, že na portálu se zobrazí informace o agregovaných (1 minuta), ale můžeme měřit propustnost v reálném čase – tak, aby byl pouze odhad.

    Řešení: Zvýšení jednotek propustnosti oboru názvů může pomoct. Můžete provést na portálu v **škálování** okno obrazovky obor názvů služby Event Hubs. Nebo můžete použít [automatické rozšiřování](event-hubs-auto-inflate.md).

### <a name="error-code-50001"></a>Kód chyby: 50001

By měl k této chybě dochází jen zřídka. To se stane, když je nedostatek procesoru kontejneru kódu pro váš obor názvů – začíná není více než několik sekund, než nástroj pro vyrovnávání zatížení služby Event Hubs.


## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

---
title: Výjimky pro zasílání zpráv Azure Service Bus | Microsoft Docs
description: Seznam výjimky zasílání zpráv Service Bus a doporučované akce.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 3d8526fe-6e47-4119-9f3e-c56d916a98f9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/31/2018
ms.author: sethm
ms.openlocfilehash: efcfad2834c2d6775c6693f5c705a0531b2650d6
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28930146"
---
# <a name="service-bus-messaging-exceptions"></a>Výjimky zasílání zpráv Service Bus
V tomto článku jsou uvedené některé výjimky generované zasílání zpráv na úrovni rozhraní API Microsoft Azure Service Bus. Tento odkaz se může změnit, tak to zkuste znovu aktualizací.

## <a name="exception-categories"></a>Výjimka kategorie
Rozhraní API pro zasílání zpráv generování výjimek, které můžete spadají do následujících kategorií, společně s přidružené akce, které můžete provést a zkuste to opravit je. Všimněte si, že významy a způsobí výjimku se může lišit v závislosti na typu entity přenosu zpráv:

1. Uživatel kódování chyby ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Obecné akce: pokusí opravit kód než budete pokračovat.
2. Chyba instalace/konfigurace ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Obecné akce: Zkontrolujte konfiguraci a v případě potřeby změnit.
3. Přechodný výjimky ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Obecné akce: operaci opakovat nebo oznámit uživatelům.
4. Ostatní výjimky ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Obecné akce: specifické pro daný typ výjimky; naleznete v tabulce v následující části. 

## <a name="exception-types"></a>Typy výjimek
Následující tabulka uvádí typy výjimek zasílání zpráv a jejich příčiny a poznámky navrhovaná akce, které můžete provést.

| **Typ výjimky** | **Popis nebo příčina/příklady** | **Navrhovaná akce** | **Poznámka: na automatické, okamžitou opakování** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serveru nereagovala na požadovanou operaci v určeném čase, které řídí [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Server může dokončit požadovanou operaci. K tomu dochází z důvodu sítě nebo jiné infrastruktury zpoždění. |Zkontrolujte stav systému pro konzistence a opakujte v případě potřeby. V tématu [výjimkám časového limitu](#timeoutexception). |Opakování vám může pomoci v některých případech; Přidejte logiku opakovaných pokusů ke kódu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná uživatelská operace není povolena v rámci serveru nebo službě. Zobrazí se zpráva výjimky podrobnosti. Například [Complete()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) vygeneruje výjimku, pokud zpráva byla přijata v [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) režimu. |Zkontrolujte kód a dokumentace. Ujistěte se, že požadovaná operace je platná. |Opakování nepomůže. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Je proveden pokus o vyvolání operace u objektu, který již byl uzavřen, byl zrušen nebo zlikvidován. Ve výjimečných případech je již zveřejněn vedlejším transakce. |Zkontrolujte kód a ujistěte se, že nevyvolá operací na objekt uvolněné. |Opakování nepomůže. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.azure.servicebus.tokenprovider) objekt se nedá získat token, token je neplatný nebo token neobsahuje deklarace identity potřebná k provedení operace. |Ujistěte se, že se vytvoří zprostředkovatel tokenu s správné hodnoty. Zkontrolujte konfiguraci služby Řízení přístupu. |Opakování vám může pomoci v některých případech; Přidejte logiku opakovaných pokusů ke kódu. |
| [ArgumentException –](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Jeden nebo více argumenty metodě jsou neplatné.<br /> Zadaný identifikátor URI do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) obsahuje segment(s) cesta.<br /> Schéma identifikátoru URI zadané [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) je neplatný. <br />Hodnota vlastnosti je větší než 32KB. |Volání kódu a ujistěte se, že jsou správné argumenty. |Opakování nepomůže. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entity přidružené operaci neexistuje nebo byla odstraněna. |Ujistěte se, že entita existuje. |Opakování nepomůže. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Pokuste se zobrazí zpráva s konkrétní pořadové číslo. Tato zpráva nebyla nalezena. |Zkontrolujte, zda že zpráva již nebyl přijat. Zkontrolujte fronty nedoručených zpráv, které chcete zobrazit, pokud zpráva byla deadlettered. |Opakování nepomůže. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient není schopen vytvořit připojení k Service Bus. |Ujistěte se, že je název hostitele zadaný správný a hostitel není dosažitelný. |Opakování mohou pomoci, pokud existují problémy s občasným připojením. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Služba není schopna zpracovat žádost v tuto chvíli. |Klienta můžete čekat na určitou dobu a pak zkuste operaci zopakovat. |Klient může po určité době opakujte. Pokud opakování výsledkem různé výjimky, zkontrolujte opakování chování této výjimky. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Vypršela platnost tokenu zámku spojený se zprávou, nebo nebyl nalezen token zámku. |Dispose – zpráva. |Opakování nepomůže. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Zámek přidružené k této relaci se ztratí. |Přerušení [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) objektu. |Opakování nepomůže. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Obecná zasílání zpráv výjimka, která může být vyvolána v následujících případech:<br /> K pokusu o vytvoření [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) pomocí názvu nebo cestu, která patří do jiné entity typu (například téma).<br />  Je k pokusu o odeslání zprávy větší než 256KB. Název serveru nebo služby došlo k chybě během zpracování požadavku. Podrobnosti viz podrobnosti zpráva o výjimce. Obvykle se jedná o přechodný výjimku. |Zkontrolujte kód a ujistěte se, že pouze serializovatelné objekty se používají pro tělo zprávy (nebo použít vlastní serializátor). Podívejte se do dokumentace pro pouze pomocí podporované typy a typy podporovaných hodnot vlastností. Zkontrolujte [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) vlastnost. Pokud je **true**, mohou zkuste operaci zopakovat. |Postup pro opakované není definován a nemusí pomoci. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Pokus o vytvoření entitu s názvem, který je již využíván jinou entitou v daném oboru názvů služby. |Odstranit stávající entitu, nebo zvolte jiný název pro entity, která má být vytvořen. |Opakování nepomůže. |
| [Quotaexceededexception –](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Zasílání zpráv entity bylo dosaženo jeho maximální povolenou velikost, nebo byl překročen maximální počet připojení k oboru názvů. |Přijímání zpráv z entit nebo jeho dílčí fronty k vytvoření prostoru v entitě. V tématu [quotaexceededexception –](#quotaexceededexception). |Opakování mohou pomoci, pokud do té doby byly odebrány zprávy. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus vrací výjimku, pokud pokusí vytvořit pravidlo neplatná akce. Service Bus připojí k zprávu deadlettered výjimku, pokud dojde k chybě při zpracování akce pravidla pro tuto zprávu. |Zkontrolujte správnost akce pravidla. |Opakování nepomůže. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus vrátí výjimku, pokud se pokusí vytvořit neplatný filtr. Service Bus připojí k zprávu deadlettered výjimku, pokud došlo k chybě při zpracování filtr pro zprávy. |Zkontrolujte správnost filtru. |Opakování nepomůže. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Pokus o přijmout relaci s ID konkrétní relace, ale relace je aktuálně uzamčen jiným klientem. |Zkontrolujte, zda že je relace odemknout pomocí jiných klientů. |Opakování mohou pomoci, pokud relace byla vydána při pokusech. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Příliš mnoho operace jsou součástí transakce. |Snižte počet operací, které jsou součástí této transakce. |Opakování nepomůže. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Žádost o operaci modul runtime na zakázané entity. |Aktivujte entity. |Opakování mohou pomoci, pokud byla aktivována entity dočasně. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus vrací výjimku, pokud odeslání zprávy do tématu, který má povolené předem filtrování a filtry neodpovídají. |Ujistěte se, že odpovídá alespoň jeden filtr. |Opakování nepomůže. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Datovou část zprávy překračuje limit 256 KB. Všimněte si, že limit 256 KB je celková velikost zpráv, které mohou zahrnovat vlastnosti systému a všechny režijní náklady na rozhraní .NET. |Zmenšete velikost datové části zprávy a potom operaci opakujte. |Opakování nepomůže. |
| [TransactionException –](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Vedlejším transakce (*Transaction.Current*) je neplatný. Může být dokončit nebo přerušena. Další informace mohou poskytnout informacích o vnitřní výjimce. | |Opakování nepomůže. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Pokus o operaci v transakci, která je nejistá, nebo je proveden pokus o potvrzení transakce a transakce se změní na nejistých. |Aplikace musí zpracovávat výjimku (jako ve speciálním případě), protože transakce byla pravděpodobně potvrdit. |- |

## <a name="quotaexceededexception"></a>Quotaexceededexception –
[Quotaexceededexception –](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) označuje, že byla překročena kvóta pro konkrétní entitu.

### <a name="queues-and-topics"></a>Fronty a témata
Pro fronty a témata je to často velikost fronty. Vlastnost chybového zpráva obsahuje další podrobnosti, jako v následujícím příkladu:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Zpráva informuje, že tématu překročila limit velikosti v této případu 1 GB (výchozí limit velikosti). 

### <a name="namespaces"></a>Názvové prostory

Pro obory názvů [quotaexceededexception –](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) může naznačovat, že aplikace byla překročena maximální počet připojení k oboru názvů. Příklad:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Běžné příčiny
Existují dvě běžné příčiny této chyby: frontu nedoručených zpráv a nefunkční příjemce zprávy.

1. **[Frontu nedoručených zpráv](service-bus-dead-letter-queues.md)**  čtečka čipových karet se nedaří dokončit zprávy a zprávy jsou vráceny do fronty nebo téma, když vyprší platnost zámek. K tomu může dojít, pokud čtečka zaznamená výjimku, která brání jeho volání [BrokeredMessage.Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Jakmile zprávu byl načten 10krát, přesune se do fronty nedoručených zpráv ve výchozím nastavení. Toto chování je řízeno [QueueDescription.MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) vlastnost a výchozí hodnota je 10. Jako zprávy hromadí do fronty nedoručených zpráv, zabíraly místo.
   
    K vyřešení problému, přečtěte si a dokončení zprávy z fronty nedoručených zpráv, jako byste z jiné fronty. Můžete použít [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) metoda pomohou formátu cesta frontu nedoručených zpráv.
2. **Příjemce zastavena** příjemce byla zastavena, přijímání zpráv z fronty nebo předplatné. Způsob, jak můžete identifikovat toto je prohlédnout [QueueDescription.MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) vlastnosti, která ukazuje úplné rozdělení zpráv. Pokud [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) vlastnost je vysoká nebo rostoucí pak tak rychle, jak se zapisují nejsou při čtení zprávy.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace se uživatel spustil trvá déle, než je časový limit operace. 

Je potřeba zkontrolovat hodnotu [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) vlastnost jako stiskne tento limit, může také způsobit [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

### <a name="queues-and-topics"></a>Fronty a témata
Pro fronty a témata, časový limit zadaný v [MessagingFactorySettings.OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout) vlastnost jako součást připojovacího řetězce, nebo prostřednictvím [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Chybová zpráva, samotné se můžou lišit, ale vždy obsahuje hodnotu časového limitu, zadaný pro aktuální operaci. 



## <a name="next-steps"></a>Další postup

Odkaz na dokončení rozhraní API .NET Service Bus, najdete v článku [referenční dokumentace rozhraní API .NET Azure](/dotnet/api/overview/azure/service-bus).

Další informace o [Service Bus](https://azure.microsoft.com/services/service-bus/), najdete v následujících článcích:

* [Přehled přenosu zpráv ve službě Service Bus](service-bus-messaging-overview.md)
* [Základy služby Service Bus](service-bus-fundamentals-hybrid-solutions.md)
* [Architektura služby Service Bus](service-bus-architecture.md)


---
title: Výjimky pro zasílání zpráv Azure Event Hubs | Microsoft Docs
description: Seznam výjimky a doporučené postupy pro zasílání zpráv Azure Event Hubs.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 2c6273de-0106-47e5-b45d-59040e51f2c5
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: 964475ba8b42ac41707fa78468bfe551677c595f
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2017
ms.locfileid: "26783124"
---
# <a name="event-hubs-messaging-exceptions"></a>Výjimky zasílání zpráv služby Event Hubs

V tomto článku jsou uvedeny některé výjimky generované rozhraní API knihovny zasílání zpráv Azure Service Bus, mezi které patří rozhraní API centra událostí. Tento odkaz se může změnit, tak to zkuste znovu aktualizací.

## <a name="exception-categories"></a>Výjimka kategorie

Rozhraní API centra událostí generování výjimek, které můžete spadají do následujících kategorií, společně s přidružené akce, které můžete provést a zkuste to opravit je.

1. Uživatel kódování Chyba: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Obecné akce: pokusí opravit kód než budete pokračovat.
2. Chyba instalace/konfigurace: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [ System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Obecné akce: Zkontrolujte konfiguraci a v případě potřeby změnit.
3. Přechodný výjimkami: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [ Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Obecné akce: operaci opakovat nebo oznámit uživatelům.
4. Ostatní výjimky: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Obecné akce: specifické pro daný typ výjimky; naleznete v tabulce v následující části. 

## <a name="exception-types"></a>Typy výjimek
Následující tabulka uvádí typy výjimek zasílání zpráv a jejich příčiny a poznámky navrhovaná akce, které můžete provést.

| **Typ výjimky** | **Popis nebo příčina/příklady** | **Navrhovaná akce** | **Poznámka: na automatické, okamžitou opakování** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serveru nereagovala na požadovanou operaci v určeném čase, které řídí [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings#Microsoft_ServiceBus_Messaging_MessagingFactorySettings_OperationTimeout). Server může dokončit požadovanou operaci. K tomu dochází z důvodu sítě nebo jiné infrastruktury zpoždění. |Zkontrolujte stav systému pro konzistence a opakujte v případě potřeby.<br /> V tématu [TimeoutException](#timeoutexception). |Opakování vám může pomoci v některých případech; Přidejte logiku opakovaných pokusů ke kódu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná uživatelská operace není povolena v rámci serveru nebo službě. Zobrazí se zpráva výjimky podrobnosti. Například [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Complete) vygeneruje výjimku, pokud zpráva byla přijata v [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu. |Zkontrolujte kód a dokumentace. Ujistěte se, že požadovaná operace je platná. |Opakování nepomůže. |
| [OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Je proveden pokus o vyvolání operace u objektu, který již byl uzavřen, byl zrušen nebo zlikvidován. Ve výjimečných případech je již zveřejněn vedlejším transakce. |Zkontrolujte kód a ujistěte se, že nevyvolá operací na objekt uvolněné. |Opakování nepomůže. |
| [UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objekt se nedá získat token, token je neplatný nebo token neobsahuje deklarace identity potřebná k provedení operace. |Ujistěte se, že se vytvoří zprostředkovatel tokenu s správné hodnoty. Zkontrolujte konfiguraci služby Řízení přístupu. |Opakování vám může pomoci v některých případech; Přidejte logiku opakovaných pokusů ke kódu. |
| [ArgumentException –](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Výjimka ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Jeden nebo více argumenty metodě jsou neplatné. Zadaný identifikátor URI do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) obsahuje segment(s) cesta. Schéma identifikátoru URI zadané [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory#Microsoft_ServiceBus_Messaging_MessagingFactory_Create_System_Collections_Generic_IEnumerable_System_Uri__) je neplatný. Hodnota vlastnosti je větší než 32KB. |Volání kódu a ujistěte se, že jsou správné argumenty. |Opakování nepomůže. |
| [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /> [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) |Entity přidružené operaci neexistuje nebo byla odstraněna. |Ujistěte se, že entita existuje. |Opakování nepomůže. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient není možné navázat připojení do centra událostí. |Ujistěte se, že je název hostitele zadaný správný a hostitel není dosažitelný. |Opakování mohou pomoci, pokud existují problémy s občasným připojením. |
| [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) |Služba není schopna zpracovat žádost v tuto chvíli. |Klienta můžete čekat na určitou dobu a pak zkuste operaci zopakovat. <br /> V tématu [ServerBusyException](#serverbusyexception). |Klient může po určité době opakujte. Pokud opakování výsledkem různé výjimky, zkontrolujte opakování chování této výjimky. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Obecná zasílání zpráv výjimka, která může být vyvolána v následujících případech: K pokusu o vytvoření [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) pomocí názvu nebo cestu, která patří do jiné entity typu (například téma). Je k pokusu o odeslání zprávy větší než 256KB. Název serveru nebo služby došlo k chybě během zpracování požadavku. Podrobnosti viz podrobnosti zpráva o výjimce. Obvykle se jedná o přechodný výjimku. |Zkontrolujte kód a ujistěte se, že pouze serializovatelné objekty se používají pro tělo zprávy (nebo použít vlastní serializátor). Podívejte se do dokumentace pro pouze pomocí podporované typy a typy podporovaných hodnot vlastností. Zkontrolujte [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception#Microsoft_ServiceBus_Messaging_MessagingException_IsTransient) vlastnost. Pokud je **true**, mohou zkuste operaci zopakovat. |Postup pro opakované není definován a nemusí pomoci. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Pokus o vytvoření entitu s názvem, který je již využíván jinou entitou v daném oboru názvů služby. |Odstranit stávající entitu, nebo zvolte jiný název pro entity, která má být vytvořen. |Opakování nepomůže. |
| [Quotaexceededexception –](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Zasílání zpráv entity bylo dosaženo jeho maximální povolenou velikost. To může dojít, pokud maximální počet příjemců (což je 5) již byla otevřena na úrovni skupiny-příjemce. |Přijímání zpráv z entit nebo jeho dílčí fronty k vytvoření prostoru v entitě. <br /> V tématu [quotaexceededexception –](#quotaexceededexception) |Opakování mohou pomoci, pokud do té doby byly odebrány zprávy. |
|  | | | |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) |Žádost o operaci modul runtime na zakázané entity. |Aktivujte entity. |Opakování mohou pomoci, pokud byla aktivována entity dočasně. |
| [Microsoft.ServiceBus.Messaging.MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /> [Microsoft.Azure.EventHubs.MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Datovou část zprávy překračuje limit 256 kB. Všimněte si, že limit 256 kB je celková velikost zpráv, které mohou zahrnovat vlastnosti systému a všechny režijní náklady na rozhraní .NET. |Zmenšete velikost datové části zprávy a potom operaci opakujte. |Opakování nepomůže. |

## <a name="quotaexceededexception"></a>Quotaexceededexception –
[Quotaexceededexception –](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) označuje, že byla překročena kvóta pro konkrétní entitu.

To může dojít, pokud maximální počet příjemců (5) již byla otevřena na úrovni skupiny-příjemce.

### <a name="event-hubs"></a>Event Hubs
Centra událostí může obsahovat maximálně 20 skupiny příjemců za centra událostí. Při pokusu vytvořit další, obdržíte [quotaexceededexception –](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace se uživatel spustil trvá déle, než je časový limit operace. 

Časový limit pro Event Hubs, je zadána buď jako součást připojovacího řetězce, nebo prostřednictvím [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Chybová zpráva, samotné se můžou lišit, ale vždy obsahuje hodnotu časového limitu, zadaný pro aktuální operaci. 

### <a name="common-causes"></a>Běžné příčiny
Existují dvě běžné příčiny této chyby: nesprávné konfigurace nebo k přechodné chybě.

1. **Nesprávná konfigurace** časový limit operace může být příliš malá pro provozní stav. Výchozí hodnota pro časový limit operace v klientovi SDK je 60 sekund. Zkontrolujte, zda kód obsahuje hodnotu nastaveno něco příliš malá. Všimněte si, že podmínka síť a využití procesoru může ovlivnit dobu potřebnou pro konkrétní operaci dokončit, tak časový limit operace by neměl být nastavený na hodnotu velmi malé.
2. **Přechodné chybě** někdy služby Event Hubs můžete zaznamenat zpoždění při zpracování požadavků, například během období intenzivní provoz. V takových případech může pokus zopakovat operaci po prodlevě, dokud nebude operace úspěšná. Pokud stejné operace stále po několika pokusech selže, podívejte se [lokality stav služby Azure](https://azure.microsoft.com/status/) Pokud jsou k dispozici žádné známé výpadky.

## <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) nebo [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) označuje, že je serveru přetížena. Existují dva kódy chyb relevantní pro tuto výjimku.

### <a name="error-code-50002"></a>Kód chyby 50002

Této chybě může dojít pro jednu ze dvou důvodů:

1. Zatížení není rovnoměrně mezi všechny oddíly v Centru událostí a omezení jednotek propustnosti místní dotkne jeden oddíl.
    
    Řešení: Úprava strategie distribuční oddílu nebo pokusu o [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient#Microsoft_ServiceBus_Messaging_EventHubClient_Send_Microsoft_ServiceBus_Messaging_EventData_) vám může pomoci.

2. Obor názvů služby Event Hubs nemá dostatečná jednotky propustnosti (můžete zkontrolovat **metriky** obrazovky události okno centra oboru názvů v [portál Azure](https://portal.azure.com) k potvrzení). Všimněte si, že portálu zobrazují informace agregované (1 min), ale jsme měření propustnosti v reálném čase – tak, aby byl pouze odhad.

    Řešení: Zvýšení jednotky propustnosti na obor názvů může pomoct. To provedete na portálu, v **škálování** okně na obrazovce oboru názvů služby Event Hubs.

### <a name="error-code-50001"></a>Kód chyby 50001

Tato chyba by měl dochází jen zřídka. Ho se stane, když kontejneru spuštění kódu pro obor názvů je nedostatek procesoru – začne více než pár sekund, než nástroj pro vyrovnávání zatížení služby Event Hubs.


## <a name="next-steps"></a>Další kroky
Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

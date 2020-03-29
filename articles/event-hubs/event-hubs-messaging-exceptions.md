---
title: Průvodce odstraňováním potíží – Azure Event Hubs | Dokumenty společnosti Microsoft
description: Tento článek obsahuje seznam výjimek zasílání zpráv služby Azure Event Hubs a navrhovaných akcí.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.custom: seodec18
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: de5b95bd10bf72f60ba5d63c4b3a799556fcce33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76309774"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Průvodce odstraňováním potíží pro Centra událostí Azure
Tento článek obsahuje některé výjimky rozhraní .NET generované rozhraními API rozhraní .NET Framework .NET hubs a také další tipy pro řešení potíží. 

## <a name="event-hubs-messaging-exceptions---net"></a>Výjimky zasílání zpráv centra událostí - .NET
V této části jsou uvedeny výjimky rozhraní .NET generované rozhraními API rozhraní .NET Framework. 

### <a name="exception-categories"></a>Kategorie výjimek

Rozhraní API centra událostí .NET generují výjimky, které mohou spadat do následujících kategorií, spolu s přidruženou akcí, kterou můžete provést, abyste se je pokusili opravit.

1. Chyba kódování uživatele: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Obecná akce: pokuste se opravit kód před pokračováním.
2. Chyba instalace/konfigurace: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Obecná akce: zkontrolujte konfiguraci a v případě potřeby ji změňte.
3. Přechodné výjimky: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Obecná akce: opakujte operaci nebo upozorněte uživatele.
4. Další výjimky: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Obecná akce: specifická pro typ výjimky; naleznete v tabulce v následující části. 

### <a name="exception-types"></a>Typy výjimek
V následující tabulce jsou uvedeny typy výjimek zasílání zpráv a jejich příčiny a poznámky navrhované akce, které můžete provést.

| Typ výjimky | Popis/Příčina/Příklady | Navrhovaná akce | Poznámka k automatickému/okamžitému opakování |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [Timeoutexception](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Server neodpověděl na požadovanou operaci v zadaném čase, který je řízen [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Server pravděpodobně dokončil požadovanou operaci. K této výjimce může dojít z důvodu zpoždění sítě nebo jiné infrastruktury. |Zkontrolujte konzistenci stavu systému a v případě potřeby to opakujte.<br /> Viz [TimeoutException](#timeoutexception). | V některých případech může pomoct opakování. přidejte logiku opakování do kódu. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná operace uživatele není povolena v rámci serveru nebo služby. Podrobnosti naleznete ve zprávě o výjimce. Například [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) generuje tuto výjimku, pokud byla zpráva přijata v režimu [ReceiveAndDelete.](/dotnet/api/microsoft.servicebus.messaging.receivemode) | Zkontrolujte kód a dokumentaci. Ujistěte se, že požadovaná operace je platná. | Opakování nepomůže. |
| [Operationcanceledexception](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Je proveden pokus o vyvolání operace na objektu, který již byl uzavřen, přerušen nebo vyřazen. Ve výjimečných případech okolí transakce je již uvolněna. | Zkontrolujte kód a ujistěte se, že nevyvolá operace na vyřazený objekt. | Opakování nepomůže. |
| [Unauthorizedaccessexception](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objekt nemohl získat token, token je neplatný nebo token neobsahuje deklarace identity potřebné k provedení operace. | Ujistěte se, že zprostředkovatel tokenu je vytvořen se správnými hodnotami. Zkontrolujte konfiguraci služby řízení přístupu. | V některých případech může pomoct opakování. přidejte logiku opakování do kódu. |
| [Argumentexception](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Argumentnullexception](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Jeden nebo více argumentů dodaných do metody je neplatných. Identifikátor URI dodaný do [správce oboru namespacemanagernebo](/dotnet/api/microsoft.servicebus.namespacemanager) [create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obsahuje segmenty cesty. Schéma URI dodané programu [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) je neplatné. Hodnota vlastnosti je větší než 32 KB. | Zkontrolujte volající kód a ujistěte se, že argumenty jsou správné. | Opakování nepomůže. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Entita přidružená k operaci neexistuje nebo byla odstraněna. | Ujistěte se, že entita existuje. | Opakování nepomůže. |
| [Výjimka pro komunikaci zpráv](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klient není schopen navázat připojení k centru událostí. |Ujistěte se, že zadaný název hostitele je správný a hostitel je dostupný. | Opakování může pomoci, pokud existují občasné problémy s připojením. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Služba není schopna zpracovat požadavek v tuto chvíli. | Klient může čekat po určitou dobu a potom operaci opakovat. <br /> Viz [ServerBusyException](#serverbusyexception). | Klient může opakovat po určité interval. Pokud opakování má za následek jinou výjimku, zkontrolujte chování opakování této výjimky. |
| [Výjimka zasílání zpráv](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Obecná výjimka zasílání zpráv, která může být vyvolána v následujících případech: Pokus o vytvoření [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) pomocí názvu nebo cesty, která patří do jiného typu entity (například téma). Je proveden pokus o odeslání zprávy větší než 1 MB. Server nebo služba zjistila chybu během zpracování požadavku. Podrobnosti naleznete ve zprávě o výjimce. Tato výjimka je obvykle přechodná výjimka. | Zkontrolujte kód a ujistěte se, že pouze serializovatelné objekty se používají pro text zprávy (nebo použijte vlastní serializátor). V dokumentaci naleznete podporované typy hodnot vlastností a používejte pouze podporované typy. Zkontrolujte [istransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) vlastnost. Pokud je to **pravda**, můžete operaci opakovat. | Opakování chování není definována a nemusí pomoci. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Pokuste se vytvořit entitu s názvem, který již používá jiná entita v tomto oboru názvů služby. | Odstraňte existující entitu nebo zvolte jiný název entity, která má být vytvořena. | Opakování nepomůže. |
| [Quotaexceededexception](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Entita zasílání zpráv dosáhla maximální povolené velikosti. K této výjimce může dojít, pokud byl maximální počet přijímačů (což je 5) již otevřen na úrovni skupiny pro příjemce. | Vytvořte místo v entitě příjemzpráv od entity nebo jejích podfront. <br /> Viz [KvótapřekročilaVýjimka](#quotaexceededexception) | Opakování může pomoci, pokud byly zprávy mezitím odebrány. |
| [Výjimka Pro zasílání zpráv](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Požadavek na operaci runtime na zakázané entitě. |Aktivujte entitu. | Opakování může pomoci, pokud byla entita aktivována v mezidobí. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Datová část zprávy překračuje limit 1 MB. Toto omezení 1 MB je pro celkovou zprávu, která může zahrnovat vlastnosti systému a všechny režie .NET. | Zmenšete velikost datové části zprávy a opakujte operaci. |Opakování nepomůže. |

### <a name="quotaexceededexception"></a>Quotaexceededexception
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indikuje, že došlo k překročení kvóty pro některou z entit.

K této výjimce může dojít, pokud byl maximální počet přijímačů (5) již otevřen na úrovni skupiny pro příjemce.

#### <a name="event-hubs"></a>Event Hubs
Event Hubs má limit 20 skupin spotřebitelů na Event Hub. Při pokusu o vytvoření další, obdržíte [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>Timeoutexception
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace iniciovaná uživatelem trvá déle než časový časový režim operace. 

Pro centra událostí je časový limit zadán buď jako součást připojovacího řetězce, nebo prostřednictvím [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Samotná chybová zpráva se může lišit, ale vždy obsahuje hodnotu časového času zadanou pro aktuální operaci. 

#### <a name="common-causes"></a>Běžné příčiny
Existují dvě běžné příčiny této chyby: nesprávná konfigurace nebo chyba přechodné služby.

1. **Nesprávná konfigurace** Časový čas operace může být pro provozní stav příliš malý. Výchozí hodnota časového limitu operace v klientské sadě SDK je 60 sekund. Zkontrolujte, zda váš kód má hodnotu nastavenou na něco příliš malého. Podmínka využití sítě a procesoru může ovlivnit čas potřebný k dokončení určité operace, takže časový limit operace by neměl být nastaven na malou hodnotu.
2. **Chyba přechodné služby** Někdy služba Event Hubs může dojít ke zpoždění při zpracování požadavků; například v obdobích vysokého provozu. V takových případech můžete operaci opakovat po prodlevě, dokud nebude operace úspěšná. Pokud stejná operace stále selže po několika pokusech, navštivte [webu stavu služby Azure](https://azure.microsoft.com/status/) a zjistěte, zda existují nějaké známé výpadky služby.

### <a name="serverbusyexception"></a>Výjimka ServerBusyException

Výjimka [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) nebo [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) označuje přetížení serveru. Pro tuto výjimku existují dva příslušné kódy chyb.

#### <a name="error-code-50002"></a>Kód chyby 50002

K této chybě může dojít z jednoho ze dvou důvodů:

1. Zatížení není rovnoměrně rozloženo napříč všemi oddíly v centru událostí a jeden oddíl zasáhne omezení místní propustné jednotky.
    
    Řešení: Může pomoci revising partition distribution strategy or trying [EventHubClient.Send(eventDataWithOutPartitionKey).](/dotnet/api/microsoft.servicebus.messaging.eventhubclient)

2. Obor názvů Event Hubs nemá dostatečné jednotky propustností (můžete zkontrolovat obrazovku **Metriky** v okně oboru názvů Event Hubs na [webu Azure Portal](https://portal.azure.com) a potvrdit to). Portál zobrazuje souhrnné (1 minutové) informace, ale propustnost měříme v reálném čase – takže je to jen odhad.

    Řešení: Zvýšení propustnost jednotky v oboru názvů může pomoci. Tuto operaci můžete provést na portálu v okně **Škálování** na obrazovce oboru oborů event hubů. Nebo můžete použít [automatické nafouknutí](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Kód chyby 50001

K této chybě by mohlo dojít jen zřídka. Stává se to, když kontejner s kódem pro váš obor názvů je nedostatek procesoru – ne více než několik sekund před zahájením centra událostí.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Omezení volání metody GetRuntimeInformation
Azure Event Hubs podporuje až 50 volání za sekundu GetRuntimeInfo za sekundu. Po dosažení limitu můžete obdržet výjimku podobnou následující:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problémy s připojením, certifikátem nebo časovým časem
Následující kroky vám mohou pomoci s řešením problémů s připojením/certifikátem/časovým časem pro všechny služby v části *.servicebus.windows.net. 

- Přejděte na nebo [wget](https://www.gnu.org/software/wget/) `https://<yournamespacename>.servicebus.windows.net/`. Pomáhá při kontrole, zda máte filtrování IP nebo problémy s virtuální sítí nebo řetězem certifikátů (nejběžnější při použití sady Java SDK).

    Příklad úspěšné zprávy:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Příklad chybové zprávy selhání:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Spusťte následující příkaz a zkontrolujte, zda je v bráně firewall blokován nějaký port. Použité porty jsou 443 (HTTPS), 5671 (AMQP) a 9093 (Kafka). V závislosti na knihovně, kterou používáte, se používají také další porty. Zde je ukázkový příkaz, který kontroluje, zda je port 5671 blokován.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Na Linuxu:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Pokud dochází k občasným problémům s připojením, spusťte následující příkaz a zkontrolujte, zda nejsou nějaké vynecháné pakety. Tento příkaz se pokusí vytvořit 25 různých připojení TCP každých 1 sekundu se službou. Potom můžete zkontrolovat, kolik z nich se podařilo nebo se nezdařilo a také zobrazit latenci připojení TCP. Zde si `psping` můžete stáhnout nástroj [zde](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Ekvivalentní příkazy můžete použít, pokud používáte `tnc`jiné `ping`nástroje, například , a tak dále. 
- Získat trasování do sítě, pokud předchozí kroky nepomáhají a analyzovat pomocí nástrojů, jako je [Wireshark](https://www.wireshark.org/). V případě potřeby kontaktujte [podporu společnosti Microsoft.](https://support.microsoft.com/) 

## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled centra událostí](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

---
title: Průvodce odstraňováním potíží – Azure Event Hubs | Microsoft Docs
description: Tento článek obsahuje seznam výjimky zasílání zpráv služby Azure Event Hubs a doporučené akce.
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
ms.openlocfilehash: 96eaae81a25e361c0041fb02099b8e0cb9da8c28
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76260445"
---
# <a name="troubleshooting-guide-for-azure-event-hubs"></a>Průvodce odstraňováním potíží pro Azure Event Hubs
Tento článek poskytuje některé výjimky .NET vygenerované Event Hubs rozhraní API .NET Framework a také další tipy pro řešení problémů. 

## <a name="event-hubs-messaging-exceptions---net"></a>Výjimky zasílání zpráv Event Hubs – .NET
V této části jsou uvedeny výjimky rozhraní .NET generované rozhraním API .NET Framework. 

### <a name="exception-categories"></a>Kategorie výjimek

Rozhraní API Event Hubs .NET generují výjimky, které mohou být v následujících kategoriích, spolu s přidruženou akcí, kterou můžete provést, abyste je mohli zkusit opravit.

1. Chyba kódování uživatele: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [ System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). Obecné akce: Zkuste opravit kód než budete pokračovat.
2. Chyba instalace/konfigurace: [Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [Microsoft.Azure.EventHubs.MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception), [ System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Obecné akci: Zkontrolujte konfiguraci a v případě potřeby změnit.
3. Přechodným výjimkám: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](#serverbusyexception), [ Microsoft.Azure.EventHubs.ServerBusyException](#serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). Obecné akce: operaci opakovat nebo upozornit uživatele.
4. Ostatní výjimky: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](#timeoutexception), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception). Obecné akce: specifické pro daný typ výjimky naleznete v tabulce v následující části. 

### <a name="exception-types"></a>Typy výjimek
Následující tabulka uvádí zasílání zpráv typy výjimek a jejich příčiny a poznámky navrhované akce, které můžete provést.

| Typ výjimky | Popis a příčinu a příklady | Navrhované akce | Poznámka: na automatické/okamžité opakování |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Server neodpověděl na požadovanou operaci v zadaném čase, který je řízen [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Server může dokončit požadovanou operaci. Tato výjimka může nastat v důsledku zpoždění sítě nebo jiné infrastruktury. |Zkontrolujte stav systému pro zajištění konzistence a opakujte akci v případě potřeby.<br /> Zobrazit [TimeoutException](#timeoutexception). | V některých případech; může pomoci opakování Přidejte do kódu logiku opakování. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná operace uživatele není povolena v rámci serveru nebo služby. Zobrazit zpráva o výjimce podrobnosti. Například [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) vygeneruje tuto výjimku, pokud byla zpráva přijata v [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) režimu. | Zkontrolujte kód a dokumentace. Ujistěte se, že požadovaná operace je platná. | Zkuste to znovu. |
| [OperationCanceledException –](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) | Je proveden pokus o vyvolání operace u objektu, který již byl uzavřen, byla přerušena nebo odstraněn. Ve výjimečných případech je již uvolněna okolí transakce. | Zkontrolujte kód a ujistěte se, že nevyvolává operace u uvolněného objektu. | Zkuste to znovu. |
| [UnauthorizedAccessException –](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) | Objekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nemohl získat token, token je neplatný nebo token neobsahuje deklarace identity vyžadované k provedení operace. | Zajistěte, aby že poskytovatele tokenu, kterého se vytvoří s správné hodnoty. Ověřte konfiguraci Access Control Service. | V některých případech; může pomoci opakování Přidejte do kódu logiku opakování. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) | Jeden nebo více argumentů zadaný pro metodu nejsou platné. Identifikátor URI dodaný [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obsahuje segmenty cesty. Schéma identifikátoru URI předaná [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) je neplatný. Hodnota vlastnosti je větší než 32 KB. | Zkontrolujte volající kód a ujistěte se, že argumenty jsou správné. | Opakování nepomůže. |
| [Microsoft.ServiceBus.Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft.Azure.EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Entity přidružené operaci neexistuje nebo byl odstraněn. | Ujistěte se, že entita existuje. | Opakování nepomůže. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klient není schopen navázat připojení do centra událostí. |Ujistěte se, že je správný název zadaného hostitele a hostitel je dostupný. | Opakování může pomoci, pokud dojde k problémům přerušovaným připojením. |
| [Microsoft.ServiceBus.Messaging ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft.Azure.EventHubs ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Službu není možné na zpracování žádosti. v tuto chvíli. | Klienta můžete počkat určitou dobu a pak zkuste operaci zopakovat. <br /> Zobrazit [ServerBusyException](#serverbusyexception). | Klient může po určité době opakujte. Pokud opakování vede k jiné výjimce, podívejte se na chování při opakování této výjimky. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Obecná výjimka, která může být vyvolána v následujících případech pro zasílání zpráv: je proveden pokus o vytvoření [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) pomocí názvu nebo cesty, která patří k jiné entitě typu (například téma). Byl proveden pokus o odeslání zprávy větší než 1 MB. Server nebo službu došlo k chybě při zpracování požadavku. Zobrazit zpráva o výjimce podrobnosti. Tato výjimka je obvykle s přechodnou výjimkou. | Zkontrolujte kód a ujistěte se, že pouze serializovatelné objekty se používají pro tělo zprávy (nebo použijte vlastní serializátor). V dokumentaci pro typy podporovaná hodnota vlastnosti a pouze použití podporované typy. Zkontrolujte, [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) vlastnost. Pokud je **true**, můžete operaci opakovat. | Chování při opakování není definována a nemusí nápovědy. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Pokuste se vytvořit entitu s názvem, který se už používá jiná entita v tomto oboru názvů služby. | Odstraňte existující entity nebo vyberte jiný název entity, který se má vytvořit. | Opakování nepomůže. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Entity pro zasílání zpráv dosáhl maximální povolenou velikost. Tato výjimka může dojít, pokud již bylo otevřeno maximální počet přijímačů (což je 5) na úrovni skupiny – příjemce. | Příjem zpráv z entity nebo jeho podfronty k vytvoření prostoru v dané entitě. <br /> Zobrazit [QuotaExceededException](#quotaexceededexception) | Pokud do té doby byly odebrány zprávy, mohou pomoci při opakování. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Žádost o operaci runtime na entitu, zakázané. |Aktivujte entity. | Pokud entita byla aktivována mezitím, mohou pomoci při opakování. |
| [Microsoft.ServiceBus.Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft.Azure.EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Datová část zprávy překračuje limit 1 MB. Tento limit 1 MB slouží jako celková zpráva, která může zahrnovat systémové vlastnosti a jakékoli režie .NET. | Zmenšit velikost datové části zprávy a potom zkuste operaci zopakovat. |Opakování nepomůže. |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indikuje, že došlo k překročení kvóty pro některou z entit.

Tato výjimka může dojít, pokud již bylo otevřeno maximální počet přijímačů (5) na úrovni skupiny – příjemce.

#### <a name="event-hubs"></a>Event Hubs
Event Hubs má limit 20 skupiny uživatelů na Centrum událostí. Při pokusu o vytvoření další, se zobrazí [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

### <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace iniciovaná uživatelem trvá déle, než je časový limit operace. 

Časový limit pro službu Event Hubs je zadán buď jako součást připojovacího řetězce, nebo prostřednictvím [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Vlastní chybová zpráva se může lišit, ale vždy obsahuje hodnotu časového limitu zadaný pro aktuální operaci. 

#### <a name="common-causes"></a>Mezi běžné příčiny
Existují dvě běžné příčiny této chyby: nesprávné konfigurace nebo k přechodné chybě.

1. **Nesprávná konfigurace** časový limit operace může být příliš malá pro provozní podmínky. Výchozí hodnota pro vypršení časového limitu operace v Klientská sada SDK je 60 sekund. Zkontrolujte, pokud chcete zobrazit, pokud kód má hodnotu nastavit na příliš malá. Stav sítě a využití procesoru může ovlivnit dobu potřebnou k dokončení konkrétní operace, takže časový limit operace by neměl být nastaven na malou hodnotu.
2. **Chyba služby přechodné** někdy služby Event Hubs může docházet k prodlevám při zpracování požadavků, například během období vysoké návštěvnosti. V takových případech můžete opakovat operaci po prodlevě, dokud je operace úspěšná. Pokud stejnou operaci nepodaří ani po několika pokusech, přejděte [web stavu služeb Azure](https://azure.microsoft.com/status/) zobrazíte, pokud jsou známé nějaké výpadky služeb.

### <a name="serverbusyexception"></a>ServerBusyException

A [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) nebo [Microsoft.Azure.EventHubs.ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) označuje, že je na serveru přetížena. Existují dva příslušných kódů chyb pro tuto výjimku.

#### <a name="error-code-50002"></a>Kód chyby: 50002

Této chybě může dojít z jednoho ze dvou důvodů:

1. Zatížení se rovnoměrně nedistribuuje napříč všemi oddíly v centru událostí a jeden oddíl má omezení místní jednotky propustnosti.
    
    Řešení: Úprava distribuční strategie oddílu ani se nemusíte pokoušet [EventHubClient.Send(eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) můžou pomoct.

2. Obor názvů Event Hubs nemá dostatečný počet jednotek propustnosti (můžete zaškrtnout obrazovku **metriky** v okně Event Hubs oboru názvů v [Azure Portal](https://portal.azure.com) pro potvrzení). Portál zobrazuje agregované informace (1 minuty), ale měříme propustnost v reálném čase, takže se jedná o pouze odhad.

    Řešení: Zvýšení jednotek propustnosti oboru názvů může pomoct. Tuto operaci na portálu, můžete provést v **škálování** okno obrazovky obor názvů služby Event Hubs. Nebo můžete použít [automatické rozšiřování](event-hubs-auto-inflate.md).

#### <a name="error-code-50001"></a>Kód chyby: 50001

By měl k této chybě dochází jen zřídka. To se stane, když je nedostatek procesoru kontejneru kódu pro váš obor názvů – začíná není více než několik sekund, než nástroj pro vyrovnávání zatížení služby Event Hubs.

#### <a name="limit-on-calls-to-the-getruntimeinformation-method"></a>Omezení volání metody Getruntimeinformation –
Azure Event Hubs podporuje až 50 volání za sekundu do GetRuntimeInfo za sekundu. Po dosažení limitu se může zobrazit výjimka podobná té následující:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```

## <a name="connectivity-certificate-or-timeout-issues"></a>Problémy s připojením, certifikátem nebo časovým limitem
Následující postup vám může pomáhat s odstraňováním potíží s připojením/vypršením časového limitu pro všechny služby v rámci *. servicebus.windows.net. 

- Vyhledejte `https://<yournamespacename>.servicebus.windows.net/`nebo [wget](https://www.gnu.org/software/wget/) . Pomáhá s kontrolou, jestli máte problémy s filtrováním IP adres nebo virtuální sítí nebo s řetězem certifikátů (nejběžnější při použití sady Java SDK).
- Spusťte následující příkaz, který zkontroluje, jestli je v bráně firewall blokovaný port. Použité porty jsou 443 (HTTPS), 5671 (AMQP) a 9093 (Kafka). V závislosti na knihovně, kterou používáte, se používají také jiné porty. Tady je ukázkový příkaz, který zkontroluje, jestli je port 5671 blokovaný.

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    V Linuxu:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
    
    Příklad úspěšné zprávy:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    Příklad chybové zprávy o selhání:

    ```json
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- Pokud dochází k problémům s přerušovaným připojením, spusťte následující příkaz, který zkontroluje, jestli nedošlo k vyřazeným paketům. Tento příkaz se pokusí vytvořit 25 různých připojení TCP každé 1 sekundy ke službě. Pak můžete zjistit, kolik z nich bylo úspěšné/neúspěšné, a také zobrazit latenci připojení TCP. `psping` nástroj si můžete stáhnout [tady](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
    ```
    Ekvivalentní příkazy můžete použít, pokud používáte jiné nástroje, například `tnc`, `ping`a tak dále. 
- Získejte trasování sítě, pokud předchozí kroky neumožňují a neanalyzují ho pomocí nástrojů, jako je třeba [Wireshark](https://www.wireshark.org/). V případě potřeby kontaktujte [Podpora Microsoftu](https://support.microsoft.com/) . 

## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled služby Event Hubs](event-hubs-what-is-event-hubs.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

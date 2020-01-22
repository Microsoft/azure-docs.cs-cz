---
title: Průvodce odstraňováním potíží pro Azure Service Bus | Microsoft Docs
description: Seznam Service Bus výjimek zasílání zpráv a navrhovaných akcí
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
ms.date: 01/17/2020
ms.author: aschhab
ms.openlocfilehash: 20d9fc141fa19a5c6d01f33c2a5398ca84497b9f
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2020
ms.locfileid: "76309961"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Průvodce odstraňováním potíží pro Azure Service Bus
Tento článek poskytuje některé výjimky .NET vygenerované Service Bus rozhraní API .NET Framework a také další tipy pro řešení problémů. 

## <a name="service-bus-messaging-exceptions"></a>Výjimky zasílání zpráv Service Bus
V této části jsou uvedeny výjimky rozhraní .NET generované rozhraním API .NET Framework. 

### <a name="exception-categories"></a>Kategorie výjimek
Rozhraní API pro zasílání zpráv generují výjimky, které mohou být v následujících kategoriích, spolu s přidruženou akcí, kterou můžete provést při pokusu o jejich opravu. Význam a příčiny výjimky se mohou lišit v závislosti na typu entity zasílání zpráv:

1. Chyba kódování uživatele ([System. ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System. InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System. OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System. Runtime. Serialization. SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Obecné akce: Zkuste opravit kód než budete pokračovat.
2. Chyba nastavení/konfigurace ([Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Obecné akci: Zkontrolujte konfiguraci a v případě potřeby změnit.
3. Přechodné výjimky ([Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. výjimka serverbusyexception](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Obecné akce: operaci opakovat nebo upozornit uživatele. Třídu `RetryPolicy` v klientské sadě SDK lze nakonfigurovat tak, aby zpracovávala opakované pokusy automaticky. Další informace najdete v tématu [pokyny pro opakování](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Další výjimky ([System. Transactions. TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System. TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Obecná akce: specifická pro typ výjimky; informace najdete v tabulce v následující části: 

### <a name="exception-types"></a>Typy výjimek
Následující tabulka uvádí zasílání zpráv typy výjimek a jejich příčiny a poznámky navrhované akce, které můžete provést.

| **Typ výjimky** | **Popis/příčiny/příklady** | **Navrhovaná akce** | **Poznámka k automatickému/okamžitému opakování** |
| --- | --- | --- | --- |
| [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Server neodpověděl na požadovanou operaci v zadaném čase, který je řízen [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Server může dokončit požadovanou operaci. K tomu může dojít z důvodu zpoždění sítě nebo jiné infrastruktury. |Zkontrolujte stav systému pro zajištění konzistence a opakujte akci v případě potřeby. Viz [výjimky časového limitu](#timeoutexception). |V některých případech; může pomoci opakování Přidejte do kódu logiku opakování. |
| [InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná operace uživatele není povolena v rámci serveru nebo služby. Zobrazit zpráva o výjimce podrobnosti. Například [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) vygeneruje tuto výjimku, pokud byla zpráva přijata v režimu [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) . |Zkontrolujte kód a dokumentace. Ujistěte se, že požadovaná operace je platná. |Nemůžete to zkusit znovu. |
| [OperationCanceledException –](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Je proveden pokus o vyvolání operace u objektu, který již byl uzavřen, byla přerušena nebo odstraněn. Ve výjimečných případech je již uvolněna okolí transakce. |Zkontrolujte kód a ujistěte se, že nevyvolává operace u uvolněného objektu. |Nemůžete to zkusit znovu. |
| [UnauthorizedAccessException –](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |Objekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nemohl získat token, token je neplatný nebo token neobsahuje deklarace identity vyžadované k provedení operace. |Zajistěte, aby že poskytovatele tokenu, kterého se vytvoří s správné hodnoty. Ověřte konfiguraci Access Control Service. |V některých případech; může pomoci opakování Přidejte do kódu logiku opakování. |
| [ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [ArgumentNullException](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[ArgumentOutOfRangeException](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Jeden nebo více argumentů zadaný pro metodu nejsou platné.<br /> Identifikátor URI dodaný [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obsahuje segmenty cesty.<br /> Schéma identifikátoru URI předaná [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) je neplatný. <br />Hodnota vlastnosti je větší než 32 KB. |Zkontrolujte volající kód a ujistěte se, že argumenty jsou správné. |Nemůžete to zkusit znovu. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entita přidružená k operaci neexistuje nebo byla odstraněna. |Ujistěte se, že entita existuje. |Nemůžete to zkusit znovu. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Došlo k pokusu o přijetí zprávy s určitým pořadovým číslem. Tato zpráva se nenašla. |Ujistěte se, že zpráva již nebyla přijata. Zkontrolujte frontu nedoručených zpráv a podívejte se, zda byla zpráva deadlettered. |Nemůžete to zkusit znovu. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient nemůže navázat připojení k Service Bus. |Ujistěte se, že je správný název zadaného hostitele a hostitel je dostupný. |Opakování může pomoci, pokud dojde k problémům přerušovaným připojením. |
| [ServerBusyException](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Služba v tuto chvíli nemůže zpracovat požadavek. |Klienta můžete počkat určitou dobu a pak zkuste operaci zopakovat. |Klient může po určité době opakujte. Pokud opakování vede k jiné výjimce, podívejte se na chování při opakování této výjimky. |
| [MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception) |Platnost tokenu zámku přidruženého ke zprávě vypršela nebo se token zámku nenašel. |Vyřadit zprávu. |Nemůžete to zkusit znovu. |
| [SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception) |Zámek přidružený k této relaci je ztracený. |Přerušit objekt [MessageSession](/dotnet/api/microsoft.servicebus.messaging.messagesession) . |Nemůžete to zkusit znovu. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) |Obecná výjimka zasílání zpráv, která může být vyvolána v následujících případech:<br /> Byl proveden pokus o vytvoření [QueueClient](/dotnet/api/microsoft.azure.servicebus.queueclient) pomocí názvu nebo cesty, která patří k jinému typu entity (například téma).<br />  Je proveden pokus o odeslání zprávy větší než 256 KB. Server nebo službu došlo k chybě při zpracování požadavku. Zobrazit zpráva o výjimce podrobnosti. Obvykle se jedná o přechodnou výjimku. |Zkontrolujte kód a ujistěte se, že pouze serializovatelné objekty se používají pro tělo zprávy (nebo použijte vlastní serializátor). V dokumentaci pro typy podporovaná hodnota vlastnosti a pouze použití podporované typy. Zkontrolujte, [IsTransient](/dotnet/api/microsoft.servicebus.messaging.messagingexception) vlastnost. Pokud je to **pravda**, můžete operaci zopakovat. |Chování při opakování není definována a nemusí nápovědy. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) |Pokuste se vytvořit entitu s názvem, který se už používá jiná entita v tomto oboru názvů služby. |Odstraňte existující entity nebo vyberte jiný název entity, který se má vytvořit. |Nemůžete to zkusit znovu. |
| [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) |Entita zasílání zpráv dosáhla maximální povolené velikosti nebo byl překročen maximální počet připojení k oboru názvů. |Příjem zpráv z entity nebo jeho podfronty k vytvoření prostoru v dané entitě. Viz [QuotaExceededException](#quotaexceededexception). |Pokud do té doby byly odebrány zprávy, mohou pomoci při opakování. |
| [RuleActionException](/dotnet/api/microsoft.servicebus.messaging.ruleactionexception) |Service Bus vrátí tuto výjimku, pokud se pokusíte vytvořit neplatnou akci pravidla. Service Bus připojí tuto výjimku ke zprávě deadlettered, pokud dojde k chybě při zpracování akce pravidla pro tuto zprávu. |Ověřte správnost akce pravidla. |Nemůžete to zkusit znovu. |
| [FilterException](/dotnet/api/microsoft.servicebus.messaging.filterexception) |Service Bus vrátí tuto výjimku, pokud se pokusíte vytvořit neplatný filtr. Service Bus připojí tuto výjimku ke zprávě deadlettered, pokud došlo k chybě při zpracování filtru pro tuto zprávu. |Ověřte správnost filtru. |Nemůžete to zkusit znovu. |
| [SessionCannotBeLockedException](/dotnet/api/microsoft.servicebus.messaging.sessioncannotbelockedexception) |Došlo k pokusu o přijetí relace s určitým ID relace, ale relace je aktuálně uzamčena jiným klientem. |Ujistěte se, že je relace odemčena ostatními klienty. |Zkuste to znovu, pokud byla relace vydaná v provizorním režimu. |
| [TransactionSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.transactionsizeexceededexception) |Transakce je příliš mnoho operací. |Snižte počet operací, které jsou součástí této transakce. |Nemůžete to zkusit znovu. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.azure.servicebus.messagingentitydisabledexception) |Žádost o operaci runtime na entitu, zakázané. |Aktivujte entity. |Pokud entita byla aktivována mezitím, mohou pomoci při opakování. |
| [NoMatchingSubscriptionException](/dotnet/api/microsoft.servicebus.messaging.nomatchingsubscriptionexception) |Service Bus vrátí tuto výjimku, pokud odešlete zprávu do tématu s povoleným předfiltrem a žádný z filtrů neodpovídá. |Ujistěte se, že alespoň jeden filtr odpovídá. |Nemůžete to zkusit znovu. |
| [MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Datová část zprávy překračuje limit 256 KB. Limit 256-KB je celková velikost zprávy, která může zahrnovat systémové vlastnosti a veškeré režie technologie .NET. |Zmenšit velikost datové části zprávy a potom zkuste operaci zopakovat. |Nemůžete to zkusit znovu. |
| [TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx) |Ambientní transakce (*Transaction. Current*) je neplatná. Je možné, že byla dokončena nebo přerušena. Vnitřní výjimka může poskytovat další informace. | |Nemůžete to zkusit znovu. |
| [TransactionInDoubtException](https://msdn.microsoft.com/library/system.transactions.transactionindoubtexception.aspx) |Došlo k pokusu o provedení operace v transakci, která je nejistá, nebo dojde k pokusu o potvrzení transakce a transakce se stane nejistými. |Vaše aplikace musí tuto výjimku zpracovat (jako zvláštní případ), protože transakce již byla potvrzena. |- |

### <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) indikuje, že došlo k překročení kvóty pro některou z entit.

#### <a name="queues-and-topics"></a>Fronty a témata
U front a témat je často velikost fronty. Vlastnost chybová zpráva obsahuje další podrobnosti, jako v následujícím příkladu:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException
Message: The maximum entity size has been reached or exceeded for Topic: ‘xxx-xxx-xxx’. 
    Size of entity in bytes:1073742326, Max entity size in bytes:
1073741824..TrackingId:xxxxxxxxxxxxxxxxxxxxxxxxxx, TimeStamp:3/15/2013 7:50:18 AM
```

Zpráva uvádí, že téma překročilo omezení velikosti, v tomto případě 1 GB (výchozí omezení velikosti). 

#### <a name="namespaces"></a>Obory názvů

Pro obory názvů může [QuotaExceededException](/dotnet/api/microsoft.azure.servicebus.quotaexceededexception) značit, že aplikace překročila maximální počet připojení k oboru názvů. Například:

```Output
Microsoft.ServiceBus.Messaging.QuotaExceededException: ConnectionsQuotaExceeded for namespace xxx.
<tracking-id-guid>_G12 ---> 
System.ServiceModel.FaultException`1[System.ServiceModel.ExceptionDetail]: 
ConnectionsQuotaExceeded for namespace xxx.
```

#### <a name="common-causes"></a>Mezi běžné příčiny
Existují dva běžné příčiny této chyby: fronta nedoručených zpráv a přijímače nefunkčních zpráv.

1. **[Fronta nedoručených zpráv](service-bus-dead-letter-queues.md)** U čtecího modulu se nedaří dokončit zprávy a zprávy jsou po vypršení zámku vráceny do fronty nebo tématu. K tomu může dojít, pokud Čtenář narazí na výjimku, která zabrání v volání [BrokeredMessage. Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete). Po 10 časech se zpráva ve výchozím nastavení přesune do fronty nedoručených zpráv. Toto chování je řízeno vlastností [QueueDescription. MaxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount) a má výchozí hodnotu 10. Vzhledem k tomu, že se zprávy dostanou ve frontě nedoručených zpráv, zabírají místo.
   
    Chcete-li tento problém vyřešit, přečtěte si a dokončete zprávy z fronty nedoručených zpráv, stejně jako z jakékoli jiné fronty. Můžete použít metodu [FormatDeadLetterPath](/dotnet/api/microsoft.azure.servicebus.entitynamehelper.formatdeadletterpath) , která vám pomůžou naformátovat cestu fronty nedoručených zpráv.
2. **Přijímač byl zastaven**. Příjemce zastavil přijímání zpráv z fronty nebo odběru. Způsob, jak to zjistit, je podívat se na vlastnost [QueueDescription. MessageCountDetails](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails) , která zobrazuje úplné rozpis zpráv. Pokud je vlastnost [ActiveMessageCount](/dotnet/api/microsoft.servicebus.messaging.messagecountdetails.activemessagecount) vysoká nebo roste, zprávy nejsou čteny tak rychle, jak jsou zapisovány.

### <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace iniciovaná uživatelem trvá déle, než je časový limit operace. 

Měli byste zaškrtnout hodnotu vlastnosti [Třída ServicePointManager. DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) , protože po dosažení tohoto limitu může také dojít k [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

#### <a name="queues-and-topics"></a>Fronty a témata
V případě front a témat je časový limit zadán buď ve vlastnosti [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) jako součást připojovacího řetězce, nebo prostřednictvím [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Vlastní chybová zpráva se může lišit, ale vždy obsahuje hodnotu časového limitu zadaný pro aktuální operaci. 

## <a name="connectivity-certificate-or-timeout-issues"></a>Problémy s připojením, certifikátem nebo časovým limitem
Následující postup vám může pomáhat s odstraňováním potíží s připojením/vypršením časového limitu pro všechny služby v rámci *. servicebus.windows.net. 

- Vyhledejte `https://<yournamespace>.servicebus.windows.net/`nebo [wget](https://www.gnu.org/software/wget/) . Pomáhá s kontrolou, jestli máte problémy s filtrováním IP adres nebo virtuální sítí nebo s řetězem certifikátů (nejběžnější při použití sady Java SDK).

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
- Spusťte následující příkaz, který zkontroluje, jestli je v bráně firewall blokovaný port. Použité porty jsou 443 (HTTPS), 5671 (AMQP) a 9354 (NET Messaging/SBMP). V závislosti na knihovně, kterou používáte, se používají také jiné porty. Tady je ukázkový příkaz, který zkontroluje, jestli je port 5671 blokovaný. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    V Linuxu:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- Pokud dochází k problémům s přerušovaným připojením, spusťte následující příkaz, který zkontroluje, jestli nedošlo k vyřazeným paketům. Tento příkaz se pokusí vytvořit 25 různých připojení TCP každé 1 sekundy ke službě. Pak můžete zjistit, kolik z nich bylo úspěšné/neúspěšné, a také zobrazit latenci připojení TCP. `psping` nástroj si můžete stáhnout [tady](/sysinternals/downloads/psping).

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    Ekvivalentní příkazy můžete použít, pokud používáte jiné nástroje, například `tnc`, `ping`a tak dále. 
- Získejte trasování sítě, pokud předchozí kroky neumožňují a neanalyzují ho pomocí nástrojů, jako je třeba [Wireshark](https://www.wireshark.org/). V případě potřeby kontaktujte [Podpora Microsoftu](https://support.microsoft.com/) . 


## <a name="next-steps"></a>Další kroky

Kompletní referenční informace k rozhraní .NET API pro Service Bus najdete v [referenčních informacích k rozhraní Azure .NET API](/dotnet/api/overview/azure/service-bus).

Další informace o [Service Bus](https://azure.microsoft.com/services/service-bus/)najdete v následujících článcích:

* [Přehled přenosu zpráv ve službě Service Bus](service-bus-messaging-overview.md)
* [Architektura služby Service Bus](service-bus-architecture.md)


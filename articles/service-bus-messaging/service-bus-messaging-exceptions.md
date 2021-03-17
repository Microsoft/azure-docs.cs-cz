---
title: Azure Service Bus – výjimky zasílání zpráv | Microsoft Docs
description: Tento článek poskytuje seznam výjimek zasílání zpráv Azure Service Bus a navrhovaných akcí, které se mají učinit, když dojde k výjimce.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 3b56aff2635593d6cb49adbcf3784ddd5cb4fa39
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2021
ms.locfileid: "99219141"
---
# <a name="service-bus-messaging-exceptions"></a>Service Bus výjimky zasílání zpráv
Tento článek obsahuje seznam výjimek rozhraní .NET generovaných rozhraními API .NET Framework. 

## <a name="exception-categories"></a>Kategorie výjimek
Rozhraní API pro zasílání zpráv generují výjimky, které mohou být v následujících kategoriích, spolu s přidruženou akcí, kterou můžete provést při pokusu o jejich opravu. Význam a příčiny výjimky se mohou lišit v závislosti na typu entity zasílání zpráv:

1. Chyba kódování uživatele ([System. ArgumentException](/dotnet/api/system.argumentexception), [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception), [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception), [System. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception)). Obecná akce: Zkuste opravit kód, než budete pokračovat.
2. Chyba nastavení/konfigurace ([Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception), [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). Obecná akce: Zkontrolujte konfiguraci a v případě potřeby proveďte změny.
3. Přechodné výjimky ([Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. výjimka serverbusyexception](/dotnet/api/microsoft.azure.servicebus.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)). Obecná akce: zkuste operaci zopakovat nebo upozorněte uživatele. `RetryPolicy`Třídu v klientské sadě SDK lze nakonfigurovat tak, aby zpracovávala opakované pokusy automaticky. Další informace najdete v tématu [pokyny pro opakování](/azure/architecture/best-practices/retry-service-specific#service-bus).
4. Další výjimky ([System. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception), [System. TimeoutException](/dotnet/api/system.timeoutexception), [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.azure.servicebus.messagelocklostexception), [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.azure.servicebus.sessionlocklostexception)). Obecná akce: specifická pro typ výjimky; informace najdete v tabulce v následující části: 

## <a name="exception-types"></a>Typy výjimek
V následující tabulce jsou uvedeny typy výjimek zasílání zpráv a jejich příčiny a poznámky navrhovaná akce, které můžete provést.

| **Typ výjimky** | **Popis/příčiny/příklady** | **Navrhovaná akce** | **Poznámka k automatickému/okamžitému opakování** |
| --- | --- | --- | --- |
| [TimeoutException](/dotnet/api/system.timeoutexception) |Server neodpověděl na požadovanou operaci v zadaném čase, který je řízen [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Je možné, že server dokončil požadovanou operaci. K tomu může dojít z důvodu zpoždění sítě nebo jiné infrastruktury. |Ověřte konzistenci stavu systému a v případě potřeby akci opakujte. Viz [výjimky časového limitu](#timeoutexception). |V některých případech může být užitečné zkusit to znovu. Přidejte do kódu logiku opakování. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception) |Požadovaná operace uživatele není povolena v rámci serveru nebo služby. Podrobnosti najdete ve zprávě výjimky. Například [Complete ()](/dotnet/api/microsoft.azure.servicebus.queueclient.completeasync) vygeneruje tuto výjimku, pokud byla zpráva přijata v režimu [ReceiveAndDelete](/dotnet/api/microsoft.azure.servicebus.receivemode) . |Podívejte se na kód a dokumentaci. Ujistěte se, že požadovaná operace je platná. |Nemůžete to zkusit znovu. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception) |Došlo k pokusu o vyvolání operace u objektu, který již byl uzavřen, přerušen nebo vyřazen. Ve výjimečných případech je ambientní transakce již uvolněna. |Zkontrolujte kód a ujistěte se, že nevyvolává operace u uvolněného objektu. |Nemůžete to zkusit znovu. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception) |Objekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nemohl získat token, token je neplatný nebo token neobsahuje deklarace identity vyžadované k provedení operace. |Ujistěte se, že Poskytovatel tokenů je vytvořený se správnými hodnotami. Ověřte konfiguraci Access Control Service. |V některých případech může být užitečné zkusit to znovu. Přidejte do kódu logiku opakování. |
| [ArgumentException](/dotnet/api/system.argumentexception)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception) |Jeden nebo více argumentů dodaných metodě je neplatných.<br /> Identifikátor URI zadaný pro [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obsahuje segmenty cesty.<br /> Schéma identifikátoru URI zadané pro [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Vytvoření](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) je neplatné. <br />Hodnota vlastnosti je větší než 32 KB. |Zkontrolujte kód volajícího a ujistěte se, že jsou argumenty správné. |Nemůžete to zkusit znovu. |
| [MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.servicebus.messagingentitynotfoundexception) |Entita přidružená k operaci neexistuje nebo byla odstraněna. |Ujistěte se, že entita existuje. |Nemůžete to zkusit znovu. |
| [MessageNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagenotfoundexception) |Došlo k pokusu o přijetí zprávy s určitým pořadovým číslem. Tato zpráva se nenašla. |Ujistěte se, že zpráva již nebyla přijata. Zkontrolujte frontu nedoručených zpráv a podívejte se, zda byla zpráva deadlettered. |Nemůžete to zkusit znovu. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) |Klient nemůže navázat připojení k Service Bus. |Ujistěte se, že je zadaný název hostitele správný a že je hostitel dosažitelný. <p>Pokud je váš kód spuštěn v prostředí s bránou firewall nebo proxy, zajistěte, aby přenosy do Service Bus domény/IP adresy a porty nebyly blokované.</p>|Zkuste to znovu, pokud dojde k problémům s přerušovaným připojením. |
| [Výjimka serverbusyexception](/dotnet/api/microsoft.azure.servicebus.serverbusyexception) |Služba v tuto chvíli nemůže zpracovat požadavek. |Klient může na určitou dobu počkat a pak operaci zopakovat. |Klient může po určitém intervalu opakovat pokus. Pokud výsledkem opakování dojde k jiné výjimce, ověřte chování této výjimky znovu. |
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
| [TransactionException](/dotnet/api/system.transactions.transactionexception) |Ambientní transakce (*Transaction. Current*) je neplatná. Je možné, že byla dokončena nebo přerušena. Vnitřní výjimka může poskytovat další informace. | |Nemůžete to zkusit znovu. |
| [TransactionInDoubtException –](/dotnet/api/system.transactions.transactionindoubtexception) |Došlo k pokusu o provedení operace v transakci, která je nejistá, nebo dojde k pokusu o potvrzení transakce a transakce se stane nejistými. |Vaše aplikace musí tuto výjimku zpracovat (jako zvláštní případ), protože transakce již byla potvrzena. |- |

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

### <a name="namespaces"></a>Obory názvů

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
[TimeoutException](/dotnet/api/system.timeoutexception) označuje, že operace iniciovaná uživatelem trvá déle, než je časový limit operace. 

Měli byste zaškrtnout hodnotu vlastnosti [Třída ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit) , protože po dosažení tohoto limitu může také dojít k [TimeoutException](/dotnet/api/system.timeoutexception).

Očekává se, že dojde k vypršení časového limitu během operací údržby, jako jsou například aktualizace služby Service Bus (nebo) aktualizace operačního systému na prostředky, které službu spouštějí. Během aktualizace operačního systému se entity pohybují a uzly se aktualizují nebo restartují, což může způsobit vypršení časových limitů. Podrobnosti o smlouvě o úrovni služeb (SLA) pro službu Azure Service Bus najdete v tématu [SLA pro Service Bus](https://azure.microsoft.com/support/legal/sla/service-bus/).


### <a name="queues-and-topics"></a>Fronty a témata
V případě front a témat je časový limit zadán buď ve vlastnosti [MessagingFactorySettings. OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings) jako součást připojovacího řetězce, nebo prostřednictvím [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.azure.servicebus.servicebusconnectionstringbuilder). Chybová zpráva se může lišit, ale vždy obsahuje hodnotu časového limitu zadanou pro aktuální operaci. 

## <a name="messagelocklostexception"></a>MessageLockLostException

### <a name="cause"></a>Příčina

**MessageLockLostException** se vyvolá při přijetí zprávy pomocí režimu přijetí [PeekLock](message-transfers-locks-settlement.md#peeklock) a zámek, který uchovává klient, vyprší na straně služby.

Zámek zprávy může vypršet z různých důvodů – 

  * Platnost časovače zámku vypršela před tím, než byla obnovena klientskou aplikací.
  * Klientská aplikace získala zámek, uložila ho do trvalého úložiště a pak se restartovala. Po restartu se klientská aplikace vyhledá ve zprávách o letu a pokusila se je dokončit.

### <a name="resolution"></a>Řešení

V případě **MessageLockLostException** nemůže klientská aplikace nadále zprávu zpracovat. Klientská aplikace může volitelně zvážit protokolování výjimky pro účely analýzy, ale klient *musí* zprávu vyřadit.

Vzhledem k tomu, že zámek zprávy vypršel, by se mohl vrátit do fronty (nebo předplatného) a může je zpracovat další klientská aplikace, která volá příjem.

Pokud **MaxDeliveryCount** překročí, může se zpráva přesunout do **DeadLetterQueue**.

## <a name="sessionlocklostexception"></a>SessionLockLostException

### <a name="cause"></a>Příčina

**SessionLockLostException** je vyvolána, když je relace přijata, a zámek, který uchovává klient, vyprší na straně služby.

Zámek relace může vypršet z různých důvodů – 

  * Platnost časovače zámku vypršela před tím, než byla obnovena klientskou aplikací.
  * Klientská aplikace získala zámek, uložila ho do trvalého úložiště a pak se restartovala. Po restartu se klientská aplikace vyhledala v podletových relacích a pokusila se zpracovat zprávy v těchto relacích.

### <a name="resolution"></a>Řešení

V případě **SessionLockLostException** nemůže klientská aplikace nadále zpracovávat zprávy v relaci. Klientská aplikace může zvážit protokolování výjimky pro účely analýzy, ale klient *musí* zprávu vyřadit.

Vzhledem k tomu, že zámek relace vypršel, vrátí se zpátky do fronty (nebo předplatného) a může být uzamčený další klientskou aplikací, která relaci přijme. Vzhledem k tomu, že zámek relace je uchováván v jediné klientské aplikaci v daném okamžiku, je zaručeno zpracování v daném pořadí.

## <a name="socketexception"></a>SocketException

### <a name="cause"></a>Příčina

V následujících případech je vyvolána výjimka **SocketException** -
   * Pokus o připojení se nezdaří, protože hostitel po uplynutí určité doby neodpověděl správně (kód chyby TCP 10060).
   * Navázání připojení selhalo, protože se nepovedlo odpovědět připojenému hostiteli.
   * Při zpracování zprávy došlo k chybě nebo časový limit je překročen vzdáleným hostitelem.
   * Problém související se síťovými prostředky

### <a name="resolution"></a>Řešení

Chyby **SocketException** označují, že virtuální počítač hostující aplikace není schopen převést název `<mynamespace>.servicebus.windows.net` na odpovídající IP adresu. 

Zkontrolujte, zda je příkaz v mapování na IP adresu úspěšný, v části.

```Powershell
PS C:\> nslookup <mynamespace>.servicebus.windows.net
```

který by měl poskytnout výstup níže

```bash
Name:    <cloudappinstance>.cloudapp.net
Address:  XX.XX.XXX.240
Aliases:  <mynamespace>.servicebus.windows.net
```

Pokud se výše uvedený název **nepřekladuje** na IP adresu a alias oboru názvů, zkontrolujte, který správce sítě další prozkoumat. Překlad názvů se provádí prostřednictvím serveru DNS obvykle prostřednictvím prostředku v síti zákazníka. Pokud se překlad DNS provádí Azure DNS kontaktujte prosím podporu Azure.

Pokud překlad názvů **funguje podle očekávání**, ověřte, jestli [tady](service-bus-troubleshooting-guide.md#connectivity-certificate-or-timeout-issues) jsou povolené připojení k Azure Service Bus.


## <a name="messagingexception"></a>MessagingException

### <a name="cause"></a>Příčina

**MessagingException** je obecná výjimka, která může být vyvolána z různých důvodů. Níže jsou uvedeny některé z důvodů.

   * Došlo k pokusu o vytvoření **QueueClient** na **téma** nebo v **předplatném**.
   * Velikost odeslané zprávy je větší než limit dané úrovně. Přečtěte si další informace o [kvótách Service Bus a omezeních](service-bus-quotas.md).
   * Konkrétní požadavek na rovinu dat (odeslání, přijetí, dokončení, opuštění) byl ukončen z důvodu omezení.
   * Přechodné problémy způsobily kvůli upgradům a restartům služby.

> [!NOTE]
> Výše uvedený seznam výjimek není vyčerpávající.

### <a name="resolution"></a>Řešení

Postup řešení závisí na tom, co způsobilo vyvolání **MessagingException** .

   * V případě **přechodného problému** (kde je to velmi **_přechodný příkaz_*_ nastaveno na _*_true_*_) nebo pro* problémy s omezením**, opakování operace může problém vyřešit. Pro tuto operaci je možné využít výchozí zásady opakování v sadě SDK.
   * V případě jiných problémů se v podrobnostech výjimky označují problémy a postup řešení je možné odvodit ze stejného.

## <a name="next-steps"></a>Další kroky
Kompletní referenční informace k rozhraní .NET API pro Service Bus najdete v [referenčních informacích k rozhraní Azure .NET API](/dotnet/api/overview/azure/service-bus).
Tipy pro řešení potíží najdete v [Průvodci odstraňováním potíží](service-bus-troubleshooting-guide.md) .

---
title: Azure Event Hubs – výjimky (starší verze)
description: Tento článek poskytuje seznam výjimek zasílání zpráv Azure Event Hubs a navrhovaných akcí.
ms.topic: article
ms.date: 11/02/2020
ms.openlocfilehash: adaf7242530727a1f77a9662110a43341e57e80a
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289330"
---
# <a name="event-hubs-messaging-exceptions---net-legacy"></a>Výjimky zasílání zpráv Event Hubs – .NET (starší verze)
V této části jsou uvedeny výjimky rozhraní .NET generované rozhraním API .NET Framework. 

> [!IMPORTANT]
> Některé z výjimek uvedených v článku se vztahují jenom na starší knihovny Event Hubs .NET. Například: Microsoft. ServiceBus. * Exceptions.
> 
> Informace o EventHubsException vyvolaném novou knihovnou .NET najdete v tématu [EventHubsException-.NET](exceptions-dotnet.md) .

## <a name="exception-categories"></a>Kategorie výjimek

Rozhraní API Event Hubs .NET generují výjimky, které mohou být v následujících kategoriích, spolu s přidruženou akcí, kterou můžete provést při pokusu o jejich opravu:

 - Chyba kódování uživatele: 
 
   - [System. ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)
   - [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true)
   - [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true)
   - [System. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception?view=netcore-3.1&preserve-view=true)
   
   Obecná akce: Zkuste opravit kód, než budete pokračovat.
 
 - Chyba nastavení/konfigurace: 
 
   - [Microsoft. ServiceBus. Messaging. MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception)
   - [Microsoft. Azure. EventHubs. MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception)
   - [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true)
   
   Obecná akce: Zkontrolujte konfiguraci a v případě potřeby proveďte změny.
   
 - Přechodné výjimky: 
 
   - [Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception)
   - [Microsoft. ServiceBus. Messaging. výjimka serverbusyexception](#serverbusyexception)
   - [Microsoft. Azure. EventHubs. výjimka serverbusyexception](#serverbusyexception)
   - [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception)
   
   Obecná akce: zkuste operaci zopakovat nebo upozorněte uživatele.
 
 - Další výjimky: 
 
   - [System. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception?view=netcore-3.1&preserve-view=true)
   - [System. TimeoutException](#timeoutexception)
   - [Microsoft. ServiceBus. Messaging. MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception)
   - [Microsoft. ServiceBus. Messaging. SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)
   
   Obecná akce: specifická pro typ výjimky; informace najdete v tabulce v následující části. 

## <a name="exception-types"></a>Typy výjimek
V následující tabulce jsou uvedeny typy výjimek zasílání zpráv a jejich příčiny a poznámky navrhovaná akce, které můžete provést.

| Typ výjimky | Popis/příčiny/příklady | Navrhovaná akce | Poznámka k automatickému/okamžitému opakování |
| -------------- | -------------------------- | ---------------- | --------------------------------- |
| [TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) |Server neodpověděl na požadovanou operaci v zadaném čase, který je řízen [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings). Je možné, že server dokončil požadovanou operaci. Tato výjimka může nastat v důsledku zpoždění sítě nebo jiné infrastruktury. |Ověřte konzistenci stavu systému a v případě potřeby akci opakujte.<br /> Viz [TimeoutException](#timeoutexception). | V některých případech může být užitečné zkusit to znovu. Přidejte do kódu logiku opakování. |
| [InvalidOperationException](/dotnet/api/system.invalidoperationexception?view=netcore-3.1&preserve-view=true) |Požadovaná operace uživatele není povolena v rámci serveru nebo služby. Podrobnosti najdete ve zprávě výjimky. Například [kompletní](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage) vygeneruje tuto výjimku, pokud byla zpráva přijata v režimu [ReceiveAndDelete](/dotnet/api/microsoft.servicebus.messaging.receivemode) . | Podívejte se na kód a dokumentaci. Ujistěte se, že požadovaná operace je platná. | Zkuste to znovu. |
| [OperationCanceledException](/dotnet/api/system.operationcanceledexception?view=netcore-3.1&preserve-view=true) | Došlo k pokusu o vyvolání operace u objektu, který již byl uzavřen, přerušen nebo vyřazen. Ve výjimečných případech je ambientní transakce již uvolněna. | Zkontrolujte kód a ujistěte se, že nevyvolává operace u uvolněného objektu. | Zkuste to znovu. |
| [UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception?view=netcore-3.1&preserve-view=true) | Objekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nemohl získat token, token je neplatný nebo token neobsahuje deklarace identity vyžadované k provedení operace. | Ujistěte se, že Poskytovatel tokenů je vytvořený se správnými hodnotami. Ověřte konfiguraci Access Control Service. | V některých případech může být užitečné zkusit to znovu. Přidejte do kódu logiku opakování. |
| [ArgumentException](/dotnet/api/system.argumentexception?view=netcore-3.1&preserve-view=true)<br /> [ArgumentNullException](/dotnet/api/system.argumentnullexception?view=netcore-3.1&preserve-view=true)<br />[ArgumentOutOfRangeException](/dotnet/api/system.argumentoutofrangeexception?view=netcore-3.1&preserve-view=true) | Jeden nebo více argumentů dodaných metodě je neplatných. Identifikátor URI zadaný pro [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) obsahuje segmenty cesty. Schéma identifikátoru URI zadané pro [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Vytvoření](/dotnet/api/microsoft.servicebus.messaging.messagingfactory) je neplatné. Hodnota vlastnosti je větší než 32 KB. | Zkontrolujte kód volajícího a ujistěte se, že jsou argumenty správné. | Nemůžete to zkusit znovu. |
| [Microsoft. ServiceBus. Messaging MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception) <br /><br/> [Microsoft. Azure. EventHubs MessagingEntityNotFoundException](/dotnet/api/microsoft.azure.eventhubs.messagingentitynotfoundexception) | Entita přidružená k operaci neexistuje nebo byla odstraněna. | Ujistěte se, že entita existuje. | Nemůžete to zkusit znovu. |
| [MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception) | Klient nemůže navázat připojení k centru událostí. |Ujistěte se, že je zadaný název hostitele správný a že je hostitel dosažitelný. | Zkuste to znovu, pokud dojde k problémům s přerušovaným připojením. |
| [Microsoft. ServiceBus. Messaging výjimka serverbusyexception](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) <br /> <br/>[Microsoft. Azure. EventHubs výjimka serverbusyexception](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) | Služba v tuto chvíli nemůže zpracovat požadavek. | Klient může na určitou dobu počkat a pak operaci zopakovat. <br /> Viz [výjimka serverbusyexception](#serverbusyexception). | Klient může po určitém intervalu opakovat pokus. Pokud výsledkem opakování dojde k jiné výjimce, ověřte chování této výjimky znovu. |
| [MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception) | Obecná výjimka zasílání zpráv, která může být vyvolána v následujících případech: pokus o vytvoření [QueueClient](/dotnet/api/microsoft.servicebus.messaging.queueclient) pomocí názvu nebo cesty, která patří k jinému typu entity (například téma). Byl proveden pokus o odeslání zprávy větší než 1 MB. U serveru nebo služby došlo k chybě během zpracování žádosti. Podrobnosti najdete ve zprávě výjimky. Tato výjimka je obvykle přechodný výjimkou. | Zkontrolujte kód a zajistěte, aby se pro tělo zprávy používaly pouze serializovatelné objekty (nebo použijte vlastní serializátor). Vyhledejte v dokumentaci podporované typy hodnot vlastností a používejte pouze podporované typy. Ověřte vlastnost- [přechodný](/dotnet/api/microsoft.servicebus.messaging.messagingexception) . Pokud má **hodnotu true** , můžete operaci zopakovat. | Chování při opakování není definované a nemusí pomáhat. |
| [MessagingEntityAlreadyExistsException](/dotnet/api/microsoft.servicebus.messaging.messagingentityalreadyexistsexception) | Pokusí se vytvořit entitu s názvem, který už používá jiná entita v daném oboru názvů služby. | Odstraňte existující entitu nebo vyberte jiný název entity, která se má vytvořit. | Nemůžete to zkusit znovu. |
| [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) | Entita zasílání zpráv dosáhla maximální povolené velikosti. Tato výjimka může nastat, pokud je maximální počet přijímačů (5) již otevřen na úrovni skupiny pro jednotlivé uživatele. | Přiřadíte místo v entitě příjem zpráv z entity nebo jejích podfront. <br /> Viz [QuotaExceededException](#quotaexceededexception) | Pokud se zprávy během této doby odstranily, může to zkusit znovu. |
| [MessagingEntityDisabledException](/dotnet/api/microsoft.servicebus.messaging.messagingentitydisabledexception) | Požadavek na běhovou operaci u zakázané entity |Aktivujte entitu. | Zkuste to znovu, pokud se entita aktivovala průběžně. |
| [Microsoft. ServiceBus. Messaging MessageSizeExceededException](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) <br /><br/> [Microsoft. Azure. EventHubs MessageSizeExceededException](/dotnet/api/microsoft.azure.eventhubs.messagesizeexceededexception) | Datová část zprávy překračuje limit 1 MB. Tento limit 1 MB slouží jako celková zpráva, která může zahrnovat systémové vlastnosti a jakékoli režie .NET. | Snižte velikost datové části zprávy a potom operaci opakujte. |Nemůžete to zkusit znovu. |

## <a name="quotaexceededexception"></a>QuotaExceededException
[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indikuje, že došlo k překročení kvóty pro některou z entit.

K této výjimce může dojít, pokud je maximální počet přijímačů (5) již otevřen na úrovni skupiny pro jednotlivé uživatele.

### <a name="event-hubs"></a>Event Hubs
Event Hubs má limit 20 skupin uživatelů na centrum událostí. Když se pokusíte vytvořit další, dostanete [QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception). 

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](/dotnet/api/system.timeoutexception?view=netcore-3.1&preserve-view=true) označuje, že operace iniciovaná uživatelem trvá déle, než je časový limit operace. 

Pro Event Hubs je časový limit zadán buď jako součást připojovacího řetězce, nebo prostřednictvím [ServiceBusConnectionStringBuilder](/dotnet/api/microsoft.servicebus.servicebusconnectionstringbuilder). Chybová zpráva se může lišit, ale vždy obsahuje hodnotu časového limitu zadanou pro aktuální operaci. 

Očekává se, že dojde k vypršení časového limitu během operací údržby, jako jsou například aktualizace služby Event Hubs (nebo) aktualizace operačního systému na prostředky, které službu spouštějí. Během aktualizace operačního systému se entity pohybují a uzly se aktualizují nebo restartují, což může způsobit vypršení časových limitů. Podrobnosti o smlouvě o úrovni služeb (SLA) pro službu Azure Event Hubs najdete v tématu [SLA pro Event Hubs](https://azure.microsoft.com/support/legal/sla/event-hubs/). 


### <a name="common-causes"></a>Běžné příčiny
Existují dva běžné příčiny této chyby: nesprávná konfigurace nebo přechodný Chyba služby.

- **Nesprávná konfigurace** Časový limit operace může být pro provozní podmínky příliš malý. Výchozí hodnota pro časový limit operace v sadě SDK klienta je 60 sekund. Zkontrolujte, jestli má váš kód nastavenou hodnotu příliš malý. Stav sítě a využití procesoru může ovlivnit dobu potřebnou k dokončení konkrétní operace, takže časový limit operace by neměl být nastaven na malou hodnotu.
- **Přechodný chybový služba** V některých případech může služba Event Hubs zaznamenat prodlevy při zpracování požadavků; například během období vysokého provozu. V takových případech můžete po zpoždění operaci opakovat, dokud nebude operace úspěšná. Pokud se stejná operace stále po několika pokusech nezdařila, přejděte na [stavovou lokalitu služby Azure](https://azure.microsoft.com/status/) , kde zjistíte, jestli nejsou nějaké nějaké známé výpadky služeb.

## <a name="serverbusyexception"></a>Výjimka serverbusyexception

[Microsoft. ServiceBus. Messaging. výjimka serverbusyexception](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) nebo [Microsoft. Azure. EventHubs. výjimka serverbusyexception](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) označuje, že server je přetížený. Pro tuto výjimku existují dva relevantní kódy chyb.

### <a name="error-code-50002"></a>Kód chyby 50002
K této chybě může dojít z jednoho ze dvou důvodů:

- Zatížení se rovnoměrně nedistribuuje napříč všemi oddíly v centru událostí a jeden oddíl má omezení místní jednotky propustnosti.
    
    **Řešení** : při revizi strategie distribuce oddílů nebo při pokusu o [EventHubClient. Send (eventDataWithOutPartitionKey)](/dotnet/api/microsoft.servicebus.messaging.eventhubclient) může pomáhat.

- Obor názvů Event Hubs nemá dostatečný počet jednotek propustnosti (můžete zaškrtnout obrazovku **metriky** v okně Event Hubs oboru názvů v [Azure Portal](https://portal.azure.com) pro potvrzení). Portál zobrazuje agregované informace (1 minuty), ale měříme propustnost v reálném čase, takže se jedná o pouze odhad.

    **Řešení** : zvýšení jednotek propustnosti v oboru názvů vám může pomáhat. 

    Jednotky propustnosti můžete nakonfigurovat na stránce **škálování** nebo na stránce s **přehledem** na stránce **oboru názvů vaší Event Hubs** v Azure Portal. Nebo můžete použít [Automatické](event-hubs-auto-inflate.md)rozstupné, které se automaticky škálují zvýšením počtu jednotek propustnosti, aby se splnily požadavky na používání.

    Jednotky propustnosti (počet propustnosti) platí pro všechna centra událostí v oboru názvů Event Hubs. Znamená to, že si koupíte počet propustnosti na úrovni oboru názvů a sdílíte se mezi centry událostí v rámci tohoto oboru názvů. Každé z nich opravňuje obor názvů k následujícím funkcím:

    - Až 1 MB událostí příchozího přenosu dat (události odeslané do centra událostí) za sekundu, ale ne víc než 1000 událostí příchozího přenosu dat, operací správy nebo řídicích volání rozhraní API za sekundu.
    - Až 2 MB událostí odchozího přenosu dat (události spotřebované z centra událostí) za sekundu, ale ne víc než 4096 odchozích událostí.
    - Až 84 GB úložiště událostí (je dostatečné pro výchozí dobu uchovávání po dobu 24 hodin).
    
    Na stránce **Přehled** v části **Zobrazit metriky** přepněte na kartu **propustnost** . Vyberte graf pro otevření v větším okně s 1 minutou na ose x. Podívejte se na vrcholové hodnoty a rozdělte je o 60, abyste získali Příchozí bajty za sekundu nebo odchozí bajty za sekundu. Použijte podobný přístup k výpočtu počtu požadavků za sekundu v časech špičky na kartě **požadavky** . 

    Pokud vidíte hodnoty vyšší než počet počet propustnosti * omezení (1 MB za sekundu pro příchozí nebo 1000 žádosti o přijetí za sekundu, 2 MB za sekundu pro odchozí přenosy), zvyšte počet počet propustnosti pomocí stránky **škálování** (na levé straně) na stránce Event Hubs oboru názvů tak, aby se ručně zvýšila velikost nebo používala funkce [Automatické neploché](event-hubs-auto-inflate.md) Event Hubs. Všimněte si, že automatické rozplochelné může zvyšovat až 20 počet PROPUSTNOSTI. Pokud ho chcete zvýšit na přesně 40 počet propustnosti, odešlete [žádost o podporu](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

### <a name="error-code-50001"></a>Kód chyby 50001

Tato chyba by se měla vyskytovat zřídka. K tomu dojde, pokud je v kontejneru, který spouští kód pro váš obor názvů, nedostatek procesoru – ne více než několik sekund, než začne Nástroj pro vyrovnávání zatížení Event Hubs začínat.

**Řešení** : omezení volání metody getruntimeinformation –. Azure Event Hubs podporuje až 50 volání za sekundu do GetRuntimeInfo za sekundu. Po dosažení limitu se může zobrazit výjimka podobná té následující:

```
ExceptionId: 00000000000-00000-0000-a48a-9c908fbe84f6-ServerBusyException: The request was terminated because the namespace 75248:aaa-default-eventhub-ns-prodb2b is being throttled. Error code : 50001. Please wait 10 seconds and try again.
```


## <a name="next-steps"></a>Další kroky

Další informace o službě Event Hubs najdete na následujících odkazech:

* [Přehled Event Hubs](./event-hubs-about.md)
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

---
title: Azure Relay výjimky a jak je vyřešit | Microsoft Docs
description: Seznam výjimek Azure Relay a navrhovaných akcí, které můžete provést, abyste je mohli vyřešit.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 44eeba6eb7b8cfd4e81a923c2d9a3155f1709f2c
ms.sourcegitcommit: a0c1d0d0906585f5fdb2aaabe6f202acf2e22cfc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98625144"
---
# <a name="azure-relay-exceptions"></a>Výjimky Azure Relay

V tomto článku jsou uvedeny některé výjimky, které mohou být vygenerovány Azure Relay rozhraní API. Tato reference se může změnit, proto vraťte aktualizace.

## <a name="exception-categories"></a>Kategorie výjimek

Rozhraní API pro předávání generují výjimky, které mohou patřit do následujících kategorií. V seznamu jsou uvedené i navrhované akce, které můžete provést, abyste mohli výjimky vyřešit.

*   **Chyba kódování uživatele**: [System. ArgumentException](/dotnet/api/system.argumentexception), [System. InvalidOperationException](/dotnet/api/system.invalidoperationexception), [System. OperationCanceledException](/dotnet/api/system.operationcanceledexception), [System. Runtime. Serialization. SerializationException](/dotnet/api/system.runtime.serialization.serializationexception). 

    **Obecná akce**: Zkuste opravit kód, než budete pokračovat.
*   **Chyba nastavení/konfigurace**: [System. UnauthorizedAccessException](/dotnet/api/system.unauthorizedaccessexception). 

    **Obecná akce**: Zkontrolujte konfiguraci. V případě potřeby změňte konfiguraci.
*   **Přechodné výjimky**: [Microsoft. ServiceBus. Messaging. MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft. ServiceBus. Messaging. výjimka serverbusyexception](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft. ServiceBus. Messaging. MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Obecná akce**: zkuste operaci zopakovat nebo upozorněte uživatele.
*   **Další výjimky**: [System. Transactions. TransactionException](/dotnet/api/system.transactions.transactionexception), [System. TimeoutException](/dotnet/api/system.timeoutexception). 

    **Obecná akce**: specifická pro typ výjimky. Podívejte se na tabulku v následující části. 

## <a name="exception-types"></a>Typy výjimek

V následující tabulce jsou uvedeny typy výjimek zasílání zpráv a jejich příčiny. Také si poznamenejte navrhované akce, které můžete provést, abyste mohli výjimky vyřešit.

| **Typ výjimky** | **Popis** | **Navrhovaná akce** | **Poznámka k automatickému nebo okamžitému opakování** |
| --- | --- | --- | --- |
| [Prodlev](/dotnet/api/system.timeoutexception) |Server neodpověděl na požadovanou operaci v zadaném čase, který je řízen [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Je možné, že server dokončil požadovanou operaci. K tomu může dojít v důsledku zpoždění sítě nebo jiné infrastruktury. |Zkontrolujte konzistenci stavu systému a v případě potřeby akci opakujte. Viz [TimeoutException](#timeoutexception). |V některých případech může být užitečné zkusit to znovu. Přidejte do kódu logiku opakování. |
| [Neplatná operace](/dotnet/api/system.invalidoperationexception) |Požadovaná operace uživatele není povolena v rámci serveru nebo služby. Podrobnosti najdete ve zprávě výjimky. |Podívejte se na kód a dokumentaci. Ujistěte se, že požadovaná operace je platná. |Nemůžete to zkusit znovu. |
| [Operace zrušena](/dotnet/api/system.operationcanceledexception) |Došlo k pokusu o vyvolání operace u objektu, který již byl uzavřen, přerušen nebo vyřazen. Ve výjimečných případech je ambientní transakce již uvolněna. |Zkontrolujte kód a ujistěte se, že nevyvolává operace u uvolněného objektu. |Nemůžete to zkusit znovu. |
| [Neautorizovaný přístup](/dotnet/api/system.unauthorizedaccessexception) |Objekt [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) nemohl získat token, token je neplatný nebo token neobsahuje deklarace požadované k provedení operace. |Ujistěte se, že Poskytovatel tokenů je vytvořený se správnými hodnotami. Ověřte konfiguraci služby Access Control. |V některých případech může být užitečné zkusit to znovu. Přidejte do kódu logiku opakování. |
| [Výjimka argumentu](/dotnet/api/system.argumentexception),<br /> [Argument null](/dotnet/api/system.argumentnullexception),<br />[Argument je mimo rozsah.](/dotnet/api/system.argumentoutofrangeexception) |Došlo k jedné nebo více následujícím akcím:<br />Jeden nebo více argumentů dodaných metodě je neplatných.<br /> Identifikátor URI, který se zadal pro [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) , obsahuje jeden nebo více segmentů cesty.<br />Schéma identifikátoru URI zadané pro [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Vytvoření](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) je neplatné. <br />Hodnota vlastnosti je větší než 32 KB. |Zkontrolujte kód volajícího a ujistěte se, že jsou argumenty správné. |Nemůžete to zkusit znovu. |
| [Zaneprázdněný Server](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Služba v tuto chvíli nemůže zpracovat požadavek. |Klient může počkat na určitou dobu a pak operaci zopakovat. |Klient se může po určitém intervalu opakovat. Pokud výsledkem opakování dojde k jiné výjimce, Projděte si chování opakování této výjimky. |
| [Překročení kvóty](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Entita zasílání zpráv dosáhla maximální povolené velikosti. |Přiřadíte místo v entitě příjem zpráv z entity nebo jejích podfront. Viz [QuotaExceededException](#quotaexceededexception). |Pokud se zprávy během této doby odstranily, může to zkusit znovu. |
| [Překročení velikosti zprávy](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Datová část zprávy překračuje limit 256 KB. Počítejte s tím, že limit 256-KB je celková velikost zprávy. Celková velikost zprávy může zahrnovat systémové vlastnosti a jakoukoli režii Microsoft .NET. |Snižte velikost datové části zprávy a potom operaci opakujte. |Nemůžete to zkusit znovu. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indikuje, že došlo k překročení kvóty pro některou z entit.

Pro Relay Tato výjimka zabalí [System. ServiceModel. QuotaExceededException](/dotnet/api/system.servicemodel.quotaexceededexception), který indikuje, že pro tento koncový bod byl překročen maximální počet naslouchacího procesu. To je uvedeno v hodnotě **MaximumListenersPerEndpoint** zprávy o výjimce.

## <a name="timeoutexception"></a>TimeoutException
[TimeoutException](/dotnet/api/system.timeoutexception) označuje, že operace iniciovaná uživatelem trvá déle, než je časový limit operace. 

Ověřte hodnotu vlastnosti [Třída ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) . Dosažení tohoto limitu může také způsobit [TimeoutException](/dotnet/api/system.timeoutexception).

U Relay se při prvním otevření připojení typu Relay pro předávání může zobrazit výjimka časového limitu. Existují dva běžné příčiny pro tuto výjimku:

*   Hodnota [OpenTimeout](/previous-versions/) může být příliš malá (Pokud dokonce ani zlomek sekundy).
* Místní naslouchací proces přenosu může přestat reagovat (nebo může narazit na problémy s pravidly brány firewall, které znemožňují naslouchací proces přijímání nových připojení klientů) a hodnota [OpenTimeout](/previous-versions/) je menší než přibližně 20 sekund.

Příklad:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Běžné příčiny
Tato chyba má dvě obvyklé příčiny:

*   **Nesprávná konfigurace**
    
    Časový limit operace může být pro provozní podmínky příliš malý. Výchozí hodnota pro časový limit operace v sadě SDK klienta je 60 sekund. Zkontrolujte, zda je hodnota v kódu nastavena na hodnotu příliš malá. Všimněte si, že využití procesoru a podmínka sítě mohou ovlivnit dobu potřebnou k dokončení operace. Je vhodné nastavit časový limit operace na velmi malou hodnotu.
*   **Přechodný chybový služba**

    V některých případech může služba předávání nastat při zpracování požadavků k prodlevám. K tomu může dojít například během období vysokého provozu. Pokud k tomu dojde, zkuste operaci zopakovat po prodlevě, dokud nebude operace úspěšná. Pokud se stejná operace po několika pokusech i nadále nezdařila, zkontrolujte [lokalitu stavu služby Azure](https://azure.microsoft.com/status/) a zjistěte, jestli nejsou známé výpadky služeb.

## <a name="next-steps"></a>Další kroky
* [Nejčastější dotazy k Azure Relay](relay-faq.md)
* [Vytvoření oboru názvů Relay](relay-create-namespace-portal.md)
* [Začínáme s Azure Relay a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s Azure Relay a uzlem](relay-hybrid-connections-node-get-started.md)

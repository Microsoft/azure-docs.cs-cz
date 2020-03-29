---
title: Výjimky Azure Relay a jejich řešení | Dokumenty společnosti Microsoft
description: Seznam výjimek Azure Relay a doporučených akcí, které můžete provést, abyste je mohli vyřešit.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: spelluru
ms.openlocfilehash: fe8f057443b978e70e7cdd2591affd455fefdca8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60749032"
---
# <a name="azure-relay-exceptions"></a>Výjimky Azure Relay

Tento článek uvádí některé výjimky, které mohou být generovány pomocí azure relay API. Tento odkaz se může změnit, proto se můžete vrátit k aktualizaci.

## <a name="exception-categories"></a>Kategorie výjimek

Relé API generovat výjimky, které mohou spadat do následujících kategorií. V seznamu jsou také uvedeny navrhované akce, které můžete provést k vyřešení výjimek.

*   **Chyba kódování uživatele**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Obecná akce**: Pokuste se opravit kód, než budete pokračovat.
*   **Chyba instalace/konfigurace**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Obecná akce**: Zkontrolujte konfiguraci. V případě potřeby změňte konfiguraci.
*   **Přechodné výjimky:** [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Obecná akce**: Opakujte operaci nebo upozorněte uživatele.
*   **Další výjimky**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Obecná akce**: Specifické pro typ výjimky. Viz tabulka v následující části. 

## <a name="exception-types"></a>Typy výjimek

V následující tabulce jsou uvedeny typy výjimek zasílání zpráv a jejich příčiny. Bere také na vědomí navrhované akce, které můžete provést k vyřešení výjimek.

| **Typ výjimky** | **Popis** | **Navrhovaná akce** | **Poznámka k automatickému nebo okamžitému opakování** |
| --- | --- | --- | --- |
| [Časový limit](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Server neodpověděl na požadovanou operaci v zadaném čase, který je řízen [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Server pravděpodobně dokončil požadovanou operaci. K tomu může dojít v důsledku zpoždění sítě nebo jiné infrastruktury. |Zkontrolujte konzistenci stavu systému a v případě potřeby opakujte akci. Viz [TimeoutException](#timeoutexception). |V některých případech může pomoct opakování. přidejte logiku opakování do kódu. |
| [Neplatná operace](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná operace uživatele není v rámci serveru nebo služby povolena. Podrobnosti naleznete ve zprávě o výjimce. |Zkontrolujte kód a dokumentaci. Ujistěte se, že požadovaná operace je platná. |Opakování nepomůže. |
| [Operace byla zrušena.](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Je proveden pokus o vyvolání operace na objektu, který již byl uzavřen, přerušen nebo vyřazen. Ve výjimečných případech okolí transakce je již uvolněna. |Zkontrolujte kód a ujistěte se, že nevyvolá operace na vyřazený objekt. |Opakování nepomůže. |
| [Neoprávněný přístup](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objekt nemohl získat token, token je neplatný nebo token neobsahuje deklarace potřebné k provedení operace. |Ujistěte se, že zprostředkovatel tokenu je vytvořen se správnými hodnotami. Zkontrolujte konfiguraci služby Řízení přístupu. |V některých případech může pomoct opakování. přidejte logiku opakování do kódu. |
| [Výjimka argumentu](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argument Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argument je mimo rozsah.](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Došlo k jedné nebo více z následujících došlo:<br />Jeden nebo více argumentů dodaných do metody je neplatných.<br /> Identifikátor URI dodaný do [nástroje NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) obsahuje jeden nebo více segmentů cesty.<br />Schéma URI dodané programu [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [Create](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) je neplatné. <br />Hodnota vlastnosti je větší než 32 KB. |Zkontrolujte volající kód a ujistěte se, že argumenty jsou správné. |Opakování nepomůže. |
| [Server je zaneprázdněn.](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Služba není schopna zpracovat požadavek v tuto chvíli. |Klient může čekat po určitou dobu a potom operaci opakovat. |Klient může opakovat po určitém intervalu. Pokud opakování má za následek jinou výjimku, zkontrolujte chování opakování této výjimky. |
| [Byla překročena kvóta.](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Entita zasílání zpráv dosáhla maximální povolené velikosti. |Vytvořte místo v entitě příjemzpráv od entity nebo jejích podfront. Viz [KvótaExceededException](#quotaexceededexception). |Opakování může pomoci, pokud byly zprávy mezitím odebrány. |
| [Byla překročena velikost zprávy.](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Datová část zprávy překračuje limit 256 KB. Všimněte si, že limit 256 KB je celková velikost zprávy. Celková velikost zprávy může zahrnovat vlastnosti systému a všechny režie microsoft .NET. |Zmenšete velikost datové části zprávy a opakujte operaci. |Opakování nepomůže. |

## <a name="quotaexceededexception"></a>Quotaexceededexception

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indikuje, že došlo k překročení kvóty pro některou z entit.

Pro relé tato výjimka obtéká [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), což znamená, že byl překročen maximální počet posluchačů pro tento koncový bod. To je uvedeno v **hodnotě MaximumListenersPerEndpoint** zprávy o výjimce.

## <a name="timeoutexception"></a>Timeoutexception
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace iniciovaná uživatelem trvá déle než časový časový režim operace. 

Zkontrolujte hodnotu vlastnosti [ServicePointManager.DefaultConnectionLimit.](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) Dosažení tohoto limitu také může způsobit [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Pro relé můžete obdržet výjimky časového limitu při prvním otevření připojení odesílatele přenosu. Existují dvě běžné příčiny této výjimky:

*   Hodnota [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) může být příliš malá (i když i o zlomek sekundy).
* Místní naslouchací proces přenosu nemusí odpovídat (nebo může dojít k problémům s pravidly brány firewall, které zakazují posluchačům přijímat nová připojení klientů) a hodnota [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) je menší než přibližně 20 sekund.

Příklad:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Běžné příčiny
Existují dvě běžné příčiny této chyby:

*   **Nesprávná konfigurace**
    
    Časový čas operace může být pro provozní stav příliš malý. Výchozí hodnota časového limitu operace v klientské sadě SDK je 60 sekund. Zkontrolujte, zda je hodnota v kódu nastavena na něco příliš malého. Všimněte si, že využití procesoru a stav sítě může ovlivnit čas potřebný k dokončení operace. Je vhodné nenastavovat časový časový režim operace na velmi malou hodnotu.
*   **Chyba přechodné služby**

    V některých případě může dojít ke zpoždění služby při zpracování požadavků. K tomu může dojít například v obdobích vysokénávštěvnosti. Pokud k tomu dojde, opakujte operaci po zpoždění, dokud nebude operace úspěšná. Pokud stejná operace nadále selhat po několika pokusech, zkontrolujte [web stavu služby Azure](https://azure.microsoft.com/status/) a zjistěte, zda existují známé výpadky služby.

## <a name="next-steps"></a>Další kroky
* [Nejčastější dotazy k přenosu Azure](relay-faq.md)
* [Vytvoření reléového oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s Azure Relay a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s Azure Relay a Node](relay-hybrid-connections-node-get-started.md)


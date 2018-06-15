---
title: Azure předávání výjimky a jejich řešení | Microsoft Docs
description: Seznam předávání přes Azure výjimky a doporučované akce, které můžete provést k jejich řešení.
services: service-bus-relay
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2017
ms.author: sethm
ms.openlocfilehash: 1dbe73dac0d09db96ab902909125869959963e6f
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2017
ms.locfileid: "26855857"
---
# <a name="azure-relay-exceptions"></a>Azure předávání výjimky

V tomto článku jsou uvedené některé výjimky, které může být generována předávání přes rozhraní API Azure. Tento odkaz se může změnit, tak to zkuste znovu aktualizací.

## <a name="exception-categories"></a>Výjimka kategorie

Předávání přes rozhraní API generování výjimek, které může spadají do následujících kategorií. Navrhovaná akce, které vám pomohou vyřešit výjimky jsou také uvedeny.

*   **Uživatel kódování chyba**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Obecné akce**: pokusí opravit kód, než budete pokračovat.
*   **Chyba instalace/konfigurace**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Obecné akce**: Zkontrolujte konfiguraci. V případě potřeby změňte konfiguraci.
*   **Přechodný výjimky**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Obecné akce**: operaci opakovat nebo oznámit uživatelům.
*   **Ostatní výjimky**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Obecné akce**: specifické pro daný typ výjimky. Najdete v tabulce v následující části. 

## <a name="exception-types"></a>Typy výjimek

Následující tabulka uvádí typy zasílání zpráv výjimky a jejich příčiny. Zaznamenává taky doporučované akce, které můžete provést vyřešit výjimky.

| **Typ výjimky** | **Popis** | **Navrhovaná akce** | **Poznámka: na automatické nebo okamžitou opakování** |
| --- | --- | --- | --- |
| [Časový limit](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Serveru nereagovala na požadovanou operaci v určeném čase, které řídí [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Server může mít dokončit požadovanou operaci. K tomu dochází z důvodu sítě nebo jiné infrastruktury zpoždění. |Zkontrolujte stav systému pro konzistence a opakujte, v případě potřeby. V tématu [TimeoutException](#timeoutexception). |Opakování vám může pomoci v některých případech; Přidejte logiku opakovaných pokusů ke kódu. |
| [Neplatná operace](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná uživatelská operace není povolena v rámci serveru nebo službě. Zobrazí se zpráva výjimky podrobnosti. |Zkontrolujte kód a dokumentace. Ujistěte se, že požadovaná operace je platná. |Opakování nepomůže. |
| [Operace byla zrušena](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Je proveden pokus o vyvolání operace u objektu, který již byl uzavřen, byl zrušen nebo zlikvidován. Ve výjimečných případech je již zveřejněn vedlejším transakce. |Zkontrolujte kód a ujistěte se, že nevyvolá operací na objekt uvolněné. |Opakování nepomůže. |
| [Neoprávněný přístup](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objekt se nedá získat token, token je neplatný nebo token neobsahuje deklarace identity potřebná k provedení operace. |Ujistěte se, že se vytvoří zprostředkovatel tokenu s správné hodnoty. Zkontrolujte konfiguraci služby Řízení přístupu. |Opakování vám může pomoci v některých případech; Přidejte logiku opakovaných pokusů ke kódu. |
| [Argument výjimka](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argument Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argument mimo rozsah.](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Došlo k minimálně jeden z následujících akcí:<br />Jeden nebo více argumenty metodě jsou neplatné.<br /> Zadaný identifikátor URI do [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) obsahuje jeden nebo více segmenty cesty.<br />Schéma identifikátoru URI zadané [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) je neplatný. <br />Hodnota vlastnosti je větší než 32 KB. |Volání kódu a ujistěte se, že jsou správné argumenty. |Opakování nepomůže. |
| [Server je zaneprázdněný](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Služba není schopna zpracovat žádost v tuto chvíli. |Klienta můžete čekat na určitou dobu a pak zkuste operaci zopakovat. |Klient může opakujte po uplynutí zadaného intervalu. Pokud výsledky opakování v různých výjimek, zkontrolujte opakování chování této výjimky. |
| [Kvóta byla překročena.](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Zasílání zpráv entity bylo dosaženo jeho maximální povolenou velikost. |Přijímání zpráv z entit nebo jeho podfronty k vytvoření prostoru v entitě. V tématu [quotaexceededexception –](#quotaexceededexception). |Opakování mohou pomoci, pokud do té doby byly odebrány zprávy. |
| [Velikost zprávy překročen](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Datovou část zprávy překračuje limit 256 KB. Všimněte si, že limit 256 KB je celková velikost zpráv. Celková velikost zpráv mohou zahrnovat vlastnosti systému a všechny režijní náklady na rozhraní Microsoft .NET. |Zmenšete velikost datové části zprávy a potom operaci opakujte. |Opakování nepomůže. |

## <a name="quotaexceededexception"></a>Quotaexceededexception –

[Quotaexceededexception –](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) označuje, že byla překročena kvóta pro konkrétní entitu.

Pro předávání, se zabalí tuto výjimku [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), což naznačuje, že byl překročen maximální počet naslouchací procesy pro tento koncový bod. To je uvedené v **MaximumListenersPerEndpoint** hodnotu zpráva o výjimce.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace se uživatel spustil trvá déle, než je časový limit operace. 

Zkontrolujte hodnotu [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) vlastnost. Dosažení tohoto limitu může také způsobit [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Pro předávání se může zobrazit výjimkám časového limitu při prvním otevření předávacího spojení odesílatele. Existují dvě běžné příčiny této výjimky:

*   [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) hodnota může být příliš malý (Pokud i za zlomek sekundy).
* Naslouchací proces předávání místní může být reagovat (nebo setkat problémů pravidla brány firewall, které moduly pro naslouchání přijímat nová připojení klienta) a [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) hodnota je menší než přibližně 20 sekund.

Příklad:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Běžné příčiny
Existují dvě běžné příčiny této chyby:

*   **Nesprávná konfigurace**
    
    Časový limit operace může být příliš malá pro provozní stav. Výchozí hodnota pro časový limit operace v klientovi SDK je 60 sekund. Zkontrolujte, zda je hodnota v kódu nastavena na něco příliš malá. Všimněte si, že využití procesoru a podmínku sítě může ovlivnit čas potřebný pro dokončení operace. Je vhodné není nastavená na hodnotu velmi malé časový limit operace.
*   **Přechodné chybě**

    V některých případech může dojít k předávací službou zpoždění při zpracování požadavků. K tomu může dojít například během období intenzivní provoz. Pokud k tomu dojde, opakujte operaci po prodlevě, dokud nebude operace úspěšná. Pokud stejné operace dále nedaří po několika pokusech, podívejte se [lokality stav služby Azure](https://azure.microsoft.com/status/) a zjistěte, zda jsou známy výpadky.

## <a name="next-steps"></a>Další postup
* [Nejčastější dotazy k Azure předávání](relay-faq.md)
* [Vytvoření oboru názvů předávání](relay-create-namespace-portal.md)
* [Začínáme s Azure předávání a rozhraní .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s Azure předávání a uzlu](relay-hybrid-connections-node-get-started.md)


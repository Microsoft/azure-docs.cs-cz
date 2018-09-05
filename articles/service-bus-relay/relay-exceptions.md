---
title: Azure Relay výjimky a jejich řešení | Dokumentace Microsoftu
description: Seznam výjimek Azure Relay a doporučené akce, které si můžete pro jejich řešení.
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
ms.openlocfilehash: 0fe30fe95e77adceaa5013f89206b08daf2a58a2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43702091"
---
# <a name="azure-relay-exceptions"></a>Azure Relay výjimky

Tento článek uvádí některé výjimky, které mohou být generovány rozhraní API služby Azure Relay. Tento odkaz se může změnit, proto zkontrolujte novinky.

## <a name="exception-categories"></a>Kategorie výjimek

Rozhraní API služby Relay generovat výjimky, které může vrátit do následujících kategorií. Doporučené akce, které si můžete vyřešit výjimky jsou také uvedeny.

*   **Chyba kódování uživatele**: [System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx). 

    **Obecné akce**: Zkuste opravit kód, než budete pokračovat.
*   **Chyba instalace/konfigurace**: [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). 

    **Obecné akce**: zkontrolovat svou konfiguraci. V případě potřeby změňte konfiguraci.
*   **Přechodným výjimkám**: [Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [ Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception). 

    **Obecné akce**: operaci opakovat nebo upozornit uživatele.
*   **Ostatní výjimky**: [System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx). 

    **Obecné akce**: specifické pro daný typ výjimky. V tabulce v následující části. 

## <a name="exception-types"></a>Typy výjimek

Následující tabulka uvádí typy zasílání zpráv výjimek a jejich příčiny. Rovněž uvedena doporučené akce, které si můžete vyřešit výjimky.

| **Typ výjimky** | **Popis** | **Navrhované akce** | **Mějte na paměti při automatické nebo okamžité opakování** |
| --- | --- | --- | --- |
| [časový limit](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Server neodpověděl na požadovanou operaci v zadaném čase, které řídí [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Server může dokončení požadované operace. Může to být způsobeno sítě nebo jiné infrastruktury zpoždění. |Zkontrolujte stav systému pro zajištění konzistence a opakujte, v případě potřeby. Zobrazit [TimeoutException](#timeoutexception). |V některých případech; může pomoci opakování Přidejte do kódu logiku opakování. |
| [Neplatná operace](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |Požadovaná uživatelská operace není povolena v rámci serveru nebo služby. Zobrazit zpráva o výjimce podrobnosti. |Zkontrolujte kód a dokumentace. Ujistěte se, že požadovaná operace je platná. |Opakování nepomůže. |
| [Operace byla zrušena](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Je proveden pokus o vyvolání operace u objektu, který již byl uzavřen, byla přerušena nebo odstraněn. Ve výjimečných případech je již uvolněna okolí transakce. |Zkontrolujte kód a ujistěte se, že vyvolat operace na smazaném objektu. |Opakování nepomůže. |
| [Neoprávněný přístup](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |[TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) objekt nebylo možné získat token, token je neplatný nebo token neobsahuje deklarace potřebné k provedení této operace. |Ujistěte se, že poskytovatele tokenu, kterého se vytvoří s správné hodnoty. Zkontrolujte konfiguraci služby Řízení přístupu. |V některých případech; může pomoci opakování Přidejte do kódu logiku opakování. |
| [Výjimka argumentu](https://msdn.microsoft.com/library/system.argumentexception.aspx),<br /> [Argument Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx),<br />[Argument mimo rozsah.](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Došlo k jedné nebo více z následujících akcí:<br />Jeden nebo více argumentů zadaný pro metodu nejsou platné.<br /> Identifikátor URI dodaný [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) obsahuje jeden nebo více segmentů cesty.<br />Schéma identifikátoru URI předaná [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) nebo [vytvořit](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) je neplatný. <br />Hodnota vlastnosti je větší než 32 KB. |Zkontrolujte volající kód a ujistěte se, že argumenty jsou správné. |Opakování nepomůže. |
| [Server je zaneprázdněn](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Službu není možné na zpracování žádosti. v tuto chvíli. |Klienta můžete počkat určitou dobu a pak zkuste operaci zopakovat. |Klient může opakovat pokus po uplynutí zadaného intervalu. Pokud výsledky opakování různé výjimky, podívejte se na chování opakování této výjimky. |
| [Překročila se kvóta](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |Entity pro zasílání zpráv dosáhl maximální povolenou velikost. |Příjem zpráv z entity nebo jeho podfronty k vytvoření prostoru v dané entitě. Zobrazit [QuotaExceededException](#quotaexceededexception). |Pokud do té doby byly odebrány zprávy, mohou pomoci při opakování. |
| [Překročila se velikost zprávy](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Tělo zprávy překračuje limit 256 KB. Všimněte si, že limit 256 KB je celková velikost zpráv. Celková velikost zpráv může obsahovat vlastnosti systému a rezervy rozhraní Microsoft .NET. |Zmenšit velikost datové části zprávy a potom zkuste operaci zopakovat. |Opakování nepomůže. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) označuje, že byla překročena kvóta pro konkrétní entitu.

Pro předávání, zabalí tuto výjimku [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), což znamená, že byl překročen maximální počet naslouchacích procesů pro tento koncový bod. To je uvedeno v **MaximumListenersPerEndpoint** hodnotu zpráva o výjimce.

## <a name="timeoutexception"></a>TimeoutException
A [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) označuje, že operace iniciovaná uživatelem trvá déle, než je časový limit operace. 

Zkontrolujte hodnotu [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) vlastnost. Dosažení tohoto limitu může také způsobit [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Pro propojení se může zobrazit výjimkám časového limitu při prvním otevření připojení odesílatele přenosu. Existují dvě běžné příčiny této výjimky:

*   [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) hodnota může být příliš malý (Pokud se i za zlomek sekundy).
* Naslouchací proces přenosového místní může být reagovat (nebo setkat problémů pravidla brány firewall, které naslouchacích procesů přijímat nová připojení klientů) a [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) hodnota je menší než přibližně 20 sekund.

Příklad:

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10.
The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Mezi běžné příčiny
Existují dvě běžné příčiny této chyby:

*   **Nesprávná konfigurace**
    
    Časový limit operace může být příliš malá pro provozní podmínky. Výchozí hodnota pro vypršení časového limitu operace v Klientská sada SDK je 60 sekund. Zkontrolujte, zda hodnota ve vašem kódu je nastavena na něco příliš malá. Všimněte si, že využití procesoru a stav sítě může ovlivnit čas potřebný pro dokončení operace. Je vhodné Nenastavovat časový limit operace velmi malou hodnotu.
*   **Chyba přechodné služby**

    Se službou Relay vytvoříte v některých případech může dojít k prodlevám při zpracování žádosti. K tomu může dojít například během období vysoké návštěvnosti. Pokud k tomu dojde, zopakujte operaci po prodlevě, dokud je operace úspěšná. Pokud stejnou operaci nadále selhávat po několika pokusech, zkontrolujte, [web stavu služeb Azure](https://azure.microsoft.com/status/) a zjistěte, jestli jsou známé výpadky služeb.

## <a name="next-steps"></a>Další postup
* [Nejčastější dotazy k Azure Relay](relay-faq.md)
* [Vytvořit obor názvů služby relay](relay-create-namespace-portal.md)
* [Začínáme s Azure Relay a .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s Azure Relay a uzlu](relay-hybrid-connections-node-get-started.md)


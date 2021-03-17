---
title: Protokoly diagnostiky pro Hybrid Connections
description: Tento článek poskytuje přehled všech protokolů aktivit a diagnostiky, které jsou k dispozici pro Azure Relay.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 9b459750ad1445da89a8e89a10a35b878bfb64e1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590870"
---
# <a name="enable-diagnostics-logs-for-azure-relay-hybrid-connections"></a>Povolit protokoly diagnostiky pro Azure Relay Hybrid Connections
Když začnete používat Hybrid Connections Azure Relay, možná budete chtít monitorovat, jak a kdy jsou posluchači a odesílatelé spuštěni a zavřeni a jak se budou vaše Hybrid Connections vytvářet a jsou odesílány zprávy. Tento článek poskytuje přehled protokolů aktivit a diagnostiky, které poskytuje služba Azure Relay. 

Pro Azure Relay můžete zobrazit dva typy protokolů:

- [Protokoly aktivit](../azure-monitor/essentials/platform-logs-overview.md): tyto protokoly obsahují informace o operacích provedených proti vašemu oboru názvů ve Azure Portal nebo prostřednictvím šablony Azure Resource Manager. Tyto protokoly jsou vždycky povolené. Například: "vytvořit nebo aktualizovat obor názvů", "vytvořit nebo aktualizovat hybridní připojení". 
- [Diagnostické protokoly](../azure-monitor/essentials/platform-logs-overview.md): můžete nakonfigurovat diagnostické protokoly pro rozsáhlejší zobrazení všeho, co se děje s operacemi a akcemi provedenými na vašem oboru názvů pomocí rozhraní API nebo prostřednictvím jazykové sady SDK.

## <a name="view-activity-logs"></a>Zobrazení protokolů aktivit
Pokud chcete zobrazit protokoly aktivit pro obor názvů Azure Relay, přepněte na Azure Portal na stránku **protokolu aktivit** .

![Protokol aktivit Azure Relay](./media/diagnostic-logs/activity-log.png)

## <a name="enable-diagnostic-logs"></a>Povolení diagnostických protokolů

> [!NOTE]
> Diagnostické protokoly jsou k dispozici pouze pro Hybrid Connections, nikoli pro přenos Windows Communication Foundation (WCF).

Chcete-li povolit diagnostické protokoly, proveďte následující kroky:

1. V [Azure Portal](https://portal.azure.com)přejdete do oboru názvů Azure Relay a v části **monitorování** vyberte  **nastavení diagnostiky**.
1. Na stránce **nastavení diagnostiky** vyberte **Přidat nastavení diagnostiky**.  

   ![Odkaz Přidat nastavení diagnostiky](./media/diagnostic-logs/add-diagnostic-setting.png)

1. Nastavení diagnostiky nakonfigurujte pomocí následujících kroků:
    1. Do pole **název** zadejte název nastavení diagnostiky.  
    2. Jako typ protokolu vyberte **HybridConnectionsEvent** . 
    3. Vyberte jedno z následujících tří **míst** pro diagnostické protokoly:  
        1. Pokud vyberete možnost **archivovat do účtu úložiště**, nakonfigurujte účet úložiště, do kterého se budou protokoly diagnostiky ukládat.  
        2. Pokud vyberete **Stream do centra událostí**, nakonfigurujte centrum událostí, do kterého chcete streamovat diagnostické protokoly.
        3. Pokud vyberete možnost **Odeslat do Log Analytics**, určete, do které instance Log Analytics bude Diagnostika odeslána.  

        ![Ukázka nastavení diagnostiky](./media/diagnostic-logs/sample-diagnostic-settings.png)
1. Nastavení uložte kliknutím na **Uložit** na panelu nástrojů.

Nové nastavení se projeví přibližně po 10 minutách. Protokoly se zobrazí v konfigurovaném cíli archivace v podokně **diagnostické protokoly** . Další informace o konfiguraci nastavení diagnostiky najdete v tématu [Přehled protokolů diagnostiky Azure](../azure-monitor/essentials/platform-logs-overview.md).


## <a name="schema-for-hybrid-connections-events"></a>Schéma pro události hybridních připojení
Řetězce JSON protokolu událostí Hybrid Connections obsahují prvky uvedené v následující tabulce:

| Název | Description |
| ------- | ------- |
| ResourceId | ID prostředku Azure Resource Manager |
| ID aktivity | Interní ID, které slouží k identifikaci zadané operace. Může být také známé jako "TrackingId" |
| Koncový bod | Adresa prostředku Relay |
| OperationName | Typ operace Hybrid Connections, která se zaznamená |
| EventTimeString | Časové razítko UTC záznamu protokolu |
| Zpráva | Podrobná zpráva o události |
| Kategorie | Kategorie události V současné době je k dispozici pouze `HybridConnectionsEvents` . 


## <a name="sample-hybrid-connections-event"></a>Ukázka události hybridních připojení
Tady je ukázka události hybridních připojení ve formátu JSON. 

```json
{
    "resourceId": "/SUBSCRIPTIONS/0000000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/MyResourceGroup/PROVIDERS/MICROSOFT.RELAY/NAMESPACES/MyRelayNamespace",
    "ActivityId": "7006a0db-27eb-445c-939b-ce86133014cc",
    "endpoint": "sb://myrelaynamespace.servicebus.windows.net/myhybridconnection/7006a0db-27eb-445c-939b-ce86133014cc_G5",
    "operationName": "Microsoft.Relay/HybridConnections/NewSenderRegistering",
    "EventTimeString": "2020-04-27T20:27:57.3842810Z",
    "message": "A new sender is registering.",
    "category": "HybridConnectionsEvent"
}
```

## <a name="events-and-operations-captured-in-diagnostic-logs"></a>Události a operace zaznamenané v diagnostických protokolech

| Operace | Description | 
| --------- | ----------- | 
| AuthorizationFailed | Autorizace se nezdařila.|
| InvalidSasToken | Neplatný token SAS | 
| ListenerAcceptingConnection | Naslouchací proces přijímá připojení. |
| ListenerAcceptingConnectionTimeout | Vypršel časový limit naslouchacího procesu pro přijetí připojení. |
| ListenerAcceptingHttpRequestFailed | Naslouchací proces přijal požadavek HTTP se nezdařil z důvodu výjimky. |
| ListenerAcceptingRequestTimeout | Vypršel časový limit žádosti o přijetí naslouchacího procesu. |  
| ListenerClosingFromExpiredToken | Naslouchací proces se zavírá, protože platnost tokenu zabezpečení vypršela. | 
| ListenerRejectedConnection | Naslouchací proces odmítl připojení. |
| ListenerReturningHttpResponse | Naslouchací proces vrací odpověď HTTP. |  
| ListenerReturningHttpResponseFailed | Naslouchací proces vrací odpověď HTTP s kódem chyby. | 
 ListenerSentHttpResponse | Služba Relay obdržela odpověď HTTP od naslouchacího procesu. | 
| ListenerUnregistered | Naslouchací proces není zaregistrován. | 
| ListenerUnresponsive | Naslouchací proces nereaguje při vracení odpovědi. | 
| MessageSendingToListener | Zpráva se odesílá do naslouchacího procesu. |
| MessageSentToListener | Zpráva se odešle do naslouchacího procesu. | 
| NewListenerRegistered | Byl zaregistrován nový naslouchací proces. |
| NewSenderRegistering | Probíhá registrace nového odesílatele. | 
| ProcessingRequestFailed | Zpracování operace hybridního připojení se nezdařilo. | 
| SenderConnectionClosed | Připojení odesílatele je ukončeno. |
| SenderListenerConnectionEstablished | Odesílatel a naslouchací proces se úspěšně navázal připojení. |
| SenderSentHttpRequest | Odesílatel odeslal požadavek HTTP. | 


## <a name="next-steps"></a>Další kroky

Další informace o Azure Relay najdete v těchto tématech:

* [Úvod do Azure Relay](relay-what-is-it.md)
* [Začínáme s hybridními připojeními pro přenos](relay-hybrid-connections-dotnet-get-started.md)

---
title: Nastavení pro odesílání dat ve službě Azure Signal
description: Zavedení nastavení nadřazeného objektu a protokolu pro odesílání zpráv
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 7434e8796ddcd89968a0ffa0328a823d635f51c9
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2020
ms.locfileid: "84988577"
---
# <a name="upstream-settings"></a>Nastavení pro upstream

Nadřazený objekt je funkce, která umožňuje službě signalizace odesílat zprávy a události připojení do sady koncových bodů v režimu bez serveru. Pomocí nadřazeného způsobu lze vyvolat metodu centra z klientů v režimu bez serveru a koncovým bodům se dozvíte, když jsou připojení klienta připojena nebo odpojena.

> [!NOTE]
> Nastavení pro odesílání dat může konfigurovat jenom režim bez serveru.

## <a name="upstream-settings-details"></a>Podrobnosti o nadřazeném nastavení

Nastavení pro odesílání dat se skládá ze seznamu citlivých položek. Každá položka se skládá z `URL Template` , který určuje, kde se zprávy odesílají, sada `Rules` a `Authentication` konfigurací. V případě, že zadaná událost nastane, `Rules` bude položka zaškrtnuta v uvedeném pořadí a zprávy se budou odesílat na adresu URL nadřazeného objektu, který odpovídá první položce.

### <a name="url-template-settings"></a>Nastavení šablony URL

Adresa URL může být Parametrizovaná tak, aby podporovala různé vzory. Existují tři předdefinované parametry:

|Předdefinovaný parametr|Description|
|---------|---------|
|zdroj| Centrum je koncept signálu. Centrum je izolovaná jednotka, rozsah uživatelů a doručování zpráv je omezený na centrum.|
|kategorií| Kategorie může být jedna z následujících hodnot: <ul><li>**připojení**: události doby života připojení. Je aktivována, když je připojení klienta připojeno nebo odpojeno. Včetně *připojených* a *odpojených* událostí</li><li>**zprávy**: aktivuje se, když klienti vyvolávají metodu centra. Zahrnutí všech ostatních událostí s výjimkou těch v kategorii *připojení*</li></ul>|
|událostí| Pro kategorii *zprávy* je událost *cílem* ve [zprávě vyvolání](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) , kterou klienti odesílají. Pro kategorii *připojení* se používá pouze *připojeno* a *Odpojeno* .|

Tyto předdefinované parametry lze použít v `URL Pattern` parametrech a budou nahrazeny zadanou hodnotou při vyhodnocování NADŘAZENÉHO URL. Například 
```
http://host.com/{hub}/api/{category}/{event}
```
Když je připojení klienta v centru konverzace připojené, pošle se na adresu URL zpráva:
```
http://host.com/chat/api/connections/connected
```
Když klient v centru `chat` vyvolá metodu centra `broadcast` , pošle se na adresu URL zpráva:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rules-settings"></a>Nastavení pravidel

Můžete nastavit pravidla pro *pravidla centra*, *pravidla kategorie* a *pravidla událostí* samostatně. Pravidlo pro porovnání podporuje tři formáty. Jako příklad proveďte *pravidla událostí* :
- Pro vyhledání odpovídajících událostí použijte hvězdičku (*).
- Uživatelská čárka (,) pro připojení k více událostem Například `connected, disconnected` porovná události *připojené* a *Odpojeno*.
- Pro vyhledání události použijte úplný název události. Například `connected` odpovídá *připojené* události.

### <a name="authentication-settings"></a>Nastavení ověřování

*Ověřování* pro každou položku nastavení pro odesílání dat můžete nakonfigurovat samostatně. V případě nakonfigurovaného *ověřování*se token nastaví v záhlaví *ověřování* nadřazeného zprávy. Aktuální služba signalizace podporuje následující typ ověřování
- Žádné
- ManagedIdentity

Když vyberete *ManagedIdentity*, musíte povolit spravovanou identitu ve službě signalizace předem a volitelně zadat *prostředek*. Podrobnosti najdete v tématu [použití spravovaných identit pro službu Azure Signal](howto-use-managed-identity.md) .

## <a name="create-upstream-settings"></a>Vytvořit nastavení pro odesílání dat

### <a name="create-upstream-settings-via-azure-portal"></a>Vytvoření nastavení pro odesílání pomocí Azure Portal

1. Přejít na službu Azure Signal Service.
2. Klikněte na Bland *Nastavení* a přepněte *režim služby* na bez *serveru*. *Nastavení pro nadřazený datový proud* se zobrazí takto:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Nastavení pro upstream":::

3. Vyplní adresy URL do *nadřazeného vzoru adresy URL*a pak se v nastavení, jako jsou *pravidla centra* , zobrazí výchozí hodnota.
4. Pokud chcete nastavit nastavení, jako jsou *pravidla centra*, *pravidla událostí*, *pravidla kategorie* a *nadřazené ověřování*, klikněte na hodnotu *pravidla rozbočovače*. Stránka, která umožňuje upravit nastavení se zobrazí, jak je znázorněno níže:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Nastavení pro upstream":::

5. Pokud chcete nastavit *nadřazené ověřování*, ujistěte se, že jste nejdřív povolili spravovanou identitu, a pak v části *ověřování pomocí nadřazeného ověřování*vyberte *použít spravovanou identitu* . Podle vašich potřeb můžete zvolit jakékoli možnosti v části *ID prostředku ověřování*. Podrobnosti najdete v tématu [Povolení spravované identity](howto-use-managed-identity.md) .

### <a name="create-upstream-settings-via-arm-template"></a>Vytvoření nastavení pro odesílání pomocí šablony ARM

Chcete-li vytvořit nastavení pro odesílání pomocí [šablony Správce prostředků](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview), nastavte `upstream` vlastnost ve `properties` Vlastnosti. Následující fragmenty kódu ukazují, jak nastavit `upstream` vlastnost pro vytváření a aktualizaci nastavení pro odesílání dat.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="signalr-serverless-protocol"></a>Protokol bez serveru signálů

Služba signalizace odesílá zprávy do koncových bodů, které následují tyto protokoly.

### <a name="method"></a>Metoda

POST

### <a name="request-header"></a>Hlavička požadavku

|Name |Description|
|---------|---------|
|X-ASRS-ID připojení |ID připojení pro připojení klienta|
|X – ASRS – rozbočovač |Centrum, do kterého patří připojení klienta|
|X-ASRS-kategorie |Kategorie, do které zpráva patří|
|X-ASRS – událost |Událost, ke které zpráva patří|
|X-ASRS-signatura |HMAC, který se používá k ověření. Podrobnosti najdete v tématu [Signatura](#signature) .|
|X-ASRS-User-deklarace identity |Skupina deklarací identity připojení klienta|
|X-ASRS-User-ID |Identita uživatele klienta, který odesílá zprávu.|
|X-ASRS-Client-Query |Dotaz na požadavek při připojení klientů ke službě|
|Ověřování |Volitelný token při použití *ManagedIdentity* |

### <a name="request-body"></a>Text požadavku

#### <a name="connected"></a>Připojeno

Content-Type: Application/JSON

#### <a name="disconnected"></a>Propojení

Typ obsahu:`application/json`

|Name  |Typ  |Description  |
|---------|---------|---------|
|Chyba |řetězec |Chybová zpráva připojení je zavřená. Prázdné při zavření připojení bez chyby|

#### <a name="invocation-message"></a>Zpráva o vyvolání

Typ obsahu: `application/json` nebo`application/x-msgpack`

|Name  |Typ  |Description  |
|---------|---------|---------|
|InvocationId |řetězec | Volitelný řetězec představuje zprávu o vyvolání. Hledání podrobností ve [voláních](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Cíl |řetězec | Totéž jako u *události* a stejné jako *cíl* ve [zprávě vyvolání](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding). |
|Arguments |Pole objektu |Pole obsahující argumenty, které se mají použít pro metodu, na kterou odkazuje cíl. |

### <a name="signature"></a>Podpis

Služba vypočítá SHA256 kód pro tuto `X-ASRS-Connection-Id` hodnotu pomocí primárního přístupového klíče a sekundárního přístupového klíče jako `HMAC` klíč a nastaví ho v `X-ASRS-Signature` hlavičce při vytváření požadavků HTTP na nadřazený objekt:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Další kroky

- [Jak používat spravované identity pro službu Azure Signal Service](howto-use-managed-identity.md)
- [Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service](signalr-concept-serverless-development-config.md)

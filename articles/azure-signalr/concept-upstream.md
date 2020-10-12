---
title: Nastavení pro odesílání dat ve službě Azure Signal
description: Získejte základní informace o nastaveních nadřazeného objektu a protokolů pro odesílání zpráv.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: c3e317a87ba888fac3c069cc5327bd89c859e9de
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89514233"
---
# <a name="upstream-settings"></a>Nastavení pro upstream

Nadřazený objekt je funkce ve verzi Preview, která umožňuje službě Azure Signal Service odesílat zprávy a události připojení do sady koncových bodů v režimu bez serveru. Pomocí nadřazeného datového proudu můžete vyvolat metodu rozbočovače z klientů v režimu bez serveru a koncovým bodům se dá upozornit, když jsou připojení klienta připojena nebo odpojena.

> [!NOTE]
> Nastavení pro odesílání dat může konfigurovat jenom režim bez serveru.

## <a name="details-of-upstream-settings"></a>Podrobnosti o nastavení pro odesílání dat

Nastavení pro odesílání dat se skládá ze seznamu položek závislých na pořadí. Každá položka se skládá z těchto položek:

* Šablona adresy URL, která určuje, kam se zprávy odesílají.
* Sada pravidel.
* Konfigurace ověřování. 

V případě, že dojde k zadané události, jsou kontrolována pravidla položky v daném pořadí. Zprávy se odešlou do nadřazeného objektu URL pro odeslání první položky.

### <a name="url-template-settings"></a>Nastavení šablony URL

Můžete parametrizovat adresu URL pro podporu různých vzorů. Existují tři předdefinované parametry:

|Předdefinovaný parametr|Description|
|---------|---------|
|zdroj| Centrum je koncept služby Azure Signal. Centrum je jednotka izolace. Rozsah uživatelů a doručování zpráv je omezen na centrum.|
|kategorií| Kategorie může být jedna z následujících hodnot: <ul><li>**připojení**: události doby života připojení. Je aktivována, když je připojení klienta připojeno nebo odpojeno. Zahrnuje připojené a odpojené události.</li><li>**zprávy**: aktivuje se, když klienti vyvolají metodu rozbočovače. Zahrnuje všechny ostatní události kromě těch v kategorii **připojení** .</li></ul>|
|událostí| Pro kategorii **zprávy** je událost cílem ve [zprávě vyvolání](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) , kterou klienti odesílají. Pro kategorii **připojení** se používají jenom *připojená* a *odpojená* .|

Tyto předdefinované parametry lze použít ve vzoru adresy URL. Při hodnocení nadřazené adresy URL budou parametry nahrazeny zadanou hodnotou. Například: 
```
http://host.com/{hub}/api/{category}/{event}
```
Když je připojení klienta v centru konverzace připojené, pošle se na tuto adresu URL zpráva:
```
http://host.com/chat/api/connections/connected
```
Když klient v centru konverzace vyvolá metodu centra `broadcast` , pošle se na tuto adresu URL zpráva:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="rule-settings"></a>Nastavení pravidla

Můžete nastavit pravidla pro *pravidla centra*, *pravidla kategorie*a *pravidla událostí* samostatně. Pravidlo pro porovnání podporuje tři formáty. Jako příklad proveďte pravidla událostí:
- Pro vyhledání odpovídajících událostí použijte hvězdičku (*).
- K připojení více událostí použijte čárku (,). Například se `connected, disconnected` shoduje s připojenými a odpojenými událostmi.
- Pro vyhledání události použijte úplný název události. Například `connected` odpovídá připojené události.

> [!NOTE]
> Pokud používáte Azure Functions a [Trigger signálu](../azure-functions/functions-bindings-signalr-service-trigger.md), Trigger signálu Trigger vystaví jeden koncový bod v následujícím formátu: `https://<APP_NAME>.azurewebsites.net/runtime/webhooks/signalr?code=<API_KEY>` .
> Pro tuto adresu URL můžete pouze nakonfigurovat šablonu URL.

### <a name="authentication-settings"></a>Nastavení ověřování

Ověřování můžete nakonfigurovat pro každou položku nastavení pro odesílání zvlášť. Když nakonfigurujete ověřování, v `Authentication` záhlaví nadřazeného zprávy se nastaví token. Služba signalizace Azure v současné době podporuje následující typy ověřování:
- `None`
- `ManagedIdentity`

Když vyberete `ManagedIdentity` , musíte povolit spravovanou identitu ve službě Azure Signal Service předem a volitelně zadat prostředek. Podrobnosti najdete v tématu [spravované identity pro službu Azure Signal Service](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-the-azure-portal"></a>Vytvoření nastavení pro odesílání pomocí Azure Portal

1. Přejít na službu Azure Signal Service.
2. Vyberte **Nastavení** a přepněte **režim služby** na bez **serveru**. Zobrazí se nastavení pro odesílání dat:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Nastavení pro upstream":::

3. Přidejte adresy URL pod **vzorem nadřazených adres URL**. Pak nastavení, jako jsou **pravidla centra** , se zobrazí jako výchozí hodnota.
4. Pokud chcete nastavit nastavení pro **pravidla centra**, **pravidla událostí**, **pravidla kategorií**a **nadřazené ověřování**, vyberte hodnotu **pravidla rozbočovače**. Zobrazí se stránka, která umožňuje upravit nastavení:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Nastavení pro upstream":::

5. Pokud chcete nastavit **nadřazené ověřování**, ujistěte se, že jste nejdřív povolili spravovanou identitu. Pak vyberte **použít spravovanou identitu**. Podle vašich potřeb můžete zvolit jakékoli možnosti v části **ID prostředku ověřování**. Podrobnosti najdete v tématu [spravované identity pro službu Azure Signal Service](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-resource-manager-template"></a>Vytvoření nastavení pro odesílání pomocí Správce prostředků šablony

Chcete-li vytvořit nastavení pro odesílání pomocí [šablony Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview), nastavte `upstream` vlastnost ve `properties` Vlastnosti. Následující fragment kódu ukazuje, jak nastavit `upstream` vlastnost pro vytváření a aktualizaci nastavení pro odesílání dat.

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

## <a name="serverless-protocols"></a>Protokoly bez serveru

Služba signalizace Azure posílá zprávy do koncových bodů, které následují po následujících protokolech.

### <a name="method"></a>Metoda

POST

### <a name="request-header"></a>Hlavička požadavku

|Název |Popis|
|---------|---------|
|X-ASRS-ID připojení |ID připojení pro připojení klienta.|
|X – ASRS – rozbočovač |Centrum, do kterého patří klientské připojení.|
|X-ASRS-kategorie |Kategorie, do které zpráva patří|
|X-ASRS – událost |Událost, ke které zpráva náleží.|
|X-ASRS-signatura |Kód pro ověřování zpráv (HMAC) založený na hodnotě hash, který se používá k ověření. Podrobnosti najdete v tématu [Signatura](#signature) .|
|X-ASRS-User-deklarace identity |Skupina deklarací identity připojení klienta.|
|X-ASRS-User-ID |Identita uživatele klienta, který odesílá zprávu.|
|X-ASRS-Client-Query |Dotaz na požadavek při připojení klientů ke službě.|
|Authentication |Volitelný token při použití `ManagedIdentity` . |

### <a name="request-body"></a>Text požadavku

#### <a name="connected"></a>Připojeno

Content-Type: Application/JSON

#### <a name="disconnected"></a>Propojení

Typ obsahu: `application/json`

|Název  |Typ  |Description  |
|---------|---------|---------|
|Chyba |řetězec |Chybová zpráva uzavřeného připojení. Prázdné při zavření připojení bez chyby.|

#### <a name="invocation-message"></a>Zpráva o vyvolání

Typ obsahu: `application/json` nebo `application/x-msgpack`

|Název  |Typ  |Description  |
|---------|---------|---------|
|InvocationId |řetězec | Volitelný řetězec, který představuje zprávu o vyvolání. Hledání podrobností ve [voláních](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Cíl |řetězec | Totéž jako u události a stejné jako cíl ve [zprávě vyvolání](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding). |
|Argumenty |Pole objektu |Pole obsahující argumenty, které mají být použity pro metodu, na kterou odkazuje `Target` . |

### <a name="signature"></a>Podpis

Služba vypočítá SHA256 kód pro tuto `X-ASRS-Connection-Id` hodnotu pomocí primárního přístupového klíče a sekundárního přístupového klíče jako `HMAC` klíč. Služba ho nastaví v `X-ASRS-Signature` hlavičce při odesílání požadavků HTTP do nadřazeného:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Další kroky

- [Spravované identity pro službu Azure Signal Service](howto-use-managed-identity.md)
- [Vývoj a konfigurace služby Azure Functions s využitím služby Azure SignalR Service](signalr-concept-serverless-development-config.md)

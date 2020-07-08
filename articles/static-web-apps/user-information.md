---
title: Přístup k informacím o uživateli v Azure static Web Apps
description: Naučte se číst zprostředkovatele autorizace – vrácená uživatelská data.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 273ab5c20cf41b7305d72f1bfef5deeeb046c49a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "83597748"
---
# <a name="accessing-user-information-in-azure-static-web-apps-preview"></a>Přístup k informacím o uživateli ve službě Azure static Web Apps Preview

Služba Azure static Web Apps poskytuje informace o uživateli související s ověřováním prostřednictvím [koncového bodu přímého přístupu](#direct-access-endpoint) a [funkcí rozhraní API](#api-functions).

Mnoho uživatelských rozhraní velmi spoléhá na data ověřování uživatelů. Koncový bod přímého přístupu je rozhraní API nástroje, které zpřístupňuje informace o uživateli bez nutnosti implementace vlastní funkce. Kromě pohodlí nemůže koncový bod přímého přístupu podléhat prodlevám při počátečním spuštění, které jsou spojeny s architekturou bez serveru.

## <a name="client-principal-data"></a>Data objektu zabezpečení klienta

Objekt objektu zabezpečení klienta zpřístupňuje vaší aplikaci uživatelsky identifikovatelné informace. V objektu zabezpečení klienta jsou doporučené tyto vlastnosti:

| Vlastnost  | Popis |
|-----------|---------|
| `identityProvider` | Název [zprostředkovatele identity](authentication-authorization.md) |
| `userId` | Jedinečný identifikátor pro uživatele, který je specifický pro službu Azure static Web Apps. <ul><li>Hodnota je jedinečná na základě jednotlivých aplikací. Stejný uživatel například vrací jinou `userId` hodnotu v jiném statickém Web Apps prostředku.<li>Hodnota přetrvává po dobu života uživatele. Pokud odstraníte a přidáte stejného uživatele zpátky do aplikace, `userId` vygeneruje se nový.</ul>|
| `userDetails` | Uživatelské jméno nebo e-mailová adresa uživatele Někteří poskytovatelé vrátí [e-mailovou adresu uživatele](authentication-authorization.md), zatímco ostatní odešlou [popisovač uživatele](authentication-authorization.md). |
| `userRoles`     | Pole [přiřazených rolí uživatele](authentication-authorization.md) |

Následující příklad je ukázkový objekt objektu zabezpečení klienta:

```json
{
  "identityProvider": "facebook",
  "userId": "d75b260a64504067bfc5b2905e3b8182",
  "userDetails": "user@example.com",
  "userRoles": [ "anonymous", "authenticated" ]
}
```

## <a name="direct-access-endpoint"></a>Koncový bod přímého přístupu

Můžete odeslat `GET` žádost do `/.auth/me` trasy a získat přímý přístup k hlavním datům klienta. Když se stav zobrazení spoléhá na autorizační data, použijte tento přístup k dosažení nejlepšího výkonu.

V případě přihlášených uživatelů odpověď obsahuje objekt JSON objektu zabezpečení klienta. Žádosti od neověřených uživatelů vrátí `null` .

Pomocí rozhraní API pro [načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> můžete k hlavním datům klienta přistupovat pomocí následující syntaxe.

```javascript
async function getUserInfo() {
  const response = await fetch("/.auth/me");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUserInfo());
```

## <a name="api-functions"></a>Funkce rozhraní API

Hlavní data klienta se předávají funkcím API v `x-ms-client-principal` hlavičce požadavku. Hlavní data klienta se odesílají jako řetězec kódovaný v [kódování Base64](https://www.wikipedia.org/wiki/Base64)obsahující serializovaný objekt JSON.

Následující příklad funkce ukazuje, jak číst a vracet informace o uživateli.

```javascript
module.exports = async function (context, req) {
  const header = req.headers["x-ms-client-principal"];
  const encoded = Buffer.from(header, "base64");
  const decoded = encoded.toString("ascii");

  context.res = {
    body: {
      clientPrincipal: JSON.parse(decoded)
    }
  };
};
```

Za předpokladu, že je výše uvedená funkce pojmenována `user` , můžete použít rozhraní API pro [načtení](https://developer.mozilla.org/docs/Web/API/Fetch_API/Using_Fetch)<sup>1</sup> prohlížeče pro přístup k odpovědi rozhraní API pomocí následující syntaxe.

```javascript
async function getUser() {
  const response = await fetch("/api/user");
  const payload = await response.json();
  const { clientPrincipal } = payload;
  return clientPrincipal;
}

console.log(getUser());
```

<sup>1</sup> rozhraní API pro [načtení](https://caniuse.com/#feat=fetch) a operátor [await](https://caniuse.com/#feat=mdn-javascript_operators_await) nejsou v Internet Exploreru podporované.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Konfigurace nastavení aplikace](application-settings.md)

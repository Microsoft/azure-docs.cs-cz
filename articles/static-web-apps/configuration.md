---
title: Konfigurace statického Web Apps Azure
description: Naučte se konfigurovat trasy, vynutili pravidla zabezpečení a globální nastavení pro statické Web Apps Azure.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: cshoe
ms.openlocfilehash: b6779de0203246a60bdfa60ea110a0f0d5f26ff3
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106445"
---
# <a name="configure-azure-static-web-apps"></a>Konfigurace statického Web Apps Azure

Konfigurace pro statickou Web Apps Azure je definována v _staticwebapp.config.jsv_ souboru, který řídí následující nastavení:

- Směrování
- Authentication
- Autorizace
- Záložní pravidla
- Přepsání odpovědi HTTP
- Definice globálních hlaviček protokolu HTTP
- Vlastní typy MIME

## <a name="file-location"></a>Umístění souboru

Doporučené umístění pro _staticwebapp.config.jsv_ je složka nastavená jako `app_location` v [souboru pracovního postupu](./github-actions-workflow.md). Soubor je však možné umístit do libovolného umístění ve složce zdrojového kódu aplikace.

Podrobnosti najdete v [ukázkovém konfiguračním](#example-configuration-file) souboru.

> [!IMPORTANT]
> [ _routes.jspro_ soubor](./routes.md) se ignoruje, pokud existuje _staticwebapp.config.js_ .

## <a name="routes"></a>Trasy

Pravidla směrování umožňují definovat vzor adres URL, které umožňují přístup k vaší aplikaci na web. Trasy jsou definovány jako pole pravidel směrování. Příklady použití najdete v [příkladech konfiguračního souboru](#example-configuration-file) .

- Pravidla se spouštějí v pořadí, ve kterém se zobrazují v poli `routes` .
- Vyhodnocení pravidla se zastaví při první shodě – pravidla směrování se nezřetězená dohromady.
- Máte plnou kontrolu nad názvy vlastních rolí.
  - Existuje několik předdefinovaných názvů rolí, které zahrnují [`anonymous`](./authentication-authorization.md) a [`authenticated`](./authentication-authorization.md) .

Související směrování se významně překrývá s ověřováním (identifikace uživatele) a autorizací (přiřazování schopností uživateli). Nezapomeňte si přečíst průvodce [ověřováním a autorizací](authentication-authorization.md) spolu s tímto článkem.

Výchozím souborem pro statický obsah je soubor *index.html* .

## <a name="defining-routes"></a>Definování tras

Každé pravidlo se skládá ze vzoru směrování spolu s jednou nebo více volitelnými vlastnostmi pravidla. Příklady použití najdete v [příkladech konfiguračního souboru](#example-configuration-file) .

| Vlastnost pravidla  | Vyžadováno | Výchozí hodnota | Komentář                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Yes      | Není k dispozici          | Vzor trasy požadovaný volajícím.<ul><li>[Zástupné znaky](#wildcards) jsou podporovány na konci cest směrování.<ul><li>Například _správce tras/ \*_ odpovídá libovolné trase v cestě _správce_ .</ul></ul>|
| `rewrite`        | No       | Není k dispozici          | Definuje soubor nebo cestu vrácenou z požadavku.<ul><li>Se vzájemně vylučují pro `redirect` pravidlo<li>Pravidla přepsání nemění umístění prohlížeče.<li>Hodnoty musí být relativní vzhledem ke kořenu aplikace.</ul>  |
| `redirect`        | No       | Není k dispozici          | Definuje cíl přesměrování souboru nebo cesty pro požadavek.<ul><li>Se vzájemně vylučují pro `rewrite` pravidlo.<li>Pravidla přesměrování mění umístění prohlížeče.<li>Výchozí kód odpovědi je [`302`](https://developer.mozilla.org/docs/Web/HTTP/Status/302) (dočasné přesměrování), ale můžete přepsat pomocí [`301`](https://developer.mozilla.org/docs/Web/HTTP/Status/301) (trvalé přesměrování).</ul> |
| `allowedRoles` | No       | Anonymous     | Definuje seznam názvů rolí vyžadovaných pro přístup k trase. <ul><li>Mezi platné znaky patří `a-z` , `A-Z` , `0-9` a `_` .<li>Integrovaná role [`anonymous`](./authentication-authorization.md) platí pro všechny neověřené uživatele.<li>Integrovaná role se [`authenticated`](./authentication-authorization.md) vztahuje na všechny přihlášené uživatele.<li>Uživatelé musí patřit do alespoň jedné role.<li>Role se shodují na _nebo_ bázi.<ul><li>Pokud je uživatel v některé z uvedených rolí, pak je udělen přístup.</ul><li>Jednotliví uživatelé jsou přidruženi k rolím prostřednictvím [pozvánk](authentication-authorization.md).</ul> |
| `headers`<a id="route-headers"></a> | No | Není k dispozici | Sada [hlaviček http](https://developer.mozilla.org/docs/Web/HTTP/Headers) přidaných k odpovědi <ul><li>Hlavičky specifické pro trasu, [`globalHeaders`](#global-headers) Pokud je hlavička specifická pro trasu shodná s globální hlavičkou, která je v odpovědi.<li>Chcete-li odebrat záhlaví, nastavte hodnotu na prázdný řetězec.</ul> |
| `statusCode`   | No       | `200`, `301` nebo `302` pro přesměrování | [Stavový kód protokolu HTTP](https://developer.mozilla.org/docs/Web/HTTP/Status) odpovědi. |
| `methods` | No | Všechny metody | Seznam metod požadavků, které odpovídají trase. K dispozici jsou tyto metody: `GET` , `HEAD` , `POST` , `PUT` , `DELETE` , `CONNECT` , `OPTIONS` , `TRACE` a `PATCH` . |

Každá vlastnost má v kanálu požadavků a odpovědí určitý účel.

| Účel | Vlastnosti |
|---|---|
| Rozlišovat trasy | `route`, `methods` |
| Autorizovat po porovnání trasy | `allowedRoles` |
| Proces po porovnání a autorizaci pravidla | `rewrite` (upravuje žádost) <br><br>`redirect`, `headers` , `statusCode` (upraví odpověď) |

## <a name="securing-routes-with-roles"></a>Zabezpečení tras s rolemi

Trasy jsou zabezpečené přidáním jednoho nebo více názvů rolí do `allowedRoles` pole pravidla a uživatelé jsou přidruženi k vlastním rolím prostřednictvím [pozvánk](./authentication-authorization.md). Příklady použití najdete v [příkladech konfiguračního souboru](#example-configuration-file) .

Ve výchozím nastavení každý uživatel patří do předdefinované `anonymous` role a všichni přihlášení uživatelé jsou členy této `authenticated` role.

Chcete-li například omezit směrování pouze na ověřené uživatele, přidejte do pole vestavěnou `authenticated` roli `allowedRoles` .

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

V poli můžete podle potřeby vytvořit nové role `allowedRoles` . Pro omezení trasy jenom na správce můžete v poli definovat vlastní roli s názvem `administrator` `allowedRoles` .

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Máte plnou kontrolu nad názvy rolí; neexistuje žádný seznam, na který musí vaše role dodržovat.
- Jednotliví uživatelé jsou přidruženi k rolím prostřednictvím [pozvánk](authentication-authorization.md).

## <a name="wildcards"></a>Zástupné znaky

Pravidla zástupných znaků odpovídají všem požadavkům ve schématu směrování, jsou podporovány pouze na konci cesty a lze je filtrovat podle přípony souboru. Příklady použití najdete v [příkladech konfiguračního souboru](#example-configuration-file) .

Chcete-li například implementovat trasy pro aplikaci kalendáře, můžete přepsat všechny adresy URL, které spadají do trasy v _kalendáři_ , aby sloužily jedinému souboru.

```json
{
  "route": "/calendar/*",
  "rewrite": "/calendar.html"
}
```

Soubor _calendar.html_ pak může použít směrování na straně klienta pro různé zobrazení odchylek URL `/calendar/january/1` , jako jsou, `/calendar/2020` a `/calendar/overview` .

Můžete filtrovat shody se zástupnými znaky podle přípony souboru. Pokud jste chtěli například přidat pravidlo, které odpovídá pouze souborům HTML v dané cestě, mohli byste vytvořit následující pravidlo:

```json
{
  "route": "/articles/*.html",
  "headers" : {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Chcete-li filtrovat více přípon souborů, zahrňte možnosti ve složených závorkách, jak je znázorněno v následujícím příkladu:

```json
{
  "route": "/images/thumbnails/*.{png,jpg,gif}",
  "headers": {
    "Cache-Control": "public, max-age=604800, immutable"
  }
}
```

Mezi běžné případy použití zástupných znaků patří:

- Obsluha konkrétního souboru pro celý vzor cesty
- Mapování různých metod HTTP na celý vzor cesty
- Vynucování ověřovacích a autorizačních pravidel
- Implementace specializovaných pravidel ukládání do mezipaměti

## <a name="fallback-routes"></a>Záložní trasy

Jednostránkové aplikace často spoléhají na směrování na straně klienta. Tato pravidla směrování na straně klienta aktualizují umístění okna prohlížeče bez podání požadavků zpět na server. Pokud stránku aktualizujete nebo přejdete přímo na adresy URL vygenerované pravidly směrování na straně klienta, je potřeba pro poskytování příslušné stránky HTML (což je obecně _index.html_ pro aplikaci na straně klienta) používat záložní cestu na straně serveru.

Aplikaci můžete nakonfigurovat tak, aby používala pravidla, která implementují záložní cestu, jak je znázorněno v následujícím příkladu, který používá zástupný znak cesty s filtrem souborů:

```json
{
  "navigationFallback": {
    "rewrite": "/index.html",
    "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
  }
}
```

V níže uvedené struktuře souborů jsou k dispozici následující výsledky s tímto pravidlem.

```files
├── images
│   ├── logo.png
│   ├── headshot.jpg
│   └── screenshot.gif
│
├── css
│   └── global.css
│
└── index.html
```

| Požadavky na... | vrátí... | se stavem... |
| --- | --- | --- |
| *upozorňován* | Soubor */index.html* | `200` |
| */images/logo.png* | Soubor obrázku  | `200` |
| */images/icon.svg* | Soubor */index.html* – protože ve filtru není uvedená Přípona souboru *SVG* `/images/*.{png,jpg,gif}`   | `200` |
| */images/unknown.png* | Chyba nenalezeného souboru  | `404` |
| */css/unknown.css* | Chyba nenalezeného souboru  | `404` |
| */css/global.css* | Soubor šablony stylů | `200` |
| Jakýkoli jiný soubor mimo složku */images* nebo */CSS* | Soubor */index.html* | `200` |

## <a name="global-headers"></a>Globální hlavičky

`globalHeaders`Oddíl poskytuje sadu [hlaviček protokolu HTTP](https://developer.mozilla.org/docs/Web/HTTP/Headers) použitých u každé odpovědi, pokud není přepsán pravidlem [hlavičky trasy](#route-headers) , jinak se vrátí sjednocení hlaviček z trasy a globálních hlaviček.

Příklady použití najdete v [příkladech konfiguračního souboru](#example-configuration-file) .

Chcete-li odebrat záhlaví, nastavte hodnotu na prázdný řetězec ( `""` ).

Mezi běžné případy použití globálních hlaviček patří:

- Vlastní pravidla ukládání do mezipaměti
- Vynucování zásad zabezpečení
- Nastavení kódování

## <a name="response-overrides"></a>Přepsání odpovědí

V `responseOverrides` části najdete možnost definovat vlastní odpověď, pokud by server jinak vrátil kód chyby. Příklady použití najdete v [příkladech konfiguračního souboru](#example-configuration-file) .

Pro přepsání jsou k dispozici následující kódy HTTP:

| Stavový kód | Význam | Možná příčina |
| --- | --- | --- |
| [400](https://developer.mozilla.org/docs/Web/HTTP/Status/400) | Chybný požadavek | Neplatné propojení pozvánky |
| [401](https://developer.mozilla.org/docs/Web/HTTP/Status/401) | Neautorizováno | Požadavek na stránky s omezeným přístupem během neověřené |
| [403](https://developer.mozilla.org/docs/Web/HTTP/Status/403) | Forbidden |<ul><li>Uživatel je přihlášen, ale nemá role potřebné k zobrazení stránky.<li>Uživatel je přihlášen, ale modul runtime nemůže získat podrobnosti o uživateli z deklarací identity identity.<li>K webu se přihlásilo příliš mnoho uživatelů s vlastními rolemi, modul runtime proto nemůže uživatele přihlašovat.</ul> |
| [404](https://developer.mozilla.org/docs/Web/HTTP/Status/404) | Nenalezeno | Soubor se nenašel. |

Následující příklad konfigurace ukazuje, jak přepsat kód chyby.

```json
{
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html",
            "statusCode": 200
        },
        "401": {
            "statusCode": 302,
            "redirect": "/login"
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html",
            "statusCode": 200
        },
        "404": {
            "rewrite": "/custom-404.html",
            "statusCode": 200
        }
    }
}
```

## <a name="example-configuration-file"></a>Ukázkový konfigurační soubor

```json
{
    "routes": [
        {
            "route": "/profile",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/admin/*",
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/images/*",
            "headers": {
                "cache-control": "must-revalidate, max-age=15770000"
            }
        },
        {
            "route": "/api/*",
            "methods": [ "GET" ],
            "allowedRoles": ["registeredusers"]
        },
        {
            "route": "/api/*",
            "methods": [ "PUT", "POST", "PATCH", "DELETE" ],
            "allowedRoles": ["administrator"]
        },
        {
            "route": "/api/*",
            "allowedRoles": ["authenticated"]
        },
        {
            "route": "/customers/contoso",
            "allowedRoles": ["administrator", "customers_contoso"]
        },
        {
            "route": "/login",
            "rewrite": "/.auth/login/github"
        },
        {
            "route": "/.auth/login/twitter",
            "statusCode": 404
        },
        {
            "route": "/logout",
            "redirect": "/.auth/logout"
        },
        {
            "route": "/calendar/*",
            "rewrite": "/calendar.html"
        },
        {
            "route": "/specials",
            "redirect": "/deals",
            "statusCode": 301
        }
    ],
    "navigationFallback": {
      "rewrite": "index.html",
      "exclude": ["/images/*.{png,jpg,gif}", "/css/*"]
    },
    "responseOverrides": {
        "400" : {
            "rewrite": "/invalid-invitation-error.html"
        },
        "401": {
            "redirect": "/login",
            "statusCode": 302
        },
        "403": {
            "rewrite": "/custom-forbidden-page.html"
        },
        "404": {
            "rewrite": "/404.html"
        }
    },
    "globalHeaders": {
        "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
    },
    "mimeTypes": {
        ".json": "text/json"
    }
}
```

Na základě výše uvedené konfigurace si přečtěte následující scénáře.

| Požadavky na... | výsledky... |
| --- | --- |
| _/Profile_ | Ověřeným uživatelům se obsluhuje soubor _/profile/index.html_ . Neověření uživatelé budou přesměrováni na _/Login_. |
| _/admin_ | Ověřeným uživatelům v roli _správce_ se obsluhuje soubor _/admin/index.html_ . Ověřeným uživatelům, kteří nejsou v roli _správce_ , se doplní `403` Chyba <sup>1</sup>. Neověření uživatelé budou přesměrováni na _/Login_. |
| _/logo.png_ | Slouží jako obrázek s vlastním pravidlem mezipaměti, kde maximální stáří je trochu více než 182 dní (15 770 000 sekund). |
| _/api/admin_ | `GET` žádosti od ověřených uživatelů v roli _registeredusers_ se odesílají do rozhraní API. Ověření uživatelé, kteří nejsou v roli _registeredusers_ , a neověření uživatelé se doplní `401` chybou.<br/><br/>`POST``PUT` `PATCH` `DELETE` do rozhraní API se odesílají žádosti,, a od ověřených uživatelů v roli _správce_ . U ověřených uživatelů, kteří nejsou v roli _správce_ , se zobrazí chyba neověření uživatelé `401` . |
| _/customers/contoso_ | Ověřeným uživatelům, kteří patří k rolím _správce_ nebo _zákazníkům \_ společnosti Contoso_ , je _/Customers/contoso/soubor index.html_ . U ověřených uživatelů, kteří nejsou v roli _správce_ nebo _Uživatelé \_ společnosti Contoso_ , se zobrazí `403` Chyba <sup>1</sup>. Neověření uživatelé budou přesměrováni na _/Login_. |
| _/Login_ | Neověření uživatelé mají k ověření pomocí GitHubu výzvy. |
| _/.auth/login/twitter_ | V případě, že pravidlo směrování zakáže autorizaci na Twitteru, `404` vrátí se chyba, která se vrátí k obsluze _/index.html_ se `200` stavovým kódem. |
| _/logout_ | Uživatelé se odhlásí od jakéhokoli poskytovatele ověřování. |
| _/calendar/2021/01_ | V prohlížeči se dosloužil soubor _/calendar.html_ . |
| _/specials_ | Prohlížeč se trvale přesměruje na _/Deals_. |
| _/data.jsna_ | Soubor poskytovaný pomocí `text/json` typu MIME. |
| _/About_ nebo libovolná složka, která odpovídá vzorům směrování na straně klienta | Soubor _/index.html_ se zpracovává se `200` stavovým kódem. |
| Neexistující soubor ve složce _/images/_ | `404`Chyba. |

<sup>1</sup> můžete zadat vlastní chybovou stránku pomocí [pravidla přepsání odpovědi](#response-overrides).

## <a name="restrictions"></a>Omezení

Pro _staticwebapps.config.jsv_ souboru existují následující omezení.

- Maximální velikost souboru je 100 KB.
- Maximální počet 50 jedinečných rolí

Obecná omezení a omezení najdete v článku věnovaném [kvótám](quotas.md) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení ověřování a autorizace](authentication-authorization.md)

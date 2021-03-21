---
title: Trasy v Azure static Web Apps
description: Přečtěte si o pravidlech směrování back-endu a o tom, jak zabezpečit trasy s rolemi.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 5cbcbcf8914a663a6d039abecd6a4488eaf677b2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101739640"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Trasy ve službě Azure static Web Apps Preview

> [!IMPORTANT]
> Funkce definované v *routes.jsv* souboru jsou teď zastaralé a v [konfiguračním souboru](./configuration.md#routes)Azure static Web Apps se implementují lépe.

Směrování v Azure static Web Apps definuje pravidla směrování back-endu a autorizační chování pro statický obsah a rozhraní API<sup>1</sup>. Pravidla jsou definována jako pole pravidel v _routes.jsv_ souboru.

- _routes.js_ souboru musí existovat v kořenu složky artefaktů sestavení aplikace.
- Pravidla se spouštějí v pořadí, ve kterém se zobrazují v poli `routes` .
- Vyhodnocení pravidla se zastaví při první shodě. Pravidla směrování nejsou zřetězená dohromady.
- Role jsou definované v _routes.jspro_ soubor a uživatelé jsou k rolím přidruženi prostřednictvím [pozvánk](authentication-authorization.md).
- Máte plnou kontrolu nad názvy rolí.

Téma směrování se významně překrývá s koncepty ověřování a autorizace. Nezapomeňte si přečíst průvodce [ověřováním a autorizací](authentication-authorization.md) spolu s tímto článkem.

Podrobnosti najdete v [souboru s ukázkovým směrováním](#example-route-file) .

## <a name="location"></a>Umístění

> [!IMPORTANT]
> Funkce definované v *routes.jsv* souboru jsou teď zastaralé a v [konfiguračním souboru](./configuration.md#routes)Azure static Web Apps se implementují lépe.

_routes.js_ souboru musí existovat v kořenu složky artefaktů sestavení aplikace. Pokud vaše webová aplikace zahrnuje krok sestavení, který kopíruje sestavené soubory z konkrétní složky do vaší složky artefaktů sestavení, musí _routes.jsv_ souboru existovat v této konkrétní složce.

Následující tabulka uvádí vhodné umístění pro vložení _routes.js_ do souboru pro několik rozhraní front-end a knihoven.

|Architektura/knihovna | Umístění  |
|---------|----------|
| Angular | _prostředky_   |
| React   | _public_  |
| Svelte  | _public_   |
| Vue     | _public_ |
| Blazor  | _wwwroot_ |

Výše uvedená tabulka je reprezentativní jenom pro několik architektur a knihoven kompatibilních se službou Azure static Web Apps. Další informace najdete v tématu [konfigurace front-endové architektur a knihoven](./front-end-frameworks.md) .

## <a name="defining-routes"></a>Definování tras

> [!IMPORTANT]
> Funkce definované v *routes.jsv* souboru jsou teď zastaralé a v [konfiguračním souboru](./configuration.md#routes)Azure static Web Apps se implementují lépe.

Trasy jsou definovány v _routes.jsv_ souboru jako pole pravidel směrování pro danou `routes` vlastnost. Každé pravidlo se skládá ze vzoru směrování spolu s jednou nebo více volitelnými vlastnostmi pravidla. Příklady použití najdete v [ukázkovém souboru směrování](#example-route-file) .

| Vlastnost pravidla  | Vyžadováno | Výchozí hodnota | Komentář                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Yes      | Není k dispozici          | Vzor trasy požadovaný volajícím.<ul><li>[Zástupné znaky](#wildcards) jsou podporovány na konci cest směrování. Například _správce tras/ \*_ odpovídá libovolné trase v cestě _správce_ .<li>Výchozí soubor trasy je _index.html_.</ul>|
| `serve`        | No       | Není k dispozici          | Definuje soubor nebo cestu vrácenou z požadavku. Cesta k souboru a název se mohou lišit od požadované cesty. Pokud není `serve` definována hodnota, použije se požadovaná cesta. Parametry QueryString nejsou podporovány; `serve` hodnoty musí ukazovat na skutečné soubory.  |
| `allowedRoles` | No       | Anonymous     | Pole názvů rolí <ul><li>Mezi platné znaky patří `a-z` , `A-Z` , `0-9` a `_` .<li>Předdefinovaná role `anonymous` platí pro všechny neověřené uživatele.<li>Předdefinovaná role `authenticated` se vztahuje na všechny přihlášené uživatele.<li>Uživatelé musí patřit do alespoň jedné role.<li>Role se shodují na _nebo_ bázi. Pokud je uživatel v některé z uvedených rolí, pak je udělen přístup.<li>Jednotliví uživatelé jsou přidruženi k rolím prostřednictvím [pozvánk](authentication-authorization.md).</ul> |
| `statusCode`   | No       | 200           | Odpověď [kódu stavu HTTP](https://wikipedia.org/wiki/List_of_HTTP_status_codes) pro požadavek. |

## <a name="securing-routes-with-roles"></a>Zabezpečení tras s rolemi

> [!IMPORTANT]
> Funkce definované v *routes.jsv* souboru jsou teď zastaralé a v [konfiguračním souboru](./configuration.md#routes)Azure static Web Apps se implementují lépe.

Trasy jsou zabezpečené přidáním jednoho nebo více názvů rolí do `allowedRoles` pole pravidla. Příklady použití najdete v [ukázkovém souboru směrování](#example-route-file) .

Ve výchozím nastavení každý uživatel patří do předdefinované `anonymous` role a všichni přihlášení uživatelé jsou členy této `authenticated` role. Chcete-li například omezit směrování pouze na ověřené uživatele, přidejte do pole vestavěnou `authenticated` roli `allowedRoles` .

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

V poli můžete podle potřeby vytvořit nové role `allowedRoles` . Chcete-li zakázat směrování pouze správcům, můžete v poli definovat `administrator` roli `allowedRoles` .

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Máte plnou kontrolu nad názvy rolí; neexistuje žádný seznam, na který musí vaše role dodržovat.
- Jednotliví uživatelé jsou přidruženi k rolím prostřednictvím [pozvánk](authentication-authorization.md).

## <a name="wildcards"></a>Zástupné znaky

> [!IMPORTANT]
> Funkce definované v *routes.jsv* souboru jsou teď zastaralé a v [konfiguračním souboru](./configuration.md#routes)Azure static Web Apps se implementují lépe.

Pravidla zástupných znaků odpovídají všem požadavkům v rámci daného modelu směrování. Pokud `serve` v pravidle definujete hodnotu, bude jako odpověď obsluhován pojmenovaný soubor nebo cesta.

Chcete-li například implementovat trasy pro aplikaci kalendáře, můžete všechny adresy URL, které spadají do trasy v _kalendáři_ , namapovat tak, aby sloužily jedinému souboru.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

Soubor _calendar.html_ pak může použít směrování na straně klienta pro různé zobrazení odchylek URL `/calendar/january/1` , jako jsou, `/calendar/2020` a `/calendar/overview` .

Můžete také zabezpečit trasy pomocí zástupných znaků. V následujícím příkladu všechny soubory požadované v cestě _správce_ vyžadují ověřeného uživatele, který je členem role _správce_ .

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> Požadavky na soubory, na které se odkazuje pomocí doručeného souboru, se vyhodnocují jenom pro kontroly ověřování. Například požadavky na soubor CSS v rámci cesty zástupných znaků slouží jako soubor CSS a nikoli to, co je definováno jako `serve` soubor. Soubor CSS se obsluhuje, dokud uživatel splňuje požadované požadavky na ověření.

## <a name="fallback-routes"></a>Záložní trasy

> [!IMPORTANT]
> Funkce definované v *routes.jsv* souboru jsou teď zastaralé a v [konfiguračním souboru](./configuration.md#routes)Azure static Web Apps se implementují lépe.

Jednostránkové aplikace, bez ohledu na to, zda používají předdefinované rozhraní JavaScript nebo knihovny nebo platformy WebAssembly, jako je Blazor, se často spoléhají na směrování na straně klienta pro navigaci webové aplikace. Tato pravidla směrování na straně klienta aktualizují umístění okna prohlížeče bez podání požadavků zpět na server. Pokud stránku aktualizujete nebo přejdete přímo do umístění generovaných pravidly směrování na straně klienta, je pro obsluhu příslušné stránky HTML nutná záložní trasa na straně serveru.

V následujícím příkladu je uvedena společná záložní trasa:

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Záložní trasa musí být uvedena jako poslední ve svých pravidlech směrování, protože zachytává všechny požadavky, které nejsou zachyceny dříve definovanými pravidly.

## <a name="redirects"></a>Přesměruje

> [!IMPORTANT]
> Funkce definované v *routes.jsv* souboru jsou teď zastaralé a v [konfiguračním souboru](./configuration.md#routes)Azure static Web Apps se implementují lépe.

Pro přesměrování požadavků z jedné trasy do druhé můžete použít stavové kódy http [301](https://en.wikipedia.org/wiki/HTTP_301) a [302](https://en.wikipedia.org/wiki/HTTP_302) .

Následující pravidlo například vytvoří přesměrování 301 od _old-page.html_ do _new-page.html_.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

Přesměrování také fungují s cestami, které nedefinují odlišné soubory.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Vlastní chybové stránky

> [!IMPORTANT]
> Funkce definované v *routes.jsv* souboru jsou teď zastaralé a v [konfiguračním souboru](./configuration.md#routes)Azure static Web Apps se implementují lépe.

Uživatelé mohou narazit na řadu různých situací, které mohou mít za následek chybu. Pomocí `platformErrorOverrides` pole můžete v reakci na tyto chyby poskytnout vlastní prostředí. Chcete-li umístit pole do _routes.js_ souboru, přečtěte si [ukázkový soubor směrování](#example-route-file) .

> [!NOTE]
> Jakmile požadavek nastaví úroveň přepsání platformy, pravidla směrování se znovu nespustí.

V následující tabulce jsou uvedeny potlačení dostupných chyb platformy:

| Typ chyby  | Stavový kód HTTP | Popis |
|---------|---------|---------|
| `NotFound` | 404  | Na serveru se nenašla stránka. |
| `Unauthenticated` | 401 | Uživatel není přihlášený pomocí [zprostředkovatele ověřování](authentication-authorization.md). |
| `Unauthorized_InsufficientUserInformation` | 401 | Uživatelský účet na zprostředkovateli ověřování není nakonfigurovaný tak, aby vystavoval požadovaná data. K této chybě může dojít v situacích, kdy se aplikace zeptá poskytovatele ověřování pro e-mailovou adresu uživatele, ale uživatel se rozhodl omezit přístup k e-mailové adrese. |
| `Unauthorized_InvalidInvitationLink` | 401 | Buď vypršela platnost pozvánky, nebo uživatel následoval odkaz na pozvánku vygenerovaný pro jiného příjemce.  |
| `Unauthorized_MissingRoles` | 401 | Uživatel není členem požadované role. |
| `Unauthorized_TooManyUsers` | 401 | Lokalita dosáhla maximálního počtu uživatelů a server omezuje další doplňky. Tato chyba je k dispozici klientovi, protože neexistuje žádné omezení počtu [požadavků](authentication-authorization.md) , které můžete vygenerovat, a někteří uživatelé nemusí nikdy přijmout svou pozvánku.|
| `Unauthorized_Unknown` | 401 | Při pokusu o ověření uživatele došlo k neznámému problému. Jednou z příčin této chyby je, že uživatel není rozpoznaný, protože neudělil souhlas aplikaci.|

## <a name="custom-mime-types"></a>Vlastní typy MIME

> [!IMPORTANT]
> Funkce definované v *routes.jsv* souboru jsou teď zastaralé a v [konfiguračním souboru](./configuration.md#routes)Azure static Web Apps se implementují lépe.

`mimeTypes`Objekt, který je uveden na stejné úrovni jako `routes` pole, umožňuje přidružit [typy MIME](https://developer.mozilla.org/docs/Web/HTTP/Basics_of_HTTP/MIME_types/Common_types) k příponám souborů.

```json
{
    "routes": [],
    "mimeTypes": {
        "custom": "text/html"
    }
}
```

V předchozím příkladu jsou všechny soubory s `.custom` příponou obsluhovány pomocí `text/html` typu MIME.

Při práci s typy MIME jsou důležité tyto okolnosti:

- Klíče nemohou mít hodnotu null ani být prázdné ani delší než 50 znaků.
- Hodnoty nemůžou být null ani prázdné ani delší než 1000 znaků.

> [!NOTE]
> Statický Web Apps rozumí aplikacím v Blazor a očekávaným typům MIME pro soubory WASM a DLL, není nutné přidávat mapování pro ty.

## <a name="default-headers"></a>Výchozí hlavičky

> [!IMPORTANT]
> Funkce definované v *routes.jsv* souboru jsou teď zastaralé a v [konfiguračním souboru](./configuration.md#routes)Azure static Web Apps se implementují lépe.

`defaultHeaders`Objekt, který je uveden na stejné úrovni jako `routes` pole, umožňuje přidat, upravit nebo odebrat [hlavičky odpovědí](https://developer.mozilla.org/docs/Web/HTTP/Headers).

Zadáním hodnoty pro záhlaví buď přidáte nebo změníte hlavičku. Zadáním prázdné hodnoty odeberete hlavičku, která má být obsluhována klientovi.

```json
{
    "routes": [],
    "defaultHeaders": {
      "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'",
      "cache-control": "must-revalidate, max-age=6000",
      "x-dns-prefetch-control": ""
    }
}
```

V předchozím příkladu `content-security-policy` je přidána nová hlavička, `cache-control` mění se výchozí hodnota serveru a `x-dns-prefetch-control` Hlavička je odebrána.

Při práci s hlavičkami jsou důležité následující důležité informace:

- Klíč nemůže mít hodnotu null ani být prázdný.
- Hodnoty null nebo prázdné hodnoty odstraňují hlavičku ze zpracování.
- Klíče nebo hodnoty nesmí být delší než 8 000 znaků.
- Definované hlavičky jsou obsluhovány se všemi požadavky.
- Hlavičky definované v _routes.js_ platí jenom pro statický obsah. V kódu funkce můžete přizpůsobit hlavičky odpovědí koncového bodu rozhraní API.

## <a name="example-route-file"></a>Příklad souboru směrování

> [!IMPORTANT]
> Funkce definované v *routes.jsv* souboru jsou teď zastaralé a v [konfiguračním souboru](./configuration.md#routes)Azure static Web Apps se implementují lépe.

Následující příklad ukazuje, jak vytvořit pravidla směrování pro statický obsah a rozhraní API v _routes.js_ v souboru. Některé trasy používají [systémovou složku _/.auth_](authentication-authorization.md) , která přistupuje k koncovým bodům souvisejícím s ověřováním.

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
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "statusCode": "302",
      "serve": "/login"
    }
  ],
  "defaultHeaders": {
    "content-security-policy": "default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'"
  },
  "mimeTypes": {
      "custom": "text/html"
  }
}
```

Následující příklady popisují, co se stane, když požadavek odpovídá pravidlu.

| Požadavky na... | Výsledek... |
|--|--|--|
| _/Profile_ | Ověřeným uživatelům se obsluhuje soubor _/profile/index.html_ . Neověření uživatelé přesměrováni na _/Login_. |
| _/admin/reports_ | Ověřeným uživatelům v roli _správců_ se obsluhuje soubor _/admin/Reports/index.html_ . Ověřeným uživatelům, kteří nejsou v roli _Administrators_ , se doplní chybová zpráva 401.<sup>2</sup>. Neověření uživatelé přesměrováni na _/Login_. |
| _/api/admin_ | Žádosti od ověřených uživatelů v roli _Administrators_ se odesílají do rozhraní API. Ověřeným uživatelům, kteří nejsou v roli _správců_ a neověřeným uživatelům, je zpracována chyba 401. |
| _/customers/contoso_ | Ověřeným uživatelům, kteří patří do rolí _správců nebo správců_ _\_ společnosti Contoso_ , je dodáván soubor _/Customers/contoso/index.html_ <sup>2</sup>. U ověřených uživatelů, kteří nejsou ve _skupině Administrators_ nebo _Customers role \_ Contoso_ , se doplní chyba 401. Neověření uživatelé přesměrováni na _/Login_. |
| _/Login_ | Neověření uživatelé mají k ověření pomocí GitHubu výzvy. |
| _/.auth/login/twitter_ | Autorizace pomocí Twitteru je zakázaná. Server odpoví chybou 404. |
| _/logout_ | Uživatelé se odhlásí od jakéhokoli poskytovatele ověřování. |
| _/calendar/2020/01_ | V prohlížeči se dosloužil soubor _/calendar.html_ . |
| _/specials_ | Prohlížeč se přesměruje na _/Deals_. |
| _/unknown-folder_ | Soubor _/custom-404.html_ se obsluhuje. |
| Soubory s `.custom` příponou | Jsou obsluhovány s `text/html` typem MIME |

Všechny odpovědi obsahují `content-security-policy` záhlaví s hodnotou `default-src https: 'unsafe-eval' 'unsafe-inline'; object-src 'none'` .

<sup>1</sup> pravidla směrování pro funkce rozhraní API podporují jenom [přesměrování](#redirects) a [zabezpečení tras s rolemi](#securing-routes-with-roles).

<sup>2</sup> můžete zadat vlastní chybovou stránku definováním `Unauthorized_MissingRoles` pravidla v poli `platformErrorOverrides` .

## <a name="restrictions"></a>Omezení

- _routes.jsv_ souboru nemůže být větší než 100 kB.
- _routes.jsv_ souboru podporuje maximálně 50 jedinečných rolí.

Obecná omezení a omezení najdete v článku věnovaném [kvótám](quotas.md) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení ověřování a autorizace](authentication-authorization.md)

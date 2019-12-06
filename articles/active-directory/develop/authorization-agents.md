---
title: Autorizační agenti a jejich povolení | Azure
description: Přečtěte si o různých autorizačních agentech, které Microsoft Authentication Library (MSAL) umožňuje vaší aplikaci pro Android používat a jak je povolit.
services: active-directory
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a0e93c87c50fbc63cddad349ec9b5fbf45d91f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74843899"
---
# <a name="authorization-agents-android"></a>Autorizační agenti (Android)

Tento článek popisuje různé autorizační agenty, které Microsoft Authentication Library (MSAL) umožňuje vaší aplikaci používat a jak je povolit.

Volba konkrétní strategie pro autorizační agenty je volitelná a představuje další funkční aplikace, které můžete přizpůsobit. Většina aplikací bude používat výchozí hodnoty MSAL (informace o různých výchozích nastaveních najdete v tématu [vysvětlení konfiguračního souboru Android MSAL](msal-configuration.md) ).

MSAL podporuje autorizaci pomocí `WebView`nebo prohlížeč systému.  Následující obrázek ukazuje, jak vypadá pomocí `WebView`, nebo v prohlížeči systému pomocí CustomTabs nebo bez CustomTabs:

![Příklady přihlášení MSAL](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Důsledky jednotného přihlašování

Ve výchozím nastavení aplikace integrované s MSAL používají k autorizaci vlastní karty systémového prohlížeče. Na rozdíl od webového zobrazení vlastní karty sdílí JAR se souborem cookie s výchozím systémovým prohlížečem, který umožňuje menší počet přihlášení pomocí webových nebo jiných nativních aplikací, které jsou integrované s vlastními kartami.

Pokud aplikace používá strategii `WebView` bez integrace Microsoft Authenticator nebo Portál společnosti podporu do své aplikace, uživatelé nebudou mít k dispozici jednotné přihlašování (SSO) v rámci zařízení nebo mezi nativními aplikacemi a webovými aplikacemi.

Pokud aplikace používá MSAL s podporou Microsoft Authenticator nebo Portál společnosti, mohou uživatelé mít v aplikacích prostředí jednotného přihlašování, pokud má uživatel aktivní přihlášení pomocí jedné z aplikací.

## <a name="webview"></a>WebView

Pokud chcete použít webzobrazení v aplikaci, vložte do kódu JSON konfigurace aplikace následující řádek, který se předává do MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Při použití `WebView`v aplikaci se uživatel přihlásí přímo k aplikaci. Tokeny se uchovávají v izolovaném prostoru aplikace a nejsou dostupné mimo JAR souborů cookie aplikace. V důsledku toho uživatel nemůže mít v aplikacích možnosti jednotného přihlašování, pokud se aplikace neintegrují s ověřovatelem nebo Portál společnosti.

`WebView` ale nabízí možnost přizpůsobit si vzhled a chování uživatelského rozhraní pro přihlašování. Další informace o tom, jak toto přizpůsobení provést, najdete v tématu věnovaném [zobrazením v Androidu](https://developer.android.com/reference/android/webkit/WebView) .

## <a name="default-browser-plus-custom-tabs"></a>Výchozí prohlížeč a vlastní karty

Ve výchozím nastavení MSAL používá strategii prohlížeče a [vlastních karet](https://developer.chrome.com/multidevice/android/customtabs) . Tuto strategii můžete explicitně označit, aby nedocházelo ke změnám v budoucích verzích `DEFAULT` pomocí následující konfigurace JSON v souboru vlastního konfigurace:

```json
"authorization_user_agent" : "BROWSER"
```

Tento přístup slouží k zajištění jednotného přihlašování přes prohlížeč zařízení. MSAL používá sdílený soubor cookie, který umožňuje ostatním nativním aplikacím nebo webovým aplikacím dosáhnout jednotného přihlašování na zařízení pomocí souborů cookie trvalé relace nastavených pomocí MSAL.

## <a name="browser-selection-heuristic"></a>Heuristika výběru prohlížeče

Vzhledem k tomu, že MSAL není možné zadat přesný balíček prohlížeče, který se má použít pro každé široké spektrum telefonů s Androidem, MSAL implementuje heuristickou volbu pro výběr prohlížeče, která se pokusí poskytnout nejlepší jednotné přihlašování mezi zařízeními.

MSAL načte úplný seznam prohlížečů nainstalovaných na zařízení a vybere prohlížeč, který se má použít. Seznam je v pořadí vráceném správcem balíčků, který nepřímo odráží předvolby uživatele. Například výchozí prohlížeč, pokud je nastaveno, je první záznam v seznamu. _První_ prohlížeč v seznamu se vybere bez ohledu na to, jestli podporuje vlastní karty. Pokud prohlížeč podporuje vlastní karty, MSAL spustí vlastní kartu. vlastní karty mají vzhled a chování blíž k `WebView` v aplikaci a umožňují základní přizpůsobení uživatelského rozhraní. Další informace najdete [v tématu vlastní karty v Androidu](https://developer.chrome.com/multidevice/android/customtabs) .

Pokud na zařízení nejsou žádné balíčky prohlížeče, MSAL používá `WebView`v aplikaci.

Pořadí prohlížečů v seznamu prohlížeče je určeno operačním systémem. Je v pořadí od nejvíce do nejnižší. Pokud se výchozí nastavení zařízení nezmění, měl by se pro každé přihlášení spustit stejný prohlížeč, aby se zajistilo prostředí jednotného přihlašování.

> [!NOTE]
> MSAL už vždy preferuje Chrome, pokud je jako výchozí nastavený jiný prohlížeč. Například na zařízení, které má předinstalované Chrome i jiný prohlížeč, MSAL použije prohlížeč, který uživatel nastavil jako výchozí.

### <a name="tested-browsers"></a>Testované prohlížeče

Následující prohlížeče byly testovány, aby se zjistilo, zda správně přesměrují na `"redirect_uri"` zadané v konfiguračním souboru:

| | Vestavěný prohlížeč | Chrome | Opera  | Microsoft Edge | Prohlížeč UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (rozhraní API 17) | Dána | Dána |nelze použít |nelze použít |nelze použít |nelze použít |
| Samsung S7 (rozhraní API 25) | dána | Dána | Dána | Dána | fail |Dána |
| Huawei (rozhraní API 26) |Pass * * | Dána | fail | Dána | Dána |Dána |
| Vivo (API 26) |Dána|Dána|Dána|Dána|Dána|fail|
| Pixel 2 (rozhraní API 26) |Dána | Dána | Dána | Dána | fail |Dána |
| OPPO | Dána | nelze použít * * * |nelze použít  |nelze použít |nelze použít | nelze použít|
| OnePlus (rozhraní API 25) |Dána | Dána | Dána | Dána | fail |Dána |
| Nexus (rozhraní API 28) |Dána | Dána | Dána | Dána | fail |Dána |
|MI | Dána | Dána | Dána | Dána | fail |Dána |

\* Integrovaný Prohlížeč Samsung je Samsung Internet.  
\* * Vestavěný prohlížeč Huawei je Huawei prohlížeč.  
Výchozí prohlížeč nejde změnit v nastavení zařízení OPPO.

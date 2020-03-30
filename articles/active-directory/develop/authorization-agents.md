---
title: Povolovací agenti a jak jim to umožnit | Azure
description: Informace o různých autorizačních agentech, které knihovna Microsoft Authentication Library (MSAL) umožňuje aplikaci pro Android používat, a jak je povolit.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: marsma
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: 4f1b3fc5b60069cfa47d437e4341ded141204418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085328"
---
# <a name="authorization-agents-android"></a>Autorizační agenti (Android)

Tento článek popisuje různé autorizační agenty, které knihovna Ověřování Společnosti Microsoft (MSAL) umožňuje aplikaci používat, a jak je povolit.

Výběr konkrétní strategie pro autorizační agenty je volitelný a představuje další funkce, které lze přizpůsobit aplikacím. Většina aplikací bude používat výchozí hodnoty MSAL (viz [Principy konfiguračního souboru Android MSAL](msal-configuration.md) zobrazíte různé výchozí hodnoty).

MSAL podporuje autorizaci pomocí aplikace nebo systémového `WebView`prohlížeče.  Obrázek níže `WebView`ukazuje, jak to vypadá pomocí , nebo systémový prohlížeč s CustomTabs nebo bez CustomTabs:

![Příklady přihlášení msal](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Důsledky jednotného přihlášení

Ve výchozím nastavení aplikace integrované s MSAL používat vlastní karty prohlížeče systému autorizovat. Na rozdíl od webových zobrazení sdílejí vlastní karty nádobu se soubory cookie s výchozím systémovým prohlížečem, který umožňuje méně přihlášení s webovými nebo jinými nativními aplikacemi, které se integrovaly s vlastními kartami.

Pokud aplikace používá `WebView` strategii bez integrace podpory Microsoft Authenticator nebo Portálspolečnosti do své aplikace, uživatelé nebudou mít možnosti jednotného přihlašování (SSO) napříč zařízením nebo mezi nativními aplikacemi a webovými aplikacemi.

Pokud aplikace používá MSAL s Microsoft Authenticator nebo portál společnosti podpory, pak uživatelé mohou mít prostředí s přihlašování napříč aplikacemi, pokud má uživatel aktivní přihlášení s jednou z aplikací.

## <a name="webview"></a>WebView

Chcete-li použít webové zobrazení v aplikaci, vložte do konfigurace aplikace JSON, která je předána službě MSAL, následující řádek:

```json
"authorization_user_agent" : "WEBVIEW"
```

Při používání aplikace `WebView`se uživatel přihlásí přímo k aplikaci. Tokeny jsou uloženy uvnitř izolovaného prostoru aplikace a nejsou k dispozici mimo aplikaci cookie jar. V důsledku toho uživatel nemůže mít prostředí s přihlašování napříč aplikacemi, pokud se aplikace neintegrují s ověřovacím nebo firemním portálem.

Poskytuje `WebView` však možnost přizpůsobit vzhled a chování pro přihlašovací ui. Další informace o tom, jak provést toto přizpůsobení, najdete v [tématu Android WebViews.](https://developer.android.com/reference/android/webkit/WebView)

## <a name="default-browser-plus-custom-tabs"></a>Výchozí prohlížeč a vlastní karty

Ve výchozím nastavení msal používá prohlížeč a [vlastní karty](https://developer.chrome.com/multidevice/android/customtabs) strategie. Tuto strategii můžete explicitně označit, abyste `DEFAULT` zabránili změnám v budoucích verzích aplikace pomocí následující konfigurace JSON ve vlastním konfiguračním souboru:

```json
"authorization_user_agent" : "BROWSER"
```

Pomocí tohoto přístupu můžete poskytovat prostředí s přistupovat prostřednictvím prohlížeče zařízení. MSAL používá sdílenou nádobu cookie, která umožňuje jiným nativním aplikacím nebo webovým aplikacím dosáhnout přidělování služeb při spojené stavení mase v zařízení pomocí souboru cookie trvalé relace nastaveného službou MSAL.

## <a name="browser-selection-heuristic"></a>Heuristický výběr prohlížeče

Vzhledem k tomu, že je nemožné pro MSAL určit přesný balíček prohlížeče pro použití na každé z široké škály telefonů Android, MSAL implementuje výběr prohlížeče heuristické, který se snaží poskytnout nejlepší cross-device SSO.

MSAL načte úplný seznam prohlížečů nainstalovaných v zařízení a vybere prohlížeč, který má být používán. Seznam je v pořadí vrácené správcem balíčků, což nepřímo odráží předvolby uživatele. Například výchozí prohlížeč, pokud je nastaven, je první položkou v seznamu. _První_ prohlížeč v seznamu bude vybrán bez ohledu na to, zda podporuje vlastní karty. Pokud prohlížeč podporuje vlastní karty, msal spustí vlastní kartu. Vlastní karty mají vzhled a `WebView` chování blíže k in-app a umožňují základní přizpůsobení uživatelského rozhraní. Další informace najdete [v tématu Vlastní karty v Systému Android.](https://developer.chrome.com/multidevice/android/customtabs)

Pokud v zařízení nejsou žádné balíčky prohlížeče, používá `WebView`msal v aplikaci .

Pořadí prohlížečů v seznamu prohlížečů je určeno operačním systémem. Je to v pořádku od většiny přednostní nejméně. Pokud se výchozí nastavení zařízení nezmění, měl by být pro každé přihlášení spuštěn stejný prohlížeč, aby bylo zajištěno jednotné přihlašování.

> [!NOTE]
> MSAL již vždy preferuje Chrome, pokud je jiný prohlížeč nastaven jako výchozí. Například na zařízení, které má chrome i jiný předinstalovaný prohlížeč, použije msal prohlížeč, který uživatel nastavil jako výchozí.

### <a name="tested-browsers"></a>Testované prohlížeče

Následující prohlížeče byly testovány, aby zjistili, zda `"redirect_uri"` správně přesměrovat na zadaný v konfiguračním souboru:

| | Vestavěný prohlížeč | Chrome | Opera  | Microsoft Edge | Prohlížeč UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | Předat | Předat |nepoužije se |nepoužije se |nepoužije se |nepoužije se |
| Samsung S7 (API 25) | průkaz* | Předat | Předat | Předat | Selhání |Předat |
| Společnost Huawei (API 26) |průchod** | Předat | Selhání | Předat | Předat |Předat |
| Vivo (API 26) |Předat|Předat|Předat|Předat|Předat|Selhání|
| Pixel 2 (API 26) |Předat | Předat | Předat | Předat | Selhání |Předat |
| Oppo | Předat | nepoužije se*** |nepoužije se  |nepoužije se |nepoužije se | nepoužije se|
| OnePlus (API 25) |Předat | Předat | Předat | Předat | Selhání |Předat |
| Nexus (ROZHRANÍ API 28) |Předat | Předat | Předat | Předat | Selhání |Předat |
|MI | Předat | Předat | Předat | Předat | Selhání |Předat |

*Vestavěný montovna Samsung je Samsung Internet.  
**Vestavěný prohlížeč Huawei je prohlížeč Huawei.  
Výchozí prohlížeč nelze změnit v nastavení zařízení Oppo.

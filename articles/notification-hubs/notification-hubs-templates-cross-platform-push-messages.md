---
title: Šablony
description: Toto téma vysvětluje šablon pro službu Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 02473eb5649c7d201b6a54fd57faea997c1a21cc
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450230"
---
# <a name="templates"></a>Šablony

Šablony umožňují klientské aplikaci určit přesný formát oznámení, která chce přijmout. Pomocí šablon, můžete aplikaci realizovat několik různých výhod, včetně následujících:

- Back-endu. pro více platforem
- Přizpůsobená oznámení
- Nezávislosti na verzi klienta
- Snadné lokalizace

Tato část obsahuje dva podrobné příklady použití šablony k odesílání oznámení pro více platforem cílí na všechna zařízení napříč platformami a přizpůsobení všesměrového vysílání oznámení jednotlivým zařízením.

## <a name="using-templates-cross-platform"></a>Pomocí šablony pro různé platformy

K odeslání pro každé upozornění, které se mají poslat konkrétní datová část službám oznamování platformy (WNS, APNS) je standardní způsob, jak posílat nabízená oznámení. Například pokud chcete odeslat oznámení APNS, je datová část objektu JSON v následujícím formátu:

```json
{"aps": {"alert" : "Hello!" }}
```

Odeslat zprávu podobné informační zprávy v aplikaci Windows Store, datová část XML je následující:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Podobně jako datové části můžete vytvořit pro MPNS (Windows Phone) a FCM platforem (Android).

Tento požadavek vynutí back-endu aplikace k vytvoření různých datových částí pro každou platformu a efektivně je zodpovědná za součástí prezentační vrstvy aplikace back-endu. Některé aspekty zahrnují lokalizace a grafické rozložení (hlavně pro aplikace Windows Store, které zahrnují oznámení pro různé typy dlaždic).

Funkce šablony služby Notification Hubs umožňuje klientská aplikace vytvořit zvláštní registrace, volá se registrace šablon, které zahrnují kromě sad značek, šablony. Funkce šablony služby Notification Hubs umožňuje klientskou aplikaci, ať už pracujete s registrací zařízení (upřednostňováno) nebo přidružení zařízení k šablony. S ohledem předchozí příklady datovou část, informace pouze nezávislá na platformě je skutečná zpráva s výstrahou (Hello!). Šablona je sada instrukcí pro Centrum oznámení o tom, jak formátování zprávy nezávislá na platformě pro registraci této konkrétní klientské aplikace. V předchozím příkladu je nezávislá na platformě zpráva jedné vlastnosti: `message = Hello!`.

Na následujícím obrázku je znázorněn proces:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Šablony pro registraci aplikace iOS klienta vypadá takto:

```json
{"aps": {"alert": "$(message)"}}
```

Odpovídající šablonu pro klientskou aplikaci pro Windows Store je:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Všimněte si, že skutečné zpráva nahradí výrazu $(zprávy). Tento výraz instruuje centru oznámení pokaždé, když se odešle zprávu do této konkrétní registraci, k vytvoření zprávy, která následuje ji a přepínače ve společné hodnoty.

Pokud pracujete s modelem instalace, obsahuje klíč "šablony" instalace JSON několik šablon. Pokud pracujete s modelem zápisu, klientská aplikace můžete vytvořit více registrací Chcete-li používat více šablon; například šablony pro šablonu pro aktualizace dlaždice a oznámení. Klientské aplikace můžete také kombinovat nativní registrace (s žádná šablona registrace) a šablona registrace.

Centrum oznámení odešle jedno oznámení ke každé šabloně bez zohlednění, jestli patří do stejné klientské aplikace. Toto chování je možné přeložit nezávislá na platformě oznámení do další oznámení. Například stejné nezávislá na platformě zprávy do centra oznámení lze bezproblémově přeložit v informační výstrahy a aktualizace dlaždice, aniž by bylo nutné upozornit back-endu. Některé platformy (například iOS) může sbalit několik oznámení do stejného zařízení, pokud se odesílají v krátké době.

## <a name="using-templates-for-personalization"></a>Pomocí šablon pro přizpůsobení

Další výhodou použití šablony je schopnost provádět přizpůsobení za registraci oznámení pomocí Notification Hubs. Představte si třeba o počasí aplikaci, která se zobrazí dlaždice s počasí v konkrétním umístění. Může uživatel vybrat mezi stupních Celsia nebo Fahrenheita a jedné nebo pětidenního předpovědi. Pomocí šablon, každou instalaci klienta aplikace si můžou zaregistrovat formátu potřebném (1 den stupně Celsia, 1 den Fahrenheita, 5 dní ve stupních Celsia, 5 dní Fahrenheita kurzy), a back-endu odeslat zprávu, která obsahuje všechny informace potřebné k vyplnění těchto šablon (například pětidenního Prognózování s stupních Celsia a Fahrenheita).

Šablona pro předpověď jednodenní s ve stupních Celsia teploty vypadá takto:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Zpráva odeslaná do centra oznámení obsahuje následující vlastnosti:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Pomocí tohoto modelu back-endu pouze odešle do jedné zprávy bez nutnosti ukládat konkrétní nastavení možnosti pro uživatele aplikace. Následující obrázek ukazuje tento scénář:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Postup registrace šablon

Zaregistrovat se šablonami pomocí Instalační model (upřednostňováno) nebo modelu zápisu, naleznete v tématu [Správa registrací](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Výraz jazyka šablony

Šablony jsou omezené na formát dokumentu XML nebo JSON. Navíc můžete pouze umístit výrazy na konkrétní místech; například uzel atributy nebo hodnoty XML řetězec hodnoty vlastností pro formát JSON.

Jazyk povolené v šablonách naleznete v následující tabulce:

| Výraz       | Popis |
| ---------------- | --- |
| $(prop)          | Odkaz na vlastnost události se zadaným názvem. Názvy vlastností nerozlišují malá a velká písmena. Tento výraz se přeloží do hodnoty vlastnosti text nebo na prázdný řetězec je-li vlastnost není k dispozici. |
| $(prop, n)       | Jak je uvedeno výše ale text není explicitně oříznut n znaků, například $(title, 20) klipy obsah vlastnost názvu 20 znaků. |
| . (prop, n)       | Jak je uvedeno výše ale text je přidán se třemi tečkami, jako je oříznut. Celková velikost řetězce zkrácený a přípony nepřekročí n znaků. . (název, 20) se vstupní vlastnost "Toto je název řádku" výsledků v **Toto je název...** |
| %(Prop)          | Podobně jako $(name) s tím rozdílem, že je výstup kódovaný identifikátor URI. |
| #(prop)          | Používá se v šablonách JSON (například pro iOS a Android šablony).<br><br>Tato funkce funguje stejně jako $(prop) dříve zadán, s výjimkou případů, když se používá v šablonách JSON (například Apple šablony). V tomto případě, pokud není tato funkce obklopený "{","}" (například myJsonProperty: "#(název)"), a je vyhodnocen jako číslo ve formátu jazyka Javascript, například regexp: (0&#124;(&#91;1 až 9&#93;&#91;0-9&#93;*)) (\.&#91;0-9&#93;+)? ((elektronická&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, pak ve výstupu JSON je číslo.<br><br>Například "oznámení" BADGE ":"#(název)"se stane"badge": 40 (a ne 40). |
| 'text' nebo "text" | Literál. Literály obsahují libovolný text v jednoduchých nebo dvojitých uvozovkách. |
| expr1 + expr2    | Operátor zřetězení propojení dvou výrazů do jednoho řetězce. |

Výraz může být některé z předchozích formuláře.

Při použití zřetězení, musí být v celý výraz s `{}`. Například, `{$(prop) + ‘ - ’ + $(prop2)}`.

Například následující šablona není platnou šablonu XML:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Jak jsme vysvětlili výše, při použití zřetězení, musí být zabaleny výrazy do složených závorek. Příklad:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

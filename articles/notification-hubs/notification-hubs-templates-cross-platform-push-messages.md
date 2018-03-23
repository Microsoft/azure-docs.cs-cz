---
title: Šablony
description: Toto téma vysvětluje šablon pro Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: ''
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 1ca24a4bf08ecdbe1c1e47a931613144309a04a9
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="templates"></a>Šablony
## <a name="overview"></a>Přehled
Šablony povolte klientská aplikace k určení přesném formátu, které chce dostávat oznámení. Pomocí šablon, aplikace můžou realizovat několik různých výhod, včetně následujících:

* Back-end bez ohledu na platformy
* Přizpůsobené oznámení
* Nezávislost verze klienta
* Snadno lokalizace

Tato část obsahuje dva podrobné příklady, jak používat šablony k odesílání oznámení bez ohledu na platformu cílení na všechna zařízení napříč platformami a přizpůsobení všesměrového vysílání oznámení pro každé zařízení.

## <a name="using-templates-cross-platform"></a>Pomocí šablony a platformy
Standardní způsob k odesílání nabízených oznámení je odeslání pro jednotlivá oznámení, která má být odeslán konkrétní datové části pro služby oznámení platformy (WNS, APNS). Pokud chcete odeslat výstrahu do služby APN, například datové části je objekt Json v následujícím formátu:

    {"aps": {"alert" : "Hello!" }}

Odeslat zprávu podobné informační zprávy v aplikaci pro Windows Store, datovou část XML je následující:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">Hello!</text>
        </binding>
      </visual>
    </toast>

Podobně jako datové části můžete vytvořit pro platformy (Android) GCM a MPNS (Windows Phone).

Tento požadavek vynutí back-end aplikace k vytvoření různých datových částí pro každou platformu a efektivně zajišťuje back-end zodpovědná za součástí prezentační vrstvy aplikace. Některé aspekty patří lokalizace a grafické rozložení (hlavně u aplikací pro Windows Store, které obsahují oznámení pro různé typy dlaždic).

Funkce šablony Notification Hubs umožňuje klientskou aplikaci vytvořit speciální registrace, nazývá šablona registrace, které obsahují, kromě sadu značky, šablony. Funkce šablony Notification Hubs umožňuje klientskou aplikaci pro přidružení zařízení se šablonami, zda pracujete s instalací (doporučeno) nebo registrací. Informace pouze nezávislé na platformě zadané v předchozích příkladech datové části, je skutečný zpráva s výstrahou (Hello!). Šablona je sada pokynů pro Centrum oznámení o tom, jak formátování zprávy nezávislé na platformě pro registraci tohoto konkrétního klienta aplikace. V předchozím příkladu nezávislé zpráva platformy je jedinou vlastností: **zpráva = Hello!**.

Následující obrázek znázorňuje proces výše:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Šablona pro registraci aplikace iOS klienta vypadá takto:

    {"aps": {"alert": "$(message)"}}

Odpovídající šablonu pro klientské aplikace Windows Store je:

    <toast>
        <visual>
            <binding template=\"ToastText01\">
                <text id=\"1\">$(message)</text>
            </binding>
        </visual>
    </toast>

Všimněte si, že skutečné zpráva nahradí výraz $(zprávy). Tento výraz dá pokyn centru oznámení pokaždé, když ji odešle zprávu do této konkrétní registraci, k vytvoření zprávu, která odpovídá jeho a přepínačů v hodnotě běžné.

Pokud pracujete s modelem instalace, instalace "šablony" klíč obsahuje JSON více šablon. Pokud pracujete s modelem registrace, klientská aplikace můžete vytvořit více registrace Chcete-li použít několik šablon; například šablonu pro oznámení a šablonu pro dlaždici aktualizací. Klientské aplikace můžete také kombinovat nativní registrace (registrace s žádnou šablonu) a šablony registrace.

Centra oznámení odešle jedno oznámení ke každé šabloně bez ohledu, jestli patří do stejné klientské aplikace. Toto chování lze přeložit nezávislé na platformě oznámení do další oznámení. Například stejnou platformu nezávislé zprávu do centra oznámení můžete bezproblémově přeložit v informační výstrahy a aktualizace dlaždice, bez nutnosti back-end zajímat ho. Všimněte si, že některé platformy (například iOS) může sbalit několik oznámení do stejného zařízení, pokud jsou odesílány v krátké době.

## <a name="using-templates-for-personalization"></a>Pomocí šablon pro přizpůsobení
Další výhodou použití šablony je schopnost provádět přizpůsobení za registraci oznámení pomocí centra oznámení. Představte si třeba počasí aplikaci, která zobrazuje dlaždici počasí na určité místo. Uživatel může zvolit c nebo Fahrenheita stupňů a jednoho nebo pětidenního prognózy. Pomocí šablon, každou instalaci klienta aplikace můžete zaregistrovat pro formát požadované (1 den Celsius, 1 den Fahrenheita, 5 dní c, 5 dní Fahrenheita kurzy), a back-end odeslat zprávu, která obsahuje všechny informace potřebné k vyplnění těchto šablon (například pět dní prognózy s c a Fahrenheita stupních).

Šablona pro jeden den prognózy s c teploty vypadá takto:

    <tile>
      <visual>
        <binding template="TileWideSmallImageAndText04">
          <image id="1" src="$(day1_image)" alt="alt text"/>
          <text id="1">Seattle, WA</text>
          <text id="2">$(day1_tempC)</text>
        </binding>  
      </visual>
    </tile>

Zpráva odeslaná do centra oznámení obsahuje následující vlastnosti:

<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>

Pomocí tohoto vzoru back-end pouze odešle do jedné zprávy bez nutnosti ukládat konkrétní nastavení možnosti pro uživatele aplikace. Následující obrázek znázorňuje tento scénář:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Postup registrace šablony
Zaregistrovat se šablonami pomocí modelu instalace (doporučeno) nebo model registraci, najdete v tématu [registrace správu](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Výraz jazyka šablony
Šablony jsou omezeny na formáty XML nebo JSON dokumentů. Navíc můžete pouze umístit výrazy konkrétní místech; například atributy uzlu nebo hodnoty pro formát XML řetězec hodnoty vlastností pro formát JSON.

V následující tabulce jsou povolené v šablonách jazyk:

| Výraz | Popis |
| --- | --- |
| $(prop) |Odkaz na vlastnost události se zadaným názvem. Názvy vlastností nerozlišují malá a velká písmena. Tento výraz přeloží do vlastnosti textové hodnoty nebo na prázdný řetězec, pokud vlastnost není k dispozici. |
| $(prop, n) |Jak je uvedeno výše zatímco text elementu je explicitně oříznut n znaků, například $(název, 20) klipy obsah název vlastnosti v 20 znaků. |
| . (prop, n) |Jak je uvedeno výše ale text je na konci se třemi tečkami jako je oříznut. Celková velikost oříznutí řetězec a příponu nepřesahuje n znaků. . (title, 20) s vstupní vlastností "Toto je název řádku" výsledků v **Toto je název...** |
| %(Prop) |Podobně jako u $(name) s tím rozdílem, že výstupem je kódovaný identifikátor URI. |
| #(prop) |Používá se v šablony JSON (například pro iOS a Android šablony).<br><br>Tato funkce funguje stejně jako $(prop) dříve zadán, s výjimkou při používány šablony JSON (například Apple šablony). V tomto případě, pokud není tato funkce obklopená "{','}" (například myJsonProperty: '#(název)'), a se vyhodnocuje na číslo ve formátu Javascript, například regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*)) (\. &#91;0-9&#93;+)? ((e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, pak výstup JSON je číslo.<br><br>Například "oznámení" BADGE ":"#(název), se změní na "oznámení": 40 (a ne 40). |
| 'text' nebo "text" |Literál. Literály obsahovat libovolný text v jednoduchých nebo dvojitých uvozovek. |
| Výraz1 + Výraz2 |Operátor řetězení propojení dvou výrazů do jednoho řetězce. |

Výraz může být libovolná z předchozí formuláře.

Pokud používáte zřetězení, musí být uzavřena celý výraz s {}. Například {$(prop) + '-' + $(prop2)}. |

Například následující není platné šablony XML:

    <tile>
      <visual>
        <binding $(property)>
          <text id="1">Seattle, WA</text>
        </binding>  
      </visual>
    </tile>


Jak je vysvětleno výše při použití zřetězení, výrazy musí být uzavřen do složených závorek. Příklad:

    <tile>
      <visual>
        <binding template="ToastText01">
          <text id="1">{'Hi, ' + $(name)}</text>
        </binding>  
      </visual>
    </tile>


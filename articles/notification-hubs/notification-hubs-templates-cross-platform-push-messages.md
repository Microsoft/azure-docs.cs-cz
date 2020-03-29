---
title: Šablony center Azure Notification Hubs
description: Přečtěte si o používání šablon pro Centra oznámení Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 7d88f57fe92b9da62cc9f90d64bdec4c27642fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263740"
---
# <a name="templates"></a>Šablony

Šablony umožňují klientské aplikaci určit přesný formát oznámení, která chce přijímat. Pomocí šablon může aplikace využívat několik různých výhod, včetně následujících:

- Back-end agnostik platformy
- Personalizovaná oznámení
- Nezávislost verze klienta
- Snadná lokalizace

Tato část obsahuje dva podrobné příklady použití šablon k odesílání oznámení nezávislána platformě, která cílí na všechna vaše zařízení napříč platformami, a k přizpůsobení oznámení o vysílání pro každé zařízení.

## <a name="using-templates-cross-platform"></a>Používání šablon napříč platformami

Standardní způsob odesílání nabízených oznámení je odeslat pro každé oznámení, které má být odesláno, konkrétní datovou část službě oznámení platformy (WNS, APNS). Chcete-li například odeslat výstrahu do apns, datová část je objekt JSON následujícího formuláře:

```json
{"aps": {"alert" : "Hello!" }}
```

Chcete-li odeslat podobnou informační zprávu v aplikaci pro Windows Store, je datová část XML následující:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Můžete vytvořit podobné datové části pro platformy MPNS (Windows Phone) a FCM (Android).

Tento požadavek vynutí back-end aplikace k výrobě různých datových částí pro každou platformu a efektivně umožňuje back-end odpovědný za část prezentační vrstvy aplikace. Mezi některé obavy patří lokalizace a grafická rozložení (zejména pro aplikace pro Windows Store, které obsahují oznámení pro různé typy dlaždic).

Funkce šablony Centra oznámení umožňuje klientské aplikaci vytvářet speciální registrace nazývané registrace šablon, které kromě sady značek zahrnují také šablonu. Funkce šablony Centra oznámení umožňuje klientské aplikaci přidružit zařízení k šablonám bez ohledu na to, zda pracujete s instalacemi (upřednostňovanými) nebo registracemi. Vzhledem k předchozím příkladům datové části je pouze informace nezávislé na platformě skutečné výstražné zprávy (Hello!). Šablona je sada pokynů pro Centrum oznámení o tom, jak formátovat zprávu nezávislou na platformě pro registraci této konkrétní klientské aplikace. V předchozím příkladu je zpráva nezávislá na `message = Hello!`platformě jedinou vlastností: .

Následující obrázek ilustruje proces:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Šablona pro registraci klientské aplikace pro iOS je následující:

```json
{"aps": {"alert": "$(message)"}}
```

Odpovídající šablona pro klientskou aplikaci pro Windows Store je:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Všimněte si, že skutečná zpráva je nahrazen výraz $(message). Tento výraz instruuje Centrum oznámení, kdykoli odešle zprávu této konkrétní registraci, vytvořit zprávu, která následuje a přepne ve společné hodnotě.

Pokud pracujete s instalačním modelem, instalační klíč "šablony" obsahuje json více šablon. Pokud pracujete s modelem registrace, klientská aplikace může vytvořit více registrací, aby bylo možné použít více šablon; například šablona pro výstražné zprávy a šablona pro aktualizace dlaždic. Klientské aplikace mohou také kombinovat nativní registrace (registrace bez šablony) a registrace šablon.

Centrum oznámení odešle jedno oznámení pro každou šablonu bez ohledu na to, zda patří do stejné klientské aplikace. Toto chování lze přeložit oznámení nezávislé na platformě do další oznámení. Například stejná zpráva nezávislá na platformě do centra oznámení může být bezproblémově přeložena do informační zprávy a aktualizace dlaždic, aniž by bylo nutné, aby si to back-end uvědomoval. Některé platformy (například iOS) může sbalit více oznámení do stejného zařízení, pokud jsou odeslány v krátkém časovém období.

## <a name="using-templates-for-personalization"></a>Použití šablon pro přizpůsobení

Další výhodou použití šablon je možnost používat centra oznámení k provádění individuálního přizpůsobení oznámení pro registraci. Zvažte například aplikaci pro počasí, která zobrazuje dlaždici s povětrnostními podmínkami v určitém místě. Uživatel si může vybrat mezi stupněm Celsia nebo Fahrenheita a jednodenní nebo pětidenní předpovědí. Pomocí šablon se každá instalace klientské aplikace může zaregistrovat do požadovaného formátu (1 den Celsia, 1 den Fahrenheita, 5 dní Celsia, 5 dní Fahrenheita) a mít back-end odeslat jednu zprávu, která obsahuje všechny informace potřebné k vyplnění těchto šablon (například pětidenní předpověď s stupňů Celsia a Fahrenheita).

Šablona pro jednodenní předpověď s teplotami Celsia je následující:

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

Zpráva odeslaná do centra oznámení obsahuje všechny následující vlastnosti:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Pomocí tohoto vzoru back-end odesílá pouze jednu zprávu bez nutnosti ukládat konkrétní možnosti přizpůsobení pro uživatele aplikace. Následující obrázek ilustruje tento scénář:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Jak registrovat šablony

Pokud se chcete zaregistrovat pomocí šablon pomocí modelu instalace (upřednostňovaný) nebo modelu registrace, přečtěte si informace [o správě registrace](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Jazyk výrazu šablony

Šablony jsou omezeny na formáty dokumentů XML nebo JSON. Výrazy lze také umísťovat pouze na určitá místa; například atributy uzlu nebo hodnoty pro XML, hodnoty vlastností řetězce pro JSON.

V následující tabulce je uveden jazyk povolený v šablonách:

| Expression       | Popis |
| ---------------- | --- |
| $(rekvizita)          | Odkaz na vlastnost události s daným názvem. Názvy vlastností nejsou rozlišována malá a velká písmena. Tento výraz se překládá do textové hodnoty vlastnosti nebo do prázdného řetězce, pokud vlastnost není k dispozici. |
| $(rekvizita, n)       | Jak je uvedeno výše, ale text je explicitně oříznut na n znaků, například $(title, 20) klipy obsah title vlastnost na 20 znaků. |
| . (rekvizita, n)       | Jak je uvedeno výše, ale text je připnut třemi tečkami, když je oříznut. Celková velikost oříznutého řetězce a přípony nepřesahuje n znaků. . (title, 20) se vstupní vlastností "Toto je titulní řádek" výsledky v **Toto je název ...** |
| %(vrtule)          | Podobně jako $(name) s tím rozdílem, že výstup je kódován uri. |
| #(rekvizita)          | Používá se v šablonách JSON (například pro šablony iOS a Android).<br><br>Tato funkce funguje přesně stejně jako $(prop) dříve zadané, s výjimkou při použití v šablonách JSON (například šablony Apple). V tomto případě, pokud tato funkce není obklopena "{','}" (například 'myJsonProperty' : '#(name)'), a vyhodnotí se na číslo ve formátu Javascript, například regexp: (0&#124; (&#91;1-9&#93;&#91;0-9&#93;*))(\.&#91;0-9&#93;+)? ((e&#124;E)(+&#124;-)?&#91;0-9&#93;+)?, pak výstup JSON je číslo.<br><br>Například 'odznak: '#(jméno)' se změní 'odznak' : 40 (a ne '40'). |
| "text" nebo "text" | Doslovný. Literály obsahují libovolný text uzavřený v jednoduchých nebo dvojitých uvozovkách. |
| expr1 + expr2    | Operátor zřetězení spojující dva výrazy do jednoho řetězce. |

Výrazy mohou být libovolné z předchozích formulářů.

Při použití zřetězení musí být celý `{}`výraz obklopen . Například, `{$(prop) + ‘ - ’ + $(prop2)}`.

Například následující šablona není platnou šablonou XML:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Jak bylo vysvětleno dříve, při použití zřetězení musí být výrazy zabaleny do složených závorek. Například:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>Další kroky

[Informace o azure notifikacích centrech](notification-hubs-push-notification-overview.md)
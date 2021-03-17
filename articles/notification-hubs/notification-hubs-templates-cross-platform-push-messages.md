---
title: Šablony Azure Notification Hubs
description: Přečtěte si o používání šablon pro Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: ee42512a468f4ff86ad7ba273d3971fd124779e2
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635638"
---
# <a name="notification-hubs-templates"></a>Šablony Notification Hubs

Šablony umožňují klientské aplikaci zadat přesný formát oznámení, která chce přijmout. Pomocí šablon může aplikace realizovat několik různých výhod, včetně následujících:

- Back-end nezávislá platformy
- Přizpůsobená oznámení
- Nezávislost klienta a verze
- Snadná lokalizace

V této části najdete dva podrobné příklady použití šablon k posílání oznámení nezávislá platforem, která cílí na všechna vaše zařízení napříč platformami, a pro přizpůsobení oznámení všesměrového vysílání na každé zařízení.

## <a name="using-templates-cross-platform"></a>Používání šablon pro různé platformy

Standardní způsob, jak odesílat nabízená oznámení, je odeslat pro každé oznámení, které se pošle, konkrétní datovou část do služby oznámení platformy (WNS, APNS). Například pro odeslání výstrahy službě APNS je datová část objekt JSON v následujícím tvaru:

```json
{"aps": {"alert" : "Hello!" }}
```

Chcete-li odeslat podobnou zprávu informační zprávy v aplikaci pro Windows Store, datová část XML je následující:

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

Tento požadavek vynutí, aby back-end aplikace vytvářely různé datové části pro každou platformu, a efektivně vytvoří back-end zodpovědný za součást prezentační vrstvy aplikace. Mezi důležité aspekty patří lokalizace a grafické rozložení (zejména pro aplikace pro Windows Store, které obsahují oznámení pro různé typy dlaždic).

Funkce šablony Notification Hubs umožňuje klientské aplikaci vytvořit speciální registrace, které se nazývají registrace šablon, mezi které patří také sada značek, šablona. Funkce šablony Notification Hubs umožňuje klientské aplikaci přidružit zařízení k šablonám bez ohledu na to, jestli pracujete s instalacemi (preferované) nebo registrací. V předchozích příkladech datové části se jedná o skutečnou zprávu s upozorněním (**Hello!**), která je závislá na platformě. Šablona je sada instrukcí pro Centrum oznámení o tom, jak naformátovat zprávu nezávislou na platformě pro registraci konkrétní klientské aplikace. V předchozím příkladu je zpráva nezávislá na platformě jedinou vlastností: `message = Hello!` .

Následující obrázek znázorňuje proces:

![Diagram znázorňující proces používání šablon pro různé platformy](./media/notification-hubs-templates/notification-hubs-hello.png)

Šablona pro registraci klientské aplikace pro iOS je následující:

```json
{"aps": {"alert": "$(message)"}}
```

Odpovídající šablona klientské aplikace pro Windows Store je:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Všimněte si, že je pro výraz nahrazena skutečná zpráva `$(message)` . Tento výraz instruuje centrum oznámení, kdykoli pošle zprávu této konkrétní registraci, k vytvoření zprávy, která následuje, a vloží společnou hodnotu.

Pokud pracujete s modelem instalace, klíč instalace "templates" obsahuje JSON více šablon. Pokud pracujete s registračním modelem, může klientská aplikace vytvořit více registrací, aby bylo možné použít více šablon. například šablona pro zprávy s výstrahami a šablonu pro aktualizace dlaždic. Klientské aplikace mohou také kombinovat nativní registrace (registrace bez šablony) a registrace šablon.

Centrum oznámení pošle jedno oznámení pro každou šablonu bez zvážení toho, jestli patří do stejné klientské aplikace. Toto chování se dá použít k překladu oznámení nezávislých na platformě na další oznámení. Například stejná zpráva nezávislá na platformě do centra oznámení může být hladce přeložena informační zprávou a aktualizací dlaždic, aniž by to vyžadovalo, aby se v něm dozvěděla. Některé platformy (například iOS) mohou sbalit více oznámení na stejné zařízení, pokud jsou odesílány v krátké době.

## <a name="using-templates-for-personalization"></a>Použití šablon pro přizpůsobení

Další výhodou pro používání šablon je schopnost použít Notification Hubs k provedení individuálního nastavení oznámení v rámci registrace. Představte si třeba aplikaci počasí, která zobrazuje dlaždici s povětrnostními podmínkami v určitém umístění. Uživatel si může vybrat mezi stupních Celsia nebo Fahrenheita a jednou nebo 5 dny. Pomocí šablon se může každá instalace klientské aplikace zaregistrovat pro požadovaný formát (1 den Celsia, 1 den Fahrenheita, 5 dnů Celsia, 5 dní Fahrenheita) a mít back-end jednu zprávu, která obsahuje všechny informace potřebné k vyplnění těchto šablon (například předpověď na pět dní se stupněm Celsia a Fahrenheita).

Šablona pro jeden den prognózy s teplotou Celsia je následující:

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

| day1_image | day2_image | day3_image | day4_image | day5_image |
|------------|------------|------------|------------|------------|
| day1_tempC | day2_tempC | day3_tempC | day4_tempC | day5_tempC |
| day1_tempF | day2_tempF | day3_tempF | day4_tempF | day5_tempF |

Při použití tohoto vzoru back-end pošle jenom jednu zprávu, aniž by museli ukládat konkrétní možnosti přizpůsobení pro uživatele aplikace. Tento scénář je znázorněný na následujícím obrázku:

![Diagram znázorňující, jak back-end odesílá jednu zprávu na každou platformu.](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Postup při registraci šablon

Chcete-li provést registraci v šablonách pomocí modelu instalace (preferované) nebo registračního modelu, viz [Správa registrace](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Jazyk výrazu šablony

Šablony jsou omezené na formáty dokumentů XML nebo JSON. Můžete také umístit výrazy jenom na konkrétní místa; například atributy uzlu nebo hodnoty pro XML, hodnoty vlastností řetězce pro JSON.

V následující tabulce je uveden jazyk povolený v šablonách:

| Výraz       | Popis |
| ---------------- | --- |
| $ (Prop)          | Odkaz na vlastnost události se zadaným názvem. V názvech vlastností se nerozlišují velká a malá písmena. Tento výraz se přeloží na textovou hodnotu vlastnosti nebo do prázdného řetězce, pokud vlastnost není k dispozici. |
|$ (Prop, n)       | Jak je uvedeno výše, ale text se explicitně ořízne n znaků, například $ (title, 20), ořízne obsah vlastnosti title o 20 znaků. |
| . (Prop, n)      | Jak je uvedeno výše, ale text má příponu se třemi tečkami, jak je oříznutý. Celková velikost oříznutého řetězce a přípona nepřekračuje n znaků. (title; 20) se vstupní vlastností "Toto je název řádku". Výsledkem je **název...** |
| % (Prop)          | Podobně jako $ (název) s tím rozdílem, že výstup je kódovaný pomocí identifikátoru URI. |
| # (Prop)          | Používá se v šablonách JSON (například pro šablony iOS a Android).<br><br>Tato funkce funguje úplně stejně jako vlastnost $ (Prop), která je dřív zadaná, s výjimkou případů, kdy se používá v šablonách JSON (například šablony Apple). V takovém případě, pokud tato funkce není obklopena "{", "}" (například "myJsonProperty": "# (Name)") a vyhodnotí se jako číslo ve formátu JavaScriptu, například RegExp: (0&#124; (&#91;1-9&#93;&#91;0-9&#93; *)) ( \.&#91;0-9&#93;+)? ( (e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, výstupní JSON je číslo.<br><br>Například ' BADGE ': ' # (Name) ' se bude ' BADGE ': 40 (a ne ' 40 '). |
| ' text ' nebo "text" | Literál. Literály obsahují libovolný text uzavřený v jednoduchých nebo dvojitých uvozovkách. |
| Výraz1 + Výraz2    | Operátor zřetězení spojuje dva výrazy do jednoho řetězce. |

Výrazy mohou být libovolné z předchozích forem.

Při použití zřetězení musí být celý výraz ohraničený pomocí `{}` . Například, `{$(prop) + ‘ - ’ + $(prop2)}`.

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

Jak bylo vysvětleno dříve, při použití zřetězení musí být výrazy zabaleny do složených závorek. Příklad:

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

[Přečtěte si o Azure Notification Hubs](notification-hubs-push-notification-overview.md)

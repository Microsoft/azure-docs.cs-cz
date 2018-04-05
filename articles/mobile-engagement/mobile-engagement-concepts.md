---
title: Koncepty Mobile Engagementu | Dokumentace Microsoftu
description: Koncepty Azure Mobile Engagementu
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 8d19abd1-0a6c-4772-9fa5-5e99980ac5da
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 1bc09de37c1b2aca35ef1ea74227df770f15baf5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
---
# <a name="azure-mobile-engagement-concepts"></a>Koncepty Azure Mobile Engagementu
> [!IMPORTANT]
> Azure Mobile Engagement se vyřadí z provozu 31. 3. 2018. Tato stránka bude odstraněna krátce potom.
> 

Mobile Engagement definuje několik konceptů, které jsou společné pro všechny podporované platformy. Tento článek tyto koncepty stručně popisuje.

Tento článek byste si měli přečíst, pokud s Mobile Engagementem začínáte. Také si nezapomeňte prostudovat dokumentaci pro konkrétní platformu, kterou používáte. Najdete tam podrobnější charakteristiku konceptů popsaných v tomto článku doplněnou více příklady s uvedením možných omezení.

## <a name="devices-and-users"></a>Zařízení a uživatelé
Mobile Engagement rozpoznává uživatele tak, že pro každé zařízení generuje jedinečný identifikátor. Tento identifikátor se nazývá identifikátor zařízení (neboli `deviceid`). Generuje se takovým způsobem, že všechny aplikace spuštěné na stejném zařízení sdílejí stejný identifikátor zařízení.

Implicitně to znamená, že Mobile Engagement počítá s tím, že jedno zařízení patří právě jednomu uživateli, takže uživatelé a zařízení znamenají v podstatě totéž.

## <a name="sessions-and-activities"></a>Relace a aktivity
Relace je jedno použití aplikace, které uživatel provede, počítáno od okamžiku, kdy s používáním začne, až do chvíle, kdy skončí.

Aktivita je jedno použití určité dílčí části aplikace, které provede jeden uživatel (obvykle to bývá zobrazení určité obrazovky, ale může to být i cokoli jiného, co se dá  v rámci aplikace provést).

Uživatel může najednou provést pouze jednu aktivitu.

Aktivita je identifikována názvem (omezeným na 64 znaků) a dají se do ní volitelně vložit i doplňující data (limit 1024 bajtů).

Relace se automaticky vypočítávají podle sekvence aktivit, které uživatelé provádějí. Relace začíná, když uživatel zahájí první aktivitu, a končí, jakmile skončí poslední aktivita. To znamená, že relaci není přímo nutné zahajovat a ukončovat. Místo toho se explicitně zahajují a ukončují aktivity. Pokud není hlášena žádná aktivita, není hlášena ani relace.

## <a name="events"></a>Události
Události se používají k hlášení okamžitých akcí (například stisknutí tlačítka nebo přečtení článku uživatelem).

Událost může souviset s aktuální relací, s probíhající úlohou, nebo se může jednat o samostatnou událost.

Událost je identifikována názvem (omezeným na 64 znaků) a dají se do ní volitelně vložit i doplňující data (limit 1024 bajtů).

## <a name="error"></a>Chyba
Chyby se používají k hlášení problémů, které aplikace správně rozpozná (například chybně použité akce uživatele nebo nepodařené volání rozhraní API).

Chyba může souviset s aktuální relací, s probíhající úlohou, nebo se může jednat o samostatnou chybu.

Chyba je identifikována názvem (omezeným na 64 znaků) a dají se do ní volitelně vložit i doplňující data (limit 1024 bajtů).

## <a name="job"></a>Úloha
Úlohy slouží k hlášení akcí, které mají určitou dobu trvání (například doba volání  API, doba zobrazování reklamy, doba běhu úloh na pozadí nebo délka akcí uživatele).

Úlohy nesouvisí s relací, protože se dají provádět na pozadí, aniž by musel uživatel nějak zasahovat.

Úloha je identifikována názvem (omezeným na 64 znaků) a dají se do ní volitelně vložit i doplňující data (limit 1024 bajtů).

## <a name="crash"></a>Zhroucení
Zhroucení vydává automaticky sada Mobile Engagement SDK. Hlásí tak selhání aplikace, pokud se aplikace zhroutí a sama nerozpozná a nenahlásí chybu.

## <a name="application-information"></a>Informace o aplikaci
Informace o aplikaci slouží k označování uživatelů. To znamená, že aplikace jednotlivým uživatelům přiřadí určitá data. Podobá se to trochu webovým souborům cookie, ale informace o aplikaci se ukládají přímo na serveru platformy Azure Mobile Engagement.

Informace o aplikaci se dají zaregistrovat přes rozhraní API sady Mobile Engagement SDK nebo přes API zařízení platformy Mobile Engagement.

Informace o aplikaci tvoří pár klíč/hodnota přidružený k zařízení. Klíč je název informace o aplikaci (omezený na 64 znaků ASCII – písmena [a-zA-Z], číslice [0-9] a podtržítka [_]). Hodnota (omezena na 1024 znaků) může to být řetězec, celé číslo, datum (rrrr MM-dd) nebo logická hodnota (pravda nebo nepravda).

K zařízení se dá přidružit libovolný počet informací o aplikaci (v rámci omezení stanoveného cenovými podmínkami Mobile Engagementu). Pro každý klíč uchovává Mobile Engagement pouze nejnovější nastavenou hodnotu (žádná historie). Nastavení nebo změna hodnoty informace o aplikaci přinutí Mobile Engagement znovu vyhodnotit kritéria cílové skupiny nastavená pro tuto informaci o aplikaci (pokud tato kritéria existují). To znamená, že informace o aplikaci lze využít k aktivaci nabízených oznámení v reálném čase.

## <a name="extra-data"></a>Doplňující data
Doplňující data jsou libovolná data, která se dají připojit k událostem, chybám, aktivitám nebo úlohám.

Doplňující data jsou strukturovaná podobně jako objekty JSON: tvoří je strom z párů klíč/hodnota. Klíče jsou omezené na 64 znaků ASCII, tj. písmena [a-zA-Z], číslice [0-9] a podtržítka [_]). Celková velikost doplňkových dat je omezená na 1024 znaků (po zakódování ve formátu JSON přes sadu Mobile Engagement SDK).

Celý strom párů klíč/hodnota se ukládá jako objekt JSON. Nicméně pouze první úroveň párů klíč/hodnota je rozložená, aby byly přímo přístupné pro některé pokročilé funkce, jako jsou třeba segmenty. Můžete například snadno definovat segment s názvem „scifisti“ sestávající ze všech uživatelů, kteří za minulý měsíc alespoň desetkrát odeslali událost s názvem „content_viewed“, u které je doplňkový klíč „content_type“ nastaven na hodnotu „scifi“. Proto důrazně doporučujeme odesílat pouze doplňková data tvořená z jednoduchých seznamů párů klíč/hodnota, které používají skalárních hodnoty (například řetězce, data, celá čísla nebo logické hodnoty).

## <a name="next-steps"></a>Další kroky
* [Přehled sady Windows Universal SDK pro Azure Mobile Engagement](mobile-engagement-windows-store-sdk-overview.md)
* [Přehled sady Windows Phone Silverlight SDK pro Azure Mobile Engagement](mobile-engagement-windows-phone-sdk-overview.md)
* [iOS SDK pro Azure Mobile Engagement](mobile-engagement-ios-sdk-overview.md)
* [Android SDK pro Azure Mobile Engagement](mobile-engagement-android-sdk-overview.md)


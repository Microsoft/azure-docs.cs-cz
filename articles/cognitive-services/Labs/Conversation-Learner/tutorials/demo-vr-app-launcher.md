---
title: Ukázka konverzace student skutečnosti aplikace, virtuální aplikace Spouštěče - kognitivní služby Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informace o vytvoření ukázkové aplikace student konverzace.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3e41125bf7da9ee64d666d22cb275af01af54012
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35343261"
---
# <a name="demo-virtual-reality-app-launcher"></a>Ukázku: Spouštěč aplikace virtuální skutečnosti

Tato ukázka znázorňuje Spouštěč aplikace virtuální skutečnosti, podpora příkazy, jako jsou "spustit Skype a umístí stěny." Uživatel potřebuje a sdělit, chcete-li spustit aplikaci název aplikace a umístění. Spuštění aplikace zpracovává volání rozhraní API. Název aplikace rozpoznané od uživatele, entityDetectionCallback kontroluje, zda požadovaná aplikace odpovídá jedné nebo víc aplikací v seznamu nainstalovaných aplikací. Zpracovává tento případ, kde není nainstalována požadovaná aplikace, a název aplikace je nejednoznačný (odpovídá více než jeden nainstalovanou aplikaci).

## <a name="requirements"></a>Požadavky

Tento kurz vyžaduje, aby robota VRAppLauncher běží

    npm run demo-vrapp

### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu aplikací webového uživatelského rozhraní klikněte na VRAppLauncher. 

## <a name="entities"></a>Entity

Vytvořili jsme čtyři entity:

- AppName: pro příklad Skype
- PlacementLocation: pro příklad wall
- UnknownAppName: programové entita, která nastaví systému při nerozpoznal název entity uživateli říká, například nebyl nainstalován.
- DisAmbigAppNames: uvedená pole dvou nebo více názvů nainstalovanou aplikaci, které odpovídají to, co uživatel. 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>Akce

Vytvořili jsme sadu akcí, která zahrnuje rozhraní API volat LaunchApp, čímž se spustí volání funkce spustí aplikaci.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>Školení dialogová okna
Jsme definovali dialogy školení.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

Jako příklad Zkusme vyučující relace.

1. Klikněte na tlačítko Train dialogová okna, dialogové okno pak nový vlaku.
1. Zadejte "hi".
2. Klikněte na tlačítko akce skóre.
3. Klikněte na možnost vyberte, které aplikace Opravdu chcete spustit?"
4. Zadejte "outlook".
    - Všimněte si, že LEOŠ ho rozpozná jako entity.
5. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte, kde chcete ji umístit?"
4. Zadejte "na wall'.
    - Všimněte si, že LEOŠ ho rozpozná jako PlacementLocation.
2. Zadejte akce skóre.
6. Vyberte 'LaunchApp.
7. System: 'spuštění aplikace Microsoft outlook na stěny'.
    - Všimněte si, že tento stav aktivován volání rozhraní API. Kód pro toto volání je ve C:\<\installedpath > \src\demos\demoVRAppLauncher.ts. Logika pro aplikaci outlook pro tuto ukázku spustit ale ve skutečnosti nemá.
    - Vypne AppName a PlacementLocation entity. Se vrátí řetězec uvedený výše jako odpověď.
4. Klikněte na tlačítko Hotovo vyučující.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Začněme jiná relace školení pro zpracování neznámé a nejednoznačný entity.

1. Kliknutím na dialogové okno Nový vlaku.
1. Zadejte 'start OneNote'. 
    - Rozpozná ho jako název aplikace vzhledem k tomu, že EntityDetectionCallback definované v kódu trvá zadá uživatelské jméno a přeloží na název aplikace pomocí odpovídajících u seznamu aplikací, které jsou definované v kódu. Pak vrátí sadu všechny odpovídající aplikace. 
    - Pokud je seznam shod nula, to znamená, že aplikace není nainstalovaná. Vloží jej do unknownAppName.
    - Pokud najde více než jednu aplikaci, bude zkopírujte je do DisambigAppNames a vymazat AppName entity.
2. Klikněte na tlačítko akce skóre.
3. Kliknutím vyberte "Bohužel neznámého aplikace $UknownAppName."
4. Zadejte 'start amazon'. Pokusíme se jiné cesty.
5. Klikněte na tlačítko akce skóre.
    - Poznámka: Amazon Video a Amazon hudba jsou teď ve DisambigAppNames paměti. A OneNote byl vymazán.
3. Kliknutím vyberte "Existuje několik aplikací, které zvukových jako je například..."
    - Všimněte si, že skóre není velmi vysoké, protože máme několik dialogů školení až tento bod. Vypadá to by potřebujeme některé zde chcete-li model více klíčovou.
2. Zadejte akce skóre.
4. Klikněte na tlačítko Hotovo vyučující.

Nyní jste viděli postup řešení entity. Ukázku také ukazuje zpětná volání rozhraní API a vám ukázal, šablony pro shromažďování informací o, Kontrola přítomnosti a nejednoznačnosti a pravém akce, která na základě.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nasazení konverzace student robota](../deploy-to-bf.md)

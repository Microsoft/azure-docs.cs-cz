---
title: Ukázka Spouštěče aplikací konverzace Learner modelů, virtuální realitu – Microsoft Cognitive Services | Dokumentace Microsoftu
titleSuffix: Azure
description: Zjistěte, jak vytvořit model konverzace Learner ukázku.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 896ec007c03e30e5c20a5344430be040271bc00b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171140"
---
# <a name="demo-virtual-reality-app-launcher"></a>Ukázka: Spouštěč aplikací virtuální realitu

Tato ukázka znázorňuje Spouštěč virtuální realitu modelu, který podporuje příkazech, jako je "start Skype a umístit na zdi." Uživatel musí třeba název aplikace a služby umístění Pokud chcete spustit aplikaci. Spouštění modelu zpracovává volání rozhraní API. Název aplikace rozpoznané od uživatele, entityDetectionCallback kontroluje, zda požadovaná aplikace odpovídá jedné nebo víc aplikací v seznamu nainstalovaných aplikací. Zpracovává případ, kde není nainstalována požadovaná aplikace, a název aplikace je nejednoznačný (odpovídá více než jeden nainstalované aplikace).

## <a name="video"></a>Video

[![Ukázka VR aplikací ve verzi Preview](http://aka.ms/cl-demo-vrapp-preview)](http://aka.ms/blis-demo-vrapp)

## <a name="requirements"></a>Požadavky

Tento kurz vyžaduje, že VRAppLauncher bot běží:

    npm run demo-vrapp
    
### <a name="open-the-demo"></a>Otevřete ukázku

V seznamu modelu ve webovém uživatelském rozhraní kliknutím na VRAppLauncher. 

## <a name="entities"></a>Entity

Vytvořili jsme čtyři entity:

- AppName: pro příklad Skype
- PlacementLocation: pro příklad zeď
- UnknownAppName: programové entity, která nastavuje systému, když ho nedokáže rozpoznat název entity uživatel zvolí, například protože není nainstalována.
- DisAmbigAppNames: pole dvou nebo více názvů nainstalované aplikace, které odpovídají uživatele, ale nutné dodat. 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>Akce

Vytvořili jsme sadu akcí, která zahrnuje rozhraní API volá LaunchApp, čímž se spustí volání funkce ke spuštění aplikace.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>Dialogová okna školení
Jsme definovali dialogy školení.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

Jako příklad si vyzkoušíme relaci výuky.

1. Klikněte na dialogová okna trénování, pak nové dialogové okno trénování.
1. Zadejte "hi".
2. Klikněte na akci skóre.
3. Klikněte na Vybrat "kterou aplikaci chcete spustit?"
4. Zadejte "Outlooku".
    - Služba LUIS je rozpozná jako entity.
5. Klikněte na výsledek akce.
3. Kliknutím vyberte "kde chcete ji umístit?"
4. Zadejte "na zeď".
    - Služba LUIS je rozpozná jako PlacementLocation.
2. Zadejte akce skóre.
6. Vyberte "LaunchApp.
7. System: "spuštění aplikace Microsoft outlook na zeď".
    - Aktivuje toto volání rozhraní API. Kód pro toto volání je c:\<\installedpath > \src\demos\demoVRAppLauncher.ts. Však neobsahuje ve skutečnosti kód ke spuštění aplikace Outlook pro tuto ukázku.
    - Vymaže AppName a PlacementLocation entit. Vrátí řetězec uvedený výše jako odpověď.
4. Klikněte na Hotovo výuky.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Začněme jiná relace školení pro zpracování neznámé a nejednoznačný entity.

1. Kliknutím na dialogové okno Nový trénování.
1. Zadejte 'start Onenotu'. 
    - Model rozpozná Onenotu jako název aplikace. `EntityDetectionCallback` Funkci definovanou v kódu přeloží název zadaný uživatelem na název aplikace to provede spárováním odpovídajících do seznamu aplikací, které jsou definovány v kódu. Pak vrací sadu všech odpovídajících aplikací. 
    - Pokud je seznam shod nula, znamená to, že aplikace není nainstalovaná. Vloží jej do unknownAppName.
    - Pokud se najde více než jednu aplikaci, zkopíruje je do `DisambigAppNames` a vymaže AppName entity.
2. Klikněte na akci skóre.
3. Kliknutím vyberte: je nám líto, nevím aplikace $UknownAppName."
4. Zadejte 'start Amazon'. Zkusíme jiné cesty.
5. Klikněte na výsledek akce.
    - Amazon videa a Hudba Amazon jsou teď v `DisambigAppNames` se vymazala paměti a OneNote.
3. Kliknutím vyberte "Existuje několik aplikací, která zní tímto způsobem..."
    - Skóre je velmi vysoké, protože jsme definovali pouze několik dialogů školení do této chvíle. Definování další dialogová okna školení s žádným modelu rychlejší rozhodování.
2. Zadejte akce skóre.
4. Klikněte na Hotovo výuky.

Nyní jste viděli jak dělat entit řešení. Ukázky také popisují zpětná volání rozhraní API a jsme si ukázali, šablony pro shromažďování informací o provedení kontroly na přítomnost a nejednoznačnosti a odpovídajícím způsobem trvá správnou akci.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Nasazení robota Learner konverzace](../deploy-to-bf.md)

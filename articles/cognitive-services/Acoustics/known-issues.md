---
title: Známé problémy s modulem plug-in s akustickými projekty
titlesuffix: Azure Cognitive Services
description: Při použití náhledu návrháře pro akustické navýšení projektu může dojít k následujícím známým potížím.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: kylsto
ms.openlocfilehash: 53df981564eb177da66b86022ecfc80b25f1c763
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68296537"
---
# <a name="project-acoustics-known-issues"></a>Známé problémy v projektu
Při použití náhledu návrháře pro akustické navýšení projektu může dojít k následujícím známým potížím.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Při přejmenování scény se ztratí akustické parametry.

Pokud přejmenujete scénu, všechny akustické parametry, které patří do této scény, se automaticky nepřesunou do nové scény. Stále existují v původním souboru prostředků. Vyhledejte soubor **SceneName_AcousticParameters. Asset** v adresáři **editoru** vedle souboru scény. Přejmenujte soubor tak, aby odrážel název nové scény.

## <a name="unity-crashes-when-closing-project"></a>Chyby Unity při zavírání projektu

V nejnovějších verzích Unity (2018.2 +) existuje známá chyba, kde při zavření projektu dojde k chybě Unity. Toto je sledováno tímto [problémem Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="deploying-to-android-from-some-unity-versions"></a>Nasazení na Android z některých verzí Unity

Některé verze Unity mají chybu s nasazením zvukových modulů plug-in do Androidu. Ujistěte se, že nepoužíváte verzi ovlivněnou [touto chybou](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Zobrazila se chyba, že se nepodařilo najít soubor metadat System. Security. dll.

Ujistěte se, že verze skriptovacího modulu runtime v nastavení přehrávače je nastavená na hodnotu **ekvivalent .NET 4. x**a restartuje Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Mám problémy s ověřováním při připojování k Azure

Přečtěte si, že jste pro svůj účet Azure použili správné přihlašovací údaje, že váš účet podporuje typ uzlu požadovaný v zanesli a že vaše systémové hodiny jsou přesné.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Zrušení zanesli opustí kartu zanesli ve stavu "odstranění".
Akustické projekty budou vyčistit všechny prostředky Azure pro úlohu po úspěšném dokončení nebo zrušení. Tato možnost může trvat až 5 minut.

## <a name="next-steps"></a>Další kroky
* Vyzkoušejte si vzorový obsah [Unity](unity-quickstart.md) nebo [Unreal](unreal-quickstart.md)


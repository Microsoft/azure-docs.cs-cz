---
title: Známé problémy s modulem plug-in s akustickými projekty
titlesuffix: Azure Cognitive Services
description: Při použití náhledu návrháře pro akustické navýšení projektu může dojít k následujícím známým potížím.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a70d9ea1090f1c518f804dd28f3461918af965cd
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854288"
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

## <a name="next-steps"></a>Další postup
* Vyzkoušejte si vzorový obsah [Unity](unity-quickstart.md) nebo [Unreal](unreal-quickstart.md)


---
title: Známé problémy modulu plug-in Project Acoustics
titlesuffix: Azure Cognitive Services
description: V aplikaci Project Acoustics může dojít k následujícím známým problémům.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243041"
---
# <a name="project-acoustics-known-issues"></a>Známé problémy projektu Akustika
Tento článek popisuje problémy, které mohou nastat při použití aplikace Project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Při přejmenování scény dojde ke ztrátě akustických parametrů

Pokud přejmenujete scénu, všechny akustické parametry, které do této scény patří, se automaticky nepřenesou do nové scény. Ale stále existují ve starém souboru majetku. Vyhledejte soubor *[SceneName]_AcousticParameters.asset* v *adresáři Editor* vedle souboru scény. Přejmenujte soubor tak, aby odrážel nový název scény.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Chyba nasazení androida z některých verzí Unity

Některé verze Unity mají [chybu](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) v tom, jak nasazují zvukové plug-iny do systému Android. Ujistěte se, že nepoužíváte verzi, která je touto chybou ovlivněna.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Chyba "Nelze najít soubor metadat System.Security.dll"

Ujistěte se, že **skriptovací runtime verze** v nastavení **přehrávače** je *.NET 4.x Ekvivalentní*a restartujte Unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Problémy s ověřováním při připojování k Azure

Zkontrolujte, zda:
- Použili jste správná pověření pro váš účet Azure.
- Váš účet podporuje typ uzlu, který jste požadovali v pečeme.
- Systémové hodiny jsou nastaveny správně.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>Na kartě Pečeme se po zrušení stále zobrazuje "odstranění"
Aplikace Project Acoustics vyčistí všechny prostředky Azure pro úlohu po úspěšném dokončení nebo zrušení. Tento proces může trvat až 5 minut.

## <a name="next-steps"></a>Další kroky
* Vyzkoušejte ukázkový obsah [Unity](unity-quickstart.md) nebo [Unreal.](unreal-quickstart.md)

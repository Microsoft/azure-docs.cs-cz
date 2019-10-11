---
title: Známé problémy s modulem plug-in projektu
titlesuffix: Azure Cognitive Services
description: Může dojít k následujícím známým problémům v důsledku akustického projektu.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243041"
---
# <a name="project-acoustics-known-issues"></a>Známé problémy v projektu
Tento článek popisuje problémy, se kterými se můžete setkat při používání akustického projektu.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Při přejmenování scény se ztratí akustické parametry.

Pokud přejmenujete scénu, všechny akustické parametry, které patří do této scény, se automaticky nepřesunou do nové scény. Ale stále existují v původním souboru prostředků. V adresáři *editoru* vedle vašeho souboru scény vyhledejte soubor *[scéna] _AcousticParameters. Asset* . Přejmenujte soubor tak, aby odrážel název nové scény.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Chyba nasazení na Android z některých verzí Unity

Některé verze Unity mají [chybu](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) ve způsobu, jakým nasazují zvukové moduly plug-in do Androidu. Ujistěte se, že nepoužíváte verzi, na kterou tato chyba ovlivnila.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"Nepovedlo se najít chybu souborového souboru metadat System. Security. dll.

Ujistěte se, že **verze skriptovacího modulu runtime** v nastavení **přehrávače** je *ekvivalentní .NET 4. x*a restartuje Unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Problémy s ověřováním při připojování k Azure

Ověřte, že:
- Použili jste ke svému účtu Azure správné přihlašovací údaje.
- Váš účet podporuje typ uzlu, který jste požadovali v zanesli.
- Systémové hodiny se nastavují správně.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>Po zrušení se na kartě zanesli pořád zobrazuje "odstraňování".
Akustické projekty po úspěšném dokončení nebo zrušení vyčistí všechny prostředky Azure pro úlohu. Tento proces může trvat až 5 minut.

## <a name="next-steps"></a>Další kroky
* Vyzkoušejte si vzorový obsah [Unity](unity-quickstart.md) nebo [Unreal](unreal-quickstart.md) .

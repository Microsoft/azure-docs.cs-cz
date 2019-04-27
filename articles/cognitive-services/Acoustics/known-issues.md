---
title: Známé problémy s modulem plug-in Akustika projektu
titlesuffix: Azure Cognitive Services
description: Při použití návrháře ve verzi Preview pro projekt Akustika se můžete setkat s následující známé problémy.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 03/20/2019
ms.author: kylestorck
ms.openlocfilehash: 50de4d983ed24440d655cf5b9ba3fb5e33d8d7cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61335343"
---
# <a name="project-acoustics-known-issues"></a>Projekt Akustika známé problémy
Při použití návrháře ve verzi Preview pro projekt Akustika se můžete setkat s následující známé problémy.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akustický parametry jsou ztraceny při přejmenování scény

Pokud přejmenujete scény, vše, co akustický parametry, které patří do tohoto scény nebude automaticky převést na novou scénu. Budete stále existují v původní soubor prostředku ale. Hledat **SceneName_AcousticParameters.asset** soubor uvnitř **Editor** adresáře vedle souboru scény. Přejmenujte soubor tak, aby odrážely nový název scény.

## <a name="unity-crashes-when-closing-project"></a>Unity, dojde k chybě při zavření projektu

Na nejnovějších verzích Unity (2018.2 +) se o známý problém, kdy Unity dojde k chybě při zavření projektu. To je sledován pomocí funkce [potíže Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="deploying-to-android-from-some-unity-versions"></a>Nasazení do systému Android z některé verze Unity

Některé verze Unity mají chybu s nasazením zvuku modulů plug-in do systému Android. Ujistěte se, že nepoužíváte verzi ovlivněny [tuto chybu](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Dojde k chybě tento "nelze najít soubor metadat System.Security.dll.

Zkontrolujte verze modulu Runtime skriptování v nastavení Player je nastavená na **.NET 4.x ekvivalent**a restartujte Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Mám potíže s ověřování při připojení k Azure

Zkontrolujte používáte správné přihlašovací údaje k účtu Azure, že váš účet podporuje typ uzlu, která má označení vytvoření požadované a že systémové hodiny je přesné.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Zrušení která má označení vytvoření opustí, která má označení vytvoření karty ve stavu "odstraňování"
Projekt Akustika vyčistí všechny prostředky Azure pro úlohy na úspěšném dokončení nebo zrušení. To může trvat až 5 minut.

## <a name="next-steps"></a>Další postup
* Zkuste [Unity](unity-quickstart.md) nebo [Unreal](unreal-quickstart.md) ukázkový obsah


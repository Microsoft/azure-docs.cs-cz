---
title: Známé problémy s modulem plug-in Akustika projektu
titlesuffix: Azure Cognitive Services
description: Při použití návrháře ve verzi Preview pro projekt Akustika se můžete setkat s následující známé problémy.
services: cognitive-services
author: kylestorck
manager: cgronlun
ms.service: cognitive-services
ms.component: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: 6d3605b579a44dccb259bef281392cbfe2b9f916
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902144"
---
# <a name="known-issues"></a>Známé problémy
Při použití návrháře ve verzi Preview pro projekt Akustika se můžete setkat s následující známé problémy.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akustický parametry jsou ztraceny při přejmenování scény

Pokud přejmenujete scény, akustický parametry, které patří do tohoto scény nebude automaticky převést na novou scénu. Bude stále existují v původní soubor prostředku ale. Hledat **SceneName_AcousticParameters.asset** soubor uvnitř **Editor** adresáře vedle souboru scény. Přejmenujte soubor tak, aby odrážely nový název scény.

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>Modul runtime voxels jsou jinou velikost než voxels scény ve verzi preview

Pokud to uděláte **Calculate** na **sondy** karty a zobrazení voxels, proveďte která má označení vytvoření a zobrazení voxels za běhu pro stejnou scénu, voxels jsou různých velikostí. Voxels zobrazí před která má označení vytvoření jsou voxels používaných pro simulaci. Voxels, zobrazuje se v modulu runtime se používají pro interpolaci mezi body testu. To může způsobit nekonzistenci umístění portály open za běhu, které nejsou ve skutečnosti otevřít.

## <a name="unity-crashes-when-closing-project"></a>Unity, dojde k chybě při zavření projektu

Na nejnovějších verzích Unity (2018.2 +) se o známý problém, kdy Unity dojde k chybě při zavření projektu. To je sledován pomocí funkce [potíže Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Potíže s nasazením do systému Android
Použití Akustika projekt v Androidu, změňte cíl sestavení pro Android. Některé verze Unity mají chybu s nasazením zvuku moduly plug-in – Ujistěte se, že nepoužíváte verzi ovlivněny [tuto chybu](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Dojde k chybě tento "nelze najít soubor metadat System.Security.dll.

Zkontrolujte verze modulu Runtime skriptování v nastavení Player je nastavená na **.NET 4.x ekvivalent**a restartujte Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Mám potíže s ověřování při připojení k Azure

Zkontrolujte používáte správné přihlašovací údaje k účtu Azure, že váš účet podporuje typ uzlu, která má označení vytvoření požadované a že systémové hodiny je přesné.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>Zrušení která má označení vytvoření opustí, která má označení vytvoření karty ve stavu "odstraňování"
Akustika projektu bude vyčistit všechny prostředky Azure pro úlohy na úspěšném dokončení nebo zrušení, což může trvat až 5 minut.

## <a name="next-steps"></a>Další postup
* Začínáme s [integrací akustiky do projektu Unity](getting-started.md)


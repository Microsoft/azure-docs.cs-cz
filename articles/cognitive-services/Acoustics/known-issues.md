---
title: Známé problémy s modulem plug-in Akustika – Cognitive Services
description: Při použití návrháře ve verzi Preview pro projekt Akustika se můžete setkat s následující známé problémy.
services: cognitive-services
author: kylestorck
manager: noelc
ms.service: cognitive-services
ms.component: acoustics
ms.topic: article
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: c19b19cab33ae868f11ded0b7ce87dac99269596
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47431972"
---
# <a name="known-issues"></a>Známé problémy
Při použití návrháře ve verzi Preview pro projekt Akustika se můžete setkat s následující známé problémy.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akustický parametry jsou ztraceny při přejmenování scény

Pokud přejmenujete scény, akustický parametry, které patří do tohoto scény nebude automaticky převést na novou scénu. Bude stále existují v původní soubor prostředku ale. Hledat **SceneName_AcousticParameters.asset** soubor uvnitř **Editor** adresáře vedle souboru scény. Přejmenujte soubor tak, aby odrážely nový název scény.

## <a name="the-default-path-for-the-acousticsdata-folder-in-probes-tab-is-an-absolute-path"></a>Výchozí cesta ke složce AcousticsData na kartě testy je absolutní cesta

To by ve výchozím nastavení relativní cestu k usnadnění sdílení projektů mezi spolupracovníky. Jako alternativní řešení změníte cestu relativní vzhledem k adresáři projektu.

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>Modul runtime voxels jsou jinou velikost než voxels scény ve verzi preview

Pokud to uděláte **Calculate** na **sondy** karty a zobrazení voxels, proveďte která má označení vytvoření a zobrazení voxels za běhu pro stejnou scénu, voxels jsou různých velikostí. Voxels zobrazí před která má označení vytvoření jsou voxels používaných pro simulaci. Voxels, zobrazuje se v modulu runtime se používají pro interpolaci mezi body testu. To může způsobit nekonzistenci umístění portály open za běhu, které nejsou ve skutečnosti otevřít.

## <a name="uwp-builds-not-working"></a>UWP vytváří nefunguje

Na nejnovějších verzích Unity (2018.2 +) nejsou UPW sestavení úspěšná. Manipulace se blokováním do fáze spuštění sestavení a bude docházet k chybám "rozšíření Unity nejsou inicializovány ještě". To je sledován pomocí funkce [potíže Unity](https://fogbugz.unity3d.com/default.asp?1070491_1rgf14bakv5u779d).

## <a name="unity-crashes-when-closing-project"></a>Unity, dojde k chybě při zavření projektu

Na nejnovějších verzích Unity (2018.2 +) se o známý problém, kdy Unity dojde k chybě při zavření projektu. To je sledován pomocí funkce [potíže Unity](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Potíže s nasazením do systému Android
Použití Akustika projekt v Androidu, změňte cíl sestavení pro Android. Některé verze Unity mají chybu s nasazením zvuku moduly plug-in – Ujistěte se, že nepoužíváte verzi ovlivněny [tuto chybu](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Dojde k chybě tento "nelze najít soubor metadat System.Security.dll.

Zkontrolujte verze modulu Runtime skriptování v nastavení Player je nastavená na **.NET 4.x ekvivalent**a restartujte Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>Mám potíže s ověřování při připojení k Azure

Zkontrolujte používáte správné přihlašovací údaje k účtu Azure, že váš účet podporuje typ uzlu, která má označení vytvoření požadované a že systémové hodiny je přesné.

## <a name="next-steps"></a>Další postup
* Začínáme s [integrací akustiky do projektu Unity](getting-started.md)


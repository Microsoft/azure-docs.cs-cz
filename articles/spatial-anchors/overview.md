---
title: Přehled služby Azure prostorových kotvy | Dokumentace Microsoftu
description: Zjistěte, jak prostorových kotvy Azure vám pomůže vytvořit prostředí pro různé platformy, smíšené reality.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 2451922f0eb49a5ccee036db72eb046760287dca
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "60240480"
---
# <a name="azure-spatial-anchors-overview"></a>Přehled služby Azure prostorových kotvy

Vítá vás Azure prostorových ukotvení. Azure prostorových kotvy umožňuje vývojářům základní možnosti pro vytváření aplikací prostorově vědět smíšená realita. Tyto aplikace můžou podporovat Microsoft HoloLens, podpora arkit, která zařízení se systémem iOS a zařízení se systémem Android podporuje ARCore. Prostorové kotvy Azure umožňuje vývojářům fungovat pro hybridní realitu vnímat mezery, určit přesné body zájmu a stránkám znovu vyvolat tyto body zájmu z podporovaných zařízení.
Tyto přesné body zájmu se označují jako prostorových ukotvení.

![Různé platformy](./media/cross-platform.png)

## <a name="examples"></a>Příklady

Některé příklady případů použití umožněné prostorových kotvy zahrnují:

- [Prostředí pro více uživatelů](tutorials/tutorial-share-anchors-across-devices.md). Prostorový kotvy usnadňuje lidí na stejném místě k účasti v aplikacích pro hybridní realitu více uživatelů. Například dva uživatelé hru lze spustit smíšené reality Šachy umístěním virtuální šachovnici na tabulku. Najetím myší svoje zařízení v tabulce, jsou pak, zobrazení a interakci s virtuální šachovnici společně.

- [Způsob zjištění](concepts/anchor-relationships-way-finding.md). Také můžou vývojáři připojit prostorových kotvy společně vytváření vztahů mezi nimi. Aplikace může například obsahovat prostředí, které má dva nebo víc bodů zájmu, které uživatel musí spolupracovat s k dokončení úkolu. Tyto body zájmu možné vytvářet propojené způsobem. Později když uživatel dokončuje vícekrokových úkolů, aplikaci požádat o kotev vztahů, které jsou poblíž tu nasměrovat uživatele na další krok v úloze.

- [Zachování virtuální obsahu v reálném světě](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Aplikace můžete nechat uživatele umístit virtuální kalendáře na zeď místnosti konference, uživatelé mohou zobrazit aplikaci pro telefon nebo na zařízení HoloLens. V průmyslových nastavení, může uživatel zobrazit kontextové informace o počítači pouhým ukázáním fotoaparát podporovaných zařízení.

Azure prostorových ukotvení se skládá z spravovaných služeb a klientských sad SDK pro podporované platformy zařízení. Následující části obsahují informace o začátcích se vytváření aplikací s použitím Azure prostorových ukotvení.

## <a name="next-steps"></a>Další postup

Vytvoření první aplikace pomocí prostorových ukotvení.

> [!div class="nextstepaction"]
> [Unity](unity-overview.yml)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

---
title: Přehled prostorových kotev Azure
description: Přečtěte si, jak prostorové kotvy Azure pomáhají vyvíjet hybridní prostředí realit pro různé platformy.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 361a75c2e5951dc540830efd6be057753402daf1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "83006097"
---
# <a name="azure-spatial-anchors-overview"></a>Přehled prostorových kotev Azure

Vítá vás prostorové kotvy Azure. Prostorové kotvy Azure umožňují vývojářům vytvářet základní funkce pro vytváření aplikací s kombinovanými realitami s prostorovou podporou. Tyto aplikace můžou podporovat Microsoft HoloLens, zařízení se systémem iOS podporující ARKit a zařízení s Androidem podporující ARCore. Azure Spatial Anchors umožňuje vývojářům pracovat s platformami smíšené reality, vnímat prostory, navrhovat přesné body zájmu a vyvolat tyto body zájmu z podporovaných zařízení.
Tyto přesné body zájmu se označují jako prostorové kotvy.

![Různé platformy](./media/cross-platform.png)

## <a name="examples"></a>Příklady

Příklady případů použití povolené prostorovými kotvami zahrnují:

- [Prostředí pro více uživatelů](tutorials/tutorial-share-anchors-across-devices.md). Prostorové kotvy Azure usnadňují lidem na stejném místě účast v aplikacích hybridní reality pro více uživatelů. Například dvě osoby můžou začít hru šachy ve hybridní realitě tím, že na tabulku umístí virtuální šachovnici. Díky tomu, že umístíte zařízení v tabulce, může zobrazit a pracovat s virtuální šachovnicí společně.

- [Způsob – hledání](concepts/anchor-relationships-way-finding.md). Vývojáři mohou také prostorové kotvy propojit vytvářením vztahů mezi nimi. Aplikace může například zahrnovat prostředí, které má dva nebo více bodů zájmu, se kterými musí uživatel pracovat, aby dokončil úkol. Tyto body zájmu je možné vytvořit připojeným způsobem. Když později uživatel dokončuje úlohu s více kroky, může požádat o kotvy, které jsou poblíž aktuální, a nasměrovat uživatele k dalšímu kroku v úloze.

- [Zachování virtuálního obsahu v reálném světě](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Aplikace může uživateli umístit virtuální kalendář na zeď konferenční místnosti, aby mohli vidět použití aplikace pro telefon nebo zařízení HoloLens. V průmyslovém prostředí může uživatel přijímat kontextové informace o stroji tak, že na něj nasměruje kameru podporovaného zařízení.

Azure Spatial Anchors se skládá ze spravované služby a klientských sad SDK pro podporované platformy zařízení. V následujících částech najdete informace o tom, jak začít sestavovat aplikace pomocí prostorových kotev Azure.

## <a name="next-steps"></a>Další kroky

Vytvořte svou první aplikaci pomocí prostorových kotev Azure.

> [!div class="nextstepaction"]
> [Unity (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Unity (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Unity (Android)](quickstarts/get-started-unity-android.md)

> [!div class="nextstepaction"]
> [iOS](quickstarts/get-started-ios.md)

> [!div class="nextstepaction"]
> [Android](quickstarts/get-started-android.md)

> [!div class="nextstepaction"]
> [HoloLens](quickstarts/get-started-hololens.md)

> [!div class="nextstepaction"]
> [Xamarin (Android)](quickstarts/get-started-xamarin-android.md)

> [!div class="nextstepaction"]
> [Xamarin (iOS)](quickstarts/get-started-xamarin-ios.md)

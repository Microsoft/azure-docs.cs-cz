---
title: Přehled prostorových kotev Azure
description: Zjistěte, jak vám Azure Spatial Anchors pomáhá vyvíjet prostředí smíšené reality napříč platformami.
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: 5ebd29b5fb6fdedcdfbc434209b350512c4cd5dc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77657303"
---
# <a name="azure-spatial-anchors-overview"></a>Přehled prostorových kotev Azure

Vítá vás prostorové kotvy Azure. Azure Spatial Anchors umožňuje vývojářům základní funkce k vytváření prostorově uvědomit aplikace smíšené reality. Tyto aplikace mohou podporovat Microsoft HoloLens, zařízení se systémem iOS podporující ARKit a zařízení se systémem Android podporující ARCore. Azure Spatial Anchors umožňuje vývojářům pracovat s platformami smíšené reality, vnímat prostory, navrhovat přesné body zájmu a vyvolat tyto body zájmu z podporovaných zařízení.
Tyto přesné body zájmu jsou označovány jako prostorové kotvy.

![Různé platformy](./media/cross-platform.png)

## <a name="examples"></a>Příklady

Některé příklady případů použití povolených prostorovými kotvami zahrnují:

- [Víceuživatelské prostředí](tutorials/tutorial-share-anchors-across-devices.md). Prostorové kotvy usnadňují lidem na stejném místě účast v aplikacích smíšené reality pro více uživatelů. Například, dva lidé mohou začít hru smíšené reality šachy umístěním virtuální šachovnici na stůl. Poté, když namíří své zařízení na stůl, mohou společně zobrazit virtuální šachovnici a pracovat s ní.

- [Way-hledání](concepts/anchor-relationships-way-finding.md). Vývojáři mohou také propojit prostorové kotvy dohromady a vytvářet vztahy mezi nimi. Aplikace může například obsahovat prostředí, které má dva nebo více bodů zájmu, se kterými musí uživatel pracovat k dokončení úkolu. Tyto body zájmu mohou být vytvořeny v propojeném způsobem. Později, když uživatel provádí vícekrokový úkol, aplikace může požádat o kotvy, které jsou v blízkosti aktuální, aby uživatele nasměrovaly k dalšímu kroku úkolu.

- [Zachování virtuálního obsahu v reálném světě](how-tos/create-locate-anchors-unity.md#create-a-cloud-spatial-anchor). Aplikace může uživateli unechat umístit virtuální kalendář na stěnu konferenční místnosti, který mohou lidé vidět pomocí aplikace pro telefon nebo zařízení HoloLens. V průmyslovém prostředí může uživatel přijímat kontextové informace o stroji tak, že na něj nasměruje kameru podporovaného zařízení.

Azure Spatial Anchors se skládá ze spravované služby a klientských sad SDK pro podporované platformy zařízení. Následující části obsahují informace o tom, jak začít s vytvářením aplikací pomocí prostorových ukotvení Azure.

## <a name="next-steps"></a>Další kroky

Vytvořte si první aplikaci s prostorovými kotvami.

> [!div class="nextstepaction"]
> [Jednota (HoloLens)](quickstarts/get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [Jednota (iOS)](quickstarts/get-started-unity-ios.md)

> [!div class="nextstepaction"]
> [Jednota (Android)](quickstarts/get-started-unity-android.md)

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

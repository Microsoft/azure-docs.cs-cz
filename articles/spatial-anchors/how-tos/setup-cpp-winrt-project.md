---
title: Instalace prostorových kotev Azure pro aplikaci HoloLens v C++/WinRT
description: Konfigurace projektu aplikace/WinRT v jazyce C++ pro použití prostorových kotev Azure
author: craigktreasure
manager: vriveras
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 43d5c1ae03c359dcbef21f8e7ba3205bc6ab0004
ms.sourcegitcommit: 93329b2fcdb9b4091dbd632ee031801f74beb05b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92096109"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>Konfigurace prostorových ukotvení Azure v projektu/WinRT v C++

## <a name="requirements"></a>Požadavky

* Sada [Visual Studio 2019](https://www.visualstudio.com/downloads/) nainstalovaná s úlohou **vývoje Univerzální platforma Windows** a součástí **sady Windows 10 SDK (10.0.18362.0 nebo novější)** .

## <a name="configuring-a-project"></a>Konfigurace projektu

Prostorové kotvy Azure pro HoloLens a C++/WinRT se distribuují pomocí balíčku NuGet [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) .

Podle [zde](/nuget/consume-packages/install-use-packages-visual-studio) uvedených pokynů použijte Správce balíčků NuGet sady Visual Studio k instalaci balíčku NuGet [Microsoft. Azure. SpatialAnchors. WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) do svého projektu.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Postupy: vytváření a hledání kotev v C++/WinRT](./create-locate-anchors-cpp-winrt.md)
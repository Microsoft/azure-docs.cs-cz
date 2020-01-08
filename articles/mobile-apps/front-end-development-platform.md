---
title: Volba front-endové vývojové platformy pro vytváření klientských aplikací pomocí sady Visual Studio a služeb Azure
description: Přečtěte si o podporovaných nativních jazycích a jazycích pro různé platformy, které vám umožní sestavovat klientské aplikace.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: c55c9c36d84c8545f4192e1c9a65d8af0c580396
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453158"
---
# <a name="choose-mobile-development-frameworks"></a>Volba rozhraní pro vývoj mobilních zařízení
Vývojáři můžou používat technologie na straně klienta k vytváření mobilních aplikací sami pomocí specifických rozhraní a vzorů pro přístup mezi platformami. Na základě svých rozhodovacích faktorů můžou vývojáři vytvářet:
- Nativní aplikace pro různé platformy s využitím jazyků, jako jsou objektivní C a Java
- Aplikace pro různé platformy pomocí Xamarin, .NET aC#
- Hybridní aplikace s využitím Cordova a jejích variant

## <a name="native-platforms"></a>Nativní platformy
Sestavování nativních aplikací vyžaduje programovací jazyky specifické pro konkrétní platformu, sady SDK, vývojové prostředí a další nástroje poskytované dodavateli operačního systému.

### <a name="ios"></a>iOS
Společnost Apple vytvořila a vyvinula iOS a používá se k sestavování aplikací na zařízeních Apple, a to pro iPhone a iPad.

- **Programovací jazyky**: cíl-C, SWIFT
- **Integrované vývojové prostředí (IDE**): Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
Android je navržený podle Google a nejoblíbenějšího operačního systému na světě, který se používá k vytváření aplikací, které se dají spouštět na různých smartphonech a tabletech.

- **Programovací jazyk**: Java, Kotlin 
- **IDE**: Android Studio a Android Developer Tools 
- **Sada SDK**: Android SDK

### <a name="windows"></a>Windows
- **Programovací jazyk**:C#
- **IDE**: Visual Studio, Visual Studio Code
- **Sada SDK**: Windows SDK

#### <a name="pros"></a>V oblasti IT
- Dobré uživatelské prostředí
- Reaguje na aplikace s vysokým výkonem a možností rozhraní s nativními knihovnami.
- Vysoce zabezpečené aplikace

#### <a name="cons"></a>Nevýhody
- Aplikace běží jenom na jedné platformě.
- Další prostředky pro vývoj a náročné vytváření aplikací
- Snížení opětovného použití kódu

## <a name="cross-platforms-and-hybrid-applications"></a>Různé platformy a hybridní aplikace
Aplikace pro různé platformy umožňují psát nativní mobilní aplikace jednou, sdílet kód a spouštět je v systémech iOS, Android a Windows.

### <a name="xamarin"></a>Xamarin
[Xamarin](https://visualstudio.microsoft.com/xamarin/) se ve vlastnictví Microsoftu používá k vytváření robustních mobilních aplikací pro různé platformy v C#. Xamarin má knihovnu tříd a modul runtime, který funguje na různých platformách, jako je iOS, Android a Windows. Také kompiluje nativní (neinterpretované) aplikace, které poskytují vysoký výkon. Xamarin kombinuje všechny možnosti nativních platforem a přináší vlastní řadu výkonných funkcí.

- **Programovací jazyk**:C#
- **IDE**: Visual Studio v systému Windows nebo Mac

### <a name="react-native"></a>React Native
[Reagující](https://facebook.github.io/react-native/) na facebooku v 2015, je [Open Source](https://github.com/facebook/react-native) rozhraní JavaScript pro psaní reálných a nativně vykreslovat mobilní aplikace pro iOS a Android. Je založen na reakci, knihovně JavaScript na Facebooku pro sestavování uživatelských rozhraní. Místo cílení na prohlížeč cílí na mobilní platformy. Nereagující nativní použití nativních součástí namísto webových součástí jako stavebních bloků.
 
- **Programovací jazyk**: JavaScript
- **IDE**: Visual Studio Code

### <a name="unity"></a>Unity
 Unity je modul optimalizovaný pro vytváření her. Můžete ji použít k vytváření vysoce kvalitních 2D nebo 3D aplikací s C# platformami, jako jsou Windows, iOS, Android a Xbox.

### <a name="cordova"></a>Cordova
Cordova umožňuje sestavovat hybridní aplikace pomocí Visual Studio Tools pro Apache Cordova nebo Visual Studio Code s rozšířeními pro Cordova. Pomocí hybridního přístupu můžete sdílet součásti s weby a opakovaně používat webové aplikace založené na webových serverech s přístupy k hostovaným webovým aplikacím, které jsou založeny na Cordova.

#### <a name="pros"></a>V oblasti IT
- Zvýšení použitelnosti kódu vytvořením jednoho základu kódu pro více platforem
- Stravování pro širší cílovou skupinu napříč mnoha platformami
- Výrazné snížení doby vývoje
- Snadné spuštění a aktualizace

#### <a name="cons"></a>Nevýhody
- Nižší výkon
- Nedostatek flexibility
- Každá platforma má unikátní sadu funkcí a funkcí, aby se nativní aplikace lépe Creative.
- Vyšší Doba návrhu uživatelského rozhraní
- Omezení nástroje

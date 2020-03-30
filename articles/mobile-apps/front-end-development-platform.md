---
title: Výběr front-endové vývojové platformy pro vytváření klientských aplikací pomocí Visual Studia a služeb Azure
description: Seznamte se s podporovanými nativními jazyky a jazyky napříč platformami pro vytváření klientských aplikací.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240864"
---
# <a name="choose-mobile-development-frameworks"></a>Vyberte si mobilní vývojové architektury
Vývojáři mohou používat technologie na straně klienta k vytváření mobilních aplikací sami pomocí konkrétní chod rozhraní a vzory pro přístup napříč platformami. Na základě svých rozhodovacích faktorů mohou vývojáři vytvářet:
- Nativní aplikace s jednou platformou pomocí jazyků, jako je cíl C a Java
- Aplikace napříč platformami pomocí Xamarinu, .NET a C #
- Hybridní aplikace pomocí Cordova a jejích variant

## <a name="native-platforms"></a>Nativní platformy
Vytváření nativní aplikace vyžaduje programovací jazyky specifické pro platformu, sady SDK, vývojová prostředí a další nástroje poskytované dodavateli operačního systému.

### <a name="ios"></a>iOS
Vytvořil a vyvinutý společností Apple, iOS se používá k vytváření aplikací na zařízeních Apple, a to iPhone a iPad.

- **Programovací jazyky**: Objective-C, Swift
- **IDE**: Xcode
- **Sada SDK**: iOS SDK

### <a name="android"></a>Android
Navržený společností Google a nejpopulárnější operační systém na světě, Android se používá k vytváření aplikací, které lze spustit na řadě smartphonů a tabletů.

- **Programovací jazyk**: Java, Kotlin 
- **IDE**: Android Studio a Android vývojářské nástroje 
- **Sada SDK**: Sada Android SDK

### <a name="windows"></a>Windows
- **Programovací jazyk**: C #
- **IDE**: Visual Studio, Visual Studio Kód
- **Sada SDK**: Sada Windows SDK

#### <a name="pros"></a>Výhody
- Dobrá uživatelská zkušenost
- Responzivní aplikace s vysokým výkonem a schopností komunikovat s nativními knihovnami
- Vysoce zabezpečené aplikace

#### <a name="cons"></a>Nevýhody
- Aplikace běží pouze na jedné platformě
- Vytvoření aplikace je náročnější na zdroje pro vývojáře a nákladné
- Nižší opětovné použití kódu

## <a name="cross-platforms-and-hybrid-applications"></a>Meziplatformy a hybridní aplikace
Aplikace napříč platformami vám dávají sílu psát nativní mobilní aplikace jednou, sdílet kód a spouštět je v systémech iOS, Android a Windows.

### <a name="xamarin"></a>Xamarin
Společnost [Xamarin,](https://visualstudio.microsoft.com/xamarin/) kterou vlastní společnost Microsoft, se používá k vytváření robustních mobilních aplikací pro různé platformy v systému C#. Xamarin má knihovnu tříd a runtime, který funguje na mnoha platformách, jako jsou iOS, Android a Windows. Také zkompiluje nativní (neinterpretované) aplikace, které poskytují vysoký výkon. Xamarin kombinuje všechny schopnosti nativních platforem a přidává řadu vlastních výkonných funkcí.

- **Programovací jazyk**: C #
- **IDE**: Visual Studio ve Windows nebo Macu

### <a name="react-native"></a>Reagovat nativní
Vydáno Facebook v 2015, [React Native](https://facebook.github.io/react-native/) je [open-source](https://github.com/facebook/react-native) JavaScript framework pro psaní skutečných, nativně vykreslování mobilních aplikací pro iOS a Android. Je založen na React, Facebook JavaScript knihovna pro budování uživatelských rozhraní. Místo cílení na prohlížeč se zaměřuje na mobilní platformy. React Native používá jako stavební bloky nativní komponenty místo webových komponent.
 
- **Programovací jazyk**: JavaScript
- **IDE**: Kód visual studia

### <a name="unity"></a>Unity
 Unity je motor optimalizovaný pro vytváření her. Můžete ji použít k vytváření vysoce kvalitních 2D nebo 3D aplikací s C# pro platformy, jako jsou Windows, iOS, Android a Xbox.

### <a name="cordova"></a>Cordova
Cordova umožňuje vytvářet hybridní aplikace pomocí visual studio nástroje pro Apache Cordova nebo Visual Studio kód s rozšířeními pro Cordova. S hybridním přístupem můžete sdílet komponenty s webovými stránkami a znovu používat webové serverové aplikace s hostovanými přístupy webových aplikací založenými na Cordově.

#### <a name="pros"></a>Výhody
- Zvýšena použitelnost kódu vytvořením jednoho základu kódu pro více platforem
- Uspokojte širší publikum na mnoha platformách
- Dramatické zkrácení doby vývoje
- Snadné spuštění a aktualizace

#### <a name="cons"></a>Nevýhody
- Nižší výkon
- Nedostatečná flexibilita
- Každá platforma má jedinečnou sadu funkcí a funkcí, aby se nativní aplikace více kreativní
- Zvýšená doba návrhu ui
- Omezení nástroje

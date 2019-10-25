---
title: Volba front-endové vývojové platformy pro vytváření klientských aplikací pomocí sady Visual Studio a služeb Azure
description: Přečtěte si o podporovaných nativních a různých jazykových platformách pro vytváření klientských aplikací.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 891967e74e52c1311d73cbe13fff8c95e479478c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795494"
---
# <a name="choose-mobile-development-framework"></a>Volba rozhraní pro vývoj mobilních zařízení
Vývojáři můžou používat technologie na straně klienta k vytváření mobilních aplikací, které používají konkrétní rozhraní a vzory pro přístup mezi platformami. Vývojáři můžou vytvářet nativní (nativní prostředí s různými platformami pomocí jazyků, jako jsou například aplikace typu C a Java), aplikací pro různé platformy pomocí Xamarin, C#.NET a) a hybridních (pomocí Cordova) a jejich variant v závislosti na jejich rozhodnutí. skutečnosti.

## <a name="native-platforms"></a>Nativní platformy
Sestavování nativních aplikací vyžaduje programovací jazyky specifické pro konkrétní platformu, sady SDK, vývojové prostředí a další nástroje poskytované dodavateli operačního systému.

### <a name="ios"></a>iOS
Vytvořené a vyvinuté společností Apple, aplikace založené na iOS běží na zařízeních Apple, jmenovitě iPhone a iPad.

- **Programovací jazyky** – cíl-C, SWIFT
- **IDE** – Xcode
- **Sada SDK** – sada SDK pro iOS

### <a name="android"></a>Android
Aplikace založené na Androidu, které jsou navržené pro Google a nejoblíbenějším operačním systémem na světě, můžou běžet na řadě smartphonů a tabletů.

- **Programovací jazyk** – Java, Kotlin 
- **IDE** – Android Studio a Android vývojářské nástroje 
- **Sada SDK** – Android SDK

### <a name="windows"></a>Windows
- **Programovací jazyk** –C#
- **IDE** – Visual Studio, Visual Studio Code
- **Sada SDK** – Windows SDK

#### <a name="pros"></a>IT
- Dobré uživatelské prostředí
- Reaguje na aplikace s vysokým výkonem a možností rozhraní s nativními knihovnami.
- Vysoce zabezpečené aplikace

#### <a name="cons"></a>Nevýhody
- Aplikace běží jenom na jedné platformě.
- Další prostředky pro vývojáře a nákladné sestavení aplikace
- Snížení opětovného použití kódu

## <a name="cross-platforms-and-hybrid-applications"></a>Různé platformy a hybridní aplikace
Aplikace pro různé platformy vám umožňují psát nativní mobilní aplikace jednou, sdílet kód a spouštět je v systémech iOS, Android a Windows.

### <a name="xamarin"></a>Xamarin
[Xamarin](https://visualstudio.microsoft.com/xamarin/) umožňuje vytvářet robustní mobilní aplikace pro víc platforem v C#, a to s využitím knihovny tříd a modulu runtime, který funguje napříč mnoha platformami, včetně iOS, Androidu a Windows, a přitom pořád kompiluje nativní (neinterpretované ) aplikace, které jsou vysoce výkonné. Xamarin kombinuje všechny možnosti nativních platforem a přináší vlastní řadu výkonných funkcí.

- **Programovací jazyk** –C#
- **IDE** – Visual Studio v systému Windows nebo Mac

### <a name="react-native"></a>React Native
[Reagující](https://facebook.github.io/react-native/) na facebooku v 2015, je [Open Source](https://github.com/facebook/react-native) rozhraní JavaScript pro psaní reálných, nativně vykreslovat mobilní aplikace pro iOS a Android. Je založena na reakci, knihovně JavaScript Facebooku pro sestavování uživatelských rozhraní, ale místo cílení na prohlížeč cílí na mobilní platformy. Nereagující nativní použití nativních součástí namísto webových součástí jako stavebních bloků.
 
- **Programovací jazyk** – JavaScript
- **IDE** – Visual Studio Code

### <a name="unity"></a>Unity
 Unity je modul optimalizovaný pro vytváření her, který vývojářům umožňuje vytvořit vysoce kvalitní 2D a 3D aplikace s C# různými platformami, včetně systémů Windows, iOS, Android a Xbox.

### <a name="cordova"></a>Cordova
Cordova umožňuje sestavovat hybridní aplikace pomocí Visual Studio Tools pro Apache Codova nebo Visual Studio Code s rozšířeními pro Cordova. S využitím hybridního přístupu můžete sdílet součásti s weby a využívat přístupy k hostovaným webovým aplikacím na základě Cordova.

#### <a name="pros"></a>IT
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

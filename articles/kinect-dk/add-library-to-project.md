---
title: Přidání knihovny Azure Kinect do projektu sady Visual Studio
description: Naučte se přidat balíček NuGet Azure Kinect do projektu sady Visual Studio.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, senzor, SDK, Visual Studio 2017, Visual Studio 2019, NuGet
ms.openlocfilehash: fd71f0d327b8c828cc9ddac5810757cccdffbcea
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359608"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Přidání knihovny Azure Kinect do projektu sady Visual Studio

Tento článek vás provede procesem přidání balíčku NuGet Azure Kinect do projektu sady Visual Studio.

## <a name="install-azure-kinect-nuget-package"></a>Nainstalovat balíček NuGet Azure Kinect

Postup instalace balíčku NuGet pro Azure Kinect:

1. Podrobné pokyny pro instalaci balíčku NuGet v aplikaci Visual Studio najdete v tématu [rychlý Start: instalace a použití balíčku v aplikaci Visual Studio](/nuget/quickstart/install-and-use-a-package-in-visual-studio).
2. Chcete-li přidat balíček, můžete použít uživatelské rozhraní Správce balíčků kliknutím pravým tlačítkem myši na odkazy a výběrem možnosti spravovat balíčky NuGet z Průzkumník řešení.
3. Jako zdroj balíčku zvolte [NuGet.org](https://www.nuget.org) , vyberte Procházet kartu a vyhledejte `Microsoft.Azure.Kinect.Sensor` .
4. Vyberte tento balíček ze seznamu a nainstalujte.

## <a name="use-azure-kinect-nuget-package"></a>Použití balíčku NuGet pro Azure Kinect

Po přidání balíčku přidá hlavičkový soubor do zdrojového kódu, například:

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>[Teď jste připraveni sestavit svou první aplikaci.](build-first-app.md)
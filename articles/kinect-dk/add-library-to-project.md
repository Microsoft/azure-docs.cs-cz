---
title: Přidání knihovny Azure Kinect do projektu sady Visual Studio
description: Naučte se přidat balíček NuGet Azure Kinect do projektu sady Visual Studio.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, Azure, senzor, SDK, Visual Studio 2017, Visual Studio 2019, NuGet
ms.openlocfilehash: b0395118481cbaecd5ad0b6a3a6b3e89cc29dfaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277162"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Přidání knihovny Azure Kinect do projektu sady Visual Studio

Tento článek vás provede procesem přidání balíčku NuGet Azure Kinect do projektu sady Visual Studio.

## <a name="install-azure-kinect-nuget-package"></a>Nainstalovat balíček NuGet Azure Kinect

Postup instalace balíčku NuGet pro Azure Kinect:

1. Podrobné pokyny pro instalaci balíčku NuGet v aplikaci Visual Studio najdete v tématu [rychlý Start: instalace a použití balíčku v aplikaci Visual Studio](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio).
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

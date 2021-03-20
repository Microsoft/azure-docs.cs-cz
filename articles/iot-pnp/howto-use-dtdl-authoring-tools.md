---
title: Použití nástroje k vytváření a ověřování modelů DTDL | Microsoft Docs
description: Nainstalujte Editor DTDL pro Visual Studio Code nebo Visual Studio 2019 a použijte ho k vytváření modelů IoT technologie Plug and Play.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 40d1ae4da07e159c24970c065d1c39e22b89a29a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93280198"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>Instalace a použití nástrojů pro tvorbu DTDL

[Digitální DTDL modely definice jazyka ()](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) jsou soubory JSON. Můžete použít rozšíření pro Visual Studio Code nebo Visual Studio 2019 k vytváření a ověřování těchto souborů modelu.

## <a name="install-and-use-the-vs-code-extension"></a>Instalace a použití rozšíření VS Code

Rozšíření DTDL pro VS Code přidává tyto funkce DTDL pro vytváření obsahu:

- Ověřování syntaxe DTDL v2.
- IntelliSense, včetně automatického dokončování, které vám pomůžou s syntaxí jazyka.
- Možnost vytvářet rozhraní z palety příkazů.

Pokud chcete nainstalovat rozšíření DTDL, [vyhledejte Visual Studio Code v editoru DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl). **DTDL** můžete vyhledat také v zobrazení rozšíření v vs Code.

Když jste nainstalovali rozšíření, použijte ho pro pomoc s vytvářením souborů DTDL modelů v VS Code:

- Rozšíření poskytuje ověřování syntaxe v souborech modelů DTDL a zvýrazňuje chyby, jak je znázorněno na následujícím snímku obrazovky:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="Ověřování modelu v VS Code":::

- Použití IntelliSense a automatického dokončování při úpravách DTDL modelů:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="Použití IntelliSense pro modely DTDL v VS Code":::

- Vytvořte nové rozhraní DTDL. Příkaz **DTDL: Create Interface** vytvoří soubor JSON s novým rozhraním. Rozhraní obsahuje ukázkovou telemetrii, vlastnost a definice příkazů.

## <a name="install-and-use-the-visual-studio-extension"></a>Instalace a používání rozšíření sady Visual Studio

Rozšíření DTDL pro Visual Studio 2019 přidává následující funkce pro vytváření obsahu DTDL:

- Ověřování syntaxe DTDL v2.
- IntelliSense, včetně automatického dokončování, které vám pomůžou s syntaxí jazyka.

Rozšíření DTDL nainstalujete tak, že přejdete na [podporu jazyka DTDL pro VS 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport). Můžete také vyhledat **DTDL** v nástroji **Správa rozšíření** v aplikaci Visual Studio.

Když jste nainstalovali rozšíření, použijte ho pro pomoc s vytvářením souborů DTDL modelů v aplikaci Visual Studio:

- Rozšíření poskytuje ověřování syntaxe v souborech modelů DTDL a zvýrazňuje chyby, jak je znázorněno na následujícím snímku obrazovky:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Ověřování modelu v aplikaci Visual Studio":::

- Použití IntelliSense a automatického dokončování při úpravách DTDL modelů:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Použití IntelliSense pro modely DTDL v aplikaci Visual Studio":::

## <a name="next-steps"></a>Další kroky

V tomto článku se naučíte, jak používat rozšíření DTDL pro Visual Studio Code a Visual Studio 2019 k vytváření a ověřování souborů modelů DTDL. Navržený další krok se naučíte používat [Azure IoT Explorer se svými modely a zařízeními](./howto-use-iot-explorer.md).

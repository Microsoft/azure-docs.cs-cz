---
title: zahrnout soubor
description: zahrnout soubor
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 01/28/2020
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 2eb82eb8b4a64cbbfef91539f46ba084aa73f207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77205736"
---
Přidejte podporu ve vámi upřednostňovaném vývojovém prostředí pomocí následujících metod.

| Vývojové prostředí  | Typ aplikace      | Přidání podpory |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Knihovna tříd C#      | [Instalace balíčku NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Založeno na [základních nástrojích](../articles/azure-functions/functions-run-local.md) | [Registrace rozšíření balíčku](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Doporučujeme nainstalovat [rozšíření Nástroje Azure.](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) |
| Jakýkoli jiný editor/IDE     | Založeno na [základních nástrojích](../articles/azure-functions/functions-run-local.md) | [Registrace rozšíření balíčku](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure Portal             | Online pouze na portálu | Nainstaluje se při přidávání vazby<br /><br /> Viz [Aktualizace rozšíření](../articles/azure-functions/install-update-binding-extensions-manual.md) aktualizovat existující rozšíření vazby bez nutnosti znovu publikovat aplikace funkce. |

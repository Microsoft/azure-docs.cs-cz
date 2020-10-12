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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77205736"
---
Přidejte podporu do preferovaného vývojového prostředí pomocí následujících metod.

| Vývojové prostředí  | Typ aplikace      | Přidání podpory |
|--------------------------|-----------------------|----------------|
| Visual Studio            | Knihovna tříd C#      | [Instalace balíčku NuGet](../articles/azure-functions/functions-bindings-register.md#vs) |
| Visual Studio Code       | Založený na [základních nástrojích](../articles/azure-functions/functions-run-local.md) | [Registrace balíčku rozšíření](../articles/azure-functions/functions-bindings-register.md#extension-bundles)<br><br>Doporučuje se nainstalovat [rozšíření Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) . |
| Jakýkoli jiný Editor nebo IDE     | Založený na [základních nástrojích](../articles/azure-functions/functions-run-local.md) | [Registrace balíčku rozšíření](../articles/azure-functions/functions-bindings-register.md#extension-bundles) |
| Azure Portal             | Jenom online na portálu | Instaluje se při přidávání vazby.<br /><br /> Pokud chcete aktualizovat existující rozšíření vazby bez nutnosti opětovného publikování aplikace Function App, přečtěte si téma [aktualizace rozšíření](../articles/azure-functions/install-update-binding-extensions-manual.md) . |

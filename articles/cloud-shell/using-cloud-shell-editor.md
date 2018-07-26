---
title: Pomocí editoru Azure Cloud Shell | Dokumentace Microsoftu
description: Přehled o tom, jak použít Azure Cloud Shell editor.
services: azure
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: juluk
ms.openlocfilehash: caf6e18a9a30654710f5445ed6ab957a5253d62e
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259807"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Pomocí editoru Azure Cloud Shell

Azure Cloud Shell zahrnuje integrované souboru v editoru kódu postaveném z open-source [editoru Monaco](https://github.com/Microsoft/monaco-editor). Cloud Shell editor podporuje funkce, jako je zvýrazňování jazyka, paletu příkazů a Průzkumníka souborů.

![Cloud Shell editoru](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Otevření editoru

Pro vytvoření jednoduchého souboru a úpravy, spuštění editoru spuštěním `code .` v Terminálové službě Cloud Shell. Tato akce otevře v editoru pomocí služby active directory pracovní, v terminálu.

Chcete-li otevřít přímo soubor pro rychlé úpravy, spusťte `code <filename>` otevřete editor bez Průzkumníka souborů.

Chcete-li otevřít editor prostřednictvím tlačítka uživatelského rozhraní, klikněte na tlačítko `{}` editor ikon na panelu nástrojů. Tím otevřete editor a Průzkumník souborů na výchozí `/home/<user>` adresáře.

## <a name="closing-the-editor"></a>Editor zavřít

Zavřete editor, otevřete `...` panelu akcí v horní části přímo z editoru a vyberte `Close editor`.

![Zavřít editor](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Paleta příkazů

Chcete-li spustit paletu příkazů, použijte `F1` klávesy, když je fokus nastavený na editoru. Otevřete paletu příkazů je možné provést prostřednictvím panelu akcí.

![Cmd palety](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Přispívání do editoru Monaco

Podpora jazyků zvýraznění v editoru Cloud Shell je podporované prostřednictvím nadřazeného funkce v [editoru Monaco](https://github.com/Microsoft/monaco-editor)využití Monarch syntaxe definice. Zjistěte, jak přispívat, přečtěte si [Průvodce pro přispěvatele Monako](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Další postup
[Vyzkoušejte si rychlý start pro Bash ve službě Cloud Shell](quickstart.md)
[zobrazit úplný seznam integrovaných nástrojů Cloud Shellu](features.md)
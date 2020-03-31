---
title: Použití editoru Cloud Shell azure | Dokumenty společnosti Microsoft
description: Přehled toho, jak používat editor Azure Cloud Shell.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 7f597bb5cba1a12bdb93325fe2b877ffc644e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60199185"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Použití editoru Cloud Shell azure

Azure Cloud Shell obsahuje integrovaný editor souborů vytvořený z open source [Editoru Monaka](https://github.com/Microsoft/monaco-editor). Editor Cloud Shellu podporuje funkce, jako je například zvýraznění jazyka, paleta příkazů a průzkumník souborů.

![Editor cloudového prostředí](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Otevření editoru

Pro jednoduché vytvoření a úpravu souboru spusťte editor spuštěním `code .` v terminálu Cloud Shellu. Tato akce otevře editor s aktivním pracovním adresářem nastaveným v terminálu.

Pokud chcete přímo otevřít soubor pro rychlé úpravy, spusťte `code <filename>` a otevřete editor bez průzkumníka souborů.

Pokud chcete editor otevřít pomocí tlačítka uživatelského rozhraní, klikněte na ikonu editoru `{}` na panelu nástrojů. Otevře se editor a průzkumník souborů se nastaví na adresář `/home/<user>`.

## <a name="closing-the-editor"></a>Zavření editoru

Chcete-li editor zavřít, otevřete `...` panel akcí v `Close editor`pravém horním rohu editoru a vyberte .

![Zavřít editor](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Paleta příkazů

Chcete-li spustit paletu `F1` příkazů, použijte klávesu při nastavení fokusu v editoru. Otevření palety příkazů lze provést také prostřednictvím panelu akcí.

![Cmd paleta](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Přispívání do Monaka Editor

Podpora zvýraznění jazyka v editoru Cloud Shell je podporována prostřednictvím upstream funkce v [Editor u Monaka](https://github.com/Microsoft/monaco-editor)'s použití definice syntaxe Monarch. Chcete-li se dozvědět, jak přispívat, přečtěte si [průvodce přispěvatelem Monaka](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Další kroky
[Vyzkoušejte rychlý start pro Bash v Cloud ShellU](quickstart.md)
[Prohlédněte si úplný seznam integrovaných nástrojů Cloud Shellu](features.md)
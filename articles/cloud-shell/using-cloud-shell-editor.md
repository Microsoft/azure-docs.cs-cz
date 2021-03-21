---
title: Použití editoru Azure Cloud Shell | Microsoft Docs
description: Přehled způsobu použití editoru Azure Cloud Shell.
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
ms.openlocfilehash: 1e3ea9222b0f231250bde43fb86c07847ca4835e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832331"
---
# <a name="using-the-azure-cloud-shell-editor"></a>Použití editoru Azure Cloud Shell

Azure Cloud Shell obsahuje integrovaný editor souborů sestavený z open source [editoru Monako](https://github.com/Microsoft/monaco-editor). Editor Cloud Shellu podporuje funkce, jako je například zvýraznění jazyka, paleta příkazů a průzkumník souborů.

![Editor Cloud Shell](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>Otevírání editoru

Pro jednoduché vytvoření a úpravu souboru spusťte editor spuštěním `code .` v terminálu Cloud Shellu. Tato akce otevře editor s aktivním pracovním adresářem nastaveným v terminálu.

Pokud chcete přímo otevřít soubor pro rychlé úpravy, spusťte `code <filename>` a otevřete editor bez průzkumníka souborů.

Pokud chcete editor otevřít pomocí tlačítka uživatelského rozhraní, klikněte na ikonu editoru `{}` na panelu nástrojů. Otevře se editor a průzkumník souborů se nastaví na adresář `/home/<user>`.

## <a name="closing-the-editor"></a>Zavření editoru

Chcete-li Editor zavřít, otevřete `...` panel akce v pravém horním rohu editoru a vyberte `Close editor` .

![Zavřít editor](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Paleta příkazů

Chcete-li spustit paletu příkazů, použijte `F1` klíč při nastavení fokusu v editoru. Otevření palety příkazů lze provést také prostřednictvím panelu akcí.

![Paleta cmd](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Přispívání do editoru Monako

Podpora zvýraznění jazyka v editoru Cloud Shell je podporovaná prostřednictvím funkcí pro odesílání dat v [editoru Monako](https://github.com/Microsoft/monaco-editor)použití definic syntaxe Monarch. Informace o tom, jak vytvořit příspěvky, najdete v [příručce pro přispěvatele](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md)pro spolupráci.

## <a name="next-steps"></a>Další kroky

- [Vyzkoušejte si rychlý Start pro bash v Cloud Shell](quickstart.md)
- [Zobrazit úplný seznam integrovaných nástrojů Cloud Shell](features.md)

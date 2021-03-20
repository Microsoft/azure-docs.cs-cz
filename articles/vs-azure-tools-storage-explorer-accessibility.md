---
title: Přístupnost Průzkumník služby Azure Storage | Microsoft Docs
description: Pochopení přístupnosti v Průzkumník služby Azure Storage. Zkontrolujte, jaké čtečky obrazovky jsou k dispozici, možnosti přiblížení, motivy s vysokým kontrastem a klávesové zkratky.
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: ca4a8d719277eaa1d853d53d282649f839256be9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88035481"
---
# <a name="storage-explorer-accessibility"></a>Přístupnost Průzkumníka služby Storage

## <a name="screen-readers"></a>Čtečky obrazovky

Průzkumník služby Storage podporuje použití čtečky obrazovky v systémech Windows a Mac. Pro každou platformu se doporučují následující čtečky obrazovky:

Platforma | Čtečka obrazovky
---------|--------------
Windows  | NVDA
Mac      | Voice Over
Linux    | (čtečky obrazovky se v systému Linux nepodporují.)

Pokud při použití Průzkumník služby Storage narazíte na problém s přístupností, [otevřete prosím problém na GitHubu](https://github.com/Microsoft/AzureStorageExplorer/issues).

## <a name="zoom"></a>Zoom

Text v Průzkumník služby Storage může být zvětšený pomocí přiblížení. Přiblížení zobrazíte kliknutím na tlačítko **přiblížit** v nabídce Help (Lupa). K zmenšení a obnovení úrovně přiblížení zpět na výchozí úroveň můžete použít také nabídku Help.

![Možnosti přiblížení v nabídce Help][0]

Nastavení přiblížení zvyšuje velikost většiny prvků uživatelského rozhraní. Doporučuje se také povolit nastavení velkých textů a přiblížení pro váš operační systém, aby se zajistilo správné škálování všech prvků uživatelského rozhraní.

## <a name="high-contrast-themes"></a>Vysoký kontrast motivů

Průzkumník služby Storage má dva motivy s vysokým kontrastem, **vysoký kontrast světlo** a **vysoký kontrast tmavé**. Svůj motiv můžete změnit tak, že v nabídce motivy > Help vyberete v.

![Dílčí nabídka motivů][1]

Nastavení motivu mění barvu většiny prvků uživatelského rozhraní. Doporučuje se také povolit motiv odpovídající vašemu operačnímu systému, aby bylo zajištěno, že všechny prvky uživatelského rozhraní jsou správně barevné.

## <a name="shortcut-keys"></a>Klávesové zkratky

### <a name="window-commands"></a>Příkazy okna

Příkaz       | Klávesová zkratka
--------------|--------------------
Nové okno    | **Ctrl + Shift + N**
Zavřít editor  | **Ctrl + F4**
Ukončit          | **CTRL + SHIFT + W**

### <a name="navigation-commands"></a>Navigační příkazy

Příkaz                | Klávesová zkratka
-----------------------|----------------------
Další panel – fokus       | **F6**
Fokus na předchozí panel   | **Shift+F6**
Průzkumník               | **CTRL + SHIFT + E**
Správa účtů     | **Ctrl + Shift + A**
Přepnout postranní panel        | **CTRL + B**
Protokol aktivit           | **CTRL + SHIFT + L**
Akce a vlastnosti | **CTRL + SHIFT + P**
Aktuální editor         | **Ovládací prvek + domů**
Další Editor            | **Ctrl + PageDown**
Předchozí Editor        | **Ctrl + Page Up**

### <a name="zoom-commands"></a>Příkazy lupy

Příkaz  | Klávesová zkratka
---------|------------------
Přiblížit  | **CTRL + =**
Oddálit | **Ovládací prvek + –**

### <a name="blob-and-file-share-editor-commands"></a>Příkazy editoru objektů BLOB a sdílení souborů

Příkaz | Klávesová zkratka
--------|--------------------
Zpět    | **Alt + šipka doleva**
Forward | **Alt + šipka doprava**
Nahoru      | **Alt + šipka nahoru**

### <a name="editor-commands"></a>Příkazy editoru

Příkaz | Klávesová zkratka
--------|------------------
Kopírovat    | **CTRL + C**
Vyjmout     | **CTRL + X**
Vložit   | **Control + V**
Aktualizovat  | **CTRL + R**

### <a name="other-commands"></a>Další příkazy

Příkaz                | Klávesová zkratka
-----------------------|------------------
Přepnout Vývojářské nástroje | **F12**
Načíst znovu                 | **Alt + Ctrl + R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png

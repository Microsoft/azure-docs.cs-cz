---
title: Používání okna prostředí cloudu Azure | Microsoft Docs
description: Přehled o tom, jak použít okno prostředí cloudu Azure.
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
ms.date: 01/30/2018
ms.author: juluk
ms.openlocfilehash: 43da2bf5b66ff7db03a6fb5c2e1ceaebe322bcbb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919990"
---
# <a name="using-the-azure-cloud-shell-window"></a>Používání okna prostředí cloudu Azure

Tento dokument vysvětluje, jak použít okno cloudové prostředí.

## <a name="swap-between-bash-and-powershell-environments"></a>Swap – mezi prostředí Bash a prostředí PowerShell
![](media/using-the-shell-window/env-selector.png)

Pomocí modulu pro výběr prostředí na panelu nástrojů v prostředí cloudu se Prohodit mezi prostředí Bash a prostředí PowerShell.

## <a name="restart-cloud-shell"></a>Restartovat Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Restartování prostředí cloudu obnoví stav počítače a všechny soubory ve vašem Azure není trvalý sdílené složky budou ztraceny.

* Klikněte na ikonu restartování na panelu nástrojů cloudové prostředí. Chcete-li obnovit stav počítače.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimalizovat & maximalizace okna cloudové prostředí
![](media/using-the-shell-window/minmax.png)
* Klikněte na ikonu Minimalizovat nahoře napravo od okna ho chcete skrýt. Klikněte na ikonu cloudové prostředí znovu na Zobrazit skryté.
* Klikněte na ikonu Maximalizovat nastavit okno na maximální výšku. Pokud chcete obnovit původní velikost okna, kliknutím na tlačítko Obnovit.

## <a name="concurrent-sessions"></a>Souběžných relací
Cloudové prostředí umožňuje víc souběžných relací mezi záložkách prohlížeče tím, že každou relaci existovat jako samostatný proces Bash.
Pokud ukončení relace, je nutné ukončit každé relaci okno každý proces běží nezávisle i když běží na stejném počítači.

## <a name="copy-and-paste"></a>Kopírování a vkládání
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Změnit velikost okna cloudové prostředí
* Klikněte na tlačítko a přetáhněte ji na horní okraj panelu nástrojů nahoru nebo dolů změňte velikost okna cloudové prostředí.

## <a name="scrolling-text-display"></a>Posouvání zobrazení textu
* Posouvání pomocí myši nebo dotykové přesunout terminálu text.

## <a name="changing-the-text-size"></a>Změna velikost textu
![](media/using-the-shell-window/text-size.png)
* Klikněte na ikonu nastavení nahoře vlevo okna, potom najeďte myší na možnost "Velikost textu" a vyberte velikost vašeho požadovaný text. Výběr bude napříč relacemi trvalé.

## <a name="exit-command"></a>Příkaz exit
Spuštění `exit` ukončí aktivní relace. K tomuto chování dochází ve výchozím nastavení po 20 minutách bez zásahu.

## <a name="next-steps"></a>Další postup

[V cloudu Quickstart prostředí bash](quickstart.md)
[prostředí PowerShell v prostředí cloudu rychlý start](quickstart-powershell.md)
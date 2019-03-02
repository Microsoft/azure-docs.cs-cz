---
title: Pomocí okna Azure Cloud Shell | Dokumentace Microsoftu
description: Přehled o tom, jak použít okno Azure Cloud Shell.
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
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: a02642540e6eb39f35b9cc0d38d187a7afa36b7a
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2019
ms.locfileid: "57243444"
---
# <a name="using-the-azure-cloud-shell-window"></a>Pomocí okna Azure Cloud Shell

Tento dokument popisuje způsob použití okna služby Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Mezi prostředí Bash a PowerShell
![](media/using-the-shell-window/env-selector.png)

Pomocí selektoru prostředí na panelu nástrojů Cloud Shellu mezi prostředí Bash a PowerShell.

## <a name="restart-cloud-shell"></a>Restartovat Cloud Shell
![](media/using-the-shell-window/restart.png)
> [!WARNING]
> Restartovat Cloud Shell se resetuje stav počítače a všechny soubory není určen na základě Azure sdílenou složku se ztratí.

* Klikněte na ikonu restartování v panelu nástrojů Cloud Shellu můžete resetovat stav počítače.

## <a name="minimize--maximize-cloud-shell-window"></a>Minimalizovat & maximalizovat okna služby Cloud Shell
![](media/using-the-shell-window/minmax.png)
* Klikněte na ikonu Minimalizovat v horním pravém rohu okna tak jej skrýt. Klepnutím na ikonu Cloud Shell znovu zobrazit.
* Klikněte na ikonu Maximalizovat nastavujete časové období maximální výšku. Chcete-li obnovit předchozí velikost okna, klikněte na obnovení.

## <a name="concurrent-sessions"></a>Souběžných relací
Cloud Shell umožňuje víc souběžných relací mezi záložkách prohlížeče tím, že každá relace existuje jako samostatný proces prostředí Bash.
Pokud se ukončení relace, je nutné ukončit každé okno relace, každý proces běží nezávisle na sobě, i když jsou spuštěné na stejném počítači.

## <a name="copy-and-paste"></a>Kopírování a vkládání
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Změňte velikost okna služby Cloud Shell
* Klikněte na tlačítko a přetáhněte ji na horní okraj panelu nástrojů nahoru nebo dolů změny velikosti okna služby Cloud Shell.

## <a name="scrolling-text-display"></a>Posouvání zobrazení textu
* Posouvání pomocí myši nebo touchpadu přesunout terminálu text.

## <a name="changing-the-text-size"></a>Změna velikosti textu
![](media/using-the-shell-window/text-size.png)
* Kliknutím na ikonu nastavení v horní levé části okna, pak najeďte myší možnost "Velikost textu" a vyberte velikost vašeho požadovaný text. Váš výběr se nastavit jako trvalý napříč relacemi.

## <a name="exit-command"></a>Příkaz exit
Spuštění `exit` ukončí aktivní relace. K tomuto chování dochází ve výchozím nastavení po 20 minutách bez zásahu.

## <a name="next-steps"></a>Další postup

[Bash v Cloud Shellu Quickstart](quickstart.md)
[prostředí PowerShell ve službě Cloud Shell Quickstart](quickstart-powershell.md)
---
title: Použití okna Azure Cloud Shell | Microsoft Docs
description: Přehled způsobu použití okna Azure Cloud Shell.
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
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 3435d958049012750e775e2a17f43f463a0cc358
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "70860319"
---
# <a name="using-the-azure-cloud-shell-window"></a>Použití Azure Cloud Shellho okna

Tento dokument vysvětluje, jak používat okno Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Prohození mezi prostředími bash a PowerShellem

K prohození mezi prostředími bash a prostředí PowerShell použijte selektor prostředí na panelu nástrojů Cloud Shell.  
![Výběr prostředí](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Restartovat Cloud Shell
Klikněte na ikonu restartovat na panelu nástrojů Cloud Shell a obnovte stav počítače.  
![Restartovat Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Restartováním Cloud Shell se resetuje stav počítače a všechny soubory, které vaše sdílená složka Azure neuchovává, se ztratí.

## <a name="change-the-text-size"></a>Změna velikosti textu
Klikněte na ikonu nastavení v levém horním rohu okna, pak najeďte myší na možnost velikost textu a vyberte požadovanou velikost textu. Váš výběr bude trvalý napříč relacemi.
![Velikost textu](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Změna písma
Klikněte na ikonu nastavení v levém horním rohu okna, pak najeďte myší na možnost Font (písmo) a vyberte požadované písmo.  Váš výběr bude trvalý napříč relacemi.
![Písmo](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Nahrávání a stahování souborů
Klikněte na ikonu nahrát/stáhnout soubory v levém horním rohu okna a pak vyberte nahrát nebo stáhnout.  
![Nahrávání a stahování souborů](media/using-the-shell-window/uploaddownload.png)
* Pro nahrání souborů použijte místní okno k vyhledání souboru na místním počítači, vyberte požadovaný soubor a klikněte na tlačítko otevřít.  Soubor se nahraje do `/home/user` adresáře.
* Pro stahování souboru zadejte plně kvalifikovanou cestu k souboru do automaticky otevíraného okna (tj. v podstatě cestu v `/home/user` adresáři, který se ve výchozím nastavení zobrazuje), a klikněte na tlačítko Stáhnout.  
> [!NOTE] 
> Cesty souborů a souborů rozlišují velká a malá písmena v Cloud Shell. V cestě k souboru poklikejte na velká písmena.

## <a name="open-another-cloud-shell-window"></a>Otevřít další Cloud Shell okno
Cloud Shell umožňuje více souběžných relací napříč kartami prohlížeče, což umožňuje, aby každá relace existovala jako samostatný proces.
Při ukončení relace nezapomeňte ukončit z každého okna relace, protože každý proces běží nezávisle, i když běží na stejném počítači.  
Klikněte na ikonu otevřít novou relaci v levém horním rohu okna. Otevře se nová karta s jinou relací připojenou ke stávajícímu kontejneru.
![Otevřít novou relaci](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Editor Cloud Shell
* Informace najdete na stránce s [editorem Azure Cloud Shell](using-cloud-shell-editor.md) .

## <a name="web-preview"></a>Náhled webu
Klikněte na ikonu náhled webu v levém horním rohu okna, vyberte možnost konfigurovat a zadejte požadovaný port, který chcete otevřít.  Vyberte možnost "otevřít port", chcete-li otevřít pouze port, nebo "otevřít a procházet" pro otevření portu a zobrazení náhledu portu na nové kartě.  
![Náhled webu](media/using-the-shell-window/preview.png)  
<br>
![Konfigurace portu](media/using-the-shell-window/preview-configure.png)  
Klikněte na ikonu náhled webu v levém horním rohu okna a vyberte možnost port náhledu... Zobrazí náhled otevřeného portu na nové kartě. Klikněte na ikonu náhled webu v levém horním rohu okna a vyberte Zavřít port... pro zavření otevřeného portu.  
![Zobrazit/zavřít port](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimalizace & maximalizuje Cloud Shell okno
Kliknutím na ikonu minimalizovat v pravém horním rohu okna ji skryjete. Znovu klikněte na ikonu Cloud Shell a zobrazte ji znovu.
Kliknutím na ikonu maximalizovat nastavíte maximální výšku okna. Chcete-li obnovit předchozí velikost okna, klikněte na tlačítko obnovit.  
![Minimalizace nebo maximalizace okna](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopírování a vkládání
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Změnit velikost okna Cloud Shell
Chcete-li změnit velikost okna Cloud Shell, klikněte a přetáhněte horní okraj panelu nástrojů nahoru nebo dolů.

## <a name="scrolling-text-display"></a>Zobrazení posouvaného textu
Posuňte se myší nebo touchpadem, abyste přesunuli text terminálu.

## <a name="exit-command"></a>Exit – příkaz
Spuštění `exit` ukončí aktivní relaci. K tomuto chování dochází ve výchozím nastavení po 20 minutách bez interakce.

## <a name="next-steps"></a>Další kroky

[Bash v rychlém startu Cloud Shell](quickstart.md) <br>
[PowerShell v Cloud Shell rychlý Start](quickstart-powershell.md)

---
title: Použití okna Prostředí Azure cloud | Dokumenty společnosti Microsoft
description: Přehled použití okna Azure Cloud Shell.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70860319"
---
# <a name="using-the-azure-cloud-shell-window"></a>Použití okna Cloud Shell Azure

Tento dokument vysvětluje, jak používat okno Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Přepnutí mezi prostředími Bash a PowerShell

Pomocí voliče prostředí na panelu nástrojů prostředí Cloud Shell můžete přepínat mezi prostředími Bash a PowerShell.  
![Výběr prostředí](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Restartovat prostředí cloudu
Chcete-li obnovit stav počítače, klepněte na ikonu restartu na panelu nástrojů prostředí Cloud Shell.  
![Restartovat prostředí cloudu](media/using-the-shell-window/restart.png)
> [!WARNING]
> Restartování cloudového prostředí obnoví stav počítače a všechny soubory, které nejsou trvalé ve sdílené složce Azure, budou ztraceny.

## <a name="change-the-text-size"></a>Změna velikosti textu
Klikněte na ikonu nastavení v levém horním rohu okna, najeďte na položku Velikost textu a vyberte požadovanou velikost textu. Váš výběr bude zachován v rámci relací.
![Velikost textu](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Změna písma
Klikněte na ikonu nastavení v levém horním rohu okna, najeďte na položku "Písmo" a vyberte požadované písmo.  Váš výběr bude zachován v rámci relací.
![Písmo](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Ukládání a stahování souborů
Klikněte na ikonu nahrát nebo stáhnout soubory v levém horním rohu okna a pak vyberte nahrát nebo stáhnout.  
![Nahrávání a stahování souborů](media/using-the-shell-window/uploaddownload.png)
* Chcete-li nahrávat soubory, vyhledejte pomocí automaticky otevíraného tlačítka soubor v místním počítači, vyberte požadovaný soubor a klepněte na tlačítko Otevřít.  Soubor bude odeslán do `/home/user` adresáře.
* Pro stahování souboru zadejte plně kvalifikovanou cestu k souboru do vyskakovacího okna (tj. v podstatě cestu pod adresářem, `/home/user` který se ve výchozím nastavení zobrazí) a vyberte tlačítko "Stáhnout".  
> [!NOTE] 
> Soubory a cesty k souborům se v prostředí Cloud Shell rozlišují malá a velká písmena. Zkontrolujte velikost písmen v cestě k souboru.

## <a name="open-another-cloud-shell-window"></a>Otevření jiného okna cloudového prostředí
Prostředí Cloud Shell umožňuje více souběžných relací na kartách prohlížeče tím, že umožňuje, aby každá relace existovala jako samostatný proces.
Pokud ukončíte relaci, nezapomeňte ukončit z každého okna relace, protože každý proces běží nezávisle, i když jsou spuštěny ve stejném počítači.  
Klikněte na ikonu otevřít novou relaci v levém horním rohu okna. Otevře se nová karta s jinou relací připojenou k existujícímu kontejneru.
![Otevřít novou relaci](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Editor cloudového prostředí
* Podívejte se na [stránku editoru Azure Cloud Shell.](using-cloud-shell-editor.md)

## <a name="web-preview"></a>Webový náhled
Klikněte na ikonu webového náhledu v levém horním rohu okna, vyberte "Konfigurovat", zadejte požadovaný port, který chcete otevřít.  Výběrem možnosti "Otevřít port" otevřete pouze port, nebo "Otevřít a procházet" otevřete port a zobrazíte náhled portu na nové kartě.  
![Webový náhled](media/using-the-shell-window/preview.png)  
<br>
![Konfigurace portu](media/using-the-shell-window/preview-configure.png)  
Klikněte na ikonu náhledu webu v levém horním rohu okna a vyberte "Náhled portu ..." chcete-li zobrazit náhled otevřeného portu na nové kartě. zavřete otevřený port.  
![Náhled/zavření portu](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimalizace & maximalizace okna Cloud Shell
Kliknutím na ikonu minimalizovat v pravém horním rohu okna ji skryjte. Chcete-li zobrazit, klepněte znovu na ikonu prostředí Cloud Shell.
Chcete-li nastavit maximální výšku okna, klepněte na ikonu maximalizace. Chcete-li obnovit předchozí velikost okna, klepněte na tlačítko Obnovit.  
![Minimalizace nebo maximalizace okna](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopírování a vkládání
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Změna velikosti okna cloudového prostředí
Kliknutím a přetažením horního okraje panelu nástrojů nahoru nebo dolů změňte velikost okna Prostředí Cloud.

## <a name="scrolling-text-display"></a>Zobrazení posouvání textu
Posouváním myši nebo touchpadu můžete přesunout text terminálu.

## <a name="exit-command"></a>Ukončit, příkaz
Spuštění `exit` ukončí aktivní relaci. K tomuto chování dochází ve výchozím nastavení po 20 minutách bez interakce.

## <a name="next-steps"></a>Další kroky

[Bash v cloudovém prostředí Rychlý start](quickstart.md) <br>
[PowerShell v cloudovém prostředí Úvodní příručka](quickstart-powershell.md)

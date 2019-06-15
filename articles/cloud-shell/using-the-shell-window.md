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
ms.date: 04/15/2019
ms.author: damaerte
ms.openlocfilehash: 2511f2c8fb706e232cde9ee4c02c7f8114bd3a2b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60200608"
---
# <a name="using-the-azure-cloud-shell-window"></a>Pomocí okna Azure Cloud Shell

Tento dokument popisuje způsob použití okna služby Cloud Shell.

## <a name="swap-between-bash-and-powershell-environments"></a>Mezi prostředí Bash a PowerShell

Pomocí selektoru prostředí na panelu nástrojů Cloud Shellu mezi prostředí Bash a PowerShell.  
![Vyberte prostředí](media/using-the-shell-window/env-selector.png)

## <a name="restart-cloud-shell"></a>Restartovat Cloud Shell
Klikněte na ikonu restartování v panelu nástrojů Cloud Shellu můžete resetovat stav počítače.  
![Restartovat Cloud Shell](media/using-the-shell-window/restart.png)
> [!WARNING]
> Restartovat Cloud Shell se resetuje stav počítače a všechny soubory není určen na základě Azure sdílenou složku se ztratí.

## <a name="change-the-text-size"></a>Změnit velikost textu
Kliknutím na ikonu nastavení v horní levé části okna, pak najeďte myší možnost "Velikost textu" a vyberte velikost vašeho požadovaný text. Váš výběr se nastavit jako trvalý napříč relacemi.
![Velikost textu](media/using-the-shell-window/text-size.png)

## <a name="change-the-font"></a>Změna písma
Kliknutím na ikonu nastavení v horní levé části okna, poté najeďte myší možnost "Písmo" a vyberte požadované písmo.  Váš výběr se nastavit jako trvalý napříč relacemi.
![Písma](media/using-the-shell-window/text-font.png)

## <a name="upload-and-download-files"></a>Ukládání a stahování souborů
Kliknutím na ikonu nahrávání a stahování souborů na horní levé části okna, vyberte odeslání nebo stažení.  
![Nahrávání a stahování souborů](media/using-the-shell-window/uploaddownload.png)
* Pro nahrávání souborů pomocí automaticky otevírané okno přejděte k souboru na místním počítači, vyberte požadovaný soubor a klikněte na tlačítko "Otevřená".  Soubor se nahraje do `/home/user` adresáře.
* Pro stahování souborů, zadejte plně kvalifikovanou cestu do v automaticky otevíraném okně a klikněte na tlačítko "Stáhnout".  
> [!NOTE] 
> Soubory a cesty k souborům jsou malá a velká písmena ve službě Cloud Shell. Pečlivě zkontrolujte vaše malých a velkých písmen v cestě k souboru.

## <a name="open-another-cloud-shell-window"></a>Otevřete další okno Cloud Shell
Cloud Shell umožňuje víc souběžných relací mezi záložkách prohlížeče tím, že každá relace existuje jako samostatný proces.
Pokud se ukončení relace, je nutné ukončit každé okno relace, každý proces běží nezávisle na sobě, i když jsou spuštěné na stejném počítači.  
Klikněte na ikonu otevřít novou relaci na horní levé části okna. Otevře se nová karta s jinou relaci připojenou k existující kontejner.
![Otevřete novou relaci](media/using-the-shell-window/newsession.png)

## <a name="cloud-shell-editor"></a>Cloud Shell editoru
* Odkazovat [pomocí editoru Azure Cloud Shell](using-cloud-shell-editor.md) stránky.

## <a name="web-preview"></a>Webový náhled
Klikněte na ikonu web ve verzi preview na horní levé části okna, vyberte "Configure", zadejte požadovaný port otevřete.  Vyberte buď "otevřený port" pouze otevřít port, nebo "otevřete a vyhledejte" otevřít port a port na nové kartě preview.  
![Webový náhled](media/using-the-shell-window/preview.png)  
<br>
![Konfigurace portu](media/using-the-shell-window/preview-configure.png)  
Vyberte ikonu web ve verzi preview na horní levé části okna, klikněte na tlačítko "Zobrazit náhled port..." Chcete-li otevřít port na nové kartě preview. Klikněte na ikonu web ve verzi preview na horní levé části okna, vyberte "Zavřít port..." Chcete-li zavřít otevřeného portu.  
![Ve verzi Preview nebo zavřít port](media/using-the-shell-window/preview-options.png)

## <a name="minimize--maximize-cloud-shell-window"></a>Minimalizovat & maximalizovat okna služby Cloud Shell
Klikněte na ikonu Minimalizovat v horním pravém rohu okna tak jej skrýt. Klepnutím na ikonu Cloud Shell znovu zobrazit.
Klikněte na ikonu Maximalizovat nastavujete časové období maximální výšku. Chcete-li obnovit předchozí velikost okna, klikněte na obnovení.  
![Minimalizovat nebo maximalizovat okno](media/using-the-shell-window/minmax.png)

## <a name="copy-and-paste"></a>Kopírování a vkládání
[!INCLUDE [copy-paste](../../includes/cloud-shell-copy-paste.md)]

## <a name="resize-cloud-shell-window"></a>Změňte velikost okna služby Cloud Shell
Klikněte na tlačítko a přetáhněte ji na horní okraj panelu nástrojů nahoru nebo dolů změny velikosti okna služby Cloud Shell.

## <a name="scrolling-text-display"></a>Posouvání zobrazení textu
Posouvání pomocí myši nebo touchpadu přesunout terminálu text.

## <a name="exit-command"></a>Příkaz exit
Spuštění `exit` ukončí aktivní relace. K tomuto chování dochází ve výchozím nastavení po 20 minutách bez zásahu.

## <a name="next-steps"></a>Další postup

[Bash v Cloud Shellu Quickstart](quickstart.md) <br>
[Prostředí PowerShell ve službě Cloud Shell Quickstart](quickstart-powershell.md)
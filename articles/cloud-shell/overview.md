---
title: Přehled služby Azure Cloud prostředí | Microsoft Docs
description: Přehled prostředí cloudu Azure.
services: ''
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
ms.date: 02/15/2018
ms.author: juluk
ms.openlocfilehash: 72338a4c168b4d3b7c918fbb16758724f73fefc2
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
ms.locfileid: "29844101"
---
# <a name="overview-of-azure-cloud-shell"></a>Přehled prostředí cloudu Azure
Prostředí Azure Cloud je interaktivní, přístupných prohlížeče prostředí pro správu prostředků Azure.
Poskytuje flexibilitu zvolit prostředí shell, které nejlépe vyhovuje způsobu práce.
Uživatelé Linuxu si mohou vybrat Bash. Uživatelé Windows se mohou rozhodnout pro PowerShell.

Zkuste z shell.azure.com pomocí tohoto tlačítka.

[![](https://shell.azure.com/images/launchcloudshell.png "Spusťte prostředí cloudu Azure")](https://shell.azure.com)

Zkuste z portálu Azure pomocí ikony cloudové prostředí.

![Spuštění portálu](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funkce
### <a name="browser-based-shell-experience"></a>Prostředí shell založené na prohlížeči
Cloudové prostředí umožňuje přístup k založené na prohlížeči příkazového řádku prostředí, vytvořené s nástroji úlohy správy Azure v paměti.
Můžete zadat využívání cloudové prostředí pro práci untethered z místního počítače způsobem, jenom do cloudu.

### <a name="choice-of-preferred-shell-experience"></a>Volba upřednostňované prostředí prostředí
Linux uživatelé mohou používat Bash v prostředí cloudu i uživatelé s Windows můžete pomocí prostředí PowerShell v prostředí cloudu (Preview) z rozevíracího seznamu prostředí.

![Bash v prostředí cloudu](media/overview/overview-bash-pic.png)

![Prostředí PowerShell v prostředí cloudu (Preview)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Ověřený a konfiguraci Azure pracovní stanice
Tak se oblíbených nástrojů příkazového řádku a jazyková podpora prostředí cloudu spravovaného společností Microsoft. Cloudové prostředí také bezpečně ověřuje automaticky pro okamžitý přístup k prostředkům prostřednictvím rutin 2.0 rozhraní příkazového řádku Azure nebo Azure PowerShell.

Zobrazit v seznamu úplné nástrojů [prostředí Bash](features.md#tools) a [prostředí PowerShell (Preview).](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Několik přístupových bodů
Cloudové prostředí je flexibilní nástroj, který lze použít z:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Dokumentace Azure "Vyzkoušet" 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Účet Azure kód rozšíření sady Visual Studio](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Připojení úložiště Microsoft Azure Files
Cloudové prostředí počítače jsou dočasné a vyžadují připojit jako soubory Azure sdílenou složku `clouddrive` se zachovat svoje soubory.

Při prvním spuštění prostředí cloudu vás vyzve k vytvoření prostředku skupiny, účet úložiště a Azure Files sdílet vaším jménem. To je jednorázový krok a bude automaticky připojen pro všechny relace. Sdílení souborů lze mapovat a použije Bash a prostředí PowerShell v prostředí cloudu (Preview).

#### <a name="create-new-storage"></a>Vytvoření nového úložiště
![](media/overview/basic-storage.png)

Místně redundantní úložiště (LRS) účtu a sdílené složky souborů Azure vytvářet vaším jménem. Sdílenou složku Azure soubory se použije pro prostředí Bash a prostředí PowerShell, pokud se rozhodnete použít. Náklady na úložiště regulární použít.

Vaším jménem vytvoří tři zdroje:
1. Skupinu prostředků s názvem: `cloud-shell-storage-<region>`
2. Účet úložiště s názvem: `cs<uniqueGuid>`
3. Sdílené složky s názvem: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash v prostředí cloudu vytvoří také výchozí obrázek 5 GB disk k uchování `$Home`. Všechny soubory v adresáři $Home například klíče SSH zůstávají v bitové kopii disku uživatele uložený ve sdílené složce vaší připojeného souboru Azure. Při ukládání souborů v adresáři $Home a připojené sdílenou složku Azure, použít osvědčené postupy.

#### <a name="use-existing-resources"></a>Používat existující prostředky
![](media/overview/advanced-storage.png)

Upřesňující možnost je určen k přidružení stávající prostředky pro cloudové prostředí.
Do příkazového řádku instalace úložiště klikněte na tlačítko "Zobrazit upřesňující nastavení" zobrazte další možnosti.

> [!Note]
> Rozevírací seznamy jsou filtrovány předem přiřazené cloudové prostředí oblasti a účty úložiště LRS nebo GRS nebo ZRS.

[Další informace o prostředí cloudové úložiště, aktualizace sdílené složky Azure a nahrávání nebo stahování souborů.](persisting-shell-storage.md)

## <a name="concepts"></a>Koncepty
* Spuštění prostředí cloudu dočasné hostiteli k dispozici na každou relaci, jednotlivých uživatelů
* Cloudové prostředí vyprší po 20 minutách bez interaktivní aktivity
* Cloudové prostředí vyžaduje sdílenou složku Azure chcete připojit
* Cloudové prostředí používá stejnou sdílenou složkou Azure pro Bash a prostředí PowerShell
* Cloudové prostředí je přiřazený jeden počítač na uživatelský účet
* Bash potrvají $Home pomocí bitové kopie 5 GB uchovávat do sdílené složky
* Máte nastavená oprávnění jako běžný uživatel Linux v Bash

Další informace o funkcích v [Bash v prostředí cloudu](features.md) a [prostředí PowerShell v prostředí cloudu (Preview)](features-powershell.md).

## <a name="pricing"></a>Ceny
Tento počítač hostování prostředí cloudu je bezplatná s předpoklad připojené sdílené složky Azure Files. Náklady na úložiště regulární použít.

## <a name="next-steps"></a>Další postup
[Bash v prostředí cloudu rychlý start](quickstart.md) <br>
[Prostředí PowerShell v prostředí cloudu (Preview) rychlý start](quickstart-powershell.md)
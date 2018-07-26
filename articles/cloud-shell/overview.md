---
title: Přehled služby Azure Cloud Shell | Dokumentace Microsoftu
description: Přehled služby Azure Cloud Shell.
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
ms.date: 07/24/2018
ms.author: juluk
ms.openlocfilehash: 9588bebdc827760f0e0d3e2aadccbff5f24723f1
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258921"
---
# <a name="overview-of-azure-cloud-shell"></a>Přehled služby Azure Cloud Shell
Azure Cloud Shell je interaktivní, prohlížeč přístupné prostředí pro správu prostředků Azure.
Poskytuje možnost vybrat si prostředí, která nejlépe vyhovuje stylu vaší práce.
Uživatelé Linuxu si mohou vybrat Bash. Uživatelé Windows se mohou rozhodnout pro PowerShell.

Zkuste z shell.azure.com kliknutím níže.

[![](https://shell.azure.com/images/launchcloudshell.png "Spustit Azure Cloud Shell")](https://shell.azure.com)

Zkuste z webu Azure portal pomocí ikony Cloud Shell.

![Spuštění portálu](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funkce
### <a name="browser-based-shell-experience"></a>Příkazové prostředí založené na prohlížeči
Cloud Shell umožňuje přístup k založené na prohlížeči prostředí příkazového řádku vytvořené s úkoly správy Azure v úvahu.
Využijte Cloud Shell pro práci untethered z místního počítače způsobem pouze cloud může poskytovat.

### <a name="choice-of-preferred-shell-experience"></a>Volba preferovaného prostředí
Uživatelé Linuxu můžete použít Bash ve službě Cloud Shell, zatímco uživatelé Windows můžete použít PowerShell ve službě Cloud Shell (Preview) z rozevíracího seznamu prostředí.

![Bash ve službě Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell ve službě Cloud Shell (Preview)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Ověřený a nakonfigurovanou Azure pracovní stanice
Cloud Shell se spravuje přes Microsoft proto jde o s oblíbenými nástroji příkazového řádku a podpora jazyků. Cloud Shell také bezpečně ověří automaticky pro okamžitý přístup k prostředkům pomocí rutin Azure PowerShell nebo příkazového řádku Azure CLI 2.0.

Projděte si kompletní [seznam nástrojů, které jsou nainstalované ve službě Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrovaný editor Cloud Shell
Cloud Shell nabízí integrované grafické text editor založený na editoru Monaco open source. Jednoduše vytvořte a upravte konfigurační soubory spuštěním `code .` umožňuje snadné nasazení prostřednictvím příkazového řádku Azure CLI 2.0 nebo Azure Powershellu.

[Další informace o službě Cloud Shell editor](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>Několik přístupových bodů
Cloud Shell je flexibilní nástroj, který je možné z:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Dokumentace k webu "Vyzkoušejte si to" Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Mobilní aplikace Azure](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Rozšíření Azure Account kódu VS](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Připojte své úložiště Microsoft Azure Files
Cloudové prostředí počítače jsou dočasné a vyžadují sdílenou složku služby soubory Azure připojit jako `clouddrive` pro uchovávání souborů.

Při prvním spuštění Cloud Shell zobrazí výzvu k vytvoření prostředku skupiny, účet úložiště a službou soubory Azure sdílet vaším jménem. Toto je jednorázová kroku a automaticky připojí na všechny relace. Sdílení souborů lze mapovat a použije Bash i PowerShell ve službě Cloud Shell (Preview).

#### <a name="create-new-storage"></a>Vytvoření nového úložiště
![](media/overview/basic-storage.png)

Účet místně redundantního úložiště (LRS) a sdílenou složku služby soubory Azure můžete vytvořit za vás. Sdílenou složku služby soubory Azure se použije pro prostředí Bash i PowerShell, pokud se rozhodnete, chcete-li použít. Náklady na úložiště regulární použít.

Za vás vytvoří tři prostředky:
1. Skupina prostředků s názvem: `cloud-shell-storage-<region>`
2. Účet úložiště s názvem: `cs<uniqueGuid>`
3. Sdílená složka s názvem: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash ve službě Cloud Shell také vytvoří výchozí obrázek 5 GB disk k uchování `$Home`. Všechny soubory v vašeho adresáře $Home jako jsou klíče SSH jsou zachované ve vaší imagi disku uživatele uložené v připojené sdílené složky Azure. Použijte osvědčené postupy při ukládání souborů do vašeho adresáře $Home a připojené sdílené složky Azure.

#### <a name="use-existing-resources"></a>Použít existující prostředky
![](media/overview/advanced-storage.png)

Upřesňující možnosti slouží k přidružení stávajících prostředků, abyste službě Cloud Shell.
Na řádku nastavení úložiště, klikněte na tlačítko "Zobrazit pokročilé nastavení" zobrazte další možnosti.

> [!Note]
> Rozevírací seznamy se filtrují pro předem přiřazené oblast Cloud Shellu a účty úložiště LRS/GRS/ZRS.

[Další informace o službě Cloud Shell ukládání, aktualizuje se sdílenými složkami Azure a nahrávání a stahování souborů.](persisting-shell-storage.md)

## <a name="concepts"></a>Koncepty
* Cloud Shell spouští na dočasné hostiteli k dispozici na relací, jednotlivé uživatele
* Cloud Shell vyprší časový limit po 20 minutách bez interaktivní aktivity
* Cloud Shell vyžaduje sdílenou složku Azure připojit
* Pro Bash i PowerShell cloud Shell používá stejné sdílené složky Azure
* Cloud Shell je přiřazen jeden počítač v rámci uživatelského účtu
* Cloud Shell nevyřeší $Home pomocí 5 GB image uložené ve sdílené složce
* Oprávnění jsou nastavena jako běžný uživatel Linuxu v prostředí Bash

Další informace o funkcích v [Bash ve službě Cloud Shell](features.md) a [prostředí PowerShell ve službě Cloud Shell (Preview)](features-powershell.md).

## <a name="pricing"></a>Ceny
Počítače hostujícího Cloud Shell je bezplatné, s předpokladem připojené sdílené složky Azure Files. Náklady na úložiště regulární použít.

## <a name="next-steps"></a>Další postup
[Bash ve službě Cloud Shell rychlý start](quickstart.md) <br>
[Prostředí PowerShell v Cloud Shellu (Preview) quickstart](quickstart-powershell.md)
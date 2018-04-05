---
title: Prostředí PowerShell v prostředí cloudu Azure (Preview) funkce | Microsoft Docs
description: Přehled funkcí prostředí PowerShell v prostředí cloudu Azure
services: Azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: damaerte
ms.openlocfilehash: 5c4cb6230d8509830d25caaf497e4e63ac7e5806
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell-preview"></a>Funkce a nástroje pro prostředí PowerShell v prostředí cloudu Azure (Preview)

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Funkce a nástroje pro [Bash](features.md) je také k dispozici.

Prostředí PowerShell v prostředí cloudu (Preview) běží na `Windows Server 2016`.

## <a name="features"></a>Funkce

### <a name="secure-automatic-authentication"></a>Zabezpečení automatické ověřování

Prostředí PowerShell v prostředí cloudu (Preview) bezpečně a automaticky ověřuje přístup k účtu pro prostředí Azure PowerShell.

### <a name="files-persistence-across-sessions"></a>Soubory trvalost napříč relacemi

Pro soubory zachová napříč relacemi, cloudové prostředí vás provede procesem připojení sdílené složky souborů aplikace Microsoft Azure při prvním spuštění.
Po dokončení cloudové prostředí automaticky připojí úložiště (připojit jako `$home\clouddrive`) pro všechny budoucí relace.
Vzhledem k tomu, že každý požadavek pro cloudové prostředí je přidělování dočasného počítače souborů mimo vaši `$home\clouddrive` a stav počítače nejsou trvalé napříč relacemi.

[Další informace o připojení sdílené složky Azure pro cloudové prostředí.](persisting-shell-storage-powershell.md)

### <a name="azure-drive-azure"></a>Disk Azure (Azure:)

Prostředí PowerShell v prostředí cloudu (Preview) vám spuštění v Azure jednotce (`Azure:`).
Azure jednotky umožňuje snadno našli a navigační prostředků Azure, jako jsou výpočty, sítě, úložiště atd podobná navigační systému souborů.
Můžete nadále používat známé [rutin prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure) tyto zdroje spravovat.
Všechny změny prostředků Azure, buď provedené přímo na portálu Azure nebo pomocí rutin prostředí Azure PowerShell, se okamžitě projeví v Azure jednotky.

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>Kontextová sledování
- **Rozsahu skupiny prostředků**: když v rámci cesty skupiny prostředků v Azure jednotce (`Azure:`), název skupiny prostředků se automaticky předá rutin prostředí Azure PowerShell.

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**: Tato rutina vrátí seznam příkazů použít v kontextu umístění v Azure jednotce (`Azure:`). Například pokud uživatel v rámci zobrazuje pouze příkazy související s úložištěm `Azure:\<subscription name>\StorageAccounts`

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>Úpravy skriptu prostředí PowerShell formátováním

Při použití VIM k úpravě souborů prostředí PowerShell (`.ps1,.psm1,.psd1`), automaticky získáte zvýraznění syntaxe a podporu technologie IntelliSense.
Podporu technologie IntelliSense se implementuje prostřednictvím vim plug-in, který komunikuje s místní instanci [Editor prostředí PowerShell služby](https://github.com/PowerShell/PowerShellEditorServices).

> [!TIP]
> Použití `TAB` získat dokončení (IntelliSense) na názvy příkazů, názvy parametrů a hodnoty parametrů (v případě potřeby).

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>Rozšiřitelné modelu

Pomocí [PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget), můžete snadno nainstalovat (a aktualizovat) vlastní moduly a skripty od [Galerie prostředí PowerShell](https://www.powershellgallery.com).
Po instalaci jsou moduly automaticky trvalé napříč relacemi cloudové prostředí.

> [!TIP]
> Moduly uživatelé nainstalují se ukládají do `$Home\CloudDrive\.pscloudshell\WindowsPowerShell` složky. Symbolický odkaz na tato složka se vytvoří ve složce Dokumenty uživatele (`$home\Documents\WindowsPowerShell`).

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>Správa virtuálních počítačů hosta

Pomocí dvou integrované příkazy - `Enter-AzureRmVM` a `Invoke-AzureRmVMCommand`, můžete vzdáleně spravovat virtuální počítače Azure.
Tyto příkazy jsou postavená na vzdálenou komunikaci prostředí PowerShell a vyžaduje připojení k prostředí PowerShell na virtuálních počítačích Azure.

## <a name="tools"></a>Nástroje

|**Kategorie**    |**Název**                                 |
|----------------|-----------------------------------------|
|Nástroje Azure     |[Prostředí Azure PowerShell (5.6.0)](https://docs.microsoft.com/powershell/azure/overview)<br> [Azure CLI (2.0.30)](https://docs.microsoft.com/cli/azure)|
|Textové editory    |VIM<br> nano                             |
|Správce balíčků |PowerShellGet<br> PackageManagement<br> npm<br> pip |
|Správa zdrojového kódu  |Git                                      |
|Databáze       |[SqlServer module](https://www.powershellgallery.com/packages/SqlServer)<br> [Nástroj SQLCMD](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Testovací nástroje      |Pester                                   |

## <a name="language-support"></a>Podpora jazyků

|**Jazyk**|**Verze**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 a [6.0.2](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>Další postup

[Rychlé spuštění pomocí prostředí PowerShell v prostředí cloudu (Preview)](quickstart-powershell.md)

[Další informace o prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/)

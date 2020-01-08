---
title: Aktualizace skriptu nasazení šablony sady Visual Studio pro použití AZ PowerShell
description: Aktualizace skriptu nasazení šablony sady Visual Studio z AzureRM na az PowerShell
author: cweining
ms.topic: quickstart
ms.date: 09/27/2019
ms.author: cweining
ms.openlocfilehash: c34cde5593b48c301826be3dd2641dc2490ee88d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75473511"
---
# <a name="updating-the-visual-studio-template-deployment-script-to-use-az-powershell"></a>Aktualizace skriptu nasazení šablony sady Visual Studio pro použití AZ PowerShell

Visual Studio 16,4 podporuje použití AZ PowerShellu ve skriptu nasazení šablony. Visual Studio neinstaluje AZ PowerShell ani automaticky neaktualizuje skript nasazení v projektu skupiny prostředků. Aby bylo možné použít novější příkaz AZ PowerShell, je nutné provést tyto čtyři věci:
1. Odinstalace prostředí PowerShell pro AzureRM
1. Instalace AZ PowerShell
1. Aktualizace na Visual Studio 16,4
1. Aktualizujte skript nasazení v projektu.

## <a name="uninstall-azurerm-powershell"></a>Odinstalace prostředí PowerShell pro AzureRM
Pomocí těchto [pokynů](https://docs.microsoft.com/powershell/azure/uninstall-az-ps?view=azps-2.7.0#uninstall-the-azurerm-module) odinstalujte prostředí PowerShell pro AzureRM.

## <a name="install-az-powershell"></a>Instalace AZ PowerShell
Pomocí těchto [pokynů](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.7.0) nainstalujte AZ PowerShell.

## <a name="update-visual-studio-to-164"></a>Aktualizace sady Visual Studio na 16,4
Aktualizujte na Visual Studio 16,4, pokud je k dispozici. Během upgradu se ujistěte, že není zaškrtnutá součást Azure PowerShellu. Vzhledem k tomu, že jste z Galerie nainstalovali AZ PowerShell, nechcete AzureRM verzi PowerShellu, která se instaluje se sadou Visual Studio.

Pokud jste už provedli upgrade na 16,4 a komponenta prostředí Azure PowerShell byla zkontrolována, můžete ji odinstalovat spuštěním Instalační program pro Visual Studio a zrušením zaškrtnutí komponenty prostředí Azure PowerShell v úloze Azure nebo na stránce jednotlivé součásti.

## <a name="update-the-deployment-script-in-your-project"></a>Aktualizace skriptu nasazení v projektu
Ve svém skriptu nasazení nahraďte všechny výskyty řetězce "AzureRm" pomocí příkazu AZ. Změny si můžete prohlédnout v tomto [registru](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) . Další informace o upgradu skriptů na az PowerShell najdete v této [dokumentaci](https://docs.microsoft.com/powershell/azure/migrate-from-azurerm-to-az?view=azps-2.5.0) .



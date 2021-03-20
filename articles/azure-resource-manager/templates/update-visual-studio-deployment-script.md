---
title: Aktualizace skriptu nasazení šablony sady Visual Studio pro použití AZ PowerShell
description: Aktualizace skriptu nasazení šablony sady Visual Studio z AzureRM na az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "76963865"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Aktualizace skriptu nasazení šablony sady Visual Studio pro použití AZ PowerShell Module

Visual Studio 16,4 podporuje použití modulu AZ PowerShell ve skriptu nasazení šablony. Visual Studio ale tento modul automaticky nenainstaluje. Chcete-li použít modul AZ Module, je třeba provést čtyři kroky:

1. [Odinstalace modulu AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Instalace AZ Module](/powershell/azure/install-az-ps)
1. Aktualizace sady Visual Studio na 16,4
1. Aktualizujte skript nasazení v projektu.

## <a name="update-visual-studio-to-164"></a>Aktualizace sady Visual Studio na 16,4

Aktualizujte instalaci sady Visual Studio na verzi 16,4 nebo novější. V průběhu upgradu se ujistěte, že není zaškrtnuta součást Azure PowerShell. Vzhledem k tomu, že jste nainstalovali modul AZ Module přes galerii, nechcete přeinstalovat modul AzureRM.

Pokud jste již provedli upgrade na 16,4 a byla zkontrolována součást Azure PowerShell, můžete ji odinstalovat spuštěním Instalační program pro Visual Studio. Nevybírejte součást Azure PowerShell v úloze Azure nebo na stránce jednotlivé součásti.

## <a name="update-the-deployment-script-in-your-project"></a>Aktualizace skriptu nasazení v projektu

Ve svém skriptu nasazení nahraďte všechny výskyty řetězce "AzureRm" pomocí příkazu AZ. Změny si můžete prohlédnout v tomto [registru](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) . Další informace o tom, jak upgradovat skripty na modul AZ, najdete v tématu [migrace Azure PowerShell z AzureRM na az](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Další kroky

Další informace o použití projektu sady Visual Studio najdete v tématu [Vytvoření a nasazení skupin prostředků Azure pomocí sady Visual Studio](create-visual-studio-deployment-project.md).

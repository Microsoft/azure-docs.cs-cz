---
title: Aktualizace skriptu nasazení šablony sady Visual Studio pro použití prostředí Az PowerShell
description: Aktualizace skriptu nasazení šablony Sady Visual Studio z AzureRM do prostředí Az PowerShell
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76963865"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Aktualizace skriptu nasazení šablony sady Visual Studio pro použití modulu Az PowerShell

Visual Studio 16.4 podporuje použití modulu Az PowerShell ve skriptu pro nasazení šablony. Visual Studio však není automaticky nainstalovat tento modul. Chcete-li použít modul Az, musíte provést čtyři kroky:

1. [Odinstalace modulu AzureRM](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Instalace modulu Az](/powershell/azure/install-az-ps)
1. Aktualizace sady Visual Studio na 16.4
1. Aktualizujte skript nasazení v projektu.

## <a name="update-visual-studio-to-164"></a>Aktualizace sady Visual Studio na 16.4

Aktualizujte instalaci sady Visual Studio na verzi 16.4 nebo novější. Během upgradu zkontrolujte, že součást Azure PowerShell není zaškrtnutá. Vzhledem k tomu, že jste nainstalovali modul Az prostřednictvím galerie, nechcete přeinstalovat modul AzureRM.

Pokud jste už upgradovali na 16.4 a byla zkontrolována součást Azure PowerShell, můžete ji odinstalovat spuštěním Instalační služby Visual Studia. Nevybírejte komponentu Azure PowerShell u úlohy Azure ani na stránce jednotlivých komponent.

## <a name="update-the-deployment-script-in-your-project"></a>Aktualizace skriptu nasazení v projektu

Nahraďte všechny výskyty řetězce "AzureRm" "Az" ve skriptu nasazení. Můžete odkazovat na revize v této [podstatě vidět](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619) změny. Další informace o upgradu skriptů do modulu Az najdete v [tématu Migrace Azure PowerShellu z AzureRM do Az](/powershell/azure/migrate-from-azurerm-to-az).

## <a name="next-steps"></a>Další kroky

Další informace o použití projektu Sady Visual Studio najdete v [tématu vytváření a nasazování projektů skupiny prostředků Azure prostřednictvím sady Visual Studio](create-visual-studio-deployment-project.md).

---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 684b212ca771af6c336cf6239e18ea367f2da5ce
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045086"
---
Tento článek používá rutiny prostředí PowerShell. Pokud chcete spustit rutiny, můžete použít Azure Cloud Shell interaktivní prostředí prostředí hostované v Azure, které se používá v prohlížeči. Azure Cloud Shell obsahuje předem instalované rutiny Azure PowerShell.

Chcete-li spustit libovolný kód obsažený v tomto článku v Azure Cloud Shell, otevřete relaci Cloud Shell, pomocí tlačítka **kopírování** na bloku kódu kód zkopírujte a vložte jej do relace Cloud shell s __kombinací kláves CTRL + SHIFT + v__ v systémech Windows a Linux nebo __Cmd + Shift + v__ v MacOS. Vkládaný text není automaticky spuštěn, takže stiskněte klávesu **ENTER** pro spuštění kódu.

Azure Cloud Shell můžete spustit pomocí:

|  |   |
|-----------------------------------------------|---|
| Zvolte **Vyzkoušet** v pravém horním rohu bloku kódu. Automaticky __se__ nekopíruje text na Cloud Shell. | ![Příklad pokusu o Azure Cloud Shell](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Otevřete [Shell.Azure.com](https://shell.azure.com) v prohlížeči. | [tlačítko ![spustit Azure Cloud Shell](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Zvolte **Cloud Shell** v nabídce v pravém horním rohu webu [Azure Portal](https://portal.azure.com). | ![Tlačítko Cloud Shell na webu Azure Portal](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**Místní spuštění PowerShellu**

V počítači můžete také nainstalovat a spustit rutiny Azure PowerShell místně. Rutiny PowerShellu se často aktualizují. Pokud nepoužíváte nejnovější verzi, hodnoty uvedené v pokynech mohou selhat. Pokud chcete najít verze Azure PowerShell nainstalované v počítači, použijte rutinu `Get-Module -ListAvailable Az`. Chcete-li nainstalovat nebo aktualizovat, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).

Pokud používáte prostředí PowerShell místně, nezapomeňte spustit příkaz "Connect-AzAccount" a vytvořit připojení k Azure.
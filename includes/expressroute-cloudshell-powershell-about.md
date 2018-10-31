---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/25/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 874643c5149cf6a222ab6d244dd561e5c4da5a84
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50251392"
---
Tento článek používá rutiny prostředí PowerShell. Pokud chcete spustit rutiny, můžete použít Azure Cloud Shell, bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Stačí kliknout na **Kopírovat** a zkopírovat kód, vložit ho do Cloud Shellu a potom ho spustit stisknutím Enter. Existuje několik způsobů, jak Cloud Shell spustit:

|  |   |
|-----------------------------------------------|---|
| Klikněte na **Vyzkoušet** v pravém horním rohu bloku kódu. | ![Cloud Shell v tomto článku](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Otevřete Cloud Shell ve vašem prohlížeči. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Klikněte na tlačítko **Cloud Shell** v nabídce v pravé horní části webu Azure Portal. | [![Cloud Shell na portálu](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Pokud nechcete použít Azure Cloud Shell, můžete nainstalovat prostředí PowerShell místně místo. Pokud se rozhodnete nainstalovat a používat PowerShell místně, nezapomeňte nainstalovat nejnovější verzi rutin Powershellu pro Azure Resource Manager. Rutiny Powershellu se často aktualizují a obvykle budete muset aktualizovat zobrazíte nejnovější funkce rutiny Powershellu. Pokud rutiny PowerShellu neaktualizujete, zadané hodnoty nemusí fungovat. 

Najít verzi prostředí PowerShell, které spouštíte místně, použijte rutinu "Get-Module - ListAvailable AzureRM". Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).
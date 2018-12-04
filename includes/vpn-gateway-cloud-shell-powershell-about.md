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
ms.openlocfilehash: 6a0ea318f2e9b8f392ac7c0a1f1091c062c59d41
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852351"
---
Tento článek používá rutiny prostředí PowerShell. Pokud chcete spustit rutiny, můžete použít Azure Cloud Shell, bezplatné interaktivní prostředí. Má předinstalované obecné nástroje Azure, které jsou nakonfigurované pro použití s vaším účtem. Stačí kliknout na **Kopírovat** a zkopírovat kód, vložit ho do Cloud Shellu a potom ho spustit stisknutím Enter. Existuje několik způsobů, jak Cloud Shell spustit:

|  |   |
|-----------------------------------------------|---|
| Klikněte na **Vyzkoušet** v pravém horním rohu bloku kódu. | ![Cloud Shell v tomto článku](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Otevřete Cloud Shell ve vašem prohlížeči. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Klikněte na tlačítko **Cloud Shell** v nabídce v pravé horní části webu Azure Portal. | [![Cloud Shell na portálu](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Pokud nechcete použít Azure Cloud Shell, můžete nainstalovat prostředí PowerShell místně místo. Pokud se rozhodnete nainstalovat a používat PowerShell místně, nezapomeňte nainstalovat nejnovější verzi rutin Powershellu pro Azure Resource Manager k získání nejnovější funkce.

Najít verzi prostředí PowerShell, které spouštíte místně, použijte rutinu "Get-Module - ListAvailable AzureRM". Pokud chcete aktualizovat, najdete v článku [instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).
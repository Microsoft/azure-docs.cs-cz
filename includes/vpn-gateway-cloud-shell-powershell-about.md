---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c8f45e4bb16c05c9f322dd04d2c80f6144744e64
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66146976"
---
Tento článek používá rutiny prostředí PowerShell. Pokud chcete spustit rutiny, můžete použít Azure Cloud Shell. Azure Cloud Shell je bezplatné interaktivní prostředí, který má obecné nástroje Azure, které jsou předinstalované a nakonfigurovány pro použití s vaším účtem. Stačí kliknout na **kopírování** zkopírovat kód, vložte ho do Cloud Shellu a stiskněte klávesu enter k jeho spuštění. Existuje několik způsobů, jak Cloud Shell spustit:

|  |   |
|-----------------------------------------------|---|
| Klikněte na **Vyzkoušet** v pravém horním rohu bloku kódu. | ![Cloud Shell v tomto článku](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Otevřete Cloud Shell ve vašem prohlížeči. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Klikněte na tlačítko **Cloud Shell** v nabídce v pravé horní části webu Azure Portal. | [![Cloud Shell na portálu](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

**Místní použití Powershellu**

Můžete také nainstalovat a spustit rutiny prostředí Azure PowerShell místně ve vašem počítači. Rutiny Powershellu se často aktualizují. Pokud používáte nejnovější verzi, zadané v pokynech pro hodnoty nemusí fungovat. Pokud chcete zjistit verzi prostředí PowerShell, které spouštíte místně, použijte `Get-Module -ListAvailable Az` rutiny. Pro instalaci nebo aktualizaci, najdete v článku [instalace modulu Azure PowerShell](/powershell/azure/install-az-ps).
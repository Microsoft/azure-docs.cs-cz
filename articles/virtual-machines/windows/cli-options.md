---
title: Použití Azure CLI v systému Windows | Microsoft Docs
description: Použití Azure CLI v systému Windows
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/14/2017
ms.author: cynthn
ms.openlocfilehash: e1d611617b72a1fd5134b83bd3a2de59ba668b11
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="using-the-azure-cli-on-windows"></a>Použití Azure CLI v systému Windows

Rozhraní příkazového řádku Azure (CLI) poskytuje příkazového řádku a skriptovací prostředí pro vytváření a správě prostředků Azure. Rozhraní příkazového řádku Azure je k dispozici pro systému macOS, Linux a operačních systémů Windows. Mezi tyto operační systémy, rozhraní příkazového řádku jsou stejné, ale specifickou syntaxi skriptování operačního systému se může lišit.

Tento dokument podrobně popisuje způsoby, rozhraní příkazového řádku Azure můžete nainstalovat a spustit v systému Windows a podrobnosti syntaktické důležité informace pro každý. Pro podrobné naleznete v dokumentaci rozhraní příkazového řádku Azure [dokumentaci k rozhraní příkazového řádku Azure]( https://docs.microsoft.com/cli/azure).

## <a name="windows-subsystem-for-linux"></a>Subsystém Windows pro Linux

Subsystém Windows pro Linux (WSL) poskytuje prostředí Ubuntu Linux na Windows 10 Anniversary a novější verze. Když je povolené, WSL poskytuje nativní prostředí Bash, který můžete použít pro vytváření a spouštění skriptů rozhraní příkazového řádku Azure. Protože WSL poskytuje nativní prostředí Bash, rozhraní příkazového řádku Azure skripty lze sdílet mezi systému macOS, Linux a Windows bez úprav.

Pokud chcete používat rozhraní příkazového řádku Azure v WSL, proveďte následující kroky.

|Úkol | Pokyny |
|---|---|
| Povolit WSL | [Nainstalujte WSL dokumentace ](https://msdn.microsoft.com/commandline/wsl/install_guide) |
| Instalace rozhraní příkazového řádku Azure CLI |[Nainstalovat rozhraní příkazového řádku na WSL/Ubuntu 14.04](https://docs.microsoft.com/cli/azure/install-az-cli2#ubuntu)|

## <a name="powershell"></a>PowerShell

Rozhraní příkazového řádku Azure se může spouštět nativně ve Windows. V této konfiguraci balíček rozhraní příkazového řádku Azure je nainstalovaný na operačním systému Windows a příkazy můžete spustit z prostředí PowerShell. V této konfiguraci Azure CLI příkazy a skripty lze spustit na všechny podporované verze systému Windows, ale je vyžadován specifickou syntaxi skriptovací platformu. Z toho důvodu skripty nelze nutně sdílet mezi systému macOS, Linux a Windows bez úprav.

Pokud chcete používat rozhraní příkazového řádku Azure v systému Windows, nainstalujte balíček podle těchto pokynů [nainstalovat rozhraní příkazového řádku v systému Windows](https://docs.microsoft.com/cli/azure/install-az-cli2#windows).

## <a name="docker-image"></a>Obrázek docker

Při použití Docker pro systém Windows, bitovou kopii Docker lze spustit zahrnující Azure CLI. Tento image je založen na systému Linux a zahrnuje nativní prostředí Bash.  Při použití Docker pro systém Windows a bitovou kopii rozhraní příkazového řádku Azure, skripty, které sdílet mezi systému macOS, Linux a Windows. 

Pokud chcete používat rozhraní příkazového řádku Azure na Docker pro systém Windows, zkontrolujte, zda je spuštěn Docker pro systém Windows a spusťte následující příkaz.

```bash
docker run -it azuresdk/azure-cli-python:latest bash
```

Po dokončení Bash, tedy spuštění relace se předem načtou nástrojů příkazového řádku Azure.

## <a name="next-steps"></a>Další kroky

[Ukázka rozhraní příkazového řádku pro virtuální počítače Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Ukázky rozhraní příkazového řádku pro webové aplikace Azure](../../app-service/app-service-cli-samples.md)

[Ukázky rozhraní příkazového řádku pro Azure SQL](../../sql-database/sql-database-cli-samples.md)

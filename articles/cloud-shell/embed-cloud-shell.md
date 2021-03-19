---
title: Vložit Azure Cloud Shell | Microsoft Docs
description: Naučte se vkládat Azure Cloud Shell.
services: cloud-shell
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "60614316"
---
# <a name="embed-azure-cloud-shell"></a>Vložit Azure Cloud Shell

Vkládání Cloud Shell umožňuje vývojářům a zapisovačům obsahu přímo otevírat Cloud Shell z vyhrazené adresy URL [Shell.Azure.com](https://shell.azure.com). Tím okamžitě získáte kompletní možnosti ověřování Cloud Shell, nástrojů a aktuálnosti nástrojů Azure CLI/Azure PowerShell pro vaše uživatele.

Tlačítko běžné velikosti

[![Normální spuštění](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)

Tlačítko velké velikosti

[![Velké spuštění](https://shell.azure.com/images/launchcloudshell@2x.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Postupy

Integrací spouštěcího tlačítka Cloud Shell do souborů Markdownu zkopírujte následující:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

KÓD HTML pro vložení automaticky otevíraného okna Cloud Shell je následující:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Přizpůsobení prostředí

Pomocí rozšíření adresy URL nastavte konkrétní prostředí prostředí.

|Prostředí   |URL   |
|---|---|
|Naposledy použité prostředí   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Další kroky
[Bash v rychlém startu Cloud Shell](quickstart.md)<br>
[PowerShell v Cloud Shell rychlý Start](quickstart-powershell.md)

---
title: Vložení prostředí Azure Cloud Shell | Dokumenty společnosti Microsoft
description: Naučte se vložit Azure Cloud Shell.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60614316"
---
# <a name="embed-azure-cloud-shell"></a>Vložení Cloudshellu Azure

Vložení prostředí Cloud Shell umožňuje vývojářům a autorům obsahu přímo otevřít cloudové prostředí z vyhrazené adresy [URL, shell.azure.com](https://shell.azure.com). To okamžitě přináší plnou sílu cloudového prostředí ověřování, nástrojů a aktuálních nástrojů Azure CLI/Azure PowerShell vašim uživatelům.

Tlačítko normální velikosti

[![Pravidelné spuštění](https://shell.azure.com/images/launchcloudshell.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)

Tlačítko velké velikosti

[![Velký start](https://shell.azure.com/images/launchcloudshell@2x.png "Spuštění služby Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Postupy

Integrujte spouštěcí tlačítko Cloud Shell do souborů markdownu zkopírováním následujících položek:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

HTML pro vložení automaticky otevírané hospo- prostředí Cloud shell u. je níže:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Přizpůsobení prostředí

Nastavte konkrétní prostředí rozšířením adresy URL.

|Prostředí   |zprostředkovatele identity   |
|---|---|
|Naposledy použité prostředí   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Další kroky
[Bash v rychlém startu Cloud Shellu](quickstart.md)<br>
[PowerShell v cloudovém prostředí rychlý start](quickstart-powershell.md)

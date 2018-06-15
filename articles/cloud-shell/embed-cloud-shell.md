---
title: Vložení prostředí cloudu Azure | Microsoft Docs
description: Postup vložení prostředí cloudu Azure.
services: cloud-shell
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 3ceddb94336fc2703e6f916f05ab1ec3676cb50d
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2018
ms.locfileid: "27864880"
---
# <a name="embed-azure-cloud-shell"></a>Vložení prostředí cloudu Azure

Vložení cloudové prostředí umožňuje vývojářům a obsahu zapisovače přímo z vyhrazené adresy URL, otevřete prostředí cloudu [shell.azure.com](https://shell.azure.com). To přináší okamžitě potenciál ověřování cloudové prostředí, nástrojů, a aktuální Azure CLI nebo Azure PowerShell nástroje pro vaše uživatele.

Regulární velikostí tlačítko

[![](https://shell.azure.com/images/launchcloudshell.png "Spusťte prostředí cloudu Azure")](https://shell.azure.com)

Velké velikostí tlačítko

[![](https://shell.azure.com/images/launchcloudshell@2x.png "Spusťte prostředí cloudu Azure")](https://shell.azure.com)

## <a name="how-to"></a>Postupy

Integrace tlačítko spustit prostředí cloudu do soubory markdown tak, že zkopírujete následující:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

Zde je kód HTML pro vložení automaticky otevírané okno prostředí cloudu:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Přizpůsobení prostředí

Nastavte možnosti specifické prostředí rozšířit vaše adresa URL.
|Prostředí   |Adresa URL   |
|---|---|
|Naposledy použité prostředí   |shell.azure.com           |
|Bash                       |shell.azure.com/bash       |
|PowerShell                 |shell.azure.com/powershell |

## <a name="next-steps"></a>Další postup
[Bash v prostředí cloudu rychlý start](quickstart.md)<br>
[Prostředí PowerShell v prostředí cloudu rychlý start](quickstart-powershell.md)

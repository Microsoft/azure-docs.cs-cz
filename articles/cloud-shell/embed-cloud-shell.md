---
title: Vložení služby Azure Cloud Shell | Dokumentace Microsoftu
description: Zjistěte, jak vložit Azure Cloud Shell.
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
ms.openlocfilehash: 0bd5382e5ea37f7c3c52d119e9d39fe7e0bfdc7c
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "42056788"
---
# <a name="embed-azure-cloud-shell"></a>Vložení služby Azure Cloud Shell

Využitím vkládání služby Cloud Shell umožňuje vývojářům a autory obsahu přímo z vyhrazené adresy URL, otevřete Cloud Shell [shell.azure.com](https://shell.azure.com). Okamžitě zobrazí všechny možnosti ověřování Cloud Shell, nástrojů, a aktuální Azure CLI nebo Azure PowerShell nástroje pro vaše uživatele.

Běžné velikosti tlačítko

[![](https://shell.azure.com/images/launchcloudshell.png "Spustit Azure Cloud Shell")](https://shell.azure.com)

Velké velikosti tlačítko

[![](https://shell.azure.com/images/launchcloudshell@2x.png "Spustit Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Postupy

Tlačítko Spustit Cloud Shell můžete integrate do souborů markdown tak, že zkopírujete následující:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

Kód HTML pro vložení místní Cloud Shell je nižší než:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Přizpůsobení prostředí

Nastavte konkrétní příkazové prostředí posílením adresu URL.
|Prostředí   |zprostředkovatele identity   |
|---|---|
|Naposledy použité prostředí   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[Shell.Azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[Shell.Azure.com/PowerShell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Další postup
[Bash ve službě Cloud Shell rychlý start](quickstart.md)<br>
[PowerShell ve službě Cloud Shell rychlý start](quickstart-powershell.md)

---
title: zahrnout soubor
description: zahrnout soubor
ms.custom: include file
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: include
manager: douge
ms.openlocfilehash: e4bde4f521e0e19e7acd36260c98cfe80973e284
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
## <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inicializovat debug prostředky s příponou VS Code
Nejdřív musíte nakonfigurovat projektu kódu, takže VS Code budou komunikovat s našeho vývojového prostředí v Azure. Rozšíření VS Code prostory Dev Azure poskytuje pomocníka příkaz nastavit konfiguraci ladění. 

Otevřete **příkaz palety** (pomocí **zobrazení | Příkaz palety** nabídky) a používání automatické doplňování a vyberte tento příkaz: `Azure Dev Spaces: Create configuration files for connected development`. 

Tento postup Přidá konfiguraci ladění pro Azure Dev prostory v části `.vscode` složky.

![](../media/common/command-palette.png)

> [!Important]
> Z důvodu chyby zavřete a znovu ho otevřete VS Code než budete pokračovat.
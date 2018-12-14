---
title: zahrnout soubor
description: zahrnout soubor
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 026540290710d039dbc06c394ab538ebe2d7c12f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53344665"
---
Zpět v _okně místního terminálu_ přidejte vzdálené úložiště Azure do místního úložiště Git. Nahraďte _&lt;deploymentLocalGitUrl-from-create-step>_ adresou URL vzdáleného úložiště Git, kterou jste uložili v kroku [Vytvoření webové aplikace](#create-a-web-app).

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Nasaďte aplikaci do vzdáleného úložiště Azure pomocí následujícího příkazu. Po zobrazení výzvy k zadání přihlašovacích údajů ve Správci přihlašovacích údajů Git se ujistěte, že zadáváte přihlašovací údaje, které jste vytvořili v části [Konfigurace uživatele nasazení](#configure-a-deployment-user), a ne přihlašovací údaje, se kterým se přihlašujete k webu Azure Portal.

```bash
git push azure master
```

Spuštění tohoto příkazu může trvat několik minut. Při spuštění příkaz zobrazí podobné informace jako v následujícím příkladu:
